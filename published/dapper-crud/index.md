Neste artigo vamos aprender a criar um CRUD com Dapper de uma forma simples e objetiva.

## O que é o Dapper?
Dapper é uma biblioteca que facilita nossa vida na hora de consultar, editar, excluir e inserir dados em um banco, seja ele SQL Server, Postgres, MySQL ou qualquer outro.

O Dapper é uma extensão do pacote `Microsoft.Data.SqlClient`, uma biblioteca que faz parte de um ecossistema chamado ADO (Active Data Objects), ou simplificando, o alicerce de acesso à dados da Microsoft.

Desta forma, o Dapper apenas incrementa o `Microsoft.Data.SqlClient`, adicionando métodos como `Execute` e `Query` ao `SqlConnection`. Parece um bicho de sete cabeças, mas é bem simples, confere só 👇

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
dotnet new console -o DapperCrud
cd DapperCrud
```

## Preparando o banco de dados
Antes de fazer qualquer operação, precisamos de um banco criado e pelo menos uma tabela. Não vou estender o artigo para este lado, então abaixo está o código para criação de tudo que precisamos.

```sql
CREATE DATABASE [DapperCrud]
GO

USE [DapperCrud]
GO

CREATE TABLE [Category] (
    [Id] INT NOT NULL PRIMARY KEY IDENTITY (1,1),
    [Title] NVARCHAR(80) NOT NULL,
    [Slug] VARCHAR(80) NOT NULL,
    [Description] TEXT
)
```
Basta executar o código acima no [Azure Data Studio](https://azure.microsoft.com/pt-br/products/data-studio/#overview) conectado ao seu SQL Server, seja ele no Docker ou via SQL Server Express (Somente Windows).

## Adicionando o Dapper
Nosso próximo passo é adicionar os dois pacotes que precisamos para realizar operações no banco, conforme mostrado abaixo.
```
dotnet add package Microsoft.Data.SqlClient
dotnet add package Dapper
```

## CRUD
Agora vamos ao que interessa, realizar as operações no banco. Para isto, precisamos primeiro abrir uma conexão, que pode ser feita via `SqlConnection`.

```csharp
const string connectionString =
    "Server=localhost,1433;Database=DapperCrud;User ID=sa;Password=1q2w3e4r@#$;Trusted_Connection=False; TrustServerCertificate=True;";
await using (var connection = new SqlConnection(connectionString))
{
    // ... Operações
}
```

Note que já aproveitamos para definir nossa Connection String como uma constante. Lembre-se que ela não deve ficar exposta assim no código e sim ser configurada via Application Settings como vemos no [curso **Fundamentos do Azure, Git, GitHub e DevOps**](https://balta.io/cursos/fundamentos-azure-git-github-devops).

Outro ponto é o uso do `await` antes do `using`. Este uso é opcional, mas aqui vou mostrar os métodos sendo executados de forma assíncrona. É apenas uma demonstração, não muda nada o uso do `async/await` neste cenário.

### Create
Sendo o Dapper uma extensão do `Microsoft.Data.SqlClient`, temos a adição do método `ExecuteAsync` no objeto `connection` que abrimos no passo anterior. Este método é bem simples e espera apenas dois parâmetros.

```csharp
await connection.ExecuteAsync("INSTRUÇÃO SQL", PARAMETROS);
```

Dado que já temos o *ID*  como auto-increment na tabela **Category**, podemos informar apenas os campos restantes, tendo como resultado a seguinte instrução SQL.

```sql
INSERT INTO 
    [Category] 
VALUES(
    @title, 
    @slug, 
    @description)
```

> **IMPORTANTE** Nunca utilize concatenação de strings nas instruções SQL, isto pode resultar em um SQL Injection. Utilize sempre PARÂMETROS, como os demonstrados acima com @ (Não precisa de aspas duplas nem simples).

Embora o C# seja uma linguagem fortemente tipada, podemos utilizar tipos anônimos para otimizar o tempo. Tem a opção de records também, mas vou focar aqui nos tipos anônimos pela praticidade.

```csharp
await using (var connection = new SqlConnection(connectionString))
{
    await connection.ExecuteAsync("INSERT INTO [Category] VALUES(@title, @slug, @description)",
        new
        {
            title = "Backend",
            slug = "backend",
            description = "Aprenda tudo sobre backend nesta carreira completa."
        });
}
```

Pronto, temos um trecho de código que insere uma linha em um banco de dados SQL Server com Dapper de uma forma extremamente simples e eficiente. Ao rodar a aplicação, meu resultado foi este.

```
1	Backend	backend	Aprenda tudo sobre backend nesta carreira completa.
```

### ExecuteScalar
Apenas para intuito de curiosidade, existe uma sintaxe no SQL Server que retornar o Id do objeto que acabou de ser inserido. Basta utilizar o `scope_identity` conforme mostrado abaixo.

```sql
SELECT CAST(scope_identity() AS INT)
```

Podemos combinar o `scope_identity` com o `ExecuteScalarAsync<int>` do Dapper e retornar o Id da linha que acabamos de inserir.

```csharp
await using (var connection = new SqlConnection(connectionString))
{
    var id = await connection.ExecuteScalarAsync<int>("INSERT INTO [Category] VALUES(@title, @slug, @description);SELECT CAST(scope_identity() AS INT)",
        new
        {
            title = "Backend",
            slug = "backend",
            description = "Aprenda tudo sobre backend nesta carreira completa."
        });
    
    Console.WriteLine(id);
}
```

### Read
Existem várias formas de ler registros do banco, dentre elas estão o `QueryAsync`, `QueryAsync<T>`, `QueryFirstOrDefaultAsync` e `QueryFirstOrDefaultAsync<T>` conforme mostrado abaixo.

```csharp
// Lista todas as categorias
var categories = await connection.QueryAsync("SELECT [Id], [Title], [Slug], [Description] FROM [Category]");
foreach (var category in categories) 
    Console.WriteLine(category.Title);

// Lista a categoria 1
var category = await connection.QueryFirstOrDefaultAsync("SELECT [Id], [Title], [Slug], [Description] FROM [Category] WHERE [Id]=@id", new { id = 1 });
Console.WriteLine(category.Title);
```

Caso queira tipa explicitamente o retorno dos resultados, podemos utilizar um `Struct`, `Class` ou `Record`. Neste caso vou usar um `Record` pela simplicidade.

```csharp
public record Category(int Id, string Title, string Slug, string Description);
```

```csharp
// Lista todas as categorias
var categories = await connection.QueryAsync<Category>("SELECT [Id], [Title], [Slug], [Description] FROM [Category]");
foreach (var category in categories) 
    Console.WriteLine(category.Title);

// Lista a categoria 1
var category = await connection.QueryFirstOrDefaultAsync<Category>(
    "SELECT [Id], [Title], [Slug], [Description] FROM [Category] WHERE [Id]=@id", 
    new { id = 1 });
Console.WriteLine(category.Title);
```

### Update
Os métodos a seguir são bem simples e seguem a mesma linha do **CREATE**, utilizando o método `ExecuteAsync` para executar a instrução SQL.

```csharp
await connection.ExecuteAsync(
    "UPDATE [Category] SET [Title]=@title WHERE [Id]=@id",
    new { id = 1, title = "Frontend" });
```

### Delete
Por fim, o **DELETE** é ainda mais simples, sendo necessário apenas informar o *ID* do objeto a ser excluído.
```csharp
await connection.ExecuteAsync(
    "DELETE FROM [Category] WHERE [Id]=@id",
    new { id = 1 });
```

## Quer aprender mais sobre Dapper?
* [Masterclass GRAUITA - Dapper VS Entity Framework](https://go.balta.io/masterclass-dapper-vs-entity-framework)
* [CURSO COMPLETO - Acesso à dados com Dapper e SQL Server](https://balta.io/cursos/acesso-dados-csharp-net-dapper-sql-server)
* [CURSO COMPLETO - Entity Framework e SQL Server](https://balta.io/cursos/fundamentos-entity-framework)
* [CARREIRA COMPLETO - .NET do ZERO ao PRO](https://balta.io/carreiras/desenvolvedor-backend-dotnet)