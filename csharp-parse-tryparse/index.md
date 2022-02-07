Parse e TryParse são dois métodos que temos para converter, ou tentar converter, uma string para outro de forma explícita no C#.

## Parse e TryParse
Os métodos `Parse` e `TryParse` nos permitem converter ou tentar converter uma `string` para um outro tipo. No caso, todos os tipos **built-in** do .NET possuem estes métodos.

## Utilizando o Parse
O `Parse` funciona como uma extensão aos métodos **built-in**, onde podemos invocar o método informando uma `string`, como por exemplo `int.Parse("123")`.

Abaixo podemos ver três exemplos de conversões de `string` para outros tipos **built-in** utilizando `Parse`, incluindo um `DateTime` que é uma estrutura, um tipo mais complexo.

```csharp
var valor = int.Parse("123");
var valor2 = bool.Parse("true");
var valor3 = DateTime.Parse("01/01/2022");

Console.WriteLine(valor);
Console.WriteLine(valor2);
Console.WriteLine(valor3);
```
Como resultado da execução deste programa temos o seguinte texto sendo impresso na tela.

```
123
True
1/1/2022 12:00:00 AM
```

## IFormatProvider
Continuando o exemplo anterior, temos um `DateTime` e estamos passando o valor `01/01/2022` para o mesmo. Este valor foi proposital para não causar nenhum erro.

Agora vamos considerar uma nova data, `28/10/2021`, onde temos o formato `dd/MM/yyyy`, um formato utilizado aqui no Brasil, mas diferente dos EUA (Formato padrão do .NET) que utiliza `MM/dd/yyyy`.

```
Unhandled exception. System.FormatException: String '28/10/2021' was not recognized as a valid DateTime.
   at System.DateTime.Parse(String s)
   at Program.<Main>$(String[] args) in C:\dev\CsharpTryParse\Program.cs:line 8
```

Neste caso, a conversão falharia, pois `28/10/2021` não é uma data válida nos EUA, o correto seria `10/28/2021`, mas felizmente conseguimos contornar isto utilizando uma cultura.

```csharp
var culture = new CultureInfo("pt-BR");
var valor3 = DateTime.Parse("28/10/2021", culture);
```
Note que agora temos um objeto do tipo `CultureInfo`, que define a cultura como `pt-BR` (Português do Brasil) e quando executamos o `Parse`, informamos também a cultura, resultando no sucesso da conversão.

### NumberStyles
Outro modificador que podemos utilizar (Existem vários) é o `NumberStyles`, que nos permite trabalhar nas pontuações dos números no caso de inteiros.


```csharp
Console.WriteLine(int.Parse("1234", NumberStyles.None)); 
// 1234

Console.WriteLine(int.Parse("12123.0", NumberStyles.AllowDecimalPoint)); 
// 12123

Console.WriteLine(int.Parse("123,456", NumberStyles.AllowThousands)); 
// 12356
```
Ainda podemos adicionar a cultura aos números, junto aos `NumberStyles` para trabalhar na relação "ponto ou vírgula` dos valores.

## TryParse
Como pudemos notar nos exemplos anteriores, ao usar o `Parse` temos uma tentativa de conversão de uma `string` para outro tipo, o que nem sempre ocorrerá com sucesso.

No caso demonstrado com o `DateTime` mesmo, tivemos um `FormatException` pois não informamos a data no formato correto.

```
Unhandled exception. System.FormatException: String '28/10/2021' was not recognized as a valid DateTime.
   at System.DateTime.Parse(String s)
   at Program.<Main>$(String[] args) in C:\dev\CsharpTryParse\Program.cs:line 8
```

Para evitar este tipo de situação, temos o `TryParse` que resulta em um booleano informando se a conversão foi possível e caso verdade, o valor da conversão.

```csharp
var deuCerto = int.TryParse("45689", out var valor);

Console.WriteLine(deuCerto);
Console.WriteLine(valor);
```

Note que o `TryParse` faz uso do `out var valor`, então o resultado direto da execução dele é um `bool` e não o valor convertido em sí. Este valor pode ser extraído da variável definida no `out`.

Como resultado de uma conversão de sucesso, temos o seguinte texto sendo impresso no terminal.

```
True
45689
```

Agora vamos ao segundo cenário, onde o `TryParse` mais se destaca. Aqui temos uma tentativa de conversão que vai falhar, afinal estamos tentando converter `"asdaasd"` para um `int`.

```csharp
var deuCerto = int.TryParse("asdaasd", out var valor);

Console.WriteLine(deuCerto);
Console.WriteLine(valor);
```

Neste caso, não teremos uma exceção e sim os valores `false` para o resultado da execução e `0` para o `int`. Isto ocorre pois o `int` é um tipo de valor e seu valor inicial é sempre 0.

```
False
0
```

## Conclusão
Fazer conversões de valores no C# não é uma tarefa fácil e temos vários recursos para isto, incluindo o Parse e TryParse como vimos neste artigo.