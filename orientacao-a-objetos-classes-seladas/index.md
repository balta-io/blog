Na orientação a objetos, uma classe selada não pode ser extendida e tomar esta decisão design pode ter diferentes impactos no consumo do seu código. Neste artigo vamos entender melhor quando utilizá-las.

## Sobre o Flunt

[Flunt](https://github.com/andrebaltieri/flunt) é um pacote que criei para aplicar validações e o padrão Domain Notification. Com o tempo ele se tornou popular e tem até versões em Java/JS, o que é bem legal.

## Modificador Sealed
No C# e em diversas outras linguagens orientadas à objetos nós temos um modificador chamado `sealed` que previne a classe de ser extendida.

Por tempos eu fiquei me perguntando onde eu usaria este modificador, qual seria um caso real para ele, e em 2017 acabei utilizando no Flunt.

Para tornar uma classe selada (Impedir que a mesma seja extendida), basta utilizar o seguinte exemplo.

```csharp
public sealed class Notification 
{
    ...
}
```

Desta forma, se tentarmos criar uma nova classe que derive de `Notification` nos depararemos com o seguinte erro.

```csharp
public class CustomNotification : Notification
{
    ...
}
```

**ERRO**
```
'Program.CustomNotification': cannot derive from sealed type 'Program.Notification'
```

## Motivações

Tudo bem, você já entendeu o que é o `sealed` e como utilizar, mas agora vem a motivação para o seu uso.

Nas primeiras versões do Flunt eu não selei a classe `Notification` o que permitiu que outras classes fossem criadas sobre ela, e durante uma passagem por um dos meus clientes, notei que ele possuiam customizações destas notificações.

Não vou me recordar 100% agora, mas era algo como isto.

```csharp
// Classe do Flunt
public class Notification 
{
    ...
}

// Derivações (No cliente)
public class ErrorNotification 
{
    ...
}

public class WarningNotification 
{
    ...
}

public class SuccessNotification 
{
    ...
}
```

Cada uma destas derivações tinha suas próprias customizações, seus métodos e suas propriedades. Como temos a possibilidade de `upcast` na OOP, estas classes também podiam substituir sua classe pai, a `Notification`.

Mas qual o problema nisso? Qual problema em criar customizações?

Algumas coisas não foram feitas para serem customizadas ou você não quer permitir que elas sejam feitas de formas diferentes.

A ideia por trás das notificações é ter uma padronização nas APIs por exemplo, onde todo erro 400 (BadRequest) retorna uma lista de notificações, algo parecido com isto.

```json
{
    "status": 400,
    "message": "Falha na requisição",
    "data": null,
    "notifications": [
        { "Name": "Nome inválido" },
        { "Document": "CPF inválido" },
    ]
}
```

Desta forma, qualquer aplicação que consuma a API pode se preparar para o mesmo padrão, isto facilita a vida do Frontend que pode ter uma tipagem sobre o retorno de qualquer método da API, em suma, qualquer requisição a API resulta neste modelo.

Focando apenas no nó `notifications`, a classe `Notification` do Flunt deveria garantir que seu resultado fosse sempre neste padrão, ou seja, quem consome a API sempre pode esperar uma lista de notificações contendo chave/valor.

Porém, ao customizar (Extender) as notificações, o resultado podia variar de acordo com o tipo da notificação.

```json
{
    "status": 400,
    "message": "Falha na requisição",
    "data": null,
    "notifications": [
        { "Name": "Nome inválido" },
        { "Document": "CPF inválido" },
    ]
}
```

```json
{
    "status": 200,
    "message": "Cadastro realizado",
    "data": null,
    "notifications": [
        { "id": 1, "type": "success", "value":"Cadastro OK" }
    ]
}
```

```json
{
    "status": 200,
    "message": "Cadastro realizado",
    "data": null,
    "notifications": [
        { "type": "success", "key":"XYZ", "value":"XPTO" }
    ]
}
```

Isto geraria no mínimo um `switch` no Frontend para saber o tipo de notificação e então tratá-la de forma diferente. Ainda vale lembrar que o Frontend não está limitado a Web, tem Flutter, Xamarin e várias outras tecnologias/linguagens.

Deste primeiro cenário veio a motivação para selar a classe `Notification` e padronizar todas as notificações dos sistemas que usam o Flunt.

## Outro exemplo

Como comentei no começo deste artigo, esta foi uma decisão que tomei em 2017, mas o que me motivou a escrever este artigo foi outro cenário que passei hoje.

Em alguns pontos críticos de leitura das Apps/Apis do balta eu utilizo o `Dapper` para acesso à dados, inclusive [tem um curso sobre isto aqui](https://balta.io/cursos/criando-apis-com-aspnetcore-2-e-dapper).

Porém, o Dapper é um micro-orm que extende o `IDbConnection` apenas, o que significa que estamos literalmente por conta própria para tudo, inclusive para gerenciar a conexão com o banco de dados.

Desta forma, eu sempre crio um classe para gerenciar a conexão e garantir que não vou deixar conexão aberta no meu `SQL Server`.

Fazer isto não é uma tarefa difícil, basta implementar a interface `IDisposable` e chamar o método `Connection.Dispose` para encerrar a conexão.

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

Mas e aí, você notou algo de diferente nesta classe? Sim, ela está selada e você já deve imaginar o motivo!

Pois é, se tem uma classe que não quero que alguém extenda é esta, ela é uma das mais importantes e sensíveis da aplicação, e sem o `sealed` nela, poderiam ter várias versões da mesma.

Ainda pior, com a possibilidade de `upcast` poderiamos facilmente substituir a conexão padrão por uma customizada que talvez esquecesse de fechar a conexão com o banco de dados.

Imagina o problemão que não arrumaríamos?

## Conclusão
As vezes, na correria do dia-a-dia tendemos a fazer o mais fácil, e em diversos cenários, optaríamos por criar uma nova versão de uma classe do que dar manutenção e melhorá-la.

O modificador `sealed` age neste ponto, ele te ajuda a travar as classes que você não quer que sejam extendidas e forçar as coisas a seguirem um padrão.

Mas calma, não é para sair selando todas as classes do sistema só porque aprendeu algo novo hoje!
