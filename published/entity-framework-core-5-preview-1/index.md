Recentemente tivemos o lançamento do Preview 1 do Entity Framework Core 5, que talvez seja uma das versões mais esperadas do Framework. Neste artigo vamos ver como utilizar esta versão e quais as primeiras novidades.

Antes de começar
----------------

Esta versão trata-se de um PREVIEW, ou seja, contém funcionalidades incompletas ou que serão drasticamente alteradas até sua versão final.

Desta forma, não recomendamos seu uso em produção ainda.

Instalando o .NET Core 5 Preview 1
----------------------------------

Para utilizar o Entity Framework Core 5 Preview 1 você precisa primeiro realizar a instalação da versão nova do .NET Core.

Para isto, siga o [processo de instalação do site oficial](https://dotnet.microsoft.com/download/dotnet-core/5.0). Lembre-se que precisamos da SDK e não somente do Runtime.

Feito isto, você pode usar o comando **dotnet --list-sdks** para visualizar todas as SDKs instaladas em sua máquina:

    dotnet --list-sdks  
    2.2.207 [C:\Program Files\dotnet\sdk\]  
    3.1.102 [C:\Program Files\dotnet\sdk\]  
    5.0.100-preview.1.20155.7 [C:\Program Files\dotnet\sdk\]

E para saber a versão da SDK rodando, basta executar o comando **dotnet --version**:

    dotnet --version  
    5.0.100-preview.1.20155.7

Esta versão fica armazenada no **global.json**, e caso você precise voltar para uma versão anterior, você pode remover a versão atual ou executar o seguinte comando:

    dotnet new globaljson --sdk-version 3.1.102 --force

Desta forma um novo arquivo **global.json** será gerado e sobrescreverá o anterior, com a versão especificada por você no comando.

Instalando o Entity Framework Core 5 - Preview 1
------------------------------------------------

Para estes testes, criei apenas um Console Application, executando o comando:

    dotnet new console -o EfCinco

Em seguida, podemos adicionar os pacotes do Entity Framework Core 5 utilizando os comandos:

    dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 5.0.0-preview.2.20120.8  
    dotnet add package Microsoft.EntityFrameworkCore.Design --version 5.0.0-preview.2.20120.8

O primeiro pacote dá suporte ao SQL Server, mas você pode utilizar o InMemory no lugar dele também, sem problemas.

O segundo adiciona suporte as migrações, que não é necessário caso esteja usando o banco em memória.

Note que todos os pacotes seguem o mesmo número de versão. Isto valerá para todos pacotes relacionados que você possuir.

Instalando .NET Tools - EF
--------------------------

Outra ferramenta que utilizamos com frequência é o EF Tools, que é instalado pelo comando dotnet ef, como nas migrações por exemplo.

Desta forma, vamos remover o EF Tools anterior e instalar o Preview. Você pode repetir este processo caso queira deixar de utilizar a versão Preview posteriormente.

    dotnet tool uninstall --global dotnet-ef  
    dotnet tool install --global dotnet-ef --version 5.0.0-preview.2.20120.8

Pronto, isto é tudo que precisamos para trabalhar com EF Core 5 - Preview 1. Agora vamos as novidades.

Suporte a Log facilitado
------------------------

A primeira feature anunciada foi um suporte melhor e mais fácil ao log de informações que estão acontecendo na aplicação envolvendo o EF.

protected override void OnConfiguring(DbContextOptionsBuilder opt) => opt.LogTo(Console.WriteLine);

Utilizando esta opção, teremos as informações sendo exibidas no terminal, mas cuidado para não exibir informações sensíveis aí.

Ainda existem outras sobrecargas de métodos que permitem configurar quais e quanto de informações serão exibidas, bem como aplicar filtros a elas.

Visualizar a Query gerada
-------------------------

Para quem nunca confia na query que o EF gera, agora podemos executar um ToQueryString nas queries e simplesmente ver qual comando SQL será executado no banco.

    var query = context.Set<customer>().Where(c => c.City == city);  
    Console.WriteLine(query.ToQueryString())

Tabelas sem chave
-----------------

Se você já utilizou EF alguma vez, principalmente com uma boa OOP, você sofreu na parte de mapear objetos de valor por exemplo, que não possuem uma chave.

O atributo Keyless chega justamente para definir que um objeto não tem uma chave primaria.

    [Keyless]  
    public class Address  
    {  
        public string Street { get; set; }  
        public string City { get; set; }  
        public int Zip { get; set; }  
    }

Alterar Connection String dinamicamente
---------------------------------------

Agora é possível alterar dinamicamente a Connection String de um Data Context, porém ainda não saiu nenhum material oficial com exemplos para este cenário.

Checagem de enumeradores
------------------------

Durante a execução da migração, o EF Core será capaz de checar enumeradores, para garantir que nenhum valor incluso está fora de seu range.

    EnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN('Useful', 'Useless', 'Unknown'))

Checagem de Banco
-----------------

Agora podemos utilizar os comandos **IsRelational**, **IsSqlServer**, **IsSqlLite** e **IsInMemory** para checar qual banco estamos utilizando.

Esta é uma funcionalidade bem útil para quando você quer executar determinada ação apenas "em memória" ou apenas em um banco local com SQLite por exemplo.

Outras novidades
----------------

Você pode conferir todas as novidades no [post oficial de anúncio do EF Core 5 Preview 1](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-5-0-preview-1/) e muitas outras novidades estão a caminho.

Vamos aguardar mais demos e principalmente o esperado NxN!

<div role="main" id="blog-s1-dotnet-134e3db1eea6c9829db1"></div>
<script type="text/javascript" src="https://d335luupugsy2.cloudfront.net/js/rdstation-forms/stable/rdstation-forms.min.js"></script>
<script type="text/javascript"> new RDStationForms('blog-s1-dotnet-134e3db1eea6c9829db1', 'UA-48664517-12').createForm();</script>