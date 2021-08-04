Dapper ou Entity Framework Core? Se você já trabalhou com acesso à dados, com certeza se fez esta pergunta. Se ainda não teve esta oportunidade, vamos conferir agora.

![Dapper vs Entity Framework Core](https://baltaio.blob.core.windows.net/static/images/articles/dapper-entity-framework-core_share.jpg "Dapper vs Entity Framework Core")

## Dapper ou Entity Framework Core?
Tanto o Dapper quanto o Entity Framework Core estão classificados como Frameworks ORMs (Object/Relational Mapping) ou mapeamento objeto relacional.

Na verdade, o Dapper se classifica como um Micro ORM por ser menor, ter menos funcionalidades e consequentemente ser mais leve.

Em ambos, a finalidade é facilitar a execução de operações sobre bancos de dados relacionais como CREATE, READ, UPDATE e DELETE (CRUD).

O SQL Server (Ou qualquer outro banco relacional) trabalha com uma linguagem diferente do C# (O SQL), então o objetivo de ambos aqui é puro e simplesmente converter o retorno do SQL para C# e vice-versa.

No caso do Entity Framework Core, existem várias outras facilidades que ele nos provê por ser mais robusto, algumas delas até conseguimos agregando outras bibliotecas ao Dapper, mas não é nosso foco aqui.

Em resumo, o que queremos é executar operações no banco de dados com C# de uma forma fácil. Por isto vamos comparar os dois.

## Dapper e Entity Framework Core: O que acontece por baixo dos panos?
Independente da sua escolha, o processo para conexão e operações no banco de dados é sempre o mesmo, o que o Dapper ou Entity Framework Core vão nos prover é uma abstração disso.

### Connection String
A Connection String define como vamos nos conectar ao banco de dados, qual usuário e banco vamos utilizar e outros recursos de segurança.

> Caso ainda não tenha o SQL Server instalado, recomendo que realize [este tutorial aqui](https://balta.io/blog/sql-server-docker).

Para estes exemplos, vamos tomar como base uma Connection String simples, que usamos nos cursos do balta.io.

```csharp
const string connectionString = "Server=localhost,1433;Database=balta;User ID=sa;Password=1q2w3e4r@#$";
```

### Microsoft.Data.SqlClient
Embora o .NET não tenha uma biblioteca para acesso à dados já incluso, a Microsoft disponibiliza o **Microsoft.Data.SqlClient**, um pacote oficial para conexão e operações no **SQL Server**.

Para adicionar este pacote, basta executar o comando abaixo:
```
dotnet add package Microsoft.Data.SqlClient
```

Este pacote é a base para conexões ao **SQL Server**, e tanto no Dapper quanto Entity Framework Core, precisamos dele. No caso do Entity Framework Core, ele é instalado automaticamente junto a outro pacote que vou mostrar mais adiante.

### Conectando ao banco
Com o pacote instalado, precisamos nos conectar ao banco de dados antes de executar qualquer operação, e isto é feito utilizando o `SqlConnection`.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    ...
}
```

Note que utilizamos o `using` aqui para fazer o `Dispose` automático deste objeto, afinal, não queremos ninguém conectado ao banco de dados mais do que o necessário.

> Ficou confuso sobre o **Dispose**? Então dá uma [conferida nosso curso Fundamentos do C#](https://balta.io/cursos/fundamentos-csharp) que lá explico tudo com detalhes.

### Comandos
Com a conexão criada, podemos abrí-la e executar quaisquer comandos que desejarmos no SQL Server utilizando o `SqlCommand`.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    connection.Open();

    using (var command = new SqlCommand())
    {
        command.Connection = connection;
        command.CommandType = System.Data.CommandType.Text;
        command.CommandText = "SELECT [Id], [Title] FROM [Category]";

        ...
    }
}
```

### SqlDataReader
Pergunta de entrevista: "Qual forma mais rápida de leitura de dados no SQL Server via .NET/C#?"

A resposta é **SqlDataReader**, um cursor **Forward Only** (Só anda para frente) que tanto o Dapper quanto Entity Framework Core utilizam para leitura de dados.

O método `ExecuteReader` retorna um `SqlDataReader` e então podemos iterar por ele.

```csharp
var reader = command.ExecuteReader();
while (reader.Read())
{
    Console.WriteLine($"{reader.GetGuid(0)} - {reader.GetString(1)}");
}
```

O código final deste método fica assim:
```csharp
using (var connection = new SqlConnection(connectionString))
{
    connection.Open();

    using (var command = new SqlCommand())
    {
        command.Connection = connection;
        command.CommandType = System.Data.CommandType.Text;
        command.CommandText = "SELECT [Id], [Title] FROM [Category]";

        var reader = command.ExecuteReader();
        while (reader.Read())
        {
            Console.WriteLine($"{reader.GetGuid(0)} - {reader.GetString(1)}");
        }
    }
}
```

### Por que isto é importante?
Como você pode notar, escrevemos bastante código para fazer uma simples leitura do banco de dados. Isto em uma escala maior seria suicídio, eu recomendaria este método apenas em casos de **extrema** necessidade de performance.

Porém, entender o que acontece por baixo dos panos do Dapper e do Entity Framework Core nos ajuda a entender o ponto mais importante sobre os ORMs: A finalidade deles.

Basicamente, o que o Dapper e o Entity Framework Core fazem é executar este `SqlDataReader` (Para casos de leitura) e converter o resultado dele para um `objeto` do C#.

Então dada uma `Query` como esta:
```sql
SELECT [Name], [Age], [Salary] FROM [Customer]
```

O Dapper ou Entity Framework Core conseguiria converter o `SqlDataReader` para o seguinte objeto:
```csharp
public class Customer
{
    public string Name { get; set; }
    public int Age { get; set; }
    public decimal Salary { get; set; }
}
```

> Entender SQL é fundamental para qualquer desenvolvedor(a), então caso precise de uma ajuda, [veja nosso curso de Fundamentos do SQL Server](https://balta.io/cursos/fundamentos-sql-server).

## Começando com Dapper
Particularmente gosto bastante do Dapper pela simplicidade e performance, ele é um pacote desenvolvido e mantido pelo time do **Stack Overflow** e que está em constante evolução.

Para utilizar o Dapper, basta adicionar o pacote `Dapper` como mostrado abaixo.
```
dotnet add package Dapper
```

O Dapper também exige um `SqlConnection` porém consegue reduzir muito nosso código adicionando extensões a ele.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var query = "SELECT [Name], [Age], [Salary] FROM [Customer]";
    var customers = connection.Query<Customer>(query);
}
```

O que o Dapper fez foi adicionar a extensão `Query<T>` ao objeto `SqlConnection`, permitindo que façamos o `Parse` (Conversão) do resultado vindo do SQL Server para um objeto do C# de forma extremamente simples.

> Ficou com dúvidas sobre `Parse`, `Generics` e outros assuntos relacionados a OOP? Então [faz nosso curso de Fundamentos da Orientação a Objetos](https://balta.io/cursos/fundamentos-orientacao-objetos).

A mágica toda aconteceu por que temos uma `Query` que retorna os mesmos campos que temos como propriedades no objeto `Customer`. Se mudassemos de `Name` para `Nome` na `Query` ou na propriedade do objeto `Customer`, teríamos um erro.

### Dapper Contrib
Ainda podemos melhorar este código utilizando um pacote adicional ao Dapper, chamado `Dapper.Contrib`.

```
dotnet add package Dapper.Contrib
```

O `Dapper.Contrib` adiciona ainda mais extensões ao `SqlConnection` que gera até a query para nós. Isto mesmo, não precisamos escrever o `SQL`.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var customers = connection.GetAll<Customer>();
}
```

Novamente, a mágica acontece por que temos os mesmos campos na tabela `Customer` como as propriedades no objeto `Customer`, então ele é inteligente o suficiente para gerar um `SELECT` com base nisso.

O mesmo vale para os outros métodos:
```csharp
connection.GetAll<Customer>();
connection.Get<Customer>(id);
connection.Insert<Customer>(customer);
connection.Update<Customer>(customer);
connection.Delete<Customer>(id);
```

Senhoras e senhores, isto é boa parte do que o Dapper, Entity Framework Core e qualquer outro ORM faz por nós!

## Começando com Entity Framework Core
O Entity Framework Core trabalha de uma forma diferente do Dapper, ele precisa de um pouco mais de configuração, porém entrega muito mais "mágica".

Para trabalhar com Entity Framework Core e SQL Server precisamos adicionar dois pacotes:

```
dotnet add package Microsoft.EntityFrameworkCore
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

### Data Context
O Entity Framework Core trabalha com um conceito chamado **Data Context** ou contexto de dados, que é a representação do banco de dados transportado para nossos objetos.

Em suma o **Data Context** é apenas uma class que concentra quais tabelas do banco queremos mapear para nossos objetos.

```csharp
public class MyDbContext : DbContext 
{
    public MyDbContext(DbOptions options) : base(options) { }

    public DbSet<Customer> Customers { get; set; }
}
```

Desta forma, podemos ter **N** objetos em nossa aplicação, mas o Entity Framework Core vai tomar conta apenas dos objetos que informarmos nos `DbSet` nesta classe, como fizemos com o `Customer`.

### Conexão
O Entity Framework Core também se diferencia do Dapper na criação e gerenciamento da conexão com o banco de dados.

Normalmente nas aplicações ASP.NET, temos um item específico para configurar o Entity Framework Core, com um código similar a este.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddDbContext<MyDbContext>()
        .UseSqlServer("CONNECTION_STRING");
    ...
}
```

Isto é tudo que precisamos configurar para começar a trabalhar com Entity Framework Core. Porém, é recomendado trabalhar na parte de mapeamento dentre N outros itens que não vamos cobrir aqui.

### Operações com Entity Framework Core
Embora tenha uma configuração mais complexa de início, o Entity Framework Core traz algumas facilidades que podem influenciar muito na nossa produtividade.

```csharp
// Lendo todos os registros
var customers = context.Customers.ToList();

// Lendo um registro
var customers = context.Customers.FirstOrDefault(x => x.Id == id);

// Criando um registro
context.Customers.Add(customer);
context.SaveChanges();

// Lendo todos os registros
context.Customers.Update(customer);
context.SaveChanges();

// Lendo todos os registros
context.Customers.Remove(customer);
context.SaveChanges();
```

### Unit of Work
Você deve ter notado no código acima o uso do `context.SaveChanges()`, isto ocorre pelo fato do Entity Framework Core trabalhar com um padrão chamado **Unit of Work** ou unidade de trabalho.

Enquanto não executamos o `SaveChanges()` nada é persistido no banco, ou seja, temos uma espécie de transação acontecendo aqui.

## LINQ
Até então, nada especial com o Entity Framework Core, correto? Mas aí entra uma questão legal dele, as `queries` usando LINQ.

Depois de devidamente mapeadas e referenciadas, podemos executar consultas utilizando o **LINQ** e o Entity Framework Core traduzirá elas para linguagem SQL.

> Quer aprender mais sobre LINQ, listas e OOP? Então [faz nosso curso de Fundamentos da Orientação a Objetos](https://balta.io/cursos/fundamentos-orientacao-objetos).

Podemos por exemplo carregar todos os produtos e suas respectivas categorias sem muito esforço.
```csharp
var products = context.Products.Include(x => x.Category).ToList()
```

Isto resultará em uma `query` similar a isto:
```sql
SELECT
    * -- Usado apenas como exemplo
FROM
    [Product]
    INNER JOIN [Category] 
        ON [Product].[CategoryId] = [Category].[Id]
```

Em resumo, é uma baita economia de código, e diga-se de passagem, ele faz bastante mágica.

Podemos ir além e executar queries mais complexas ainda, como por exemplo carregar um pedido que possui vários itens e estes itens possuem um produto.

```csharp
var orders = context
    .Order
    .Include(x => x.Lines)
    .ThenInclude(x => x.Product);
```

O mesmo vale para sintaxe sem `Lambda Expression`.
```csharp
var orders = from order in context.Orders select order;
```

## SQL ou LINQ?
Outro ponto a se ponderar é o uso excessivo tanto de queries (SQL) quanto de LINQ. Embora o uso do LINQ traga vários benefícios como produtividade e erros em tempo de compilação, há cenários onde claramente a sintaxe SQL se destaca.

Então o segredo é ponderar o uso. Se está tentando replicar a consulta SQL via LINQ e não está conseguindo, então que tal executar uma SQL pura?

Aliás, o Entity Framework Core também permite que você execute sintaxes SQL (queries cruas -- Raw Queries) através do método `context.ExecuteRawQuery<T>("SELECT ...")`.

Não se limite a uma única visão, saiba como ambos funcionam e tire o melhor proveito de cada um em cada situação.

## Dapper ou Entity Framework Core, qual devo utilizar?

Mas e aí, no resumão, compensa usar Dapper ou Entity Framework Core? Tenho que dizer que é uma pergunta que eu responderia com depende, mas vou deixar algumas dicas.

Em diversos cenários menores, como por exemplo nas `Azure Functions` que tenho aqui no balta.io, onde preciso somente fazer um `SELECT` em uma tabela e retornar os dados, eu prefiro o **Dapper**.

As referências dele são simples e leve, o pacote é bem polido e para uma situação onde não tenho quase nada de escrita, ele é perfeito.

Já no contexto de Backoffice (Cadastro de cursos, categorias, artigos e afins) eu uso massivamente o Entity Framework Core.

Ele me dá toda produtividade que preciso, já que o Backoffice é acessado por 3~5 usuários apenas. Além disso a integração do Visual Studio com o Entity Framework Core me permite gerar telas de forma automática, poupando ainda mais tempo.

No cenário de conteúdo (Site e Player onde vocês assistem as aulas) por exemplo, eu também uso o Entity Framework Core, por pura comodidade.

Hoje temos em torno de 30k alunos, por volta de 7k conexões simultâneas, o Entity Framework Core dá conta disso tranquilamente. Na balança **Performance** vs **Produtividade** pesamos para o lado da **Produtividade** neste momento.

Ainda tem um terceiro cenário, que é uma tela apenas, a **Home** do Player, onde preciso carregar cursos, carreiras e vários itens do aluno.

Neste cenário eu uso o `QueryMultiple` do Dapper e faço a leitura de uma View que temos no SQL Server, é a forma mais otimizada que consegui resolver estas diversas queries quer precisamos executar.

Futuramente, com a chegada do Entity Framework Core 6, pretendo colcar esta query no Entity Framework Core e remover o Dapper, simplesmente por termos mais necessidade do Entity Framework Core do que dele, não por que ele é ruim.

Por fim mas não menos importante, se você está começando, estudando, o **Dapper** é sua porta de entrada. Por abstrair menos coisas, você será forçado a aprender mais sobre acesso à dados.

Inclua também na sua lista aprender `SQL`, sim, ele é extremamente importante e muitas pessoas negligenciam isto.

Basicamente todo sistema se conecta a um banco de dados e embora os **NoSQL** sejam fantásticos, a probabilidade de você trabalhar com um banco **Relacional** é muito maior.

Sabendo bem os fundamentos, acesso à dados, **Dapper** e **Entity Framework Core**, você está preparado para o que der e vier.