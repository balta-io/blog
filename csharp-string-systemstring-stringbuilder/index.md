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
Outro item que causa bastante confusão é o `StringBuilder`, mas ele age de forma bem diferente do `string` e `String`, ele tem a finalidade de concatenar **strings**, ou seja, juntar pedaços de textos em um texto maior.

É normal vermos o uso da concatenação de strings utilizando o operador de atribuição `+=` do C#, o que pode não ter muito efeito negativo em cadeias de caracteres pequenas, mas em longos textos pode ser tornar um grande vilão.

```csharp
var meuTexto = "Olá mundo";
meuTexto += " aqui vai mais um";
meuTexto += " e aqui mais um pedaço da string";
```

O que ocorre aqui é que para cada concatenação que fazemos deste modo, uma terceira variável é gerada, com conteúdo das anteriores se acumulando. Agora imagina este cenário com muitas concatenações?

Para isto, existe o `StringBuilder`, como o nome já diz, um "construtor de strings", feito sob medida para esta demanda de concatenação de textos.

```csharp
var meuTexto = new StringBuilder();
meuTexto.Append("Olá mundo");
meuTexto.Append(" aqui vai mais um");
meuTexto.Append(" e aqui mais um pedaço da string");

Console.WriteLine(meuTexto.ToString());
```

No exemplo acima temos um código muito mais otimizado, utilizando o `StringBuilder` para construir um texto longo que é baseado em outras cadeias de caracteres. Notamos também que por se tratar de um objeto complexo, precisamos do método `ToString()` para imprimir o valor do texto composto na tela.

### Interpolação de strings
É importante frisar que não devemos confundir a concatenação de **strings** com a interpolação de **strings**. Você pode usar a interpolação sempre que precisar, e uma das melhores formas é sinalizar isto ao compilador utilizando o `$` antes da mesma.

```csharp
var meuTexto = "Mundo";
var outroTexto = $"Olá {meuTexto}"
```


## Curso de Fundamentos do C#
Quer aprender mais sobre C#? Então confere nosso curso **Fundamentos do C#**, que conta com mais de 100 aulas e 12.2 horas de conteúdo de forma simples, direta e objetiva.

<a class="uk-button uk-button-primary uk-width-1-1" href="https://balta.io/cursos/fundamentos-csharp?utm_source=site&utm_medium=blog&utm_campaign=blog-to-course&utm_content=csharp-strings">CURSO FUNDAMENTOS DO C#</a>

## Conclusão
Embora seja algo fundamental, a criação e consumo de **strings** no C# pode ter suas pegadinhas e saber estes macetes pode salvar muito consumo de processamento e memória da sua aplicação, o que resulta em uma redução de custo de infraestrutura! Em suma, mais economia para o bolso da empresa!


<div role="main" id="blog-s1-dotnet-134e3db1eea6c9829db1"></div>
<script type="text/javascript" src="https://d335luupugsy2.cloudfront.net/js/rdstation-forms/stable/rdstation-forms.min.js"></script>
<script type="text/javascript"> new RDStationForms('blog-s1-dotnet-134e3db1eea6c9829db1', 'UA-48664517-12').createForm();</script>