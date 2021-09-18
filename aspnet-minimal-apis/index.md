Neste artigo vamos criar uma API completa com menos de 50 linhas de código!

## Minimal APIs
Uma das novidades do .NET 6/ASP.NET 6 é a chegada de um novo template, que vai substituir o padrão e trazer uma boa redução na escrita de código.

Desta forma, ao executarmos o comando `dotnet new web` com o .NET 6 instalado, teremos a criação de um template com o mínimo de código possível.

Vale lembrar que você ainda pode usar o `dotnet new mvc | razorpages` para criar templates mais robustos que atendam outras necessidades.

### Criando sua primeira Minimal API
Para executar os códigos a seguir, você precisa do .NET 6 (Estou usando o Preview 1) [instalado e configurado](https://balta.io/blog/dotnet-instalacao-configuracao-e-primeiros-passos). Com isto feito, basta executar o comando abaixo:

```
dotnet new web -o MiniTodo
```

Uma nova API será criada e ao abri-la com Visual Studio ou Visual Studio Code, você vai se deparar com apenas um arquivo, chamado `Program.cs` com o seguinte conteúdo:

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.MapGet("/", () => "Hello World");

app.Run();
```

Acredite, isto é uma API, você pode executar o comando `dotnet run` na pasta gerada anteriormente para rodá-la e ver a mensagem `Hello World` na tela!

## Criando um Record
O C# 9 introduziu um novo tipo para estruturas de dados, chamado **Record**, que permite ter recursos de objetos em estruturas mais simples.

Este tipo é o caso perfeito para nosso cenário, já que não teremos nenhum comportamento aqui, apenas dados.

Desta forma, vamos criar nosso **Record** chamado **Todo.cs** e para organizar melhor, coloquei ele dentro de uma pasta chamada `Models`.

```csharp
public record Todo(Guid Id, string Title, bool Done);
```

Note que os **Records** são bem menos burocráticos e podemos definir suas propriedades já na declaração do mesmo.

## Tipando o retorno
Na minha opinião, algo mágico (E que funciona muito bem) no ASP.NET é o **Model Binding**. Nossa API vai receber/retornar JSON, mas no C# trabalhamos com objetos e o **Model Binding** é o responsável por fazer esta tradução.

Desta forma, podemos simplesmente retornar um **Record** ou qualquer outro objeto que o **Model Binding** vai convertê-lo para JSON antes de enviar para quem requisitou.

```csharp
var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

app.MapGet("/", () => new Todo(Guid.NewGuid(), "Ir a academia", false));

app.Run();
```

Ao executar a API neste momento, você deverá ver um JSON com as propriedades e valores que informamos no **Todo** acima.

## HTTP Status Code
Outro item importante é o código HTTP que retornamos. Por exemplo, na criação de um item, o correto é retornar o status `201 - Created`.

Felizmente o ASP.NET já traz tudo isto pronto, e podemos simplesmente utilizar o `Results` para informar o status.

```csharp
app.MapGet("/", () => Results.Ok(new Todo(Guid.NewGuid(), "Ir a academia", false)));
```

## Entity Framework
Legal, poucas linhas, mas no mundo real vamos trabalhar com dados, então como fica o Entity Framework neste cenário?

O primeiro passo é adicionar os pacotes necessários para o EF funcionar:
```
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
dotnet add package Microsoft.EntityFrameworkCore.Design
```

Em seguida, vamos criar o nosso Data Context, que é a representação do banco de dados em memória. Aliás, para este exemplo, estou utilizando o **SQLite**!

```csharp
using Microsoft.EntityFrameworkCore;

namespace MiniTodo.Data
{
    public class AppDbContext : DbContext
    {
        public DbSet<Todo> Todos { get; set; }

        protected override void OnConfiguring(DbContextOptionsBuilder options)
            => options.UseSqlite("DataSource=app.db;Cache=Shared");
    }
}
```

Nada diferente aqui, é o mesmo arquivo que uso em outros exemplos com EF inclusive. Para organizar melhor, deixei esta classe na pasta **Data**.

### Migrations
Os Migrations também funcionam perfeitamente, e como você pode notar, não há um banco chamado **app.db** ainda.

Desta forma, podemos executar os códigos abaixo para criar uma migração e gerar o banco de dados.

```
dotnet ef migrations add InitialCreation
dotnet ef database update
```

> Para esta etapa você precisará das ferramentas do EF instaladas. Caso ainda não tenha feito, execute o comando **dotnet tool install --global dotnet-ef** antes das migrações.

Pronto, temos nosso Data Context e nosso banco gerado!

### Injeção de Dependência
Outro recurso do ASP.NET (Todos estão presente) que gosto bastante é a DI, que ao meu ver funciona muito bem.

Assim como fazemos nas APIs com MVC, aqui podemos injetar o DbContext criado via DI como mostrado abaixo:

```csharp
using MiniTodo.Data;

var builder = WebApplication.CreateBuilder(args);
builder.Services.AddDbContext<AppDbContext>();

...
```

### GET
Agora que temos nosso Data Context, nada mais justo que utilizar ele no método GET da API, e para isto basta criar uma referência a ele no método.

```csharp
app.MapGet("/v1/todos", (AppDbContext context) =>
{
    var todos = context.Todos;
    return todos is not null ? Results.Ok(todos) : Results.NotFound();
});
```

Note que no retorno do método eu utilizo um recurso novo do C# chamado Pattern Matching, onde comparo `todos is not null` ao invés de `todos != null`.

Esta comparação nos permite retornar `Results.NotFound()` (404) ou `Results.Ok()` (200).

## ViewModels e validações
E validações? Pacotes externos, View Models? Sim! Dá para usar tudo isto aqui, e vamos começar instalando o Flunt:

```
dotnet add package Flunt
```

Em seguida, podemos criar nossa View Model chamada **CreateTodoViewModel** na pasta ViewModels para ficar mais organizado.

```csharp
using Flunt.Notifications;
using Flunt.Validations;

namespace MiniTodo.ViewModels
{
    public class CreateTodoViewModel : Notifiable<Notification>
    {
        public string Title { get; set; }

        public Todo MapTo()
        {
            AddNotifications(new Contract<Notification>()
                .Requires()
                .IsNotNull(Title, "Informe o título da tarefa")
                .IsGreaterThan(Title, 5, "O título deve conter mais de 5 caracteres"));

            return new Todo(Guid.NewGuid(), Title, false);
        }
    }
}
```

Para facilitar, criei um método chamado `MapTo` que retorna o Todo! Desta forma, nosso método POST fica assim:

```csharp
app.MapPost("/v1/todos", (AppDbContext context, CreateTodoViewModel model) =>
{
    var todo = model.MapTo();
    if (!model.IsValid)
        return Results.BadRequest(model.Notifications);

    context.Todos.Add(todo);
    context.SaveChanges();

    return Results.Created($"/v1/todos/{todo.Id}", todo);
});
```

## Swagger
Para finalizar nossa API, vamos adicionar suporte ao Swagger para documentação da mesma!

```
dotnet add package Swashbuckle.AspNetCore
```

Com os pacotes instalados, podemos adicionar as referências do Swagger:

```csharp
...
var builder = WebApplication.CreateBuilder(args);

builder.Services.AddDbContext<AppDbContext>();
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

var app = builder.Build();
app.UseSwagger();
app.UseSwaggerUI();
...
```

Agora basta executar a API e navegar para `localhost:5001/swagger/index.html` que teremos a API documentada.

### Tipagem no Swagger
Como retornamos `Results.Ok` o Swagger não consegue entender o tipo do retorno e gerar os schemas. Para isto podemos usar o `Produces`:

```csharp
app.MapGet("/v1/todos", (AppDbContext context) =>
{
    var todos = context.Todos;
    return todos is not null ? Results.Ok(todos) : Results.NotFound();
}).Produces<Todo>();
```

Agora temos suporte a documentação com Schemas!

## Conclusão
Podemos criar APIs simples e rápidas com ASP.NET Minimal APIs e ainda assim usar todos os recursos presentes no ASP.NET!

### Fontes e Recursos
* [Código Fonte](https://github.com/andrebaltieri/minimal-api-sample)
* [Live no canal Developers BR](https://www.youtube.com/watch?v=iV0iB2l43pw&t=3s)
* [Nick Chapsas](https://www.youtube.com/watch?v=XKN0084p7WQ&t=862s)