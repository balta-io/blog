O ASP.NET 7 já tem data prevista e os primeiros Previews já estão disponíveis. Neste artigo vamos ver as primeiras mudanças que vem por aí na nova versão.

## Instalando o .NET 7
Até a escrita deste artigo o .NET 7 está em sua segunda Preview e para conferir todas as novidades do ASP.NET 7 você precisa [**instalar o SDK desta versão**](https://dotnet.microsoft.com/en-us/download/dotnet/7.0).

Não se preocupe pois as versões anteriores do .NET na sua máquina não serão alteradas, o .NET roda lado a lado, não sendo necessário remover instalações antigas.

## Atualizando seu projeto
Caso tenha criado algum projeto em .NET 6 e queira atualizar para o 7, basta alterar as dependências no arquivo `.csproj` apontando para versão 7.

 * Microsoft.AspNetCore.* => 7.0.0-preview.2.*.
 * Microsoft.Extensions.* => 7.0.0-preview.2.*.

## Inferência de parâmetros nas ações dos Controllers
Se você conferiu nosso [**artigo sobre Minimal APIs**](https://balta.io/blog/aspnet-minimal-apis), você provavelmente notou que não utilizamos `[FromServices]`, `[FromBody]`, `[From*]` nos parâmetros dos métodos que respondem as rotas.

Isto por que o ASP.NET infere estes parâmetros, fazendo uma busca nas dependências, corpo da requisição, cabeçalho da requisição e até na Query String.

Este recurso também estará presente no ASP.NET 7, não sendo mais necessário especificar de onde o parâmetro vem. Pelo menos na maioria dos casos, não.

```csharp
Services.AddScoped<SomeCustomType>();

[Route("[controller]")]
[ApiController]
public class MyController : ControllerBase
{
    // Ambos métodos irão funcionar
    public ActionResult GetWithAttribute([FromServices]SomeCustomType service) => Ok();
    public ActionResult Get(SomeCustomType service) => Ok();
}
```

Caso queira desabilitar este comportamento, basta setar a propriedade `DisableImplicitFromServicesParameters` para `true` nas configurações da sua API, como demonstrado abaixo.

```csharp
Services.Configure<ApiBehaviorOptions>(options =>
{
     options.DisableImplicitFromServicesParameters = true;
})
```

## SignalR agora suporta DI
O SignalR também receberá uma atualização, permitindo a injeção de dependências nos métodos dos Hubs, e assim como no ASP.NET, não necessitamos do `[FromServices]`.

```csharp
Services.AddScoped<SomeCustomType>();

public class MyHub : Hub
{
    // SomeCustomType vem da DI por padrão agora
    public Task Method(string text, SomeCustomType type) => Task.CompletedTask;
}
```
Caso queira desabilitar este comportamento, basta setar a propriedade para `true` nas configurações da sua API, como demonstrado abaixo.`DisableImplicitFromServicesParameters`

```csharp
services.AddSignalR(options =>
{
    options.DisableImplicitFromServicesParameters = true;
});
```

Caso queira explicitar a origem do parâmetro, você ainda pode utilizar o `[FromServices]` também, sem problema algum.

```csharp
public class MyHub : Hub
{
    public Task Method(string arguments, [FromServices] SomeCustomType type);
}
```

## Descrição e sumário nas Minimal APIs
Uma adição nas Minimal APIs é a possibilidade de informarmos uma descrição e/ou sumário a um método, que poderá ser refletido posteriormente na documentação da API.

```csharp
app.MapGet("/hello", () => ...)
  .WithDescription("Sends a request to the backend HelloService to process a greeting request.");

// Ou

app.MapGet("/hello", [EndpointSummary("Sends a Hello request to the backend")]() => ...)

```

## Arrays e StringValues nas Minimal APIs
Outra adição as Minimal APIs será a possibilidade de fazer bind dos valores do cabeçalho HTTPS e Query Strings para arrays de tipos primitivos ou StringValues.

```csharp
// Bind query string values to a primitive type array
// GET  /tags?q=1&q=2&q=3
app.MapGet("/tags", (int[] q) => $"tag1: {q[0]} , tag2: {q[1]}, tag3: {q[2]}")

// Bind to a string array
// GET /tags?names=john&names=jack&names=jane
app.MapGet("/tags", (string[] names) => $"tag1: {names[0]} , tag2: {names[1]}, tag3: {names[2]}")

// Bind to StringValues
// GET /tags?names=john&names=jack&names=jane
app.MapGet("/tags", (StringValues names) => $"tag1: {names[0]} , tag2: {names[1]}, tag3: {names[2]}")
```
No caso das Query Strings podemos inclusive fazer bind de valores para tipos complexos.

```csharp
// Bind to an array of a complex type
// GET /tags?tags=trendy&tags=hot&tags=spicy
app.MapGet("/tags", (Tag[] tags) =>
{
    return Results.Ok(tags);
});

...

class Tag 
{
    public string? TagName { get; init; }

    public static bool TryParse(string? tagName, out Tag tag)
    {
        if (tagName is null) 
        {
            tag = default;
            return false;
        }

        tag = new Tag { TagName = tagName };
        return true;
    }
}
```

## Customizar o consentimento dos Cookies
Teremos a adição da propriedade `CookiePolicyOptions.ConsentCookieValue` para acompanhar e customizar o consentimento do usuário em relação aos Cookies que armazenamos.

## Fonte
 * https://devblogs.microsoft.com/dotnet/asp-net-core-updates-in-dotnet-7-preview-2