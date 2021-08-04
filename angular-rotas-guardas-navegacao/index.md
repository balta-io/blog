O Angular nos fornece um esquema de rotas e navegação completo, simples e fácil de utilizar, incluindo um esquema de guardas de rotas para implementar segurança em nossos Apps.  
  
Se você não sabe como eles funcionam, ou quer aprender mais sobre o assunto, vem comigo que vou te mostrar neste artigo.

Criando o módulo de rotas
-------------------------

No Angular, muita coisa é modularizada, e as rotas não são diferentes. Ao criar uma aplicação Angular, somos indagados sobre o uso das mesmas (Would you like to add Angular Routing?), e ao responder “Yes”, já temos tudo que precisamos para o roteamento.  
  
Na verdade, tudo que precisamos para ter rotas em nossa aplicação é um arquivo de módulo das rotas e importá-lo posteriormente no Root Module.  
  
Este arquivo de rotas, normalmente chamado app-routing.module.ts, tem a seguinte estrutura.

    import { NgModule } from '@angular/core';
    import { Routes, RouterModule } from '@angular/router';

    const routes: Routes = [];

    @NgModule({
        imports: [RouterModule.forRoot(routes)],
        exports: [RouterModule]
    })
    export class AppRoutingModule { }

O que importa para nós neste momento é a constante “routes”, que será o local onde colocaremos nossa estrutura de rotas.  
  
Em adicional, vamos criar algumas páginas (Components) para poder exemplificar melhor, rodando os comandos abaixo.  
  
    ng generate componente pages/login  
    ng generate componente pages/signup  
    ng generate componente pages/master  
    ng generate componente pages/home  
    ng generate componente pages/reports

Compondo as rotas
-----------------

As rotas são definidas por dois elementos principais, o Path, que é o caminho (URL) e o Componente, que é o componente a ser carregado. Ainda existem outras formas de carregar páginas mais eficientes, como carregamento assíncrono, via módulos, mas este é assunto para um próximo artigo.  
  
Este é o resultado da forma mais simples de rotas que podemos ter no Angular.

    import { NgModule } from '@angular/core';
    import { Routes, RouterModule } from '@angular/router';
    import { LoginComponent } from './pages/login/login.component';
    import { SignupComponent } from './pages/signup/signup.component';
    import { MasterComponent } from './pages/master/master.component';
    import { HomeComponent } from './pages/home/home.component';
    import { ReportsComponent } from './pages/reports/reports.component';

    const routes: Routes = [
        { path: 'login', component: LoginComponent },
        { path: 'signup', component: SignupComponent },
        { path: 'master', component: MasterComponent },
        { path: 'home', component: HomeComponent },
        { path: 'reports', component: ReportsComponent },
    ];

    @NgModule({
        imports: [RouterModule.forRoot(routes)],
        exports: [RouterModule]
    })
    export class AppRoutingModule { }

Ao abrir o browser, podemos navegar para http://localhost:4200/home por exemplo, porém, continuaremos a ver a página padrão do Angular na tela.

Exibindo as páginas
-------------------

Quando configuramos uma rota e navegamos até ela, o Angular recupera a URL, checa no arquivo de rotas e tenta carregar o componente na tela.  
  
Neste caso, o que acontece é que o Angular recuperou a URL http://localhost:4200/home, encontrou uma rota com este nome, localizou o componente que devia renderizar na tela, mas não conseguiu encontrar ONDE, na tela, ele deveria renderizar este componente.  
  
Para que isto aconteça, precisamos de uma tag especial, chamada de Router Outlet, pois sempre que um fluxo como o descrito acima é chamado, o Angular busca esta tag para poder renderizar o componente desejado.  
  
Vamos então para página app.component.html, remover todo conteúdo dela e deixar apenas as tags .  
  
Desta forma, ao navegar para URL http://localhost:4200/home, conseguimos ver o texto “home Works!”, padrão dos componentes do Angular.  
  
É importante notar que qualquer HTML contido na página app.component.html será exibido em todas as páginas do nosso App, então, particularmente prefiro deixar apenas o Router Outlet nele.

Navegando via HTML
------------------

Um erro comum na navegação do Angular é utilizar o href padrão do HTML para link de páginas. O uso desta propriedade, fará com que nossa aplicação seja recarregada a cada navegação.  
  
Ao invés disso, utilizamos o routerLink do Angular, que fará com que apenas os componentes necessários sejam renderizados na tela.  
  
Desta forma, vamos incluir o seguinte código à todas as páginas HTML (home, master, login, signup, reports) que temos.

    <hr>
    <a routerLink="/login">Login</a><br>
    <a routerLink="/signup">Signup</a><br>
    <a routerLink="/master">Master</a><br>
    <a routerLink="/home">Home</a><br>
    <a routerLink="/reports">Reports</a><br>

Neste momento, já podemos executar a aplicação, utilizando “ng serve” e navegar entre as páginas que criamos posteriormente.

Selecionando o Link atual
-------------------------

Existe também, um elemento chamado Router Link Active, que nos permite deixar um link com algum CSS diferente quando sua rota está selecionada.  
  
Para este exemplo, criei um estilo chamado “selected” no arquivo styles.css, conforme mostrado abaixo.

    .selected {
        font-weight: bold;
    }

Desta forma, podemos adicionar a propriedade routerLinkActive no nosso link e assim que uma rota for chamada o link ficará em negrito. Vamos então substituir nosso código anterior, por este abaixo.

    <hr>
    <a routerLink="/login" routerLinkActive="selected">Login</a><br>
    <a routerLink="/signup" routerLinkActive="selected">Signup</a><br>
    <a routerLink="/master" routerLinkActive="selected">Master</a><br>
    <a routerLink="/home" routerLinkActive="selected">Home</a><br>
    <a routerLink="/reports" routerLinkActive="selected">Reports</a><br>

Navegando via TypeScript
------------------------

Da mesma forma que podemos navegar utilizando elementos do HTML, podemos também via TypeScript.  
  
Para isto, precisamos utilizar o Router, injetado no componente e posteriormente utilizar seu método Navigate, conforme mostrado abaixo.

    import { Component, OnInit } from '@angular/core';
    import { Router } from '@angular/router';

    @Component({
        selector: 'app-home',
        templateUrl: './home.component.html',
        styleUrls: ['./home.component.css']
    })
    export class HomeComponent implements OnInit {

        constructor(
            private router: Router
        ) { }

        ngOnInit() {

        }

        goToLogin() {
            this.router.navigate(['/login']);
        }

    }

Parâmetros nas Rotas
--------------------

Para passar parâmetros em rotas, precisamos primeiro definir o nome dele nas constantes do módulo de rotas.

    { path: 'home/:parametro', component: HomeComponent },

Feito isto, podemos navegar novamente para página Home através da URL https://localhost:4200/home/MeuParametro e recuperar seu valor pelo Activated Route.

    import { Component, OnInit } from '@angular/core';
    import { ActivatedRoute } from '@angular/router';

    @Component({
        selector: 'app-home',
        templateUrl: './home.component.html',
        styleUrls: ['./home.component.css']
    })
    export class HomeComponent implements OnInit {

        constructor(
            private activatedRoute: ActivatedRoute
        ) { }

        ngOnInit() {
            const par = this.activatedRoute.snapshot.paramMap.get('parametro');
            console.log(par);
        }

    }

O Activated Route nos dá acesso a rota ativa no momento, seus parâmetros e demais valores.

Rotas aninhadas
---------------

Normalmente, as páginas de Login, Signup, Reset Password não possuem menu lateral ou cabeçalho, porém a Home, Reports, dentre outras, possuem.  
  
O conceito de Master Pages pode ser aplicado aqui também, onde temos uma página mestre e carregamos outro conteúdo dentro dela.  
  
Para ter este resultado no Angular, basta movermos uma rota dentro da outra, utilizando a propriedade Children.

    import { NgModule } from '@angular/core';
    import { Routes, RouterModule } from '@angular/router';
    import { LoginComponent } from './pages/login/login.component';
    import { SignupComponent } from './pages/signup/signup.component';
    import { MasterComponent } from './pages/master/master.component';
    import { HomeComponent } from './pages/home/home.component';
    import { ReportsComponent } from './pages/reports/reports.component';

    const routes: Routes = [
        { path: 'login', component: LoginComponent },
        { path: 'signup', component: SignupComponent },
        {
            path: '',
            component: MasterComponent,
            children: [
            { path: '', component: HomeComponent },
            { path: 'reports', component: ReportsComponent },
            ],
        },
    ];

    @NgModule({
        imports: [RouterModule.forRoot(routes)],
        exports: [RouterModule]
    })
    export class AppRoutingModule { }

Note que agora, Home e Reports ficam dentro de Master na rota, e o ciclo para renderização dos componentes também segue este modelo.  
  
Eu removi o Path do componente Master e Home, já que não fazia mais sentido tê-lo, senão as URL ficariam “master/home”, “master/reports” e nunca teríamos um “/” apenas.  
  
Seguindo o ciclo, ao renderizar as telas agora o Angular tentará carregar DOIS COMPONENTES, o Master e o Home por exemplo, porém, só temos UM Router Outlet na tela, causando confusão.  
  
Desta forma, no Master, temos também que adicionar um Router Outlet.

    <p>master works!</p>
    <hr>
    <a routerLink="/login" routerLinkActive="selected">Login</a><br>
    <a routerLink="/signup" routerLinkActive="selected">Signup</a><br>
    <a routerLink="/master" routerLinkActive="selected">Master</a><br>
    <a routerLink="/home" routerLinkActive="selected">Home</a><br>
    <a routerLink="/reports" routerLinkActive="selected">Reports</a><br>
    <hr>
    <h3>Menu/Cabeçalho</h3>
    <router-outlet></router-outlet>

Agora ao navegar para Home ou Reports, teremos além dos links carregados em todas as páginas, uma mensagem “Menu/Cabeçalho”.

Guardas
-------

Para fechar nosso assunto de rotas, temos a possibilidade de criar guardas, que permitem ou não o acesso a determinadas rotas.  
  
Estas guardas são cumulativas, ou seja, você pode ter vários arquivos de guardas, e todos eles seguem um mesmo padrão, implementam CanActivate e tem um método apenas, chamado também CanActivate, que retorna verdadeiro ou falso, informando se o usuário pode ou não chegar a esta página.  
  
Neste exemplo, criei uma rota que retorna sempre falso, ou seja, não permite a navegação para uma página.

    import { CanActivate } from '@angular/router';
    import { Injectable } from '@angular/core';

    @Injectable()
    export class SampleGuard implements CanActivate {
        canActivate() {
            return false;
        }
    }

Feito isto, vamos adicionar a guarda aos Providers no AppModule.

    import { BrowserModule } from '@angular/platform-browser';
    import { NgModule } from '@angular/core';

    import { AppRoutingModule } from './app-routing.module';
    import { AppComponent } from './app.component';
    import { LoginComponent } from './pages/login/login.component';
    import { SignupComponent } from './pages/signup/signup.component';
    import { MasterComponent } from './pages/master/master.component';
    import { HomeComponent } from './pages/home/home.component';
    import { ReportsComponent } from './pages/reports/reports.component';
    import { SampleGuard } from './guards/sample.guard';

    @NgModule({
        declarations: [
            AppComponent,
            LoginComponent,
            SignupComponent,
            MasterComponent,
            HomeComponent,
            ReportsComponent
        ],
        imports: [
            BrowserModule,
            AppRoutingModule
        ],
        providers: [
            SampleGuard
        ],
        bootstrap: [AppComponent]
    })
    export class AppModule { }

Para finalizar, podemos utilizar o CanActivate das rotas para aplicar a guarda a uma rota pai ou alguma rota filha.

    import { NgModule } from '@angular/core';
    import { Routes, RouterModule } from '@angular/router';
    import { LoginComponent } from './pages/login/login.component';
    import { SignupComponent } from './pages/signup/signup.component';
    import { MasterComponent } from './pages/master/master.component';
    import { HomeComponent } from './pages/home/home.component';
    import { ReportsComponent } from './pages/reports/reports.component';
    import { SampleGuard } from './guards/sample.guard';

    const routes: Routes = [
        { path: 'login', component: LoginComponent },
        { path: 'signup', component: SignupComponent },
        {
            path: 'master',
            component: MasterComponent,
            canActivate: [SampleGuard],
            children: [
            { path: 'home', component: HomeComponent },
            { path: 'reports', component: ReportsComponent },
            ],
        },
    ];

    @NgModule({
        imports: [RouterModule.forRoot(routes)],
        exports: [RouterModule]
    })
    export class AppRoutingModule { }

Agora ao tentar navegar para as rotas “/” ou “reports” seremos bloqueados.

Formação Completa de Angular
============================

E aí, gostou deste artigo? Quer aprender a criar um App completo com autenticação, restrição em rotas, perfis de acesso? Então você não pode deixar de fazer minha formação em Angular!  
São mais de 8 horas de curso, com mais de 100 aulas, 100% prático, com didática diferenciada, e o melhor, você COMEÇA DE GRAÇA!  
  
[COMEÇAR AGORA](https://balta.io/comece-de-graca)

<div role="main" id="blog-s1-angular-9e433e330effce9fa8de"></div>
<script type="text/javascript" src="https://d335luupugsy2.cloudfront.net/js/rdstation-forms/stable/rdstation-forms.min.js"></script>
<script type="text/javascript"> new RDStationForms('blog-s1-angular-9e433e330effce9fa8de', 'UA-48664517-12').createForm();</script>
