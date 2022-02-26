Neste artigo vamos conferir as primeiras novidades e mudanças anunciadas para o C# 11, presente no .NET 7, que será lançado em Novembro de 2022.

## O que vem por aí?
Semana passada, Kathleen Dollard, a Program Manager do C#, liberou um [post com um Preview](https://devblogs.microsoft.com/dotnet/early-peek-at-csharp-11-features/) das primeiras novidades do C# 11, e vamos conferir neste artigo algumas delas.


## Como testar o Preview?
Para testar estas funcionalidades, você precisa editar seu arquivo `csproj` e adicionar a chave `<LangVersion>preview</LangVersion>` a ele, como mostrado abaixo.

```xml
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>net6.0</TargetFramework>
        <ImplicitUsings>enable</ImplicitUsings>
        <Nullable>enable</Nullable>
        <LangVersion>preview</LangVersion>
    </PropertyGroup>
</Project>
```

## Quebra de linha na interpolação de strings
No C# podemos utilizar o caractere `$` antes de uma `string` para permitir sua interpolação. O mesmo acontece com a necessidade de quebra de linhas dentro da `string` que demanda o caractere `@` antes da mesma.

```csharp
var inter = $"eu sou uma string {valor}";
var quebra = @"eu posso
                quebrar linhas";
var osDois = $@"eu interpolo {valores} e
                ainda quebro linhas";
```

Note que precisamos utilizar `$@` juntos para obter o resultado esperado. No C# 11, isto poderá ser feito diretamente na interpolação de `string`, apenas usando `$`.

```csharp
var v = $"Count ist: { this.Is.Really.Something()
                            .That.I.Should(
                                be + able)[
                                    to.Wrap()] }.";
```

## List patterns
Outra mudança bem-vinda é o **List Patterns** ou padrões de listas, algo similar ao que temos em outras linguagens como JavaScript e que nos permite criar padrões utilizando `...`.

```csharp
int[] arr1 = { 1, 2, 5, 6, 7, 8, 9, 10 };

public static int CheckSwitch(int[] values)
    => values switch
    {
        [1, 2, .., 10] => 1, // Vai até o valor 10
        [1, 2] => 2,
        [1, _] => 3,
        [1, ..] => 4, // Vai do 1 ao fim da lista
        [..] => 50 // Vale para toda a lista
    };
```

Ainda podemos capturar parte de uma lista, como mostrado abaixo utilizando `var middle` que no caso recebeu a **fatia** da lista.

```csharp
public static string CaptureSlice(int[] values)
    => values switch
    {
        [1, .. var middle, _] => $"Middle {String.Join(", ", middle)}",
        [.. var all] => $"All {String.Join(", ", all)}"
    };
```

## Null Checking
Outra mudança é a checagem de nulos, que já havia sido melhorada nas versões anteriores com uso do `is`, como mostrado abaixo:

```csharp
public static void M(string s)
{
    if (s is null)
    {
        throw new ArgumentNullException(nameof(s));
    }
    // Body of the method
}
```

Porém, no C# 11, teremos a inclusão do **double bang** `!!`, que impede a passagem de um valor nulo ao parâmetro, tornando assim dispensável a checagem por nulos.

```csharp
public static void M(string s!!)
{
    // Body of the method
}
```

## Conclusão
O C# vem se modernizando a cada versão, melhorando performance e se aproximando de linguagens mais "moderninhas". E você, está empolgado com esta nova versão que chega em Novembro deste ano?