O Docker é uma forma de abstração da nossa infraestrutura que nos beneficia tanto em tempo de desenvolvimento quanto em produção.

Índice
------

*   [O que é o Docker?](#o-que-é-o-docker)
*   [Docker para Desenvolvedores](#docker-para-desenvolvedores)
*   [Requisitos](#requisitos)
*   [Versões do Docker](#versões-do-docker)
*   [Criando a Conta](#criando-a-conta)
*   [Instalando o Docker](#instalando-o-docker)
*   [Executando o Docker](#executando-o-docker)
*   [Testando a Instalação](#testando-a-instalação)
*   [Listando as Imagens](#listando-as-imagens)
*   [Listando os Contêineres](#listando-os-contêineres)
*   [Publicando uma API](#publicando-uma-api)
*   [Criando uma Imagem](#criando-uma-imagem)
*   [Rodando o Contêiner](#rodando-o-contêiner)
*   [Acessando os Logs](#acessando-os-logs)
*   [WSL 2 (Somente Windows)](#wsl)

## O que é o Docker?

Com certeza você já ouviu uma famosa frase 'Na minha máquina funciona'... então se na sua máquina funciona, por que não enviar ela para produção?

Pois bem, o que o **Docker** faz é parecido com isto. Em termos gerais ele abstrai a infraestrutura das aplicações através de imagens prontas.

O que temos são basicamente '_máquinas virtuais_' de baixo custo rodando somente com a infraestrutura das nossas aplicações.

Por exemplo, uma das imagens mais famosas que temos no **Docker** é a [Alpine](https://hub.docker.com/_/alpine), que contém apenas **5MB**.

Esta imagem contém apenas o Core do sistema operacional, no caso Linux, que a maioria das aplicações precisam para serem executadas.

A ideia é que a partir dela, você possa adicionar o que mais precisar, como a SDK do .NET, Node, PHP e depois fazer um pacote disto.

Com este pacote montado, você envia ele completo, do jeito que está, para produção, assim não corre o risco de dar algo errado.

## Docker para Desenvolvedores

Embora o Docker seja voltado para infraestrutura, um dos seus grandes usos também são ambientes de desenvolvimento.

Hoje trabalhamos com diversos bancos de dados, diversas tecnologias e serviços, isto tudo gera um acúmulo de instalações muito grandes.

Imagina instalar o SQL Server, MySQL e MongoDb, todos na sua máquina, sendo executados como serviços do Windows. É complicado.

Neste caso, o que podemos fazer é utilizar imagens prontas de máquinas com SQL Server, MySQL e Mongo e simplesmente executá-las quando quisermos.

Quer criar um projeto com SQL Server? Só fazer o download da imagem oficial dele para Docker, executar e pronto, temos o serviço rodando.

O Docker funciona para Mac, Windows e Linux, e no caso do Windows, recomendamos utilizar a versão 10, pois ela tem suporte à subsistemas Linux, que são a base do Docker.

## Requisitos

Aqui vem a parte mais chata do Docker, os requisitos. Embora as questões de memória e CPU variem de acordo com a quantidade de images que você tem executando, o disco sempre será necessário.

Embora você possa fazer o download da imagem que quiser a qualquer momento, é muito mais prático manter as imagens locais.

Desta forma você economiza banda e tempo, porém perde em disco. Fica a seu critério manter ou baixar novamente as imagens.

Não são recomendações oficiais, mas preferencialmente tenha pelo menos um processador **Dual Core**, e **8GB** de Ram.

Quanto mais disco você tiver melhor, em casos de discos de _128GB_, com pouco espaço, você pode excluir as imagens sempre que não estiver mais utilizando.

A instalação inicial do Docker vai tomar por volta de **2GB de disco**.

**WINDOWS ONLY**  
Caso esteja utilizando sistema operacional Windows, recomendamos fortemente o uso do Windows 10 pois ele possui suporte a subsistemas Linux, que são a base do Docker.

## Versões do Docker

Atualmente, o Docker é divido em dois produtos, _Community Edition (CE)_ e _Enterprise Edition (EE)_.

Como os nomes sugerem, o Community é **gratuito** e voltado a comunidade, enquanto o Enterprise é recomendado para uso empresarial.

Mas não se preocupe com isto neste momento, tudo que precisamos para trabalhar com Docker é fornecido pela versão Community que é gratuita.

## Criando a Conta

A primeira coisa que precisamos fazer é [criar uma conta no Docker Hub](https://hub.docker.com/signup), ela vai ser utilizada tanto para ativação do Docker em nossa máquina quanto para gerenciar nossas imagens.

Caso já tenha uma conta criada, pode prosseguir para o passo seguinte, do caso contrário, navegue até o site oficial do [Docker](https://docker.com/), clique em **Sign In** e em seguida em **Sign Up**.

Preencha todas as informações necessárias e pronto. É importante lembrar que eles verificarão sua conta via E-mail, então não esqueça este passo também.

## Instalando o Docker

O Docker possui uma versão para Desktop que irá facilitar nossa vida. Se você gosta de linha de comando, não se preocupe pois ele também fará a instalação do CLI.

Vamos então acessar a [página de início do Docker](https://www.docker.com/get-started) e clicar em **_Download Desktop and Take a Tutorial_**. Isto vai nos levar a página de Downloads.

Selecione a versão que atende seu sistema operacional, como **Docker for Windows** ou **Docker for Mac** e na tela seguinte selecione **_Get Docker_**.

Siga a instalação dos pacotes para realizar todo o processo. Recomendamos que ao término do mesmo reinicie seu computador.

**WINDOWS ONLY**  
Durante a instalação do Docker para Windows, você será questionado sobre utilizar contêineres Windows ao invés de Linux.

Fique à vontade em testar esta opção, mas para utilização do Docker em nossos cursos, recomendamos que **NÃO DEIXE-A MARCADA**. Nos cursos **_sempre vamos utilizar contêineres Linu_**x como padrão.

## Executando o Docker

Após reiniciar o computador, inicie o Docker caso o mesmo não tenha sido iniciado. O nome da aplicação no sistema ficará como Docker mesmo.

Você saberá que ele está em execução caso o ícone do Docker (uma baleia) esteja próximo ao relógio (System Tray).

No primeiro acesso você será indagado para autenticar utilizando seu **_Docker ID_** criado previamente neste artigo.

Recomendamos fortemente que faça o login, e caso tenha perdido esta primeira tela, basta clicar novamente no ícone do Docker (baleia) e selecionar a opção **Sign In**.

Isto é tudo que precisamos para ter o Docker rodando em nossa máquina. Em adicional recomendamos que clique novamente no ícone do Docker e vá até a opção **Settings**.

Na aba **General**, desmarque a opção **_Start Docker for Desktop when you Login_**.

Isto fará com que o Docker não seja iniciado junto com o sistema operacional, tornando a inicialização mais rápida.

Caso tenha uma máquina mais poderosa e queira deixar esta opção marcada, fique à vontade.

## Testando a Instalação

Ao instalar o Docker automaticamente fazemos a instalação do seu CLI, que disponibiliza o comando **docker** em nosso terminal.

Desta forma, abra um terminal e digite o seguinte comando para ver a versão do Docker instalada.

    docker --version

Se estiver no Windows, recomendamos fortemente o [Windows Terminal](https://www.microsoft.com/pt-br/p/windows-terminal-preview/9n0dx20hk701) ou Power Shell.

Vamos então executar nosso famoso Hello World, que vai baixar e executar um contêiner para testarmos se tudo está funcionando corretamente.

    docker run hello-world

Ao executar o comando, veremos a mensagem '**_Unable to find image hello-world:latest locally_**', o que significa que ele não conseguiu encontrar nenhuma imagem chamada hello-world localmente.

Faz todo sentido, afinal não fizemos download de nada ainda, mas mesmo assim, ele fará uma busca online e encontrará esta imagem.

Seguindo adiante ele fará o download e execução da imagem, exibindo a mensagem '**_This message shows that your installation appears to be working correctly._**' caso tudo tenha dado certo.

## Listando as Imagens

Durante a execução destes primeiros comandos, duas coisas muito importante aconteceram, o download da imagem e a criação do contêiner.

Uma imagem é a informação crua que temos enquanto o contêiner é uma representação desta imagem local.

Os contêineres se baseiam nas imagens, mas nunca executamos as imagens, sempre os contêineres.

Desta forma, temos uma imagem do **hello-world**, que é uma base que temos para construir diversos contêineres.

Para listar as imagens que temos localmente é só executar o comando abaixo.

    docker image ls

Como você deve imaginar, estas imagens ocupam espaço em disco, e podemos removê-las e baixá-las novamente a qualquer momento.

No caso, para remover uma imagem baixada, podemos utilizar o comando abaixo.

    docker image rmi ID_DA_IMAGEM

É importante frisar que uma imagem não poderá ser apagada caso esteja em uso.

## Listando os Contêineres

Para listar os contêineres podemos utilizar o comando abaixo, porém neste caso o hello-world não aparecerá.

    docker container ls

Isto ocorre pelo hello-world ser um contêiner de testes apenas, então para vê-lo listado, precisamos executar o comando com --all no final.

    docker container ls --all

Desta forma você verá todos os contêineres que estão na máquina.

### Iniciando e parando um contêiner

Para executar ou para um contêiner, podemos usar o comando start/stop.

    docker container start ID_DO_CONTAINER
    docker container stop ID_DO_CONTAINER

Para ver a lista completa do que podemos fazer com um contêiner basta executar o comando abaixo.

    docker container --help

### Removendo um contêiner

Com o contêiner parado, podemos removê-lo utilizando o comando abaixo. Em seguida podemos também executar o comando para remover a imagem baixada.

    docker container rm ID_DO_CONTAINER

## Publicando uma API

Chegou a hora do desafio final, vamos publicar uma API criada utilizando Node/JavaScript em um contêiner e torná-la acessível pelo nosso Browser.

Para executar esta parte do artigo você precisará do [Git](https://git-scm.com/) e do [Visual Studio Code](https://code.visualstudio.com/) instalados em sua máquina.

Abra um terminal e navegue para uma pasta segura, sem caracteres especiais ou com caminho muito longo e execute o seguinte comando.

    git clone https://github.com/andrebaltieri/docker-sample-api.git

Este comando fará o download do código fonte de uma API de exemplo que está no nosso GitHub, gerando assim uma nova pasta chamada **docker-sample-api**.

### Docker File

Com o código baixado, precisamos gerar um contêiner para nossa aplicação, e neste processo informar alguns detalhes.

O **Docker File** é o arquivo de instruções que o Docker utilizará para saber como publicar nossa aplicação em um contêiner, bem como qual imagem ele deve utilizar.

Abra pasta **docker-sample-api** com o **Visual Studio Code** e vamos criar um arquivo chamado **_Dockerfile_** na raiz.

Note que este arquivo não tem extensão alguma, é apenas **Dockerfile** mesmo.

O primeiro passo que precisamos fazer neste arquivo é definir qual imagem vamos utilizar em nossa aplicação.

Lembre-se que a imagem é a base para construção, contendo apenas o necessário do SO para execução da nossa aplicação.

Neste caso, vamos utilizar a [imagem oficial do Node para Docker](https://hub.docker.com/_/node/). Você pode encontrar imagens oficiais de diversas empresas no [Docker Hub](https://hub.docker.com/).

Para definir que estamos utilizando uma imagem como base, utilizamos a palavra **FROM**, seguida da imagem que queremos utilizar.

    FROM node:current-slim

Note que após o nome da imagem temos dois pontos e **current-slim**. Na verdade o que vem depois dos dois pontos é a TAG da imagem.

As tags são utilizadas para definir versões destas imagens e no caso, **current** diz que estamos pegando a versão mais atual do Node (_LTS - Long Term Support_) e **slim** diz que é a versão enxuta.

Cada imagem tem suas tags, então você deve sempre olhar no Docker Hub pela versão que precisa utilizar.

A segunda definição que iremos fazer em nosso arquivo será o caminho, dentro da imagem, onde ficará o código fonte da nossa aplicação.

Isto é feito utilizando a palavra **WORKDIR**, como mostrado abaixo.

    WORKDIR /usr/src/app

Agora precisamos copiar o arquivo **_Package.json_**, que contém os pacotes necessários para execução da nossa API.

Lembre-se que no caso do Node, não precisamos instalar uma SDK ou algo do tipo, porém toda aplicação tem a pasta **node_modules** com as bibliotecas que utilizamos durante a construção do App.

Para executar esta cópia vamos utilizar a palavra COPY, como mostrado abaixo.

    COPY package.json .

Note que no fim do comando há um '.', definindo que o arquivo será copiado para raiz da nossa aplicação definido no **WORKDIR**.

Com tudo pronto, vamos executar o comando **_NPM INSTALL_** para instalar todos os pacotes que precisamos, e isto é feito utilizando a palavra **RUN**.

    RUN npm install

Até o momento temos o código da nossa aplicação sendo copiado para a imagem e em seguida sendo instalados os pacotes que a API precisa para rodar.

Porém, precisamos definir em qual porta esta API irá rodar, e isto é feito pela palavra EXPOSE, como mostrado abaixo.

    EXPOSE 8080

Neste caso, estamos expondo a API na porta 8080, porém fique à vontade em alterar esta porta.

É importante lembrar que nossa API, internamente (Dentro da imagem) roda na porta **3000**. Isto está definido na _linha 15 do arquivo bin/www_.

Embora tenhamos copiado os arquivos e instalado os pacotes, nossa API ainda não está executando, e para isto precisamos de fato executar o comando **NPM START**.

Desta forma, vamos utilizar a palavra **CMD** que nos permite passar uma lista de comandos a serem executados.

    CMD ['npm', 'start']

Por fim, vamos executar mais um COPY para copiar o resto da nossa aplicação para a imagem.

    COPY . .

Note que utilizamos o mesmo esquema anterior, copiando os arquivos da raiz ('.') da nossa aplicação para a raiz ('.') da imagem.

O arquivo final **Dockerfile** fica com o seguinte conteúdo.

    # Copy the file from your host to your current location
    COPY package.json .
    
    # Run the command inside your image filesystem
    RUN npm install
    
    # Inform Docker that the container is listening on the specified port at runtime.
    EXPOSE 8080
    
    # Run the specified command within the container.
    CMD [ "npm", "start" ]
    
    # Copy the rest of your app's source code from your host to your image filesystem.
    COPY . .



## Criando uma imagem

Nosso desafio agora é criar uma imagem customizada a partir desta API/'Dockerfile que criamos. Vamos juntar a imagem padrão definida no **Dockerfile** com o restante da execução do script.

Dentre os comandos que podemos executar, temos o **_docker image build_**, para criar uma imagem, e vamos utilizá-lo para gerar o que precisamos aqui.

Certifique-se que você está na raiz da API, a pasta **_docker-sample-api_** e execute o seguinte comando.

    docker image build -t dockerapi:1.0 .

Para facilitar futuras execuções, vamos dar um nome para esta imagem, adicionando o parâmetro **-t**, seguido pelo **_nome:versão_** da imagem.

Por fim, devemos especificar o diretório onde está nosso Dockerfile, que no caso é a raiz da nossa API, definido no comando pelo ".".

Para verificar se tudo deu certo, basta executar o comando **docker image ls** para listar as imagens que temos na máquina.

## Rodando o contêiner

A última coisa que precisamos fazer é pegar esta imagem gerada e executar ela como um contêiner, e isto é feito pelo comando **docker container run**.

Novamente, certifique-se que você está na raiz da API, a pasta **_docker-sample-api_** e execute o seguinte comando.

    docker container run --publish 8080:3000 --detach --name api dockerapi:1.0

O primeiro parâmetro que temos no comando é o **--publish**, que vai fazer o redirecionamento do tráfego para a porta onde nossa API está rodando.

Neste caso, vamos rodar nosso contêiner na porta **_8080_**, ou seja, acessaremos nossa API no Browser pela URL _http://localhost:8080_, porém, internamente nossa API está rodando na porta **_3000_**.

O **--publish** cuidará exatamente deste redirecionamento de tráfego para nós. Você também pode alterar a porta de execução para outra qualquer, diferente da 8080 se quiser.

O **--detach** diz para o Docker executar este contêiner em background, e não vai travar o processo do nosso terminal ou Visual Studio Code que acabamos de executar.

O **--name** especifica o nome do contêiner, e fica a seu critério colocar qualquer nome que quiser, desde que não tenha espaços ou caracteres especiais.

Neste momento você pode abrir o browser no endereço _http://localhost:8080_ para ver a mensagem **_'Docker Node API Running'_** na tela, o que significa que nossa API está rodando.

Por fim temos o nome da imagem especificado, que no caso é a que acabamos de criar. Agora você pode usar os comandos _**docker container ls**_ para ver este contêiner em execução, além do _**docker container start ID**_ e **_docker container stop ID_** para iniciar ou parar ele.

## Acessando os Logs

Para visualizar os logs de execução da API você pode clicar no ícone do Docker e ir na opção Dashboard. Uma nova tela se abrirá listando o contêiner que acabamos de criar.

Clique sobre o contêiner e em seguida clique na opção LOGS para visualizar tudo que está sendo executado neste contêiner.

## Conclusão

Criar e executar imagens e contêineres no Docker não é uma tarefa difícil. Neste artigo vimos a instalação e primeiros passos para 'conteinerizar' nossas aplicações, mas ainda há muito mais a se aprender sobre Docker.

# Atualizações

<small>24/08/2020</small>

## WSL 2 (Somente Windows)

O WSL é um subsistema Linux que roda dentro do Windows, permitindo que o Docker seja executado mais rápido e de forma otimizada.

Recomendamos fortemente que utilize o WSL 2 caso possível. Isto vai garantir uma performance e estabilidade melhor.

O ideal é habilitar o WSL 2 **antes** de instalar o Docker, para isto basta acessar este link e realizar a instalação: [https://balta.io/blog/wsl](https://balta.io/blog/wsl)

Desta forma, ao realizar a instalação do Docker, na primeira tela irá aparecer a opção abaixo, que **deve ficar** marcada.
```
Enable WSL 2 Windows features
```

Caso já tenha o Docker instalado e queria apenas habilitar a opção de rodá-lo via WSL, você pode alterar as opções em <code>Settings > Resources > WSL Integration > Enable integration with my default WSL Distro</code>.

<small>16/03/2021</small>
## Kitematic

Nas últimas versões do Docker, o Kitematic foi descontinuado, dando espaço a um novo Dashboard que listará seus contêineres.

No entanto, esta nova versão não permite que você navegue e baixe imagens, este primeiro passo precisa ser via linha de comando.

Para rodar o SQL Server por exemplo, teríamos que via linha de comando executar o comando para baixar a imagem do SQL Server:

```
docker pull mcr.microsoft.com/mssql/server
```

E em seguida executar o comando para iniciar o contêiner baseado na imagem que acabamos de baixar.

#### Mac & Linux
```
docker run --name sqlserver -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=1q2w3e4r@#$" -p 1433:1433 -d mcr.microsoft.com/mssql/server
```
#### Windows
```
docker run -v ~/docker --name sqlserver -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=1q2w3e4r@#$" -p 1433:1433 -d mcr.microsoft.com/mssql/server
```

A partir deste ponto, esta imagem fica disponível visualmente quando acessamos o menu **Dashboard**, clicando com o botão direito do mouse sobre o ícone do Docker.

## Artigos relacionados
* [Instalação do SQL Server via Docker](https://balta.io/blog/sql-server-docker)
* [Instalação do MongoDb via Docker](https://balta.io/blog/mongodb-docker)


<div role="main" id="blog-s1-docker-27636d903305b429071d"></div>
<script type="text/javascript" src="https://d335luupugsy2.cloudfront.net/js/rdstation-forms/stable/rdstation-forms.min.js"></script>
<script type="text/javascript"> new RDStationForms('blog-s1-docker-27636d903305b429071d', 'UA-48664517-12').createForm();</script>