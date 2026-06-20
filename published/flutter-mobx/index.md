Flutter e MobX parece um casamento perfeito, e se você está procurando algo para gestão de estado dos seus Apps, talvez este seja o casal certo.

É importante que antes de falar de MobX, você esteja por dentro do [que é gestão de estado e quais modelos temos no Flutter](https://balta.io/blog/flutter-setstate-bloc-mobx-state-management), para então entender por quê o MobX se destaca.

Mas deixando a polêmica de lado, vamos falar do MobX em sí e do seu uso no Flutter, em como podemos tirar proveito desta biblioteca.

O que é o MobX?
---------------

Antes de mais nada, o MobX não é algo exclusivo do Flutter, ele existe para outras plataformas como a Web por exemplo, embora não seja tão popular lá.

O MobX é uma biblioteca para gestão de estado, o que significa que podemos utilizar ele para estados locais (páginas) ou globais (com _Provider_ por exemplo).

O MobX se baseia em observáveis, ações e reação, um conceito que vem da programação reativa. No caso toda ação executada gera uma reação, que procura um observável para atualizá-lo.

### MobX e Gestão de Estado

Como sabemos, no Flutter não temos a tela sendo renderizada o tempo todo, precisamos chamar o **setState** para esta ação, e isto vai se complicando a medida que vamos tendo atualizações em **Widgets** que estão mais distantes ou mesmo em outras árvores da nossa aplicação.

O MobX basicamente resolve isto criando um **Observable** ao redor de um **Widget** que queremos manter sempre atualizado. Neste **Widget** então, podemos referenciar uma propriedade marcada como **_@observable_** e toda vez que ela for alterada, seu resultado se reflete na tela.

A única regra aqui é que você 'deve' passar por uma ação (**_@action_**) para alterar um observável. Na verdade você pode até alterar um observável sem passar por uma ação, mas a ideia é concentrar as regras de negócio nestas ações e manter tudo mais organizado.

Esta ação produz uma reação que atualiza o observável na tela, igual descrevi no começo deste artigo.

### MobX vs Streams

Neste momento você deve estar se perguntando: _'Mas os Streams não fazem exatamente o mesmo?'_

Sim, os **Streams** também tem esta capacidade de alterar uma propriedade sem o uso do **setState**, porém, eles tem muito mais código e você precisa gerenciar bem eles. Se começar a esquecer **Streams** abertos, vira um **CAOS**!

Claro que toda esta mágica tem um custo, e o MobX também precisa de muito código para fazer isto, porém, ele gera boa parte deste código automático para nós.

Por exemplo, ao invés de precisarmos criar um Stream de entrada ou saída (Ou ambos) para um observável, podemos simplesmente decorar uma propriedade com **@observable** que o código adicional será gerado por baixo dos panos pelo MobX.

Desta forma, teremos um arquivo _'.g.dart'_ para cada arquivo que utilizarmos MobX, e estes arquivos não devem ser alterados em hipótese alguma. Toda alteração neles será perdida quando executar alguma mudança nos seus objetos.

Flutter e MobX - Instalação
---------------------------

Para utilizar o MobX com Flutter, vamos separar a instalação em duas partes, as dependências da aplicação, que de fato serão empacotadas com nosso App.

Além delas, teremos as dependências de desenvolvimento, que utilizaremos apenas na 'nossa máquina' para gerar o código adicional que comentei.

### Criando a aplicação

Se você ainda não configurou seu ambiente para trabalhar com Flutter, dê uma olhada neste artigo onde separamos [como você pode começar com Flutter](https://balta.io/blog/flutter-por-onde-comecar).

Abra seu emulador ou simulador favorito, abra uma instância do **Visual Studio Code**, pressione **CTRL (CMD)+SHIFT+P** e selecione _Flutter: New Project_.

No meu caso, defini o nome como **flumobx**, se quiser utilizar o mesmo nome para total compatibilidade. De qualquer formas, os fontes estão no final deste artigo.

### Adicionando os pacotes

A primeira coisa que temos que fazer é abrir o arquivo pubspec.yaml e adicionar as seguintes dependências:

    dependencies:
        flutter:
            sdk: flutter
        mobx: ^1.0.0
        flutter_mobx: ^1.0.0

A dependência **mobx** traz o código base do MobX para nossa aplicação, enquanto a **flutter\_mobx** traz a implementação do MobX sobre o Flutter. Precisamos de ambas neste caso.

### Adicionando as dependências de desenvolvimento

Como comentamos previamente, o MobX faz mágica gerando código para nós, porém, precisamos de dois pacotes para que ele consiga executar esta geração.

O primeiro pacote é o mobx_codegen que é de fato quem vai olhar para os observáveis e gerar o código necessário por baixo dos panos para menter nossa tela atualizada.

O segundo é o build_runner, que é quem vai interagir com o mobx_codegen para possibilitar que este código seja gerado. Como resultado temos as seguintes dependências de desenvolvimento.

    dev_dependencies:
        flutter_test:
            sdk: flutter
        build_runner: ^1.7.2
        mobx_codegen: ^1.0.0

Ao salvar o arquivo, o Flutter tentará restaurar os pacotes, se tudo der certo, você verá a mensagem 'exit code 0'_ na aba **output** do terminal do Visual Studio Code.

Você pode executar este processo manualmente também, utilizando o comando **flutter packages get**, mas normalmente o automático já funciona.

Adicionando um Store
--------------------

Quando falamos em gestão de estado, precisamos de um local para armazenar este, e no caso do MobX (E outros como Redux) chamamos de **Stores**.

Podemos ter vários Stores, um se comunicando com outro, mas este é um assunto mais avançado, para os próximos artigos. Vamos focar aqui em criar um único **Store** que servirá de base para nossa tela.

Primeiramente, vamos pensar em um item que comentei no começo deste artigo, sobre separar as regras de negócio, remover elas dos Widgets e colocar em Actions.

Então, antes de pensar em MobX, vamos pensar nesta tela inicial que temos do Flutter, deste App padrão que nos é gerado, onde temos um número sendo exibido na tela e um botão para incrementá-lo.

Dada esta situação, temos visivelmente uma variável, numérica, que receberá o incremento, e uma função para executar este incremento em mais um, toda vez que o botão é pressionado.

Pensando deste modo, vamos criar uma classe **_lib/counter.dart_** com o mínimo de código possível para execução do App, como mostrado abaixo.

    class Counter {
        int value = 0;

        increment() {
            value++;
        }
    }

Utilizando nosso contador
-------------------------

Com nossa primeira versão do contador criado (Ele ainda não é um Store), vamos refatorar nossa tela para utilizá-lo, e o primeiro passo é importá-lo no topo do arquivo **_lib/main.dart_** e criar uma instância da nossa classe **Counter**.

    import 'package:flumobx/counter.dart';
    import 'package:flutter/material.dart';

    void main() => runApp(MyApp());

    final counter = Counter();

    class MyApp extends StatelessWidget {
    ...

Desta forma, não precisaremos mais da variável **_\_counter_**, declarada na classe **_\_MyHomePageState_**, então vamos excluí-la e utilizar nosso contador.

Toda vez que clicamos no botão incrementar, a função **\_incrementCounter** é chamada, vamos alterar sua execução para chamar nosso contador ao invés de incrementar manualmente.

    void _incrementCounter() {
        setState(() {
            // This call to setState tells the Flutter framework that something has
            // changed in this State, which causes it to rerun the build method below
            // so that the display can reflect the updated values. If we changed
            // _counter without calling setState(), then the build method would not be
            // called again, and so nothing would appear to happen.
            counter.increment(); // Antes era _counter++;
        });
    }

Note que a ação de executar um incremento, embora seja básica, não deixa de ser uma regra de negócio, e agora nossas regras estão no contador e não mais na tela.

Ainda precisamos de mais uma alteração, na exibição do valor, que é um _Text_ quase no fim do código. Agora vamos exibir o valor do contador ao invés de **_counter**.

    Text(
        '${counter.value}', // Antes era $_counter
        style: Theme.of(context).textTheme.display1,
    ),

Tudo pronto, se você reiniciar a aplicação tudo deve estar funcionando como antes, porém com uma separação melhor de código. Até o momento o código completo do arquivo lib/main.dart é este:

    import 'package:flumobx/counter.dart';
    import 'package:flutter/material.dart';

    void main() => runApp(MyApp());

    final counter = Counter();

    class MyApp extends StatelessWidget {
        // This widget is the root of your application.
        @override
        Widget build(BuildContext context) {
            return MaterialApp(
            title: 'Flutter Demo',
            theme: ThemeData(
                // This is the theme of your application.
                //
                // Try running your application with "flutter run". You'll see the
                // application has a blue toolbar. Then, without quitting the app, try
                // changing the primarySwatch below to Colors.green and then invoke
                // "hot reload" (press "r" in the console where you ran "flutter run",
                // or simply save your changes to "hot reload" in a Flutter IDE).
                // Notice that the counter didn't reset back to zero; the application
                // is not restarted.
                primarySwatch: Colors.blue,
            ),
            home: MyHomePage(title: 'Flutter Demo Home Page'),
            );
        }
    }

    class MyHomePage extends StatefulWidget {
        MyHomePage({Key key, this.title}) : super(key: key);

        // This widget is the home page of your application. It is stateful, meaning
        // that it has a State object (defined below) that contains fields that affect
        // how it looks.

        // This class is the configuration for the state. It holds the values (in this
        // case the title) provided by the parent (in this case the App widget) and
        // used by the build method of the State. Fields in a Widget subclass are
        // always marked "final".

        final String title;

        @override
            _MyHomePageState createState() => _MyHomePageState();
        }

        class _MyHomePageState extends State<MyHomePage> {
            void _incrementCounter() {
                setState(() {
                // This call to setState tells the Flutter framework that something has
                // changed in this State, which causes it to rerun the build method below
                // so that the display can reflect the updated values. If we changed
                // _counter without calling setState(), then the build method would not be
                // called again, and so nothing would appear to happen.
                counter.increment();
                });
            }

            @override
            Widget build(BuildContext context) {
                // This method is rerun every time setState is called, for instance as done
                // by the _incrementCounter method above.
                //
                // The Flutter framework has been optimized to make rerunning build methods
                // fast, so that you can just rebuild anything that needs updating rather
                // than having to individually change instances of widgets.
                return Scaffold(
                appBar: AppBar(
                    // Here we take the value from the MyHomePage object that was created by
                    // the App.build method, and use it to set our appbar title.
                    title: Text(widget.title),
                ),
                body: Center(
                    // Center is a layout widget. It takes a single child and positions it
                    // in the middle of the parent.
                    child: Column(
                    // Column is also a layout widget. It takes a list of children and
                    // arranges them vertically. By default, it sizes itself to fit its
                    // children horizontally, and tries to be as tall as its parent.
                    //
                    // Invoke "debug painting" (press "p" in the console, choose the
                    // "Toggle Debug Paint" action from the Flutter Inspector in Android
                    // Studio, or the "Toggle Debug Paint" command in Visual Studio Code)
                    // to see the wireframe for each widget.
                    //
                    // Column has various properties to control how it sizes itself and
                    // how it positions its children. Here we use mainAxisAlignment to
                    // center the children vertically; the main axis here is the vertical
                    // axis because Columns are vertical (the cross axis would be
                    // horizontal).
                    mainAxisAlignment: MainAxisAlignment.center,
                    children: <Widget>[
                        Text(
                            'You have pushed the button this many times:',
                        ),
                        Text(
                            '${counter.value}',
                            style: Theme.of(context).textTheme.display1,
                        ),
                    ],
                    ),
                ),
                floatingActionButton: FloatingActionButton(
                    onPressed: _incrementCounter,
                    tooltip: 'Increment',
                    child: Icon(Icons.add),
                ), // This trailing comma makes auto-formatting nicer for build methods.
            );
        }
    }

Flutter e MobX
--------------

Até o momento tudo que fizemos foi separar nosso código, mas de fato não utilizamos nada do MobX ainda, tanto que na tela estamos ainda fazendo uso do **SetState** para reagir as mudanças.

Vamos então refatorar nosso contador para utilizar os recursos do MobX e desfrutar de tudo que comentamos neste artigo até o momento.

### Flutter e MobX - Stores

A primeira coisa que temos que fazer é de fato transformar nosso contador em um **Store**, e para isto utilizamos um **Mixin**. Os mixins são uma maneira de reutilizar o código de uma classe em várias hierarquias de classes.

Parece algo difícil, mas é parte do conceito de herança que temos, então toda vez que utilizamos a palavra reservada **with**, estamos nos referindo ao **Mixin**.

Desta forma, para tornar nossa classe um Store, tudo que precisamos fazer é importar o pacote do MobX e em seguida adicionar o trecho **with Store** em nossa classe.

    import 'package:mobx/mobx.dart';

    class Counter with Store {
        int value = 0;

        increment() {
            value++;
        }
    }

Desta forma, temos a nossa classe _**Counter**_ transformada em um **Store**, mas CALMA, ainda tem mais coisas que precisamos discutir aqui.

### Flutter e MobX - Observables

Nosso próximo passo é fazer a magia do MobX aparecer, ou seja, fazer com que nossa tela se atualize automaticamente, sem a necessidade de chamar o **SetState** a cada mudança.

Como conversamos anteriormente neste artigo, para realizar esta ação precisamos de um observável, ou seja, um tipo no qual nosso Widget ficará sempre olhando para ele.

Chamamos este padrão de Publish/Subscriber, onde vários Widgets podem se inscrever para receber uma notificação a cada alteração de uma variável.

No Flutter MobX, para tornar um objeto observável, basta adicionar o decorador @observable sobre a variável que desejarmos, como mostrado abaixo.

    import 'package:mobx/mobx.dart';

    class Counter with Store {
        @observable
        int value = 0;

        increment() {
            value++;
        }
    }

Pronto, desta forma já temos uma variável em nosso **Store** que pode ser observada por diversos Widgets, mas ainda não terminou.

### Flutter e MobX - Actions

Lembra no começo deste artigo quando dissemos que havia uma regra no MobX onde os observáveis só seriam alterados através de ações.

Se olharmos nosso contador, ele já tem este tipo de comportamento, pois temos um método chamado **increment** que faz o incremento do nosso valor.

Desta forma, tudo que precisamos fazer é decorar este método com **@action**, afinal as ações nada mais são do que métodos que contém nossas regras e alteram nossos observáveis (_Estado_).

    import 'package:mobx/mobx.dart';

    class Counter with Store {
        @observable
        int value = 0;

        @action
        increment() {
            value++;
        }
    }

Pronto, temos um observável e uma ação!

### Gerando o Código

Se testarmos este código, veremos que nada mudou, que ainda precisamos do SetState para alterar o estado da tela.

Isto ocorre pois a mágica do MobX ainda não aconteceu. Lembra que comentamos no começo deste artigo que ele **_gera um código para nós_**?

Pois bem, o que precisamos fazer agora é pedir ao MobX que gere todo código para gerenciar essas ações e observáveis que decoramos em nosso **Store**.

Isto pode ser feito utilizando o comando abaixo:

    flutter packages pub run build_runner build

Este comando vai olhar para os arquivos que utilizam o **Mixin Store** (_with Store_) e gerar um arquivo secundário para eles, com a extensão '_.g.dart_'.

Sempre que fizermos alguma alteração em nossos **Stores**, precisamos executar este comando novamente, e novos arquivos '_.g.dart_' serão gerados.

Por este motivo, de nada adianta alterar estes arquivos. Toda alteração será perdida quando uma nova versão for gerada.

Em adicional temos o comando **_CLEAN_**, para limpar todos estes arquivos gerados. Caso enfrente algum problema, você pode executar um **_CLEAN_** e depois um **_BUILD_** para garantir que foi tudo gerado corretamente.

    flutter packages pub run build_runner clean

Porém não adianta executar este comando ainda, precisamos de uma última modificação em nosso **Store** para finalizá-lo.

### Unificando os arquivos

Até o momento vimos como externalizar nossas regras, trabalhar com **Store** e na última sessão, como gerar o código mágico do MobX para ter o suporte às ações e observáveis.

Porém, agora precisamos unificar o código gerado pelo MobX com o nosso, tornando um único **Store** para nosso App consumir.

A primeira coisa que precisamos fazer é importar o código gerado, não se preocupe se ele não existe ainda, afinal nós sabemos que ele existirá e qual o nome dele.

Deste modo, após a importação do MobX no lib/counter.dart, vamos fazer a importação do arquivo com código complementar que será gerado automaticamente.

    part 'counter.g.dart';

Note que o nome do arquivo gerado sempre será 'arquivo.g.dart'.

Todo arquivo gerado pelo MobX será uma classe, contendo código específico, relacionado aos decoradores que utilizamos em nosso Store.

Nosso desafio agora é unificar estas duas classes em uma só, fornecendo ao nosso App um único acesso ao **Store**.

Como vimos previamente, podemos fazer esta junção utilizando um Mixin (with), assim como fizemos com **Store** no contador, e aqui não é diferente.

A classe gerada pelo MobX segue um padrão colocando um __$_ na frente do nome da nossa classe. Então se chamamos nossa classe de Counter, o nome gerado pelo MobX será __$Counter**_.

Sabendo disto, podemos criar uma nova classe em nosso arquivo lib/counter.dart, antes da nossa classe Counter que vai unificar as duas.

    class MeuCounter = Counter with _$Counter;

Este não é nosso código final ainda, mas note que geramos uma nova classe chamada **_MeuCounter_** que é igual a junção do nosso **Counter** mais o código gerado pelo MobX.

### Limitando o Acesso

Esta implementação AINDA não está completa. Neste formato, nada impede que alguém tenha uma nova instância do nosso _Counter_ ao invés de usar o _MeuCounter_ que é a unificação de ambos.

Na verdade, quem for consumir este código ficará confuso, pois tem duas classes neste arquivo, ambas sobre contador. Qual a correta?

Aqui entra um conceito muito legal da orientação à objetos, onde temos as classes Abstratas.

As classes abstratas definem comportamentos padrões mas elas **_NÃO PODEM SER INSTANCIADAS_**, ou seja, não conseguimos criar um objeto a partir de uma classe abstrata.

Em adicional, os nomes que usamos nas classes não ficaram concisos. Por exemplo, _MeuContador_ ficou estranho, então seguindo uma convenção sugerida pela própria documentação do MobX, vamos utilizar um '_' na nossa classe.

Desta forma podemos deixar o nome **_Counter_** para a classe final, que será utilizada pelo nosso App. O código final do nosso contador fica assim.

    import 'package:mobx/mobx.dart';
    part 'counter.g.dart';

    class Counter = _Counter with _$Counter;

    abstract class _Counter with Store {
        @observable
        int value = 0;

        @action
        increment() {
            value++;
        }
    }

Para finalizar, vamos executar o comando para gerar nosso código automático novamente.

    flutter packages pub run build_runner build

Se tudo deu certo, agora você tem um arquivo _**lib/counter.g.dart**_ e seu código está compilando corretamente.

### Flutter e MobX - Observer

Estamos quase lá, agora faltam apenas algumas modificações na tela para tudo funcionar e a primeira delas é importar o MobX na lib/main.dart.

    import 'package:flutter_mobx/flutter_mobx.dart';

A segunda modificação que vamos fazer é excluir a função **__incrementCounter_**, já que podemos chamar diretamente a função **increment** do nosso **Counter** no botão.

Para isto, vamos precisar de uma instância do nosso **Counter**, que pode ser definida logo abaixo do método **_main_**.

    final counter = Counter();

Esta instância está marcada como **_final_** pois nunca será alterada posteriormente a sua criação.

Seguindo para o nosso botão, quase no fim do arquivo, vamos alterar sua propriedade **_onPressed_** para chamar a função **increment** do nosso Counter.

    onPressed: counter.increment,

Note que ao chamar a função **_increment_**, não abrimos parenteses, pois isto faria com que a função fosse imediatamente chamada.

Por fim, precisamos exibir o valor do contador na tela, e para isto precisaremos de um Widget adicional, chamado de **Observer**.

Este Widget é o que conecta nossa tela ao nosso Store e fica reagindo às mudanças que ocorrem lá. Se apenas colocarmos algum valor que está no Store na tela, o mesmo não reagirá as mudanças.

Desta forma, vamos envolver o **_Widget Text_** que exibi o número de cliques na tela, com o **_Widget Observer_**, neste formato.

    Observer(
        builder: (_) => Text(
            '${counter.value}',
            style: Theme.of(context).textTheme.display1,
        ),
    ),

O **Observer** não possui um **_child_** e sim o **_builder_** que é uma função. Como não queremos passar nenhum parâmetro nesta função, simplesmente utilizamos um '\_'.

O código final do arquivo lib/main.dart pode ser visto abaixo.

    import 'package:flumobx/counter.dart';
    import 'package:flutter/material.dart';
    import 'package:flutter_mobx/flutter_mobx.dart';

    void main() => runApp(MyApp());

    final counter = Counter();

    class MyApp extends StatelessWidget {
        @override
        Widget build(BuildContext context) {
            return MaterialApp(
                title: 'Flutter Demo',
                theme: ThemeData(
                    primarySwatch: Colors.blue,
                ),
                home: MyHomePage(title: 'Flutter Demo Home Page'),
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
        @override
        Widget build(BuildContext context) {
            return Scaffold(
                appBar: AppBar(
                    title: Text(widget.title),
                ),
                body: Center(
                    child: Column(
                    mainAxisAlignment: MainAxisAlignment.center,
                    children: <Widget>[
                        Text(
                            'You have pushed the button this many times:',
                        ),
                        Observer(
                            builder: (_) => Text(
                                '${counter.value}',
                                style: Theme.of(context).textTheme.display1,
                            ),
                        ),
                    ],
                    ),
                ),
                floatingActionButton: FloatingActionButton(
                    onPressed: counter.increment,
                    tooltip: 'Increment',
                    child: Icon(Icons.add),
                ),
            );
        }
    }

Conclusão
---------

Parabéns, você criou seu primeiro App com Flutter e MobX e espero que tenha entendido os conceitos básicos dele aqui.

Ainda há muito o que aprender sobre MobX, mas vamos ver com calma nos próximos artigos.

Não deixe de ver nossa **[carreira Flutter Mobile Developer](https://balta.io/carreiras/flutter-mobile-developer)**, ela tem vários cursos gratuitos e te dará uma boa base em seus estudos.

### Código Fonte

O código fonte deste aplicativo pode ser encontrado aqui:

[https://github.com](https://github.com/andrebaltieri/flutter-mobx-getting-started)[/andrebaltieri/flutter-mobx-getting-started](https://github.com/andrebaltieri/flutter-mobx-getting-started)

<div role="main" id="blog-s1-flutter-15c15f0b7777b4675fad"></div>
<script type="text/javascript" src="https://d335luupugsy2.cloudfront.net/js/rdstation-forms/stable/rdstation-forms.min.js"></script>
<script type="text/javascript"> new RDStationForms('blog-s1-flutter-15c15f0b7777b4675fad', 'UA-48664517-12').createForm();</script>
