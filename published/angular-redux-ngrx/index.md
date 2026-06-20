Com certeza você já ouviu falar de React e Redux, programação reativa e gerenciamento de estado em aplicações Web. Porém, este é um assunto pouco explorado no Angular, que também fornece tudo que precisamos para implementar estes conceitos. Vem comigo que vou te mostrar como!

O que é State Management?
-------------------------

Antes de saber o que é Redux, como implementá-lo, é importante saber o que é State Management ou gestão de estado, e porque precisamos disso.

### Components

Se você já criou alguma aplicação com Angular sabe que trabalhamos muito com Components (Componentes) nele, e basicamente qualquer elemento na tela é um componente.  
  
Se você ainda não tem experiência com Angular, recomendo assistir meu curso GRATUITO “Começando com Angular”.  
  
Desta forma, tudo que queremos exibir na tela acabamos transformando em um componente, acaba sendo até uma boa prática, para reuso de código. Botões, formulários, tabelas, enfim, quase tudo vira componente.  
  
No Angular somos obrigados a ter pelo menos um componente, chamado de Root Component, e seguindo então nossa árvore de componentes normalmente.  
  
![Angular - Redux e NgRx](https://baltaio.blob.core.windows.net/blog/angular-redux-ngrx-001.PNG)  
  
Porém, em determinados momentos precisamos enviar informações para estes componentes e/ou receber informações dele.

### Comunicação entre Componentes

A forma mais fácil que temos de nos comunicar entre componentes no Angular é o @Input() e @Output(), que provem a entrada e saída de dados respectivamente.  
  
Sendo assim, temos a comunicação bidirecional entre dois componentes, como mostrado abaixo.  
  
![Angular - Redux e NgRx](https://baltaio.blob.core.windows.net/blog/angular-redux-ngrx-002.PNG)  
  
Esta comunicação pode ser de cima para baixo, de baixo para cima, da esquerda para direita, da direita para esquerda, mas nunca entre mais de um componente.  
  
Caso necessitemos passar informação para um terceiro componente, ela terá que ser feita manualmente, passado do A para o B e do B para o C.  
  
Para cenários menores, esta comunicação está mais que boa, mas em alguns cenários, queremos algo a mais.

### Notificação entre vários componentes

O RxJs nos fornece um cara chamado Observables, que nada mais são que variáveis observáveis nos quais componentes podem se inscrever para receber notificações.  
  
Aqui as coisas já ficam mais interessantes, pois imagine que temos um carrinho de compras, onde exibimos o total de itens no carrinho em diversas partes do App, como no menu superior, menu lateral, rodapé, entre outros.  
  
Se cada vez que o carrinho for atualizado, precisarmos nos lembrar de enviar informações de volta para os componentes, ficaria difícil, fora que existem componentes isolados, que também precisam ser atualizados.  
  
![Angular - Redux e NgRx](https://baltaio.blob.core.windows.net/blog/angular-redux-ngrx-003.PNG)  
  
Neste cenário acima, precisamos atualizar um componente que está fora do nó em que nosso componente está.  
  
Este cenário é o ideal para utilizarmos os Observables, onde podemos ter um carrinho de comprar “observável” e toda vez que um novo item for adicionado, ele notificará todos os outros componentes inscritos a ele para receber estas notificações.

### Observables VS @Input()/@Output()

Só para deixar claro que não deixamos de utilizar o @Input() e @Output() para passagem de dados para os componentes, eles ainda existem em diversos cenários.  
  
Por exemplo, quando temos um componente que recebe uma lista de produtos e a exibe na tela, este componente pode muito bem receber a lista via @Input(), não precisa usar Observables para tudo.  
  
Já em cenários onde precisamos notificar vários componentes ao mesmo tempo, utilizar os Observables vem a ser o melhor dos casos.

### State Management

Ao observar e notificar componentes, podemos nos deparar com uma outra necessidade, a gestão de estado.  
  
Enquanto nos comunicamos entre componentes apenas utilizando o @Input(), passamos os dados para este componente que os manipula e nos devolve, é algo simples e fácil de gerenciar.  
  
Muitas vezes inclusive um estado se inicia em um componente e morre em outro, sem a necessidade de persisti-lo.  
  
Porém, vamos tomar novamente como base o carrinho de compras, que terá componentes distintos em nós distintos da nossa árvore de componentes alterando seu estado.  
  
Imagine que temos a adição de produtos no carrinho na página inicial, na página de detalhes do produto, também podemos remover produtos na página de carrinho de compras ou simplesmente limpar o carrinho de compras diretamente no menu lateral.  
  
Complicou né? São muitos componentes alterando um estado (Carrinho de Compras) e se não tivermos um estado centralizado (Global) para ele, não temos como alterar suas informações de componentes em nós diferentes e também seria uma bagunça gerenciar tudo isto.  
  
![Angular - Redux e NgRx](https://baltaio.blob.core.windows.net/blog/angular-redux-ngrx-004.PNG)  
  
Desta forma, ao centralizarmos o nosso estado, os componentes passam a interagir com ele ao invés de enviar e receber informações entre si.  
  
Os componentes ainda podem trocar informações, mas não os referentes aos estados centralizados, estas devem ser mantidas como mostrado acima para evitar bagunça.

### Regras de Negócio

Então tudo certo? Não, ainda não! Ainda pegando um gancho no carrinho de compras, toda vez que precisamos do total do pedido, temos um cálculo, que é o total dos itens, menos o desconto, mais a taxa de entrega.  
  
Este cálculo é uma regra de negócio, e seguindo os princípios de um bom código, temos o DRY (Don\`t repeat yourself) ou a não repetição de um código, principalmente de regras de negócio.  
  
Repetir este cálculo em diversos componentes traria um retrabalho toda vez que esta regra mudasse, fora que para lembrar onde estamos usando isto depois é um caos! Então precisamos também centralizar as regras de negócio.  
  
![Angular - Redux e NgRx](https://baltaio.blob.core.windows.net/blog/angular-redux-ngrx-005.PNG)  
  
Sempre que falamos em State Management e centralização de estado, dá a entender que devemos centralizar apenas as informações (Lista de itens no carrinho neste caso), mas não. É uma boa centralizarmos os comportamentos também (Adicionar, Remover, Limpar o Carrinho).

O que é Redux?
--------------

Com o conceito do que precisamos em mente, partimos para implementação do código, e como todo bom código precisamos de uma boa organização e divisão.  
  
O Redux é um contêiner de estados para Apps JavaScript, mas seu conceito se espalhou rapidamente e temos versões em Dart e até C#.  
  
Além do estado, ele provê uma espécie de padrão que também se popularizou bastante e que auxilia a organização do código provendo a seguinte divisão.  
  
![Angular - Redux e NgRx](https://baltaio.blob.core.windows.net/blog/angular-redux-ngrx-006.PNG)  
  
O fluxo acontece da seguinte forma, a UI (Um componente) dispara uma ação (Adicionar um produto ao carrinho por exemplo).  
  
Esta ação é manipulada pelos Reducers, que contém as regras de negócio como cálculo do total e afins, centralizando o código em um só lugar.  
  
Os Reducers por sua vez alteram o armazenamento das informações, chamados de Stores, que por sua vez refletem no estado (State).  
  
Com o State alterado, os componentes da tela são atualizados, e assim se repete o ciclo e desta forma temos as seguintes responsabilidades.

### Actions

São as possíveis ações que temos para um item, no caso do carrinho de compras, ADD, REMOVE, CLEAR, para adicionar produtos, remover produtos e limpar o carrinho respectivamente.

### Reducers

São responsáveis receber uma ação e manipular ela, como por exemplo receber uma ação para adicionar um produto ao carrinho.  
  
Neste caso, a ação vem com um “payload” (Uma carga, um pacote) junto, que são os dados para a ação ser completa, ou seja, o produto a ser adicionado ao carrinho.  
  
O Reducer por sua vez, adiciona o produto ao Store e executa o cálculo para saber o total atual do pedido, armazenando-o também no Store.

### Store

Neste momento, temos um armazenamento (Em memória) dos itens do carrinho e do total do carrinho, e isto se reflete no estado da aplicação.  
Podemos ter mais de um Store, um para os pedidos e outro para o total por exemplo, assim como podemos ter um único Store para toda aplicação.

### State

Mudado os dados do Store, nossos observadores são acionados disparando notificações para os componentes que estão inscritos para serem atualizados, e assim finaliza o ciclo.

O que é e como usar o NgRx?
---------------------------

Lembra que comentei que o Redux existe para várias plataformas? Pois é, temos uma implementação dele com Angular, chamada de NgRx!

### Instalação

Para executar este exemplo, vamos precisar do NgRx e seu Store, seguindo as instalações abaixo.  
  
    npm install @ngrx/store –save 
    ng add @ngrx/store
  
Desta forma está tudo pronto para começarmos a codificar.

### Organização da Solução

Quem faz meus cursos sabe o quanto sou chato para organizar meus Apps, isto por que assim que ele cresce, fica mais difícil gerenciar e sem uma boa organização isto é quase impossível.  
  
Desta forma, na pasta src/app eu crio a seguinte estrutura.  

*   Models
*   Actions
*   Components
*   Reducers

### Models

As pastas já são autoexplicativas, representando cada uma sua função e vamos começar como sempre, pelos modelos, e como quanto mais tipamos mais o TypeScript nos ajuda, vamos começar pelo produto.

    export class ProductModel {
        public _id: string;
        public title: string;
        public category: string;
        public description: string;
        public price: number;
        public images: string[];
    }

Seguindo depois do nosso Carrinho de Compras.

    import { ProductModel } from './product.model';

    export class CartModel {
        public products: ProductModel[] = [];
        public total: number = 0;
    }

Lembra que comentei que toda ação viria com um Payload junto? Eu gosto de tipar isto também, deixando claro e objetivo que toda ação deste contexto DEVE seguir este padrão.

    import { Action } from '@ngrx/store';

    export class ActionModel implements Action {
        type: string;
        payload: any;
    }

### Actions

Com nossos modelos criados, vamos partir para as ações, e na implementação temos apenas as assinaturas delas.

    import { Action } from '@ngrx/store';

    export enum ActionTypes {
        Add = 'ADD',
        Remove = 'REM',
        Clear = 'CLE',
    }

    export const Add = (product: any) => {
        return <Action>{ type: ActionTypes.Add, payload: product };
    }

    export const Remove = (product: any) => {
        return <Action>{ type: ActionTypes.Remove, payload: product };
    }

    export const Clear = () => {
        return <Action>{ type: ActionTypes.Clear, payload: null };
    }

Na implementação também vemos que podemos ou não enviar um Payload, no caso do Clear, que limpa o carrinho, não há esta necessidade.

### Reducers

Agora é hora de implementar os métodos e eles são feitos nos Reducers, como abaixo.

    import { ActionTypes } from '../actions/cart.action';
    import { ActionModel } from '../models/action.model';
    import { CartModel } from '../models/cart.model';
    import { ProductModel } from '../models/product.model';

    export const cart = new CartModel();

    export function cartReducer(state = cart, action: ActionModel) {
        switch (action.type) {
            case ActionTypes.Add:
                {
                    state.products.push(action.payload);
                    state.total = calculateTotal(state.products);

                    console.log(state);
                    return state;
                };

            case ActionTypes.Remove:
                {
                    const index = state.products.indexOf(action.payload);
                    state.products.splice(index, 1);
                    state.total = calculateTotal(state.products);

                    console.log(state);
                    return state;
                };

            case ActionTypes.Clear:
                {
                    state = new CartModel();
                    state.total = calculateTotal(state.products);

                    console.log(state);
                    return state;
                }

            default:
                return state;
        }


    }

    function calculateTotal(products: ProductModel[]): number {
        let total: number = 0;
        products.forEach(product => {
            total += product.price;
        });
        return total;
    }

Note que nossa função recebe sempre uma ação como modelamos anteriormente e sempre retornamos o estado todo.

### Configuração

Agora precisamos configurar o NgRx para nossa aplicação e isto é feito no nosso módulo principal, mais especificamente na linha 29 como mostrado abaixo.

    import { NgModule } from '@angular/core';
    import { BrowserModule } from '@angular/platform-browser';
    import { RouteReuseStrategy } from '@angular/router';
    import { IonicModule, IonicRouteStrategy } from '@ionic/angular';
    import { HttpClientModule } from '@angular/common/http';
    import { AppComponent } from './app.component';
    import { AppRoutingModule } from './app-routing.module';
    import { ProductListComponent } from './components/product-list/product-list.component';
    import { HomePage } from './pages/home/home.page';
    import { StoreModule } from '@ngrx/store';
    import { cartReducer } from './reducers/cart.reducer';
    import { NavbarComponent } from './components/navbar/navbar.component';
    import { ShoppingCartComponent } from './components/shopping-cart/shopping-cart.component';

    @NgModule({
    declarations: [
        AppComponent,
        HomePage,
        ProductListComponent,
        NavbarComponent,
        ShoppingCartComponent,
    ],
    entryComponents: [],
    imports: [
        BrowserModule,
        IonicModule.forRoot(),
        AppRoutingModule,
        HttpClientModule,
        StoreModule.forRoot({
        cart: cartReducer
        })
    ],
    providers: [
        {
        provide: RouteReuseStrategy,
        useClass: IonicRouteStrategy
        }
    ],
    bootstrap: [AppComponent]
    })
    export class AppModule { }

Na configuração podemos ver a chamada ao Reducer que criamos, e poderíamos ter vários ali.

### Lendo informações

Com tudo pronto e configurado, podemos nos inscrever para receber atualizações sempre que nosso Store é modificado.

    import { Component, OnInit } from '@angular/core';
    import { Observable } from 'rxjs';
    import { Store, select } from '@ngrx/store';
    import { CartModel } from 'src/app/models/cart.model';

    @Component({
    selector: 'app-navbar',
    templateUrl: './navbar.component.html',
    styleUrls: ['./navbar.component.scss'],
    })
    export class NavbarComponent implements OnInit {
    cart$: Observable<CartModel>;

    constructor(private store: Store<CartModel>) {
        this.cart$ = store.pipe(select('cart'));
    }

    ngOnInit() {
    }

    }

### Chamando Ações

Podemos também chamar nossas ações (Dispatch) em nossos componentes.

    import { Component, OnInit } from '@angular/core';
    import { Observable } from 'rxjs';
    import { Store, select } from '@ngrx/store';
    import { Add } from 'src/app/actions/cart.action';
    import { DataService } from 'src/app/data.service';
    import { CartModel } from 'src/app/models/cart.model';
    import { ToastController } from '@ionic/angular';

    @Component({
    selector: 'app-product-list',
    templateUrl: './product-list.component.html',
    styleUrls: ['./product-list.component.scss'],
    })
    export class ProductListComponent implements OnInit {
    public products: any[] = null;

    constructor(
        private store: Store<CartModel>,
        private toastCtrl: ToastController,
        private service: DataService,
    ) {

    }

    ngOnInit() {
        this.service
        .getProducts()
        .subscribe((data) => {
            this.products = data;
        });
    }

    async add(product) {
        this.store.dispatch(Add(product));
        const toast = await this.toastCtrl.create({
        message: `${product.title} adicionado ao carrinho`,
        duration: 2000,
        showCloseButton: true,
        closeButtonText: "OK"
        });
        toast.present();
    }

    }

Como resultado, temos todo aquele ciclo demonstrado na imagem, de uma forma organizada e atualizando todos os componentes da tela.

Curso Criando Apps Web com Angular e Redux
------------------------------------------

E aí, gostou deste artigo? Viu como podemos utilizar Redux com Angular e que isto não é um bicho de sete cabeças?

Se quiser ver passo-a-passo esta implementação, confere meu curso [7189 – Criando Apps Web com Angular e Redux](https://balta.io/cursos/criando-apps-web-com-angular-8-e-redux) que vou te mostrar muito mais conteúdo sobre o assunto.

Código Fonte Completo
---------------------

O código fonte completo do curso 7189 você pode encontrar no GitHub do balta.io.

[https://github.com/balta-io/7189](https://github.com/balta-io/7189)

Fontes
------

*   [https://redux.js.org/](https://redux.js.org/)
*   [https://ngrx.io/](https://ngrx.io/)

<div role="main" id="blog-s1-angular-9e433e330effce9fa8de"></div>
<script type="text/javascript" src="https://d335luupugsy2.cloudfront.net/js/rdstation-forms/stable/rdstation-forms.min.js"></script>
<script type="text/javascript"> new RDStationForms('blog-s1-angular-9e433e330effce9fa8de', 'UA-48664517-12').createForm();</script>
