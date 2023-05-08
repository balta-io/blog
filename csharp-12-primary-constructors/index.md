O C# 12 está previsto para ser lançado em Novembro de 2023, junto com o .NET 8, mas já tivemos uma prévia das suas novidades no Preview 4 do .NET 8 e vamos conferir aqui.

## Antes de começar

É importante salientar que este recuso está disponível no .NET 8 Preview 3, ou sejá é apenas uma pré-visualização do que poderá ser este recurso.

Já vimos casos no passado, como o !! que foram removidos da versão final, então não se apegue muito aos novos recursos em sí até a versão final.

É importante frisar também que o .NET 8 só sai em Novembro de 2023, muita coisa pode mudar até lá e você precisa instalar a versão Preview 3 ou superior para testar estas funcionalidades.

### Como instalar o .NET 8

Você pode fazer o download do .NET 8 no site oficial da Microsoft, utilizando a URL abaixo.

🔗 [https://dotnet.microsoft.com/en-us/download/dotnet/8.0](https://dotnet.microsoft.com/en-us/download/dotnet/8.0)

### Verificando a versão instalada

Com o .NET instalado, basta fechar seus terminais e abrir novamente, seguido pela execução do comando abaixo para verificar a versão instalada.

```bash
dotnet --version
## 8.0.100-preview.3.23178.7
```

Além disso, o .NET 8 ainda mantém o C# 11 como linguagem padrão, então é preciso habilitar uma funcionalidade chamada **Language Preview** no arquivo `.csproj` do seu projeto.

Isto é feito pela configuração chamada **LangVersion** cujo temos que atribuir o valor `preview`, como mostrado abaixo.

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework>net8.0</TargetFramework> 👈 Garantir que o .NET está na V8
    <ImplicitUsings>enable</ImplicitUsings>
    <Nullable>enable</Nullable>
    <LangVersion>preview</LangVersion> 👈 Adiciona esta linha
  </PropertyGroup>

</Project>
```

> IMPORTANTE: Provavelmente sua IDE não vai reconhecer os comandos novos, identificando eles como possíveis erros.

## Construtores Primários

Se você já trabalha com C# e está atualizado sobre as melhorias na linguagem, provavelmente já utilizou o recurso de construtores primários nas `structs` e/ou `records`.

Porém, no C# 12 teremos esta novidade inclusa também nas classes. Isto mesmo, agora podemos simplificar muito a criação de classes utilizando construtores primários.

```csharp
// Exemplo de construtor primário
// em Records
public record Person(int Id, string Name);

// Exemplo de construtor primário
// em Structs
public struct Person(int Id, string Name);

// Exemplo de construtor primário
// em Classes (⭐️ novidade do C# 12)
public class Person(int Id, string Name);
```

## O que e um é um construtor?

Construtor ou Constructor em inglês é o nome dado a um método especial de uma classe, que é executado toda vez que um objeto com base nesta classe é criado e que obrigatóriamente deve ter o mesmo nome da classe.

Então, supondo que temos uma classe chamada `Name`, podemos ter um ***Construtor*** chamado `Name` nesta classe.

```csharp
public class Name
{
		public Name()
		{
			// Método Construtor
		}

    public string FirstName { get; set; }
    public string LastName { get; set; }
}
```

Desta forma, toda vez que instanciarmos um objeto a partir desta classe, o método `Name` será chamado. Note que ele não tem retorno e pode ou não conter parâmetros.

```csharp
var name = new Name();
// Neste momento o construtor é chamado

var name = new Name {
	FirstName = "André",
	LastName = "Baltieri",
};
// Outro exemplo
```

Sempre que criamos um construtor sem parâmetros, ele recebe o nome de ***Parameterless***, que significa exatamente **Sem Parâmetros** em inglês, porém, é plenamente possível criar um construtor com parâmetros.

```csharp
public class Name
{
		public Name(string firstName, string lastName)
		{
			FirstName = firstName;
			LastName = lastName;
		}

    public string FirstName { get; set; }
    public string LastName { get; set; }
}
```

O único detalhe é que uma vez definido um (E apenas um) construtor com parâmetros, somos obrigados a informar os valores já na instância do objeto.

```csharp
var name = new Name();
// ❌ ERRO - Precisa informar os valores

var name = new Name("André", "Baltieri");
// ✅ OK - Informou os valores
```

Por fim, podemos combinar construtores, tendo mais de um para nossas classes, incluindo um com e outro sem parâmetros.

```csharp
public class Name
{
		public Name() { }

		public Name(string firstName, string lastName)
		{
			FirstName = firstName;
			LastName = lastName;
		}

    public string FirstName { get; set; }
    public string LastName { get; set; }
}
```

No exemplo acima, temos uma sobrecarga de método, dando a possibilidade de informar ou não argumentos na chamada do método.

```csharp
var name = new Name();
// ✅ OK - Passa pelo Parameterless

var name = new Name("André", "Baltieri");
// ✅ OK - Informou os valores
```

É comum também, vermos construtores como o modificador `protected` ao invés do `public`. Isto ocorre por que o Entity Framework por exemplo, exige um construtor parameterless para instância de objetos.

```csharp
public DbSet<Product> Products { get; set; }
// ❌ - Vai falhar se Product não tiver um construtor parameterless
```

Entretanto, uma boa recomendação é explicitar no construtor da sua classe, quais propriedades são obrigatórias para mesma funcionar.

Para resolver esta situação, podemos utilizaro protected no construtor parameterless, afinal ele só será usado por uma classe proxy que derivará da classe base no EF.

```csharp
public class Name
{
		// 👇 Agora é protected
		protected Name() { } 

		public Name(string firstName, string lastName)
		{
			FirstName = firstName;
			LastName = lastName;
		}

    public string FirstName { get; set; }
    public string LastName { get; set; }
}
```

Desta forma podemos evitar que nossa classe seja instanciada sem valores for do seu escopo, mas ainda permitimos que classes filhas faça uso do construtor parameterless.

```csharp
var name = new Name();
// ❌ Erro - Não pode instanciar sem passar valores

var name = new Name("André", "Baltieri");
// ✅ OK - Informou os valores

public DbSet<Product> Products { get; set; }
// ✅ - Proxy das classes vão funcionar
```

Para finalizar, podemos fazer uso dos Optional Parameters, para ter um efeito parecido com os de cima.

```csharp
public class Measurement
{
	public Measurement(
		int width = 0,  // 👈 Parâmetro opcional, já possui um valor
		int height = 0) // 👈 Parâmetro opcional, já possui um valor
	{
  }

	public int Width { get; set; }
	public int Height { get; set; }
}
```

Isto torna a instância do objeto mais maleável, podendo ou não informar os valores definidos no construtor.

```csharp
// Inicia com os valores padrão
var measurement = new Measurement(); 

// Inicia com 10 para width e 0 para height
var measurement = new Measurement(10); 

// Inicia com 10 para width e 10 para height
var measurement = new Measurement(10, 10); 
```

De qualquer forma, as possibilidade são muitas, vai do que você precisa para o seu cenário e de como quer tratar a instância do seu objeto.

# O que são Primary Constructors?

Legal, mas supondo que eu tenha uma classe com duas propriedades apenas, porém, estas propriedades devem ser obrigatórias, meu código ficaria assim?

```csharp
public class Name
{
		public Name(string firstName, string lastName)
		{
			FirstName = firstName;
			LastName = lastName;
		}

    public string FirstName { get; set; }
    public string LastName { get; set; }
}
```

Pois é! Muito código para algo realmente simples, não acha? Inclusive, é sempre uma boa recomendação ver se os **records** ou **structs** podem fazer o trabalho pela sua classe.

```csharp
public record Name(string FirstName, string LastName);
public struct Name(string FirstName, string LastName);
```

Como podemos ver acima, tanto os records quanto os structs possuem um recurso chamado ***Primary Constructors*** ou construtores primários.

Este recurso permite que sequer precisemos abrir chaves para criação da classe, ou seja, tudo é definido na estrutura inicial.

```csharp
var name = new Name();
// ❌ ERRO - Precisa informar os valores

var name = new Name("André", "Baltieri");
// ✅ OK - Informou os valores
```

Então, um construtor primário, nada mais é do que a possibilidade de explicitar alguns valores obrigatórios já na definição da classe, simplificando o código.

Desta forma, no C# 12, teremos a inclusão dos ***Primary Constructors*** para classes, tornando a sintaxe ainda mais enxuta.

```csharp
// Podemos usar a sintaxe reduzida
public class Name(string FirstName, string LastName);

// Ou mesmo expandir e ter comportamento na classe
public class Name(string FirstName, string LastName)
{
    
}
```

Como resultado, temos um comportamento similar aos anteriores, onde somos obrigados a informar os valores na instância do objeto.

```csharp
var name = new Name();
// ❌ CS7036: There is no argument given that corresponds to the required parameter 'FirstName' of 'Name.Name(string, string)'

var name = new Name("André", "Baltieri");
✅ - OK
```

## Primary Constructors para classes

Existe uma diferença nos construtores primários para as classes em relação a mesma funcionalidade para records e structs.

No caso dos Recors e Struts, ao utilizar o recurso de construtor primário temos automaticamente uma propriedade pública com o mesmo nome, criada de forma automática.

```csharp
public record Name(string FirstName, string LastName);

var name = new Name("André", "Baltieri");
Console.WriteLine(name.FirstName);
Console.WriteLine(name.LastName);
```

Neste mesmo exemplo, ao alterar de record para class, mudamos o comportamento, visto que os parâmetros dos **Primary Constructors** são identificados como membros privados em uma classe.

> **IMPORTANTE**
Este comportamento pode ser alterado até a versão final do C# 12 que está prevista para Novembro de 2023.
> 

```csharp
//      👇 utilizando class ao invés de record
public class Name(string FirstName, string LastName);

var name = new Name("André", "Baltieri");
Console.WriteLine(name.FirstName); // ❌ FirstName não existe
Console.WriteLine(name.LastName); // ❌ LastName não existe
```

Para ter o mesmo comportamento de um `record` ou `struct`, precisamos exportar os argumentos, explicitando o acesso dos mesmos com o modificado `public`.

```csharp
public class Name(string firstName, string lastName)
{
    public string FirstName => firstName;
    public string LastName => lastName;
}
```

Desta forma, agora temos dois parâmetros de entrada, que são privados por padrão e consequentemente dois membros para saída, que são públicos.

```csharp
var name = new Name("André", "Baltieri");
Console.WriteLine(name.FirstName); // ✅
Console.WriteLine(name.LastName); // ✅
```

Note também que não há necessidade de nomear os parâmetros com inicial maiúscula, já que eles sempre serão privados.

```csharp
public record Name(string FirstName, string LastName);
// Recomendo usar maiúscula já que são membros públicos

var name = new Name();
Console.WriteLine(name.FirstName);
Console.WriteLine(name.LastName);

// No caso das classes o comportamento inverte
// recomendo usar minúscula pois são privados
public class Name(string firstName, string lastName)
{
    public string FirstName => firstName;
    public string LastName => lastName;
}

var name = new Name();
Console.WriteLine(name.FirstName);
Console.WriteLine(name.LastName);
```

## Herança e Primary Construtors

Caso haja necessidade de herdar de uma classe que contém um **Primary Constructor**, precisamos fornecer os dados para mesma, assim como já fazemos hoje com construtores que não são ***parameterless***.

```csharp
public class Student(string firstName, string lastName, int grade)
    : Name(firstName, lastName)
{
}
```

Note que ao utilizar a herança “:” somos obrigados a informar o `firstName` e `lastName`, e neste caso, precisamos receber eles também no `Student`.

Um ponto importante e interessante aqui, é que a classe `Student` possui uma propriedade a mais, chamada Grade e que não está sendo exportada, logo, esta propriedade não poderá ser acessada.

```csharp
var student = new Student("André", "Baltieri", 10);

Console.WriteLine(student.FirstName); // ✅ - Publica no Name
Console.WriteLine(student.LastName); // ✅ - Publica no Name
Console.WriteLine(student.Grade); // ❌ - Somente Student conhece
```

## Construtores Parameterless e Primary Constructors

Outro ponto interessante que testei foi o cenário onde temos a herança de uma classe com um construtor primário mas queremos ter um construtor **parameterless**.

Mais especificamente, vamos supor que a classe `Name` tenha um construtor primário mas a classe `Student` que herda dela tenha um construtor **parameterless**.

```csharp
public class Student(string firstName, string lastName, int grade)
    : Name(firstName, lastName)
{
		// Construtor com valores padrões
    public Student() : this("First", "Last", 5)
    {
    }

		public int Grade => grade;
}
```

Neste caso, podemos utilizar o this para inicializar o construtor parameterless e informar os valores padrão para `Name`, visto que eles são obrigatórios.

## Conclusão
Os construtores primários com certeza são uma ótima adição as classes no C#, tornando ainda mais simples e fácil utilizá-las.