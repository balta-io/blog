Sumário

 1. [Introdução](#introducao)
 2. [Conexão padrão](#conexao-padrao)
 3. [Conexão confiável](#conexao-confiavel)
 4. [Conexao a uma instância](#conexao-a-uma-instancia)
 5. [Utilizando uma porta diferente](#utilizando-uma-porta-diferente)
 6. [Conexão via endereço IP (Internet protocol)](#conexao-via-endereco-ip)
 7. [Conclusão](#conclusao)
 8. [Referências](#referencias)

<div id='introducao'></div>

# Introdução

As strings de conexão ou cadeia de caracteres de conexao especificam informações necessárias para se conectar à um banco de dados. Elas podem conter informações de autenticação, direcionamento e especificações de segurança.

Neste artigo o tema abordado são as strings de conexão com banco de dados. Entendendo a utilidade e quando utilizar cada parâmetro apresentado da forma correta. Para manter a segurança necessária neste processo e evitar erros.

<div id='conexao-padrao'></div>

# Conexão padrão

A conexão padrão é composta por propriedades básicas que indicam o servidor, o título do banco ao qual o programa deve se conectar e informações de autenticação (usuário e senha). Veja abaixo:

```console
Server=myServerAddress;Database=myDataBase;User Id=myUsername;Password=myPassword;
```

Acima podemos ver a estrutura de uma conexão padrão com as propriedades exibidas na ordem em que foram descritas. Esta estrutura é descrita como padrão por ser o suficiente para a maior parte dos projetos. Porém a mesma definição de padrão pode variar de acordo com a tecnologia utilizada para desenvolvimento ou serviço de dados.

A propriedade `Server`: É definida aqui através do domínio ao qual o servidor do banco de dados pode estar relacionado assim como pode ser um endereço ip ou nome de uma instância local (veremos mais a frente).

A propriedade `Database`: Esta propriedade deve receber o nome do banco de dados.

A propriedade `User Id`: também conhecida como nome do usuário ou username deve ser usada para identificar quem ou qual usuário está tentando se conectar através desta conexão.

A propriedade `Password`: Deve ser preenchida com a palavra-passe ou senha definida para o mesmo usuário que foi definido na propriedade User Id.

<div id='conexao-confiavel'></div>

# Conexão confiável

Neste modo de conexão o SQL Server autentica o usuário através de um token (referente à entidade do windows). Este formato é o formato padrão do SQL Server e considerado mais seguro que a autenticação do SQL Server (usuário e senha).

Uma conexão confiável pode ser estabelecida adicionando o parâmetro `Trusted_Connection`: Ao adicionar este parâmetro você está permitindo que os usuários sejam autenticados através das credenciais do windows.

```console
Server=myServerAddress;Database=myDataBase;Trusted_Connection=True;
```

Utilizando este recurso é possível atribuir permissões a grupos de usuários do sistema que podem ser criados e configurados através do próprio windows (no caso do MSSQL Server). E posteriormente estes terem acesso ao servidor de dados.

<div id='conexao-a-uma-instancia'></div>

# Conexão a uma instância

Conectar à uma instância nada mais é que especificar através do nome a instalação do banco de dados que você deseja utilizar. Podemos ter várias versões do mesmo banco em uma única máquina e ao conectar sem declarar este nome, estamos nos conectando à instância padrão.

Ao adicionarmos a propriedade `Server`: Podemos além de declarar o nome ou endereço do servidor, a instância que desejamos nos conectar. Veja no exemplo abaixo:

```console
Server=myServerName\myInstanceName;Database=myDataBase;User Id=myUsername;Password=myPassword;
```

<div id='utilizando-uma-porta-diferente'></div>

# Utilizando uma porta diferente

Partindo da string de conexão padrão existem casos onde o servidor do nosso banco de dados está configurado para permitir conexões a partir de uma porta "não padrão". No caso do SQL Server esta porta é atualmente a porta 1433.

Mas para apontar uma porta iremos utilizar poucas modificações na string de conexão padrão. Sendo necessário adicionar apenas uma vírgula após o nome do Servidor e logo em seguida a porta que desejamos. Veja o exemplo:

```console
Server=myServerAddress, 1433;Database=myDataBase;User Id=myUsername;Password=myPassword;
```

<div id='conexao-via-endereco-ip'></div>

# Conexão via endereço de IP (Internet Protocol)

Nas conexões deste tipo não utilizamos a propriedade Server e sim `Data Source` para especificar onde está nosso banco de dados. Como valor este campo pode receber o endereço IP do servidor e por padrão terá a porta 1433. Caso a porta configurada seja outra podemos configurar conforme vimos no tópico acima.

A propriedade `Network Library`: Especifica o valor da conexão com o banco de dados. Podendo ser: DBNMPNTW (Pipes nomeados), DBMSRPCN (Multiprotocol/RC), DBMSVINN (Banyan vines), DBMSSPXN (IPX/SPX) e DBMSSOCN (TCP/IP) que é o padrão.

Na propriedade `Initial Catalog`: Devemos especificar o nome do banco de dados e os demais campos seguem como padrão. Veja o exemplo:

```console
Data Source=190.190.200.100,1433;Network Library=DBMSSOCN;Initial Catalog=myDataBase;User ID=myUsername;Password=myPassword;
```

<div id='conclusao'></div>

# Conclusão

Este artigo desenvolveu definições para casos frequêntes do uso de strings de conexões. Veja que tratamos das propriedades mais comuns. Existem algumas outras propriedades que especificam atributos como criptografia e tempo de conexão.

Se você está utilizando este artigo como material de estudo sugiro que instale uma instância de banco de dados e configure de forma a testar um conjunto de configurações especificas para cada instância. Então poderá fazer comparações relativas ao resultado de cada conjunto.

<div id='referencias'></div>

# Referências

[Strings de conexão | SQL SERVER](https://www.connectionstrings.com/sql-server/)

[SqlConnection.ConnectionString | Documentação da Microsoft](https://docs.microsoft.com/pt-br/dotnet/api/system.data.sqlclient.sqlconnection.connectionstring?view=dotnet-plat-ext-6.0)
