**Sumário**
 1. [Contextualizando](#introducao)
 2. [Entendendo o arquivo .gitconfig](#oqueegitconfig)
 3. [Estabelecendo um padrão de organização](#padrao)
 3. [Definindo as configurações específicas](#config)
        1. [Criando gitconfigs individuais](#gitconfigindividual)
        2. [Setando o valor de cada gitconfig](#setandogitconfig)
 3. [Incluindo as configuração no .gitconfig global](#incluindoconfig)
 4. [Conclusão](#conclusao)
 4. [Referências](#referencias)

*******
<div id='introducao'></div> 


## Contextualizando

Em algumas empresas que já trabalhei, precisei usar minha própria máquina para exercer a atividade de desenvolvimento de software. Nesse cenário, por muito tempo, eu tive uma grande dificuldade/questionamento:

*__Como eu poderia separar as configurações do git para os projetos pessoais e para os projetos profissionais?__*

Comecei a buscar por algumas soluções e logo no início, para não desprender o que achava que seria "muito tempo" em configurações, eu adotei a seguinte prática:

todos os projetos devem estar em um local seguro e dentro de um diretório chamado `www`, e toda vez que eu clonar um repo novo, dentro do projeto eu vou configurar localmente coisas como o e-mail e nome que irão na assinatura de cada commit.

E, por muito tempo, fiz assim:

- Clonava um repositório
- Acessava o repositório
- efetuava as configs, como: 

```bash
$ git config user.name "Xiquinho da silva"
$ git config user.email "oi@xiquinhodasilva"
```

Um belo dia, reparei que na verdade isso era muita perda de tempo, e era muito mais coisa... Era **CHATO**. Resolvi voltar a pesquisar com um único objetivo **"dar voz a minha preguiça interior e fazer essas configurações uma única vez e esquecê-las depois..."**

Felizmente, eu consegui e é isso quero compartilhar por aqui!

###### AVISO
<blockquote>
    Todas configurações aqui apresentadas foram feitas usando uma distro Linux. Portanto, você vai conseguir executar todos os passos em qualquer distro Linux, em um Mac e no Windows creio que apenas se seu workflow estiver dentro do WSL!
</blockquote>

<blockquote>
    Sinta-se a vontade para complementar este artigo com uma maneira de implementar essas configurações em um ambiente Windows sem WSL.
</blockquote>

****
<div id='oqueegitconfig'></div> 

## Entendendo o arquivo .gitconfig 

Este é um arquivo oculto responsável por armazenar as configurações globais ou locais do git em sua máquina, é nele que você vai encontrar, por exemplo, o nome que você definiu que deve ser exibido no log dos commits, o seu e-mail, é nele que você cria os aliases do git, etc. Você consegue manipular os valores deste arquivo utilizando o comando abaixo:

```bash
$ git config --local || --global || --system config value
```

Este arquivo vai ser o responsável por chamar as configurações que nós queremos para cada projeto, conforme veremos mais adiante.

<div id='padrao'></div> 

## Estabelecendo um padrão de organização

Para alcançar o nosso objetivo, precisamos antes definir um modelo de organização para nossos projetos, aqui vai uma sugestão:

- Crie um diretório chamado `www` em um local de sua preferência;
- Acesse este diretório e agora crie um diretório para sua empresa e outro para projetos pessoais

```bash
$ mkdir side-projects && mkdir empresa-qualquer
```

Isso é necessário, pois faremos com que o git sete uma configuração específica para cada um destes diretórios, dessa maneira, cada projeto que você clonar para `side-projects` terá uma configuração diferente do que foi aplicado para `empresa-qualquer`.

###### AVISO
<blockquote>
    Aqui estou usando dois diretórios apenas, mas você pode ter quantos você quiser, ok? Segue o fio...
</blockquote>

<div id='config'></div> 

## Definindo as configurações específicas

<div id='gitconfigindividual'></div> 

##### Criando gitconfigs individuais

Volte para raíz de seu usuário, você pode usar o comando abaixo:

```bash
$ cd $HOME
```
ou
```bash
$ cd ~
```

Estando na raíz de seu usuário, devemos criar agora um arquivo .gitconfig para cada um dos diretórios que você criou dentro de `www` no passo anterior, você pode usar o comando abaixo:

```bash
$ touch .gitconfig-side-projects && touch .gitconfig-empresa-qualquer
```
<div id='setandogitconfig'></div> 

##### Setando o valor de cada gitconfig

Agora, acesse esses arquivos criados através de seu terminal ou de um editor de texto de sua preferência. Eu usarei o querido vscode, podemos executar o seguinte comando:

```bash
$ code .gitconfig-side-projects
```
Com o arquivo aberto, é hora de começar nossa configuração, neste artigo vamos abordar tópicos simples como nome e e-mail, vamos lá!

copie e cole dentro do arquivo, o trecho abaixo:

```bash 
[user]
    name = Seu Nome
    email = seuemail@email.com
```
Salve e retorne para raíz de seu usuário.

Repita o processo para todos os outros gitconfigs criados, lembrando de cada um deve ter uma configuração específica.


<div id='incluindoconfig'></div> 

## Incluindo as configurações no gitconfig global


Acesse através de seu terminal ou de um editor de texto de sua preferência o arquivo chamado **.gitconfig**.

###### AVISO
<blockquote>
    O arquivo .gitconfig está localizado na raíz de seu usuário
</blockquote>

Copie e cole dentro do arquivo (a partir da última linha) o trecho abaixo: 

```bash 
[includeIf "gitdir:~/www/side-projects/"]
  path = ~/.gitconfig-side-projects
[includeIf "gitdir:~/www/empresa-qualquer"]
  path = ~/.gitconfig-empresa-qualquer  
```

Salve e retorne para raíz de seu usuário.

Basicamente, o que nós fizemos foi adicionar ao arquivo de configuração global do git condicionais que serão responsáveis por identificar qual o diretório em que um determinado projeto, que contenha o git inicializado, está e com isso setar o path específico para ele.

Como resultado, a cada commit que você realizar dentro, por exemplo, de `~/www/side-projects/` o que estará assinado como nome e e-mail foi o que você declarou no `.gitconfig-side-projects`.

Legal, não é?

<div id='conclusao'></div> 

## Conclusão

Agora, você tem um ambiente pronto para receber N projetos e não vai precisar se preocupar com configurações iniciais do git. Isso vai conferir a você um pouco mais de agilidade e praticidade no dia a dia.

<div id='referencias'></div> 

## Referências

[Confira aqui como está o meu arquivo de configuração global do git](https://gist.github.com/liandro-wesley/b569e935975a89f5c4096d6dc824eea9)
