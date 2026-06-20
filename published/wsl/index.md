O WSL ou Windows Subsystem for Linux permite a execução da plataforma Linux dentro do Windows via terminal, de forma rápida e direta. Este é atualmente a melhor forma para ter o melhor de cada SO unificado.

## WSL para desenvolvedores

O Linux sempre foi conhecido por ter um ótimo terminal e um ótimo gerenciador de pacotes, o Mac também cumpre seu papel nesta parte, já o Windows sempre foi conhecido pelos famosos instaladores.

Enquanto o Windows e Mac reinam no mundo dos usuários, sabemos que nos servidores, quem manda é o Linux, e como desenvolvedor Windows sempre senti aquele choque quando precisava fazer tudo via terminal.

O problema é que os comandos no Windows são diferentes do Linux, incluindo caminhos. Quando precisamos preparar nossa aplicação para o Deploy em Linux, não tem jeito, precisa dar aquela estudada no SO.

Paralelo ao Windows eu uso o Mac, e de um tempo para, principalmente por conta do Docker, acabei adotando um Dual Boot entre Windows e Ubuntu.

Particularmente não consigo viver sem Windows, os softwares do Office, Adobe, Camtasia, que são os que mais utilizo, não estão presentes no Linux.

Porém, eu adoro codificar no Ubuntu, tem tudo que preciso no terminal, consigo bastante conteúdo na internet e os add-ons do terminal são excelentes.

Mas este mundo de Dual Boot felizmente acabou, chega de reiniciar a máquina para acessar o Photoshop, agora com o WSL podemos ter o Linux dentro do Windows, e não estou falando de uma máquina virtual.

Eu também acredito que este é um primeiro passo para supremacia do UNIX nos terminais para desenvolvedores. Na verdade o Max e Linux já usam, só o Windows que estava para trás.

Pois bem, com a instalação do WSL, teremos um sub-sistema Linux (Ubuntu neste caso) rodando em nosso sistema Windows. O que significa que via linha de comando, conseguiremos ter acesso ao sistema de arquivo Windows e Linux, incluindo todos os comandos UNIX.

Apenas abrindo um parênteses, o WSL coloca o Windows um passo acima na melhor escolha como SO para desenvolvedores, já que agora temos o melhor dos dois mundos e não precisamos pagar uma fortuna por isto.

## Requisitos

Para instalar o WSL 2 você precisa da versão 2004 ou superior do Windows 10, incluindo o build 19041 ou superior. Em suma, você precisa ter o Windows atualizado.

```
Windows 10, updated to version 2004, Build 19041
```

Outro passo adicional que você **talvez** precisará, é modificar a política de execução de scripts do Power Shell, utilizando o comando abaixo **como administrador**.

```
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned
```

O passo acima varia de acordo com cada conta, mas se quiser já ficar preparado, recomendo que execute o mesmo antes de prosseguir.

## ATUALIZAÇÃO IMPORTANTE
Se você está rodando uma build igual ou superior a **20262** do Windows 10 (Ou superior), você pode instalar o **WSL** com apenas uma linha de comando, abrindo seu **Power Shell** como administrador e executando `wsl --install`.

Após este processo, reinicie a máquina e você será indagado para criar o usuário e senha do Ubuntu ou outro sabor Linux que escolheu durante a instalação. Se este processo se realizar com sucesso, você pode pular para sessão **Instalando o .NET** deste artigo!

## Habilitando o WSL

Caso esteja com o Power Shell ou Windows Terminal aberto, feche e abra um novo (Como adminitrador). Execute o comando abaixo para habilitar a funcionalidade do WSL no Windows.

```
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
```

Agora vamos habilitar os componentes adicionais de máquinas virtuais, executando o seguinte comando, também em modo administrador.

```
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

Agora reinicie sua máquina para que as alterações sejam executadas.

Caso já possua o WSL 1 instalado, após a reinicialização, abra um terminal (Power Shell ou Windows Terminal) e execute o comando abaixo.

```
wsl --set-default-version 2
```

Para listar todos os subsystemas instalados, você pode usar o comando <code>wsl --list</code>, mas caso esta seja sua primeira instalação, não deverá conter nenhum subsystema ainda.

## Instalar o Ubuntu no Store

Com o WSL instalado, vamos instalar o Ubuntu como subsystema, e para isto, basta abrir o Microsoft Store (Loja) pelo menu iniciar e procurar por Ubuntu.

Na página do produto (Ubuntu) clique em **Obter** ou **Get it** para fazer o download e instalação do substema. Existem outros além do Ubuntu, mas não cobrirei aqui, e a versão instalada, no meu caso foi a 20.04.

> Esta é a coisa mais **louca** que já vi em toda minha carreira como desenvolvedor e usuário de Windows, estamos instalando o Linux dentro do Windows.

## Executando o Ubuntu

Após a instalação uma aplicação chamada Ubuntu ficará disponível no Windows, basta pesquisar por Ubuntu no menu iniciar.

Abra esta aplicação para iniciar o setup, que é bem simples, pedindo apenas um usuário e senha. Não utilize espaços ou caracteres especiais no nome de usuário e lembre-se dele e de sua senha, vamos precisar disso depois.

Pronto, isto é tudo que precisamos para ter o Ubuntu rodando dentro do Windows, de forma invisível!

## WSL

Agora temos acesso ao WSL no terminal (Power Shell ou Windows Terminal). Basta abrí-lo e digitar <code>wsl</code> para acessar seu Ubuntu.

Lembre-se que ele já mapeia sua estrutura de pastas do Windows, então, além das pastas padrões do Linux, você também pode navegar em seu "C:/".

Caso queira checar a versão do Ubuntu instalada no WSL, você pode usar o comando abaixo.

```
lsb_release -a
```

## Instalando o .NET

Se você instalou o .NET no Windows, ele não vai funcionar no WSL, você pode testar executando o comando <code>dotnet --version</code> se quiser.

Vamos então executar a instalação do .NET também no subsistema, executando os passos abaixo.

```
sudo wget https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb

sudo apt-get update
sudo apt-get install apt-transport-https
sudo apt-get update
sudo apt-get install dotnet-sdk-3.1
```

> A versão especificada na última linha se refere a última versào disponível do SDK do .NET até a escrita deste artigo.

Com tudo instalado, você pode executar um <code>dotnet --version</code> para verificar se tudo deu certo e qual versão está sendo executada.

Por fim, limpe e aceite novamente os certificados HTTPs que o .NET utiliza, isto é importante para não termos nenhum erro de segurança durante a execução das nossas aplicações.

```
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

## Integrando ao Visual Studio Code

Por padrão, o Visual Studio Code utiliza o Power Shell ou Windows Terminal no Windows, isto significa que ele tem acesso à tudo que está em execução no Windows apenas.

Para realizar a alteração para o WSL vamos precisar de duas coisas, uma extensão e alterar uma configuração.

O primeiro passo é ir na aba **Extensions** e instalar o **Remote - WSL (ms-vscode-remote.remote-wsl)**. Esta extensão fará a ligação entre nosso ambiente Windows, onde o VS Code está rodando e nosso ambiente Linux, onde o .NET está rodando.

Em adicional, uma nova aba no menu lateral esquerdo, chamada **Remote Explorer** será adicionada, bem como um ícone no canto inferior esquerdo do VS Code.

Este ícone indica qual ambiente a aplicação que o VS Code tem aberto será executada, e você pode simplesmente alterar entre Linux e Windows quando quiser.

Lembre-se apenas que as instalações são separadas, os softwares que você tem no Windows não serão executados no Linux e vice-versa.

## WSL como terminal padrão

Toda vez que você abrir um terminal no VS Code, ele pegará a opção padrão do SO, que no caso do Windows é o Power Shell. Você pode alterar esta opção, tornando o WSL como terminal padrão do VS Code adicionando a linha abaixo a sua configuração (<kbd>CTRL</kbd>+<kbd>,</kbd> => Open Settings JSON).

```
"terminal.integrated.shell.windows": "C:\\Windows\\System32\\wsl.exe",
```

## Powerline

Este passo é totalmente opcional, mas caso queira dar um incremento em seu terminal, o Powerline é uma ótima opção. Além de deixar mais bonito, ele ainda traz informações do respositório (Git) atual.

### Fontes

O Powerline utiliza um conjunto de fontes para exibir alguns ícones na tela, então faça o download e instalação delas antes.

```
https://github.com/powerline/fonts
```

### Instalação

O Powerline pode ser instalado via WSL através do <code>apt-get</code>, utilizando o comando abaixo.

```
sudo apt-get install powerline fonts-powerline
```

Em seguida, vamos editar nosso profile para carregar o Powerline toda vez que o terminal for aberto. Abra o <code>.bashrc</code> com o VS Code.

```
code ~/.bashrc
```

E adicione as seguintes linhas ao fim do arquivo.

```
if [ -f /usr/share/powerline/bindings/bash/powerline.sh ]; then
    source /usr/share/powerline/bindings/bash/powerline.sh
fi
```

Pronto, você tem o Powerline completamente funcional no seu terminal Linux rodando via WSL no Windows!

## Fonte

- https://docs.microsoft.com/pt-br/windows/wsl/
