 # Sumário

 1. [Introdução](#introducao)

 2. [Array monodimensional (Vetor)](#array-monodimensional)
   
    2.1. [Declarando o vetor](#declarando)

    2.2. [Inicializando o vetor](#inicializando)

    2.3. [Lendo itens o vetor](#lendo-itens)

    2.4. [Percorrendo o vetor](#percorrendo)

 3. [Array multidimensional (Matriz)](#array-multidimensional)
   
    3.1. [Declarando a matriz](#declarando-matriz)

    3.2. [Inicializando a matriz](#inicializando-matriz)

    3.3. [Lendo itens da matriz](#lendo-itens-matriz)

    3.4. [Percorrendo a matriz](#percorrendo-matriz)

 4. [Agora é com você](#agora-e-com-voce)

 5. [Continue lendo](#continue-lendo)

 6. [Cursos relacionados](#cursos-relacionados)

 7. [Referências](#ref)

<div id='introducao'></div> 

# Introdução

Os arrays são representações de listas no C#. Eles podem ser compostos por um conjunto de itens do mesmo tipo e podem ser manipulados ao apontar a posição desejada.

Este artigo tem o  objetivo de introduzir ao uso de arrays monodimensionais (também conhecidos como vetores) e multidimensionais (também conhecidos como matrizes) passando por declaração, inicialização, leitura e manipulação.

<div id='array-monodimensional'></div> 

# Array monodimensional (vetor)

![square-horizontal-list](images/vetor.png)

<div id='declarando'></div> 

# Declarando o vetor

O array monodimensional, também conhecido como vetor, é uma lista simples que pode ser declarada da seguinte forma:

```csharp
var monodimensionalArray = new int[10];
```

É preciso lembrar que os arrays no C# a posição inicial como zero. Sendo assim, um array de 10 elementos fica com as posições: 0, 1, 2... 8, 9.

O C# uma linguagem tipada e isto implica que, uma vez definido o tipo do array, apenas valores do mesmo tipo serão aceitos.

<div id='inicializando'></div> 

# Inicializando o vetor

O array pode ser inicializado na declaração:

```csharp
var monodimensionalArray = new int[10] {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
```

Ou posteriormente adicionando valor a cada posição:

```csharp
monodimensionalArray[0] = 61;
```

O valor entre colchetes representa a posição do array que deve receber o valor. No exemplo acima, a posição zero está recebendo o valor 61, ou seja, ao lermos a primeira posição do nosso array teremos 61 como retorno.

<div id='lendo-itens'></div> 

# Lendo itens do vetor

Ler o valor de um elemento do array é bem simples. Basta chamar o array e entre colchetes passar a posição que desejamos:

```csharp
Console.WriteLine($"{monodimensionalArray[0]}")
```

E assim teremos como saída no console o valor da posição indicada.

<div id='percorrendo'></div> 

# Percorrendo o vetor

Nós podemos utilizar tanto a instrução `for` quanto a instrução `foreach` para percorrer o nosso array e declararmos uma ação cada vez que chegarmos em uma posição diferente.

Primeiro vamos atribuir valores para cada posição do nosso array:

```csharp
for (var item = 0; item < monodimensionalArray.Length; item++)
    monodimensionalArray[item] = item;
```

No exemplo acima, para cada posição do nosso array, o valor desta posição (representada por monodimensionalArray[item]) é atribuido com o número da posição no array. Ou seja: A posição zero recebe o valor 0, a posição um recebe o valor 1 e assim por diante.

Nos exemplos abaixo, o array é percorrido e o valor armazenado em cada posição é exibido no console:

```csharp
for (var item = 0; item < monodimensionalArray.Length; item++)
    Console.WriteLine(monodimensionalArray[item]);
```

Simplificando com `foreach`:

```csharp
foreach (var item in monodimensionalArray)
    Console.WriteLine(monodimensionalArray[item]);
```

`Nota 1:` *Nos casos em que se faça necessário copiar um array, opte sempre pelo método `.Clone()`. Usar `=` com arrays apenas cria uma nova referência para os mesmos elementos, e a edição de um elemento pode acabar modificando os "dois" arrays.*

<div id='array-multidimensional'></div> 

# Array multidimensional (matriz)

Um array multidimensional (matriz) é uma tabela. Podendo ser exemplificado graficamente assim (matriz 2x3):

![square-table](images/matriz.png)

<div id='declarando-matriz'></div>

## Declarando a matriz

Podemos declarar a matriz acima como um array multidimensional 3x2:

```csharp
var rows = 3;
var columns = 2;

var multidimensionalArray = new int[rows,columns];
```

Ou de forma mais direta:

```csharp
var multidimensionalArray = new int[3,2];
```

<div id='inicializando-matriz'></div>

# Inicializando a matriz

A matriz pode ser inicializada na declaração, variando de acordo com a dimensão da matriz:

```csharp
var multidimensionalArray = new int[3,2] { {16,52}, {91,43}, {77,28} };
```

Ou posteriormente adicionando valor a cada posição assim como mostrado na matriz monodimensional:

```csharp
multidimensionalArray[0,0] = 16; // primeira linha, coluna um
multidimensionalArray[0,1] = 52; // primeira linha, coluna dois
multidimensionalArray[1,0] = 91; // segunda linha, coluna um
multidimensionalArray[1,1] = 43; // segunda linha, coluna dois
multidimensionalArray[2,0] = 77; // terceira linha, coluna um
multidimensionalArray[2,1] = 28; // terceira linha, coluna dois
```

Observe que, diferente do vetor, atribuir valor à matriz exige que seja declarada a linha e coluna do item que está recebendo valor.

<div id='lendo-itens-matriz'></div> 

# Lendo itens da matriz

Ler o valor de um item de uma matriz é tão simples quanto ler de um vetor. Basta chamar a matriz e entre colchetes passar a linha e coluna que desejamos:

```csharp
Console.WriteLine(multidimensionalArray[0,0]);
```

E assim teremos como saída no console o valor da posição indicada.

<div id='percorrendo-matriz'></div> 

# Percorrendo a matriz

Com a nossa matriz definida, vamos percorrer os elementos utilizando a instrução `foreach` para exibir o valor dos elementos no console:

```csharp
foreach (var item in multidimensionalArray)
    Console.WriteLine($"{item}");
```

<div id='agora-e-com-voce'></div>

# Agora é com você

Para exercitar o que foi mostrado neste artigo, podem ser feitos outros exemplos com vetores de objetos e matrizes com 2+ dimensões. Exibir e percorrer graficamente um vetor ou matriz também pode ser de grande aprendizado, considerando que envolve mais complexidade do que apenas declarar e percorrer matrizes em um contexto estático.

<div id='continue-lendo'></div> 

# Continue lendo:

[Criando projetos .NET via console](https://balta.io/blog/criando-projetos-dotnet-via-console)

[Números no C# e .NET](https://balta.io/blog/numeros-csharp-dotnet)

[DateTime no C# e .NET](https://balta.io/blog/datetime-csharp-dotnet)

<div id='cursos-relacionados'></div> 

# Cursos relacionados

`Gratuito` | [Fundamentos do C#](https://balta.io/cursos/fundamentos-csharp)

[Fundamentos do ASP.NET 6](https://balta.io/cursos/fundamentos-aspnet)

<div id='ref'></div> 

# Referências
[Documentação da Microsoft](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/arrays/)
