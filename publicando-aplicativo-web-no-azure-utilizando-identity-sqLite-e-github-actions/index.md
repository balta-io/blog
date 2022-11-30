Publicando aplicativo web no Azure, utilizando Identity, SQLite e GitHub Actions.

Sumário
1. [Introdução](#introducao)
2. [Instalações.](#instalacoes)
3. [Criando repositório remoto.](#criandorepositorioremoto)
4. [Repositório local e criação do app.](#repositoriolocalecriacaodoapp)
5. [Gerando as páginas do Identity.](#gerandoaspaginasdoidentity)
7. [Modelo do usuário da nossa app.](#modelocousuariodanossaapp)
8. 
9. [Referências](#referencias)

*******

<div id='introducao'></div> 

## Introdução

Neste artigo vou falar sobre uma forma simples e prática de implementar e publicar um aplicativo web no Azure. Este app será 
criado através de comandos das CLIs: .NET, Git, GitHub, e Azure. E nele, vamos implementar autenticação e autorização com 
Identity, SQLite como banco de dados, e o processo de deployment será feito em um App Service no Azure, automatizado pelo 
GitHub Actions, em uma máquina Linux.

<div id='instalacoes'></div>

## Instalações

Para excutar os passos que vou demonstrar neste artigo será necessário ter instaladas as CLIs: .NET, Git, GitHub, e Azure. 
Eu estou usando Windows Terminal, mas estes comandos podem ser executados em um terminal de sua escolha, em uma máquina Linux 
ou Mac. E, para editar o nosso código, também vamos precizar de uma IDE, estou utilizando Rider, mas podem ser VS Code, Visual 
Studio, etc.

<div id='criandorepositorioremoto'></div>

### Criando repositório remoto.
Vamos dar início à nossa jornada criando o nosso repositório remoto no GitHub. No browser, vamos acessar a página dos nossos 
repositórios, https://github.com, clicando no botão "New". Então, seremos redirecionados à página onde vamos dar um nome ao 
nosso repo, e sem adicionar o arquivo .gitignore, vamos concluir clicando no botão "Create Repository". E, na página inicial 
do nosso repositório, copiando a Url do repo, disponibilizado pelo GitHub, vamos passar para a próxima etapa.

![New_repo](images/newrepo.png?raw=true)

<div id='repositoriolocalecriacaodoapp'></div> 

### Repositório local e criação do app.
Para clonar o repositório remoto na nossa máquina é recomendado que seja feito em uma pasta com um caminho curto, como C:/dev/. 
Então, para navegar até a pasta, e clonar o repositório, vamos executar os seguintes comandos:
```
cd c:\dev\
git clone https://github.com/<NomeDoUsuario>/<NomeDoRepositorioRemoto>.git
```
Agora vamos criar um arquivo .gitignore e adicioná-lo à pasta do nosso repo, o que será feito com auxílio da CLI do .NET, que com
um único comando vai criar todo o arquivo para nós. No caso eu criei repositório com o nome "Article", e vou usar este nome 
daqui para frente. Então, navegando até a pasta do repositório local.
```
cd .\Article\
dotnet new gitignore
```
Para a criar o nosso projeto vamos adicionar alguns parâmetros ao comando "dotnet new", especificando o nome do projeto, e 
a utilização do Identity, biblioteca do .NET que vai fornecer suporte para autenticação e autorização da nossa aplicação, e o 
banco de dados será criado com base no SQLite.

![Dotnet_new](images/dotnetnew.png?raw=true)

<div id='gerandoaspaginasdoidentity'></div>

### Gerando as páginas do Identity.

Para gerar as páginas do Identity utilizaremos o scaffolder do Identity, pela linha de comando. Para isso, devemos 
instalar a ferramenta de geração de código do ASP.NET, o pacote para geração de código do Visual Studio, e o pacote
Microsoft.EntityFrameworkCore.Design.
```
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
```
Agora, vamos visualisar as opções do scaffolder do Identity, e se por acaso retornar alguma mensagem de erro verifique as 
versões dos pacotes, que devem ser compatíveis. E na sequência vamos executar o scaffolder, passando como parâmetro a 
classe com o nosso banco de dados, como referência, indicando quais as páginas que deverão ser criadas , registro de 
usuário, confirmação do registro, login, logout, e por fim o SqLite.
```
dotnet aspnet-codegenerator identity --help
dotnet aspnet-codegenerator identity --dbContext Article.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.RegisterConfirmation" --useSqLite
```

<div id='modelocousuariodanossaapp'></div>

### Modelo de usuário do app.

A fim de oferecer uma experiência um mais personalizada ao usuário da nossa aplicação, vamos criar a classe para o modelo
do usuário, criando a pasta Models, adicionando a classe "ApplicationUser". Esta classe vai herdar de IdentityUser,
indicando que é do tipo IdentityUser, e também vamos alterar o tipo que será atribuído ao Id do usuário para Guid (padrão
string). Além disso, vamos sobrescrever algumas propriedades, e adicionar novas, para que o cadastro do usuário tenha o
comportamento que nós queremos.

![ApplicationUser](images/applicationuser.png?raw=true)

Note que na herança estamos indicando que o IdentityUser vai receber o tipo Guid, no Id. Para gerarmos esse Guid estamos
sobrescrevendo a propriedade Id. E, também estamos sobrescrevendo UserName para receber a entrada de um valor do tipo
string (por padrão recebe o valor do e-mail), em uma página que altera dados do usuário, assim como NormalizedUserName.

<div id='referencias'></div>

## Referências

[Curso: Fundamentos do Azure, Git, GitHub e DevOps](https://balta.io/player/assistir/442da086-3cac-4d96-9332-cdab3797c01c)

[Introduction to Identity on ASP.NET Core](https://learn.microsoft.com/en-us/aspnet/core/security/authentication/identity?view=aspnetcore-6.0&tabs=visual-studio~~~~)
