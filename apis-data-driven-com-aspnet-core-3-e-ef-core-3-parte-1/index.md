Houve uma cena na minha carreira que nunca esqueço, foi em 2012 quando estava apresentando algumas novidades do ASP.NET Web Api durante o TDC Floripa, e no fim da minha palestra um dos espectadores veio me falar:  
  
“Achei bem legal isto, mas consigo o mesmo resultado, com metade do código e o dobro da velocidade em Node”  
  
Chegando em casa corri testar, e não era que o tal do Node era bom mesmo! Rapaz, que facilidade criar APIs com ele e que rapidez na execução.  
  
Claro que boa parte da velocidade de desenvolvimento deve-se ao fato da stack ser toda JavaScript, ou seja, não precisa ficar criando objetos para serializar ou deserializar nada.  
  
Eu sou um defensor da OOP, de domínios ricos, mas eu gosto também de pontuar que não existe bala de prata no desenvolvimento de software, ou seja, DDD não atende tudo.  
  
Em determinados momentos, você quer algo Data Driven, ou seja, apenas CRUD básico sobre seu banco, e não vai ficar criando complexidade sobre isto, [senão não terá tempo de focar no seu domínio ou mesmo de testá-lo](https://www.youtube.com/watch?v=uiBAut1VB9k).  
  
Desta forma, neste artigo vou apresentar como podemos criar APIs no formato Data Driven, de forma simples, prática e objetiva, que vai salvar um belo tempo do seu desenvolvimento.

Atualizando para o .NET Core 3.0
--------------------------------

Para realizar os códigos a seguir você precisa instalar o .NET Core 3.0, sendo assim, acesse [https://dotnet.microsoft.com/download](https://dotnet.microsoft.com/download) e faça a instalação antes de prosseguir.

Setup
-----

O primeiro passo é criar uma API, e embora em diversos casos eu utilize o template vazio do ASP.NET Core, em casos como este, onde tempo é o foco, eu opto por utilizar um template pronto, já com tudo configurado.  
  
Para criar nosso projeto, basta executar o comando:  

    dotnet new webapi -o Shop
  
Em seguida é só acessar a pasta criada (_cd Shop_) e executar a instalação do EF Core 3.0.  
  
    dotnet add package Microsoft.EntityFrameworkCore.SqlServer  
    dotnet add package Microsoft.EntityFrameworkCore.InMemory
  
Neste caso utilizaremos um banco de dados em memória para questões de didática. Você pode optar por utilizar o SQL Server local, mas não mostrarei o mapeamento Objeto/Relacional aqui, cobrirei em outro post.  
  
Esta API já vem com um exemplo criado, pode deixá-lo como está ou remover os arquivos Wheather\* da aplicação.

Models
------

Independente de ser Data Driven, começaremos pelo modelo da aplicação, que seriam as referências as tabelas que teríamos no banco de dados.  
  
Neste caso, começaremos pela categoria do produto, que contém apenas o Id e Título, com suas devidas validações.  
  
Para isto, vamos criar uma nova pasta chamada Model, e dentro dela nossa Category.cs, que vai conter o seguinte código.

    using System.Collections.Generic;
    using System.ComponentModel.DataAnnotations;

    namespace Shop.Models
    {
        public class Category
        {
            [Key]
            public int Id { get; set; }

            [Required(ErrorMessage = "Este campo é obrigatório")]
            [MaxLength(60, ErrorMessage = "Este campo deve conter entre 3 e 60 caracteres")]
            [MinLength(3, ErrorMessage = "Este campo deve conter entre 3 e 60 caracteres")]
            public string Title { get; set; }
        }
    }

Note que já estamos usando os Data Annotations, estas anotações em cima das propriedades, que servirão tanto para gerar o banco quanto para aplicar a validação nas nossas requisições.  
  
Se você já acompanhou algum curso meu sobre DDD, sabe que eu não recomendo o uso de Data Annotations em cenários Domain Driven, simplesmente pelas dependências que eles trazem ao domínio, mas neste caso, um cenário Data Driven, não há problema algum, afinal não vamos compartilhar nosso domínio.  
  
Nossa próxima entidade é o Product.cs que fica parecida com a categoria, exceto pelo fato de conter duas propriedades para referenciá-la, o CategoryId e o Category.

using System.ComponentModel.DataAnnotations;

    namespace Shop.Models
    {
        public class Product
        {
            [Key]
            public int Id { get; set; }

            [Required(ErrorMessage = "Este campo é obrigatório")]
            [MaxLength(60, ErrorMessage = "Este campo deve conter entre 3 e 60 caracteres")]
            [MinLength(3, ErrorMessage = "Este campo deve conter entre 3 e 60 caracteres")]
            public string Title { get; set; }

            [MaxLength(1024, ErrorMessage = "Este campo deve conter no máximo 1024 caracteres")]
            public string Description { get; set; }

            [Required(ErrorMessage = "Este campo é obrigatório")]
            [Range(1, int.MaxValue, ErrorMessage = "O preço deve ser maior que zero")]
            public decimal Price { get; set; }

            [Required(ErrorMessage = "Este campo é obrigatório")]
            [Range(1, int.MaxValue, ErrorMessage = "Categoria inválida")]
            public int CategoryId { get; set; }
            public Category Category { get; set; }
        }
    }

Caso você não use outras informações da categoria sem ser o ID, não é necessário a propriedade de referência (Linha 25), mas caso queira por exemplo, ao carregar um produto, carregar também os dados da sua categoria, aí ela é necessária.

### Sumário das Anotações

Você deve estar se perguntando o que é esse monte de anotações sobre as propriedades, e aqui vai um guia rápido delas.

**Key**  
Define que esta propriedade é uma chave.  
  
**Required**  
Define que este campo é requerido.  
  
**MaxLenght**  
Define um tamanho máximo (Em caracteres) para o campo.  
  
**MinLenght**  
Define um tamanho mínimo (Em caracteres) para o campo.  
  
**Range**  
Define um limite mínimo e máximo (Numérico) para o campo.

Data Context
------------

Um **DataContext** é a representação do banco em memória, nele temos acesso aos métodos CREATE, READ, UPDATE, DELETE dentre outras interações com nossa fonte de dados.  
  
Em suma, tudo que precisamos para trabalhar com Entity Framework é este arquivo, nele ficam as configurações de como nossa aplicação vai interagir com o banco de dados.  
  
Um DataContext nada mais é do que uma classe que herda de DataContext e contém as coleções (DbSet) que podemos manipular no banco de dados.  
  
Vamos então criar uma nova pasta na raiz da aplicação, chamada de Data e adicionar um arquivo chamado DataContext.cs com este conteúdo.

    using Microsoft.EntityFrameworkCore;
    using Shop.Models;

    namespace Shop.Data
    {
        public class DataContext : DbContext
        {
            public DataContext(DbContextOptions<DataContext> options)
                : base(options)
            {
            }

            public DbSet<Product> Products { get; set; }
            public DbSet<Category> Categories { get; set; }
        }
    }

Como não vamos utilizar o SQL Server aqui, não precisamos configurar uma **Connection String** ou algo do tipo.  
  
Por fim, precisamos informar a nossa aplicação que estamos utilizando EF Core, que temos um banco de dados em memória e nosso DataContext.  
  
Isto é feito no método ConfigureServices no arquivo Startup.cs, utilizando o método **AddDbContext** do ServiceCollection.  
  
O que este método faz é configurar todo serviço externo a nossa API que utilizamos, como Autenticação, Log e Banco de Dados.  
  
Desta forma, basta chamar o método AddDbContext, informar nosso contexto de dados e nas opções dizer que queremos utilizar um banco de dados em memória.

    using Microsoft.AspNetCore.Builder;
    using Microsoft.AspNetCore.Hosting;
    using Microsoft.EntityFrameworkCore;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Hosting;
    using Shop.Data;

    namespace Shop
    {
        public class Startup
        {
            ...

            public void ConfigureServices(IServiceCollection services)
            {
                services.AddDbContext<DataContext>(opt => opt.UseInMemoryDatabase("Database"));
                services.AddControllers();
            }

            ...
        }
    }

Ao informar que utilizamos um banco em memória, precisamos nomeá-lo, neste caso eu chamei de “Database”, mas poderia ser qualquer nome.

Injeção de Dependência
----------------------

DI ou Dependency Injection não é um assunto básico, mas podemos resumi-lo aqui da seguinte forma.  
  
Ao trabalhar com bancos relacionais, nós não ficamos conectados ao banco o tempo todo, isto porque o banco tem um limite de conexões, e se você esquecer uma conexão aberta por exemplo, terá graves problemas em pouco tempo de uso da sua aplicação.  
  
No modelo Web, nosso Frontend também não fica conectado a nossa API, ou seja, cada informação que o cliente precisa, ele faz uma requisição nova a API, que por sua vez se conecta ao banco, recupera (Ou gera) as informações e retorna para tela no formato JSON.  
  
No .NET temos o using, que garante que um objeto será destruído imediatamente após ser utilizado, porém o using é feito em blocos, se você tem várias chamadas ao banco, significa que ficara conectando e desconectando várias vezes.  
  
Cada conexão que fazemos a API ou ao banco tem um tempo mínimo, pois há um processo para abertura da conexão, estabelecimento de uma conexão segura, transferência de dados, conferência dos dados e fechamento da conexão. Então quanto menos chamarmos nossas APIs ou formos ao banco na API, mais rápido seremos.  
  
Mas se não podemos utilizar o using e também não podemos correr o risco de deixar nossa conexão aberta, o que podemos fazer?  
  
A solução é a seguinte, imagina que a cada requisição a nossa API, um objeto do nosso DataContext seja criado. Inicialmente ele não vai se conectar ao banco, apenas ser criado.  
  
A partir do momento que uma requisição ao banco precisar ser executada, já temos nosso contexto na memória, então chamamos ele, que já está pronto, ele se conecta ao banco e executa a instrução desejada.  
  
Pouco depois, ainda na mesma requisição, precisamos ir ao banco novamente, e advinha? Nosso contexto ainda está lá na memória, com a conexão aberta e pronto para nos atender.  
  
Por fim, ao terminar nossa requisição, um método é chamado para destruir este objeto e fechar possíveis conexões abertas no banco.  
  
Isto é possível utilizando o recurso de injeção de dependências do ASP.NET Core. Tudo que definimos (Depende da forma que fazemos isto) é gerenciado pelo próprio ASP.NET, sem a necessidade (Ou preocupação) da nossa intervenção.  
  
Injetar dependência significa explicitar que dependemos de algo para trabalhar, como por exemplo, para criar um produto, precisamos do nosso DataContext, afinal ele é ponte entre nossa API e o banco de dados.  
  
Desta forma, podemos criar uma dependência do DataContext e dizer “Olha, para cadastrar um produto eu preciso do DataContext... Como você vai me prover ele eu não sei, só sei que preciso dele”.  
  
A nível de código, criamos dependências simplesmente explicitando um objeto no construtor da classe, como por exemplo.

    public class ProductController {
        public ProductController(DataContext context) { ... }
    }

Desta forma criamos uma dependência do DataContext no ProductController, o que é totalmente plausível, afinal sem um contexto não conseguimos fazer nenhuma operação de produto no banco.  
  
Porém, o ASP.NET não sabe como resolver esta dependência. Uma coisa que você vai aprender em OOP é que devemos sempre depender de abstrações (Interfaces) e nunca de implementações – Veja o curso 1975 para maiores informações.  
  
Precisamos então RESOLVER esta dependência que geramos, e isto é feito também no Startup.cs.

    using Microsoft.AspNetCore.Builder;
    using Microsoft.AspNetCore.Hosting;
    using Microsoft.EntityFrameworkCore;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Hosting;
    using Shop.Data;

    namespace Shop
    {
        public class Startup
        {
            ...

            public void ConfigureServices(IServiceCollection services)
            {
                services.AddDbContext<DataContext>(opt => opt.UseInMemoryDatabase("Database"));
                services.AddScoped<DataContext, DataContext>();
                services.AddControllers();
            }

            ...
            
        }
    }

Desta forma (Linha 18) estamos resolvendo a dependência por requisição do nosso contexto. Para saber mais sobre DI, veja este artigo - [https://balta.io/blog/aspnet-core-dependency-injection](https://balta.io/blog/aspnet-core-dependency-injection).

Controllers
-----------

Nosso controlador será responsável por responder as requisições em nossa API, este nome vem do padrão MVC (Models, Views, Controllers), comumente utilizado no ASP.NET.  
  
Um Contoller é uma classe que herda de ControllerBase, decorado com rotas e verbos HTTP. Para criar nosso primeiro Controller, vamos criar uma pasta Controllers na raiz da aplicação e adicionar o arquivo CategoryContoller.cs. O sufixo Controller é sempre adicionado ao nome do arquivo por convenção.

    using Microsoft.AspNetCore.Mvc;
    using Microsoft.EntityFrameworkCore;

    namespace Shop.Controllers
    {
        [ApiController]
        [Route("v1/categories")]
        public class CategoryController : ControllerBase
        {
            ...
        }
    }

A primeira coisa que fazemos é herdar de ControllerBase, seguindo pela adição das anotações **ApiController** e **Route**.  
  
Enquanto o ApiController apenas diz que este Controller é uma API (Lembre-se que o ASP.NET não serve apenas para APIs), o Route define qual URL precisaremos chamar para chegar até este controlador.  
  
Eu tenho o costume de colocar sempre a versão na Rota, isto permite versionar meus endpoints e não sofrer com mudanças no Frontend futuramente.

### Listando as Categorias

Para listar as categorias precisaremos ir ao banco de dados, e desta forma precisaremos do DataContext e como já discutimos na sessão Injeção de Dependência, precisamos criar uma dependência do DataContext no construtor do controlador.

    using Microsoft.AspNetCore.Mvc;
    using Microsoft.EntityFrameworkCore;

    namespace Shop.Controllers
    {
        [ApiController]
        [Route("v1/categories")]
        public class CategoryController : ControllerBase
        {
            private DataContext _context;
            
            public CategoryController(DataContext context) {
            _context = context;
            }
        }
    }

O código ficaria parecido com o acima, porém temos uma forma bem mais simples de fazer isto. Na versão 2.0+ do ASP.NET Core, tivemos a inclusão do **FromService**, que permite injetar um objeto (Desde que ele esteja resolvido) diretamente no método.  
  
Desta forma, nosso método fica neste formato.

    ...

    [HttpGet]
    [Route("")]
    public async Task<ActionResult<List<Category>>> Get([FromServices] DataContext context)
    {
        var categories = await context.Categories.ToListAsync();
        return categories;
    }

    ...

Para ler os dados do banco, basta chamar o contexto, seguido pela coleção. Como estamos utilizando métodos assíncronos, não podemos esquecer do Await.  
  
Note também que decoramos o método com o **HttpGet** e a rota deixamos vazio.  
Para testar este método basta rodar a aplicação, e acessar: https://localhost:5001/v1/categories.

Conclusão
---------

Por enquanto é isto, vimos como criar uma API, trabalhar com EF Core, DBContext e até Dependency Injection. No próximo artigo terminaremos os métodos restantes.