As tuplas no C# são uma estrutura de dados do tipo de referência que podem armazenar valores de diferentes tipos.

## O que são tuplas?
As tuplas no C# são uma estrutura de dados do tipo de referência que podem armazenar valores de diferentes tipos.

A principal vantagem de uma tupla é permitir o armazenamento de múltiplos tipos, como por exemplo uma `string` e um `int` ao mesmo tempo.

Como o C# é uma linguagem fortemente tipada, esta possibilidade pode nos ajudar muito no dia-a-dia, evitando a criação de classes simples apenas para o transporte de dados.

```csharp
var tupla = new Tuple<string, int>("Meu Texto", 7);
```

As tuplas podem conter vários elementos e o último será uma propriedade de extensão que retorna a referência da própria tupla.

## Criando tuplas
A primeira forma que temos de criar tuplas é usando o `new Tuple<T>`, assim como fazemos normalmente com nossos objetos.

```csharp
var tupla = new Tuple<string, int>("Meu Texto", 7);
```

A segunda forma que temos é utilizando o `Tuple.Create<T>`, que é basicamente um wrapper para o método anterior.

```csharp
var tupla = Tuple.Create<string, int>("Meu Texto", 7);
```

## Lendo uma tupla
Sempre que uma tupla é criada, o compilador adicionar automaticamente o nome `ItemX` aos seus valores, então no exemplo acima, teríamos `Item1` sendo uma `string` com o texto `Meu Texto` e o `Item2` sendo um `int` com o valor `7`.

Podemos acessar este valor, utilizando o `.` após informar o nome da variável.

```csharp
var tupla = Tuple.Create<string, int>("Meu Texto", 7);
Console.WriteLine(tupla.Item1); // Exibe "Meu Texto"
Console.WriteLine(tupla.Item2); // Exibe 7
```

Este padrão é mantido por toda tupla, então caso você tenha uma tupla com 20 elementos, teria a variável `tupla.Item20`.

## Inferindo os tipos
Particularmente não gosto de utilizar `new Tuple<T>` nem `Tuple<T>.Create()` por alguns motivos, e o primeiro deles é que uma tupla pode ser inferida de forma muito simples no C#.

```csharp
(string, int) tupla = ("Meu Texto", 7);
Console.WriteLine(tupla.Item1); // Exibe "Meu Texto"
Console.WriteLine(tupla.Item2); // Exibe 7
```

Apenas colocando `(string, int)` podemos criar uma tupla com estes valores, tornando o código bem menos verboso.

O mesmo vale para o uso do `var` na criação da variável. Dado um conjunto de valores, o C# já entende que isto é uma tupla.

```csharp
var tupla = ("Meu Texto", 7);
Console.WriteLine(tupla.Item1); // Exibe "Meu Texto"
Console.WriteLine(tupla.Item2); // Exibe 7
```

## Nomeando os itens
O segundo motivo pelo qual prefiro inferir os tipos ao declarar `new Tuple <T>` ou `Tuple<T>.Create` é a possibilidade de nomear os itens.

Como vimos anteriormente, ao criar uma tupla, podemos ter **N** elementos e posteriormente o compilador os nomeia automaticamente para `ItemX`.

Porém, quando inferimos o tipo, podemos nomear os itens individualmente, como mostrado abaixo.

```csharp
(string Primeiro, int Segundo) tupla = ("Meu Texto", 7);
Console.WriteLine(tupla.Item1); // Exibe "Meu Texto"
Console.WriteLine(tupla.Item2); // Exibe 7
Console.WriteLine(tupla.Primeiro); // Exibe "Meu Texto"
Console.WriteLine(tupla.Segundo); // Exibe 7
```

Note que no exemplo acima ainda temos o `Item1` e `Item2`, eles não são descartados, porém, temos os itens `Primeiro` e `Segundo`, conforme nomeamos previamente na criação da tupla.

## Listas em tuplas
Podemos utilizar listas como itens das tuplas, tanto `List` quanto `Array`. Na verdade podemos utilizar qualquer objeto aqui.

```csharp
var tupla = (4.5, new List<string>());
tupla.Item2.Add("Meu Texto");
tupla.Item2.Add("Mais um");

foreach (var item in tupla.Item2)
    Console.WriteLine(item);
```

No exemplo acima, criamos uma tupla que tem um item `double` e um `List<string>`. Em seguida, populamos a lista com dois elementos e exibimos eles na tela.

## Tuplas em métodos
As tuplas podem ser amplamente usadas, inclusive como entrada e saída de funções caso haja necessidade de múltiplos parâmetros em uma única variável.

### Parâmetros
No exemplo abaixo temos uma função que imprime o nome na tela. Este é representado pelo parâmetro `name`, cujo tipo é uma `Tuple<string, string>`, neste caso representado pelo tipo inferido `(string FirstName, string LastName)`.

```csharp
SayMyName(("André", "Baltieri"));

void SayMyName((string FirstName, string LastName) name) 
    => Console.WriteLine($"{name.FirstName} {name.LastName}");
```

### Retorno
Outro uso bem comum das tuplas é no retorno de um método, principalmente quando não queremos criar uma classe apenas para retornar mais de um valor.

```csharp
var name = WhatsMyName("André", "Baltieri");
Console.WriteLine(name.FirstName); // Imprime André
Console.WriteLine(name.LastName); // Imprime Baltieri

(string FirstName, string LastName) WhatsMyName(string firstName, string lastName) 
    => (firstName, lastName);
```

No exemplo acima, temos um método chamado `WhatsMyName` que recebe duas strings e retorna uma tupla. 

Note que após a chamada do método, armazenamos a tupla em uma variável chamada `name` e a partir daí podemos chamar seus valores independentes como `name.FirstName` e `name.LastName`.

## ValueTuple
As tuplas são tipos de referência, o que significa que são armazenados no `heap` e imutáveis. Quaisquer alterações neles, geram um novo objeto.

Caso precise, temos também o `ValueTuple` que define uma tupla mas como tipo de valor, alocado no `stack` como os outros tipos buil-in.

```csharp
var tuple = new ValueTuple<string, int>("Meu Texto", 7);
```

## Conclusão
As tuplas são um tipo de referência (Ou valor no caso dos `ValueTuple`) que nos oferecem uma flexibilidade enorme no C#, permitindo criar variáveis que alocam diferentes tipos de dados.