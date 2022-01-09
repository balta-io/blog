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

```sql
SELECT [Name], [Email] FROM [Customer]
```

## Mais exemplos


### Order By
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
```csharp
var groupOrderByItemsQuantity =
    from order in orders
    group order by order.OrderLines.Count into groupByQuantity
    orderby groupByQuantity.Key
    select groupByQuantity;
```

## Utilizando let no LINQ