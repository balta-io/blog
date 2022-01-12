String, string ou StringBuilder? Para que servem e quando devemos utilizar cada um destes tipos no C#!

## String ou string
Uma `string` no C# é uma cadeia de caracteres que representa um tipo de valor e pode armazenar até 1 bilhão de caracteres e 2GB de memória. Podemos inicializar uma `string` das seguintes formas.

```csharp
// Cria uma string vazia 
// Receberá warning de possível referência nula a partir do .NET 6
string meuTexto;

// Cria uma string informando um valor inicial
string meuTexto = "valor";

// Cria uma string vazia
string meuTexto = string.Empty;

// ERRO
var meuTexto;

// Cria uma string informando um valor inicial
var meuTexto = "valor";

// Cria uma string vazia
var meuTexto = string.Empty;

// Cria uma string (Nula) que permite nulos
string? meuTexto;
```

Assim como nos outros tipos de valor, para permitir valores nulos em um `string` precisamos do **?** após sua definição. Outro detalhe é o uso do `string.Empty` que define um valor vazio para uma `string`.

```csharp
string? meuTexto;
string meuTexto = string.Empty;
```

É importante frisar que o `string.Empty` é apenas um **sintax sugar** para `""`, ambos tem o mesmo resultado e não influenciam na performance. A diferença é que em alguns cenário o `string.Empty` não é permitido por não ser considerado uma constante.

### Qual a diferença entre String e string?
Tá bem, mas qual a diferença entre `string` e `String`? Por quê uma é minúscula e outra começa com letra maiúscula?

```csharp
string meuTexto = "andre";
String umValor = "algum valor";
```

Embora `string` (Toda minúscula) seja a disparadamente a mais utilizada, o tipo correto para **strings** no C# é o `System.String` ou somente `String`, sim, **S** maiúsculo.

Todo tipo no .NET deriva do tipo base `System` e no caso das **strings** isto não é diferente. Desta forma, mantendo o padrão de todos os tipos no C#, a **string** tem seu namespace/tipo definidos como `System.String`.

O mesmo acontece com os tipos `Int16` que é comumente chamado de `short`, `Int32` que é chamado de `int` e `Int64` que é chamado de `long`. Por conveção, toda classe/estrutura no .NET começa com letra maiúscula.

Então os tipos que usamos tanto, como `short`, `int`, `string`, `bool`, `float`, `double` não passsam de **sintax sugar** ou **alias** (Apelido) para os tipos reais no .NET.

### Na prática o que isto muda?

Nada! Pode continuar utilizando seu `string` normalmente que para o compilador será tudo a mesma coisa, não vai afetar performance o qualquer outra coisa. É apenas algo estético.

Particularmente, acredito que muitos desses **alias** foram trazidos do C++, PHP e outras linguagens com sintaxe **C LIKE**, e por questões de harmonia foram mantidos. Tenho que concordar também que dá para contar nos dedos as vezes que usei `String` ao invés de `string`.

## StringBuilder


