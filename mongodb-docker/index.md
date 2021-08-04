Neste artigo vamos aprender a utilizar o MongoDb via Docker, dispensando a necessidade de um serviço sempre em execução na sua máquina de desenvolvimento.

## Requisitos

### Terminal
Se estiver no Mac ou Linux, provavelmente já tem um Terminal bacana, mas caso esteja utilizando o Windows, sugiro fortemente que utilize o **Windows Terminal** para estas ações.

Aqui tem um link com a instalação e configurações que uso:
[https://balta.io/blog/windows-terminal](https://balta.io/blog/windows-terminal)

## Docker

Você precisa também do **Docker** instalado e rodando em sua máquina. Caso ainda não tenha realizado esta instalação, aqui está um artigo que mostro como fazer: [https://balta.io/blog/docker-instalacao-configuracao-e-primeiros-passos](https://balta.io/blog/docker-instalacao-configuracao-e-primeiros-passos)

### WSL 2 (Somente Windows)
Caso esteja no Windows, recomendo fortemente que instale e utilize o **WSL** que é um subsistema Linux dentro do Windows. 

Este processo tem que ser feito **antes** da instalação do Docker.
Basta acessar este link e realizar a instalação: [https://balta.io/blog/wsl](https://balta.io/blog/wsl)

## Obtendo a imagem

Certifique-se que o Docker está em execução e abra um novo terminal, no meu caso, a versão do Docker em execução é a mostrada abaixo. Você pode verificar isto executando o comando <code>docker --version</code> no seu terminal.

```
Docker version 19.03.12, build 48a66213fe
```

Nosso primeiro passo então é obter a imagem do Mongo que será o molde para criarmos nossos containers. Para isto, executamos o comando abaixo.

```
docker pull mongo
```

Note que a primeira mensagem será <code>Using default tag: latest</code> o que significa que estamos obtendo a última versão desta imagem, provavelmente com a última versão estável do Mongo.

Caso queira baixar alguma versão específica, verifique as [tags disponíveis aqui](https://hub.docker.com/_/mongo?tab=tags).

## Rodando o Mongo

Para executar esta imagem você pode usar a linha abaixo. Não se esqueça de mudar o <code>MONGO_INITDB_ROOT_USERNAME</code> e <code>MONGO_INITDB_ROOT_PASSWORD</code> para o usuário e senha desejado.

```
docker run --name mongodb -p 27017:27017 -e MONGO_INITDB_ROOT_USERNAME=balta -e MONGO_INITDB_ROOT_PASSWORD=e296cd9f mongo
```

### Windows

Caso esteja no Windows, com **WSL 2** é importante informar o volume onde este container será executado, utilizando a flag <code>-v ~/docker</code> como mostrado abaixo.

```
docker run -v ~/docker --name mongodb -p 27017:27017 -e MONGO_INITDB_ROOT_USERNAME=balta -e MONGO_INITDB_ROOT_PASSWORD=e296cd9f mongo
```

Para parar a execução você pode pressionar <kbd>CTRL</kbd> + <kbd>C</kbd> no Terminal. Deste momento em diante, seu container vai aparecer na **Dashboard** do Docker de forma visual, onde você poderá parar ou iniciar ela a qualquer momento.

## Connection String

Se você utilizou as mesmas configurações deste artigo, sua **Connection String** será igual abaixo. Caso necessário, modifique as informações que alterou na execução dos containers.

```
mongodb://balta:e296cd9f@localhost:27017/admin
```

### GUI Client

Caso queira gerenciar seu banco de uma forma visual, você pode utilizar uma das ferramentas gratuitas abaixo:

 * [MongoDb Compass](https://www.mongodb.com/try/download/compass)

 ## Erros comuns

 ### Authentication Failed
 Alguns casos podem apresentar erro de autenticação, principalmente em bancos que não sejam o admin.

 Para adicionar um novo usuário ao Mongo, execute os passos abaixo substituindo o `NOME_DO_BANCO` pelo nome do banco que deseja criar o acesso.

 Estes passos precisam ser executados em um terminal com acesso ao Docker, assim como nos passos anteriores.

 ```
docker exec mongo

db.createUser(
{
    user: "balta",
    pwd: "baltaio",
    roles: [
      { role: "readWrite", db: "NOME_DO_BANCO" }
    ]
})
```
