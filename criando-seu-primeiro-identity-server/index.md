No artigo [anterior](https://balta.io/artigos/identity-server) demos uma olhada no **IdentityServer** e entendemos seu propósito e fundamentos.

Neste artigo vamos dar nossos primeiros passos no Identity Server e criar nosso primeiro servidor de identidade.

## Índice
* [Identity Server](https://balta.io/artigos/identity-server)
* **Criando seu primeiro Identity Server**

## Templates

Embora você possa criar uma aplicação vazia e depois adicionar os pacotes necessários para o Identity Server funcionar, é muito mais fácil utilizar um template já pronto.

> **IMPORTANTE:** Normalmente mantemos o nosso servidor de identidade em um projeto separado, então não se preocupe tanto com a estrutura do projeto aqui.

Para instalar os templates oficiais do Identity Server, você deve executar a seguinte linha de comando.

```
dotnet new -i IdentityServer4.Templates
```

## Criando um Identity Server

Existem vários templates disponíveis e vamos ver alguns aqui, começando pelo mais básico, que particularmente uso bastante.

Desta forma, após instalar os templates, podemos executar a criação de um servidor de identidade utilizando o comando abaixo.

```
dotnet new is4empty -n MeuIsServer
```

Este processo vai criar um Web App que roda na porta <code>5001</code> e caso precise mudar esta opção, é só alterar o arquivo <code>Properties\launchSettings.json</code>.

## Executando o Identity Server

Com a aplicação criada, podemos executá-la, pois nosso servidor já está rodando e pronto para ser configurado.

```
cd MeuIsServer
dotnet watch run
```

Para verificar se o servidor está rodando adequadamente, basta acessar a URL <code>https://localhost:5001/.well-known/openid-configuration</code>.

## Conclusão

Prontinho, em minutos você tem um servidor de identidade pronto para ser configurado e distribuído.

Nos próximos artigos veremos como configurar e conectar nossos Apps e APIs neste servidor.