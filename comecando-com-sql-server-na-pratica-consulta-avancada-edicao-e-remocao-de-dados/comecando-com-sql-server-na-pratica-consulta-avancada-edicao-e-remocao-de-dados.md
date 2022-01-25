Aprenda na prática a desenvolver consultas complexas, manipular e deletar informações no Microsoft SQL Server.

Sumário
- [Introdução](#introdução)
- [Continuando de onde paramos](#continuando-de-onde-paramos)
- [Consultas complexas](#consultas-complexas)
  - [Criando a view](#criando-a-view)
- [Editando registros](#editando-registros)
- [Editando Colunas](#editando-colunas)
- [Editando Tabelas](#editando-tabelas)
- [Considerações finais](#considerações-finais)
- [Cursos relacionados](#cursos-relacionados)
- [Referências](#referências)

 <div id='introducao'></div>

## Introdução

Este artigo é uma continuação do artigo [Começando com SQL Server na pratica (Estrutura, inserção e consulta))](https://balta.io/blog/comecando-com-sql-server-na-pratica-estrutura-insercao-e-consulta) e como descrito no título a proposta é que a pessoa lendo o artigo entenda na prática como funciona o Microsoft Sql Server.

É recomendado que caso no caso da pessoa lendo o artigo esteja começando volte e leia o artigo anterior. Caso já tenha lido e/ou tenha conhecimento nas instruções anteriores. Esta leitura é a parte final deste artigo.

<div id='continuando-de-onde-paramos'></div>

## Continuando de onde paramos

Anteriormente fizemos o registro de uma nova categoria, grupo de palestras e um usuário. Também atribuimos o nosso usuário a um grupo. Agora iremos concluir os registros com a criação de um evento. Este é o código usado para registrarmos o evento em nossa base de dados:

```sql
BEGIN TRANSACTION
    INSERT INTO [evento] VALUES (
        NEWID(),
        'Persistênca de dados',
        'Uma visão detalhada do processo de persistência.',
        '6ccbe492-8c8c-40fc-8039-c3759b9ab69b',
        GETDATE(),
        GETDATE()+7,
        'e83848d3-cffd-4445-bf7a-2996583e834a',
        0,
        NULL
    )
COMMIT
```

`NOTA I`: Sempre que a sequência dos dados que serão inseridos segue a sequência de cascata da tabela ao qual ele está relacionado. Também note que na data do evento (a data em que ele acontecerá) pegamos a data atual e adicionamos 7 dias.

## Consultas complexas

Agora faremos uma consulta para conferir se as informações que acabamos de registrar estão corretas, porém iremos pegar apenas determinados campos e antes de exibir vamos renomear as colunas de exibição para melhor entendermos os resultados. Ficando assim:

```sql
SELECT TOP (1)
        [evento].[Id] AS 'Id Evento',
        [evento].[Titulo] AS 'Evento',
        [evento].[IdCategoria] AS 'Id Categoria',
        [categoria].[Nome] AS 'Categoria',
        [evento].[DataEvento] AS 'Data do evento',
        [evento].[IdOrganizador] AS 'Id Organizador',
        [usuario].[NomeCompleto] AS 'Organizador'
    FROM [eventos].[dbo].[evento]
        INNER JOIN [categoria] ON [categoria].[Id] = [evento].[IdCategoria]
        INNER JOIN [usuario] ON [usuario].[Id] = [evento].[IdOrganizador]
WHERE [Titulo] LIKE '%dados'
```

`NOTA II`: Veja que ao usarmos o `where` para pesquisar por parte do título do evento, utilizamos o símbolo de porcentagem apenas antes da palavra chave. Assim estamos indicando que nossa consulta deverá retornar apenas eventos que tenham como final do título a palavra "dados".

<!-- select-evento.png -->

Já sabemos como pegar determinados campos e exibir em uma consulta, também sabemos como renomear colunas antes de exibir e sabemos a utilidade do `INNER JOIN`. O que vamos aprender agora é como deixar registrado em nosso banco que quando quisermos pesquisar um evento e retornar com ele as informações básicas do organizador e categoria, iremos apenas chamar uma `VIEW` e passar o parametro de busca.

<div id='criando-a-view'></div>

### Criando a view

```sql
CREATE VIEW [buscarEvento] AS
SELECT TOP (1)
        [evento].[Id] AS 'Id Evento',
        [evento].[Titulo] AS 'Evento',
        [evento].[IdCategoria] AS 'Id Categoria',
        [categoria].[Nome] AS 'Categoria',
        [evento].[DataEvento] AS 'Data do evento',
        [evento].[IdOrganizador] AS 'Id Organizador',
        [usuario].[NomeCompleto] AS 'Organizador'
    FROM [eventos].[dbo].[evento]
        INNER JOIN [categoria] ON [categoria].[Id] = [evento].[IdCategoria]
        INNER JOIN [usuario] ON [usuario].[Id] = [evento].[IdOrganizador]
```

`NOTA III`: Veja que precisamos apenas adicionar a linha `CREATE VIEW [] AS` antes do nosso `SELECT` e removemos a condição `WHERE`. Desta forma deixamos um padrão de visualização salvo e para consultar estes dados agora em vez de escrever todos os dados como fizemos no primeiro exemplo. Iremos apenas chamar a view e acrescentar nossa condição:

```sql
SELECT * FROM [buscarEvento] WHERE [Evento] LIKE '%dados'
```

O Resultado será o mesmo da nossa consulta anterior. Mas aonde fica armazenada esta view? Bem, caso queira saber, as view podem ser encontradas em uma pasta abaixo das tabelas no nosso banco de dados:

<!-- path-views.png -->

## Editando registros

Imagine que você deseja modificar a descriçao de um evento. Neste caso utilizamos o `UPDATE` e no exemplo abaixo vocÊ pode acompanhar como utilizar ele para editar a descrição de um evento registrado:

```sql
BEGIN TRANSACTION
    UPDATE [evento]
    SET
        [Descricao] = 'Aguardando descrição' 
    WHERE [Titulo] LIKE '%dados'
    GO
ROLLBACK
```

**PS**: Lembre sempre de modificar o `ROLLBACK` para `COMMIT` para que as mudanças sejam aplicadas no banco, do contrário o comando será executado apenas como teste e os dados voltaram ao valor que estavam antes da consulta.

## Editando Colunas

Agora digamos que você deseja tornar o campo "Descrição" obrigatório na tabela de eventos. Então será necessário utilizar o `ALTER TABLE` da seguinte forma:

```sql
BEGIN TRANSACTION
    ALTER TABLE [evento]
    ALTER COLUMN [Descricao] VARCHAR(150) NOT NULL;
ROLLBACK
```

Se quisermos remover uma coluna da tabela podemos fazer da seguinte forma:

```sql
BEGIN TRANSACTION
    ALTER TABLE [evento]
    DROP COLUMN [Link];
ROLLBACK
```

E teriamos retirado a coluna "Link" da nossa tabela de Eventos.

Para adicionar uma coluna podemos utilizar o `ADD`

```sql
BEGIN TRANSACTION
    ALTER TABLE [evento]
    ADD [Link] VARCHAR(255) NULL;
ROLLBACK
```

E trariamos de volta a existência do campo "Link".

Assim acabamos de reduzir o número máximo de caracteres na descriçao do evento de 200 para 150 e implementamos a obrigatoriedade do campo com o `NOT NULL`.

## Editando Tabelas

## Considerações finais
Passamos pela conexão e criação do banco. Estruturação, inserção e consulta dos dados. Na proxíma parte iremos aprender a fazer consultas avançadas, desenvolver stored procedures, editar informações dos registros e tabelas e excluir registros, tabelas e até mesmo bancos de dados inteiros.


`NOTA IV`: Os contextos exibidos neste artigo podem ser desenvolvidos facilmente. Caso o leitor sinta vontade e/ou necessidade de entender mais detalhadamente os comandos, instruções e ferramentas utilizadas é recomendado acompanhar o curso [Fundamentos do SQL Server](https://balta.io/player/assistir/cae580e7-d215-4d08-9414-fe988713cc97).

## Cursos relacionados

[Fundamentos do SQL Server](https://balta.io/player/assistir/cae580e7-d215-4d08-9414-fe988713cc97)

<div id='ref'></div> 

## Referências
[Documentação do Microsoft SQL](https://docs.microsoft.com/pt-br/sql/?view=sql-server-ver15)