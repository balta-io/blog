# Sumário

 1. [Introdução](#introducao)
 2. [O que é o GitHub Projects](#o-que-é-o-github-projects)
 3. [Criando um projeto](#criando-um-projeto)
     - [Através do menu de Organização](#através-do-menu-de-organização)
     - [Através do menu de Equipe](#através-do-menu-de-equipe)
     - [Através do menu de usuário](#através-do-menu-de-usuário)
     - [Através do menu de repositório](#através-do-menu-de-repositório)
     - [Sobre o modelo planilha](#sobre-o-modelo-planilha)
     - [Sobe o modelo Kanban](#sobe-o-modelo-kanban)
 4. [Adicionando uma atividade ao projeto](#adicionando-uma-atividade-ao-projeto)
     - [Manualmente](#manualmente)
     - [Através de um issue](#através-de-um-issue)
 5. [Costumizando a visualização](#costumizando-a-visualização)
     - [Adicionando colunas](#adicionando-colunas)
     - [Adicionando uma nova opção à uma coluna existente](#adicionando-uma-nova-opção-à-uma-coluna-existente)
 6. [Agora é com você](#agora-é-com-você)
 7. [Continue lendo:](#continue-lendo)
 8. [Cursos relacionados](#cursos-relacionados)
 9. [Referências](#referências)

<div id='introducao'></div> 

# Introdução

No ambiente de trabalho podemos utilizar diversas tecnologias diferentes e independente desta vasta quantidade de opções e possibilidades é certo que será necessário versionar o projeto. O GitHub é uma ferramenta excelente neste quisito porém "nem só de versionamento vive o GitHub" neste artigo vamos passar por algumas ferramentas que o GitHub oferece para gestão de projetos.

Este artigo tem um intuito introdutório. Aqui são apresentadas as ferramentas e recursos destinados à gestão de projetos e exemplificadas etapas iniciais deste processo.

<div id='o-que-e-o-github-projects'></div> 

# O que é o GitHub Projects

O GitHub Projects é um conjunto de ferramentas e recursos oferecidos com o proposito de trazer mais produtividade e organização para os projetos.

Com o quadro de projetos você pode definir fluxos de trabalho, organizar as atividade de cada equipe, acompanhar quem está com determinada tarefa e verificar sempre que possível em que etapa está aquela atividade.

Além dos recursos básicos também é possível automatizar fluxos de trabalho com base em eventos. Este recursos será abordado em outro artigo. Como dito antes este artigo é direcionado à introdução.

<div id='criando-um-projeto'></div> 

# Criando um projeto

Dentro do GitHub temos organizações, equipes e repositórios. Cada um destes pode ter vários projetos vinculados e é possível organizar atividades de várias origens em um único projeto.

<div id='atraves-do-menu-de-organizacao'></div> 

## Através do menu de Organização

Ao acessar a página principal da organização, selecione a opção `Projetos` no menu como mostra o exemplo abaixo:

<!-- creating-project-organization-menu.jpg -->

<div id='atraves-do-menu-de-equipe'></div> 

## Através do menu de Equipe

Na página da equipe encontraremos o menu de opções na parte superior logo abaixo do menu principal. Neste também podemos encontrar a opção `Projetos`. Clique nela para criar um projeto associado a esta equipe.

<!-- creating-project-team-menu.jpg -->

<div id='atraves-do-menu-de-usuario'></div> 

## Através do menu de usuário

No canto superior direito da tela clique na sua imagem de perfil para abrir o menu de opções do usuário e selecione a opção `Seus projetos`

<!-- creating-project-user-menu.jpg -->

<div id='atraves-do-menu-de-repositorio'></div> 

## Através do menu de repositório

Assim como o menu da organização, o menu do repositório está abaixo do menu superior principal. Identifique a opção `Projetos` e clique.

<!-- creating-project-repository-menu.jpg -->

---

Logo após vocÊ estará na página de projetos, clique no botão `Novo projeto` e Selecione a primeira opção:

<!-- selecting-project-style.jpg -->

<div id='sobre-o-modelo-planilha'></div> 

## Sobre o modelo planilha

O modelo planilha é composto por Modelos de visualização, colunas, linhas e células. Aqui as atividades são apresentadas de forma personalizada sendo as colunas inicias definidas como `Título`, `Responsável` e `Estado` da atividade.

<div id='sobre-o-modelo-kanban'></div> 

## Sobe o modelo Kanban

O modelo Kanban (não selecionado) é bem conhecido. Aqui as atividades são apresentadas em forma de cartões com um resumo das informações e ao ser clicado abre uma janela com detalhes da atividade. Ele possui uma estrutura inicial de colunas `Para fazer`, `Em andamento`, `Concluído` que também pode ser modificada para a adição de novas colunas e implementação de automação.

---

<div id='adicionando-uma-atividade-ao-projeto'></div> 

# Adicionando uma atividade ao projeto

Como atividade podemos entender os issues (tarefas, problemas, correções), pull requests (solicitações para integração de código) e atividades escritas manualmente.


<div id='manualmente'></div> 

## Manualmente

Para adicionar manualmente uma atividade ao projeto você pode utilizar o atalho `ctrl`+ `espaço` ou selecionar o ícone de `+` na lateral esquerda das linhas do projeto.

<!-- add-new-item-option.jpg -->

Digite o título da atividade e então pressione `enter` para salvar, logo em seguida vocÊ pode associar alguém ou você mesmo para esta atividade e definir o estado atual dela. Veja o exemplo abaixo:

<!-- manual-activity.jpg -->

<div id='atraves-de-um-issue'></div> 

## Através de um issue

Para adicionar uma atividade através de um issue e/ou associar um issue ao projeto é simples. Antes vamos criar um issue de exemplo acessando o repositório que desejamos associar ao projeto:

<!-- creating-an-issue-on-repository.jpg -->

`NOTA 1`: Observe que estamos na página do repositório, aqui podemos ver o botão `Novo issue`.

Ao adicionar as informações ao nosso issue podemos selecionar no menu lateral direito a opção de projetos e associar este issue a um projeto determinado.

<!-- link-issue-with-project.jpg -->

`NOTA 2`: Caso seu projeto não apareça aqui verifique as permissões do projeto, usuário, organização ou equipe.

Após atribuir o issue a um projeto veremos a opção de marcar o estado atual daquele issue.

<!-- change-issue-status.jpg -->

`NOTA 3`: O mesmo processo pode ser feito para atribuir um pull request ao projeto.

<div id='costumizando-a-visualizacao'></div>

# Costumizando a visualização

Como dito antes podemos costumizar as colunas, células e visualizações (views) de um  projeto para atender ao nosso modelo de trabalho.

<div id='adicionando-colunas'></div> 

## Adicionando colunas

Para adicionar uma coluna precisamos apenas clicar no ícone de `+ ` localizado no final dos títulos das colunas e selecionar se queremos adicionar um campo personalizado (definindo o título e tipo) ou um campo já oferecido pelo projects. No exemplo abaixo selecionaremos um campo já configurado para exibir o nome do repositório. Assim podemos saber qual o contexto da atividade.

<!-- add-repository-column-to-project.jpg -->

E depois de arrastar a coluna para o início o resultado é este:

<!-- result-of-new-repository-column.jpg -->

<div id='adicionando-uma-nova-opcao-a-uma-coluna-existente'></div> 

## Adicionando uma nova opção à uma coluna existente

Vamos adicionar a opção de `backlog` na coluna de `Status` para mapearmos quais atividades estão no pipeline para serem desenvolvidas. Precisamos então clicar no botão ao lado do título da coluna e selecionar `Editar valores`:

<!-- edit-values-of-status.jpg -->

Iremos parar na tela das opções, adicionamos a opção `Backlog` e então podemos salvar as mudanças.

<!-- saving-changes-of-edition-values-of-status.jpg -->

Agora podemos ver que temos uma opção a mais na coluna de status:

<!-- result-of-save-changes-of-edition-of-status-values.jpg -->

`IMPORTANTE`: As modificações que acabamos de fazer ainda não foram salvas então antes de sair da página verifique sempre se há o ícone que nos notifica de mudanças não salvas. Ele fica no cabeçalho da view como no exemplo abaixo:

<!-- unsaved-changes-on-project-view.jpg -->

Para salvar as mudanças, clique no botão ao lado do título da view e selecione a opção de `Salvar mudanças`.

<!-- save-changes-on-project-view.jpg -->

`NOTA 4`: Observe que ao abrir as opções da view podemos também mudar a visualização para o modelo KanBan (Colunas e cartões), Reordenar a visualização, Filtrar e Salvar tudo que fizemos em uma View diferente, mantendo a inicial.

<div id='agora-e-com-voce'></div> 

# Agora é com você

Utilize os recursos do projects nos seus projetos pessoais para exercitar. Isto vai te dar uma experiência sobre os prós e contras e também vai auxiliar você a entender a forma que você trabalha melhor.

# Continue lendo:

[Git e GitHub - Instalação, Configuração e primeiros passos](https://balta.io/blog/git-github-primeiros-passos)

[ASP.NET Deploy com GitHub Actions e Microsoft Azurel](https://balta.io/blog/aspnet-deploy-github-actions-azure)

[GitHub Packages e GitHub Actions - Distribuindo contextos delimitados](https://balta.io/blog/github-packages-github-actions-distribuindo-contextos-delimitados)

## Cursos relacionados

`Gratuito` | [Fundamentos do Git e Azure DevOps](https://balta.io/cursos/fundamentos-git-azure-devops)

<div id='ref'></div> 

## Referências
[Documentação do GitHub](https://docs.github.com/)
