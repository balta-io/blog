Neste artigo vamos conferir como gerar arquivos CSV (Excel) utilizando C# e Implicit Operators de forma simples e direta.

## O que são arquivos CSV?
CSV é a sigla para Comma Separated Values, ou valores separados por vírgula. Estes tipos de dados são comuns de serem encontrados e podem ser facilmente editados pelo Excel.

Os arquivos CSV nada mais são do que arquivos textos, com conteúdo em um formato específico e a extensão `.csv`.

Sempre que falamos em exportar para o Excel, CSV é minha primeira opção, pela facilidade que temos em manipular este tipo de arquivo.

### Exemplo
```
nome,telefone,datanasc
Bruce Wayne,5599999999999,12/08/1984
Bruce Banner,5599999999997,11/08/1979
Peter Parker,5599999999996,12/07/1992
Tony Stark,5599999999995,10/06/1988
```

## Criando o modelo
Para exemplificar bem, vamos tomar como base um modelo ou entidade, uma classe que vem do nosso banco de dados ou que de qualquer outra forma exista no nosso sistema.

```csharp
public class Hero
{
    public string Name { get; set; }
    public string Phone { get; set; }
    public DateTime BirthDate { get; set; }
}
```

Com o modelo definido, tudo que precisamos fazer é converter seus valores para uma `string`, separando-os por vírgula.

> **IMPORTANTE:** A ordem dos campos deve seguir o cabeçalho do arquivo (Primeira linha). Então se no cabeçalho definimos nome,telefone,datanasc, temos que retornar os dados nesta ordem.

### Utilizando Implicit Operators
Nos artigos anteriores, falamos sobre [Implicit Operators](https://balta.io/blog/csharp-implicit-operators) e como eles podem facilitar a conversão de tipos no C#. Vamos utilizá-los aqui para gerar o conteúdo do arquivo.

Nosso primeiro método é bem simples, basta retornar uma `string` com os valores (Nome, telefone e data de nascimento -- nesta ordem) separados por vírgula.

```csharp
public static implicit operator string(Hero hero) 
    => $"{hero.Name},{hero.Phone},{hero.BirthDate.ToString("yyyy-MM-dd")}";
```

Já a conversão de um item do **CSV** para o objeto `Hero` é um pouco mais complicada, pois neste caso temos uma **Data**, que não poderá ser convertida de uma `string` implicitamente.

Existem várias soluções aqui, mas os **Extension Methods** podem ser um ótimo caminho. Os métodos de extensão podem ser utilizados para adicionar funcionalidades a outras classes, incluindo as nativas do .NET.

Neste caso por exemplo, podemos criar um método que adiciona a funcionalidade de converter uma `string` para um `DateTime`.

```csharp
public static class StringExtension
{
    public static DateTime ToDateTime(this string value)
    {
        var data = value.Split("-");
        return new DateTime(
            int.Parse(data[0]), 
            int.Parse(data[1]), 
            int.Parse(data[2]));
    }
}
```
> **IMPORTANTE:** Como o intuito aqui é só exemplo, não vou me prender aos detalhes como tratamento de erro para as conversões de string para inteiro.

Com a extensão criada, agora temos o método `ToDateTime()` em todas as `strings` do nosso projeto. Desta forma, podemos seguir com a conversão de um item do CSV para nosso objeto.

```csharp
public static implicit operator Hero(string line)
{
    var data = line.Split(",");
    return new Hero(
        data[0], 
        data[1], 
        data[2].ToDateTime());
}
```

Novamente frisando que o intuito aqui é apenas uma demonstração, tem vários itens a serem tratados nas conversões de dados. O resultado final da classe `Hero` fica assim.

```csharp
public class Hero
{
    public Hero(string name, string phone, DateTime birthDate)
    {
        Name = name;
        Phone = phone;
        BirthDate = birthDate;
    }

    public string Name { get; set; }
    public string Phone { get; set; }
    public DateTime BirthDate { get; set; }
    
    public static implicit operator string(Hero hero) 
        => $"{hero.Name},{hero.Phone},{hero.BirthDate.ToString("yyyy-MM-dd")}";

    public static implicit operator Hero(string line)
    {
        var data = line.Split(",");
        return new Hero(
            data[0], 
            data[1], 
            data[2].ToDateTime());
    }
}
```

## Gerando CSV
Com as conversões finalizadas, nosso trabalho aqui é fácil, vamos fazer uso a [leitura](https://balta.io/blog/lendo-arquivos-em-csharp) e [escrita](https://balta.io/blog/csharp-manipulacao-arquivos) de arquivos que o .NET fornece para gerar nosso `.csv`.

Podemos começar criando nossa lista de herois, do tipo `Hero` como se estivessemos recebendo estes valores do banco por exemplo.

```csharp
var heroes = new List<Hero>
{
    new("Bruce Wayne", "5599999999996", DateTime.Now.AddYears(-40)),
    new("Bruce Banner", "5599999999997", DateTime.Now.AddYears(-38)),
    new("Peter Parker", "5599999999996", DateTime.Now.AddYears(-18)),
    new("Tony Stark", "5599999999996", DateTime.Now.AddYears(-33)),
};
```

Em seguida podemos utilizar o método `File.WriteAllLines` do .NET, que escreve uma lista de `string` no formato de linhas. O único problema aqui é que não temos uma lista de `string` e sim de `Hero`, mas podemos contornar isto facilmente com **LINQ**.

O **LINQ** possui um método chamado `Select` que permite transformar uma lista do tipo `A` em uma lista do tipo `B`, e como já temos a conversão de um tipo `Hero` para `string`, fica tudo mais fácil.

```csharp
heroes.Select(hero => (string) hero)
```

Note que precisamos explicitar o tipo da conversão neste caso como `(string)` pois o **LINQ** não é capaz de inferir.

Como resultado final, temos o seguinte código, que irá criar um arquivo chamado `heroes.csv` com o conteúdo sendo nossa lista de heróis, um por linha.

```csharp
File.WriteAllLines("heroes.csv", heroes.Select(hero => (string) hero).ToList());
```

Executando nossa aplicação, teremos o CSV gerado com o seguinte conteúdo.

```
Bruce Wayne,5599999999996,1982-01-07
Bruce Banner,5599999999997,1984-01-07
Peter Parker,5599999999996,2004-01-07
Tony Stark,5599999999996,1989-01-07
```

## Lendo um CSV
Nossa missão de leitura de um CSV é bem mais simples, já que temos o método `File.ReadAllLines` do .NET que retorna uma lista de `string`.

Com a lista de `string` em mãos, já temos o operador implícito que converte o tipo para um `Hero`, então está mamão com açucar!

```csharp
var data = File.ReadLines("heroes.csv");
foreach (var line in data)
{
    Hero hero = line;
    Console.WriteLine(hero.Name);
    Console.WriteLine(hero.Phone);
    Console.WriteLine(hero.BirthDate);
    Console.WriteLine("--");
}
```
Como resultado da execução do programa, temos a conversão do arquivo `.csv` para uma lista de `Hero` e a exibição na tela como informamos.

```
Bruce Wayne
5599999999996
01/07/1982 00:00:00
--
Bruce Banner
5599999999997
01/07/1984 00:00:00
--
Peter Parker
5599999999996
01/07/2004 00:00:00
--
Tony Stark
5599999999996
01/07/1989 00:00:00
--
```

## Conclusão
Os arquivos CSV são uma mão na roda! Eles podem ser abertos e manipulados por dentro do Excel e são fáceis de trabalhar por serem somente texto.

Precisamos apenas tomar cuidado com seu leiaute, o que é fácil de se fazer quando temos uma tipagem igual no C#.


<div role="main" id="blog-s1-dotnet-134e3db1eea6c9829db1"></div>
<script type="text/javascript" src="https://d335luupugsy2.cloudfront.net/js/rdstation-forms/stable/rdstation-forms.min.js"></script>
<script type="text/javascript"> new RDStationForms('blog-s1-dotnet-134e3db1eea6c9829db1', 'UA-48664517-12').createForm();</script>