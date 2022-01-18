O LINQ permite realizarmos consultas em listas de objetos de forma simples e prática, e neste artigo vamos explorar como podemos otimizar suas queries.

## LINQ
LINQ ou Language Integrated Query (Consulta de linguagem integrada) permite executarmos consultas sobre listas de objetos, assim como fazemos no SQL por exemplo.

```csharp
var data = from c in customers select c;
```

A diferença que é que aqui executamos a consulta sob uma lista de objetos e não sob uma tabela, e com a vantagem da tipagem e compilação.

## LINQ to SQL
Outro ponto interessante é que ao utilizar o LINQ junto ao Entity Framework por exemplo, as queries são traduzidas para linguagem SQL.

Neste caso por exemplo, caso a execução do select seja sob um `DbSet` do seu DataContext, o resultado será traduzido como abaixo.

```csharp
var data = from c in context.Customers select c;
```

```sql
SELECT * FROM [Customer]
```
O LINQ é extremamente poderoso e podemos compor ele como fazemos com a linguagem SQL, incluindo gerar novos objetos apartir do `select` como mostrado abaixo.


```csharp
var data = 
    from c in context.Customers 
    select new { c.Name, c.Email };
```

Neste caso, como podemos notar, o SQL gerado seleciona apenas os campos informados no `select new` do LINQ que acabamos de criar. Ele vai sempre acompanhar o nosso LINQ, tentando fazer a tradução mais fiel possível.


```sql
SELECT [Name], [Email] FROM [Customer]
```

## Mais exemplos
O LINQ também suporta diversos métodos que temos no SQL como **OrderBy**, **Sum**, **GroupBy** dentre outros. Na verdade o LINQ é tão poderoso quanto e podemos escrever quase qualquer SQL nele (Não que isto deva acontecer).

### Order By

O uso do **OrderBy** no LINQ é simples, basta informar a palavra reservada `orderBy`, seguida do campo e do tipo de ordenação (`ascending` | `descending`), como mostrado abaixo.

```csharp
var data = 
    from c in context.Customers 
    orderBy c.Name descending
    select new { 
        c.Name, 
        c.Email 
    };
```

### Group By

Por fim (Pelo menos aqui neste artigo) temos o **GroupBy** que nos permite agrupar conjuntos de dados, como por exemplo agrupar os pedidos pela quantidade de itens que eles possuem, conforme mostrado abaixo.

```csharp
var groupOrderByItemsQuantity =
    from order in orders
    group order by order.OrderLines.Count into groupByQuantity
    orderby groupByQuantity.Key
    select groupByQuantity;
```
Note que o retorno do `groupBy` é um `KeyValue` sendo `Key` a ordem e `Value` os itens agrupados. Por este motivo utilizamos o `orderby groupByQuantity.Key` antes do `select`. 

## Utilizando let no LINQ
Todas estas explicaçõoes foram para chegar em um recurso relativamente novo no LINQ, a possibilidade de criar variáveis durante a criação/execução da query.

O LINQ nos permite utilizar a palavra reservada `let` para armazenar valores como total de itens ou soma de determinados valores. Posteriormente, estas variáveis podem ser utilizadas no `where`, `select` ou mesmo no `groupBy`.

Para exemplificar este uso, vamos criar dois `records`, um para o pedido e outro para o item do pedido, como mostrado abaixo.

```csharp
public record Order(string Number, List<OrderLine> OrderLines);

public record OrderLine(string Product, int Quantity, decimal Price);
```
Com nossa lista criada, vamos populá-la com alguns dados. Eu criei um método chamado `GenerateOrderLines` para gerar alguns itens do pedido de forma randômica. De qualquer forma, os dados em sí não importam tanto aqui. 

```csharp
var orders = new List<Order>
{
    new("Order 1 -10 itens", GenerateOrderLines(9)),
    new("Order 2 +10 itens ", GenerateOrderLines(12)),
    new("Order 3 +10 itens", GenerateOrderLines(18)),
    new("Order 4 +10 itens", GenerateOrderLines(22))
};

List<OrderLine> GenerateOrderLines(int quantity)
{
    var result = new List<OrderLine>();
    for (var i = 0; i < quantity; i++)
        result.Add(new OrderLine($"Product {i}", i, i));

    return result;
}
```

Agora o cenário é o seguinte, vamos supor que queremos obter todos os pedidos com mais de 10 itens e cujo o total seja maior que 100 reais.

Como vimos anteriormente, podemos utilizar o `where` para filtrar os dados, mas não temos o total dos itens no pedido e nem a soma (total do pedido). Uma opção seria criar as propriedades no objeto `Order`, mas não queremos isto aqui (Para dificultar o cenário), então só vale LINQ.

```csharp
var data =
    from order in orders
    let totalItems = order.OrderLines.Count
    let total = order.OrderLines.Sum(x => x.Price)
    where
        totalItems > 10 &&
        total > 100
    select order;
```

No exemplo acima, criamos duas variávies, `totalItems` e `total` que armazenam o total de itens de um pedido e o valor total do mesmo, respectivamente.

Podemos fazer qualquer tipo de computação aqui e estas variáveis ficam disponíveis posteriormente para uso no `where` ou mesmo no `select`.

```csharp
var data =
    from order in orders
    let totalItems = order.OrderLines.Count
    let total = order.OrderLines.Sum(x => x.Price)
    where
        totalItems > 10 &&
        total > 100
    select new {
        order.Number,
        total,
        totalItems
    };
```

O exemplo acima, utilizando as variáveis no `select` além do `where` também funcionaria. Para listar os itens na tela, temos o seguinte código.

```csharp
foreach (var item in data)
    Console.WriteLine($"{item.Number} - {item.OrderLines.Sum(x => x.Price)}");
```

## Conclusão
O LINQ é uma ferramenta poderosa (Mas não significa que você deve fazer tudo nele) que nos permite realizar consultas simples e complexas em listas de objetos que podem ser traduzidas para linguagem SQL.


<div role="main" id="blog-s1-dotnet-134e3db1eea6c9829db1"></div>
<script type="text/javascript" src="https://d335luupugsy2.cloudfront.net/js/rdstation-forms/stable/rdstation-forms.min.js"></script>
<script type="text/javascript"> new RDStationForms('blog-s1-dotnet-134e3db1eea6c9829db1', 'UA-48664517-12').createForm();</script>