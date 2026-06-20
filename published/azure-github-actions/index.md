Neste artigo vamos aprender a criar e configurar aplicações no Azure integradas com GitHub Actions utilizando apenas linhas de comando.

## Command Line Interface
Trabalhar pelo terminal, via linha de comandos, traz diversos benefícios, dentre eles a integração de forma simples e direta.

Em um mundo tão focado em **DevOps** é mais do que essencial saber todos os passos de uma publicação completa, seja da sua API ou pacote, então vamos mergulhar no **CLI**.

Publicar uma aplicação pelo portal do **Azure**, integrado ao **GitHub Actions** não é uma tarefa difícil, porém, fazer isto via linha de comando por exigir um esforço maior.

Como contrapartida, por serem comandos, podemos automatizar com muito mais facilidade, tornando o processo independente dos desenvolvedores(as) nas próximas execuções.

### Instalações
Para realizar os passos a seguir você precisará instalar o CLI do Azure e GitHub, como mostrado nas documentações dos links abaixo:

* [Azure CLI](https://docs.microsoft.com/pt-br/cli/azure/install-azure-cli)
* [GitHub CLI](https://cli.github.com/)

Ao término da instalação, você deve ter disponível no seu terminal os comando `az` e `gh`.

## Azure CLI - Autenticando
O primeiro passo é se autenticar no Azure utilizando o comando `az login`. Este comando abrirá um browser para que você possa fazer um login seguro e em seguida podemos prosseguir.

```
az login
```

## Azure CLI - Listando as Contas
É normal termos diversas contas no Azure, e você pode listá-las utilizando o comando `az account list`. Em adicional a flag `-o table` faz com que o retorno da função seja uma tabela e não um **JSON**.

```
az account list -o table
```

Para definir a conta que deseja utilizar basta executar o comando `az account set`, seguido pelo **ID** da subscription.

```
az account set --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx
```
Para exibir detalhes de uma conta podemos utilizar o comando `az account show`.

```
az account show
```

## Azure CLI - Criando um Resource Group
O primeiro passo no Azure é criar um grupo de recursos, que funciona como uma espécie de rede e permite-nos agrupar aplicações, storages, bancos de dados, entre outros.

Para criar um grupo, utilizamos o comando `az group create`, definindo a localização do mesmo com o flag `--location`, que neste caso foi **brazilsouth**, seguido pelo nome do mesmo.
```
az group create --location brazilsouth --name blog 
```
Para listar os grupos podemos utilizar o comando `az group list` e para excluir um grupo o `az group delete`.
```
az group list
az group delete --name blog
```

## Azure CLI - Criando um Storage
Para criar um storage podemos utilizar o comando `az storage account`. Neste caso o flag `-h` mostra detalhes da ajuda sobre o comando. Este flag pode ser utilizado por qualquer comando.
```
az storage account -h
```
Para listar as contas de armazenamento, podemos utilizar o comando `az storage account list`.
```
az storage account list
```
Agora vamos criar uma conta de armazenamento utilizando o comando `az storage account create`, como mostrado abaixo.

```
az storage account create --name blogstorage2812 --resource-group blog -l brazilsouth --sku Standard_LRS
```
Neste exemplo, utilizamos o **SKU**, que define a faixa de preço do serviço. Para mais detalhes sobre os SKUs, [consulte este link](https://docs.microsoft.com/pt-br/rest/api/storagerp/srp_sku_types).

Para exibir a string de conexão de uma conta de armazenamento, utilizamos o comando `show-connection-string`.
```
az storage account show-connection-string --name blogstorage2812 --resource-group blog
```

## Azure CLI - Criando um Service Plan
Para criar uma aplicação, precisamos de um plano de cobranças antes, e isto é definido no comando `az appservice plan`.
```
az appservice plan -h
```

Para criar um plano, podemos utilizar o `az appservice plan create` como mostrado abaixo.
```
az appservice plan create --name blogbalta2812 --resource-group blog --sku F1 --is-linux
```
Novamente utilizamos o SKU para definir a tier de preços e o flag `--is-linux` define que nosso ambiente será **Linux**.

> SKUs disponíveis: B1, B2, B3, D1, F1, FREE, I1, I1v2, I2, I2v2, I3, I3v2, P1V2, P1V3, P2V2, P2V3, P3V2, P3V3, PC2, PC3, PC4, S1, S2, S3, SHARED, WS1, WS2, WS3

## Azure CLI - Criando um WebApp
Os WebApps ficam no comando `az webapp`, e podemos listá-los utilizando o comando abaixo.
```
az webapp list -o table
```

Para criar um **WebApp** podemos definir seu Runtime (Java, .NET, PHP, Node...) e para saber quais runtimes disponíveis no seu ambiente você pode utilizar o comando abaixo.
```
az webapp list-runtimes
```
Finalmente, para criar o webapp podemos utilizar o comando abaixo.
```
az webapp create --name blogbalta2812webapp --plan blogbalta2812 --resource-group blog --runtime "DOTNETCORE:6.0"
```

Para abrir seu app no Browser, basta executar o comando abaixo:
```
az webapp browse -n blogbalta2812webapp --resource-group blog
```

## GitHub CLI
O GitHub também possui um [**CLI**](https://cli.github.com/) que ao ser instalado disponibiliza o comando `gh` no terminal. Não devemos confundir com o CLI do **GIT** que disponibiliza o comando `git` no terminal.

## GitHub CLI - Autenticando
Para se autenticar utilizando o GitHub CLI basta utilizar o comando abaixo:
```
gh auth login
```

## GitHub CLI - Criando um Repositório
Para criar um repositório podemos utilizar o comando `gh repo create`, como mostrado abaixo. O flag `--private` torna o repositório privado.
```
gh repo create balta-blog-2812 --private
```

## Azure CLI - Criando um Perfil de Publicação
Para publicar nossa aplicação via GitHub Actions, precisamos de um perfil de publicação no Azure, e isto pode ser feito através do comando abaixo.
```
az ad sp create-for-rbac --name blogbalta2812deploy --role contributor --scopes /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/blog/providers/Microsoft.Web/sites/blogbalta2812webapp --sdk-auth
```
Como resultado, temos um JSON que devemos salvar na sessão **Secrets** do repositório que criamos previamente no GitHub.
```json
{
  "clientId": "00000000-0000-0000-0000-00000000000",
  "clientSecret": "SECRET",
  "subscriptionId": "00000000-0000-0000-0000-00000000000",
  "tenantId": "00000000-0000-0000-0000-00000000000",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

## GitHub CLI - Secrets
Caso queira, você listar e salvar os **Secrets** via linha de comando também, mas como este segredo é um JSON, recomendo fazer pelo site. De qualquer forma aqui estão os comandos para fazer isto via CLI.
```
gh secret list
gh secret set AZURE_DEPLOY --body "JSON"
```

## GitHub Actions - Workflows
Com tudo pronto, podemos criar um arquivo **yml** que será nosso worflow, neste caso é o `.github/worflows/main.yml`.
```yml
name: Build and deploy ASP.Net Core app to Azure Web App - balta-blog-2812

on:
  push:
    branches:
      - main
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v2

      - name: Set up .NET Core
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: '6.0.x'
          include-prerelease: true

      - name: Build with dotnet
        run: dotnet build --configuration Release

      - name: dotnet publish
        run: dotnet publish -c Release -o ${{env.DOTNET_ROOT}}/myapp

      - name: Upload artifact for deployment job
        uses: actions/upload-artifact@v2
        with:
          name: .net-app
          path: ${{env.DOTNET_ROOT}}/myapp
      
      - name: Azure Login
        uses: azure/login@v1.1
        with:
          creds: ${{ secrets.AZURE_DEPLOY }}

      - name: Deploy to Azure Web App
        uses: azure/webapps-deploy@v2
        with:
          app-name: "blogbalta2812webapp"
          slot-name: "production"
          package: ${{env.DOTNET_ROOT}}/myapp
```

Note que utilizamos o segredo armazenado, que neste caso chamei de **secrets.AZURE_DEPLOY**.

## Azure CLI - Configurando o Deploy
Agora basta apontar ao Azure que estamos utilizando o deployment a partir de um repositório no GitHub.
```
az webapp deployment source config --name blogbalta2812webapp --resource-group blog --repo-url https://github.com/andrebaltieri/balta-blog-2812 --branch main
```

## Azure CLI - SQL Server
Também podemos criar bancos de dados e diversos outros recursos no Azure via CLI, como mostrados abaixo:
```
az sql server list
az sql server create -l brazilsouth --resource-group blog --name blogbalta2812sqlserver -u blogsqlserveradmusr -p SUASENHA
```

Porém, recomendo fortemente que faça o SQL pelo portal por conta dos valores. Eles mudam com frequência e até onde percebi nem todas as opções estão disponíveis via CLI.

## Conclusão
Publicar aplicações no Azure de forma automatizada com GitHub sem nem acessar o portal é plenamente possível, e desta forma você pode deixar tudo automatizado. Chega de sofrer com deploy!