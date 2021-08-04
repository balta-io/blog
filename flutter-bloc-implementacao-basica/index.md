BLoC é a sigla para Business Logic Object Components, um padrão muito utilizado no Flutter e que causa muita confusão em relação ao seu conceito.  
  
Na verdade, o BLoC nada mais é do que a separação das regras de negócio da sua interface, algo recomendado em diversas tecnologias, como comento no curso [Modelando Domínios Ricos](https://balta.io/cursos/modelando-dominios-ricos) por exemplo, onde implementamos a mesma coisa, mas com nome diferente em .NET.  
  
Eu já falei algo mais conceitual sobre BLoC em um artigo aqui no blog chamado [Flutter: Provider, BLoC e State Management](https://balta.io/blog/flutter-bloc-provider-state-management), se está caindo de paraquedas aqui, sugiro que veja este artigo antes.

Quando utilizar
---------------

O mais importante sobre conhecer padrões é saber quando utilizá-los, e com o BLoC não é diferente. O BLoC serve para separar a regra de negócio da interface, mas o que isto quer dizer?  
  
Vamos imaginar que estejamos construindo uma aplicação que tenha duas interfaces, uma específica para Android e outra específica para iOS.  
  
Embora as telas sejam diferentes, a regra de negócio por trás delas é a mesma, ou seja, a forma como o usuário fará login, os cálculos a serem executados, tudo isto, será COMUM entre ambas interfaces.  
  
Aqui entra a real necessidade do uso do BLoC, se você não tem duas interfaces distintas, se não precisa reusar suas regras em lugares diferentes, então você não precisa do BLoC.  
  
Utilizar padrões desnecessariamente, causam o que chamamos de Overengeneering ou engenharia desnecessária, ele traz mais complexidade do que resolve problemas, ou seja, ao invés de ajudar, atrapalha.

Setup do Projeto
----------------

Para executar este projeto, vamos criar uma aplicação normalmente com Flutter, porém na estrutura de pastas, termos o seguinte:

*   lib
    *   android
        *   pages
        *   app.dart
    *   ios
        *   pages
        *   app.dart
    *   blocs

Na pasta Android, teremos o app.dart que contém o MaterialApp seguido das páginas que ficarão na pasta pages. No iOS teremos a mesma lógica.  
  
Na pasta blocs teremos a regra de negócio que será compartilhada entre ambas interfaces.  
  
A primeira coisa que precisamos fazer é separar as interfaces completamente, e para isto precisamos remover o MaterialApp do main.dart e externalizá-lo, afinal, não queremos ter nada do Cupertino no Material e vice-versa.  
  
Desta forma, nosso main.dart vai apenas chamar nosso AndroidApp ou iOSApp.

    import 'dart:io' show Platform;
    import 'package:flutter/material.dart';
    import 'package:imc/android/app.dart';
    import 'package:imc/ios/app.dart';

    void main() => Platform.isIOS ? runApp(IOSApp()) : runApp(AndroidApp());

Logo temos que criar nosso MaterialApp, e para isto vamos em lib/android/app.dart e cria-lo lá.

    import 'package:flutter/material.dart';
    import 'package:imc/android/pages/home.page.dart';

    class AndroidApp extends StatelessWidget {
        @override
        Widget build(BuildContext context) {
            return MaterialApp(
            title: "IMC",
            debugShowCheckedModeBanner: false,
            theme: ThemeData(
                primarySwatch: Colors.pink,
            ),
            home: HomePage(),
            );
        }
    }

Vamos repetir o processo para o CupertinoApp, que fica no caminho lib/ios/app.dart.

    import 'package:flutter/cupertino.dart';
    import 'package:imc/ios/pages/home.page.dart';

    class IOSApp extends StatelessWidget {
        @override
        Widget build(BuildContext context) {
            return CupertinoApp(
            title: "IMC",
            debugShowCheckedModeBanner: false,
            home: HomePage(),
            );
        }
    }

Pronto, quando nosso App iniciar em uma plataforma Android ele vai carregar o AndroidApp (Material) e quando iniciar em uma plataforma iOS vai carregar o iOSApp (Cupertino).  

Separando as regras de negócio
------------------------------

Nosso App vai realizar uma tarefa simples, calcular o IMC, que necessita do peso e altura da pessoa.  
  
O que NÃO QUEREMOS aqui é precisar executar o cálculo do IMC na HomePage (ainda vamos criar este arquivo) do Android e depois do iOS. Queremos reutilizar nosso cálculo em ambas telas.  
  
Outra similaridade na tela serão os campos e resultado, afinal para executar o cálculo precisamos do peso e altura, além de uma variável para exibir o resultado do cálculo. Isto deve estar presente em ambas as telas.  
  
O BLoC nada mais é do que uma classe que vai conter o peso, altura, resultado e uma função para calcular o IMC. Tudo que fazemos é literalmente tirar o código da UI.  
  
Neste caso, vamos criar um arquivo em lib/blocs/imc.bloc.dart, com o seguinte código.

    import 'package:flutter_masked_text/flutter_masked_text.dart';

    class ImcBloc {
        var heightCtrl = new MaskedTextController(mask: '000');
        var weightCtrl = new MaskedTextController(mask: '000');
        var result = "Preencha os dados para calcular seu IMC";

        calculate() {
            double weight = double.parse(weightCtrl.text);
            double height = double.parse(heightCtrl.text) / 100;
            double imc = weight / (height * height);

            if (imc < 18.6) {
                result = "Abaixo do Peso (${imc.toStringAsPrecision(2)})";
            } else if (imc >= 18.6 && imc <= 24.9) {
                result = "Peso Ideal (${imc.toStringAsPrecision(2)})";
            } else if (imc >= 24.9 && imc <= 29.9) {
                result = "Levemente acima do peso (${imc.toStringAsPrecision(2)})";
            } else if (imc >= 24.9 && imc <= 34.9) {
                result = "Obesidade Grau I (${imc.toStringAsPrecision(2)})";
            } else if (imc >= 34.9 && imc <= 39.9) {
                result = "Obesidade Grau II (${imc.toStringAsPrecision(2)})";
            } else if (imc >= 40) {
                result = "Obesidade Grau III (${imc.toStringAsPrecision(2)})";
            }
        }
    }

No BLoC, ao invés de criar apenas as variáveis do peso e altura, já estou criando os controladores, para facilitar ainda mais na interface. Lembre-se que você não está restrito a utilizar apenas recursos básicos aqui, você pode usar TextController entre outros por exemplo.  
  
O que você deve tomar cuidado é para não depender de coisas específicas do Material por exemplo, senão o código não vai funcionar no Cupertino. De resto, sinta-se livre.  
  
Neste exemplo, eu usei o MaskedTextController, caso queira utilizá-lo, precisa adicionar o pacote flutter_masked_text ao seu pubspec.yaml.

Utilizando o BLoC
-----------------

Para utilizar o BLoC tudo que precisamos é de uma instância dele, e isto pode ser feito da seguinte forma.

    ...
        class _HomePageState extends State<HomePage> {
        var bloc = new ImcBloc();

        @override
        Widget build(BuildContext context) {
        ...

Com a instância do BLoC, podemos chamar seus controles (Linha 7) em nossos TextFields.

    Padding(
        padding: EdgeInsets.all(20),
        child: TextFormField(
            decoration: InputDecoration(
            labelText: "Altura (cm)",
            ),
            controller: bloc.heightCtrl,
            keyboardType: TextInputType.number,
        ),
    ),

Ou chamar nosso método calculate() (Linha 13) em qualquer botão da página.

    Padding(
        padding: EdgeInsets.all(20),
        child: FlatButton(
            color: Theme.of(context).primaryColor,
            child: Text(
                "Calcular",
                style: TextStyle(
                    color: Colors.white,
                ),
            ),
            onPressed: () {
                setState(() {
                    bloc.calculate();
                });
            },
        ),
    ),

Como resultado, o arquivo final lib/android/pages/home.page.dart fica assim.

    import 'package:flutter/material.dart';
    import 'package:imc/blocs/imc.bloc.dart';

    class HomePage extends StatefulWidget {
        @override
        _HomePageState createState() => _HomePageState();
    }

    class _HomePageState extends State<HomePage> {
        var bloc = new ImcBloc();

        @override
        Widget build(BuildContext context) {
            return Scaffold(
                appBar: AppBar(
                    title: Text("Cálculo de IMC"),
                ),
                body: ListView(
                    children: <Widget>[
                        Padding(
                            padding: EdgeInsets.all(20),
                            child: TextFormField(
                                decoration: InputDecoration(
                                    labelText: "Altura (cm)",
                                ),
                                controller: bloc.heightCtrl,
                                keyboardType: TextInputType.number,
                            ),
                        ),
                        Padding(
                            padding: EdgeInsets.all(20),
                                child: TextFormField(
                                decoration: InputDecoration(
                                    labelText: "Peso (kg)",
                                ),
                                controller: bloc.weightCtrl,
                                keyboardType: TextInputType.number,
                            ),
                        ),
                        Padding(
                            padding: EdgeInsets.all(20),
                            child: Text(
                                bloc.result,
                                textAlign: TextAlign.center,
                            ),
                        ),
                        Padding(
                            padding: EdgeInsets.all(20),
                            child: FlatButton(
                                color: Theme.of(context).primaryColor,
                                child: Text(
                                    "Calcular",
                                    style: TextStyle(
                                    color: Colors.white,
                                    ),
                                ),
                                onPressed: () {
                                    setState(() {
                                        bloc.calculate();
                                    });
                                },
                            ),
                        ),
                    ],
                ),
            );
        }
    }

E o arquivo lib/ios/pages/home.page.dart fica assim.

    import 'package:flutter/cupertino.dart';
    import 'package:imc/blocs/imc.bloc.dart';

    class HomePage extends StatefulWidget {
        @override
        _HomePageState createState() => _HomePageState();
    }

    class _HomePageState extends State<HomePage> {
        var bloc = new ImcBloc();

        @override
        Widget build(BuildContext context) {
            return CupertinoPageScaffold(
                navigationBar: CupertinoNavigationBar(
                    middle: Text("Cálculo de IMC"),
                ),
                child: ListView(
                    children: <Widget>[
                        Padding(
                            padding: EdgeInsets.all(20),
                            child: CupertinoTextField(
                                placeholder: "Altura (cm)",
                                controller: bloc.heightCtrl,
                                keyboardType: TextInputType.number,
                            ),
                        ),
                        Padding(
                            padding: EdgeInsets.all(20),
                            child: CupertinoTextField(
                                placeholder: "Peso (kg)",
                                controller: bloc.weightCtrl,
                                keyboardType: TextInputType.number,
                            ),
                        ),
                        Padding(
                            padding: EdgeInsets.all(20),
                            child: Text(
                                bloc.result,
                                textAlign: TextAlign.center,
                            ),
                        ),
                        Padding(
                            padding: EdgeInsets.all(20),
                            child: CupertinoButton.filled(
                                child: Text(
                                    "Calcular",
                                ),
                                onPressed: () {
                                    setState(() {
                                        bloc.calculate();
                                    });
                                },
                            ),
                        ),
                    ],
                ),
            );
        }
    }

Note que as páginas ficam muito mais limpas, pois o código fica concentrado no BLoC, e além de limpas, qualquer alteração nas regras, fica centralizado, ou seja, é feito uma vez só.  
  
As páginas ficaram semelhantes em estrutura, mas repare que nunca utilizamos Cupertino no Material ou vice-versa, ou seja, elas estão completamente isoladas.

Testes de Unidade
-----------------

Outro ponto que o BLoC facilita muito são os testes de unidade. Quanto mais perto da interface, mais difícil de testar fica, e com as regras de negócio isoladas, os testes ficam bem mais simples.  
  
Mas isto é assunto para um artigo separado que farei em breve!

Código Fonte
------------

[https://github.com/balta-io/7193](https://github.com/balta-io/7193)


<div role="main" id="blog-s1-flutter-15c15f0b7777b4675fad"></div>
<script type="text/javascript" src="https://d335luupugsy2.cloudfront.net/js/rdstation-forms/stable/rdstation-forms.min.js"></script>
<script type="text/javascript"> new RDStationForms('blog-s1-flutter-15c15f0b7777b4675fad', 'UA-48664517-12').createForm();</script>
