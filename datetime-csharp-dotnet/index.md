Tabelas de conteúdo 
 1. [Começando com DateTime?](#comecandocomdatetime)
 2. [Obtendo data e hora atual com DateTime](#obtendo-data-e-hora-atual-com-datetime)
 3. [Formatando um DateTime](#formatando-um-datetime)
 4. [Manipulação de datas com DateTime](#manipulacao-de-datas-com-datetime)
 5. [Cultures e TimeZones UTC no DateTime](#cultures-e-timezones-utc-no-datetime)
 6. [Referências](#referencias)

*******

<div id='comecandocomdatetime'></div> 

## Começando com DateTime
O DateTime no C# é um tipo de dados struct. Com isto sabemos que ele é composto por outros tipos e métodos, além de ser iniciado com um valor padrão.

Para criar um DateTime fazemos da segunte forma:

```csharp
var date = new DateTime();
```

Por serem tipos de valor, os DateTimes não são nulos e por padrão sempre iniciam com um valor como foi citado antes. Então vamos vericiar qual o valor inicial da nossa variável assim:

```csharp
Console.WriteLine(date);
// 1/1/0001 12:00:00 AM
```

Estes são os valores iniciais de um DateTime. Precisamos entender também que o formato é retornado de acordo com a cultura do sistema sob o qual o código foi executado, a menos que uma configuração seja aplicada para modificar este comportamento.

<div id="obtendo-data-e-hora-atual-com-datetime"></div>

## Obtendo data e hora atual com DateTime
Agora que sabemos como criar a nossa váriavel, temos a opção de atribuir e exibir a data atual. Fazemos isto utilizando a propriedade `DateTime.Now`.

Para exibir a data e hora local:
```csharp
var date = DateTime.Now();
Console.WriteLine(date);
// 6/17/2021 8:10:09 AM
```

Para exibir a data e hora global UTC (Coordinated universal time ou tempo universal coordenado):
```csharp
var date = DateTime.UtcNow();
Console.WriteLine(date);
// 6/17/2021 11:10:09 AM
```

Note que ele retornou corretamente a data e hora do momento em que o código foi executado, porém exibiu o mês antes do dia e o horário em formato AM/PM. Este padrão é chamado de cultura e entenderemos ele mais a frente.

<div id="formatando-um-datetime"></div>

## Formatando um DateTime

No C# o DateTime formata os valores com base no sistema sob o qual o código está sendo executado, caso necessário você pode modificar este comportamento através de configurações personalizadas no seu projeto.

Para atribuir valores a nossa variável DateTime basta passar os valores como parâmetro:

```csharp
var date = new DateTime(2020, 07, 02, 22, 59, 59);
// 7/2/2020 10:59:59 PM
```

*Acima seguimos os parâmetros (ano, mês, dia, hora, minutos, segundos) para outros paramêtros culte a documentação dos **Construtores DateTime** (link no final do artigo).*

Nos exemplos anteriores você viu como exibir a data. Agora Veremos algumas formas de exibição diferentes.

Lembra quando foi dito antes que o DateTime é um struct e contém outros tipos e métodos? Podemos explorar a estrutura para obter por exemplo:

O dia da semana em que a data acima cairá:
```csharp
Console.WriteLine(date.DayOfWeek);
// Thursday
```

Valor avulsos:
```csharp
Console.WriteLine(date.Month);
// 7
```
*Recomendamos que antes de desenvolver um novo parâmetro/método, verifique a existência dele na documentação do DateTime (link no final do artigo).*

Podemos também utilizar alguns caracteres ("`Y`" para ano, "`M`" para mês, "`d`" para dia, "`H`" para hora, "`m`" para minutos, "`s`" para segundos, "`f`" para fração de segundos e "`z`" para a timezone) para formatar nosso DateTime através da interpolação de strings:

```csharp
var formattedDate = String.Format("{0:dd/MM/yyyy HH:mm:ss ff z}", date);
// 02/07/2020 22:59:59 00 -3
```

Existem outras formatações como o ERC (Eurepean research council):
```csharp
var formattedDate = String.Format("{0:r}", date);
// Thu, 02 Jul 2020 22:59:59 GMT
```

E o Sortible:
```csharp
var formattedDate = String.Format("{0:s}", date);
// 2020-07-02T22:59:59
```

<div id="manipulacao-de-datas-com-datetime"></div>

## Manipulação de datas com DateTime

Até aqui aprendemos a definir e exibir de várias formas as datas e horas. Em situações em que precisamos calcular datas e horas nós podemos utilizar alguns métodos já existentes no DateTime.

Para atribuir valores ao nosso DateTime basta passar os valores como parâmetro:
```csharp
var date = DateTime.Now();
date.AddDays(2);
// 6/19/2021 11:10:09 AM
```

Para remover valores do nosso DateTime:
```csharp
var date = DateTime.Now();
Console.WriteLine(date.AddDays(-20));
// 5/30/2020 11:10:09 AM
```

Podemos ver que ao retirar uma quantidade de dias maior que a data atual ele lidou com a regressão do mês. Usar o método existente é a forma correta de utilização da ferramenta.

<div id="cultures-e-timezones-utc-no-datetime"></div>

## Cultures e TimeZones UTC no DateTime

Como sabemos, os horários são diferentes de acordo com a região do planeta em que estamos. Dentro do C# lidamos com estas diferenças através da classe `TimeZoneInfo`.

Os formatos de data e hora utilizados por certas regiões são chamados de culturas e lidamos com eles dentro do C# através da classe `CultureInfo`.

No início citamos que o retorno de nossa data não estava no padrão esperado (Brasileiro), então este é o momento para corrigir. A estrutura para isto está dentro do `System` e podemos passar as informações necess[arias] por parâmetro dentro do método `DateTime.UtcNow()`.

Logo em seguida precisamos converter o resultado para uma `string` através do método `ToString()` e dentro deste método passamos como parâmetro o método `CreateSpecificCulture`. Este recebe como parâmetro a cultura que precisamos. Ficando assim:

```csharp
using System;
using System.Globalization;

Console.WriteLine(DateTime.UtcNow.ToString(CultureInfo.CreateSpecificCulture("pt-BR")));
```
*Para ver a lista completa de culturas recomendamos que visite o [codedigest](http://www.codedigest.com/CodeDigest/207-Get-All-Language-Country-Code-List-for-all-Culture-in-C---ASP-Net.aspx).*

## [Curso Fundamentos do C#](https://beta.balta.io/cursos/fundamentos-csharp)

<div id="referencias"></div>

## Referências
[Structs](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/language-specification/structs) | [DateTime](https://docs.microsoft.com/en-us/dotnet/api/system.datetime?view=net-5.0) | [DateTime constructors](https://docs.microsoft.com/en-us/dotnet/api/system.datetime.-ctor?view=net-5.0) | [TimeZoneInfo](https://docs.microsoft.com/en-us/dotnet/api/system.timezoneinfo?view=net-5.0) | [CultureInfo](https://docs.microsoft.com/en-us/dotnet/api/system.globalization.cultureinfo?view=net-5.0) | [System](https://docs.microsoft.com/en-us/dotnet/api/system?view=net-5.0) | [Strings](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/strings/) | [ToString](https://docs.microsoft.com/en-us/dotnet/api/system.object.tostring?view=net-5.0) | [CreateSpecificCulture](https://docs.microsoft.com/en-us/dotnet/api/system.globalization.cultureinfo.createspecificculture?view=net-5.0)
