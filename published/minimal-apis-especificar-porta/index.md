Aprenda a especificar a porta e configurar os detalhes do seu servidor Web na hora de rodar aplicações ASP.NET.

## Minimal APIs - Especificar a porta
Tanto o ASP.NET MVC quando as Minimal APIs permitem a configuração dos detalhes da execução da aplicação.

Em determinados cenários, mais controlados principalmente, precisamos executar a aplicação em uma porta específica.

Desta forma, podemos passar a **URL** de execução que desejamos no método `Run`.

```csharp
var app = WebApplication.Create(args);

app.MapGet("/", () => "Hello World!");

app.Run("http://localhost:3000"); // Aqui
```

## Minimal APIs - Especificar várias portas
Da mesma forma, podemos especificar várias portas, utilizando o método `Urls.Add(<<URL>>)`, como mostrado abaixo.

Isto fará com que o servidor fique ouvindo em todas as portas especificadas.

```csharp
var app = WebApplication.Create(args);

app.Urls.Add("http://localhost:3000");
app.Urls.Add("http://localhost:4000");

app.MapGet("/", () => "Hello World");

app.Run();
```
[![Aprenda .NET do ZERO ao PROFISSIONAL](https://baltaio.blob.core.windows.net/static/images/temp/chamada-carreira-dotnet.png)](https://balta.io/carreiras/desenvolvedor-backend-dotnet)

## Minimal APIs - Atribuir a porta dinamicamente
Porém, caso queira especificar a porta durante a execução, ou seja, não deixar isto especificado no código, podemos especificar o parâmetro `--urls` durante a execução.

```
dotnet run --urls="https://localhost:1234"
```

## Minimal APIs - Atribuir a porta via variável de ambiente

Para finalizar, podemos utilizar as variáveis de ambiente, neste caso a **PORT** para definir a porta.

Desta forma, podemos recuperar a variável de ambiente utilizando o `Environment.GetEnvironmentVariable(<<CHAVE>>)`.

```csharp
var app = WebApplication.Create(args);

var port = Environment.GetEnvironmentVariable("PORT") ?? "3000";

app.MapGet("/", () => "Hello World");

app.Run($"http://localhost:{port}");
```

## Fontes
* [Documentação Oficial do ASP.NET](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/minimal-apis?view=aspnetcore-6.0)
