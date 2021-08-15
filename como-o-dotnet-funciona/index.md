O C# pode ser considerado uma linguagem fortemente tipada, compilada e gerenciada? Vamos discutir os detalhes neste artigo.

## Tempo de compilação e tempo de execução
O primeiro ponto que precisamos alinhar antes de tudo, é entender que **tempo de compilação** ou **Compile Time** é o momento enquanto nosso código está sendo transformado para **Byte Code** ou **Native Code**.

Já o **tempo de execução** ou **Runtime** é o momento no qual o nosso código está sendo de fato executado pelo computador.

## Compilada ou interpretada
Linguagens compiladas, são aquelas que tem um processo de **compilação**, onde o código é transformado para outra coisa antes de ser executado.

Linguagens interpretadas são de fato lidas por um interpretador, executando o código naquele mesmo momento.

Para saber mais sobre as vantagens e desvantagens de cada uma, veja a aula 5 Linguagem Compilada e Interpretada do curso **Fundamentos de C#**.

## C# é compilado?
O .NET é um conjunto de bibliotecas, também conhecido como Framework, que suporta diferentes tipos de linguagens.

Isto mesmo, podemos utilizar .NET com C#, VB.NET, F# e até Cobol.NET. Aliás, podemos usar mais de uma linguagem no mesmo projeto, faça um teste e crie um arquivo `.vb` no seu projeto C#.

### IL
Suportar múltiplas linguagens, implica em comunicar-se com as mesmas. Agora como fazer isto em um Framework? O .NET com mais de 30K APIs, seria loucura escrever isto para cada linguagem suportada.

Desta forma, o que acontece no .NET é que o código `C#` que escrevemos não é compilado para código nativo e sim para **IL**.

**IL** é a sigla para **Intermediate Language** ou linguagem intermediária. Toda linguagem suportada pelo .NET é compilada para IL e só depois para native code (Código Nativo).

Apenas para intuito de curiosidade, você pode codificar em IL se quiser, aqui vai um exemplo de código.

**Código C#**
```csharp
using System;

namespace Balta.Core.Entities
{
    public class Tag : Entity
    {
        public string Title { get; set; }
        public string Url { get; set; }
        public string Tags { get; set; }
        public string Description { get; set; }
        public DateTime LastUpdateDate { get; set; }
        public bool Featured { get; set; }
        public Category Category { get; set; }
        public Guid CategoryId { get; set; }
    }
}
```

**IL gerada**
```
// Type: Balta.Core.Entities.Tag 
// Assembly: Balta.Core, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
// MVID: 32193851-5EDA-43A6-BEA9-C9C4560407C9
// Location: C:\dev\Balta\Balta.Core\bin\Debug\net5.0\Balta.Core.dll
// Sequence point data from C:\dev\Balta\Balta.Core\bin\Debug\net5.0\Balta.Core.pdb

.class public auto ansi beforefieldinit
  Balta.Core.Entities.Tag
    extends Balta.Core.Entities.Entity
{

  .field private string '<Title>k__BackingField'
    .custom instance void [System.Runtime]System.Runtime.CompilerServices.CompilerGeneratedAttribute::.ctor()
      = (01 00 00 00 )
    .custom instance void [System.Runtime]System.Diagnostics.DebuggerBrowsableAttribute::.ctor(valuetype [System.Runtime]System.Diagnostics.DebuggerBrowsableState)
      = (01 00 00 00 00 00 00 00 ) // ........
      // int32(0) // 0x00000000

  .field private string '<Url>k__BackingField'
    .custom instance void [System.Runtime]System.Runtime.CompilerServices.CompilerGeneratedAttribute::.ctor()
      = (01 00 00 00 )
    .custom instance void [System.Runtime]System.Diagnostics.DebuggerBrowsableAttribute::.ctor(valuetype [System.Runtime]System.Diagnostics.DebuggerBrowsableState)
      = (01 00 00 00 00 00 00 00 ) // ........
      // int32(0) // 0x00000000

  // Código omitido para reduzir o tamanho...
} // end of class Balta.Core.Entities.Tag

```

Desta forma, podemos resumir que o processo é:
```
Código C# | VB | F# > IL > Binário
```

### C# é compilado?
Sim! Mesmo não sendo diretamente compilado para código nativo, o C# é considerado uma linguagem compilada.

Hoje com o compilador **Roslyn** e uso dos **Top Level Programs**, recurso novo do C# 10, ele se assemelha muito a uma linguagem interpretada, mas ainda é compilado.

Por exemplo, esta é uma aplicação em C# 10:

```csharp
System.Console.WriteLine("Hello World");
```

Realmente parece um script, e o **Roslyn** compila isto em milésimos de segundo, tornando esta impressão ainda maior, mas como comentamos, o C# é **COMPILADO**.

## Tipada ou Não Tipada?
Uma linguagem tipada deve ser explícita nos seus tipos, deve obrigar o uso deles. Além disso, uma vez atribuído um tipo, o mesmo não pode mais ser alterado.

No caso do C#, podemos afirmar que ela é tipada, pois somos obrigados a declarar os tipos das variáveis.

```csharp
string name = "Andre";
name = 123; // ERRO
```

### Mas e o VAR?
Desde o C# 5, tivemos a inclusão do **var**, o que nos permite ter um código similar a este:

```csharp
var name = "André";
name = 123; // ERRO
```

Note que não utilizamos `string` e sim `var`. Isto torna o C# uma linguagem **não tipada**?

Não! O **var** é apenas um **syntax sugar**, por baixo dos panos, o que está sendo gerado é uma **string**;

Podemos afirmar isto pois depois de atribuído um valor a variável com `var`, não podemos mudar seu tipo, como mostrado no código anterior.

### Tipos dinâmicos

### Objects

## Managed e Unmanaged Code
### CLR
