Neste artigo vamos aprender a fazer autenticação e autorização em APIs [ASP.NET](http://ASP.NET) com JWT e Bearer Authentication.


## Gerando Tokens com ASP.NET

Mas chega de filosofia! Vamos colocar a mão na massa e gerar Tokens JWT com ASP.NET, o que é uma tarefa bem simples.

> **IMPORTANTE:** Não vamos cobrir a parte de Entity Framework e acesso a dados aqui, isto está bem coberto nos cursos, vamos focar em gerar Tokens e autenticação/autorização no ASP.NET.
> 

O [ASP.NET](http://ASP.NET) tem um Middleware que cuida da parte de autenticação e que já tem suporte a JWT, então podemos usufruir deste item “in-the-box” que vai facilitar nossa vida.

### Criando a API

Nosso primeiro passo é bem simples e pode ser feito com qualquer template do .NET que suporte [ASP.NET](http://ASP.NET), isto é, ASP.NET MVC, ASP.NET Razor Pages, ASP.NET Minimal APis.

Para este exemplo em específico, escolhi as Minimal APIs por serem mais simples e assim podermos focar no que realmente interessa.

É importante notar que não cobriremos a parte de acesso a dados aqui, as informações serão mockadas a fim de simplificar o exemplo.

Acesso a dados é um dos pontos mais importantes de uma aplicação e devemos tratá-los com responsabilidade. Caso queira se aprofundar no assunto, recomendo seguir a trilha abaixo:

[Fundamentos do SQL Server](https://balta.io/cursos/fundamentos-sql-server)

[Acesso à dados com .NET, C#, Dapper e SQL Server](https://balta.io/cursos/acesso-dados-csharp-net-dapper-sql-server)

[Fundamentos do Entity Framework](https://balta.io/cursos/fundamentos-entity-framework)

[Aplicações Mult-Tenant com Entity Framework Core](https://balta.io/cursos/aplicacoes-mult-tenant-entity-framework-core)

Voltando a questão da criação da API, vamos utilizar o comando abaixo para criar o projeto, navegar para pasta do mesmo e instalar o único pacote que precisamos para trabalhar com JWT no [ASP.NET](http://ASP.NET) o `Microsoft.AspNetCore.Authentication.JwtBearer`.

```bash
dotnet new web -o JwtAspNetBlazor
cd JwtAspNetBlazor
dotnet add package Microsoft.AspNetCore.Authentication.JwtBearer
```

### Chave Privada

Lembra da explicação anterior, onde comentamos que apenas o servidor poderia descriptografar o Token devido uma chave privada que é utilizada tanto na geração quanto leitura do Token?

Chegou a hora de criar ela, e esta tarefa é relativamente simples, no caso, para fins didáticos vamos colocá-la em uma classe estática que ficará de fácil acesso para uso posteriormente.

```csharp
// Configuration.cs

namespace JwtAspNetBlazor;

public static class Configuration
{
    public static string PrivateKey { get; set; } = "5+IV)E2glD3xCH2rNTElZ_at9(TbG1N(E=pH)29*";
}
```

Caso queira aprender mais sobre classes estáticas, recomendamos nosso curso sobre Orientação a Objetos:

[Fundamentos da Orientação a Objetos](https://balta.io/cursos/fundamentos-orientacao-objetos)

Em adicional, esta chave está “hard coded” ou seja, está fixa no código, o que não é uma boa prática, podemos (E devemos) fazer uso das configurações da aplicação para armazenar estas informações.

Também não devemos “comitar” este código de forma alguma, já que mais pessoas que estejam trabalhando no projeto teriam acesso a mesma chave.

Por fim, esta chave ***NUNCA*** deve ser a mesma de produção. Em produção a chave deve ser outra e ninguém deve ter acesso a ela.

Para saber mais sobre essa parte de configuração e ambientes, recomendamos nosso curso de Fundamentos do [ASP.NET](http://ASP.NET) 6.

[Fundamentos do ASP.NET 6](https://balta.io/cursos/fundamentos-aspnet)

Para saber mais sobre a publicação e gerenciamento de configurações de diferentes ambientes como DEV, QA e PROD, recomendamos o nosso curso de Fundamentos do DevOps:

[Fundamentos do Azure, Git, GitHub e DevOps](https://balta.io/cursos/fundamentos-azure-git-github-devops)

Apenas resumindo que o código anterior é apenas para fins didáticos, as boas práticas mostradas nos cursos acima devem ser sempre seguidas.

### Criando um usuário

Movendo adiante, vamos criar um usuário, um objeto que vai conter as informações do usuário como E-mail, Id e perfis que ele possui.

Este objeto pode ser simples (Anêmico) ou complexo, novamente, para geração do Token não importa, só vamos utilizá-lo para transferência de dados.

```csharp
// Models/User.cs

namespace JwtAspNetBlazor.Models;

public record User(int Id, string Email, string Password, string[] Roles);
```

Sinta-se à vontade para modificar este modelo como desejar, adicionando ou removendo propriedades, comportamentos e etc.

### Como gerar um Token?

A real tarefa começa aqui, onde temos que gerar um Token e como vimos antes o mesmo será segmentado em três partes:

- Cabeçalho
- Payload
- Assinatura

A boa notícia é que tanto o cabeçalho quanto a assinatura podem ser gerados “automagicamente” pelo pacote que adicionamos anteriormente, sobrando apenas o PayLoad para nós.

O processo na verdade é bem simples, e todo realizado por uma classe chamada `JwtSecurityTokenHandler`.

Esta classe possui os métodos `CreateToken` que literalmente cria um Token e `WriteToken` que encripta e gera a string que precisamos.

```csharp
// Cria uma instância do JwtSecurityTokenHandler
var handler = new JwtSecurityTokenHandler();

...

// Gera um Token
var token = handler.CreateToken(tokenDescriptor);
// Gera uma string do Token
var strToken = handler.WriteToken(token);
```

No meio disso tudo, precisamos apenas gerar o PayLoad e informar a Chave Privada que ele irá utilizar para assinar o Token.

### Assinando o Token

Nosso próximo passo é assinar este Token utilizando a Chave Privada que definimos anteriormente e que só o servidor terá.

Sem esta chave, como vimos anteriormente, é impossível alterar o conteúdo do Token, tornando-o inválido.

Este processo é relativamente simples e pode ser executado pelo `SigningCredentials`, onde precisamos informar uma chave simétrica (`SymmetricSecurityKey`).

O objeto SigningCredentials espera dois parâmetros, uma chave simétrica e o algoritmo que usará para encriptação.

```csharp
new SigningCredentials(CHAVE, ALGORITMO)
```

Como vimos anteriormente, o algoritmo sugerido e amplamente utilizado até a escrita deste artigo é o Sha256, e temos seu valor descrito em uma constante chamada `SecurityAlgorithms.HmacSha256Signature`.

```csharp
new SigningCredentials(CHAVE, SecurityAlgorithms.HmacSha256Signature)
```

Estamos quase lá, falta apenas a chave simétrica, que será gerada utilizando o `SymmetricSecurityKey`. O problema é que este objeto espera um array de bytes como entrada ao invés de uma string.

```csharp
// Converte nossa chave (string) para um array de bytes
var key = Encoding.ASCII.GetBytes(Configuration.PrivateKey);

new SigningCredentials(
		new SymmetricSecurityKey(key), 👈
		SecurityAlgorithms.HmacSha256Signature)
```

Pronto, desta forma temos nossas credenciais para gerar e ler a assinatura dos Tokens gerados. Até o momento temos o seguinte código (ainda não é o final).

```csharp
// Cria uma instância do JwtSecurityTokenHandler
var handler = new JwtSecurityTokenHandler();

var key = Encoding.ASCII.GetBytes(Configuration.PrivateKey);

var credentials = new SigningCredentials(
		new SymmetricSecurityKey(key),
		SecurityAlgorithms.HmacSha256Signature)

...

// Gera um Token
var token = handler.CreateToken(tokenDescriptor);
// Gera uma string do Token
var strToken = handler.WriteToken(token);
```

### Token Descriptor

Como vimos anteriormente, o `TokenHandler` tem um método chamado `CreateToken` que espera um `tokenDescriptor`, que nada mais é um objeto que reune o cabeçalho, payload e assinatura do token.

```csharp
handler.CreateToken(tokenDescriptor);
```

Nós já temos as informações de credenciais que conta com o algoritmo então já está meio caminho andado.

Podemos então criar uma instância do `SecurityTokenDescriptor` e informar as credenciais.

```csharp
var tokenDescriptor = new SecurityTokenDescriptor
{
    SigningCredentials = credentials,
};
```

Desta forma, já temos um Token que pode ser gerado, mas calma pois ainda faltam dois itens que são muito importantes.

```csharp
var handler = new JwtSecurityTokenHandler();

var key = Encoding.ASCII.GetBytes(Configuration.PrivateKey);

var credentials = new SigningCredentials(
		new SymmetricSecurityKey(key),
		SecurityAlgorithms.HmacSha256Signature)

var tokenDescriptor = new SecurityTokenDescriptor
{
    SigningCredentials = credentials,
};

var token = handler.CreateToken(tokenDescriptor);
var strToken = handler.WriteToken(token);
```

### Expirando o Token

Lembra que falamos sobre o tempo de vida de um Token? Pois é, chegou o momento que podemos definir este tempo de expiração.

Ele é feito através da propriedade `Expires` do `SecurityTokenDescriptor`, como demonstrado abaixo.

```csharp
var tokenDescriptor = new SecurityTokenDescriptor
{
    Expires = DateTime.UtcNow.AddHours(2),
    SigningCredentials = credentials,
};
```

É importante notar que o uso das datas no formato UTC é altamente recomendado aqui, principalmente se o seus servidores trabalham em regiões diferentes.

Para saber mais sobre DateTime, formatos, globalização e UTC, recomendamos nosso curso de Fundamentos do C#.

[Fundamentos do C#](https://balta.io/cursos/fundamentos-csharp)

Em adicional, fique à vontade para alterar o tempo de vida do Token, no exemplo anterior utilizamos duas horas, mas como discutimos previamente neste documento, fica a seu critério definir quando este Token deve expirar.

## Gerando o Payload

Nosso passo final é gerar o PayLoad, a carga, o conteúdo do nosso Token e isto pode ser feito de diferentes formas.

Minha recomendação é fazer uso dos `ClaimsIdentity` do [ASP.NET](http://ASP.NET) por conta da integração que temos com eles.

### Entendendo os Claims

Deixa eu te explicar melhor, no [ASP.NET](http://ASP.NET) temos um conceito chamado de **Claim** ou traduzindo, uma “Afirmação”.

As Claims são objetos do tipo “Chave/Valor” que definem uma afirmação, como por exemplo, dizer que o nome do usuário é “André Baltieri”.

```csharp
new Claim("Nome", "André Baltieri");
```

Criar um `Claim` é uma tarefa relativamente fácil, porém tem uma sacada que vai te ajudar muito a capturar estas informações depois.

Embora você possa colocar qualquer nome na “Chave”, o [ASP.NET](http://ASP.NET) tem alguns nomes que são recomendados, e que se utilizados, já fazem um vínculo automático com o `HttpContext` durante a requisição.

Isto quer dizer que se ao invés de você utilizar uma string “Nome” como chave do Claim e utilizar a constante recomendada pelo [ASP.NET](http://ASP.NET) que é `ClaimTypes.Name`, o ASP.NET será capaz de ler automaticamente este nome de usuário durante as requisições.

```csharp
new Claim("Nome", "André Baltieri"); ⚠️

public ActionResult Get()
{
		var user = User.Identity.Name; // NULL ❌
}
```

No caso acima, o [ASP.NET](http://ASP.NET) não consegue capturar o nome do usuário pois uma chave conhecida ou padrão não foi utilizada na geração do `Claim`.

```csharp
new Claim(ClaimTypes.Name, "André Baltieri"); ✅

public ActionResult Get()
{
		var user = User.Identity.Name; // André Baltieri ✅
}
```

No exemplo acima, como utilizamos o `ClaimTypes.Name` como chave, o [ASP.NET](http://ASP.NET) consegue automaticamente reconhecer a chave e buscar o nome do usuário quando utilizamos o `User.Identity.Name`.

O mais interessante é que temos diversos ClaimTypes.Names que funcionam desta forma, incluindo um dos que mais utilizamos que é o Role.

```csharp
new Claim(ClaimTypes.Name, "André Baltieri"); ✅
new Claim(ClaimTypes.Role, "premium"); ✅

public ActionResult Get()
{
		var user = User.Identity.Name; // André Baltieri ✅
		// Como definimos o role como Premium
		// Agora podemos checar se o usuário pertence a este role
		if(User.IsInRole("premium"))
		...
}
```

Desta forma, tudo o que precisamos fornecer para o `TokenDescriptor` é um `ClaimsIdentity`, que nada mais é do que um conjunto de Claims, que como vimos anteriormente é apenas uma Chave/Valor.

### Gerando um ClaimsIdentity

Para facilitar, vamos criar um método que recebe um `User` e gera os ***Claims*** baseados em seu nome e perfis.

```csharp
private static ClaimsIdentity GenerateClaims(User user)
{
    var ci = new ClaimsIdentity();
    ci.AddClaim(new Claim(ClaimTypes.Name, user.Email));
    foreach (var role in user.Roles)
        ci.AddClaim(new Claim(ClaimTypes.Role, role));

    return ci;
}
```

No caso, estamos utilizando o E-mail como **Name**, mas sinta-se livre para utilizar qual propriedade desejar.

Em adicional, como temos vários ***Roles*** no `User`, precisamos iterar entre eles e popular o `ClaimsIdentity` com seus valores.

É importante notar que não há problema em ter múltiplas chaves do tipo `ClaimTypes.Role`, diferente do Name, Id ou Email.

## Token Service

Para finalizar com chave de ouro, vamos colocar este código em um serviço no qual podemos instanciar e fazer uma chamada para gerar Tokens sempre que necessário.

No caso, recomendo criar esta classe na seguinte estrutura: `Services/TokenService.cs`

```csharp
using System.IdentityModel.Tokens.Jwt;
using System.Security.Claims;
using System.Text;
using JwtAspNetBlazor.Models;
using Microsoft.IdentityModel.Tokens;

namespace JwtAspNetBlazor.Services;

public class TokenService
{
    public string Generate(User user)
    {
        var handler = new JwtSecurityTokenHandler();
        var key = Encoding.ASCII.GetBytes(Configuration.PrivateKey);
				var credentials = new SigningCredentials(
                new SymmetricSecurityKey(key),
                SecurityAlgorithms.HmacSha256Signature);

        var tokenDescriptor = new SecurityTokenDescriptor
        {
            Subject = GenerateClaims(user),
            Expires = DateTime.UtcNow.AddHours(2),
            SigningCredentials = credentials,
        };
        var token = handler.CreateToken(tokenDescriptor);
        return handler.WriteToken(token);
    }

    private static ClaimsIdentity GenerateClaims(User user)
    {
        var ci = new ClaimsIdentity();
        ci.AddClaim(new Claim(ClaimTypes.Name, user.Email));
        foreach (var role in user.Roles)
            ci.AddClaim(new Claim(ClaimTypes.Role, role));

        return ci;
    }
}
```

Pronto, agora temos um serviço que gera Tokens baseado em um objeto `User` através do método `Create` e retorna o mesmo como uma String, pronto para enviar para tela.

## Registrando o Token Service

Uma boa prática é registrar o serviço no **Service Locator** e assim poder fazer uso da injeção de dependência em qualquer lugar da nossa API.

Este é um processo relativamente simples e podemos utilizar o modelo Transiente já que não precisamos de um tempo de vida longo para nosso objeto.

```csharp
// Program.cs
var builder = WebApplication.CreateBuilder(args);

builder.Services.AddTransient<TokenService>();
```

Caso queira se aprofundar mais na parte de Injeção de Dependências, recomendamos nosso curso Dominando Injeção de Dependência.

[Dominando Injeção de Dependência](https://balta.io/cursos/dominando-injecao-de-dependencia)

## Testando a API

Para evitar frustrações futuras, nada melhor que testar a API e ver se o Token está sendo gerado corretamente.

Nosso método vai ser extremamente simples, vamos receber um User do corpo da requisição e uma instância do `TokenService` que o ***Service Locator*** do [ASP.NET](http://ASP.NET) se encarregará de encontrar.

Com ambos em mãos, podemos simplesmente retornar o resultado do `TokenService.Create` já que ele retorna uma string.

```csharp
app.MapPost("/login", (User user, TokenService tokenService) 
    => tokenService.Generate(user));
```

Lembrando que este é apenas um exemplo, não estamos tratando erros, nem normalizando retorno das APIs ou algo do tipo, é importante sempre seguir os padrões e recomendações para criação de APIs como mostramos no curso Fundamentos do ASP.NET.

O resultado final da nossa API até o momento é este código abaixo. Até que ficou enxuto e organizado.

```csharp
using JwtAspNetBlazor.Models;
using JwtAspNetBlazor.Services;

var builder = WebApplication.CreateBuilder(args);
builder.Services.AddTransient<TokenService>();

var app = builder.Build();

app.MapPost("/login", (User user, TokenService tokenService) 
    => tokenService.Generate(user));

app.Run();
```

### Testando via Postman

Você pode testar a API chamando a URL [http://localhost:XXXX/login](http://localhost:5280/login) via CURL, mas recomendo fazer o uso do Postman por ser mais completo.

Caso ainda não tenha o Postman instalado, você pode fazer o Download e instalação dele diretamente do seu site oficial: [https://www.postman.com](https://www.postman.com/)

Então vamos lá, execute sua aplicação com o comando `dotnet run` e abra o Postman para fazermos uma requisição do tipo POST.

![Screenshot 2023-05-19 at 10.57.04.png](Screenshot_2023-05-19_at_10.57.04.png)

Note que a porta da URL será diferente no seu caso, e atente-se ao tipo da requisição que no caso é **POST**.

Para o corpo da requisição (**BODY**) selecionei **RAW** e nas opções selecionei **JSON**, adicionando o seguinte conteúdo.

```csharp
{
    "email": "teste@balta.io",
    "roles": ["admin", "premium"]
}
```

Como resultado, tive o seguinte Token como retorno.

```csharp
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1bmlxdWVfbmFtZSI6InRlc3RlQGJhbHRhLmlvIiwicm9sZSI6WyJhZG1pbiIsInByZW1pdW0iXSwibmJmIjoxNjg0NTAzOTQ3LCJleHAiOjE2ODQ1MTExNDcsImlhdCI6MTY4NDUwMzk0N30.qaJDssU17PuPWvfwj6NpbUiGuf3O-JEfa41rhC61jAg
```

### Inspecionando o Token

Com o Token em mãos, nosso último passo nos testes é inspecionar o Token no [https://jwt.io](https://jwt.io/), simplesmente colando o valor que recebemos anteriormente no site.

![Screenshot 2023-05-19 at 11.00.23.png](Screenshot_2023-05-19_at_11.00.23.png)

Se tudo deu certo, você verá as seguintes informações, contendo o “unique_name”, “roles” e “iat” conforme comentamos no começo deste documento.

```csharp
{
  "alg": "HS256",
  "typ": "JWT"
}

{
  "unique_name": "teste@balta.io",
  "role": [
    "admin",
    "premium"
  ],
  "nbf": 1684503947,
  "exp": 1684511147,
  "iat": 1684503947
}
```

## Implementando autenticação na API

Com a API iniciada e o Token sendo gerado, chegou a hora de restringir o acesso a determinadas rotas da API utilizando autenticação.

Isto pode ser feito de forma simples e fácil no ASP.NET, através de Middlewares, no caso o `AddAuthentication` e `AddAuthorization`.

Estes dois Middlewares permitem dizer quem um usuário é e o que ele pode fazer na API. Neste ponto, podemos notar que gerar Tokens e proteger APIs são coisas que embora estejam ligadas, são distintas.

Poderíamos simplesmente parar por aqui e ter uma API (Microsserviço) apenas para gerar tokens. Até que não seria uma má ideia.

De qualquer forma, vamos começar pela autenticação, pois sem saber quem é o usuário, não podemos dizer o que ele pode fazer.

### Adicionando autenticação

O processo de adicionar um Middleware ao pipeline de execução do [ASP.NET](http://ASP.NET) normalmente se divide em duas partes, uma adicionando o Middleware (Add) e outra utilizando ele (Use).

```csharp
// Adiciona o Middleware de autenticação
builder.Services.AddAuthentication();

// Usa o Middleware de autenticação
app.UseAuthentication();
```

Outro ponto importante a se notar é “quando” devemos chamar este Middleware. O Middleware é um “passo” a mais na execução de uma requisição.

Basicamente estamos falando para o [ASP.NET](http://ASP.NET) que agora também queremos verificar o cabeçalho da requisição para buscar um Token ou algo do tipo.

Então esta ordem importa! Supondo que você chame o Middleware de autenticação depois de ter registrado as rotas. Neste caso, seria inefetivo, pois as rotas já foram registradas quando não havia nenhum Middleware de autenticação.

Normalmente utilizo as chamadas de autenticação e autorização o mais breve possível, ou seja, o mais próximo possível da instância do `builder` e do `app`.

```csharp
...
var builder = WebApplication.CreateBuilder(args);
builder.Services.AddAuthentication();
...

var app = builder.Build();
app.UseAuthentication();
...
app.MapGet("/login", () => "Hello World");
... 
```

### Adicionando autorização

Com o Middleware de autenticação adicionado, vamos para a autorização, que vai seguir o mesmo exemplo.

Novamente é importante salientar a ordem dos Middlewares, lembre-se sempre que primeiro autenticamos e depois autorizamos, ou seja, precisamos saber quem é para depois dizer o que pode fazer.

Desta forma, o Middleware de autorização deve vir sempre após o Middleware de autenticação, seguindo conforme descrito acima.

```csharp
...
var builder = WebApplication.CreateBuilder(args);
builder.Services.AddAuthentication();
builder.Services.AddAuthorization(); 👈
...

var app = builder.Build();
app.UseAuthentication();
app.UseAuthorization(); 👈
...
app.MapGet("/login", () => "Hello World");
... 
```

Seguindo o mesmo exemplo temos o `AddAuthorization` e `UseAuthorization`, que respectivamente fazem a adição e uso do Middleware de autorização.

Caso não haja necessidade, você pode dispensar o Middleware de autorização, por exemplo, se não for trabalhar com perfis, claims, policies ou qualquer outro nível de acesso, você precisa apenas da autenticação.

É importante lembrar também que os Middlewares são passos a mais na requisição, ou seja, quanto mais passos, mais demorada ela se torna, mas neste caso não precisa se preocupar tanto pois eles são bem otimizados.

### Adicionando suporte ao JWT

Com autenticação e autorização prontas para serem utilizadas, vamos agora implementar seu uso, afinal, até agora só dissemos que queremos ter autenticação e autorização, mas não dissemos ao [ASP.NET](http://ASP.NET) como fazer isto.

### Bearer Authentication

Nosso primeiro passo então, é dizer ao [ASP.NET](http://ASP.NET) que a nossa autenticação será feita utilizando um Token no formato JWT que vem no cabeçalho da requisição.

Este tipo de autenticação, onde informamos um Token é chamado de Bearer Authentication, por este motivo ouvimos muito falar em “Bearer JWT” ou “JWT Bearer”, pois enquanto Bearer define como o Token é trafegado, JWT define o tipo do formato do Token.

Apenas para intuito de conhecimento, o Bearer é um padrão amplamente utilizado, tanto no Backend quanto no Frontend, e seu principal destaque é a facilidade.

Tudo o que precisamos fazer para utilizar este padrão é definir um item no cabeçalho da requisição, chamado de `Authorization` e que contenha o valor `Bearer SEU_TOKEN`.

Como temos outros tipos de autorização, como `Basic`, onde apenas encriptamos o usuário e senha em `Base64`, é necessário especificar a palavra `Bearer` antes do Token.

Não esqueça que depois da palavra `Bearer` deve haver um espaço em branco e só então seu Token, como mostrado no exemplo abaixo.

```csharp
curl 
	--location 'http://localhost:5049/hello' \
	--header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...'
```

### Definindo o esquema de autenticação

Bom, já que sabemos como é o padrão **Bearer**, ficou fácil, é só interceptar a requisição, buscar pelo **Header**, encontrar o atributo **Authorization** e recuperar o valor que venha depois do espaço em branco após a palavra **Bearer**.

Na verdade é bem mais simples do que isto, pois como este é um padrão comum, o [ASP.NET](http://ASP.NET) já consegue fazer tudo isto “automagicamente” para nós.

Tudo o que precisamos fazer é informar a ele qual esquema de autenticação estamos utilizando (Lembre-se que existem vários esquemas, então é necessário especificar o Bearer/JWT).

O método `AddAuthentication` pode receber algumas opções, dentre elas o `DefaultAuthenticateScheme` que queremos definir.

```csharp
builder.Services
    .AddAuthentication(x =>
    {
        x.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
    })
```

Neste caso, utilizamos a constante `JwtBearerDefaults.AuthenticationScheme` para definir que o esquema de autenticação que queremos utilizar é o **Bearer** e o Token virá no formato JWT.

### Challenge

Como vimos anteriormente, é possível que nosso Token seja gerado de várias formas inclusive em sites externos, como Facebook, Twitter, Google e Microsoft.

Este processo é chamado de **Challenge** ou desafiar, onde confrontamos um servidor para que ele faça uma autenticação e nos retorne o Token.

Inclusive podemos ser este servidor e definir que após validar um Token redirecionamos o usuário para outro endereço.

Desta forma, nosso desafio aqui é dizer para o [ASP.NET](http://ASP.NET) que estamos utilizando uma autenticação interna, ou seja, ela é gerada neste servidor e vale para este servidor apenas.

Pode este motivo, precisamos de uma configuração adicional, chamada `DefaultChallengeScheme` que definirá este processo.

```csharp
builder.Services
    .AddAuthentication(x =>
    {
        x.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
        x.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
    })
```

Desta forma temos todas as definições que precisamos, dizendo ao [ASP.NET](http://ASP.NET) que nosso modelo de autenticação é o Bearer/JWT e que nossa autenticação é interna.

### Lendo o Token

Para finalizar, as informações que precisamos, como nome do usuário e perfis que ele possui, estão dentro do Token.

Porém, o Token está encriptado e precisamos fazer o processo reverso feito no TokenService, para extrair as informações do Token e popular o `ClaimsPrincipal` (Usuário logado).

Já sabe né? Novamente o [ASP.NET](http://ASP.NET) traz tudo pronto para nós, basta definir algumas configurações e dizer qual chave utilizamos para encriptar o Token.

```csharp
.AddJwtBearer(x =>
{
		// Obriga uso do HTTPs
    x.RequireHttpsMetadata = false;

		// Salva os dados de login no AuthenticationProperties
    x.SaveToken = true;

		// Configurações para leitura do Token
    x.TokenValidationParameters = new TokenValidationParameters
    {
				// Chave que usamos para gerar o Token
        IssuerSigningKey = new SymmetricSecurityKey(Encoding.ASCII.GetBytes(Configuration.PrivateKey)),
				// Validações externas
        ValidateIssuer = false,
        ValidateAudience = false
    };
});
```

Como resultado final, temos o seguinte código:

```csharp
builder.Services
    .AddAuthentication(x =>
    {
        x.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
        x.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
    })
    .AddJwtBearer(x =>
    {
        x.RequireHttpsMetadata = false;
        x.SaveToken = true;
        x.TokenValidationParameters = new TokenValidationParameters
        {
            IssuerSigningKey = new SymmetricSecurityKey(Encoding.ASCII.GetBytes(Configuration.PrivateKey)),
            ValidateIssuer = false,
            ValidateAudience = false
        };
    });
builder.Services.AddAuthorization();

// ...

app.UseAuthentication();
app.UseAuthorization();
```

## Restringindo rotas

Com as configurações devidamente feitas, vamos então restringir nossas rotas para acessos não autenticados.

Para isto, vamos utilizar o `RequireAuthorization`, ou `AuthorizeAttrbute`, dependendo do tipo de projeto que esteja trabalhando.

```csharp
// Exemplo em Minimal APIs
app.MapGet("/hello", () => "Hello World")
    .RequireAuthorization(); 👈
```

Como podemos ver acima, no cenário em que estamos trabalhando com Minimal APIs, basta adicionar o `RequireAuthorization` no fim da chamada.

```csharp
// Exemplo com MVC
[Authorize]
public Task<IActionResult> Get()
{
...
}
```

Já no MVC ou Razor Pages, basta adicionar o atributo `Authorize` no método e pronto, temos uma restrição na rota.

É importante notar que o embora o atributo faça referência a **autorização**, caso não seja especificado nenhum perfil, afirmação ou política, o mesmo serve apenas como **autenticação**.

Na prática, isto significa que receberemos o erro ***401 - Unauthorized*** quando tentarmos acessar algum endpoint com esta restrição.

Caso algum perfil, afirmação ou política seja explicitado e o Token não atenda o requisito, o erro recebido será o ***403 - Forbidden***.

## Obtendo o usuário logado

Bom, as rotas estão seguras, e vamos supor que recebemos uma requisição válida, com um Token válido, como descobrimos as informações de um usuário? Como obtemos valores do PayLoad do Token?

Este cenário é comum em diversos sistemas, imagina que estamos alterando informações de um usuário, onde ele só pode ver e alterar suas próprias informações.

Sabemos que ao seguir as recomendações para padronização da nossa API, recebemos o ID do usuário na rota, e então fazemos a alteração.

Mas e se o usuário informar o ID de outro usuário? E se por algum motivo a URL que ele recebeu tem um valor diferente do seu ID?

A maneira mais segura de fazer qualquer alteração que deve afetar apenas o próprio usuário, é utilizar as informações do Token.

Desta forma, nosso método que altera informações de um usuário sempre irá alterar o próprio usuário logado, não precisa (Nem adianta) passar o ID.

Para este exemplo, vamos tomar como base o seguinte Claim, atribuído ao `ClaimTypes.Name`, que facilitará nosso código.

```csharp
new Claim(ClaimTypes.Name, "teste@balta.io");
```

Lembra que comentamos anteriormente que ao usar os `ClaimTypes` temos algumas vantagens especiais? Pois é, uma delas é que o `HttpContext`, um objeto especial no [ASP.NET](http://ASP.NET) que contém dados da requisição é populado automaticamente.

Isto significa que algumas informações do PayLoad serão automaticamente carregadas para um objeto chamado `User`, dentro do `HttpContext`.

Como exemplo, temos o `User.Identity.Name` e `User.IsInRole`, dois métodos muito comuns e presentes no objeto `User` do `HttpContext`.

```csharp
User.Identity.Name; // Nome do usuário (Que pode ser o ID ou E-mail)
User.IsInRole("ROLE"); // Verifica se o usuário pertence a um role
```

### Acessando o HttpContext

No caso dos projetos MVC e Razor Pages, o HttpContext é injetado automaticamente nos Controllers e Pages, tornando seu acesso bem simples.

```csharp
[Authorize]
public ActionResult Get()
{
	var userName = User.Identity.Name ?? string.Empty;
}
```

A única coisa que precisamos nos atentar é que o usuário pode ser nulo, bem como o `Identity.Name`, então precisamos da coalescência nula para compensar este fator.

Em adicional, é bom utilizar este recurso apenas em métodos cujo atributo `Authorize` esteja presente, já que não faz sentido o método ser chamado se o usuário não for autenticado.

No caso das Minimal APIs, podemos injetar o `HttpContext` nos endpoints e a partir dele fazer a chamada para o objeto `User` que contém as informações que precisamos.

```csharp
app.MapGet("/hello", (HttpContext context) 
		=> Results.Ok(context.User.Identity?.Name ?? string.Empty))
    .RequireAuthorization();
```

A mesma recomendação de chamada anterior se repete aqui, notando que é importante o uso do `RequireAuthrorization` em métodos que farão uso do `User.Identity.Name`.

# Autorizando as rotas da API

Até o momento temos uma API que consegue gerar Tokens e verificar se um Token válido foi recebido no cabeçalho, bem como ler informações do mesmo, como nome do usuário.

Embora este seja um importante passo para a API, ele não reflete as necessidades como um todo, pois muitas vezes temos que classificar os usuários.

Este ato de classificar é meramente rotular um usuário, dizer que de alguma forma, ele tem um determinado “poder” dentro da API.

Por exemplo, podemos dizer que o usuário batman@balta.io é o administrador e pode criar novos usuários, enquanto o usuário robin@balta.io pode apenas visualizar informações de um usuário.

Ainda podemos ter outros usuários como alfred@balta.io que além dos poderes do robin@balta.io ainda tem pode acessar a parte financeira da empresa.

## Entendendo Roles, Claims e Policies

Isto nos trás para o conceito de Roles, Claims e Policies, onde Role é um Perfil, uma simples String que podemos usar para qualificar um usuário, um Claim é uma Afirmação, onde dizemos por exemplo que o usuário X possui um perfil Y.

O que realmente nos chama a atenção no [ASP.NET](http://ASP.NET) são as Policies, ou políticas, que são literalmente trechos de código, que lidam com Roles e até acesso à dados.

Podemos criar uma política que exige que um usuário contenha um ou mais roles ou até mesmo que o usuário possua uma determinada informação como a data de nascimento preenchida.

É possível também acessar o banco de dados ou verificar um serviço externo durante a execução de uma política, mas muito cuidado com isto, pois as políticas são executadas sempre que uma rota com `RequireAuthorization` ou `AuthorizeAttribute` são chamadas.

No mais, opte sempre pelo simples e básico no começo, escale apenas se precisar, assim garantimos que a API roda o mais performático possível.

## Criando Políticas

Criar uma Policy é um trabalho relativamente simples, bastando chamar o método AddPolicy, informando o nome e validações que esta política aplica.

```csharp
AddPolicy("NOME_DA_POLICY", x => x.RequireRole("ROLE_OBRIGATORIO"));
```

Além disso, podemos criar várias políticas, para diferentes cenários e posteriormente identificar quais queremos utilizar.

```csharp
AddPolicy("Administrador", x => x.RequireRole("admin"));
AddPolicy("Funcionario", x => x.RequireRole("employee"));
```

## Registrando as políticas

Assim como fizemos uso do `AddAuthentication` para registrar o tipo de autenticação como JWT/Bearer e informar outras opções de autenticação, faremos uso do `AddAuthorization` novamente, informando as políticas como opção.

```csharp
builder.Services.AddAuthorization(options =>
{
    options.AddPolicy("Admin", policy => policy.RequireRole("manager"));
    options.AddPolicy("Employee", policy => policy.RequireRole("employee"));
});
```

Neste caso, criamos duas políticas, uma chamada **Admin** e outra chamada **Employee**, para usuários administradores e funcionários, respectivamente.

Da mesma forma que a política **Admin** requer o perfil **manager**, a política **Employee** requer o perfil **employee**.

## Utilizando as políticas

Para fechar a criação e uso de políticas, vamos aplicar suas restrições nas rotas da API, ainda utilizando o `RequireAuthorization` como fizemos anteriormente, porém, desta vez informando o nome da política.

```csharp
app.MapGet("/employee", (ClaimsPrincipal user) =>
{
    return Results.Ok(new { message = $"Authenticated as {user.Identity?.Name}" });
}).RequireAuthorization("Employee");

app.MapGet("/manager", (ClaimsPrincipal user) =>
{
    return Results.Ok(new { message = $"Authenticated as {user.Identity?.Name}" });
}).RequireAuthorization("Admin");
```

Note que nos Minimal APIs podemos encurtar o caminho até o usuário logado, obtendo o `ClaimsPrincipal` ao invés do `HttpContext`.

# Testando a API

Com tudo pronto, vamos testar a API, e para facilitar, deixei uma coleção com os endpoints já configurados no GitHub deste projeto.

[seguranca-em-apis-aspnet-com-jwt-e-bearer-authentication/endpoints.postman_collection at main · balta-io/seguranca-em-apis-aspnet-com-jwt-e-bearer-authentication](https://github.com/balta-io/seguranca-em-apis-aspnet-com-jwt-e-bearer-authentication/blob/main/endpoints.postman_collection)

Basta rodar sua API com o comando `dotnet run` e executar os endpoints conforme mostramos abaixo.

## Importando a coleção

O primeiro passo é importar a coleção no Postman, para isto vá em File > Import e selecione o arquivo `endpoints.postman_collection` baixado anteriormente.

![Untitled](Untitled.png)

Como resultado, você terá a coleção ***“Segurança em APIs [ASP.NET](http://ASP.NET) com JWT e Bearer Authentication”*** adicionada ao menu lateral.

![Untitled](Untitled%201.png)

Basta clicar em algum endpoint e fazer a requisição ou mesmo alterá-lo. Note que neste exemplo, meu App criou outra porta, a 5207, então talvez o seu esteja rodando em uma porta diferente também, basta alterar a URL.

## Testando o login

Vamos começar pelo principal, e como não temos um sistema de senhas aqui, a composição dele fica como mostrado abaixo, basta clicar no endpoint e executar a requisição.

![Untitled](Untitled%202.png)

Como resultado, temos um Token sendo gerado, salve este Token em algum lugar pois ele será necessário nas próximas requisições.

## 401 - Unauthorized

Ao tentar fazer uma requisição ao endpoint de funcionários, que está protegido, nos deparamos primeiramente com um erro 401 - Unauthorized.

Este erro indica que não estamos ***AUTENTICADOS***, embora a mensagem diga “Unauthorized”.

![Untitled](Untitled%203.png)

Para informar o Token e realizar a autenticação, podemos mover para a aba Authorization, selecionar o tipo Bearer Token e informar o Token na caixa como mostrado abaixo.

![Untitled](Untitled%204.png)

## 403 - Forbidden

Ao informar o Token, notamos um retorno diferente, com a mensagem “Forbidden” que remete ao acesso negado.

Esta sim é a mensagem referente sobre a autorização, afinal, temos um Token, estamos autenticados, mas não temos o perfil Employee, necessário para acessar a rota.

![Untitled](Untitled%205.png)

Para poder acessar esta rota, precisamos de um novo Token, com o perfil “employee”, e já sabemos como obtê-lo.

![Untitled](Untitled%206.png)

Com o Token em mãos, tudo o que precisamos fazer é atualizar o cabeçalho da nossa requisição para o endpoint de Employee e pronto!

![Untitled](Untitled%207.png)

Agora temos acesso ao conteúdo do endpoint, com retorno 200 - OK, já que nosso Token possui o perfil Employee, obrigatório segundo a política do endpoint.

# Conclusão

Neste artigo, aprendemos em detalhes como implementar um modelo de autenticação e autorização completo no [ASP.NET](http://ASP.NET) utilizando JWT e Bearer Authentication.

# Código Fonte

[GitHub - balta-io/seguranca-em-apis-aspnet-com-jwt-e-bearer-authentication: Repositório do eBook Segurança em APIs ASP.NET com JWT e Bearer Authentication](https://github.com/balta-io/seguranca-em-apis-aspnet-com-jwt-e-bearer-authentication)