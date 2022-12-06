Publicando aplicativo web no Azure, utilizando Identity, SQLite e GitHub Actions.

Sumário

1. [Introdução](#introducao)
2. [Instalações.](#instalacoes)
3. [Criando repositório remoto.](#criandorepositorioremoto)
4. [Repositório local e criação do app.](#repositoriolocalecriacaodoapp)
5. [Gerando as páginas do Identity.](#gerandoaspaginasdoidentity)
7. [Modelo do usuário da nossa app.](#modelocousuariodanossaapp)
8. [Personalizando a página de registro do usuário.](#personalizandoapaginaderegistrodousuario)
9. [Email service.](#emailservice)
10. [Microsoft Azure](#microsoftazure)
11. [Criando workflow.](#criandoworkflow)
9. [Referências.](#referencias)

*******

<div id='introducao'></div> 

## Introdução

Neste artigo vou falar sobre uma forma simples e prática de implementar e publicar um aplicativo web no Azure. Este app
será
criado através de comandos CLIs: .NET, Git, GitHub, e Azure. E nele, vamos implementar autenticação e autorização com
Identity, SQLite como banco de dados, e o processo de deployment será feito em um App Service no Azure, automatizado
pelo
GitHub Actions, em uma máquina Linux.

<div id='instalacoes'></div>

## Instalações

Para excutar os passos que vou demonstrar neste artigo será necessário ter instaladas as CLIs: .NET, Git, GitHub, e
Azure.
Eu estou usando Windows Terminal, mas estes comandos podem ser executados em um terminal de sua escolha, em uma máquina
Linux
ou Mac. Para editar o nosso código, também vamos precizar de uma IDE, estou utilizando Rider, mas podem ser VS Code,
Visual
Studio, etc.

<div id='criandorepositorioremoto'></div>

### Criando repositório remoto.

Vamos dar início à nossa jornada criando o nosso repositório remoto no GitHub. No browser, vamos acessar a página dos
nossos
repositórios, https://github.com, clicando no botão "New". Então, seremos redirecionados à página onde vamos dar um nome
ao
nosso repo, e sem adicionar o arquivo .gitignore, vamos concluir clicando no botão "Create Repository". E, na página
inicial
do nosso repositório, copiando a Url do repo, disponibilizado pelo GitHub, vamos passar para a próxima etapa.

![New_repo](images/newrepo.png?raw=true)

<div id='repositoriolocalecriacaodoapp'></div> 

### Criação do app e repositório local.

Na nossa máquina é recomendado que o repositório local seja feito em uma pasta com um caminho curto, como C:/dev/. Nesta
pasta vamos criar um aplicativo do tipo ``webapp`` vazio, que vou dar o nome de Article, e navegando para pasta do app,
vamos criar o arquivo gitignore, iniciar os arquicos .git, renomear a branch principal para "main", e adicionar uma
referência ao nosso repositório remoto.

```
cd .\dev\
dotnet new webapp -o Article
cd .\Article\
dotnet new gitignore
git init
git branch -M main
git remote add origin https://github.com/crisnordev/Article.git
```

Lembrando que no último comando você deve trocar a Url, pela referência ao seu repositório remoto.

Para a criar o nosso projeto vamos adicionar alguns parâmetros ao comando "dotnet new", especificando o nome do projeto,
e
a utilização do Identity, e o
banco de dados será criado com base no SQLite.

<div id='gerandoaspaginasdoidentity'></div>

### Gerando as páginas do Identity.

Para gerar as páginas do Identity, biblioteca .NET que vai fornecer suporte para autenticação do usuário da nossa
aplicação, utilizaremos o scaffolder do Identity, pela linha de comando. Para isso, devemos instalar a ferramenta de
geração de código do ASP.NET, o pacote para geração de código do Visual Studio, e mais alguns pacotes:

```
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

Agora, vamos visualisar as opções do scaffolder do Identity, e se por acaso retornar alguma mensagem de erro verifique
as
versões dos pacotes, que devem ser compatíveis. Na sequência vamos executar o scaffolder, passando como parâmetro, o
nome
da classe do nosso usuário, quais as páginas que deverão ser criadas, e por fim o SqLite.

```
dotnet aspnet-codegenerator identity --help
dotnet aspnet-codegenerator identity --userClass ApplicationUser --files "Account.Register;Account.Login;Account.Logout;Account.RegisterConfirmation" --useSqLite
```

Caso queira que o scaffolder gere todas as páginas do identity, basta remover o parâmetro ``--files`` que está recebendo
os nomes dos arquivos que especifiquei para serem criados.

<div id='modelocousuariodanossaapp'></div>

### Modelo de usuário do app.

A fim de oferecer uma experiência um mais personalizada ao usuário da nossa aplicação, junto com as páginas do Identity
nós criamos a classe para o modelo do usuário. Porém, como sugestão, eu vou modificar o local da pasta Data, e também
vou
criar a pasta Models, na raiz do nosso app, onde vou colocar a classe "ApplicationUser". Note que esta classe está
indicando ser do tipo IdentityUser através de herança, e também vamos alterar o tipo que será atribuído ao Id do usuário
para Guid (padrão string). Além disso, vamos sobrescrever algumas propriedades, para que o cadastro do usuário tenha o
comportamento que nós queremos.

![ApplicationUser](images/applicationuser.png?raw=true)

Lembrando que caso você tenha modificado o local onde estão dispostos DataContext e ApplicationUser, os namespaces
deverão
ser corrigidos, assim como as referências "using" das classes que estão utilizando eles.

Note que estamos sobrescrevendo UserName, para poder receber a entrada de um valor do tipo string (por padrão recebe o
valor do e-mail), que vai nos possibilitar a utilização de um nome de usuário personalizado, o que nos obriga a alterar 
a propriedade NormalizedUserName, que vai receber o valor de UserName convertido em letras maiúsculas.

Note também que na classe em que manipulamos o DataContext, devemos adicionar uma referência ao nosso ApplicationUser, e
como estamos alterando o tipo do Id, também devemos referenciar IdentityRole, informando que os dois devem estar
utilizando
o tipo Guid no Id.

<div id='personalizandoapaginaderegistrodousuario'></div>

### Personalizando a página de registro do usuário.

Agora nós vamos alterar Register.cshtml.cs, e Register.cshtml, para receber a entrada personalizada do UserName. Para
isso,
em Areas/Identity/Pages/Account, em Register.cshtml.cs, vamos até a classe ImputModel, e vamos adicionar a propriedade
UserName.

```csharp
[Required]
[Display(Name = "User Name")]
public string UserName { get; set; }
```

Também em Register.cshtml.cs, vamos alterar o método OnPostAsync(), na linha onde estamos atribuindo o valor para o nome
do usuário, substituindo pela seguinte linha.

```csharp
await _userStore.SetUserNameAsync(user, Input.UserName, CancellationToken.None);
```

Agora, em Register.cshtml, na ```<div>``` que está recebendo o input do email, na primeira linha do ```<input>``` vamos 
alterar o autocomplete para ```autocomplete="email"```, e antes dessa ```<div>``` vamos adicionar o código para receber 
o input do UserName.
```csharp
<div class="form-floating mb-3">
    <input asp-for="Input.UserName" class="form-control" autocomplete="username" aria-required="true" placeholder="User name"/>
    <label asp-for="Input.UserName">User Name</label>
    <span asp-validation-for="Input.UserName" class="text-danger"></span>
</div>
```
<div id='emailservice'></div>

## Email service.

Para configuração do serviço de e-mail, eu estou utilizando a API do SendGrid, adicionando o pacote nuget através do 
comando ```dotnet add package SendGrid```. Você pode criar uma conta grátis, https://sendgrid.com/free/?source=sendgrid-csharp. 
Na sua conta, para possibilitar a cofiguração do serviço de e-mail na nossa aplicação, você vai precisar de uma chave 
da API do SendGrid. Para criar a chave você deve navegar para aba "Settings", "Api Keys", "Create API Key", adicionando 
o nome que será tribuido a essa chave, selecionando "Restricted Access", arrastando a barra "Mail Send" até "Full Access", 
e por fim "Create API Key". Você será redirecionado à página, que irá te mostrar a chave uma única vez, é recomendado copiar 
e armazenar essa chave em um local seguro, nós vamos precisar dela mais para frente, lembrando que essa chave é sua 
e ninguém mais pode ter acesso a ela.

Na nossa aplicação, criando a pasta Services, vamos adicionar a classe EmailSender, que vai receber os dados para o 
serviço de envio de e-mail.

```csharp
public class EmailSender : IEmailSender
{
    public async Task SendEmailAsync(string toEmail, string subject, string message)
    {
        if (string.IsNullOrEmpty(Configuration.SendGridKey.SendGridApiKey)) throw new Exception("Null SendGridKey");

        await Execute(Configuration.SendGridKey.SendGridApiKey, subject, message, toEmail);
    }

    public static async Task Execute(string apiKey, string subject, string message, string toEmail)
    {
        var client = new SendGridClient(apiKey);
        var msg = new SendGridMessage
        {
            From = new EmailAddress("email@email.com", "Cristiano Noronha"),
            Subject = subject,
            PlainTextContent = message,
            HtmlContent = message
        };
        msg.AddTo(new EmailAddress(toEmail));

        msg.SetClickTracking(false, false);
        await client.SendEmailAsync(msg);
    }
}
```
O Identity usa injeção de dependêcia da interface IEmailSender para instânciar o serviço de e-mail. Na classe EmailSender, 
que acabamos de criar, nós estamos implementando essa interface, porém para a nossa aplicação saber que nas ingeções de 
dependência da interface IEmailSender, deverá ser utilizada a implementação que criamos no Program.cs, nós devemos 
resolver a dependência adicionando a linha de código ```builder.Services.AddSingleton<IEmailSender, EmailSender>();```,
onde estamos passando o tempo de vida da instância como Singleton, porque ela não vai ser modificada ao longo do tempo, e 
assim, sempre que o Identity precisar do IEmailSender, saberá que deve utilizar a implementação EmailSender.

Além disso, para receber a API Key do SendGrid, no arquivo appsettings.json, vamos adicionar uma seção com o 
nome usado na criação da chave, no SendGrid, para receber a API Key, que estará configurada no Azure. No caso eu nomeei 
como "My_SendGrid_Key".
```json
{
  "SendGridKey": {
    "SendGridApiKey": "My_SendGrid_Key"
  }
}
```

E, para receber essa chave na nossa aplicação vamos criar a classe Configuration.cs: 
```csharp
public class Configuration
{
    public static SendGridConfig SendGridKey { get; set; }
    
    public class SendGridConfig
    {
        public string SendGridApiKey { get; set; }
    }
}
```

E,  no Program.cs, vamos instanciá-la, pegando o valor da chave que estará entrando pelo appsettings.json, e atribuindo 
à propriedade SendGridKey, adicionando as seguintes linhas:

```csharp
var sendGridKey = new Configuration.SendGridConfig();
app.Configuration.GetSection("SendGridKey").Bind(sendGridKey);
Configuration.SendGridKey = sendGridKey;
```

<div id='microsoftazure'></div>

## Microsoft Azure.

Nós vamos fazer toda a parte do deployment do nosso app no Azure, plataforma de núvem da Microsoft. Onde é possível 
criar uma conta grátis, acessando https://azure.microsoft.com/. Ao criar a conta, é gerado uma "Assinatura" 
(Subscription), "Azure Subscription 1". Com essa assinatura nós vamos criar um "Grupo de Recursos" (Resource Group), 
que também pode ser encontrado pela barra de busca, e navegando até a respectiva página, clicando no botão "+ Criar"
(Create), onde selecionando a assinatura, dando um nome ao grupo de recursos, e selecionando a nossa região 
"(South America) Brazil South", vamos clicar no botão "Revisar + criar" (Review + create), em seguida "Criar" (Create).

![Resource_group](images/resourcegroup.png?raw=true)

Da mesma forma, vamos criar o "Serviços de Aplicativos" (App Service), navegando até a página, clicando no botão 
"+ Criar", seremos redirecionados à pagina onde selecionando a nossa "Assinatura" (Subscription), o 
"Grupo de recursos" (Resource Group) que criamos, escolhendo um nome para a aplicação, que deve ser único pois será 
utilizado na Url, "Publicar" (Publish) como "Codigo", "Pilha de Runtime" (Runtime Stack) nós vamos selecionar a versão
do .NET que estamos utilizando no nosso app, "Sistema Operacional" (Operating System) como "Linux", "Região" (Region) 
como "Brazil South", "Plano do Linux (Brazil South)" (Linux Plan) clicando no "Criar Novo" (Create New), vamos dar 
um nome ao plano Linux, que na sequência, clicando no botão "Alterar Tamanho" (Change Size), estaremos selecionando o 
plano "Gratuito F1, 1 GB de memória", então clicar em "Revisar + criar" (Review + create), e por fim "Criar" (Create).

![App_service](images/appservice.png?raw=true)

<div id='criandoworkflow'></div>

## Criando workflow.


<div id='referencias'></div>

## Referências.

[Curso: Fundamentos do Azure, Git, GitHub e DevOps](https://balta.io/player/assistir/442da086-3cac-4d96-9332-cdab3797c01c)

[Introduction to Identity on ASP.NET Core](https://learn.microsoft.com/en-us/aspnet/core/security/authentication/identity?view=aspnetcore-6.0&tabs=visual-studio~~~~)
