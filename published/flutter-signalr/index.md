Flutter SignalR, vamos ver como implmentar a comunicação em tempo real entre aplicativos Web e Mobile utilizando estas tecnologias.

Pré-Requisitos
--------------

Para realizar este tutorial, você precisa do Flutter instalado e já ter dado alguns passos com ele. Se você ainda não começou com Flutter, [segue nosso artigo para dar seus primeiros passos](https://balta.io/blog/flutter-por-onde-comecar).

Para o lado do servidor, precisaremos do [.NET Core](https://dotnet.microsoft.com/download) instalado, se você ainda não tem ele ou o [Visual Studio Code](https://code.visualstudio.com/), faça a instalação de ambos antes de prosseguir.

Vamos começar com Flutter SignalR?

ASP.NET Core SignalR
--------------------

O _**SignalR**_ é um pacote que anda em conjunto com o ASP.NET Core e permite trabalharmos com comunicação em tempo real entre nossas aplicações utilizando **WebSockets**.

O SignalR funciona no modelo _cliente/servidor_, onde temos nosso servidor servindo mensagens para um indivíduo ou um grupo e diversos clientes consumindo estas mensagens.

Os grupos no SignalR são chamados de **Hubs**, e eles funcionam de fato como grupos de distribuição de mensagens. Basta enviar uma mensagem para um grupo e ele se encarrega de entregar.

Além dos **Hubs**, podemos enviar uma mensagem para um indivíduo em específico, direcionando a mensagem para seu nome de usuário.

Fora isto, podemos invocar as funções que estão em nossos **Hubs** diretamente dos clientes, utilizando bibliotecas para isto, como o flutter\_signalr que veremos aqui.

Para cada cliente (Desktop, Web, Mobile) é necessário uma biblioteca diferente para comunicação com nosso **Hub**, mas o SignalR provê muitas delas já prontas.

Flutter SignalR: Criando o Servidor
-----------------------------------

Nosso primeiro passo aqui é criar nosso servidor de mensagens, que será responsável por receber e enviar as informações para nossos Apps.

### Criando o Projeto

Vamos iniciar criando nosso projeto com auxílio do **dotnet-cli**, via linha de comando, executando o seguinte.

    dotnet new webapp -o SignalRChat

Este comando vai criar uma aplicação Web utilizando ASP.NET Core e Razor Pages. Para este cenário, não importa muito o tipo do projeto (Razor Pages, MVC, etc..).

Abra este projeto com o **_Visual Studio Code_** e estaremos prontos para começar.

Caso esteja com dificuldades na criação do projeto ou seja seu primeiro contato com ASP.NET Core, realize nossa [carreira Backend .NET Developer antes](https://balta.io/carreiras/backend-developer-dotnet).

### Adicionando SignalR

Como comentamos previamente, o **SignalR** é um biblioteca que age em conjunto com o **ASP.NET Core**, e ela é dividia em duas partes, cliente e servidor.

Neste caso, vamos instalar ambas bibliotecas neste projeto, mas você poderia deixar apenas o servidor rodando aqui e o cliente sendo uma outra aplicação.

Neste caso, você poderia tanto realizar a instalação como faremos aqui e depois copiar os arquivos JavaScript, necessários para o cliente se conectar ao servidor para sua aplicação, quanto utilizar um [CDN](https://cdnjs.com/libraries/aspnet-signalr).

O pacote do SignalR está distribuído através do [Nuget](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/), e já vem no pacote do ASP.NET Core, porém, os scripts (JavaScript) utilizados pelo cliente não.

O Flutter SignalR por exemplo será obtido de um pacote criado por terceiros, previamente neste artigo.

Podemos obtê-los utilizando o **_Libman_**, executando os seguintes passos.

    dotnet tool install -g Microsoft.Web.LibraryManager.Cli
    libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js

Desta forma, o Libman fará o download dos arquivos e já os colocará nas pastas corretas da nossa aplicação **Web**.

Ao término da execução você deverá ver um resultado parecido com este.

    wwwroot/js/signalr/dist/browser/signalr.js written to disk
    wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
    Installed library @microsoft/signalr@lates to wwwroot/js/signalr

### Criando um Hub

Com os pacotes adicionados, vamos agora criar o nosso **Hub**, que é o grupo de distribução que utilizaremos.

Desta forma, vamos criar uma nova pasta chamada **_Hubs_** e um arquivo chamado _**ChatHub.cs**_ dentro dela, com o seguinte conteúdo.

    using Microsoft.AspNetCore.SignalR;
    using System.Threading.Tasks;

    namespace SignalRChat.Hubs
    {
        public class ChatHub : Hub
        {
            public async Task SendMessage(string user, string message)
            {
                await Clients.All.SendAsync("ReceiveMessage", user, message);
            }
        }
    }

Um Hub nada mais é do que uma _classe_ que herda da classe base **Hub**. Esta classe contém métodos como o _**SendMessage**_ que poderemos invocar dos clientes posteriormente.

Para disseminar a mensagem podemos utilizar o **_Clients_**, que pode tanto enviar uma mensagem para um indivíduo em específico, quanto para um grupo, quanto para todos.

Neste caso, estamos utilizando o **_Clients.All_** para enviar o usuário e a mensagem que recebemos para **TODOS** que estão conectados a este servidor.

### Configurando o SignalR

Nosso próximo passo é configurar nossa aplicação para utilizar o **SignalR**, e isto requer apenas três passos, sendo o primeiro, a adição do **Namespace** do **SignalR** no topo do arquivo **_Startup.cs_**.

    using SignalRChat.Hubs;

Em seguida, vamos adicionar o serviço do SignalR no método **ConfigureServices**, logo após o _services.AddRazorPages_.

    services.AddSignalR();

E por fim, vamos mapear as rotas do nosso Hub no método **Configure**, adicionando a linha abaixo logo após o _endpoints.MapRazorPages_.

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapRazorPages();
        endpoints.MapHub<ChatHub>("/chatHub"); // Linha adicionada
    });

Note que ao configurar um endpoint utilizando o **MapHub**, passamos seu nome, que será concatenado com a URL padrão da nossa aplicação, neste caso _https://localhost:5001_.

Isto quer dizer que qualquer requisição para nosso Hub deve ser feita com base na URL **_https://localhost:5001/chatHub_**.

### Configurando o Cliente Web

Com o SignalR instalado e configurado, vamos agora criar um cliente Web simples apenas para testá-lo. Não vamos nos prender ao HTML/CSS dele, o foco aqui é comunicação com **_Flutter_**.

Desta forma, substitua o conteúdo do arquivo Pages/index.cshtml pelo conteúdo abaixo.

    @page
    @model IndexModel
    @{
        ViewData["Title"] = "Home page";
    }

    <div class="container">
        <div class="row">&nbsp;</div>
        <div class="row">
            <div class="col-2">User</div>
            <div class="col-4"><input type="text" id="userInput" /></div>
        </div>
        <div class="row">
            <div class="col-2">Message</div>
            <div class="col-4"><input type="text" id="messageInput" /></div>
        </div>
        <div class="row">&nbsp;</div>
        <div class="row">
            <div class="col-6">
                <input type="button" id="sendButton" value="Send Message" />
            </div>
        </div>
    </div>
    <div class="row">
        <div class="col-12">
            <hr />
        </div>
    </div>
    <div class="row">
        <div class="col-6">
            <ul id="messagesList"></ul>
        </div>
    </div>

    <script src="~/js/signalr/dist/browser/signalr.js"></script>
    <script src="~/js/chat.js"></script>

Nesta página temos apenas três elementos que importam, um Input para o usuário, um Input para mensagem e um botão que vai enviar a mensagem para nosso servidor.

Ao final da página temos duas chamadas _JavaScript_, uma para o arquivo que foi baixado pelo **Libman** previamente (**_/js/signalr/dist/browser/signalr.js_**) e outro para o arquivo **_chat.js_** que ainda não criamos.

Caso queira separar seu Frontend do servidor, basta copiar o **_signalr.js_** e **_chat.js_** que mencionados nas inclusões acima para seu projeto separado.

Como nosso foco aqui é o Flutter, não vou entrar em detalhes sobre a implementação em JavaScript da comunicação desta aplicação com o servidor. Podemos tratar isto em outro artigo.

Desta forma, vamos criar um novo arquivo em **_wwwroot/js/chat.js_** com o seguinte código.

    "use strict";

    var connection = new signalR.HubConnectionBuilder().withUrl("/chatHub").build();

    //Disable send button until connection is established
    document.getElementById("sendButton").disabled = true;

    connection.on("ReceiveMessage", function (user, message) {
        var msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
        var encodedMsg = user + " says " + msg;
        var li = document.createElement("li");
        li.textContent = encodedMsg;
        document.getElementById("messagesList").appendChild(li);
    });

    connection.start().then(function () {
        document.getElementById("sendButton").disabled = false;
    }).catch(function (err) {
        return console.error(err.toString());
    });

    document.getElementById("sendButton").addEventListener("click", function (event) {
        var user = document.getElementById("userInput").value;
        var message = document.getElementById("messageInput").value;
        connection.invoke("SendMessage", user, message).catch(function (err) {
            return console.error(err.toString());
        });
        event.preventDefault();
    });

### Executando a aplicação

Isto é tudo que precisamos para ter um chat funcionando com ASP.NET Core, SignalR e JavaScript (Aplicação Web), e você já pode testar executando o seguinte comando.

    dotnet watch run

Se tudo deu certo, ao abrir seu **_Browser_** no endereço **https://localhost:5001** você verá a aplicação rodando e conseguirá enviar mensagens. Abra diversos Browsers e verá que eles se comunicam entre si.

Caso ocorra algum erro de certificado, você pode executar as seguintes linhas de comando para aceitar os certificados utilizados pelas aplicações ASP.NET Core.

    dotnet dev-certs https --clean
    dotnet dev-certs https --trust

Flutter SignalR: Criando o App
------------------------------

Agora vamos focar nossos esforços em fazer o Flutter se conectar ao nosso servidor e possibilitar o recebimento de mensagens em tempo real em nosso App.

### Criando o App

O primeiro passo é criar um App novo em branco, então abra o **_Visual Studio Code_**, execute o comando **CTRL(CMD)+SHIFT+P** e selecione **Flutter New Project**.

Caso queira seguir o mesmo nome, nosso App utilizou **_flusigr_**, desta forma seu código será totalmente compatível com o nosso. Não esqueça de abrir seu Emulador preferido ou conectar-se ao seu telefone para rodar o App.

### Instalando o pacote

Como comentamos anteriormente, para se conectar ao servidor, precisamos de um cliente, e no caso do Flutter este pacote se chama [signalr_client](https://pub.dev/packages/signalr_client).

Desta forma, vamos abrir o nosso **_pubspect.yaml_** e adicionar a seguinte referência.

    dependencies:
        flutter:
            sdk: flutter

        # The following adds the Cupertino Icons font to your application.
        # Use with the CupertinoIcons class for iOS style icons.
        cupertino_icons: ^0.1.2
        signalr_client: ^0.1.6 # Referência do signalR

Isto é tudo que precisamos no momento, vamos agora rodar nossa aplicação para testar se tudo está funcionando. Como resultado, você deve ver a aplicação padrão que o **Flutter** nos cria, com um contador na tela.

### Aceitando certificados HTTPS inválidos

O primeiro problema que vamos enfrentar é que nosso servidor está sobre **HTTPS** mas não tem um certificado válido. Isto implicará em um erro de certificado que temos que corrigir antes de começar.

O que vamos fazer é sobrescrever as requisições **HTTP** do nosso App, aceitando os certificados inválidos. Este código na verdade não faz parte de nada do **SignalR** e talvez você não passe por este problema.

Desta forma, vamos alterar o método **main** do arquivo **_main.dart_** para executar a ação que mencionamos acima.

    import 'dart:io';

    // Esta classe permite acesso ao LocalHost com certificados HTTPS inválidos
    class MyHttpOverrides extends HttpOverrides {
        @override
        HttpClient createHttpClient(SecurityContext context) {
            HttpClient client = super.createHttpClient(context);
            client.badCertificateCallback =
                (X509Certificate cert, String host, int port) => true;
            return client;
        }
    }

    void main() {
        HttpOverrides.global = new MyHttpOverrides();
        runApp(MyApp());
    }

Com este acesso garantido, nosso servidor estará disponível no seguinte endereço **_https://10.0.2.2:5001/chatHub_**, onde **10.0.2.2** é o endereço do **LocalHost** mapeado dentro do emulador e **/chatHub** é o endereço do **Hub** que criamos no servidor.

**IMPORTANTE:** Não se esqueça de deixar o servidor rodando, utilizando o comando **_dotnet watch run_** na outra instância do Visual Studio Code ou em algum terminal.

### Flutter SignalR: Conectando ao Servidor

O primeiro passo para nos conectarmos Flutter SignalR é fazer a importação da biblioteca que instalamos previamente, o [signalr_client](https://pub.dev/packages/signalr_client).

    import 'package:signalr_client/signalr_client.dart';

Com esta importação, podemos utilizar o **HubConnectionBuilder** pare realizar a conexão e posteriormente ficar ouvindo/enviando as mensagens.

A instância do **HubConnectionBuilder** por sua vez, exige uma URL, que vimos previamente aqui. Vamos então criar uma nova instância dele já no começo da nossa classe **__MyHomePageState_**.

    class _MyHomePageState extends State<MyHomePage> {
        final hubConnection = HubConnectionBuilder().withUrl("https://10.0.2.2:5001/chatHub").build();
        ...

Note que utilizamos o método **withUrl** informando a URL do nosso servidor, que no caso é **_https://10.0.2.2:5001/chatHub_**.

Por fim, precisamos iniciar esta conexão, e o melhor lugar para isto é durante a inicialização do nosso componente, no método **initState**.

Porém, o método que inicia a conexão é _assíncrono_, e o **initState** não, então vamos criar um método separado, chamado **startConnection** e chamá-lo no **initState**.

    class _MyHomePageState extends State<MyHomePage> {
        final hubConnection = HubConnectionBuilder().withUrl("https://10.0.2.2:5001/chatHub").build();
        
        @override
        void initState() {
            super.initState();
            startConnection();
        }
        
        void startConnection() async {
            await hubConnection.start(); // Inicia a conexão ao servidor
        }
        ...

### Flutter SignalR: Ouvindo as Mensagens

Nosso desafio agora é ouvir as mensagens que estão sendo enviadas pelo servidor, e para isto utilizaremos a instância do **HubConnectionBuilder** que acabamos de criar.

Toda vez que recebemos uma mensagem, temos uma lista de objetos e caso seu servidor envie um JSON, você pode tipar esta lista de objetos aqui também.

Neste caso o pacote **_signalr\_client_** vai utilizar o **dart:convert** para tentar converter seu JSON para objeto, ou seja, seu objeto precisa ter o método **fromJson**.

No nosso caso, vamos trabalhar com lista de objetos mesmo, sendo o primeiro o usuário e o segundo a mensagem que ele enviou.

Para iniciar, vamos criar um método chamado **onReceiveMessage** que recebe uma lista de objetos como retorno.

    void onReceiveMessage(List<Object> result) {
        print(result);
    }

Agora tudo que precisamos fazer é utilizar o método **on** do **HubConnectionBuilder** para ficar ouvindo um **_Hub_** do nosso servidor, e sempre que uma nova mensagem chegar ele chamará nosso método acima.

    @override
    void initState() {
        super.initState();

        hubConnection.onclose((_) {
            print("Conexão perdida");
        });

        hubConnection.on("ReceiveMessage", onReceiveMessage);

        startConnection();
    }

Aproveitei para deixar a função **onclose** exibindo a mensagem 'conexão perdida' toda vez que nossa aplicação se desconectar do servidor.

Neste momento você pode rodar sua aplicação e testar o envio de mensagens da Web para seu App, ele deve printar as mensagens enviadas da aplicação Web no console do Flutter.

### Flutter SignalR: Exibindo as Mensagens

Para exibir as mensagens na tela, vamos primeiro armazená-las em uma lista de Strings, então vamos criar esta variável no começo da classe também.

    class _MyHomePageState extends State<MyHomePage> {
        final hubConnection = HubConnectionBuilder().withUrl("https://10.0.2.2:5001/chatHub").build();
        final List<String> messages = new List<String>();
        ...

Precisamos agora refatorar o método **onReceiveMessage** para popular esta lista, e vamos precisar utilizar o **setState** a cada mensagem recebida para que o Flutter atualize a tela.

    void onReceiveMessage(List<Object> result) {
        setState(() {
            messages.add("${result[0]} diz: ${result[1]}");
        });
    }

Note que estamos utilizando **_result[0]_** para o nome do usuário e **_result[1]_** para a mensagem. Se você quiser saber mais detalhes sobre o formato da mensagem, basta printá-la como anteriormente.

Vamos então utilizar um **ListView.builder** para exibir as mensagens na tela de forma dinâmica.

    @override
    Widget build(BuildContext context) {
        return Scaffold(
            appBar: AppBar(
            title: Text(widget.title),
            ),
            body: Padding(
            padding: const EdgeInsets.all(20.0),
            child: ListView.builder(
                itemCount: messages.length,
                itemBuilder: (ctx, i) {
                return Text(messages[i]);
                },
            ),
            ),
        );
    }

Muito bem, neste momento você deve estar visualizando as mensagens enviadas do cliente Web no seu App em tempo real utilizando Flutter SignalR!

### Flutter SignalR: Enviando Mensagens

Para enviar uma mensagem, precisaremos primeiro de um **InputText**, e para obter seu texto com facilidade, precisaremos de um **TextEditingController**.

    class _MyHomePageState extends State<MyHomePage> {
        final controller = new TextEditingController(); // Adicionado esta linha
        final hubConnection = HubConnectionBuilder().withUrl("https://10.0.2.2:5001/chatHub").build();
        final List<String> messages = new List<String>();
        ...

Para enviar mensagens para nosso servidor, podemos utilizar novamente nosso **HubConnectionBuilder**, mas desta vez com o método **Invoke**, que será capaz de chamar um método definido no nosso **_ChatHub.cs_** lá no servidor.

    void sendMessage() async {
        await hubConnection.invoke("SendMessage",
            args: <Object>["Flutter", controller.text]).catchError((err) {
            print(err);
        });
    }

Neste caso, estamos chamando o método **SendMessage** que definimos, passando como argumentos um array de objetos, sendo o primeiro parâmetro uma string fixa, chamada **_Flutter_** e o segundo o texto que estará em nosso **InputText**.

Tudo que precisamos agora é de um InputText e um botão para chamar o método **sendMessage** que acabamos de criar.

    @override
    Widget build(BuildContext context) {
        return Scaffold(
            appBar: AppBar(
                title: Text(widget.title),
            ),
            body: Padding(
                padding: const EdgeInsets.all(20.0),
                    child: ListView.builder(
                        itemCount: messages.length,
                        itemBuilder: (ctx, i) {
                            return Text(messages[i]);
                        },
                    ),
                ),
                bottomSheet: Container(
                color: Theme.of(context).accentColor,
                padding: EdgeInsets.all(20),
                height: 90,
                child: TextFormField(
                    controller: controller,
                    decoration: InputDecoration(labelText: "Mensagem..."),
                ),
            ),
            floatingActionButton: FloatingActionButton(
                child: Icon(Icons.send),
                onPressed: () {
                    sendMessage();
                },
            ),
        );
    }

O resultado final do arquivo **_main.dart_** fica no seguinte formato.

    import 'dart:io';
    import 'package:flutter/material.dart';
    import 'package:signalr_client/signalr_client.dart';

    // Esta classe permite acesso ao LocalHost com certificados HTTPS inválidos
    class MyHttpOverrides extends HttpOverrides {
        @override
        HttpClient createHttpClient(SecurityContext context) {
            HttpClient client = super.createHttpClient(context);
            client.badCertificateCallback =
                (X509Certificate cert, String host, int port) => true;
            return client;
        }
    }

    void main() {
        HttpOverrides.global = new MyHttpOverrides();
        runApp(MyApp());
    }

    class MyApp extends StatelessWidget {
        @override
        Widget build(BuildContext context) {
            return MaterialApp(
                title: 'Flutter SignalR',
                theme: ThemeData(
                    primarySwatch: Colors.blue,
                ),
                home: MyHomePage(title: 'Flutter SignalR'),
            );
        }
    }

    class MyHomePage extends StatefulWidget {
        MyHomePage({Key key, this.title}) : super(key: key);
        final String title;

        @override
        _MyHomePageState createState() => _MyHomePageState();
    }

    class _MyHomePageState extends State<MyHomePage> {
        final controller = new TextEditingController();
        final hubConnection = HubConnectionBuilder().withUrl("https://10.0.2.2:5001/chatHub").build();
        final List<String> messages = new List<String>();

        @override
        void initState() {
            super.initState();

            hubConnection.onclose((_) {
                print("Conexão perdida");
            });

            hubConnection.on("ReceiveMessage", onReceiveMessage);

            startConnection();
        }

        void onReceiveMessage(List<Object> result) {
            setState(() {
                messages.add("${result[0]} diz: ${result[1]}");
            });
        }

        void startConnection() async {
            await hubConnection.start();
        }

        void sendMessage() async {
            await hubConnection.invoke("SendMessage", args: <Object>["Flutter", controller.text]).catchError((err) {
                print(err);
            });
        }

        @override
        Widget build(BuildContext context) {
            return Scaffold(
                appBar: AppBar(
                    title: Text(widget.title),
                ),
                body: Padding(
                        padding: const EdgeInsets.all(20.0),
                        child: ListView.builder(
                        itemCount: messages.length,
                        itemBuilder: (ctx, i) {
                            return Text(messages[i]);
                        },
                    ),
                ),
                bottomSheet: Container(
                    color: Theme.of(context).accentColor,
                    padding: EdgeInsets.all(20),
                    height: 90,
                    child: TextFormField(
                        controller: controller,
                        decoration: InputDecoration(labelText: "Mensagem..."),
                    ),
                ),
                floatingActionButton: FloatingActionButton(
                    child: Icon(Icons.send),
                    onPressed: () {
                        sendMessage();
                    },
                ),
            );
        }
    }

Agora é só executar a aplicação que você tem um chat funcionando entre sua aplicação Web e sua App Flutter.

Conclusão
---------

Flutter SignalR podem trabalhar de forma conjunta e fácil. Neste artigo vimos o básico de como criar um Hub e enviar/receber mensagens por ele em aplicações Web ou Mobile.

O código fonte do servidor e do App desenvolvidos neste artigo [você encontra aqui](https://github.com/andrebaltieri/flutter-signalr).

Fontes
------

*   [Introdução ao ASP.NET Core SignalR](https://docs.microsoft.com/pt-br/aspnet/core/tutorials/signalr?view=aspnetcore-3.1&tabs=visual-studio-code)
*   [Pacote signalr\_client](https://pub.dev/packages/signalr_client)


<div role="main" id="blog-s1-flutter-15c15f0b7777b4675fad"></div>
<script type="text/javascript" src="https://d335luupugsy2.cloudfront.net/js/rdstation-forms/stable/rdstation-forms.min.js"></script>
<script type="text/javascript"> new RDStationForms('blog-s1-flutter-15c15f0b7777b4675fad', 'UA-48664517-12').createForm();</script>
