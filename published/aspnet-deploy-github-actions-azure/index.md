Realizar o Deploy de aplicações ASP.NET no Microsoft Azure utilizando GitHub Actions talvez seja a escolha mais simples e prática que você possa fazer para manter seu CD em dia.

Recentemente publiquei um vídeo mostrando como realizar esta integração de uma forma visual, e neste artigo vou explicar como podemos utilizar o Azure CLI e GitHub CLI para fazer tudo via linha de comando.

[![ASP.NET Core Deploy GitHub Actions Azure](https://img.youtube.com/vi/giCR4VgKjQY/0.jpg)](https://www.youtube.com/watch?v=giCR4VgKjQY)

## CI/CD

Os termos **CI** e **CD** estão presentes na cultura **DevOps** tendo como objetivo garatir a entrega e integridade das nossas aplicações.

CI é a sigla para **Continuous Integration**, que representa a garantia da integridade da nossa aplicação. É no CI por exemplo que executamos os testes para garantir que nada está quebrado.

CD pode ser a sigla para **Continuous Deployment** ou **Continuous Delivery**. Continuous Delivery tem uma representatividade mais fixa, como por exemplo, releases diárias, semanais, mensais, de acordo com o negócio.

Já o Continuous Deployment está um passo a frente, sendo a publicação realizada sempre que as mudanças de código passarem pelas integrações. Neste artigo cobriremos o **Cotinuous Deployment**, onde cada Push na branch Master gera uma nova publicação.

## Instalações

Para este passo, vamos utilizar o [Visual Studio Code, ASP.NET Core e .NET Core](https://balta.io/blog/dotnet-instalacao-configuracao-e-primeiros-passos), caso ainda não possua algum destes instalados, realize o mesmo antes de prosseguir.

### Azure CLI

A segunda instalação que precisaremos realizar é o [Azure CLI](https://docs.microsoft.com/pt-br/cli/azure/install-azure-cli?view=azure-cli-latest), que habilitará o comando <code>az</code> no nosso terminal.

### GitHub CLI

Por fim, precisamos também do [GitHub CLI](https://github.com/cli/cli) que habilitará o comando <code>gh</code> me nosso terminal.

### Windows Terminal

Este não é obrigatório, mas é uma recomendação. Caso esteja no Windows, recomendo testar o novo [Windows Terminal](https://devblogs.microsoft.com/commandline/windows-terminal-1-0/).

## Criando um Web App no Microsoft Azure

Tanto criar o Web App quanto o repositório no GitHub primeiro não importa, então vamos começar criando nossa aplicação no Azure.

### Autenticando no Azure

O primeiro passo após instalar o Azure CLI é abrir seu terminal e se autenticar, utilizando o comando abaixo:

```
az login
```

Feito isto, um browser se abrirá para você se autenticar e pronto, login feito!

> Caso possua mais de uma assinatura ativa no Azure, você pode utilizar o comando <code>az account list</code> e <code>az account set NOME</code> para visualizar as assinaturas e tornar uma assinatura padrão respectivamente.

### Criando um Resource Group

Os **Resource Groups** são utilizados para agrupar nossos recursos, garantindo que estarão na mesma rede e provendo uma menor latência, além de ajudar na organização de tudo.

Para esta demonstração vamos criar um novo grupo chamado <code>aspnetghcdeploy</code>, utilizando o comando abaixo:

```
az group create --location brazilsouth --name aspnetghcdeploy
```

Utilizamos a flag <code>--location</code> para definir a região do grupo, neste caso Brazil South.

### Criando o Web App

O próximo passo é criar nossa aplicação, e precisaremos informar um **Service Plan**, que é a faixa de preço do servidor que nossa App utiliza.

Vamos então criar um novo plano de serviço utilizando a SKU gratuita para não sermos cobrados.

```
az appservice plan create --name aspnetghcdeploy --resource-group aspnetghcdeploy --sku FREE
```

> Os preços variam de acordo com diversos itens, então é bom consultar a [Calculadora de preços do Azure](https://azure.microsoft.com/pt-br/pricing/details/app-service/windows/) antes de tomar qualquer decisão.

Com tudo pronto, podemos criar nossa aplicação utilizando o comando <code>az webapp create</code>, como mostrado abaixo.

```
az webapp create --name aspnetghcdeploy --resource-group aspnetghcdeploy --plan aspnetghcdeploy
```

Pronto, neste momento já temos uma aplicação rodando no endereço [https://aspnetghcdeploy.azurewebsites.net/](https://aspnetghcdeploy.azurewebsites.net/).

> Para conferir todos os comandos do Azure CLI, verifique a [documentação oficial](https://docs.microsoft.com/en-us/cli/azure/reference-index?view=azure-cli-latest).

## Criando o repositório

Nosso próximo passo é criar um repositório no GitHub e isto pode ser feito pelo comando <code>gh repo create</code> como mostrado abaixo.

```
gh repo create aspnetghcdeploy
```

Caso queira trabalhar com um repositório já existente, basta usar o comando <code>gh repo clone</code> como mostrado abaixo.

```
gh repo clone andrebaltieri/aspnetghcdeploy
```

Caso esta seja sua primeira execução do comando <code>gh</code>, você será convidado a realizar o login e associação com GitHub, recebendo as mensagens abaixo.

```
Notice: additional authorization required
Press Enter to open github.com in your browser...
Authentication complete. Press Enter to continue...
```

## Criando a aplicação

Vamos aproveitar para criar nossa aplicação. Para ter algo mais visual, vamos utilizar o ASP.NET Core MVC para gerar uma aplicação padrão.

```
cd aspnetghcdeploy
dotnet new mvc
dotnet new gitignore
```

> O comando <code>dotnet new gitignore</code> já cria um arquivo .gitignore com tudo o que precisamos. Esta dica foi enviada pelo **Ruan Silva** no nosso [canal do YouTube](https://www.youtube.com/c/baltaio). Muito obrigado!

## Enviando o código

Agora precisamos enviar este código local para o GitHub, e para isto vamos executar os seguintes comandos.

```
git add --all
git commit -m "Primeira versao"
git push -u origin master
```

Note que utilizamos a branch <code>master</code> na hora de enviar as informações utilizando o <code>git push</code>.

> Você pode realizar este processo de forma visual por dentro do **Visual Studio Code** caso desejar.

Neste momento, você deve conseguir visualizar o código da aplicação padrão do ASP.NET Core MVC no seu repositório do GitHub.

## Credenciais do Azure

Para publicar nosso código no Azure através do GitHub, precisamos de uma credencial de publicação, que no portal do Azure é chamado de **Publish Profile**.

Estas credenciais nada mais são do que um JSON contendo o ID e Secret da aplicação Web que criamos. Estes dados são sensíveis e você NÃO DEVE deixá-los públicos.

Antes de prosseguir, precisaremos do ID da assinatura do Azure na qual nosso Web App foi criada. Podemos visualizar todas as assinaturas ativas executando o comando abaixo.

```
az account list --output table
```

Agora, em posse do ID da assinatura, vamos executar o comando <code>az sp create</code> para gerar um perfil de publicação.

```
- az ad sp create-for-rbac --name "aspnetghcdeploy" --role contributor --scopes /subscriptions/<ID>/resourceGroups/aspnetghcdeploy/providers/Microsoft.Web/sites/aspnetghcdeploy --sdk-auth
```

Como resultado, você receberá um JSON conforme o descrito abaixo na tela. Copie este resultado para usarmos em breve.

```
{
  "clientId": "XXXXXXXXXXXXXXXXXXXXXX",
  "clientSecret": "XXXXXXXXXXXXXXXXXXXXXX",
  "subscriptionId": "XXXXXXXXXXXXXXXXXXXXXX",
  "tenantId": "XXXXXXXXXXXXXXXXXXXXXX",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

### Armazenando as credenciais

Conforme comentado, estas configurações são sensíveis e não devem ser expostas. O melhor lugar para armazená-las é na sessão **Secrets** do seu repositório no GitHub.

Este é o único passo que não pode ser realizado via CLI, então abra o endereço [https://github.com/andrebaltieri/aspnetghcdeploy/settings/secrets](https://github.com/andrebaltieri/aspnetghcdeploy/settings/secrets) e crie um novo segredo chamado <code>AZURE_DEPLOY</code>. O valor desta chave deve ser o JSON obtido no passo anterior.

> O nome AZURE_DEPLOY não é reservado ou exclusivo, você pode utilizar qualquer nome, desde-que não contenha espaços ou caracteres especiais.

## GitHub Actions

Os GitHub Actions são ações distribuidas em Worflows (fluxos) que realizarão o CI/CD da nossa aplicação de forma automatizada.

Um Workflow nada mais é do que um arquivo <code>yml</code> com os passos que o GitHub deve executar para publicar nossa aplicação.

Qualquer repositório do GitHub pode conter um ou mais Workflows, que serão disparados conforme os comandos em seu arquivo.

Para facilitar, vamos abrir nosso projeto no Visual Studio. Você pode realizar esta ação utilizando o comando <code>code .</code> ou abrindo manualmente.

```
cd aspnetghcdeploy
code .
```

Por padrão os Workflows devem ficar na pasta <code>.github/worflows</code>, então vamos criar nosso Workflow com o seguinte arquivo.

```
.github/workflows/azure-deploy.yml
```

> Esta estrutura deve ficar na RAIZ da aplicação

Durante o deploy, o GitHub vai procurar por arquivos .yml nesta pasta.

### Iniciando o Workflow

Um Worflow é definido em basicamente duas partes, <code>on</code> e <code>jobs</code>, sendo o primeiro para definição de **quando** os itens serão executados e o segundo **quais** itens serão executados.

Nossa estrutura inicial fica assim.

```yaml
name: Deploy para o azure

on:

jobs:
```

A propriedade <code>name</code> especifica o nome do Workflow, seguido pelas propriedade que comentamos anteriormente.

> Éimportante frisar que o espaçamento no arquivo YAML importa. Então mantenha a mesma identação apresentada aqui.

Nesta aplicação, queremos realizar o deploy SOMENTE quando o código for modificado na branch MASTER. Qualquer mudança em desenvolvimento, stagging ou outro ambiente, não vai gerar um deploy.

```yaml
name: Deploy para o azure

on:
  push:
    branches:
      - master
```

Como podemos notar, fica bem intuitivo este disparo das ações. Definimos que nos pushes para branch master, daremos prosseguimento ao fluxo.

### Definindo a máquina

O primeiro passo é informar qual sistema operacional definições de máquina nossa aplicação está utilizando, e isto é feito pelo atributo <code>runs-on</code>.

Para este exemplo também, teremos apenas um job, chamado <code>build-and-deploy</code> que fará o build e publicação do código.

```yaml
name: Deploy para o azure

on:
  push:
    branches:
      - master

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
```

O próximo passo é definir quais passos serão executados para publicar a aplicação.

#### Obtendo o código

Quando criamos a máquina (Ubuntu) no passo anterior, ela vem apenas com o básico do sistema operacional, ou seja, precisamos instalar o .NET, baixar nossos fontes e tudo mais.

O primeiro passo no caso é obter o código que está no GitHub e aqui entra a magia dos GitHub Actions.

Como você pode imaginar, diversas pessoas ao redor do mundo realizam deploy de aplicações ASP.NET Core no Azure todos os dias, nós não somos diferentes.

Os GitHub Actions são conjuntos de comandos públicos que podemos reutilizar em nossos Workflows através da palavra <code>uses</code>.

Para obter o código da branch master do nosso repositório por exemplo, pode ser realizado pela [action checkout@master](https://github.com/actions/checkout), que é pública no GitHub.

Em resumo, diversos passos que precisaríamos escrever, temos pronto, podendo apenas ser reutilizado.

```yaml
name: Deploy para o azure

on:
  push:
    branches:
      - master

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@master
```

Utilizamos então o <code>uses</code> para definir nosso primeiro passo (Steps) que fará o download do nosso código do GitHub.

#### Instalação do .NET

Nosso próximo passo é instalar o .NET, e novamente vamos utilizar uma action pronta. Em adicional temos a versão do .NET sendo especificada com o <code>with</code>.

```yaml
name: Deploy para o azure

on:
  push:
    branches:
      - master

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@master

      - name: Set up .NET Core
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: "3.1.102"
```

#### .NET Build

Para compilar nossa aplicação não precisamos de nenhuma action adicional, visto que já temos o .NET instalado (Passo anterior). Então precisamos apenas executar o comando <code>dotnet build</code> como mostrado abaixo.

```yaml
name: Deploy para o azure

on:
  push:
    branches:
      - master

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@master

      - name: Set up .NET Core
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: "3.1.102"

      - name: Build with dotnet
        run: dotnet build --configuration Release
```

> Note que executamos o dotnet build com a flag --configuration Release. Caso contrário sua aplicação será compilada em modo Debug.

#### .NET Publish

Para publicar a aplicação, podemos executar o <code>dotnet publish</code>, que assim como o passo anterior, não requer nenhuma action adicional.

```yaml
name: Deploy para o azure

on:
  push:
    branches:
      - master

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@master

      - name: Set up .NET Core
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: "3.1.102"

      - name: Build with dotnet
        run: dotnet build --configuration Release

      - name: dotnet publish
        run: dotnet publish -c Release -o ${{env.DOTNET_ROOT}}/myapp
```

#### Autenticando

Com tudo pronto, precisamos agora publicar nossa aplicação no Azure e para isto precisamos nos autenticar primeiro. Neste passo utilizaremos as credenciais armazenadas nos Secrets do repositório.

```yaml
name: Deploy para o azure

on:
  push:
    branches:
      - master

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@master

      - name: Set up .NET Core
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: "3.1.102"

      - name: Build with dotnet
        run: dotnet build --configuration Release

      - name: dotnet publish
        run: dotnet publish -c Release -o ${{env.DOTNET_ROOT}}/myapp

      - name: Azure Login
        uses: azure/login@v1.1
        with:
          creds: ${{ secrets.AZURE_DEPLOY }}
```

Qualquer segredo armazenado no seu repositório fica encriptado e pode ser acessado por <code>\${{ secrets.CHAVE }}</code> em seu YAML.

#### Deploy

Nosso último passo é realizar o deploy e com isto fechamos nosso Workflow. Está tudo pronto para realizar a integração.

```yaml
name: Deploy para o azure

on:
  push:
    branches:
      - master

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@master

      - name: Set up .NET Core
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: "3.1.102"

      - name: Build with dotnet
        run: dotnet build --configuration Release

      - name: dotnet publish
        run: dotnet publish -c Release -o ${{env.DOTNET_ROOT}}/myapp

      - name: Azure Login
        uses: azure/login@v1.1
        with:
          creds: ${{ secrets.AZURE_DEPLOY }}

      - name: Deploy to Azure Web App
        uses: azure/webapps-deploy@v1
        with:
          app-name: "aspnetghcdeploy"
          slot-name: "production"
          package: ${{env.DOTNET_ROOT}}/myapp
```

### Enviando o Workflow

Até o momento, este Workflow está local. Vamos realizar o envio dele para branch master com os comandos abaixo, ou visualmente pelo **Visual Studio Code** (<kbd>CTRL</kbd>+<kbd>SHIFT</kbd>+<kbd>D</kbd>).

```
git add --all
git commit -m "Primeiro teste"
git push -u origin master
```

## Integrando o Azure o GitHub

O último passo que precisamos realizar é dizer ao Azure que o deploy desta aplicação será feito pelo GitHub, e isto é feito pelo **Deployment Source**, utilizando o comando abaixo.

```
az webapp deployment source config --name aspnetghcdeploy --resource-group aspnetghcdeploy --repo-url https://github.com/andrebaltieri/aspnetghcdeploy --branch master
```

Neste momento, qualquer informação que você enviar para a branch master (<code>git push -u origin master</code>) será automaticamente publicado no Azure.

Você pode acompanhar AO VIVO tudo que está acontecendo durante o deploy na aba ACTIONS do seu repositório, que fica na URL [https://github.com/USUARIO/REPOSITORIO/actions](https://github.com/USUARIO/REPOSITORIO/actions).

Não esqueça de conferir o vídeo no começo deste artigo para saber mais sobre GitHub, ASP.NET, Azure e variáveis de ambiente.
