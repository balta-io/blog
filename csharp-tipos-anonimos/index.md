O C# é conhecido por sua tipagem, mas neste artigo vamos conhecer os vários tipos anônimos que temos nele.

## Tipar ou não tipar?
O C# é uma linguagem muito dinâmica e versátil, o que significa que ela tem diversas aplicabilidades, como Web, Mobile, Desktop, IA e games.

Dentre as várias coisas boas do C#, está sua tipagem forte, que implica na obrigatoriedade de um tipo para nossas variáveis.

Abaixo temos um exemplo utilizando **JavaScript** e o mesmo utilizando **C#**, apenas para efeito de comparação.

```javascript
let name = 'André Baltieri';
```

```csharp
string name = "André Baltieri";
```

[**Confira nosso artigo**](https://balta.io/blog/csharp) sobre C# para ficar por dentro de todas as funcionalidades dele.

### Quando não tipar?
Um bom exemplo que passei, e não foi uma única vez, é o consumo de APIs externas. Em diversos momentos queremos fazer uma requisição a uma API, mas não queremos criar um tipo apenas para esperar seu retorno.

Outro cenário que usei muito foi importação e exportação de dados, coisas pequenas, pontuais e que criar tipos ia tomar mais tempo.

## Dicionários
Normalmente não comentamos sobre dicionários para criação de estruras, mas sim para listas. Porém, podemos utilizá-los para estruturar dados.

```csharp
var person = new Dictionary<string, object> 
{
    ["Name"] = "André Baltieri",
    ["BirthDate"] = DateTime.Now
}
```
Além do formato utilizando `[]`, podemos definir as estruturas utilizando `{}` com as chaves e valores como mostrado abaixo.

```csharp
var person = new Dictionary<string, object> 
{
    { "Name", "André Baltieri" },
    { "BirthDate", DateTime.Now }
}
```

## Tipo anônimo
Se você não quiser criar uma **classe**, **estrutura** ou **record** para seus dados, você pode utilizar um tipo anônimo no C#.

```csharp
var person = new 
{
    Name = "André Baltieri",
    BirthDate = DateTime.Now
}
```
Como podemos notar, basta "fingir" que está instanciando uma classe e criar os nomes das propriedade e valores sob demanda.

## Tipagem com Records
Caso queira uma abordagem com o mínimo de tipagem possível, a recomendação mais simples talvez sejam os `records`.

```csharp
public record Person(string Name, DateTime BirthDate);

var person = new Person("André Baltieri", DateTime.Now);
```

Basta definir o tipo e nome das propriedades já na criação do `record`, como se fossem os parâmetros de um método e pronto.

## Tipagem com classe
Por fim, se quiser utilizar o clássico, com todo poder que a OOP oferece, vale a pena ir de classes.

```csharp
public class Person
{
    public string Name { get; init; }
    public DateTime BirthDate { get; init; }
}

var person = new Person
{
    Name = "André Baltieri",
    BirthDate = DateTime.Now
};
```

## Conclusão
Podemos trabalhar com tipos anônimos de forma simples e fácil no C#, sem a necessidade de criar classes, estruturas ou registros.