Extensiom Methods ou métodos de extensão são a forma que temos de adicionar funcionalidades extras a qualquer tipo no C#.

## Extension Methods
No [artigo anterior onde falei sobre como gerar arquivos CSV no C#](https://balta.io/blog/csharp-gerar-csv) nós passamos por uma situação que precisou de um **Extension Method** para converter uma `string` para um `DateTime`.

Na verdade passamos por estes cenários quase que o tempo todo, e enquanto os [**Implicit Operators**](https://balta.io/blog/csharp-implicit-operators) nos permitem adicionar conversões customizadas dentro de um objeto, os **Extension Methods** vão além.

Os **Extension Methods** no C# nada mais são do que classes estáticas que podem viver fora das suas classes e ainda assim adicionar funcionalidade a mesma. Isto permite dentre outras coisas, uma melhor organização do código.

Vamos tomar como exemplo o cenário onde precisamos converter uma `string` em um determinado formato para um `DateTime`. Embora haja o `Cast`, não seria mais fácil ter um método `ToDateTime` em **QUALQUER** `string` do sistema que tenta fazer esta conversão?

Este é um cenário que os **Extension Methods** podem nos ajudar muito. Não podemos modificar o código **interno** do .NET, mas podemos extendê-lo! Podemos criar um método adicional na `string` do .NET.

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

Como podemos notar, precisamos de uma classe `static` com um método estático que faz o que desejamos. Neste método, tudo que precisamos fazer é adicionar a palavra reservada `this` antes do tipo de entrada do parâmetro.

> **IMPORTANTE:** Se está com dúvidas sobre como funcionam as classes estáticas no .NET, dá uma revisada no nosso curso de C#/OOP da [**Carreira Backend .NET**](https://balta.io/carreiras/desenvolvedor-backend-dotnet)

Pronto, isto é tudo que precisamos para poder usar nosso método `ToDateTime` em qualquer `string` do sistema.

```csharp
var minhaData = "2022-01-10";
var data = minhaData.ToDateTime(); // Converte a string para um DateTime
```

Este código foi escrito apenas para intuito de demonstrações, não há tratamento de erro nem otimização do mesmo.

## Extendendo objetos
No artigo de **Implicit Operators** eu mostrei uma conversão de um tipo de objeto para outro, algo que pode ampliar bastante seu leque e até dispensar o uso de libs como o Automapper em determinados cenários.

Porém, existem alguns casos onde não sabemos para qual ou quais objetos vamos converter, ou pior, nossos objetos de conversão ficam em outro assembly.

No caso do balta (Este site que você está acessando) mesmo, nós temos uma lib com todas as entidades, que refletem os modelos do banco de dados. Esta lib fica em um pacote chamado `Balta.Core`, distribuido via GitHub Packages.

Nesta lib por exemplo, temos uma entidade chamada `Course` que representa um curso, com as seguintes propriedades.

```csharp
public class Course : Entity
{
    public string Title { get; set; }
    public string Summary { get; set; }
    public string Url { get; set; }
    public EContentLevel Level { get; set; }
    public int DurationInMinutes { get; set; }
    public DateTime CreateDate { get; set; }
    public DateTime LastUpdateDate { get; set; } = DateTime.UtcNow;
    public bool Active { get; set; } = true;
    public bool Free { get; set; } = false;
    public bool Featured { get; set; } = false;
    public Guid AuthorId { get; set; }
    public Author Author { get; set; }
    public Guid CategoryId { get; set; }
    public Category Category { get; set; }
    public string Tags { get; set; }
}
```

Isto faz todo sentido para o modelo, para o reflexo que temos do objeto em relação ao banco de dados, mas não faz sentido algum para as telas, as Views, o que o usuário vê. Na verdade o usuário enxerga uma porcentagem bem menor de informações.

Para isto funcionar, nós temos as **ViewModels** ou **DTOs** que são modelos especiais que servem como transporte de dados de um lugar para outro. Neste caso da camada mais abaixo para a camada de apresentação, o ASP.NET.

```csharp
public class CourseCardViewModel
{
    public string Title { get; set; }
    public string Url { get; set; }
    public EContentLevel Level { get; set; }
    public int DurationInMinutes { get; set; }
}
```

Os **Extension Methods** se encaixam exatamente nesta parte, onde temos os uma lista de cursos (`List<Course>`) que vem do cache e precisamos convertê-la para uma `List<CourseCardViewModel>`.

Eu já utilizei bastante a abordagem do **método construtor** onde criava uma sobrecarga que recebia um `Course` e fazia a conversão ali mesmo. Funciona bem, mas a medida que temos conversões de diversos objetos, o código pode ficar bem extenso.

```csharp
public class CourseCardViewModel
{
    public CourseCardViewModel(Course course)
    {
        // Preenche as propriedades
    }

    public string Title { get; set; }
    public string Url { get; set; }
    public EContentLevel Level { get; set; }
    public int DurationInMinutes { get; set; }
}
```

### Convertendo a lista de cursos
O exemplo abaixo converte uma lista de `Course` em uma lista de `CourseViewModel`.
```csharp
courses.Select(x => new CourseCardViewModel(x));
```

## Aplicando os Extension Methods
Neste caso então, optei por ter uma pasta chamada `Extensions` onde organizo tudo por **features** assim como faço no resto do projeto, e desta forma temos os métodos de extensão ali dentro.

```csharp
public static class CourseExtensions
{
    public static CourseCardViewModel ToCard(Course course)
    {
        // Gera um novo ViewModel e retorna
    }
}
```

Agora temos a liberdade de criarmos quantas extensões para o `Course` quisermos sem modificar seu código ou o da **ViewModel**, e seu uso fica simples.

### Convertendo a lista de cursos
O exemplo abaixo converte uma lista de `Course` em uma lista de `CourseViewModel`.
```csharp
courses.Select(x.ToCard());
```

## Conclusão
Os métodos de extensão nos permitem adicionar funcionalidades a qualquer tipo, Built-in ou complexo no C#, de forma simples, fácil e organizada, separando este comportamento adicional da classe base.
