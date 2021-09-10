Sumário
 1. [Introdução](#introducao)
 2. [Números inteiros](#numint)
 3. [Números reais](#numreais)
 4. [Conversão implicita](#conimplicita)
 5. [Conversão explicita](#conexplicita)
 6. [Função Parse](#funparse)
 7. [Classe Convert](#classeconvert)
 8. [Operadores aritméticos](#oparitmeticos)
 9. [Operadores de atribuição](#opatribuicao)
 10. [Operadores de comparação](#opcomparacao)
 11. [Referências](#referencias)

<div id='introducao'></div>

## Introdução
Abordaremos a definição e uso dos tipos de números no C# e .NET com o intuito de promover o exercício de aprendizagem e fixação dos usos relativos aos números e operações relacionadas. Então vamos lá, direto ao ponto!

<div id='numint'></div>

## Números inteiros
No .NET os números inteiros são definidos por 3 tipos que são: int, long e short. O int é comumnente usado no geral para números considerados razoavéis. O long é utilizado para números longos e o short para números menores como seus nomes sugerem.

Todos os 3 tipos acima descritos contam com os tipos ***unsigned*** que significa um tipo sem sinal. Então enquanto o int aceita valores negativos, o uint aceita apenas valores positivos. O mesmo acontece com o long, ulong, short e ushort. Em um exemplo mais prático, poderiamos atribuir um valor negativo a um destes tipos mas não para um unsigned, desta forma:

```csharp
short saldo = 10; // OK
ushort saldoNegativo = -10; // ERROR
```

Acima vemos um valor sendo atribuido corretamente para a variável saldo na primeira linha como definido no comentário. já na segunda linha, ao tentar atribuir m valor negativo ao tipo unsigned temos o comentário indicando que é uma atribuição incorreta.

Os tipos unsigned ocupam o mesmo tamanho de memória que seus tipos padrões. Sendo assim o int e uint ocupam 32 bits, o short e ushort ocupam 16 bits de memória e o long e ulong ocupam 64 bits de memória.

<div id='numreais'></div>

## Números reais
Em relação a números reais, ou popularmente ditos "números quebrados", o C# disponibiliza também 3 tipos, são eles o float que aloca 32 bits de memória, o double que aloca  64 bits e o decimal que aloca 128 bits de memória. Estes tipos não contam com os tipos unsigned e por padrão podem possuem assimilação a valores positivos e negativos.

Podemos tomar como exemplo de atribuição de um valor para cada um destes tipos o seguinte código:
```csharp
double salarioA = 3.000;
float salarioB = 3.000f;
decimal salarioC = 3.000m;
```

Note que na declaração do valor do tipo `double` atribuimos o valor normalmente enquanto para declarar o `float` foi necessário adicionar a letra "f" ao fim do valor e no `decimal` a letra "m" também no fim da declaração do valor. Por padrão o C# reconhece um valor com ponto como sendo double e por este motivo precisamos adicionar a letra no fim da declaração ao atribuir valor aos outros tipos.

*Todos os números em C# e .NET são tipos que iniciam com um valor 0.*

<div id='conimplicita'></div>

## Conversão implicita
Existem ocasiões onde precisamos fazer a conversão de valores de um tipo para outro. Por padrão o .NET tenta conversões implicitas quando solititamos e caso não seja possível, ele aponta um erro.

Na conversão implicita precisamos apenas associar um valor a outro da seguinte forma:
```csharp
flaot salarioA = 3.000f;
int salarioB = 3.000;

salarioA = salarioB; // conversão implícita
```

Os tipos de conversões possíveis são:

`sbyte`
- short, int, long, float, double, decimal.

`byte`
- short, ushort, int, uint, long, ulong, float, double, decimal

`short`
- int, long, float, double, decimal

`ushort`
- int, uint, long, ulong, float, double, decimal

`int`
- long, float, double, decimal

`uint`
- long, ulong, flaot, double, decimal

`long`
- float, double, decimal

`ulong`
- float, double, decimal

`char`
- ushort, int, uint, long, ulong, float, double, decimal

`float`
- double

<div id='conexplicita'></div>

## Conversão explicita
Enquanto para uma conversão implicita precisamos apenas associar um valor a outro. Na conversão explicita temos a obrigação de informar o tipo. Isso ocorre principalmente quando os tipos de dados não são compatíveis. Então informamos antes do valor, entre aspas o tipo do valor. Desta forma:

```csharp
int valorA = 52;
uint valorB = (uint)inteiroA; // conversão explicita
```

*Caso a variável "valorA" for um valor negativo, acontecerá um erro em tempo de compilação já que o tipo uint não aceita sinais.*

<div id='funparse'></div>

## Função Parse
A função `Parse` é uma extensão dos tipos de dados que pode ser usada em tentativas de conversão de um tipo string (cadeia de caracteres) para um número, como no trecho de código abaixo:

```csharp
int valor = int.Parse("25");
```

*Caso não consiga converter, o `Parse` apontará um erro em tempo de execução.*

<div id='classeconvert'></div>

## Classe Convert
A classe `Convert` nos permite tentar converter vários tipos de dados, diferente do Parse que aceita apenas strings. Podendo acontecer da seguinte forma:

```csharp
int valor = Convert.ToInt32("25");
```

<div id='oparitmeticos'></div>

## Operadores aritméticos
Para executar operações com números em C# e .NET temos os operadores aritméticos:

- Soma: `+`
- Subtração: `-`
- Multiplicação: `*`
- Divisão: `/`

Assim como na matemática o C# executa divisão e multiplicação antes de soma e subtração, se for necessário podemos utilizar os parenteses para mudar esta ordem.

```csharp
int a = 2 + 2 * 2; // 6
int b = 2 + (2 * 2) // 6 - mesmo resultado
int c = (2 + 2) * 2; // 8 - executou a soma antes
```

Os operadores aritméticos do C# aceitam os tipos short, int, float e decimal que já vimos acima. Caso os valores sejam incompatíveis haverá arredondamento como exemplificado abaixo:

```csharp
int soma = 25 + 22; // 47
int subtracao = 25 - 22; // 3
int multiplicacao = 25 * 22 // 550
int divisao = 22 / 5; // 4 - arredondado
```

<div id='opatribuicao'></div>

## Operadores de atribuição
As operações de atribuição são usados toda vez que precisamos atribuir valor a uma variável. Podemos utilizar o `=` que é o operador de atribuição de valor e o valor a ser adicionado da seguinte forma:

```csharp
int a = 0; // valor padrão
a += 5; // mesmo que x = x + 5;
```

*Veja que fizemos de forma simplificada a operação descrita no comentário.*

Caso queiramos incrementar apenas 1 valor a mais ou a menos podemos fazer da seguinte forma:

```csharp
int a = 0; // valor padrão
a++; // incrementa 1 ao valor original
a--; // remove 1 do valor original
```

<div id='opcomparacao'></div>

## Operadores de comparação
Para fazer comparações entre números em C# e .NET usamos os seguintes operadores:

- Igual: `==`
- Diferente:` !=`
- Maior que: `>`
- Menor que: `<`
- Maior ou igual a: `>=`
- Menor ou igual a: `<=`

*Lembre que `=` atribui valor enquanto `==` compara valores.*

As comparações retornam valor booleano (true, false), podemos utiliza-las da seguinte forma:

```csharp
int a = 25;
a == 0; // False
a != 0; // True
a > 0; // True
a < 0; // False
x <= 0; // False
x >= 0; // True
```

<div id='referencias'></div>

## Referências
## [Curso Fundamentos do C#](https://balta.io/cursos/fundamentos-csharp)
