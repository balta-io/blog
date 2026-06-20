O C# conta com um recurso parecido com o do JavaScript onde podemos desconstruir um objeto e obter de forma fácil e simples suas propriedades.

## Deconstruct
O primeiro passo é entender o que é o método `Deconstruct` que não deve ser confundido com o método destrutor do objeto (`~Metodo()`). O nome **deconstruct** significa desconstruir, que é exatamente o que queremos aqui.

Nos artigos anteriores [**<u>falamos sobre métodos de extensão (Extension Methods)</u>**](https://balta.io/blog/csharp-extension-methods) e como podemos extender tipos **built-in** do .NET de forma simples e fácil. O `Deconstruct` funcionará de forma similar.

Na verdade o `Deconstruct` deve ser inserido dentro de uma classe de extensão, como fazemos com os **Extension Methods**, então vamos começar criando uma classe estática que é obrigatória neste caso.

```csharp

public static class DateTimeExtensions
{
    // ...
}
```
### Implementação
A diferença entre um método de extensão comum e um `Deconstruct` é o nome deste. Neste caso, o nome **obrigatóriamente** deve ser `Deconstruct`, como mostrado abaixo.

```csharp
public static class DateTimeExtensions
{
    public static void Deconstruct()
    {
        // ...
    }
}
```

Como pudemos notar, estamos trabalhando em uma extensão para o `DateTime`, então vamos seguir com o método como se de fato estivessemos escrevendo um **Extension Method**, que exige um parâmetro de entrada com a palavra `this` na frente.

```csharp
public static class DateTimeExtensions
{
    public static void Deconstruct(this DateTime dateTime)
    {
        // ...
    }
}
```

Além do nome `Deconstruct` obrigatório, temos que retornar um valor neste método, e aqui vem a parte legal da coisa. O ato de desconstruir um objeto, remete ao desmembramento do mesmo, em variáveis.

Nos artigos anteriores, [**<u>vimos como trabalhar com Tuplas</u>**](https://balta.io/blog/csharp-tuple), que serão utilizadas aqui. Então nada melhor que desmembrar nossa Data/Hora em uma tupla que retorna ano, mês e dia de forma separada.

```csharp
public static class DateTimeExtensions
{
    public static void Deconstruct(
        this DateTime dateTime, 
        out int year, out int month, out int day)
    {
        // ...
    }
}
```

Note que fizemos uso da palavra reservada `out`, uma vez que o método `Deconstruct` é do tipo `void` e não tem um retorno específico. Neste caso, o `out` vai gerar três variáveis para nós, `year`, `month` e `day`, todas como `int`.

Agora tudo que precisamos fazer é assimilar as variáveis aos valores do `DateTime` que recebemos como entrada. Estes valores serão automaticamente expostos de forma desconstruída.

```csharp
public static class DateTimeExtensions
{
    public static void Deconstruct(
        this DateTime dateTime, 
        out int year, out int month, out int day)
    {
        year = dateTime.Year;
        month = dateTime.Month;
        day = dateTime.Day;
    }
}
```


### Melhorando o código
Antes de prosseguir, podemos melhorar nosso código utilizando as Tuplas. Aqui podemos criar um conjunto com os três valores que utilizamos no `out` e em seguida fazer a assimilação em grupo dos valores do `DateTime` para estes.

```csharp
public static class DateTimeExtensions
{
    public static void Deconstruct(
        this DateTime dateTime, 
        out int year, out int month, out int day)
    {
        (year, month, day) = (dateTime.Year, dateTime.Month, dateTime.Day);
    }
}
```
E claro, para melhorar ainda mais nosso código, como temos apenas uma linha de código no método `Deconstruct`, podemos torná-lo um **Expression Body**, usando `=>` e retornando o resultado diretamente.

```csharp
public static class DateTimeExtensions
{
    public static void Deconstruct(
        this DateTime dateTime, 
        out int year, out int month, out int day)
        => (year, month, day) = (dateTime.Year, dateTime.Month, dateTime.Day);
}
```

## Utilizando o Deconstruct
Para utilizar o `Deconstruct`, basta assimilar uma **Data/Hora** a uma variável que seja uma Tupla esperando três valores (Assim como definimos nos `out`).

```csharp
var (y, m, d) = DateTime.Now;

Console.WriteLine(y);
Console.WriteLine(m);
Console.WriteLine(d);
```

Note que o nome das variáveis não importam, tanto que definimos `y`, `m` e `d` para elas. No entando, elas serão assimiladas conforme a ordem dos `out`, que no nosso caso é `year` (Ano), `month` (Mês) e `day` (Dia).

Desta forma, não adianta inverter a ordem na hora de receber os valores (Como mostrado abaixo), você estará mudando apenas o nome das variáveis (E confundindo outras pessoas). A ordem sempre será ANO-MÊS-DIA conforme definida nos `out` do `Deconstruct`.

```csharp
var (day, year, month) = DateTime.Now;

Console.WriteLine(day);
Console.WriteLine(year);
Console.WriteLine(month);
```

Particularmente gosto de criar nomes mais intuitivos, mesmo que maiores, então tento nomear as variáveis das tuplas de acordo com os `out`, tendo como resultado final o código abaixo.

```csharp
var (year, month, day) = DateTime.Now;

Console.WriteLine(year);
Console.WriteLine(month);
Console.WriteLine(day);
```

## Conclusão
Como pudemos notar, o C# possui inúmeras funcionalidades escondidas e o `Deconstruct` é uma delas, que pode agregar muito valor e facilitar muito nosso código.