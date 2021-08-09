Se você não pode, ou mesmo não quer, utilizar **MySQL** em sua **API-REST no .NET 5**, leia esse artigo até o final e certamente conseguirá usar com **PostgreSQL**.

**Então vamos lá!** Descobri que meu computador (**macOS** Catalina 10.15.7) não possui recurso de hardware suficiente para instalação do *MySQL*. Sim, caros amigos, o *MySQL* só pode ser instalado em versões 11+ do macOS. Logo, como não posso comprar um outro computador neste momento, resolvi buscar em fóruns e sites a solução de como criar *Migrations* no *PostgreSQL* com .NET 5. Como não encontrei a resposta facilmente, busquei consolidar neste artigo todas as pesquisas que fiz para solucionar meu problema, espero que ajude de alguma forma.

*A importância do aprendizado contínuo:* incluí esse tópico porque apenas duas semanas depois de escrever este artigo, descobri através de um curso do [**Balta.io**](https://balta.io/) (*que por sinal, super indico!*) que poderia ter usado o **Docker** com **SQL Server**. Para saber mais, consulte este [**artigo**](https://balta.io/blog/sql-server-docker) do próprio Balta.io e conseguirá usar o *SQL Server* como alternativa, sem a necessidade de instalação em sua máquina.

## O que você vai precisar instalar?

Primeiro, você precisará instalar o **.NET 5**, **Postman** (*para testar sua API!*) e o **PostgreSQL** em sua máquina. 

*Mas aqui já vai uma dica!* Ao instalar o *PostgreSQL*, sugiro baixar o instalador, porque caso faça pelo *HomeBrew* (sistema de gerenciador de pacotes), você terá que fazer a configuração de usuário e senha após a instalação por linha de comando no terminal. 

E vai por mim, se você é iniciante nessas coisas, pode dar bastante trabalho.

## Instalações de pacotes NuGet no .NET 5

Com seu projeto aberto no .NET 5, adicione os seguintes pacotes pelo *NuGet*:

 1. Microsoft.EntityFrameworkCore
 2. Microsoft.EntityFrameworkCore.Design
 3. Microsoft.EntityFrameworkCore.Tools
 4. Npgsql.EntityFrameworkCore.PostgreSQL
 5. Nuget.CommandLine

No geral, os pacotes acima permitem que você tenha os recursos necessários para fazer o acesso a dados usando objetos .NET, conexão com o banco de dados *PostgreSQL* e trabalhar com o *Nuget* pela linha de comando no terminal. 

*Aqui mais uma dica!* Caso você esteja usando a versão do .NET em um **macOS**, você não terá o recurso "**Console de pacotes**" no Visual Studio (pois é, ele é exclusivo para *Windows* e *Linux*) e terá que usar o terminal do seu sistema operacional.

## Conectando com o banco de dados PostgreSQL

Com usuário e senha do banco de dados em mãos, crie a *string* de conexão no arquivo **_appsettings.json_** do seu projeto, conforme modelo abaixo:

```
"DbContextSettings": {  
    "ConnectionString": "User ID=**seu_nome_de_usuario**;Password=**sua_senha**;Host=localhost;Port=5432;Database=**nome_do_banco_do_projeto**;Pooling=true;"  
}
```

**Importante!**  O texto `"User ID="` deve ser escrito com um espaço entre as palavras **User** e **ID**. Os nomes de usuário do banco de dados, senha e database não podem conter espaço. Você não precisa ter criado um banco de dados manualmente pelo  _pgAdmin4_  do  _PostgreSQL_, basta você colocar o nome que deseja nesta configuração e ao gerar a  _Migrations_, a *Database* e as tabelas serão criadas automaticamente.

Após isso, dentro do método "_ConfigureService"_  do arquivo  **_Startup.cs_**  do seu projeto, declare a variável de conexão para que sempre seja executado durante a inicialização.

```csharp
var connectionString = Configuration["dbContextSettings:ConnectionString"];  
    services.AddDbContext<VideoContext>(options =>  
    options.UseNpgsql(connectionString)  
);
```

**Importante!** Lembre-se de utilizar o `ALT + ENTER` para incluir as bibliotecas necessárias.

## MacOS, um passo antes das Migrations com .NET 5

**Importante!**  Se o seu Visual Studio estiver instalado no *Windows*, seja feliz e vá direto ao próximo tópico do artigo!

Caso esteja no **macOS**, terá que executar os comandos abaixo no Terminal (dentro do Visual Studio ou fora, pelo SO) antes de criar as *Migrations*. Lembre-se que o Visual Studio para macOS possui o Console de Pacotes, mas não é possível executar comandos nele, por isso é necessário fazer este procedimento.

```
dotnet tool install — global dotnet-ef
dotnet ef
```

A instalação dessas ferramentas **CLI** (interface de linha de comando) são necessárias para executarmos as *Migrations* pela linha de comando do Terminal.

Saiba mais:

[# Visão geral da CLI do .NET](https://docs.microsoft.com/pt-br/dotnet/core/tools/)

[# Referência de ferramentas de Entity Framework Core – console do Gerenciador de pacotes no Visual Studio](https://docs.microsoft.com/pt-br/ef/core/cli/powershell)

## Gerando Migrations no .NET 5 e PostgreSQL

Finalmente! Chegamos a etapa final e objetivo deste artigo existir.

Com sua API-REST criada, isto é, *Models*, *Controllers*, *conexão aos dados configuradas e seu projeto executando sem erros*. Acesse o *Console de pacotes* do seu projeto pelo *Visual Studio* e execute os comandos na ordem abaixo.

**Importante!** Se estiver usando um **macOS**, certifique-se que está localizado dentro da pasta do projeto (onde está o arquivo .csproj), antes de executar os comandos no Terminal (_veja outros detalhes no tópico anterior_).

**1. Criando as migrations:**
```
dotnet ef database update
dotnet ef migrations add InitialCreate
```
Com este comando, criamos o modelo instantâneo com base em nossa API-REST.

**2. Criando o banco de dados e as tabelas**
```
dotnet ef database update
```
**Pronto!**  Se tudo estiver configurado conforme os tópicos anteriores, suas  _Migrations_  foram criadas. Agora coloque sua aplicação para rodar, abra o  _pgAdmin4_  e o  _Postman_  e execute os testes necessários.

Abaixo listo outros comandos úteis para manutenção das *Migrations*.


**Removendo migrations:**
```
dotnet ef migrations remove
```

**Obtendo mais informações sobre falhas:**
```
dotnet ef migrations remove -v
```

Saiba mais:

[# Tutorial: parte 5, aplicar migrações ao exemplo da Contoso University](https://docs.microsoft.com/pt-br/aspnet/core/data/ef-mvc/migrations?view=aspnetcore-5.0)

[# Migrações em ambientes de equipe](https://docs.microsoft.com/pt-br/ef/core/managing-schemas/migrations/teams)

## Concluindo, sem mais delongas!

*Aprender algo novo é sempre um desafio*. Requer investimento de tempo e muita resiliência para frustrações. Eu me considero uma iniciante em .NET e demorei um dia inteiro para conseguir encontrar a solução que descrevi acima.

Espero que tenha ajudado de alguma forma. Espero ainda mais… que este artigo *inspire você a escrever sobre assuntos que você conhece e não encontrou facilmente*.

Fique a vontade em compartilhar este artigo, mas principalmente, contribuir com comentários, caso você conheça outras formas mais fáceis de fazer o que tentei explicar aqui!

Um forte abraço! E como diria uma amigo *"dúvidas, traumas ou angústias"* sobre este conteúdo, estou a disposição para ajudar.

**Cuide-se. Use máscara.**
