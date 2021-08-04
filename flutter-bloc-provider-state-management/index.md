Flutter é o queridinho do momento, indiscutivelmente. Com várias grandes empresas o adotando, como o NuBank, ele ganha mais poder a cada dia.  
  
Com as demandas de mercado aumentando, a demanda por matérias aumenta, os estudos aumentam, porém, ainda há muita confusão sobre os termos utilizados nele, padrões, programação reativa, componentes e afins.  
  
Este artigo te dará uma visão geral sobre tudo que temos até o momento sobre padrões e organização no Flutter.

Widgets
-------

Vamos começar pelo básico! O Flutter trabalha com conceito de Widgets, que é muito similar ao que trabalhamos na Web, com Components.  
  
Um Widget é um pedaço da tela, um conjunto de botões, um form ou mesmo um simples “Loading”.  
  
Os **StatelessWidgets** são componentes que não mantém estado, ou seja, sempre que atualizarmos a tela, nada é armazenado.  
  
Os **StatefullWidgets** por sua vez retêm seu estado, e mesmo atualizando a tela, tudo permance intacto.  
  
Sempre que precisamos atualizar as informações na tela, utilizamos o **SetState**, ele funciona como um refresh, e o Flutter é inteligente o bastante para refazer apenas as partes da tela que foram alteradas.  
  
Só com estes recursos, você já consegue construir várias aplicações, desde que ela não tenha muita distribuição de Widgets e seja mais acoplada, mas sejamos sinceros, muitas Apps nem precisam de gestão de estado.

Comunicação entre Widgets
-------------------------

Mas você quer ir além e prover uma boa separação na sua aplicação. Você quer dividir as páginas em vários Widgets para reutilizá-los mais tarde e agora precisa que eles se comuniquem.  
  
Vamos imaginar um cenário onde temos uma aplicação (Material App), onde temos uma página de Login (LoginPage) que por sua vez tem um Widget que é o Formulário de Login (LoginFormWidget) e dentro dele queremos exibir um Spinner caso a autenticação esteja em andamento.  

![](https://baltaio.blob.core.windows.net/blog/flutter-bloc-provider-state-management-001.png)

Neste exemplo, temos uma boa separação de Widgets. Temos o formulário de Login que pode ser reutilizado em outras páginas (Um modal por exemplo), temos o Spinner que pode ser reaproveitado também.  
  
Porém, o formulário de Login precisa de dois controladores, um para o usuário e outro para senha, e o Spinner vamos supor que precisa de uma variável booleana, dizendo se ele deve ou não aparecer na tela.  
  
Neste caso, podemos enviar e receber informações pelos CONSTRUTORES das classes sem problema, basta definir o que precisamos e ao chamar a página ou Widget, informar o valor.  
  
No exemplo abaixo, deixamos como obrigatório a passagem do parâmetro TAG a página de detalhes do produto.  

    class ProductPage extends StatelessWidget {
        final String tag;
        final _service = new ProductRepository();

        ProductPage({@required this.tag});

        @override
        Widget build(BuildContext context) {

Em seguida, ao fazermos a navegação para a página de detalhes do produto, devemos informar a TAG.  

    Navigator.push(
        context,
        MaterialPageRoute(
            builder: (context) => ProductPage(
                tag: item.tag,
                ),
        ),
    );
  
Podemos também devolver valores para a página pai sempre que fechamos uma página, tornando a comunicação bidirecional.  
  
Existe ainda o **InheritedWidget**, que permite compartilhar informações com toda árvore de Widgets, mas veremos uma solução melhor aqui, então não entrarei em detalhes.  
  
Avançamos mais um passo, concorda? Agora além de atribuir estado, atualizar tela, temos ainda a comunicação entre componentes.  
  
Neste ponto, você já tem potencial para criar Apps melhores e mais organizadas, atendendo boa parte da demanda de Apps.

State Management
----------------

Mas você quer mais ainda! Você precisa se comunicar com diferentes Widgets em sua aplicação. Você precisa que diferentes Widgets alterem estado de outros Widgets.  
  
Desta forma, você não pode apenas enviar valores para os Widgets abaixo ou acima, você precisa de uma comunicação UNILATERAL, como no exemplo abaixo.  

![](https://baltaio.blob.core.windows.net/blog/flutter-bloc-provider-state-management-002.png)

Neste caso, temos um carrinho de compras, onde na página de produtos, adicionamos um item ao carrinho, na página de favoritos, também adicionamos um item ao carrinho, e toda vez que atualizamos o carrinho de compras, nossa Tab que fica no rodapé da página tem que ser atualizada.  

![](https://baltaio.blob.core.windows.net/blog/flutter-bloc-provider-state-management-003.gif)

Aqui está a real necessidade de ter uma gestão de estado única para sua aplicação, isto é o State Management. Note que não falei nada de BloC, Repository, Provider, Streams, nada ainda.  
  
O que fazemos neste caso é simples, precisamos que o nosso carrinho (Uma lista de produtos no caso) não fique atrelado a uma página ou a um Widget.  
  
Entenda que toda vez que uma página ou Widget é criado, ele passa pelo construtor e pelo método Build que sobrescrevemos, logo, se deixarmos uma lista de produtos na página de carrinho, toda vez que a página for atualizada, esta lista será gerada novamente, perdendo os valores anteriores.  
  
Além disso, outras páginas precisam adicionar itens nesta lista. Vimos como trafegar informações de pai para filho, mas imagina a bagunça que ficaria quando esta aplicação crescesse? Ficar passando informação no construtor, nem sempre é uma boa.  
  
Desta forma, criamos um estado global e ao invés de ficar enviando e recebendo informações entre nossos Widgets, lêmos e escrevemos neste estado global.  

![](https://baltaio.blob.core.windows.net/blog/flutter-bloc-provider-state-management-004.png)

Lembre-se também, que tudo que adicionamos aos construtores, geramos DEPENDÊNCIA, ou seja, se para renderizar a página de detalhes do produto, precisamos da TAG, não podemos deixar de informar este valor.  
  
Quanto mais dependências temos, mais complicado fica de resolver. Imagina que queremos poder enviar o produto por E-mail para o usuário, logo temos a dependência do usuário logado, ou seja, para toda vez que renderizarmos a página de detalhes do produto, teremos agora que passar a TAG e o usuário logado. Loucura né! Vamos comentar mais sobre isto já já.  
  
Bom, em resumo, resolvemos um problema de comunicação aqui, agora sabemos como ter uma comunicação unilateral (Isolando o estado das páginas e Widgets) porém ainda temos alguns problemas.  
  
Precisamos agora gerenciar as dependências e de uma forma eficaz de comunicar nossos Widgets toda vez que algo na nossa aplicação mudar.

Provider
--------

Se vamos ter uma lista de produtos que representa nosso carrinho de compras, e ter ela disponível em toda nossa aplicação sem gerar tanta dependência, precisaremos de ajuda.  
  
Injetar e Resolver dependências é algo comum em quase todo cenário de desenvolvimento, e temos bons Frameworks para isto, em diversas linguagens.  
  
O que precisamos aqui é o seguinte, queremos criar uma lista de produtos que será nosso carrinho sempre que a aplicação for iniciada. Depois disso, sempre quem alguém chamar esta lista de produtos (Carrinho), nós buscaremos a lista que já está na memória do App ao invés de criar uma nova.  
  
Este “padrão” chama-se Singleton, ele garante que só teremos uma única instância de um determinado objeto na memória, garantido que ele seja utilizado em toda aplicação.  
  
Este conceito é muito utilizado no Backend também, nos cenários de bancos de dados, afinal não queremos várias conexões sendo abertas por requisição.  
  
No Flutter temos um cara bacana para isto, que é o [Provider](https://pub.dev/packages/provider), onde adicionamos o que queremos distribuir na raíz da nossa aplicação.  
  
    class MyApp extends StatelessWidget {
        @override
        Widget build(BuildContext context) {
            return MultiProvider(
            providers: [
                ChangeNotifierProvider<HomeBloc>.value(
                value: HomeBloc(),
                ),
                ChangeNotifierProvider<CartBloc>.value(
                value: CartBloc(),
                ),
                ChangeNotifierProvider<ThemeBloc>.value(
                value: ThemeBloc(),
                ),
            ],
            child: Main(),
            );
        }
    }
  
E depois simplesmente chamamos Provider.of para receber o valor da memória.  
  
    class CartPage extends StatelessWidget {
        @override
        Widget build(BuildContext context) {
            bloc = Provider.of<CartBloc>(context);
            items = bloc.cart;
  
Desta forma, garantimos que sempre que teremos uma única lista de produtos (Carrinho) para todo nosso App.

Notificar os Widgets
--------------------

O último problema da nossa lista é notificar os Widgets. Quando adicionarmos um produto no carrinho na página de produtos, como nossa Tab vai saber que tem um produto novo e atualizar seu contador?  
  
Toda vez que utilizamos o Provider, ficamos observando mudanças e existe um método chamado **NotifyListeners** que dispara o **SetState** atualizando todos os Widgets que fazem uso do contexto em memória que criamos.  

    calculateTotal() {
        total = 0;
        cart.forEach((x) {
            total += (x.price * x.quantity);
        });
        notifyListeners();
    }
  
Neste exemplo, deixamos o **notifyListeners** dentro do calculateTotal, afinal, sempre que adicionamos ou removemos um item, chamamos este método, e devemos tomar cuidado para não chamar excessivamente o notifyListeners.  
  
Mais um passo dado, e agora sim, você tem tudo que precisa pra criar seus Apps! Tem comunicação, tem estado global, notificação entre os Widgets, ou seja, você está pronto pra vida!

BLoC
----

Mas você é insistente, você quer ainda mais e você está triste porque o balta ainda não falou sobre BLoC neste artigo, então vamos lá.  
  
Vamos supor que nossa aplicação evoluiu e o usuário não quer nada de Material no iOS nem de Cupertino no Android, ou seja, teremos duas interfaces.  
  
Porém, não queremos (E não precisamos) criar duas Apps separadas, afinal, a lógica é a mesma para ambas, o que muda são apenas elementos visuais.  
  
Neste caso, separamos a lógica/regras de negócio das nossas páginas ou Widgets, isolando-as em classes e reutilizando-as onde precisar.  

![](https://baltaio.blob.core.windows.net/blog/flutter-bloc-provider-state-management-005.png)

Note que agora quem gerencia o estado é o Product Details BLoC e as páginas (Material/Cupertino) consomem ele apenas.  
  
Aqui vai um exemplo do BLoC de carrinho de compras do nosso curso de Flutter.  

    import 'package:eshop/models/cart-item.model.dart';
    import 'package:flutter/widgets.dart';

    class CartBloc extends ChangeNotifier {
        var cart = new List<CartItemModel>();
        double total = 0;

        get() {
            return cart;
        }

        add(CartItemModel item) {
            cart.add(item);
            calculateTotal();
        }

        remove(CartItemModel item) {
            cart.removeWhere((x) => x.id == item.id);
            calculateTotal();
        }

        itemInCart(CartItemModel item) {
            var result = false;
            cart.forEach((x) {
            if (item.id == x.id) result = true;
            });
            return result;
        }

        increase(CartItemModel item) {
            if (item.quantity < 10) {
            item.quantity++;
            calculateTotal();
            }
        }

        decrease(CartItemModel item) {
            if (item.quantity > 1) {
            item.quantity--;
            calculateTotal();
            }
        }

        calculateTotal() {
            total = 0;
            cart.forEach((x) {
            total += (x.price * x.quantity);
            });
            notifyListeners();
        }
    }
  
E aqui é a forma como consumimos ele, sem gerar dependências.  

    class TabsPage extends StatelessWidget {
        @override
        Widget build(BuildContext context) {
            final bloc = Provider.of<CartBloc>(context);

### Posso utilizar BLoC com Provider?

Na verdade, é uma boa ideia. Lembra do que falamos sobre gerar dependências? Se o seu Widget precisa utilizar um determinado BLoC, pode ser uma boa ideia tê-lo na memória, como mostramos no código anterior.  
  
Só tome cuidado pois nem tudo precisa estar disponível para toda a aplicação.

### BLoC não pode referenciar Material/Cupertino/Widget

De fato, é verdade! Não é interessante que seu BLoC diga quando sua tela tem que ser atualizada, mas também não é um pecado mortal fazer isto.  
  
Note que estamos utilizando Dart com Flutter, provavelmente nunca vamos compartilhar esta lógica com outra aplicação. Agora, caso esteja utilizando Dart também no Backend e esta seja uma lógica compartilhada, chamar o notifyListeners dentro dele é algo ruim.  
  
Mas em um cenário compartilhando informações entre Front e Backend, eu iria para domínios ricos, conforme mostro nos cursos de ASP.NET com C# e Node com TypeScript.  
  
Enfim, não se prenda a isto agora!

### Preciso de um BLoC para cada página?

Não necessariamente! Você pode ter um BLoC (Carrinho por exemplo) para várias páginas. O BLoC serve para externalizar as regras de negócio, se você não tem regras ou alguma lógica em uma página, não precisa de um BLoC para ela.  
  
Vejamos o exemplo do Shopping Cart ainda, neste cenário, temos a página de detalhes do produto, que não tem lógica alguma. Logo ela não precisa de um BLoC.

### Preciso trabalhar com Streams?

Não! Embora os Streams sejam muito legais (Vou falar disso em um próximo artigo), você não é obrigado a trabalhar com Streams nos BLoCs, vide o código apresentado anteriormente onde temos apenas listas.  
  
Em um cenário futuro, onde quiser eliminar os notifyListeners, aí os Streams brilham, mas isto é assunto para outro artigo.

### Preciso de Repository Pattern?

Não também! Os repositórios provem acesso único e centralizado aos dados (Sua API no caso). Você pode chamar os repositórios diretamente nas páginas sem problemas, ou nos BLoCs, fica a seu critério.

Resumo
------

Ufa, quanta coisa para um artigo, mas espero que tenha esclarecido vários pontos e discussões que vejo por aí.  
  
Embora as coisas estejam interligadas, uma não depende ou anula a outra. Enquanto o Provider fornece DI, os BLoCs isolam as regras de negócio e o estado da aplicação fica distribuído, permitindo a comunicação entre os componentes.


<div role="main" id="blog-s1-flutter-15c15f0b7777b4675fad"></div>
<script type="text/javascript" src="https://d335luupugsy2.cloudfront.net/js/rdstation-forms/stable/rdstation-forms.min.js"></script>
<script type="text/javascript"> new RDStationForms('blog-s1-flutter-15c15f0b7777b4675fad', 'UA-48664517-12').createForm();</script>
