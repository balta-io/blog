Com o crescimento de aplicações e APIs distribuídas, concentrar o processo de autenticação em um serviço pode ser uma ótima ideia para seu produto.


## Índice
* **Identity Server**
* [Criando seu primeiro Identity Server](https://balta.io/artigos/criando-seu-primeiro-identity-server)

## Identity Server

O Identity Server ou servidor de identidade é um Framework que implementa os protocolos OpenID Connect e oAuth 2.0 para o ASP.NET Core fornecendo funcionalidades como.

### Autenticação como serviço
Centraliza a autenticação de múltiplas aplicações e serviços, como por exemplo suas Apps ASP.NET, Blazor, Angular, React, Flutter e por aí vai.

### Single Sign On
O IS (Identity Server) fornece um serviço chamado SSO (Single Sign On) ou login único para todas as suas aplicações.

### SPA
Como o IS trabalha facilmente com **Access Tokens** (Tokens de acesso), ele cobre facilmente as necessidades das SPAs (Single Page Applications -- Angular, React, Vue, Blazor).

### Apps Mobile
Assim como nas SPAs, nas aplicações móveis a autenticação é realizada utilizando **Access Tokens**, onde o IS atende bem.

### APIs
Caso seja necessário a autenticação entre APIs, ela também pode ser realizada via **Access Token**, onde novamente entraria o uso do IS.

> É importante lembrar que o IS utiliza o protocolo OID Connect, o que significa que suas APIs com Node, Go, Python ou qualquer outra linguagem/Framework podem se comunicar com ele.

### ASP.NET Identity
Por fim mas não menos importante, na aplicações Web com ASP.NET (Server-Side) normalmente temos autenticação via Cookies e utilizando o **ASP.NET Identity**. Podemos conectar o IS com nostas aplicações ASP.NET facilmente.

> **Autenticação via Token** é realizada enviando um usuário e senha para o servidor, que valida estas informações e gera uma HASH, encriptada com uma chave secreta que só o servidor sabe.

> **JWT** é a sigla para Json Web Token. Quanto a HASH mencionada acima é gerada, ela é dividida em três partes e o formato desta HASH quando desencriptada é um JSON.

> **Autenticação via Cookies** enquanto em APIs nós NUNCA permanecemos autenticados, nas aplicações ASP.NET (Server Side) é normal utilizarmos autenticação via Cookies, o que significa que um HASH será gerado e armazenado nos Cookies do Browser do usuário.

> Em ambas autenticações (Token ou Cookie), nós sempre enviamos as informações do usuário no cabeçalho de cada requisição.

### Federation Gateway
O IS permite a utilização de serviços externos para validação de identidade, como Facebook, Google, Microsoft, dentre outros. Podemos mesclar nossa autenticação com as externas.

> **Autenticação** diz quem o usuário é, enquanto **Autorização** diz o que o usuário pode fazer. Em suma podemos nos basear tanto em informações internas (Nosso banco de dados por exemplo) para dizer que um usuário é realmente quem ele diz ser, mas podemos também delegar este processo para autoridades externas como o Facebook.
>
> O que acontece em ambos casos é uma autoridade que confiamos, endossando que um usuário é verdadeiro.
> * "balta.io, me confirma se xpto@balta.io é realmente o André Baltieri"
> * "Facebook, me confirma se xpto@balta.io é realmente o André Baltieri"
> * "Microsoft, me confirma se xpto@balta.io é realmente o André Baltieri"
>
> A pergunta é sempre a mesma, só muda para qual autoridade (Entidade na qual confiamos) perguntamos.

### Outras considerações
O IS também é muito customizável, o que significa que podemos alterar muita coisa nele. Ele é Open Source e está no mercado há um bom tempo, ou seja, tem uma boa maturidade e não tem custo.


## Autenticação no cenário atual

![Cenário atual de autenticação](https://identityserver4.readthedocs.io/en/latest/_images/appArch.png)

<small>Fonte: Documentação oficial do Identity Server</small>

Um cenário comum (Não é uma regra) que temos hoje é a comunicação entre diversas aplicações e APIs com nossos servidores, como mostrado na imagem acima.

Neste cenário, é comum o Browser se comunicar com a aplicação Web que é renderizada no servidor, assim como é comum, uma determinada página do Web App fazer uma requisição para uma API, também no servidor.

Note que internamente, também há uma comunicação entre o Web App e qualquer outra API que seja necessária para ele compor uma requisição.

Por fim, aplicações nativas e outros servidores (Serviços externos) também fazem uso da nossa API. Por sua vez, tanto nossa API quanto App Web fazem uso de outras APIs internas.

Inicialmente pode parecer um cenário confuso, mas pense que estes itens agem de forma independente e podem se comunicar entre si.

Na primeira camada, onde ficam as aplicações (Browser, Desktop, Mobile e até APIs externas) temos a necessidade de implementar uma autenticação, afinal, muitos usuários só podem ver seus dados, ou não podem acessar determinadas informações.

A proposta de um servidor de identidade é justamente centralizar este recurso comum entre os Apps, a autenticação. Imagina precisar escrever um processo de login para cada uma das Apps?

![Processo reestruturado](https://identityserver4.readthedocs.io/en/latest/_images/protocols.png)

<small>Fonte: Documentação oficial do Identity Server</small>

Esta seria uma visão do mesmo diagrama, mas de uma forma mais estruturada, utilizando um servidor de autenticação e protocolos de autenticação como **OAuth2**.

### SAML
SAML/SAML2p é um protocolo de autenticação que foi muito conhecido e usado. Na verdade ele ainda é, mas o OpenId vem dominando as aplicações modernas.

## WS-Federation
Assim como o SAML o WS-Federation também é um protocolo de autenticação que teve muita força na época do WCF, porém, atualmente vemos o OpenId muito a sua frente.

## OpenId Connect
É o mais novo entre os citados anteriormente (Existem outros protocolos de autenticação) mas considera como o **futuro da autenticação** (Presente e futuro na verdade).

Ele já foi pensado nos cenários atuais com múltiplas aplicações, serviços e até autenticações externas. É atualmente a recomendação para protocolos de autenticação.

### OAuth
Nossas aplicações possuem duas formas de se comunicar com APIs de forma segura, sendo a primeira utlizando uma identidade própria, gerada pela aplicação ou delegando esta identidade para um servidor externo.

As vezes precisamos utilizar ambos modos e o **OAuth** entra justamente neste ponto. Ele é um protocolo que permite nossas aplicações requisitarem um Token e utilizá-lo em suas requisições.

Conforme comentamos anteriormente neste artigo, isto reduz a escrita de código e complexidade, concentrando a parte de autenticação das aplicações em um único ponto.

O **Open ID Connect** e o **OAuth 2** são bem similares. Na verdade o Open ID Connect é uma extensão do **OAuth 2** que mantém os dois pilares principais de segurança, a autenticação e o acesso à APIs, combinados em um único protocolo.

A combinação destes dois protocolos fornece tudo que pudemos ver na imagem anterior, com autenticação para aplicações Desktop, Web, Mobile e APIs.

## Identity Server no ASP.NET Core
O Identity Server é uma implementação do Open ID Connect/OAuth 2.0 no formato de Middleware para o ASP.NET.

Basicamente, podemos ter qualquer lógica dentro das APIs ou aplicações que isto não afetará a implementação do IS, visto que ele abstrai a parte de autenticação das aplicações apenas.

## Outras terminologias

### Usuário ou User
A pessoa que utiliza um cliente registrado para obter acesso à um recurso.

### Cliente ou Client
Software que requisita os Tokens do Identity Server, tanto para autenticar outros serviços e softwares quanto pessoas. Um cliente **deve ser registrado** no servidor de identidade antes de requisitar um Token.

### Resources ou Recursos
São as informações que queremos proteger com nosso servidor de identitdade. Todo recurso tem um nome único e os clientes utilizam este nome único para especificar qual recurso eles querem acessar.

### Identity Data
Informações de identidade também são conhecidas como **Claims** e são referentes ao usuário, como nome ou endereço de E-mail.

### Identity Token
Resultado de um processo de autenticação que contém o mínimo de identificação possível sobre o usuário, também chamado de **subject** ou **subject claim**, junto a informação de como e quando ele se autenticou.

### Access Token
Permite acesso á um recurso. Os clientes requisitam tokens de acesso e os encaminham a cada requisição para as APIs (Recursos). Os Tokens de acesso contém informações tanto sobre o cliente quanto sobre o usuário e a API utiliza esta informação para autorizar o acesso à seus dados.

## Especificações suportadas

### OpenID Connect
- OpenID Connect Core 1.0
- OpenID Connect Discovery 1.0
- OpenID Connect Session Management 1.0
- OpenID Connect Front-Channel Logout 1.0
- OpenID Connect Back-Channel Logout 1.0

### OAuth 2.0
- OAuth 2.0 (RFC 6749)
- OAuth 2.0 Bearer Token Usage (RFC 6750)
- OAuth 2.0 Multiple Response Types
- OAuth 2.0 Form Post Response Mode
- OAuth 2.0 Token Revocation (RFC 7009)
- OAuth 2.0 Token Introspection (RFC 7662)
- Proof Key for Code Exchange (RFC 7636)
- JSON Web Tokens for Client Authentication (RFC 7523)
- OAuth 2.0 Device Authorization Grant (RFC 8628)
- OAuth 2.0 Mutual TLS Client Authentication and Certificate-Bound Access Tokens (RFC 8705)
- JWT Secured Authorization Request

## Fontes
 * [Documentação do Identity Server](https://identityserver4.readthedocs.io/)

<div role="main" id="blog-s1-dotnet-autenticacao-497d7348c272b594f055"></div>
<script type="text/javascript" src="https://d335luupugsy2.cloudfront.net/js/rdstation-forms/stable/rdstation-forms.min.js"></script>
<script type="text/javascript"> new RDStationForms('blog-s1-dotnet-autenticacao-497d7348c272b594f055', 'UA-48664517-12').createForm();</script>
