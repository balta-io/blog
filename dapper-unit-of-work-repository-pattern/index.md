O Dapper é um Micro ORM muito poderoso e que nos dá muita liberdade, porém com grandes poderes trazem grandes responsabilidades e neste artigo vamos conferir como implementar alguns padrões com ele.

## Dapper
O Dapper é um Micro ORM mantido pelo StackOverflow com o foco em performance. Ele se difere de outros ORM por ser mais simples em seu contexto, que é o motivo pelo qual ele consegue performar melhor.

O Dapper na verdade só extende o `IDbConnection` simplificando a leitura e escrita no banco. Por baixo dos panos ele roda sobre o contexto de acesso a dados do .NET.

A principal diferença entre ele e o Entity Framework (ORM) por exemplo é que o EF é mais completo, traz bem mais facilidades, mas obviamente estas facilidades trazem um custo maior de performance.

Embora haja como otimizar bastante as consultas utilizando o EF, em alguns pontos eu prefiro utilizar o Dapper, ou mesmo mesclar os dois, usando Dapper para leitura e o EF para escrita.

Para utilizar o Dapper em seu projeto basta instalar o pacote abaixo.

```
dotnet add package Dapper
```

## Conexão e Transação
A primeira grande responsabilidade quando trabalhamos somente com Dapper é gerenciar a conexão com o banco de dados. Enquanto ao utilizar o EF temos um `DataContext`, aqui é um trabalho mais artesanal.

Para este exemplo, vamos utilizar a classe abaixo e se você ficar curioso sobre o motivo de ela ser selada, [confira este artigo](https://balta.io/blog/orientacao-a-objetos-classes-seladas).

```csharp
public sealed class DbSession : IDisposable
{
    private Guid _id;
    public IDbConnection Connection { get; }
    public IDbTransaction Transaction { get; set; }

    public DbSession()
    {
        _id = Guid.NewGuid();
        Connection = new SqlConnection(Settings.ConnectionString);
        Connection.Open();
    }

    public void Dispose() => Connection?.Dispose();
}
```

Nesta classe temos uma conexão e uma transação, definidas pelo `IDbConnection` e `IDbTransaction` respectivamente. A escolha para estes tipos ao invés do `ISqlConnection` e `ISqlTransaction` foi a necessidade do pacote adicional `System.Data.SqlClient` necessário para se utilizar estes dois últimos tipos mencionados.

Adicionei também um `_id` na sessão, apenas para acompanhar os testes, pode removê-lo se quiser pois ele não tem efeito algum sobre o resto do código.

O `IDbConnection` será responsável por gerenciar a conexão com o banco de dados enquanto o `IDbTransaction` será responsável pelas transações, mas vamos deixar as transações para depois.

Temos o método construtor onde **SEMPRE** iniciamos uma nova conexão com o banco de dados através do método `Connection.Open()`.

Para finalizar, temos o método `Dispose` vindo da interface `IDisposable` que será executado no término da instância desta classe.

Implementar o `IDisposable` nos permite fazer uso desta classe em contextos utilizando o `using` do C#, que já garante o término da mesma no fim da excução do contexto.

Por fim no método `Dispose` fazemos o fechamento da conexão chamando o `Connection?.Dispose()`. Note que temos um `?` que faz a checagem caso o objeto `Connection` seja `null`.

### Close ou Dispose?
O objeto `Connection` tem dois métodos similares, o `Close` e o `Dispose`. Ambos fecham a conexão com o banco de dados, porém o `Close` ainda mantém o objeto `Connection` ativo.

Em suma, se você pretende reabrir esta mesma conexão futuramente, a melhor escolha é utilizar o `Close`, caso contrário, utilizando o `Dispose` precisará gerar um novo objeto `Connection`.

## Unit of Work

O padrão unidade de trabalho (Unit of Work) prevê a centralização das transações e em alguns casos até dos repositórios em um unico ponto.

O objetivo principal é garantir a transação entre todos os repositórios de forma uniforme. Em suma ele é um objeto bem simples, que só comita ou desfaz tudo que foi gerado na transação.

Novamente, como estamos trabalhando com objetos de conexão/transação no banco de dados, é importante implementar o `IDisposable` para garantir que nenhuma conexão ficará aberta.

```csharp
public interface IUnitOfWork : IDisposable
{
    void BeginTransaction();
    void Commit();
    void Rollback();
}
```

Na interface, declaramos apenas três métodos, para iniciar a transação (`BeginTransaction`), para comitar as mudanças (`Commit`) e para desfazer tudo caso algo dê errado (`Rollback`).

A implementação desta interface só precisa de uma instância do `DbSession`, com ela podemos criar uma nova transação e realizar o commit ou rollback da mesma.

```csharp
public sealed class UnitOfWork : IUnitOfWork
{
    private readonly DbSession _session;

    public UnitOfWork(DbSession session)
    {
        _session = session;
    }

    public void BeginTransaction()
    {
        _session.Transaction = _session.Connection.BeginTransaction();
    }

    public void Commit()
    {
        _session.Transaction.Commit();
        Dispose();
    }

    public void Rollback()
    {
        _session.Transaction.Rollback();
        Dispose();
    }

    public void Dispose() => _session.Transaction?.Dispose();
}
```

## Repository Pattern
O padrão repositório (Repository) prevê um ponto único de acesso a dados para uma entidade e frameworks como EF Core já trazem uma implementação do mesmo.

Os repositórios em suma não são o maior desafio, mas sim suas transações, principalmente entre repositórios.

Para este exemplo, separei o repositório de notificações com dois métodos simples de leitura e escrita.

```csharp
public class NotificationRepository
{
    private DbSession _session;

    public NotificationRepository(DbSession session)
    {
        _session = session;
    }

    public IEnumerable<NotificationModel> Get()
    {
        return _session.Connection.Query<NotificationModel>("SELECT * FROM [Notifications]", null, _session.Transaction);
    }

    public void Save(NotificationModel model)
    {
        _session.Connection.Execute("INSERT INTO [Notifications] VALUES(NEWID(), 'Title', 'URL', GETDATE())", null, _session.Transaction);
    }
}
```

Note que no final dos métodos nós temos o `_session.Transaction` sendo informado. Isto fará com que o `Dapper` considere a transação atual caso ela exista.

Sendo este um contexto transacionado, o método `Save` que realiza um `INSERT` só será considerado quando chamarmos o método `Commit` do nosso `UnitOfWork`.

## Injeção de Dependência

Para prosseguirmos, precisamos primeiro [resolver as dependências](https://balta.io/blog/aspnet-core-dependency-injection) que criamos e o ASP.NET faz bem isto através da sua DI nativa.

```csharp
services.AddScoped<DbSession>();
services.AddTransient<IUnitOfWork, UnitOfWork>();
services.AddTransient<NotificationRepository>();
```

Note que utilizamos `AddScopped` para o `DbSession` e `AddTransient` para o `UnitOfWork`, caso esteja confuso para você esta parte, recomendo a [leitura deste artigo](https://balta.io/blog/aspnet-core-dependency-injection).

## Controllers
Agora que temos tudo pronto, podemos finalizar nossa API simplesmente fazendo as devidas chamadas no controlador.

### Chamadas não transacionadas
A primeira chamada, apenas um `GET`, não será transacionada, então não precisamos do `UnitOfWork` aqui.

```csharp
[HttpGet("")]
public IEnumerable<NotificationModel> Get(
    [FromServices] NotificationRepository repository)
{
    return repository.Get();
}
```

### Chamadas transacionadas
Nossa segunda chamada será uma inserção, e queremos ter a possibilidade de revertê-la caso algo dê errado. Indo ainda mais afundo, queremos revertê-la caso algo dê errado em outro repositório inclusive.

Desta forma precisamos de uma instância do `UnitOfWork` e chamar o método `BeginTransaction` antes das chamadas do repositório.

Isto criará uma transação a nível do controlador e todas as chamadas ao banco informando `_session.Transaction` ficarão neste escopo.

Como resultado temos a possibilidade de persistir (`Commit`) ou reverter (`Rollback`) tudo que foi realizado nesta transação com a nossa unidade de trabalho (`UnitOfWork`).

```csharp
[HttpPost("")]
public IActionResult Post(
    [FromServices] IUnitOfWork unitOfWork,
    [FromServices] NotificationRepository repository)
{
    unitOfWork.BeginTransaction();
    repository.Save(new NotificationModel
    {
        Date = DateTime.Now,
        Id = Guid.NewGuid(),
        Title = "Teste",
        Url = "Teste"
    });
    unitOfWork.Commit();
    return Ok();
}
```

## Código Fonte
* [https://github.com/andrebaltieri/dapper-uow](https://github.com/andrebaltieri/dapper-uow)

## Conclusão
Embora frameworks ORM como EF Core nos tragam vários benefícios, em diversos momentos podemos nos deparar com a necessidade de um trabalho mais artesanal no acesso à dados e isto pode trazer algumas dificuldades extras como transação e conexões ao banco.

Com `UnitOfWork`, `Dapper` e `Repository Pattern` conseguimos entregar algo consistente e performático levando em conta os cenários acima.

<div role="main" id="blog-s1-dotnet-134e3db1eea6c9829db1"></div>
<script type="text/javascript" src="https://d335luupugsy2.cloudfront.net/js/rdstation-forms/stable/rdstation-forms.min.js"></script>
<script type="text/javascript"> new RDStationForms('blog-s1-dotnet-134e3db1eea6c9829db1', 'UA-48664517-12').createForm();</script>