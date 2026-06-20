O Visual Studio Code é um editor de código criado pela Microsoft e que tem uma grande adoção pelas comunidades de diversas linguagens e tecnologias.

Índice
------

*   [Requisitos](#requisitos)
*   [Instalação no Windows](#instalação-no-windows)
*   [Instalação no Mac](#instalação-no-mac)
*   [Instalação no Linux (Ubuntu)](#instalação-no-linux-ubuntu)
*   [Extensões](#extensões)
*   [Configurações](#configurações)
*   [Ícones e Temas](#ícones-e-temas)

Requisitos
----------

O Visual Studio Code tornou-se famoso por sua leveza na execução, sendo um dos editores de código mais leve e rápidos que temos no mercado.

Desta forma, os requisitos de hardware são baixos, porém recomendamos uma máquina com pelo menos Intel Core i3, Dual Core, com 8GB de Ram e no mínimo 128GB de disco.

Estas recomendações são baseadas na execução dos nossos cursos, então caso seu cenário seja diferente, elas podem varias.

Recomendamos também manter o **Sistema Operacional SEMPRE ATUALIZADO**, e em caso de uso do Windows, utilizar sempre a versão 10 ou superior.

Instalação no Windows
---------------------

A instalação no Windows é relativamente simples, basta acessar o [site oficial do Visual Studio Code](https://code.visualstudio.com/) e fazer o Download da sua última versão, clicando no botão "Download for Windows".

Em seguida, execute o instalador baixado e siga as instruções na tela, não esqueça de marcar a opção "Add to Path" para adicionar o Visual Studio Code nas variáveis de ambiente.

![](https://baltaio.blob.core.windows.net/blog/visual-studio-code-instalacao-customizacao-001.png)

Finalizada a instalação, feche todos os terminais abertos e abra um novo (Power Shell ou [Windows Terminal](https://www.microsoft.com/pt-br/p/windows-terminal-preview/9n0dx20hk701?activetab=pivot:overviewtab)) e digite o comando abaixo.

    code --version

Se tudo ocorrer como esperado, você verá a versão do Visual Studio Code instalada em sua máquina e estará tudo pronto para prosseguir.

Instalação no Mac
-----------------

A instalação no Mac é relativamente simples, basta acessar o [site oficial do Visual Studio Code](https://code.visualstudio.com/) e fazer o Download da sua última versão, clicando no botão "Download for Mac".

Abra o instalador e copie o Visual Studio Code para a pasta Applications do seu Mac e pronto, a instalação está devidamente feita.

### Adicionando ao PATH

Podemos ainda adicionar o Visual Studio Code ao PATH para ter acesso ao comando **_code_** no terminal, podendo abrir diretamente pastas de lá no Visual Studio Code.

Abra seu menu de aplicativos e inicie o Visual Studio Code e na tela inicial dele pressione F1 para abrir a tela de execução de comandos.

Procure pelo comando **_Shell Command: Install 'code' command in PATH_** para adicionar o Visual Studio Code ao PATH do seu sistema operacional.

Instalação no Linux (Ubuntu)
----------------------------

A instalação no Linux é relativamente simples, basta acessar o [site oficial do Visual Studio Code](https://code.visualstudio.com/) e fazer o Download da sua última versão, clicando no botão "Download for Linux".

Como resultado do Download você terá um pacote .deb na pasta Downloads. Abra um terminal, navegue até a mesma e execute o seguinte comando.

    sudo apt install ./ARQUIVO_BAIXADO.deb

Pronto, a instalação do Visual Studio Code está realizada e ele já deve aparecer no menu de aplicativos para você.

### Adicionando ao PATH

Podemos ainda adicionar o Visual Studio Code ao PATH para ter acesso ao comando **_code_** no terminal, podendo abrir diretamente pastas de lá no Visual Studio Code.

Abra seu menu de aplicativos e inicie o Visual Studio Code e na tela inicial dele pressione F1 para abrir a tela de execução de comandos.

Procure pelo comando **_Shell Command: Install 'code' command in PATH_** para adicionar o Visual Studio Code ao PATH do seu sistema operacional.

Extensões
---------

As extensão são uma espécie de aplicativos complementares instalados dentro do Visual Studio Code que nos permite acesso a funcionalidades extras.

Existem muitas extensões já criadas e novas chegando a cada dia, porém neste artigo vamos cobrir as utilizadas na execução dos cursos.

Com o Visual Studio Code aberto, pressione CTRL (CMD)+SHIFT+X ou localize o ícone de extensões no menu lateral direito da tela.

Uma nova tela se abrirá, para pesquisarmos as extensões, e vamos instalar as seguintes.

*   C# - ms-dotnettools.csharp
*   Azure - ms-vscode.vscode-node-azure-pack
*   Docker - ms-azuretools.vscode-docker
*   Flutter - dart-code.flutter
*   Beautify - hookyqr.beautify

Como podem haver extensões com nomes iguais ou similares, procure sempre pelo nome do pacote informado acima.

Se você não estiver trabalhando com Flutter no momento e não quiser instalar a extensão dele agora por exemplo, não precisa. Você pode limitar a instalação das extensões apenas para as que está de fato utilizando.

A extensão **Beautify** serve para identar códigos CSS, HTML e JavaScript de uma forma mais otimizada. Ela é opcional aqui, porém recomendada.

Configurações
-------------

O Visual Studio Code permite muitas configurações, e você está livre para brincar aqui, porém vamos deixar as configurações que utilizamos nos cursos listada abaixo.

Para acessar as configurações, basta pressionar CTRL (CMD)+VÍRGULA, e você será apresentado a uma interface gráfica onde poderá modificar ou pesquisar uma configuração.

No canto superior direito desta tela, haverá um botão chamado **_Open Settings (JSON)_** que irá exibir o JSON das configurações atuais.

Caso queira manter todas as configurações como as nossas, utilize o JSON abaixo em sua configuração.

    {  
        "editor.formatOnSave": true, // Formata o código sempre que salvamos  
        "editor.fontFamily": "Source Code Pro", // Fonte  
        "editor.fontSize": 18, // Tamanho da Fonte  
        "workbench.colorTheme": "Atom One Dark", // Tema  
        "workbench.iconTheme": "vscode-icons", // Pacote de Ícones  
        "workbench.startupEditor": "newUntitledFile", // Abre sempre um arquivo em branco  
        "editor.minimap.enabled": false, // Desabilita o Mini Map  
        "workbench.panel.defaultLocation": "right", // Deixa o terminal a direita  
        "terminal.integrated.cursorStyle": "underline", // Deixa o cursor do terminal como underline  
        "terminal.integrated.fontSize": 18, // Tamanho da fonte do terminal  
        "git.enableSmartCommit": true, // Habilita Smart Commit do GIT  
        "git.autofetch": true // Habilita Auto Fetch do GIT  
    }

Note que para utilizar a mesma fonte, tema e ícones você precisa instalá-los primeiro, então siga os processos abaixo antes de salvar esta configuração.

A fonte que utilizamos é a Source Code Pro e pode ser [baixada diretamente do Google Fonts](https://fonts.google.com/specimen/Source+Code+Pro). Após o Download, realize a instalação da mesma e reinicie o Visual Studio Code.

Ícones e Temas
--------------

O Visual Studio Code permite uso de ícones customizados, que facilitam muito a visualização dos arquivos.

Tanto os ícones quanto os temas podem ser instalados como uma extensão, então repita o processo de instalação de extensão e procure pelos seguintes itens.

*   VS Code Icons - vscode-icons-team.vscode-icons
*   Atom One Dark Theme - akamud.vscode-theme-onedark

Lembre-se que estas são apenas sugestões, baseados no que nós utilizamos nos cursos. Talvez você encontre um tema que mais te agrada, fique à vontade.