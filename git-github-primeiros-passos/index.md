Git é um sistema de controle de versões distribuídas, enquanto GitHub é uma plataforma que tem o Git como base para versionar nossos códigos.

Índice
------

*   [Requisitos](#requisitos)
*   [O que é versionamento de código?](#o-que-é-versionamento-de-código)
*   [O que é o Git?](#o-que-é-o-git)
*   [O que é o GitHub?](#o-que-é-o-github)
*   [Seu Portifólio](#seu-portifólio)
*   [Criando sua conta](#criando-sua-conta)
*   [Instalação do Git](#instalação-do-git)
*   [Configuração](#configuração)
*   [Criando um Repositório](#criando-um-repositório)
*   [Iniciando um Repositório](#iniciando-um-repositório)
*   [Adicionando Itens](#adicionando-itens)
*   [Ligando os Repositórios](#ligando-os-repositórios)
*   [Branchs](#branchs)
*   [Enviando Arquivos](#enviando-os-arquivos)
*   [Atualizando Arquivos Locais](#atualizando-os-arquivos-locais)
*   [Utilizando a integração com Visual Studio Code](#utilizando-a-integração-com-visual-studio-code)
*   [Clonando um Repositório](#clonando-um-repositório)

Requisitos
----------

*   Windows, Mac ou Linux
*   [Visual Studio Code](https://balta.io/blog/visual-studio-code-instalacao-customizacao)

O que é versionamento de código?
--------------------------------

Quando dizemos que estamos versionando algo, dizemos que estamos criando algo novo ou alterando algo que já existe, e isto ocorre o tempo todo no mundo do Desenvolvimento de Software.

Temos Android 10, Windows 10, MacOs 13 e por aí vai, cada software tem sua versão, com suas especificidades, com suas novidades.

Porém quando versionamos nossos códigos, temos além desta visão, uma maior, a da gestão de mudanças e possibilidade de voltar no tempo.

Imagina que você começou a trabalhar em uma nova funcionalidade do seu sistema, mas algumas horas após o início e muitas linhas de código, desistiu de fazer desta forma.

Como desfazer tudo que já foi feito? Ou pior, vamos imaginar que a versão nova do sistema tem um defeito crítico e precisamos voltar para versão anterior?

Quando dizemos que estamos versionando o código, queremos dizer que estamos tirando "fotografias" do nosso código e depois podemos olhar para elas e voltar para aquele momento.

Neste momento, você deve ter percebido que quanto mais fotografias tivermos, mais podemos voltar no tempo, ou seja, se a cada mudança você "tira uma foto" acaba sendo melhor do que tirar uma foto apenas no final do dia de trabalho.

O versionamento de código é tão comum e fácil de utilizar, que até nos exemplos de nossos códigos e cursos nós utilizamos. É um recurso que traz muita praticidade com pouco esforço.

O que é o Git?
--------------

O Git é um sistema de controle de versão criado pelo **_Linus Torvalds_**, criador do Linux, durante a construção do mesmo.

Embora existam outros sistemas como **SVN**, o Git acabou ganhando muita popularidade e basicamente assumiu o papel de principal sistema de controle de versão.

Atualmente o Git é abstraído por sistemas como GitHub, GitLab e Azure DevOps, que fornecem uma plataforma sob o sistema, facilitando seu uso em nosso dia-a-dia.

Em suma, você não precisa se preocupar tanto em como o Git funciona por baixo dos panos e sim aprender os conceitos e como utilizar alguma das plataformas que oferecem suporte ao mesmo.

O que é o GitHub?
-----------------

O [GitHub](https://github.com/) é a ferramenta que nos provê acesso ao Git para armazenarmos nossos códigos com todo poder do versionamento que o Git oferece.

O GitHub é uma plataforma completa e conta com muitos recursos, além de ser gratuito para a maioria dos serviços, incluindo todos que vamos utilizar aqui.

O GitHub foi comprado pela Microsoft em 2018, oferecendo ainda mais recursos gratuitos tanto para profissionais quanto para estudantes de tecnologia.

Seu Portifólio
--------------

O GitHub também é conhecido por ser o portifólio dos desenvolvedores, afinal é nele que ficam os códigos que produzimos.

É normal durante um processo seletivo, você ser indagado a fornecer a URL do seu perfil no GitHub, para analisarem seus códigos e atividades.

Particularmente, achamos muito importante manter a conta sempre ativa, sempre publicar projetos ou testes que você faz lá.

Isto demonstra que você está sempre estudando, evoluindo e que é uma pessoa dedicada. Não tenha medo ou vergonha, use e abuse do GitHub.

Lembre-se de escolher um bom nome de usuário também, algo que remeta sua realidade e profissionalismo, evite criar usuários com apelidos ou nomes que possam ser entendidos como algum tipo de ofensa.

Criando sua conta
-----------------

Tudo que precisamos para começar com o Git e GitHub é uma conta na ferramenta, e isto pode ser feito em [seu site oficial, sem custo algum](https://github.com/join).

Se tudo deu certo, você receberá um E-mail para confirmar sua conta. Não esqueça de realizar este processo de ativação, ele é importante para iniciar sua conta.

Instalação do Git
-----------------

Embora possamos realizar todas as tarefas que precisamos diretamente pelo GitHub, com sua interface visual, é muito comum utilizarmos a famosa interface de linha de comando (CLI).

O Git possui um CLI bem completo, que nos permite interagir não apenas com GitHub mas também com GitLab e outros mais.

Vamos então acessar a [página oficial do Git](https://git-scm.com/) e fazer o Download do instalador, que possui versões para Windows, Mac.

Caso esteja no Linux, você pode utilizar os comandos abaixo no seu terminal para instalação.

    // Debian
    sudo apt update
    sudo apt upgrade
    sudo apt install git

    // Red Hat
    sudo yum upgrade
    sudo yum install git

Abra um novo terminal e execute o seguinte comando para verificar se a instalação foi realizada com sucesso.

    git --version

Se tudo estiver funcionando, você verá a versão do Git instalada em sua máquina e o comando **git** estará disponível para continuarmos nossos estudos.

Configuração
------------

Nosso próximo passo é configurar nossa conta para trabalhar com o Git, e isto é feito pelo comando **git config**, onde podemos informar nosso nome e E-mail.

Como estas configurações são globais, também devemos adicionar a opção --global ao comando, seguida dos valores entre aspas.

    git config --global user.name "Andre Baltieri"
    git config --global user.email "meuemail@github.com"

Executando estes dois comandos, temos nosso nome e E-mail armazenados e a cada alteração que fizermos eles serão automaticamente adicionados.

Criando um Repositório
----------------------

Agora começa a parte legal, vamos novamente até a página do [Github e vamos criar nosso primeiro repositório](https://github.com/new). Repositório é o nome que damos ao nosso "projeto" no Github (Ou outras ferramentas de versionamento).

Preencha o campo **Repository Name** com um nome sem espaços ou caracteres especiais, como por exemplo "meu-primeiro-repo". O campo descrição é opcional.

Deixe a opção **Public** marcada e clique no botão **Create Repository** para criar nosso primeiro repositório no GitHub.

Sempre que marcamos um repositório como público, ele ficará visível para o mundo, ou seja, quem acessar sua página no GitHub verá todo conteúdo do seu código.

Cuidado para não enviar informações sensíveis como a senha do seu banco de dados para o GitHub em repositórios públicos, isto poderia te causar sérios problemas.

Você pode criar repositórios privados também, que só você ou pessoas que você definir tem acesso, porém eles são limitados em quantidade.

O repositório que acabamos de criar sempre estará disponível no endereço https://github.com/USUARIO/REPOSITORIO que no nosso caso é https://github.com/USUARIO/meu-primeiro-repositorio.

Por enquanto, isto é tudo que queremos fazer no GitHub, vamos agora mover para nossa máquina e dar início a integração com ele.

Iniciando um Repositório
------------------------

Para começar a brincar com o Git/GitHub, vamos criar uma pasta em um local seguro. Para isto, abra um terminal de sua preferência e execute os comandos:

    mkdir meurepo  
    cd meurepo  
    code .

Isto fará a criação de uma nova pasta chamada **meurepo** e abrirá a mesma com o _Visual Studio Code_. Caso não tenha o VS Code instalado e adicionado ao PATH, [siga nosso guia de instalação](https://balta.io/blog/visual-studio-code-instalacao-customizacao).

Em adicional, caso tenha problemas em executar o VS Code pelo termina, simplesmente abra-o, vá em **File > Open Folder** e selecione a pasta que acabamos de criar.

Com o Visual Studio Code aberto na pasta que criamos, vamos criar um novo arquivo chamado de **README.md** na raiz.

Por padrão, todo repositório no GitHub tenta buscar um arquivo README.md na raiz do projeto para exibi-lo na página inicial do repositório.

Os arquivos ".md" são no formato MarkDown, que é uma espécie de linguagem de marcação, assim como o HTML.

Abra o arquivo README.md e coloque o seguinte código nele.

    Meu Primeiro Repositório 
    ======================== 

    O Git/GitHub são legais!

Não se preocupe em entender o MarkDown neste momento, depois você pode entrar em mais detalhes dele por sua conta.

Até o momento, temos nosso repositório lá no GitHub e apenas uma pasta com um arquivo aqui, mas eles ainda não estão ligados de nenhuma forma.

Na verdade, nossa pasta local, que chamamos de repositório local, não está nem iniciada ainda. Precisamos primeiro dizer que nossa pasta é um repositório local.

Para isto, utilizamos o comando **init** do Git, para definir que temos um repositório aqui. Por baixo dos panos, ele criar uma pasta ".git" com o que ele precisa para versionar este projeto.

Não precisamos nos preocupar inicialmente com esta pasta gerada, aliás, elas nem vai aparecer para nós, é ocultada automaticamente e não devemos mexer nela.

Ainda no Visual Studio Code, pressione **_CTRL (CMD)+\`_** para abrir o terminal ou acesse o menu **Terminal > New Terminal**.

Na nova janela que se abrirá, execute o seguinte comando.

    git init

Se tudo deu certo, você receberá a mensagem _"Initialized empty Git repository in C:/dev/meurepo/.git/"_. Pronto, temos nosso repositório iniciado.

Note que podemos executar estes comandos em qualquer terminal **FORA** do Visual Studio Code também. Só realizamos por dentro do VS Code para facilitar.

Adicionando Itens
-----------------

Com tudo pronto, antes de ligarmos e enviarmos as informações locais para nosso servidor, precisamos informar para o Git quais arquivos queremos enviar.

Em projetos maiores, você terá MUITOS arquivos, e nem todos você pode querer enviar para o Git, então existe um comando chamado **add** que nos permite especificar quais arquivos queremos enviar.

Desta forma, vamos adicionar o README.md executando o seguinte comando:

    git add .\README.md

Caso esteja trabalhando com muitos arquivos e queira adicionar TODOS de uma única vez, você pode usar o comando:

    git add --all

Desta forma, informamos ao projeto os arquivos que estão sendo gerenciados pelo Git e que possívelmente vão para nosso servidor.

Comitando
---------

Você provavelmente já ouviu este termo no mundo da programação... "Vou comitar", "Comita aí"... pois bem, o termo **commit** em português significa comprometer.

Quando executamos um Commit, estamos de fato, nos comprometendo com todas as mudanças que fizemos no código, ou seja, estamos preparando para enviar estas informações para as demais pessoas que trabalham no projeto.

Comitar não significa enviar as informações para nosso repositório remoto (GitHub). Quando você comita algo, estas informações ainda estão apenas em sua máquina.

É importante frisar que não realize muitos commits locais sem enviá-los para o servidor, da mesma forma que não fique muito tempo sem buscar novas informações do servidor.

O ideia é que você trabalhe em um pedaço pequeno do software, resolva o problema, comit e já envie ele para o servidor. Em seguida, baixe a nova versão (Veremos como fazer isto neste artigo) para não ficar desatualizado.

### Comentários

Embora alguns lugares permitam que você realize commits sem informar nada, o ideal é que você sempre informe o que foi modificado.

Não precisa escrever um textão, mas também não é legal colocar algo muito genérico como "Ajustes gerais". Seja breve e seja específico.

Os comentários podem ser adicionados utilizando o parâmetro "-m", seguido pelo texto a ser enviado, durante o processo de commit.

Desta forma, nosso primeiro comando de commit ficará assim:

    git commit -m "Adicionado arquivo readme.md"

Neste momento, localmente está tudo pronto, e já podemos enviar nossas informações para o servidor.

Ligando os Repositórios
-----------------------

Até o momento temos dois repositórios distintos, o local (meurepo) e o remoto (meu-primeiro-repositorio), inclusive com nomes diferentes.

Embora tenhamos adicionado um arquivo e comitado as informações locais, ainda não especificamos para onde vão estas informações no servidor (GitHub).

Para realizar esta ação, vamos utilizar o comando **remote**, seguido pela opção **add origin**, que vai informar para qual repositório do GitHub as informações locais serão enviadas.

O comando **remote add origin** é seguido pela URL do repositório, conforme vimos anteriormente. Neste caso, o comando fica:

    git remote add origin https://github.com/USUARIO/meu-primeiro-repositorio.git

Pronto, temos agora uma ligação deste repositório local com o remoto e já podemos fazer o envio das informações.

Branchs
-------

Antes de aprendermos o comando para enviar as informações para o repositório remoto, precisamos entender o conceito de **Branchs**.

Branchs são ramificações do nosso código onde podemos copiar uma versão específica e trabalhar sobre ela, sem afetar a versão principal.

Todo repositório que criamos, já vem com uma **Branch** chamada **Master** por padrão.

Agora imagine que no software que você está trabalhando, estão querendo colocar uma nova funcionalidade, para oferecer a venda via cartão de crédito.

Esta funcionalidade irá modificar muito o código do sistema, afetando outras partes que já estão funcionando, e levará meses para ser desenvolvida.

O desenvolvimento, por algum motivo, começou diretamente na Branch Master, e vocês estão a todo vapor. Um mês se passou do desenvolvimento e MUITA COISA foi alterada.

Neste momento, apareceu um bug que ainda não havia sido notado por vocês, e uma correção URGENTE precisa ser feita.

Como o código para implementação do pagamento via cartão de crédito ainda não está pronto, as telas estão bagunçadas, com Links de testes e outras informações que deveriam ser vistas apenas a nível de desenvolvimento.

Este é um cenário terrível, porém que acontece com frequência no mundo do desenvolvimento. Temos um grande problema em mãos, pois ao mesmo tempo que precisamos corrigir um bug URGENTE, não podemos colocar o código atual (Branch Master) no ar.

Desta forma, é comum que se tenham várias Branchs em um projeto. A mais comum dela é a de desenvolvimento, que simplesmente separa o que está em andamento, do que já está pronto.

Em suma, não é legal ficar realizando desenvolvimento na Branch Master, é legal criar uma Branch para desenvolvimento e seguir nela. Com tudo pronto então, podemos juntá-la a Master.

Não vamos nos prender tanto a isto agora, a exemplificação foi só para esclarecer o comando que vem a seguir.

Enviando os Arquivos
--------------------

Agora que sabemos o que é uma Branch e que temos tudo certo localmente, vamos enviar os arquivos para nosso repositório remoto.

Os envios pelo Git são sempre feitos pelo comando **push** e devemos sempre especificar uma Branch para origem do mesmo.

Como neste caso estamos trabalhando na Branch Master, vamos especifica-la como padrão no envio, utilizando o seguinte comando.

    git push -u origin master

Pronto, neste momento, serão enviados os arquivos para o servidor e você poderá ver o resultado acessando a URL do repositório, que no caso é https://github.com/USUARIO/meu-primeiro-repositorio.

Atualizando os arquivos locais
------------------------------

Em inglês os comandos **push** e **pull** são usados para empurrar e puxar, e cuidado, **push é empurrar** e **pull e puxar**, então isto pode te confundir.

Desta forma, sempre que queremos enviar arquivos para o nosso servidor, usamos o comando **git push**, como vimos acima. Logo, para obter atualizações, utilizaremos o comando **git pull**.

Lembre-se que dificilmente você trabalhará sozinho, então é importante que frequentemente baixe novas atualizações do código. Mas termine o seu antes, para que as atualizações não o afete.

Vamos então editar nosso arquivo README.md no servidor, utilizando a URL https://github.com/USUARIO/meu-primeiro-repositorio/edit/master/README.md.

Adicione o seguinte conteúdo ao arquivo, preencha o campo **Commit Changes** com o texto "Atualizado pelo GitHub" e clique em "Commit Changes".

    Meu Primeiro Repositório 
    ======================== 

    O Git/GitHub são legais! 
    Agora estou atualizado

Neste momento, uma versão atualizada do README.md deve aparecer na tela, diferente do que temos no Visual Studio Code.

Agora, no Visual Studio Code, feche o arquivo README.md e no terminal, execute o seguinte comando:

    git pull

Abra novamente o arquivo README.md e poderá conferir que temos a versão que atualizamos no servidor, agora localmente.

Utilizando a integração com Visual Studio Code
----------------------------------------------

Embora nossa sugestão seja aprender e entender os comandos do Git, você pode facilmente realizar estas ações (Push/Pull) diretamente pelo Visual Studio Code.

O VS Code possui uma ótima integração com o Git, basta pressionar **CTRL (CMD)+SHIFT+G** para ir a tela do repositório. Nela você verá todas as alterações, conseguirá comitar e enviar/receber os arquivos.

Crie outros repositórios para brincar e testar. É importante aprender estes conceitos básicos aqui.

Clonando um repositório
-----------------------

Até o momento trabalhamos com a criação de um repositório local, porém, podemos clonar um repositório já existente em nossa máquina utilizando o comando **git clone**.

Vamos imaginar que você começou a trabalhar agora e o nosso repositório (meu-primeiro-repositorio) já está em andamento, sendo desenvolvido por uma equipe na qual você vai se juntar.

Desta forma, não podemos utilizar o git init ou criar algo localmente, e sim o clone. Feche o Visual Studio Code e exclua a pasta **meurepo** que criamos previamente.

Abra um terminal de sua preferência, navegue para uma pasta segura e execute o seguinte comando:

    git clone https://github.com/USUARIOmeu-primeiro-repositorio.git

Sempre que você quiser clonar um repositório, utilizará este padrão, ou seja, git clone https://github.com/**USUARIO**/**REPOSITORIO**.git.

Note que no final tem um ".git". Pronto, temos o repositório local novamente e você pode executar os push/pull para enviar ou receber arquivos nele.

Pronto, estes foram seus primeiros passos com Git e GitHub!