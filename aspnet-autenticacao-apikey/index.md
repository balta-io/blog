Neste artigo vamos entender um pouco mais sobre API Keys e como podemos trazer uma autenticação mais simplificadas as nossas APIs.

## Autenticação e autorização
Antes de continuar neste artigo é importante ter bem definidos os conceitos de autenticação e autorização, conforme descrevi [neste artigo](https://balta.io/blog/aspnet-core-autenticacao-autorizacao).

Caso queira uma autenticação baseado em perfis, ou mesmo entender do que se tratam JWT, Token, Roles e Claims, [veja este artigo](https://balta.io/blog/aspnet-5-autenticacao-autorizacao-bearer-jwt) onde pontuo todos estes itens.

## ApiKey
O conceito de chaves de API ou ApiKey trata-se do envio de uma chave encriptada na URL ou cabeçalho da requisição.

Este modelo não deve ser utilizado em autenticação do lado do cliente, como aplicações em JavaScript por exemplo, visto que o usuário teria acesso a esta chave via browser.

Esta chave deve ser utilizada para autenticação de clientes que rodam do lado do servidor, como uma outra API por exemplo. Desta forma sua chave fica sempre no servidor (Ou banco de dados), em segredo.

O `Azure Functions` é um ótimo exemplo disto, sempre que chamamos uma função, precisamos informar o código de acesso a mesma, e ele se encarrega do processo de autenticação e autorização.

```
https://sua-function.azurewebsites.net/api/endpoint?code=MINHA_CHAVE_DE_API
```

Em resumo, ao invés de fazer um processo de autenticação convencional, enviando usuário e senha para receber um Token e depois enviando este Token no cabeçalho da requisição, enviamos diretamente esta chave que dá acesso a API.

Deste modo, temos a ApiKey como uma substituta da autenticação via usuário e senha que geraria um Token.

### Segurança
Como você deve imaginar, esta informação é bem sensível e qualquer pessoa com sua ApiKey poderá fazer requisições para sua API, então é importante guardá-la com muito carinho.

## Criando a aplicação

Para este processo, não vamos precisar instalar nenhum pacote adicional, apenas gerar um novo projeto ASP.NET do tipo `webapi` que criará uma API vazia.

```
dotnet new webapi -o MeuApiKey
cd MeuApiKey
dotnet watch run
```

## Criando um atributo

Não existe muito mistério aqui, sabendo o conceito de ApiKey o que queremos é interromper uma requisição e verificar se ela existe na `Query` ou nos `Headers`.

Feito isto, precisamos validá-la, para garantir que ela é uma ApiKey válida. É o processo básico de autenticação e autorização.

A maneira mais fácil de realizar este procedimento é criar um atributo customizado, que no caso é apenas uma classe que herda de `Attribute`.

Para organizar melhor, crie uma pasta chamada `Attributes` na raiz da aplicação junto ao `ApiKeyAttribute.cs` dentro dela.

Nossa classe começa com as heranças e a utilização do `AttributeUsage` que limita o uso deste atributo a classes e métodos no caso.

```csharp
[AttributeUsage(validOn: AttributeTargets.Class | AttributeTargets.Method)]
public class ApiKeyAttribute : Attribute, IAsyncActionFilter
{
...
```

Desta forma, poderemos utilizar uma notação `[ApiKey]` tanto na classe (Controller) quanto em um método (Action).

### Definições

Para melhorar nosso código, vamos criar duas constantes, uma para o nome do parâmetro que vamos buscar e outro com a chave que vamos comparar.

```csharp
private const string ApiKeyName = "api_key";
private const string ApiKey = "balta_demo_IlTevUM/z0ey3NwCV/unWg==";
```

Aqui está o ponto chave deste artigo, o `ApiKey` que definimos acima é a chave de acesso a toda API. Quem utilizar esta chave, "está no comando".

Você pode armazenar esta chave no `AppSettings` por exemplo, assim pode versionar seu código tranquilamente e substituir o valor dela durante o deploy.

Caso tenha dificuldades em lidar com esta situação de deploy e troca de configurações entre ambientes, [leia este artigo](https://balta.io/blog/aspnet-deploy-github-actions-azure) que escrevi sobre GitHub Actions e Microsoft Azure.

De qualquer forma, a ideia aqui é ter uma chave (Ou várias) para saber se a requisição é valida. Posteriormente você pode melhorar isto, inclusive lendo as chaves do banco de dados por exemplo.

### Interceptando a requisição

Ao herdar da classe `Attribute` e  `IAsyncActionFilter` somos obrigados a implementar o método `OnActionExecutionAsync`, onde poderemos inspecionar a requisição atual.

Neste caso, vamos inspecionar o `context.HttpContext.Request` que possui tanto a propriedade `Headers` quanto `Query`, se referindo aos cabeçalhos e a URL da requisição respectivamente.

Logo, se queremos obter um valor da URL, utilizamos `context.HttpContext.Request.Query`, enquanto para obter um valor dos cabeçalhos utilizamos `context.HttpContext.Request.Headers`.

A única coisa que precisamos nos atentar é que podemos ter mais de um valor com o mesmo nome ou mesmo nenhum valor. Desta forma é recomendado utilizar a extensão `TryGetValue` para não ter exceções na execução do código.

```csharp
[AttributeUsage(validOn: AttributeTargets.Class | AttributeTargets.Method)]
public class ApiKeyAttribute : Attribute, IAsyncActionFilter
{
    private const string ApiKeyName = "api_key";
    private const string ApiKey = "balta_demo_IlTevUM/z0ey3NwCV/unWg==";

    public async Task OnActionExecutionAsync(
        ActionExecutingContext context,
        ActionExecutionDelegate next)
    {
        if (!context.HttpContext.Request.Query.TryGetValue(ApiKeyName, out var extractedApiKey))
        {
            // Não encontrou
        }

        await next();
    }
}
```

Por fim, podemos utilizar o método `next()` para dar continuidade a requisição quando desejarmos.

### ApiKey não encontrada

Caso nossa ApiKey não esteja presente na `Query` (Ou Headers, depende de como você escolheu utilizar), vamos atualizar o resultado do contexto atual e interromper a requisição.

```csharp
if (!context.HttpContext.Request.Query.TryGetValue(ApiKeyName, out var extractedApiKey))
{
    context.Result = new ContentResult()
    {
        StatusCode = 401,
        Content = "ApiKey não encontrada"
    };
    return;
}
```

Note que para este caso utilizamos o código `401 - Unauthorized`, pois não conter uma chave é um sinal que a requisição não está autenticada.

### ApiKey inválida

Nosso próximo passo é verificar se a chave enviada é válida, e para isto vamos utilizar o método `Equals` da `string`, responsável por sua comparação com outro valor do mesmo tipo.

```csharp
if (!ApiKey.Equals(extractedApiKey))
{
    context.Result = new ContentResult()
    {
        StatusCode = 403,
        Content = "Acesso não autorizado"
    };
    return;
}
```

Note que para este caso utilizamos o código `403 - Forbidden`, pois a chave existe mas é inválida. Você poderia utilizar o `401` aqui também, é mais uma questão de visão e preferências.

### ApiKey Attribute

A versão final do nosso atributo `[ApiKey]`, definido na classe `Attributes/ApiKeyAttribute.cs` fica assim.

```csharp
[AttributeUsage(validOn: AttributeTargets.Class | AttributeTargets.Method)]
public class ApiKeyAttribute : Attribute, IAsyncActionFilter
{
    private const string ApiKeyName = "api_key";
    private const string ApiKey = "balta_demo_IlTevUM/z0ey3NwCV/unWg==";

    public async Task OnActionExecutionAsync(
        ActionExecutingContext context,
        ActionExecutionDelegate next)
    {
        if (!context.HttpContext.Request.Query.TryGetValue(ApiKeyName, out var extractedApiKey))
        {
            context.Result = new ContentResult()
            {
                StatusCode = 401,
                Content = "ApiKey não encontrada"
            };
            return;
        }


        if (!ApiKey.Equals(extractedApiKey))
        {
            context.Result = new ContentResult()
            {
                StatusCode = 403,
                Content = "Acesso não autorizado"
            };
            return;
        }

        await next();
    }
}
```

## Adicionando autenticação e autorização

Nosso próximo passo é informar a aplicação que estamos utilizando autenticação e autorização. Normalmente a linha `app.UseAuthentication` já vem no código, mas caso não esteja presente, fica assim.

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...
    app.UseRouting();

    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints => { endpoints.MapControllers(); });
}
```

> **IMPORTANTE** a ordem de adição dos itens deve ser exatamente como acima. Se você inverter o UseAuthentication com o UseAuthorization por exemplo, não vai funcionar.

## Utilizando o atributo ApiKey

Com tudo pronto, vamos utilizar nosso atributo no `WeatherController.cs`, adicionando-o a qualquer método. Neste exemplo, criei um método novo, do tipo `GET` para facilitar os testes.

```csharp
[ApiController]
[Route("weather")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet("")]
    [ApiKey]
    public IActionResult Get()
    {
        return Ok(new {message = "Você tem acesso!"});
    }
}
```

### Testando a API

Agora vamos testar a API e se você utilizou um método `GET` como eu, basta chamar estes três endereços no seu browser.

```
https://localhost:5001/weather
https://localhost:5001/weather?api_key=12345
https://localhost:5001/weather?api_key=balta_demo_IlTevUM/z0ey3NwCV/unWg==
```

Na primeira requisição você deve receber um erro `401`, pois não informamos o ApiKey. Na segunda você deve receber um erro `403` pois o ApiKey é inválido, e por fim, devemos conseguir visualizar a mensagem "Você tem acesso!".

## Fontes
* [Secure ASP.NET Core Web API using API Key Authentication](http://codingsonata.com/secure-asp-net-core-web-api-using-api-key-authentication/)

<div role="main" id="blog-s1-dotnet-autenticacao-497d7348c272b594f055"></div>
<script type="text/javascript" src="https://d335luupugsy2.cloudfront.net/js/rdstation-forms/stable/rdstation-forms.min.js"></script>
<script type="text/javascript"> new RDStationForms('blog-s1-dotnet-autenticacao-497d7348c272b594f055', 'UA-48664517-12').createForm();</script>
