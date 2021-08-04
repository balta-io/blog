Esta série de artigos apresentam os passos iniciais para começar a desenvolver aplicações multiplataforma com Flutter e Dart.  
  
Este artigo em específico mostra a instalação e configuração dos ambientes **Android** e **iOS** no **MacOs**, para outras versões de sistemas operacionais, consulte a lista abaixo:

### Artigos Relacionados

Requisitos
----------

Para utilizar o **Flutter** você precisará de uma versão do MacOs de 64bits e de 700MB de espaço em disco.  
Este espaço não leva em consideração Virtual Machines do Android ou Simuladores do iOS, nem outras SDKs necessárias.  
  
No meu caso, tomando como base uma máquina nova e limpa, todo conteúdo instalado, incuindo conteúdo não obrigatório, está tomando 83GB de espaço em disco.

*   Adobe Photoshop
*   Apple iMovies
*   Apple Garage Band
*   Techsmith Camtasia
*   OBS Studio
*   Microsoft Office (Excel, Powerpoint, Word, Teams, OneNote, Outlook e OneDrive)
*   Entretenimento (Spotify, WhatsApp, Telegram)
*   **Visual Studio Code**
*   **Chrome**
*   Hyper (Com ZSH)
*   **XCode**
*   **Android Studio**

\* Os itens em negrito são obrigatórios.  
  
Fora estes itens, tenho os seguintes VMs/Simuladores.  

*   Google Pixel 2XL - Android VM
*   iPhone XR - iOS Simulator

Note que isto serve apenas para você ter uma base de espaço necessário com seu ambiente totalmente completo. Este número pode varia significativamente de acordo com a quantidade de simuladores que você pretende utilizar.  
  
Um simulador de iPad por exemplo chega a ser mais de 5GB, então se você não tem muito espaço, recomendo ir removendo os simuladores que não utiliza mais.  
  
Outra dica é utilizar um HDD externo, já que é bem difícil atualizar o storage dos novos MacBooks, iMacs e MacMinis. No meu caso, comprei um SSD externo da Samsung, modelo T5, ele tem 256GB e roda via USB C/Thunderbolt. É muito rápido, nem parece que é um disco externo, inclusive já rodei Windows direto dele sem afetar nada de performance.

### Ferramentas Necessárias

A minha dica aqui é instalar o XCode, o Android Studio e o Visual Studio Code. Pode parecer estranho, já que utilizo e recomendo o Visual Studio Code para desenvolver, que é muito mais simples e leve que o Android Studio/XCode, mas o fato é que instalando o Android Studio e o XCode, muita coisa já é configurada ou tem um Wizard para configuração. Dá para fazer tudo na mão, mas é complicado.

Além dos itens acima, precisaremos também de alguns itens via Command Line.  

*   bash
*   curl
*   git 2.x
*   mkdir
*   rm
*   unzip
*   which

O único item acima que pode não vir instalado é o **GIT**, que pode ser baixado e instalado nesta URL: [https://git-scm.com/](https://git-scm.com/).

Instalação do XCode
-------------------

O XCode pode ser instalado diretamente da Apple Store no MacOs, sem segredo nenhum.  
Feito a instalação, abra o XCode e aceite as licenças, ou simplesmente execute o seguinte comando no seu terminal.

sudo xcode-select --switch /Applications/Xcode.app/Contents/Developer xcodebuild -license

Instalação do Android Studio
----------------------------

Navegue para [https://developer.android.com/studio/](https://developer.android.com/studio/) e faça o Download do Android Studio.  
Abra o pacote baixado e arraste a aplicação para a sua pasta Applications.  
  
![](https://baltaio.blob.core.windows.net/blog/flutter-ios-macos-014.png)  
  
![](https://baltaio.blob.core.windows.net/blog/flutter-ios-macos-015.png)  
  
![](https://baltaio.blob.core.windows.net/blog/flutter-ios-macos-016.png)  
  
Percorrendo este caminho, você chegará nesta tela:  
![](https://baltaio.blob.core.windows.net/blog/flutter-ios-macos-017.png)  
Clique em **Finish** e terminamos a instalação do Android Studio.

Instalando o Flutter
--------------------

Navegue para [https://flutter.dev/docs/get-started/install](https://flutter.dev/docs/get-started/install) e faça o Download do Flutter para sua plataforma (Neste caso, iOS).  
Extraia o conteúdo baixado para uma pasta, eu recomendo criar um pasta chamada **development** na sua home, como mostrado na imagem abaixo.  
  
![](https://baltaio.blob.core.windows.net/blog/flutter-ios-macos-001.png)  
  
Tendo como resultado final o seguinte:  
![](https://baltaio.blob.core.windows.net/blog/flutter-ios-macos-002.png)  

Adicionando Flutter ao PATH
---------------------------

No passo anterior, o que fizemos foi apenas copiar o 'executável' do Flutter para pasta mencionada. Precisamos agora torná-lo disponível via linha de comando para começarmos a criar nossas aplicações.

### Terminal

Se você utiliza o terminal padrão do MacOs, é necessário criar um arquivo .bash\_profile (Caso não exista) na sua home, como mostrado na imagem abaixo.  
No meu caso, abri o Visual Studio Code, fui em File > New File e salvei o arquivo na home.  
![](https://baltaio.blob.core.windows.net/blog/flutter-ios-macos-003.png)  
  
O conteúdo deste arquivo deve ser:

export PATH='$PATH:Users/balta/development/flutter/bin'

### ZSH

Se você utiliza ZSH (Assim como eu), a única mudança é que o arquivo será o .zshenv e não o .bash\_profile.  
Particularmente, crio ambos, assim se deixar de utilizar ZSH, não preciso configurar novamente.  
  
O conteúdo dos arquivos também é o mesmo!

### Finalizando

Faça logout (Ou reinicie -- eu prefiro) e estará tudo pronto. Ao término do logout ou restart, abra seu terminal e digite o seguinte:

flutter doctor

Se tudo ocorrer certo, um checkup do Flutter irá se iniciar, e não se preocupe com as mensagens em vermelho, vamos corrigi-las a seguir.

Configurando o Visual Studio Code
---------------------------------

Abra o VS Code, vá até o ícone de extensões do lado direito e digite **flutter** na busca.  
![](https://baltaio.blob.core.windows.net/blog/flutter-ios-macos-007.png)  
  

Configurando o Android Studio
-----------------------------

Abra o Android Studio, clique em Configure > Plugins e faça a instalação do Flutter/Dart.  
![](https://baltaio.blob.core.windows.net/blog/flutter-ios-macos-028.png)  
  
Abra um terminal, e digite o seguinte comando para aceitar as licenças do Android Studio.

flutter doctor --android-licenses

Aproveite também para rodar novamente o **flutter doctor** para ver o que ainda falta ser configurado. Neste momento deve faltar apenas um device conectado.

Android Virtual Machine
-----------------------

A forma mais rapida de trabalhar é utilizando uma máquina virtual ou carinhosamente chamado de Emulador.  
Podemos ter N emuladores, cada um com sua versão do Android/SDK.  
Para começar, abra o Android Studio e clique em Configure > AVD Manager.  
Guarde bem este caminho, sempre que quiser abrir seu emulador, este será o caminho.  
  
Na primeira tela, clique em **Create Virtual Device** para que a tela de seleção de hardware seja mostrada.  
Neste caso, selecionei o **Pixel 2** como device.  
![](https://baltaio.blob.core.windows.net/blog/flutter-ios-macos-023.png)  
  
Na tela a seguir, selecione a SDK desejada.  
![](https://baltaio.blob.core.windows.net/blog/flutter-ios-macos-024.png)  
  
Aceite os termos e revise as configurações finais.  
![](https://baltaio.blob.core.windows.net/blog/flutter-ios-macos-026.png)  
  
Tudo pronto, só iniciar seu emulador.  
![](https://baltaio.blob.core.windows.net/blog/flutter-ios-macos-027.png)  
  

Aproveite e execute o comando **flutter doctor** para ver se o device aparece na lista. Em adicional, sempre que quiser listar os devices conectados a sua máquina, utilize o comando **flutter devices**.

iOS Simulator
-------------

Abra o XCode, navegue no menu XCode > Preferencer > Components para ver a tela abaixo:  
![](https://baltaio.blob.core.windows.net/blog/flutter-ios-macos-005.png)  
Faça o download do simulador que desejar clicando no botão de download na frente do mesmo.

Para abrir o simulador instalado, com o XCode aberto, navegue para XCode > Open Developer Tools > Simulator.

Testando seu primeiro App
-------------------------

Abra um dos emuladores criados anteriormente e deixe-o rodando, na tela inicial do SO.  
  
Abra o Visual Studio Code e pressione CMD+SHIFT+P e na janela superior que se abrir, digite **flutter**.  
Nas opções que aparecerem, selecione **Flutter: New Project**. Selecione um local para sua aplicação ser criada e pronto.  
  
No terminal do Visual Studio Code, digite **flutter run** para executar sua aplicação.  
Como resultado, você terá a seguinte aplicação rodando:  
![](https://baltaio.blob.core.windows.net/blog/flutter-ios-macos-007.png)  
É importante lembrar que se houver mais de um dispositivo conectado, o **flutter run** irá exigir uma opção extra, informando o ID do device no qual ele deve publicar a aplicação.  
  
Neste caso, utilize o **flutter devices** para listar os dispositivos conectados e utilize o comando **flutter run -d IDDODEVICE** para rodar seu App no device informado.

Android Devices
---------------

Embora os emuladores ajudem bastante, é importante rodar as aplicações nos dispositivos reais, no seu smartphone.  
Com as configurações anteriores prontas, basta plugar seu telefone que a mensagem 'Trust this device' irá aparecer no seu Android, confirme esta máquina como uma fonte confiável.  
  
É importante salientar que seu dispositivo deve estar habilitado para o Modo Desenvolvedor e isto varia de aparelho para aparelho, consulte no Google como habilitar o seu.  
  
Com seu device conectado, execute novamente o comando **flutter devices** e ele deve ser listado.

iOS Devices
-----------

Para rodar sua aplicação Flutter no iOS as coisas são um pouco mais complicadas!

### Instalando o Brew

O Brew é um gerenciador de pacotes para o MacOs que vai nos ajudar no setup aqui. Eu utilizo ele com uma certa frequência, inclusive para instalação do Node.  
  
Navegue para [o site oficial](https://brew.sh/index_pt-br) dele e siga a instrução de instalação, normalmente é apenas uma linha de comando no seu terminal.

### Configurações

Em seguida, reinicie o terminal e execute os seguintes comandos:

*   brew update
*   brew install --HEAD usbmuxd
*   brew link usbmuxd
*   brew install --HEAD libimobiledevice
*   brew install ideviceinstaller
*   brew install ios-deploy cocoapods
*   pod setup

### Devices iOS

Com o setup pronto, plugue seu device iOS na sua máquina. A mensagem de 'Trust this device' deve aparecer na tela do seu iOS, clique em **TRUST** para que o mesmo seja habilitado.

Neste momento, seu dispositivo já deve estar aparecendo na lista de devices quando você executa o comando **flutter devices**.

### Configurando seu App

Ainda precisamos 'assinar' nosso App para execução no device, e a má notícia é que você precisa de uma licença de desenvolvedor da Apple para isto. Caso não tenha, o XCode te dará as instruções.  
  
Abra o XCode e abra o arquivo **ios/runner.xcworkspace** do App que criamos anteriormente.  
![](https://baltaio.blob.core.windows.net/blog/flutter-ios-macos-009.png)  
Clique em **Runner** na lateral direira e em seguida clique em **Add Account** para autenticar com sua conta Apple.  
![](https://baltaio.blob.core.windows.net/blog/flutter-ios-macos-010.png)  
Com sua conta autenticada, selecione o time de desenvolvimento caso tenha mais de um. Sua aplicação será assinada.  
![](https://baltaio.blob.core.windows.net/blog/flutter-ios-macos-011.png)  
Para finalizar, coloque um identificador único na sua aplicação.  
![](https://baltaio.blob.core.windows.net/blog/flutter-ios-macos-012.png)  
Pronto, basta executar um **flutter run** e desfrutar da sua aplicação rodando em seu dispositivo iOS.  
![](https://baltaio.blob.core.windows.net/blog/flutter-ios-macos-013.png)


<div role="main" id="blog-s1-flutter-15c15f0b7777b4675fad"></div>
<script type="text/javascript" src="https://d335luupugsy2.cloudfront.net/js/rdstation-forms/stable/rdstation-forms.min.js"></script>
<script type="text/javascript"> new RDStationForms('blog-s1-flutter-15c15f0b7777b4675fad', 'UA-48664517-12').createForm();</script>
