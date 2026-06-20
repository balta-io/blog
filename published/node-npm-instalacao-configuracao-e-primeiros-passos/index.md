Node é um executor de código JavaScript bem popular no mundo da programação. Neste artigo veremos como realizar sua instalação, configuração e primeiros passos.

Índice
------

- [Índice](#índice)
- [Requisitos](#requisitos)
- [Versões do Node](#versões-do-node)
- [Instalação no Windows](#instalação-no-windows)
- [Mudando as políticas do Power Shell](#mudando-as-políticas-do-power-shell)
- [Instalação no Mac](#instalação-no-mac)
- [Instalação no Linux](#instalação-no-linux)
- [Verificando a versão instalada](#verificando-a-versão-instalada)
- [Instalando pacotes com NPM](#instalando-pacotes-com-npm)
- [Iniciando uma aplicação](#iniciando-uma-aplicação)
- [Instalando pacotes locais](#instalando-pacotes-locais)
- [Criando nosso App](#criando-nosso-app)
- [Instalando pacotes globais](#instalando-pacotes-globais)
- [Restaurando os pacotes](#restaurando-os-pacotes)
- [Versões dos cursos](#versões-dos-cursos)
- [Instalando pacotes de versões anteriores](#instalando-pacotes-de-versões-anteriores)

Requisitos
----------

*   Windows, Mac ou Linux
*   [Visual Studio Code](https://balta.io/blog/visual-studio-code-instalacao-customizacao)

Versões do Node
---------------

O Node normalmente é disponibilizado em duas versões, a **Current** que é a versão mais atual e a **LTS** que é a de suporte de longo tempo.

O LTS significa que esta é a versão mais estável que receberá suporte do time do Node até uma nova versão LTS.

Se você está desenvolvendo algum projeto sério, mais maduro, então pode instalar a versão LTS. Se quer tudo que há de mais novo no Node e está em ambiente de desenvolvimento, pode ir de Current.

Não existe um problema grave em utilizar Current em produção, a recomendação do LTS é apenas por precaução, mas depende das suas necessidades.

As versões do Node tendem a ser retro compatíveis, o que significa que se você instalar a última versão, irá conseguir realizar todos os cursos do site normalmente.

O que não pode acontecer é você ter uma versão **ANTERIOR** a da apresentada no curso. Uma versão maior não haverá problemas.

Mantenha sempre o Node atualizado, bastando realizar o processo de instalação novamente. Isto evita possíveis vulnerabilidades em seu ambiente.

Instalação no Windows
---------------------

A instalação do Node no Windows é relativamente simples, basta acessar o [site oficial e fazer download](https://nodejs.org/en/) do instalador. Até a escrita deste artigo a versão **Current** do Node é **_13.11.0_**.

Execute o instalador, siga as instruções na tela e pronto, o Node está instalado e adicionado ao **PATH** do Windows. Feche todos os terminais abertos e você está pronto para prosseguir.

Mudando as políticas do Power Shell
-----------------------------------

Em alguns casos, durante a execução de comandos NPM no Windows, você poderá receber um erro com a seguinte mensagem: *"O arquivo XXXX não pode ser carregado porque a execução de scripts foi desabilitada neste sistema"*

Isto ocorre pois as políticas de execução do Power Shell em seu sistema não estão habilitada. Para resolver este problema, feche todos os terminais abertos e abra um novo Power Shell em modo administrador (Clicar com botão direito do mouse sobre o ícone do Power Shell e acessar a opção **Executar como Administrador**).

Nesta nova janela que se abriu, como administrador, execute o seguinte comando:
    
    Set-ExecutionPolicy -ExecutionPolicy RemoteSigned
    
Feche novamente a janela e reabra o Power Shell ou qualquer terminal que esteja utilizando. Desta vez não precisa ser como administrador. O erro deve parar de acontecer.

Instalação no Mac
-----------------

Embora a instalação do Node no Mac possa ser feita pelo site oficial, recomendamos utilizar o **Brew**, pela facilidade na instalação e atualização dos pacotes do Node.

Vamos então realizar a instalação do Brew, executando o seguinte comando no Terminal.

    /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"

Após o término da instalação, você pode executar o comando abaixo para verificar se tudo está correto.

    brew doctor

De tempos em tempos, também é importante atualizar o Brew e suas dependências, então execute o comando abaixo:

    brew update

Tudo pronto, neste momento o Brew está devidamente instalado e podemos executar a instalação do Node.

    brew install node

Sempre que quiser atualizar a versão instalada do Node, basta executar o comando abaixo.

    brew upgrade node

Instalação no Linux
-------------------

No Linux podemos instalar o Node via apt, mas antes precisamos adicionar seu repositório com CURL.

    curl -sL https://deb.nodesource.com/setup\_13.x | sudo -E bash -

Note que adicionamos a versão 13.x do Node, caso haja uma versão superior, você pode alterar este valor. Em seguida vamos instalar o Node.

    sudo apt install nodejs

Verificando a versão instalada
------------------------------

Sempre que estiver em dúvidas sobre a versão do Node instalada em sua máquina, você pode executar o comando abaixo em um terminal.

    node --version

Instalando pacotes com NPM
--------------------------

O NPM é o gerenciador de pacotes do Node que vem junto em sua instalação. Para verificar a versão do NPM instalada, você pode executar o seguinte comando:

    npm --version

O Node apenas executa os scripts, enquanto o NPM é de onde baixamos os pacotes que desejamos utilizar.

Por exemplo, se você precisa redimensionar uma imagem, você pode usar uma biblioteca já pronta, disponibilizada através do NPM.

Isto acontece em outras tecnologias como o Nuget na Microsoft e o Dart Packages no Flutter.

A maior diferença do NPM é que o Node em si não vem com nada "pronto", então quase tudo que utilizamos são pacotes.

Os pacotes da nossa aplicação ficam armazenados na pasta NODE\_MODULES, na raiz da mesma.

Podemos instalar os pacotes localmente ou globalmente, sendo que os pacotes globais, ficam disponíveis no terminal.

Iniciando uma aplicação
-----------------------

Para dar início a nossa primeira aplicação, vamos abrir um novo terminal e executar os seguintes comandos:

    mkdir nodeapp  
    cd nodeapp  
    npm init

Ao dar sequência no último comando, você será indagado sobre as informações do App.

Neste momento, não precisa preencher nada, apenas apertar ENTER, mas caso fosse uma aplicação mais séria, seria interessante.

Para pular este processo de perguntas e responder tudo como padrão, execute o comando **npm init** com a flag **_\-y_**.

    npm init -y

Instalando pacotes locais
-------------------------

Vamos agora abrir nossa aplicação com o [Visual Studio Code](https://balta.io/blog/visual-studio-code-instalacao-customizacao), e em seguida, abrir um terminal dentro dele.

Neste terminal, vamos executar o comando para instalar um pacote chamado UUID que gera GUIDs.

GUID é a sigla para Globally Unique Identifier, ou seja, ele gera um número único, de 36 caracteres para nós.

    npm install uuid

Note que neste momento uma nova pasta chamada NODE\_MODULES foi criada, com várias sub-pastas.

Isto ocorre por que o Node em si é apenas um executor, ou seja, todo código das bibliotecas que usamos não está no Node e sim nos pacotes que instalamos.

Você NUNCA deve alterar qualquer arquivo da pasta NODE\_MODULES, nem enviar eles para seu repositório (Git/GitHub).

Sempre que você começar a trabalhar com uma aplicação Node já existente, você precisa primeiro baixar seus pacotes.

O Node sabe quais pacotes nossa aplicação precisa, por conta de um arquivo chamado Package.json, que fica na raiz do projeto.

Em adicional, será gerado um arquivo Package-lock.json, contendo apenas as configurações da sua máquina.

Porém, se notarmos o nosso Package.json, não vemos o pacote UUID listado nele, o que é um problema.

Se o Node não traz o pacote e não temos ele definido no Package.json, muito provavelmente, quem baixar nossa aplicação para trabalhar terá um erro.

Lembre-se que NUNCA enviamos a pasta NODE\_MODULES para o Git/Git. Ela ficará gigante com o tempo e isto tomaria muito espaço no servidor.

Além do mais, é mais fácil e seguro baixar os pacotes atualizados das fontes de origem (NPM) do que do nosso repositório.

Desta forma, existem duas formas de instalar pacotes pelo NPM, as locais e as globais.

Sempre que utilizamos o **npm install**, podemos adicionar a flag **_\--save_**, para forçar o salvamento do ID e versão deste pacote no Package.json.

Neste formato, teríamos algo como o seguinte:

    npm install uuid --save

Se executarmos este comando e observarmos o Package.json novamente, veremos que o ID do pacote e sua versão agora foram salvos nas dependências.

As dependências também são sementadas em duas, sendo elas para produção e para desenvolvimento.

No caso do UUID, o pacote é ESSENCIAL para execução da nossa aplicação, então ele é considerado uma dependência.

Sempre que fazemos a publicação das nossas aplicações com Node, também não enviamos a pasta NODE\_MODULES.

Apenas com o código, as ferramentas de publicação conseguem ler o arquivo Package.json e executar a instalação das dependências.

Desta forma, tudo que nossa aplicação depende, deve estar na sessão **DEPENDENCIES** do arquivo Package.json.

Além das dependências, temos as dependências de desenvolvimento, ou **DevDependencies**.

Estas dependências NÃO SERÃO instaladas quando nossa aplicação for publicada. Elas são apenas a nível de desenvolvimento.

Se você tem alguma operação manual durante o desenvolvimento e quer automatizar isto por exemplo, você utilizaria o **devDependencies**.

Para instalar um pacote apenas a nível de desenvolvimento, podemos utilizar a flag **\--save-dev**.

    npm install NOME_DO_PACOTE --save-dev

Você verá ambas instalações, com **\--save** e **\--save-dev** durante os cursos aqui no balta.io.

Criando nosso App
-----------------

Vamos agora criar um arquivo chamado index.js na raiz da pasta aberta no Visual Studio Code com o seguinte conteúdo.

    const uuid = require('uuid');  
    console.log(uuid.v4());

Não vamos nos prender ao JavaScript no momento, mas tudo que estamos fazendo é importando o módulo do UUID e utilizando ele.

Desta forma, abra um terminal e execute o comando:

    node index.js

Você deverá ver uma hash parecida com isto na tela.

    bde7ebc3-9362-48e6-a001-12753fd9de47

Pronto, este é nosso App, toda vez que chamarmos o arquivo index.js ele gerará uma hash randômica para nós.

Instalando pacotes globais
--------------------------

O Node é amplamente usado no mundo do desenvolvimento e alguns CLIs (Command Line Interfaces -- Interface de Linha de Comando) são desenvolvidos com ele.

Sempre que instalamos algum CLI em nosso sistema, um conjunto de comandos é adicionado ao nosso terminal.

Por exemplo, quando instalamos o CLI do Angular, o comando **ng** fica disponível no terminal.

Este comando nos permite criar Apps (ng new APP) ou mesmo adicionar funcionalidades a uma aplicação existente (ng add PACOTE).

Porém, se você notar, não conseguimos executar nossa aplicação fora da pasta em que estamos.

Isto deve-se ao fato da nossa aplicação ser local e ter pacotes locais, o que impede a execução dela em qualquer terminal.

Devemos manter os pacotes da nossa aplicação LOCAIS, porém em casos como citado acima, precisamos do comando disponível GLOBALMENTE.

Sempre que precisarmos instalar um pacote global, utilizamos o flag **\-g**, como por exemplo:

    npm install @angular/cli -g

Neste exemplo, estamos instalando o CLI do Angular e deixando-o disponível para toda a máquina.

Da mesma forma, você verá bastante este tipo de instalação nos cursos por aqui.

Restaurando os pacotes
----------------------

Vamos imaginar que você chegou agora na empresa e já existe um projeto, você apenas continuá-lo.

Então você baixou o projeto do GitHub, e como recomendamos, a pasta NODE\_MODULES não veio.

O que você precisa, sempre que baixa um fonte, é restaurar seus pacotes utilizando o comando abaixo:

    npm install

Desta forma, a pasta NODE_MODULES será baixada e todas as dependências da aplicação serão adicionadas a ela.

Versões dos cursos
------------------

Será comum em sua jornada aqui no balta.io, encontrar cursos com versões anteriores de pacotes.

Por exemplo, atualmente temos cursos do recém saído Angular 9, porém alguns cursos ainda são com Angular 6.

Sempre que você iniciar um curso que tenha um Package.json, a melhor coisa a se fazer é copiar a sessão **dependencies** e **devDependencies**.

Assim você garante que está utilizando as mesmas versões do curso e não terá problema algum.

Instalando pacotes de versões anteriores
----------------------------------------

Caso você opte por seguir o curso desde a instalação e não queira copiar as dependências, você pode instalar as mesmas versões utilizando o comando abaixo:

    npm install pacote@8.3.25

No setup de todo curso, temos as versões utilizadas, assim como no Package.json dos fontes do curso.


<div role="main" id="blog-s1-node-5265a18426e457e29ea1"></div>
<script type="text/javascript" src="https://d335luupugsy2.cloudfront.net/js/rdstation-forms/stable/rdstation-forms.min.js"></script>
<script type="text/javascript"> new RDStationForms('blog-s1-node-5265a18426e457e29ea1', 'UA-48664517-12').createForm();</script>
