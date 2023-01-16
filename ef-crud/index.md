Neste artigo vamos aprender a criar um CRUD com Entity Framework de uma forma simples e objetiva.

## O que é o Entity Framework
Entity Framework é um conjunto de bibliotecas para acesso à dados criada e mantida pela Microsoft que facilita a execução de operações CRUD no SQL Server e outros bancos.

## O que é CRUD?
CRUD é a sigla para CREATE (CRIAR), READ (LER), UPDATE (ALTERAR) e DELETE (EXCLUIR), ou seja, as quatro operações básicas que temos nos bancos de dados.

O CRUD é conhecido como a "base" que todo programador(a) precisa saber. É quase certeza que em alguma entrevista de emprego você será desafiado a implementá-lo.

## Ferramentas necessárias
Para seguir com este tutorial, você precisa do .NET e SQL Server instalados. Você pode encontrar artigos que detalham a instalação destas ferramentas aqui:
* [.NET](https://balta.io/blog/dotnet-instalacao-configuracao-e-primeiros-passos)
* [SQL Server](https://balta.io/blog/sql-server-docker)

## Criando o projeto
Vamos começar criando um projeto novo, utilizando o comando `dotnet new` conforme mostrado abaixo. Neste caso, estou utilizando o .NET 7.
```
dotnet new console -o EfCrud
cd EfCrud
```
## Iniciando com Code First
O Entity Framework possui uma abordagem de início diferente da que vimos no [artigo anterior sobre Dapper](https://balta.io/blog/dapper-crud), chamada **Code First**.

Como o nome sugere, **Code First** nos diz para codificar primeiro, e esquecer o banco de dados por enquanto, mas na verdade podemos começar pelo banco de dados também, sem problemas, seria a abordagem **Database First**.

De qualquer forma, vamos cria nossa classe ou como carinhosamente chamamos, **Entidade** que vai representar uma tabela no banco.

```csharp
namespace EfCrud.Entities;

public class Category
{
    public int Id { get; set; }
    public string Title { get; set; } = string.Empty;
    public string Slug { get; set; } = string.Empty;
    public string Description { get; set; } = string.Empty;
}
```

## Ferramentas do Entity Framework
A grande diferença entre o Entity Framework e o Dapper é que o EF é um Framework, ou seja, um conjunto de bibliotecas que nos permite abstrair muita coisa, inclusive criação de banco e código SQL.

Porém, para gerar o banco e executar outros comandos do EF, precisamos instalar uma ferramenta adicional, o `dotnet-ef`, conforme mostrado abaixo:

```bash
# Primeira instalação
dotnet tool install --global dotnet-ef

# Se já tem instalado e só quiser
# atualizar para última versão
dotnet tool update --global dotnet-ef
```
## Instalando o Entity Framework
Na raiz do projeto, podemos instalar o Entity Framework simplesmente executando um dos comandos abaixo.

```bash
# Caso queira trabalhar com SQL Server
dotnet add package Microsoft.EntityFrameworkCore.SqlServer

# Caso queira trabalhar com SQLite
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

A parte boa do Entity Framework é que ele abstrai o banco para nós, então tanto faz se você quer usar SQLite ou SQL Server.

## Data Context
Um contexto de dados é basicamente a representação do banco de dados em memória. Faremos as operações nele e depois ele se vira para transformar tudo que fizemos em SQL e persistir as informações no banco.

### Criando um Data Context
Embora pareça complexo, um Data Context nada mais é do que uma classe que herda outra classe chamada `DbContext`. Feito isto, precisamos apenas informar quais entidades queremos relacionar com o banco.

```csharp
using EfCrud.Entities;

namespace EfCrud.Data;

public class AppDbContext : DbContext
{
    public DbSet<Category> Categories { get; set; } = null!;
}
```

Acredite ou não, isto é tudo o que precisamos para mapear nosso banco para um contexto de dados e vice-versa. Bem simples, não é?

### Connection String
Bom, até o momento nós dissemos que vamos ter um banco, que haverá uma tabela `Categories` que se ligará com a entidade `Category`, mas em nenhum momento dissemos qual é este banco, em qual servidor ele está e como chegar até ele (usuário e senha).

Isto pode ser feito através da **Connection String** ou string de conexão, que nada mais é do que um texto separado por `;` que informa os detalhes de como chegamos ao banco.

Se você estiver perdido em como obter esta informação do seu SQL Server, [veja este artigo sobre SQL Server no Docker](https://balta.io/blog/sql-server-docker) que explico lá. Inclusive essa connection string que vou usar aqui saiu deste artigo.

```csharp
using EfCrud.Entities;

namespace EfCrud.Data;

public class AppDbContext : DbContext
{
    public DbSet<Category> Categories { get; set; } = null!;

    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlServer("Server=localhost,1433;Database=NOME_DO_BANCO;User ID=sa;Password=1q2w3e4r@#$;Trusted_Connection=False; TrustServerCertificate=True;");
}
```
Caso queira trabalhar com SQLite, mudamos apenas as opções e Connection String conforme abaixo.

```csharp
using EfCrud.Entities;

namespace EfCrud.Data;

public class AppDbContext : DbContext
{
    public DbSet<Category> Categories { get; set; } = null!;

    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlite("DataSource=app.db;Cache=Shared");
}
```
## Operações CRUD
Estamos na reta final, agora com tudo configurado, podemos executar as operações CRUD com muita facilidade. Listei elas abaixo, da forma mais simples e objetiva possível.

### Create - Criar
```csharp
// Instância da entidade
var category = new Category(0, "Categoria 1", "categoria-1", "Descrição da Categoria 1");

// Instância do Data Context
using var context = new AppDbContext();

// Adiciona a entidade ao Data Context
await context.Categories.AddAsync(category);

// Persiste as mudanças
await context.SaveChangesAsync();
```

Dois pontos importantes aqui são o uso do `using` que já se encarregará de fechar a conexão assim que a mesma não for mais utilizada (Assim como no Dapper quando usamos o SQL Connection) e o `await` para tornar as operações **assíncronas**.

Por fim, podemos notar que precisamos de duas linhas de código para persistir os dados (Na verdade para salvar, excluir e atualizar). Isto ocorre pois como comentado anteriormente, o **Data Context** é a representação do banco de dados em memória, logo, quando utilizamos o `AddAsync` ele adiciona a entidade apenas no banco em memória.

Para salvar de fato no banco de dados, precisamos do comando `SaveChangesAsync`. Este mesmo comportamento será visto nos outros métodos (Exceto leitura).

### Read - Ler
Como você deve imaginar, ler os dados não é algo difícil, porém tem alguns detalhes. Futuramente vou explicar melhor sobre `AsNoTracking` mas hoje vamos focar no básico.

Desta forma temos tudo o que precisamos no `context.Categories`, porém o resultado desta propriedade é um `IQueryable`, ou seja, não é algo materializado.

Sendo assim, sempre que utilizamos o `ToListAsync()` ou `FirstOrDefaultAsync` (Ou outros métodos do IList) a query de fato é executada no banco.

```csharp
// Instância do Data Context
using var context = new AppDbContext();

// Lê todas as categorias
var categories = await context.Categories.ToListAsync();

// Busca a categoria com ID 1
var category = await context.Categories.FirstOrDefaultAsync(x => x.Id == 1);
```

Você percebeu que se inverter o `ToListAsync` com um `Where` pode causar a leitura de todos os dados de uma tabela?

```csharp
// 🔴 ERRADO
var categories = await context.Categories.ToListAsync().Where(x => x.Active == true);

// 🟢 Correto
var categories = await context.Categories.Where(x => x.Active == true).ToListAsync();
```

### Update - Atualizar
Como os processos são muito parecidos, não vou me estender aqui, exceto pelo fato que não temos um método `UpdateAsync` assim como temos no `AddAsync` e `RemoveAsync`, então não precisamos do `await`.
```csharp
// Instância do Data Context
using var context = new AppDbContext();

// Recupera a entidade (Re-hidratação)
var category = await context.Categories.FirstOrDefaultAsync(x => x.Id == 1);

// Altera a entidade
category.Title = "Categoria Atualizada";

// Atualiza os dados no Data Context
context.Categories.Update(category);

// Persiste os dados no banco
await context.SaveChangesAsync();
```

### Delete - Excluir
Por fim, temos o método de excluir, que é bem simples.

```csharp
// Instância do Data Context
using var context = new AppDbContext();

// Recupera a entidade
var category = await context.Categories.FirstOrDefaultAsync(x => x.Id == 1);

// Remove ela do Data Context
await context.Categories.RemoveAsync(category);

// Persiste as mudanças
await context.SaveChangesAsync();
```

## Quer aprender mais sobre Entity Framework?
* [Masterclass GRAUITA - Dapper VS Entity Framework](https://go.balta.io/masterclass-dapper-vs-entity-framework)
* [CURSO COMPLETO - Acesso à dados com Dapper e SQL Server](https://balta.io/cursos/acesso-dados-csharp-net-dapper-sql-server)
* [CURSO COMPLETO - Entity Framework e SQL Server](https://balta.io/cursos/fundamentos-entity-framework)
* [CARREIRA COMPLETO - .NET do ZERO ao PRO](https://balta.io/carreiras/desenvolvedor-backend-dotnet)
