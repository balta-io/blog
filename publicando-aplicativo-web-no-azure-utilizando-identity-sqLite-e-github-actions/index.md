Publicando aplicativo web no Azure, utilizando Identity, SQLite e GitHub Actions.

Sumário
1. [Introdução](#introducao)
2. [Instalações.](#instalacoes)
3. [Criando repositório remoto.](#criandorepositorioremoto)
4. [Repositório local e criação do app.](#repositoriolocalecriacaodoapp)


5. [Linguagens Compiladas](#lpscompiladas)
6. [Linguagens Tipadas e não tipadas](#lpstipadasenaotipadas)
7. [Linguagens Gerenciadas](#lpscodigogerenciado)
8. [Quais são as principais linguagens de programação?](#principaislps)
9. [Hello World! Em diferentes linguagens de programação](#helloworldem)
10. [C# como sua primeira linguagem de programação](#csharpsuaprimeiralp)
11. [Hello World! Em C#](#hellocsharp)
12. [Referências](#ref)

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




## Referências

[Curso: Fundamentos do Azure, Git, GitHub e DevOps](https://balta.io/player/assistir/442da086-3cac-4d96-9332-cdab3797c01c)

[Introduction to Identity on ASP.NET Core](https://learn.microsoft.com/en-us/aspnet/core/security/authentication/identity?view=aspnetcore-6.0&tabs=visual-studio~~~~)
