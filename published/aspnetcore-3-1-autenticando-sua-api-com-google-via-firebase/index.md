Se tem algo que praticamente toda aplicação precisa é autenticação, e nos dias de hoje, se autenticar com redes sociais é algo que traz muita praticidade para seu usuário.  
  
Mas como fica nossa API com este tipo de autenticação? Como validamos o token? Como recuperamos o usuário?  
  
Quem me conhece sabe que não uso o ASP.NET Identity para quase nada. Acho ele bem útil quando usamos Razor (Alguém usa ainda?) porém no cenário de APIs, criar uma autenticação e autorização é bem simples, como eu mostro no curso [7194 – Criando APIs Data Driven com ASP.NET Core 3 e EF Core 3](https://balta.io/cursos/criando-apis-data-driven-com-aspnet-core-3-e-ef-core-3)  
Por incrível que pareça, para fazer autenticação com o Google ou Facebook, ou GitHub ou mesmo um Identity Server seu, também é bem simples, tudo que precisamos é do padrão oAuth/JWT.

Por que Firebase?
-----------------

O primeiro ponto aqui é a criação do usuário e setup das contas. O Firebase já traz tudo isto pronto para nós, com uma facilidade incrível, então se você não quer perder tempo fazendo todo aquele setup complexo, ele é uma ótima saída.  
  
Além disso, ele fornece uma SDK para .NET, Node/JS, Java e várias outras linguagens, ou seja, já fornece tudo pronto para o nosso Frontend também.  
  
Nosso primeiro passo então é acessar **https://console.firebase.google.com/**, se autenticar e criar um novo projeto.  
  
Em seguida vá para Authentication > Método de Login e habilite o Google.  
  
    https://console.firebase.google.com/project/SEUPROJETO/authentication/providers
  
Por fim, vá para as configurações do projeto **https://console.firebase.google.com/project/SEUPROJETO/settings/general/** e adicione uma nova aplicação.  
  
Para esta demo, utilizaremos uma aplicação Web, então ao registrá-la teremos o seguinte código:

    <!-- The core Firebase JS SDK is always required and must be listed first -->
    <script src="https://www.gstatic.com/firebasejs/7.5.0/firebase-app.js"></script>

    <!-- TODO: Add SDKs for Firebase products that you want to use
        https://firebase.google.com/docs/web/setup#available-libraries -->

    <script>
    // Your web app's Firebase configuration
    var firebaseConfig = {
        apiKey: "AIzaSyDMKIg45XOksYVHmRd9iv6amevCxNPBa4M",
        authDomain: "todo-e9f4c.firebaseapp.com",
        databaseURL: "https://todo-e9f4c.firebaseio.com",
        projectId: "todo-e9f4c",
        storageBucket: "todo-e9f4c.appspot.com",
        messagingSenderId: "88155569726",
        appId: "1:88155569726:web:d2ec12e8cad7b416855b6f"
    };
    // Initialize Firebase
    firebase.initializeApp(firebaseConfig);
    </script>

Guarde este código, pois utilizaremos ele em breve.

Autenticando no Frontend
------------------------

O primeiro passo é fazer autenticação no Frontend, no nosso HTML, Angular, React, Vue, o que quer que seja.  
  
Este processo é o padrão que realizamos em todo App de Frontend, onde vamos até o servidor de autenticação (Que em certos casos pode ser nossa própria API) e nos é gerado um Token no formato JWT.  
  
Eu já expliquei sobre oAuth, JWT, Bearer e muito mais coisas sobre segurança, autenticação e autorização neste artigo:  
[https://balta.io/blog/aspnet-core-autenticacao-autorizacao](https://balta.io/blog/aspnet-core-autenticacao-autorizacao)  
  
Com o Token em mãos, a cada requisição para nossa API nós enviamos ele no cabeçalho, como fazemos em um modelo convencional de autenticação em aplicações SPA.  
  
**IMPORTANTE**  
Nunca, em hipótese alguma, confie nos dados do Frontend. Você talvez esteja se perguntando... se eu já tenho os dados do usuário (Nome, E-mail, Imagem) que recebi do Google, por que não enviar apenas o E-mail para API?  
  
Qualquer pessoa poderia IMPERSONAR você neste caso, ou seja, se passar por você. Basta abrir o Postman por exemplo e fazer uma requisição para API informando um E-mail diferente. Isto é inseguro.  
  
O processo será _SEMPRE_ enviar o Token, que é encriptado no servidor do Google, e na API validamos este Token e obtemos seus dados.  
  
É assim que todo servidor de identidade funciona, ele provê e valida os Tokens, isto no Google, Facebook, GitHub, Azure AD, Identity Server e até no seu próprio IS.  
  
Pois bem, vamos então iniciar nosso processo para obter este Token que precisamos enviar, e para isto o código é relativamente simples.  
  
A primeira coisa que devemos fazer é incluir os arquivos do Firebase. No meu caso, criei uma nova API com ASP.NET Core (_dotnet new webapi -o Todo_) e dentro desta API criei uma pasta Client com um arquivo index.html nela.  
  
O Firebase é modularizado, então precisamos incluir dois arquivos, um base e um para autenticação.  

    <script src="https://www.gstatic.com/firebasejs/7.5.0/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/6.2.0/firebase-auth.js"></script>
  
Nosso próximo passo é inicializar o Firebase, informando as configurações que recebemos ao criar nosso App no console dele.  

    var firebaseConfig = {
        apiKey: "AIzaSyAKsIl5UdCMghM9OgaTw14iCBuFSNilYk4",
        authDomain: "todos-d70a6.firebaseapp.com",
        databaseURL: "https://todos-d70a6.firebaseio.com",
        projectId: "todos-d70a6",
        storageBucket: "todos-d70a6.appspot.com",
        messagingSenderId: "811053328343",
        appId: "1:811053328343:web:c74accaddf3e1bdd9f4d92"
    };
    // Initialize Firebase
    firebase.initializeApp(firebaseConfig);
  
Pronto, nosso App está registrado e inicializado. Neste momento você já pode rodar esta aplicação para testar e recomendo que utilize o Http Server para isto.  
  
Abra um termina e navegue para a pasta Client que criamos, em seguida execute os seguintes comandos:  

    npm install -g http-server
    http-server -c1
  
Isto vai instalar o Http Server na sua máquina e executar um servidor HTTP para arquivos estáticos na pasta Client. O “-c1” significa que o cache vai durar apenas um segundo, ou seja, não queremos cachear nada.  
  
Feito isto, seu App estará disponível no endereço http://localhost:8080. Precisamos disso pois o Firebase não permite requisições de arquivos (C:/dev/index.html por exemplo), somente de URLs.  
  
Com o Firebase configurado, é hora de configura a autenticação, e ele nos fornece diversos Providers para isto, dentre eles o do Google.  
  
Além do Provider, precisamos informar o Scope, que é a gama de informações do usuário que queremos ter acesso. Se você não informar nada, ele obtém apenas o E-mail se não me engano.  

    // Initialize Firebase
    firebase.initializeApp(firebaseConfig);
    var provider = new firebase.auth.GoogleAuthProvider();
    provider.addScope('https://www.googleapis.com/auth/contacts.readonly');
  
Esta configuração é a mesma para qualquer Provider, só mudando a URL do Scope. Agora é só executar o Login.  

    function login() {
        firebase.auth().signInWithPopup(provider).then(function (result) {
            // ESTE NÃO É O JWT, É O TOKEN PARA OBTER INFOS DO GOOGLE
            var token = result.credential.accessToken;
            var user = result.user;
            firebase.auth()
                .currentUser
                .getIdToken()
                .then((idToken) => {
                    // ESTE É O TOKEN JWT QUE VAMOS MANDAR PARA API
                    console.log(idToken);
                }).catch(function (error) {
                    console.log(error);
                });
        }).catch(function (error) {
            // DEU RUIM
            console.log(error);
        });
    }
  
Eu deixei comentado algumas linhas para informar que temos dois Tokens durante o Login, o primeiro é um Token interno do Google, que utilizamos para gerar nosso JWT e o segundo é o que de fato queremos.  
  
Neste modelo também usamos o SignInWithPopUp, que vai abrir aquela famosa janelinha do Google para o usuário poder se autenticar.  
  
Por fim eu deixei o Token sendo logado no console do Browser para podermos obtê-lo com facilidade e testar nossa API.  
  
Falta apenas o botão de login, que ficou assim:  

    <button onclick="login()">LOGIN COM GOOGLE</button>
  
Pronto, temos nosso Frontend se autenticando com o Google e escrevendo o Token no console após o login. O resultado do arquivo Client/index.html é este aqui:  

    <html>

    <head>
        <title>Fire</title>
    </head>

    <body>
        <button onclick="login()">LOGIN COM GOOGLE</button>

        <script src="https://www.gstatic.com/firebasejs/7.5.0/firebase-app.js"></script>
        <script src="https://www.gstatic.com/firebasejs/6.2.0/firebase-auth.js"></script>

        <script>
            var firebaseConfig = {
                apiKey: "AIzaSyAKsIl5UdCMghM9OgaTw14iCBuFSNilYk4",
                authDomain: "todos-d70a6.firebaseapp.com",
                databaseURL: "https://todos-d70a6.firebaseio.com",
                projectId: "todos-d70a6",
                storageBucket: "todos-d70a6.appspot.com",
                messagingSenderId: "811053328343",
                appId: "1:811053328343:web:c74accaddf3e1bdd9f4d92"
            };
            // Initialize Firebase
            firebase.initializeApp(firebaseConfig);
            var provider = new firebase.auth.GoogleAuthProvider();
            provider.addScope('https://www.googleapis.com/auth/contacts.readonly');
            function login() {
                firebase.auth().signInWithPopup(provider).then(function (result) {
                    // ESTE NÃO É O JWT, É O TOKEN PARA OBTER INFOS DO GOOGLE
                    var token = result.credential.accessToken;
                    var user = result.user;
                    firebase.auth()
                        .currentUser
                        .getIdToken()
                        .then((idToken) => {
                            // ESTE É O TOKEN JWT QUE VAMOS MANDAR PARA API
                            console.log(idToken);
                        }).catch(function (error) {
                            console.log(error);
                        });
                }).catch(function (error) {
                    // DEU RUIM
                    console.log(error);
                });
            }
        </script>
    </body>

    </html>
  

Autenticando no Backend
-----------------------

Agora vem a parte mais legal, autenticar no Backend, onde precisamos receber o Token no cabeçalho da requisição e validá-lo nos servidores do Google para receber as informações do usuário.  
  
O processo é mesmo que fazemos nas autenticações normais, que eu mostro com detalhes no curso [7194 – Criando APIs Data Driven com ASP.NET Core 3 e EF Core 3](https://balta.io/cursos/criando-apis-data-driven-com-aspnet-core-3-e-ef-core-3).  
  
Vamos começar adicionando o pacote de Autenticação/JWT a nossa API.  
    dotnet add package Microsoft.AspNetCore.Authentication.JwtBearer
  
Feito isto, vamos garantir que estamos exigindo autenticação e autorização em nossa API, indo até o método Configure do Startup.cs e verificando se há estas duas linhas:  

    app.UseAuthentication();
    app.UseAuthorization();
  
Agora precisamos informar para nossa API que estamos utilizando autenticação e que ela é do tipo JWT/Bearer. Isto é feito no método ConfigureServices, adicionando a linha abaixo:  

    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)_  
  
Isto é tudo que precisamos para dizer a nossa API que estamos trabalhando com autenticação e que vamos esperar Tokens do tipo JWT pelo cabeçalho Authorization do tipo Bearer.  
  
Até então nossa API está preparada para receber estes Tokens, neste formato, mas ela não sabe COMO ela vai desencriptar este Token e ter acesso às informações do usuário.  
  
Para isto ocorrer, precisamos dizer para nossa API qual URL ela usa para verificar o Token, e isto é chamado de **Authority**.  
  
O servidor de identidade (Google no nosso caso) não vai receber Tokens de qualquer lugar e validá-los, ele precisa saber quem está mandando este Token, afinal, podemos ter vários Apps com Login do Google na mesma conta. Desta forma, precisamos informar o **Issuer** e o **Audience**, que é a URL utilizada para verificar o Token da nossa aplicação e o ID da nossa aplicação respectivamente.  
  
O código final para este trecho fica assim:  

    services
        .AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
        .AddJwtBearer(options =>
        {
            options.Authority = "https://securetoken.google.com/<ID DO SEU PROJETO>";
            options.TokenValidationParameters = new TokenValidationParameters
            {
                ValidateIssuer = true,
                ValidIssuer = "https://securetoken.google.com/<ID DO SEU PROJETO>",
                ValidateAudience = true,
                ValidAudience = "<ID DO SEU PROJETO>",
                ValidateLifetime = true
            };
        });
  
Pronto, temos Login com Google sendo validado na nossa API.

**Validando a autenticação**

Com tudo configurado, vamos para qualquer método do nosso Controller padrão criado e vamos adicionar o atributo _\[Authorize\]_. Faça isto e tente chamar o método via Postman por exemplo. Você terá um erro **401 – Unauthorized**.  
  
Agora copie o Token gerado pelo processo anterior que fizemos, no Client, e faça uma nova requisição para sua API, na mesma URL, mas agora informando o cabeçalho:  

    Authorization | Bearer SEUTOKEN
  
Magicamente nossa API validou nosso Token nos servidores da Google e já permitiu nosso acesso ao método que solicitamos. Incrível, não é?  
  
Com pouquíssimas linhas de código nós integramos nossa API ao Google e ainda conseguimos usar os atributos padrões do ASP.NET Core, de forma simples e rápida (E melhor... **_SEM ASP.NET IDENTITY_**).  
  
Para finalizar, se você quiser obter os dados do usuário, eles ficam em **User.Identity.Claims**, aí fica uma lista com tudo que o Scope que pedimos anteriormente permite.  
  
O código final do Controller está aqui:  

using System.Linq;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;

    namespace fire.Controllers
    {
        [ApiController]
        [Route("")]
        public class HomeController : ControllerBase
        {
            [HttpGet]
            [Authorize]
            public dynamic Get()
            {
                return new
                {
                    name = User.Claims.Where(x => x.Type == "name").FirstOrDefault().Value,
                    // email = User.Claims.Where(x => x.Type == "email").FirstOrDefault().Value,
                    picture = User.Claims.Where(x => x.Type == "picture").FirstOrDefault().Value,
                };
            }
        }
    }
  
E o código fonte completo deste exemplo você encontra no meu GitHub:  
[https://github.com/andrebaltieri/aspnetcore-firebase-google-login](https://github.com/andrebaltieri/aspnetcore-firebase-google-login)

<div role="main" id="blog-s1-dotnet-autenticacao-497d7348c272b594f055"></div>
<script type="text/javascript" src="https://d335luupugsy2.cloudfront.net/js/rdstation-forms/stable/rdstation-forms.min.js"></script>
<script type="text/javascript"> new RDStationForms('blog-s1-dotnet-autenticacao-497d7348c272b594f055', 'UA-48664517-12').createForm();</script>
