O .NET é a plataforma de desenvolvimento de aplicações para diferentes dispositivos sustentada pela Microsoft. Criada em meados de 2000, a plataforma desde então ganhou um ótimo amadurecimento e espaço no mercado.

Índice
------

*   [O que é um Framework](#o-que-é-um-framework)
*   [.NET Framework, .NET Core, .NET 5](#net-framework-net-core-net-5)
*   [SDK e Runtime](#sdk-e-runtime)
*   [LTS](#lts)
*   [Instalação no Windows](#instalação-no-windows)
*   [Instalação no Mac](#instalação-no-mac)
*   [Instalação no Linux](#instalação-no-linux-ubuntu)
*   [Instalando versões anteriores](#instalando-versões-anteriores)
*   [Verificando a versão instalada](#verificando-a-versão-instalada)
*   [Especificando uma versão](#especificando-uma-versão)
*   [Criando seu primeiro App](#criando-seu-primeiro-app)
*   [Restaurando os pacotes](#restaurando-os-pacotes)
*   [Compilando](#compilando)
*   [Atualizando os certificados](#atualizando-os-certificados)
*   [Executando](#executando)


O que é um Framework?
---------------------

O .NET também é chamado de .NET Framework (Explicado mais abaixo), e isto deve-se ao fato dele ser um conjunto de bibliotecas que nos auxilia na criação de aplicações Web, Windows, Mobile, IoT, ML e diversas outras vertentes.

Imagina que você quer construir uma aplicação que precisa ler algum arquivo de texto que está no seu computador. Neste caso, você não precisaria escrever todo código.

Você poderia utilizar as bibliotecas que o .NET oferece para acesso à arquivos e fazer a leitura do mesmo através dele.

Se pensarmos em diferentes aplicações para este "reuso" de código, temos uma economia imensa na escrita.

Na verdade, é difícil imaginar uma aplicação sendo desenvolvida sem um Framework. Seria suicídio escrever tudo que você e não reusar algo já pronto, testado e funcionando.

.NET Framework, .NET Core, .NET 5
---------------------------------

Durante sua jornada com .NET, você provavelmente encontrará três nomenclaturas para o mesmo, que lhe explicaremos agora.

### .NET Framework

Quando o .NET foi idealizado e criado, em meados de 1999/2000, seu primeiro nome foi .NET Framework. Ele foi concebido com a ideia de concentrar todas as aplicações relacionadas à tecnologias Microsoft.

O .NET Framework teve diversas versões, todas side-by-side, o que significa que você pode ter diversos .NET Frameworks, de versões diferentes, instalados em sua máquina.

Sua última versão foi a 4.x, que ainda só pode ser executada em ambientes Windows, não suportando Mac ou Linux. Embora as versões do .NET Framework ainda sejam suportadas, recomendamos começar novos projetos com .NET Core ou .NET 5.

### .NET Core

Com os avanços de outras tecnologias como Node, para expandir suas ferramentas, a Microsoft decidiu que teria o .NET Framework para múltiplas plataformas, ou seja, suporte a Windows, Mac ou Linux.

Porém, como você pode imaginar, o .NET Framework não é compatível com Linux ou Mac, e para dificultar, ele possui milhões de linhas de código.

Com a necessidade de agir rápido, a Microsoft resolver criar um novo Framework, baseado no .NET, porém que suporta Windows, Mac e Linux.

Desta forma, escrever algo novo, do zero, foi a melhor saída, e então, deu-se início, em meados de 2015 ao .NET Core, um Framework com as mesmas expectativas do .NET Framework, porém com suporte muito maior.

Durante este período, a Microsoft ficou com dois Frameworks no mercado, o .NET Framework, que era o mais completo, porém rodando apenas Windows e o .NET Core, que ainda estava sendo construído, mas que rodava multi-plataforma.

Para nossa felicidade, uma das primeiras partes a serem portadas do .NET Framework para o .NET Core foi o ASP.NET, que é a tecnologia da Microsoft que utilizamos para criação de aplicações Web.

Ou seja, desde o início do .NET Core, se seu foco é desenvolver aplicações Web, você tem seu suporte. Desta forma não há motivos para começar novos projetos com .NET Framework se seu objetivo é Web.

Enquanto o .NET Framework vem apenas recebendo pequenos reparos, o .NET Core vem crescendo cada dia mais, e está quase do tamanho do seu antecessor, ou seja, já tem quase tudo.

### .NET 5

Se o .NET Core está quase com todas as funcionalidades que o .NET Framework tinha, e ainda com possibilidade de rodar multi-plataforma, não faz mais sentido manter dois Frameworks, concorda?

Pois esta é justamente a ideia do .NET 5, nome dado ao próximo Framework da Microsoft que tem sua data de lançamento prevista para Novembro de 2020.

O .NET 5 nada mais é do que a "unificação" destes mundos que foram divididos por conta das compatibilidades com sistemas operacionais.

Não se preocupe pois o .NET 5 é uma nova versão do .NET Core, ou seja, seu código será boa parte reaproveitado nele, sem grandes mudanças.

Daqui para frente seguiremos com as versões 6, 7, 8... e assim por diante do .NET, nome adotado para Framework que comentamos aqui.

SDK e Runtime
-------------

Antes de prosseguirmos para a instalação do Framework, precisamos saber a diferença entre SDK e Runtime, que serão as opções apresentadas para Download.

SDK significa Software Development Kit, ou seja, é o que precisamos para CONSTRUIR nossas aplicações utilizando o .NET.

Runtime é apenas a base para EXECUTAR aplicações já criadas e compiladas com .NET.

Sempre que precisarmos desenvolver, compilar, ou algo do tipo, precisamos do SDK e não apenas do Runtime.

O Runtime você instalaria na máquina onde seu programa já compilado irá ser executado, por exemplo.

LTS
---

Em algumas versões do .NET, será exibido a sigla LTS ao lado, que significa Long Term Support, ou versão com suporte por um longo tempo.

Normalmente, as versões terminadas em 1, como 1.1, 2.1 e 3.1 são as LTS, pois elas vem com possíveis correções que a primeira versão não trouxe.

Quando usamos uma versão LTS, estamos dizendo que teremos suporte da Microsoft por um tempo bem maior, então elas são recomendadas para produção.

Instalação no Windows
---------------------

A instalação do .NET Core (Versão atual) no Windows é relativamente simples. [Basta acessar o site oficial](https://dotnet.microsoft.com/download) e fazer o Download do instalador do SDK clicando em **_"Download the .NET SDK"_**.

Siga os passos de instalação na tela e pronto, está tudo certo para começarmos a trabalhar com .NET!

Instalação no Mac
-----------------

A instalação do .NET Core (Versão atual) no Mac é relativamente simples. [Basta acessar o site oficial](https://dotnet.microsoft.com/download) e fazer o Download do instalador do SDK clicando em **_"Download the .NET SDK"_**.

Siga os passos de instalação na tela e pronto, está tudo certo para começarmos a trabalhar com .NET!

Instalação no Linux (Ubuntu)
----------------------------

O primeiro passo para instalação no Ubuntu 19.10 é o registro dos Feeds de pacotes da Microsoft em seu sistema, que podem ser feitos com as linhas abaixo.

    wget https://packages.microsoft.com/config/ubuntu/19.10/packages-microsoft-prod.deb -O packages-microsoft-prod.deb  
    sudo dpkg -i packages-microsoft-prod.deb

Agora vamos de fato instalar o .NET Core e suas dependências utilizando os comandos abaixo.

    sudo apt-get update  
    sudo apt-get install apt-transport-https  
    sudo apt-get update  
    sudo apt-get install dotnet-sdk-3.1

Caso trabalhe com algum sabor Linux diferente, você pode acessar o [guia completo de instalação do .NET Core em sua página oficial](https://dotnet.microsoft.com/download).

Instalando versões anteriores
-----------------------------

Em diferentes situações, você encontrará aplicações que foram criadas com versões anteriores do .NET Core, como a 1.1, 2.2 e assim por diante.

Assim como o .NET Framework, o .NET Core permite instalação side-by-side, ou seja, podemos ter várias versões do .NET Core instaladas em nossa máquina.

Você pode acessar o [site oficial e verificar todas as versões disponíveis](https://dotnet.microsoft.com/download/dotnet-core) para Download. A instalação das versões anteriores é feita da mesma forma que a atual.

Verificando a versão instalada
------------------------------

Com tudo instalado, vamos verificar a versão instalada do .NET em nossa máquina, utilizando o comando abaixo.

    dotnet --version

Se tudo ocorreu corretamente, você verá a versão instalada. Você pode verificar todas as versões disponíveis do .NET em seu sistema utilizando o comando.

    dotnet --list-sdks

A [lista completa de comandos disponíveis para você executar](https://docs.microsoft.com/pt-br/dotnet/core/tools/dotnet) pode ser encontrada no site oficial do .NET.

Especificando uma versão
------------------------

Vamos supor que você acabou de baixar os fontes de uma aplicação que foi escrita em .NET Core 2.2 e precisa dar manutenção nela.

O primeiro ponto a se fazer é instalar o SDK na versão correta, como vimos anteriormente e depois executar o **dotnet --list-sdks** para verificar se tudo foi instalado.

Partindo do ponto que você instalou corretamente a SDK, que a mesma está sendo listada no seu sistema, agora precisamos definir que estamos utilizando esta versão.

Sempre que instalamos uma versão mais nova do .NET, ela será atribuída como padrão, ou seja, a partir deste momento, todas as aplicações utilizarão ela.

Se você executar um **dotnet new** por exemplo, para criar uma nova aplicação, o .NET buscará a SDK padrão do sistema para isto, e esta será a última versão.

O mesmo ocorre quando você executa o comando **dotnet restore**, para restaurar os pacotes da sua aplicação. Ele sempre se baseia na SDK padrão que no caso é a última versão disponível.

Particularmente, não recomendamos alterar este comportamento. Você pode remover uma SDK, mas imagina o trabalho que isto dá.

A solução para este comportamento, é a criação de um arquivo **_Global.json_** na raiz da aplicação ou da pasta onde você está executando os comandos **dotnet**.

O comando **dotnet** **SEMPRE** olhará este arquivo antes de prosseguir, e nele podemos definir qual versão do SDK queremos usar para criação do App ou restauração dos pacotes.

Abaixo temos um exemplo de um **Global.json** que especifica que a versão 3.1.100 do .NET Core deve ser utilizada.

    {  
        "sdk": {  
            "version": "3.1.100"  
        }  
    }

Criando seu primeiro App
------------------------

Com tudo pronto, vamos criar nosso primeiro App e ele vai ser um **Console Application**, o que significa que como resultado teremos um executável da nossa aplicação.

Existem vários tipos de projetos a serem criados com o comando **dotnet**, você pode [conferir todos eles na documentação oficial](https://docs.microsoft.com/pt-br/dotnet/core/tools/dotnet-new).

Abra um terminal e navegue para uma pasta segura, em seguida, execute os comandos abaixo.

    mkdir myapp  
    cd myapp  
    dotnet new console

O que fizemos foi criar uma nova pasta, navegar até ela e criar uma estrutura de um projeto do tipo Console Application com .NET.

O comando abaixo poderia ser utilizado para re-criar todo este processo com apenas um comando.

    dotnet new console -o myapp

A diferença é que neste último comando, não temos a oportunidade de criar o Global.json antes da execução, ou seja, ele vai criar um projeto sempre com a última versão do SDK instalado.

Caso queira criar um projeto com uma VERSÃO ANTERIOR do .NET Core, execute o seguinte processo.

    mkdir myapp  
    cd myapp

Abra esta pasta com o [Visual Studio Code](https://balta.io/blog/visual-studio-code-instalacao-customizacao) e crie um arquivo **Global.json** na raiz da aplicação, com a seguinte configuração.

    {  
        "sdk": {  
            "version": "X.X.XXX"  
        }  
    }

Não esqueça de substituir o X.X.XXX pela versão do .NET que deseja utilizar. Em adicional, garanta que a SDK desta versão está instalada em seu sistema.

Volte ao terminal e continue a criação da aplicação com o comando abaixo.

    dotnet new console

Desta forma, você deve ter criado um Console Application utilizando uma versão anterior do .NET Core SDK instalada em sua máquina.

Caso especifique uma versão não instalada em sua máquina, um erro será apresentado.

Restaurando os pacotes
----------------------

Embora o SDK do .NET forneça todo conteúdo básico que precisamos para executar nossas aplicações, é muito comum a utilização de pacotes externos.

Imagina que você precisa criar uma aplicação para ler um QR Code. Possivelmente outras pessoas já passaram pelo mesmo problema e já tem um pacote que resolve isto.

Embora este pacote para ler o QR Code não venha no .NET por padrão, você pode utilizá-lo como um pacote externo, diretamente de uma fonte de pacotes da Microsoft, chamada NuGet.

Desta forma, sempre que obtemos os fontes de uma aplicação .NET, a primeira coisa que precisamos fazer é restaurar seus pacotes, e isto é feito pelo comando abaixo.

    dotnet restore

Note que para executar este comando, você precisa estar na pasta raiz da aplicação.

Compilando
----------

Nosso próximo passo é compilar nossa aplicação para garantir que todo código está funciona. Como não alteramos nada do código, teoricamente é para estar tudo OK.

Para executar a compilação da nossa aplicação, vamos executar o comando abaixo na raiz da aplicação.

    dotnet build

Ao término da execução, você deve ver uma mensagem de sucesso.

Atualizando os certificados
---------------------------
Caso você esteja executando uma aplicação Web, você precisará simular `HTTP` localmente, o que exige um certificado digital. O .NET traz um certificado válido para testes, basta executar os comandos abaixo.

```
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

Executando
----------

Nosso último passo é executar nossa aplicação, e isto será feito utilizando o comando abaixo.

    dotnet run

Neste momento você deve ver o famoso Hello World na tela do seu terminal.

Parabéns, você instalou, configurou e criou sua primeira aplicação com .NET Core.
