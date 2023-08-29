Neste artigo vamos aprender a armazenar Claims customizados e fazer a leitura deles posteriormente no ASP.NET.

# O que são Claims?

A tradução da palavra Claim é afirmação e podemos dizer que no .NET, um Claim nada mais é do que uma composição de chave/valor que afirma algo.

Dada esta composição, podemos tomar ações de autorização em nossa aplicações ou APIs, da forma que desejarmos.

# Claims padrões

O .NET também traz alguns Claims padrões, dentro do enumerador `ClaimTypes` que nos fornece dentre outros o `[ClaimTypes.Name](http://ClaimTypes.Name)` que serve como base para obter o nome do usuário.

```csharp
new Claim(ClaimTypes.Name, "xyz@balta.io")
```

## ClaimTypes.Name e ClaimTypes.Role

Existem dois Claims em específico que são extremamente úteis no ciclo de vida de qualquer aplicação ASP.NET ou Blazor, e eles são o `ClaimTypes.Name` e `ClaimTypes.Role`.

Sempre que atribuímos o `ClaimTypes.Name` temos acesso instantâneo a ele através do `User.Identity.Name`.

```csharp
...
new Claim(ClaimTypes.Name, "andré");
...

public IActionResult GetAsync()
{
	var name = User.Identity.Name; // andré
}
```

O mesmo vale para o `ClaimTypes.Role` que podemos atribuir várias vezes e posteriormente verificar através do `User.IsInRole` ou pelo atributo `Authorize`.

```csharp
...
new Claim(ClaimTypes.Role, "student");
new Claim(ClaimTypes.Role, "premium");
...

[Authorize(Roles = "student")]
public IActionResult GetAsync()
{
	if(User.IsInRole("premium"))
		...
}
```

# Como criar Claims Customizados

Mas você sabia que podemos criar Claims customizados? Que não estão definidos no enumerador `ClaimTypes`?

Dado que um `Claim` é apenas uma composição de ***Chave/Valor***, podemos utilizar qualquer literal para sua chave. Na verdade o `ClaimTypes` é apenas um atalho para isto, que previne o uso de uma literal inválida.

Se analisarmos o código que gera os Claims mostrado no artigo anterior, teremos o seguinte:

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

Podemos modificar este código para adicionar qualquer Claim customizado, como desejarmos, no caso, para ser bem explícito, vamos utilizar o Claim abaixo.

```csharp
private static ClaimsIdentity GenerateClaims(User user)
{
    var ci = new ClaimsIdentity();
    ci.AddClaim(new Claim(ClaimTypes.Name, user.Email));
    foreach (var role in user.Roles)
        ci.AddClaim(new Claim(ClaimTypes.Role, role));

		ci.AddClaim("fruit", "banana"); // 👈

    return ci;
}
```

**IMPORTANTE**
Este Claim também será adicionado ao PayLoad do Token caso esteja trabalhando com APIs ou nos Cookies caso esteja trabalhando com ASP.NET. Então, nada de informação sensível aqui.

# Como ler Claims Customizados

Com o Claim adicionado, agora precisamos obter esta informação, e isto pode ser feito através do objeto User.Claims, que contém todos os Claims do contexto atual.

```csharp
User.Claims.FirstOrDefault(x => x.Type == "fruit"); // banana
```

Pronto, agora você tem tanto a criação como leitura de Claims customizados e pode utilizá-los para armazenar mais informações além do nome do usuário.

# Extension Methods

Aproveitando a oportunidade, este é um ótimo cenário para se criar um **Extension Method**, que retorna os Claims que você precisa.

Assim você evita possíveis erros, já que o Claim pode não existir ou o valor dele, por ser uma string, pode não ser convertido para o tipo esperado.

Abaixo está um exemplo que utilizo, onde tenho extensões para ler o Id, Nome e E-mail do usuário, três Claims que sempre utilizo nas minhas aplicações.

```csharp
public static class ClaimsPrincipalExtension
{
    public static Guid Id(this ClaimsPrincipal user)
    {
        try
        {
            var id = new Guid(user.Claims.FirstOrDefault(c => c.Type == "Id")?.Value ?? string.Empty);
            return id;
        }
        catch
        {
            return Guid.Empty;
        }
    }

    public static string Name(this ClaimsPrincipal user)
    {
        try
        {
            return user.Claims.FirstOrDefault(c => c.Type == ClaimTypes.Name)?.Value ?? string.Empty;
        }
        catch
        {
            return string.Empty;
        }
    }

    public static string Email(this ClaimsPrincipal user)
    {
        try
        {
            return user.Claims.FirstOrDefault(c => c.Type == ClaimTypes.Email)?.Value ?? string.Empty;
        }
        catch
        {
            return string.Empty;
        }
    }
}
```

Para utilizar basta importar o namespace onde criou sua extensão, no caso eu já faço isto utilizando o **Global Using** para não haver a necessidade de importar este namespace em todos os arquivos que precisarei dele.

```csharp
global using Balta.Ui.Web.Extensions;
```

E assim utilizar a extensão, que funciona tanto para APIs, MVC, Minimal APIs e até Blazor.

```csharp
// Razor
@User.Id()
@User.Name()
@User.Email()

// MVC
User.Id()
User.Name()
User.Email()

// Minimal APIs
user.Id()
user.Name()
user.Email()
```

# Conclusão

Trabalhar com Claims customizados no ASP.NET e Blazor é uma tarefa relativamente simples e com ajuda do C# tornamos nossa vida ainda mais fácil.