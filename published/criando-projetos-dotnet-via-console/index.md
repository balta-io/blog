# Sumário

 1. [Introdução](#introducao)
 2. [Templates](#templates)
 3. [Instalando um template personalizado](#instalando-um-template-personalizado)
 4. [Continue lendo](#continue-lendo)
 5. [Referências](#ref)

<div id="introducao"></div>

# Introdução

O comando `dotnet new` é bem extenso e útil, princilpalmente para aqueles que não desenvolvem no Windows. Vamos entender neste artigo os principais usos derivados deste comando para facilitar o dia a dia no desenvolvimento.

A extensão das possibilidades de configurações que podem ser especificadas ao criar um projeto são enormes. Neste caso iremos aprender a estrutura base e os usos principais, as configurações mais avançadas terão seu uso desenvolvido em outro artigo.

# Verificando instalação do .NET

Antes de começarmos é necessário ter o conhecimento de quais versões do .NET temos disponíveis na máquina. Lembrando que podemos ter diversas versões instaladas sem problemas.

Como neste artigo utilizaremos um projeto do tipo `Blazor Server App` é necessário verificar se temos o .NET 6 instalado.

Para verificar as versões instaladas podemos utilizar o seguinte comando:

```console
dotnet --list-sdks
```

Caso queira saber apenas qual a versão mais recente instalada podemos utilizar o comando:

```console
dotnet --version
```

As saídas devem ser semelhante a esta:

![dotnet-sdks-list](images/dotnet-sdks-list.png)

Caso você não tenha a versão necessária instalada, ao fim deste artigo tem uma referência à um artigo relacionado para instalação do .NET que você pode seguir para instalar a versão que for necessária.

Agora que confirmamos que temos a versão necessária instalada, podemos ir para o próximo tópico.

<div id="templates"></div>

# Templates

Os templates são modelos pré-configurados existêntes no próprio .NET, também é possível adicionar modelos personalizados. Para verificar quais modelos estão disponíveis você pode executar no seu terminal o comando `dotnet new` sem outros parametros. A saída deverá ser algo semelhante a esta:

```console
The 'dotnet new' command creates a .NET project based on a template.

Common templates are:
Template Name         Short Name    Language    Tags
--------------------  ------------  ----------  -------------------
ASP.NET Core Web App  razor,webapp  [C#]        Web/MVC/Razor Pages
Blazor Server App     blazorserver  [C#]        Web/Blazor
Class Library         classlib      [C#],F#,VB  Common/Library
Console App           console       [C#],F#,VB  Common/Console
Windows Forms App     winforms      [C#],VB     Common/WinForms
WPF Application       wpf           [C#],VB     Common/WPF

An example would be:
   dotnet new console

Display template options with:
   dotnet new console -h
Display all installed templates with:
   dotnet new --list
Display templates available on NuGet.org with:
   dotnet new web --search
```

Com isto ele já nos dá uma visão rápida sobre como criar projetos a partir dos templates. Basta usar o `dotnet new` seguido do `Short Name` do modelo.

```console
dotnet new <TEMPLATE>
```

`Nota 1:` Ao utilizar o comando como exemplificado acima será utilizada a versão mais recente instalada do .NET, os modelos só serão exibidos se estiverem disponíveis nesta versão. Exemplo: Caso tenha apenas o .NET Core 3.1 instalado você não terá disponível os templates para projetos Blazor.

<div id="instalando-um-template-personalizado"></div>

# Instalando um template personalizado
O templete que usaremos foi desenvolvido por um framework relativamente recente chamado `MudBlazor`. Para instalar usaremos o seguinte comando:

```console
dotnet new --install MudBlazor.Templates
```

A saída correta deve ser semelhante a esta:

```console
The following template packages will be installed:
   MudBlazor.Templates

MudBlazor.Templates is already installed, version: 0.6.1, it will be replaced with version .
MudBlazor.Templates::0.6.1 was successfully uninstalled.
Success: MudBlazor.Templates::0.6.1 installed the following templates:
Template Name        Short Name  Language  Tags
-------------------  ----------  --------  --------------------------------------
MudBlazor Templates  mudblazor   [C#]      Blazor/Desktop/macOS/Windows/Cloud/Web
```

Note que na última linha ele nos informou o `Short Name` que podemos usar para criar o projeto com o template.

# Criando o projeto

Para criarmos o projeto utilizaremos o `dotnet new` que foi mostrado no início seguido do `Short Name` do template. Ficando desta forma:

```console
dotnet new mudblazor -o meuProjeto
```

Opa! Acabamos de usar algo diferente. O parâmetro `-o` seguido do texto especifica que você está definindo um nome para o projeto que será criado. Caso não o faça, o projeto receberá um nome padrão.

Outro diferencial do uso do parâmetro `-o` é que ele cria um diretório com o nome especificado e insere os arquivos do projeto lá. Ao não utilziar ele você estará inserindo os arquivos do projeto diretamente no diretório em que o terminal está apontando no momento da execução do comando. 

<!--# Executando o projeto-->

<div id="continue-lendo"></div>

# Continue lendo:

[.NET - Instalação, Configuração e primeiros passos](https://balta.io/blog/dotnet-instalacao-configuracao-e-primeiros-passos)

[Números no C# e .NET](https://balta.io/blog/numeros-csharp-dotnet)

[DateTime no C# e .NET](https://balta.io/blog/datetime-csharp-dotnet)

## Cursos relacionados

`Gratuito` | [Fundamentos do C#](https://balta.io/cursos/fundamentos-git-azure-devops)

[Fundamentos do ASP.NET 6](https://balta.io/cursos/fundamentos-aspnet)

<div id='ref'></div> 

## Referências
[Documentação da Microsoft](https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-new)