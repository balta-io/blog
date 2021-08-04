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

Nosso primeiro passo então é obter a imagem do SQL Server que será o molde para criarmos nossos containers. Para isto, executamos o comando abaixo.

```
docker pull mcr.microsoft.com/mssql/server
```

Note que a primeira mensagem será <code>Using default tag: latest</code> o que significa que estamos obtendo a última versão desta imagem, provavelmente com a última versão estável do Mongo.

Caso queira baixar alguma versão específica, verifique as [tags disponíveis aqui](https://hub.docker.com/_/microsoft-mssql-server).

## Rodando o SQL Server

Para executar esta imagem você pode usar a linha abaixo. Não se esqueça de mudar o <code>MSSQL_SA_PASSWORD</code> para a senha desejado.

```
docker run --name sqlserver -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=1q2w3e4r@#$" -p 1433:1433 -d mcr.microsoft.com/mssql/server
```

### Windows

Caso esteja no Windows, com **WSL 2** é importante informar o volume onde este container será executado, utilizando a flag <code>-v ~/docker</code> como mostrado abaixo.

```
docker run -v ~/docker --name sqlserver -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=1q2w3e4r@#$" -p 1433:1433 -d mcr.microsoft.com/mssql/server
```

Para parar a execução você pode pressionar <kbd>CTRL</kbd> + <kbd>C</kbd> no Terminal. Deste momento em diante, seu container vai aparecer na **Dashboard** do Docker de forma visual, onde você poderá parar ou iniciar ela a qualquer momento.

## Connection String

Se você utilizou as mesmas configurações deste artigo, sua **Connection String** será igual abaixo. Caso necessário, modifique as informações que alterou na execução dos containers.

```
Server=localhost,1433;Database=balta;User ID=sa;Password=1q2w3e4r@#$
```

### GUI Client

Caso queira gerenciar seu banco de uma forma visual, você pode utilizar uma das ferramentas gratuitas abaixo:

 * [SQL Server Management Studio](https://docs.microsoft.com/pt-br/sql/ssms/download-sql-server-management-studio-ssms)
 * [Azure Data Studio](https://docs.microsoft.com/pt-br/sql/azure-data-studio/download-azure-data-studio)