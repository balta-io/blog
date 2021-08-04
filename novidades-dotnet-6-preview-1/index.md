Com o lançamento programado apenas para Novembro de 2021, o .NET 6 teve seu primeiro Preview anunciado, e neste artigo vamos conferir alguns detalhes.

<iframe width="100%" height="315" src="https://www.youtube.com/embed/_un-3oeQ5hE" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## Unificação
o .NET 6 quer continuar o que o .NET 5 começou, a unificação da plataforma. A ideia é conseguirmos cada vez mais compartilhar código entre diversas plataformas utilizando apenas .NET.

Inicialmente o foco está no Xamarin para Android e iOS e MacOs, com a possibilidade de integração de forma híbrida com o Blazor (Lembra do Blazor Mobile Bindings?).

Em suma, seja você um desenvolvedor Mobile (Xamarin, Android, iOS), Desktop ou Web (ASP.NET, Cloud), o .NET está focado em te atender e melhorar sua vida.

E quando falamos em melhorar, é muito! Por exemplo, ao instalar a SDK, basta executar `dotnet new android` e  `dotnet run` para criar e executar um App Android no seu emulador.

### Open Planing e Suporte

O .NET 5 está sendo criado com um planejamento aberto, que você pode conferir no GitHub. Além disso, o .NET 6 será suportado por três anos após seu lançamento, como parte do seu LTS.

## .NET MAUI

MAUI é a sigla para Multi-platform App UI, que é uma forma mais moderna de se criar interfaces para diversas plataformas com .NET como base.

O MAUI extende o Xamarin e será incluso (Versão inicial) no .NET 6. Seu maior foco é entregar Apps com visuais melhores e compartilhando mais código entre as plataformas.

Nesta primeira versão você será capaz de criar Apps para Android, iOS, macOS, e Windows, todos baseados no Xamarin Forms. Como foco desta primeira release estão a performance do App, componentes e experiência de desenvolvimento.

Com a chegada do MAUI o `Xamarin.Essentials` também chega ao .NET 6, que é um conjunto de libs que temos para acessar recursos nativos dos dispositivos, como sensores, storage e afins.

No Preview 1 o MAUI vai suportar Android e iOS e futuramente macOS e Windows, além de recursos como Hot Reload nos Apps. 

Em suma, você pode começar hoje no .NET 6 com Xamarin e Xamarin.Forms.

## Blazor Desktop Apps
O Blazor se tornou bem popular desde seu lançamento e o poder de trabalhar com C# (Ou reusar seu código) nas interfaces Web é encantador.

<iframe width="100%" height="315" src="https://www.youtube.com/embed/z_22O4eZLnk" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Com projetos como o Mobile Blazor Bindings que possibilita "plugar" componentes Blazor em suas Apps nativas e ainda ter a comunicação entre eles, o Blazor ganhou ainda mais espaço.

O Blazor Desktop é justamente uma evolução disso, ele permite que você crie Apps híbridas com parte nativa e parte web, reusando componentes que você já escreveu para seu App web por exemplo.

Em suma temos um container que é a aplicação nativa, que pode ser inclusive em Swift, e vários componentes Blazor ou mesmo um App completo rodando dentro da mesma.

O Blazor também roda sob o MAUI, o que te permite ter interfaces em XAML interagindo com ele. Nas imagens abaixo temos um App rodando nativamente no MacOs e um no Windows com WPF onde o componente Web escrito com Blazor está chamando um alert.

![MacOs](https://devblogs.microsoft.com/dotnet/wp-content/uploads/sites/10/2021/02/blazor-macos-1536x1016.jpg)

![Windows](https://devblogs.microsoft.com/dotnet/wp-content/uploads/sites/10/2021/02/blazor-windows.jpg.png)


## Outras melhorias
Fast Inner Loops e um melhor suporte a ARM64, melhorias para se trabalhar com containers, no .NET CLI e novas APIs para trabalhar com matemática.

Também chega o suporte para o novo Apple Silicon "M1" nesta versão, e o .NET foi um dos primeiros a receber o emblema de portabilidade, porém ainda consideram como `alpha-1`.

Ainda falando sobre Mac, .NET Rosetta 2 Emulation também tem sua primeira versão. A Microsoft trabalhou junto com a Apple nisso trazendo suporte já no .NET 5 para MacOS x64 que funcionam Big Sur 11.2+. Rosetta é a "interface" que temos para emular Apps que não são de fato feitas para os processadores M1 da Apple.

Outra novidade é o Single File Apps que chega no Windows e Mac. Um único binário que contém exatamente um artuivo e não precisa ser extraído nem usar pastas temporárias, nada! Este recurso antes estava presente apenas no Linux.

## EF Core 6
Atualmente ainda está rodando sob o .NET 5 mas mudará em um futuro próximo. EF Core 6 não vai rodar sob o Entity Fraework, então já é bom começar a pensar em migrar.

### Novo tipo Unicode
Junto ao EF Core 6 teremos um novo atributo `unicode` onde podemos controlar a geração do tipo de caractere no banco, como por exemplo.

```csharp
 public class Book
{
    public int Id { get; set; }
    public string Title { get; set; }

    [Unicode(false)]
    [MaxLength(22)]
    public string Isbn { get; set; }
}
```

```sql
CREATE TABLE [Book] (
    [Id] int NOT NULL IDENTITY,
    [Title] nvarchar(max) NULL,
    [Isbn] varchar(22) NULL,
    CONSTRAINT [PK_Book] PRIMARY KEY ([Id]));
```

### Precision Attribute

Outra mudança legal é a possibilidade de informar o `precision` e `scale` em tipos de moeda, diretamente via `DataAnnotations` sem precisar criar uma arquivo de mapeamento.

```csharp
public class Product
{
    public int Id { get; set; }

    [Precision(precision: 10, scale: 2)]
    public decimal Price { get; set; }
}
```

```sql
REATE TABLE [Product] (
    [Id] int NOT NULL IDENTITY,
    [Price] decimal(10,2) NOT NULL,
    CONSTRAINT [PK_Product] PRIMARY KEY ([Id]));
```

### Function Random

O EF Core 6 traz também um recurso para utilizar o `RAND` nas queries, através do `EF.Functions.Random()`, implementando nesta versão.

```csharp
var users = context.Users.Where(u => u.Popularity == (int)(EF.Functions.Random() * 5.0) + 1).ToList();
```

```sql
SELECT [u].[Id], [u].[Popularity], [u].[Username]
FROM [Users] AS [u]
WHERE [u].[Popularity] = (CAST((RAND() * 5.0E0) AS int) + 1)
```

## Microsoft.Data.Sqlite 6.0 Preview 1

A Microsoft tem investido bastante tempo (E dinheiro) no `SQLite` e o provider oficial chega em uma nova versão também, acompanhando as mudançãs do EF Core 6.

## ASP.NET 6
Inicialmente o ASP.NET não traz muitas mudanças, até porque ele está bem maduro e não precisa de tantos incrementos por parte da Microsoft.

Porém, algumas destas mudanças são de grande impacto e particularmente fiquei bem feliz com elas, principalmente o `WASM AOT`.

### Hot reload
O ASP.NET tem focado cada vez mais em melhorar a experiência de desenvolvimento e o Hot Reload é um fator importante nisto. Atualizar a UI dos Apps feitos em Razor/Blazor sem perder o estado da dos mesmos, traz muito mais produtividade para nós. 

### Micro APIs
Há uma nova forma de criar APIs realmente pequenas, aquelas que você precisa ter apenas um endpoint por exemplo. Agora você não precisa mais trazer todo aquele template do ASP.NET, com MVC e afins.

Muito disso vai ao encontro dos `Top Level Programs` que temos recente no C#, que nos possibilita basicamente escrever `scripts`, se o famoso escopo `Main`, obrigatório anteriormente em qualquer aplicação C#.

Particularmente, acho que diversos pontos dos Micro APIs vão chegar ao ASP.NET e muito do que temos no `Program.cs` hoje vai ser mudado. Isto fica invisível para nós, mas já reduz código e complexidade das APIs.

### Single-file publishing
Agora temos a capacidade de publicar uma aplicação com apenas um arquivo, de forma standalone mas ainda assim com alta performance. É o mesmo App, porém como se estivesse compactado em apenas um arquivo.

### WebAssembly ahead-of-time (AoT) compilation
Este para mim é o melhor recurso até o momento. Quando usamos a compilação JIT (Just in time) nosso App é compilado na hora da execução, já o AOT permite que o mesmo seja compilado antes de ser executado, o que traz um ganho de performance principalmente na primeira execução.

Neste caso em específico, estamos falando do Blazor Wasm, uma das tecnologias que mais precisam desse tipo de melhoria no momento. Sabemos que o WASM é novo em geral, o .NET no Browser ainda tem partes do Mono, ainda é relativamente "pesado" comparado a Apps React/Angular.

Particularmente fico feliz em ver o esforço da Microsoft direcionado aqui. Neste mesmo artigo falamos sobre Blazor for Desktops que é outra novidade bacana, e claro, o MAUI. Então somando estes pontos, podemos dizer que estamos no caminho certo!

### Templates SPA atualizados
Este é um recurso que particularmente nunca usei. Sempre crio meus Apps Angular/React for do ASP.NET, inclusive fora do Visual Studio (Prefiro VS Code para front), mas se você utiliza os templates que a Microsoft "shipa" junto ao VS, tem atualizações para as versões mais novas.

### Blazor Hybrid Desktop Apps
Quem esteve na nossa Live que rolou em Novembro de 2020 viu minha demo de Blazor Mobile Bindings e um App Blazor rodando INSTALADO no MacOs! Este é um tipo de experiência fantástica na qual podemos colocar pedaços dos nossos Apps Web, feitos em Blazor, em outros Apps.

### HTTP/3
Nesta versão foi adicionado também o suporte a HTTP/3 e o protocolo QUIC, já presente no Edge. Se o seu servidor suportar, você já pode usar as novidades.

## Conclusão
É apenas um primeiro Preview e ainda vamos falar bastante do .NET 6 por aqui, mas com certeza esta versão é uma das que vai trazer GRANDES mudanças para o nosso ambiente de desenvolvimento.

Eu ainda quero ver o MAUI sem a necessidade de utilizar XAML, só com sintaxe declarativa, quero escrever meu código todo em C# e ver ele rodando no Desktop, Web e Mobile, vai ser lindo!

## Fontes
* https://devblogs.microsoft.com/dotnet/announcing-net-6-preview-1/
* https://github.com/dotnet/aspnetcore/issues/27217
* https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-6-0-preview-1/
* https://devblogs.microsoft.com/aspnet/asp-net-core-updates-in-net-6-preview-1/

<div role="main" id="blog-s1-dotnet-134e3db1eea6c9829db1"></div>
<script type="text/javascript" src="https://d335luupugsy2.cloudfront.net/js/rdstation-forms/stable/rdstation-forms.min.js"></script>
<script type="text/javascript"> new RDStationForms('blog-s1-dotnet-134e3db1eea6c9829db1', 'UA-48664517-12').createForm();</script>