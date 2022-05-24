# Sumário
 
 1. [Introdução](#introducao)
 2. [[Bind] - Obtendo propriedades específicas](#bind-obtendo-propriedades-especificas)
 3. [[FromQuery] - Obtendo valores da cadeia de caracteres de consulta](#fromquery-obtendo-valores-da-cadeia-de-caracteres-de-consulta)
 4. [[FromRoute] - Obtendo valores de dados de rota](#from-route-obtendo-valores-de-dados-de-rota)
 5. [[FromForm] - Obtendo valores de campos de formularios postado](#fromform-obtendo-valores-de-campos-de-formularios-postados)
 6. [[FromBody] - Obtendo valores do corpo da solicitação](#frombody-obtendo-valores-do-corpo-da-solicitacao)
 7. [Continue lendo](#continue-lendo)
 8. [Referências](#ref)


<div id='introducao'></div> 

# Introdução

Nossas aplicações web (sejam elas MVC, Razor Pages, WebAPI e etc) transportam dados através do protocolo HTTP.

O ModelBinding recupera os dados, fornece para nossas aplicações através de controladores ou páginas Razor por exemplo e converte cadeias de caracteres para tipos do .NET

Este artigo tem o objetivo de introduzir às possíveis formas de manipular informações utilizando o ModelBinding.

<div id='bind-obtendo-propriedades-especificas'></div>

# [Bind] - Obtendo propriedades específicas

É possível que durante o processo de desenvolvimento surja situações onde seja necessário obter valores de propriedades especificas e ignorar outras. Para isto utilizamos o `[Bind]`. Assim ignorando propriedades não especificadas.

Código:
```csharp
[HttpPost]
public IActionResult OnPost([Bind("Name, Email, IsActive")] User user)
    => throw new NotImplementedException();
```

No código acima pode-se ver o método `OnPost` que deve receber um usuário como paranêtro. Mas como temos interesse apenas em três campos específicos, declaramos eles com o Bind e assim receberemos os demais retornaram nulos ou caso tenham, um valor padrão.

`Nota 1:` *Este comportamento relacionado aos atributos não especificados faz com que o [Bind] não seja uma boa opção em casos de edição já que ele não mantém o valor anterior das propriedades não especificadas.*

<div id='fromquery-obtendo-valores-da-cadeia-de-caracteres-de-consulta'></div>

# [FromQuery] - Obtendo valores da cadeia de caracteres de consulta

Antes de obter os valores a partir de uma cadeia de caracteres de consulta vamos deixar claro o que ela é. A cadeia de caracteres de consulta ou `QueryString` é um modelo para atribuição de valores a parâmetros utilizando a URL (Uniform resource locator).

Exemplo: `https://seudominio.com/controller/action/?Name=Joao&Email=mail@mail.com&IsActive=false&LastUpdateDate=null`

O trecho `?Name=Joao%Email=mail@mail.com` é a QueryString. Podemos identificá-la a partir do sinal de interrogação. Depois Temos os parâmetros no formato: `Parâmetro=valor` e separados pelo `&`. 

Bem, então para recuperar os dados que estão sendo transportados na QueryString e converter em um objeto do tipo `User` é necessário utilizar o atributo `[FromQuery]`:

Código:
```csharp
[HttpPost]
public IActionResult OnPost([FromQuery] User user)
    => throw new NotImplementedException();
```

Com o código acima o atributo `[FromQuey]` busca vincular os valores dos parâmetros da QueryString com propriedades equivalentes (mesmo nome e tipo compatível) no objeto `User`.

<div id='from-route-obtendo-valores-de-dados-de-rota'></div>

# [FromRoute] - Obtendo valores de dados de rota

Aqui também recuperamos dados a partir da rota, mas disponíveis de forma diferente do `[FromQuey]` que foi mostrado anteriormente. Observe a URL abaixo

URL: `https://seudominio.com/controller/action/mail@mail.com`

Para recuperar o endereço de e-mail informado na URL podemos usar o `[FromRoute]`. Veja o exemplo abaixo:

```csharp
[HttpPost("controller/action/{email}")]
public IActionResult Action([FromRoute] string email)
    => throw new NotImplementedException();
```

O `[HttpPost]` aqui recebe um modelo de rota que é complementado por um atributo que está sendo aguardado.

`Nota 2:` *É possível melhorar a definição da rota. Um assunto que não será abordado neste artigo para manter o foco no na introdução ao ModelBinding.*

<div id='fromform-obtendo-valores-de-campos-de-formularios-postados'></div>

# [FromForm] - Obtendo valores de campos de formulários postados

Como o nome do parâmetro já sugere, ele é utilizado para recuperar valores de um formulário. Seguindo o memo padrão dos parâmetros anteriores, é necessário que os nomes coincidam. No código o uso dele fica assim:

```csharp
[HttpPost]
public IActionResult ActionName([FromForm] User user)
    => throw new NotImplementedException();
```

`Nota 3:` Por ser característico (recuperar valores apenas de formulários postados) o `[FromForm]` aceita apenas métodos `[HttpPost].

<div id='frombody-obtendo-valores-do-corpo-da-solicitacao'></div>

# [FromBody] - Obtendo valores do corpo da solicitação

Caso a solicitação tenha um formato como por exemplo `JSON` você pode utilizar o `[FromBody]` para recuperar informações da mesma.

Exemplo de entrada JSON:
```json
{
    "Name": "Joao Teste",
    "Email": "mail@mail.com"
}
```


A declaração no código para leitura dos valores fica da seguinte forma:

```csharp
[HttpPost]
public ActionResult<User> ActionName([FromBody] User user)
    => throw new NotImplementedException();
```

<!-- # Obtendo valores de cabeçalhos HTTP

# Validando a conversão de dados -->

# Continue lendo:

[ASP.NET Configuration Manager](https://balta.io/blog/aspnet-configuration-manager)

[ASP.NET Minimal APIs](https://balta.io/blog/aspnet-minimal-apis)

[ASP.NET Minimal SignalR](https://balta.io/blog/aspnet-minimal-signalr)

## Cursos relacionados

`Gratuito` | [Fundamentos do C#](https://balta.io/cursos/fundamentos-csharp)

[Fundamentos do ASP.NET 6](https://balta.io/cursos/fundamentos-aspnet)

<div id='ref'></div> 

## Referências
[Documentação da Microsoft](https://docs.microsoft.com/en-us/aspnet/core/mvc/models/model-binding?view=aspnetcore-6.0)