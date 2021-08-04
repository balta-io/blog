Soft Delete com ASP.NET Core e EF Core é uma questão recorrente que recebo de dúvidas, e existem várias formas de resolver este problema.

Neste post vou mostrar uma forma simples e fácil de implementar o Soft Delete utilizando a feature de filtros globais do Entity Framework Core.

O que é Soft Delete?
--------------------

Em determinados cenários, não promovemos a exclusão permanente de um objeto no banco de dados, ao invés disso, utilizamos 'flags' para determinar se o mesmo está ativo ou inativo.

Esta é uma prática comum em muitos cenários, onde criamos por exemplo, um tipo booleano na entidade, que determina se o objeto foi 'excluído' ou não.

Criando a API
-------------

Para executar esta demo, você precisará primeiro do .NET Core 3.1 instalado em sua máquina. Você pode seguir o [guia oficial de instalação](https://dotnet.microsoft.com/download/dotnet-core) para isto.

Caso este seja seu primeiro contato com APIs, recomendo que siga nossa formação [Backend Developer .NET](https://balta.io/carreiras/backend-developer-dotnet), onde você começa de graça e vai aprender os fundamentos do ASP.NET Core.

Com tudo instalado, navegue para uma pasta segura pelo seu terminal favorito e execute o seguinte comando:

    dotnet new webap -o backoffice

O nome **backoffice** você pode alterar se quiser, mas no repositório desta demo, este foi o nome que usei. Em adicional, abra a pasta recém criada com Visual Studio Code.

Criando os Modelos
------------------

Independente se você trabalha com modelos ou entidades, a única coisa que precisamos aqui é deixar explícito uma forma de saber se o item está ou não ativo.

Se você ficar em dúvida sobre Model, Entity e outros pontos de OOP, faça nosso curso [Modelando Domínios Ricos](https://balta.io/cursos/modelando-dominios-ricos).

Neste caso, a forma que encontrei foi criando um _booleano_ chamado **Deleted** no modelo. Pode ser que para seu cenário isto não faça sentido.

De qualquer forma, não importa como você vai tratar isto, é apenas um exemplo, OK? O resultado do arquivo **Models/Product.cs** você pode conferir abaixo.

    namespace backoffice.Models
    {
        public class Product
        {
            public int Id { get; set; }
            public string Title { get; set; }
            public bool Deleted { get; set; }
        }
    }

Data Context
------------

Para trabalhar com EF Core precisamos de um único arquivo, o Data Context, e nele vamos definir quais modelos vamos mapear.

Neste exemplo vou utilizar um banco de dados em memória, já que indifere para nós a fonte onde os dados se encontram.

Desta forma, o arquivo **Data/DataContext.cs** fica com o seguinte conteúdo.

using backoffice.Models;
using Microsoft.EntityFrameworkCore;

    namespace backoffice.Data
    {
        public class DataContext : DbContext
        {
            public DataContext(DbContextOptions<DataContext> options)
                        : base(options)
            {
            }

            public DbSet<Product> Products { get; set; }

            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                modelBuilder.Entity<Product>().Property(x => x.Id);
                modelBuilder.Entity<Product>().Property(x => x.Title);
            }
        }
    }

Para finalizar esta parte, adicione suporte ao DataContext via AddDbContext no método ConfigureServices do Startup.cs (Linha 23).

    using backoffice.Data;
    using Microsoft.AspNetCore.Builder;
    using Microsoft.AspNetCore.Hosting;
    using Microsoft.EntityFrameworkCore;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Hosting;

    namespace backoffice
    {
        public class Startup
        {
            public Startup(IConfiguration configuration)
            {
                Configuration = configuration;
            }

            public IConfiguration Configuration { get; }

            public void ConfigureServices(IServiceCollection services)
            {
                services.AddControllers();
                services.AddDbContext<DataContext>(opt => opt.UseInMemoryDatabase("Database"));
            }

            public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
            {
                if (env.IsDevelopment())
                {
                    app.UseDeveloperExceptionPage();
                }

                app.UseHttpsRedirection();

                app.UseRouting();

                app.UseAuthorization();

                app.UseEndpoints(endpoints =>
                {
                    endpoints.MapControllers();
                });
            }
        }
    }

Pronto, já podemos utilizar nosso contexto nos controladores.

Controllers
-----------

Para este exemplo, preparei um controlador bem simples, com dois métodos, um para criar um novo produto e outro para ler todos os produtos.

    using System.Collections.Generic;
    using backoffice.Data;
    using backoffice.Models;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.EntityFrameworkCore;

    namespace backoffice.Controllers
    {
        [ApiController]
        [Route("products")]
        public class HomeController : ControllerBase
        {

            [HttpGet]
            public IEnumerable<Product> Get([FromServices]DataContext context)
            {
                return context.Products.AsNoTracking();
            }

            [HttpPost]
            public Product Post([FromServices]DataContext context, [FromBody]Product product)
            {
                context.Products.Add(product);
                context.SaveChanges();
                return product;
            }
        }
    }

Testando a API
--------------

Abra um terminal no VS Code e execute o comando **_dotnet watch run_**, nossa API estará rodando na porta 5000/5001.

Vamos então para o [Postman](https://www.postman.com/downloads/), executar os seguintes testes.

*   POST https://localhost:5001/products => {'title':'Produto 1', 'deleted': false}
*   POST https://localhost:5001/products => {'title':'Produto 2', 'deleted': true}
*   POST https://localhost:5001/products => {'title':'Produto 3', 'deleted': false}
*   GET https://localhost:5001/products => Deve retornar os três produtos cadastrados

Soft Delete - Aplicando filtros globais
---------------------------------------

Agora chegou a hora legal. Você deve ter notado que embora tenhamos cadastrado os produtos com **Deleted**, os mesmos ainda estão sendo listados no **GET**.

Isto ocorre pois não estamos utilizando um filtro **WHERE** na query de produtos no **HomeController**. Na verdade não queremos este filtro lá, queremos ele globalmente.

No EF Core 3.x ou superior, podemos utilizar um recurso chamado **HasQuery** para fixar uma query em um objeto, que será anexada sempre que este for utilizado.

Isto é feito no **DataContext** (Linha 19) utilizando o seguinte código.

    using backoffice.Models;
    using Microsoft.EntityFrameworkCore;

    namespace backoffice.Data
    {
        public class DataContext : DbContext
        {
            public DataContext(DbContextOptions<DataContext> options)
                        : base(options)
            {
            }

            public DbSet<Product> Products { get; set; }

            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                modelBuilder.Entity<Product>().Property(x => x.Id);
                modelBuilder.Entity<Product>().Property(x => x.Title);
                modelBuilder.Entity<Product>().HasQueryFilter(p => !p.Deleted);
            }
        }
    }

Como nossa aplicação utiliza dados em memória, esta alteração fará a mesma ser reiniciada, então repita os testes realizados.

Agora como resultado do teste de **GET**, você deve ver apenas produtos que não foram excluídos, mesmo você não tendo escrito nada de diferente no seu controlador.

Soft Delete - Excluindo Filtro
------------------------------

Para finalizar, vamos aprender como podemos desabilitar momentaneamente esta funcionalidade.

Vamos imaginar que você queira em um método específico listar todos os produtos, incluindo os excluídos.

Neste caso, podemos utilizar o **IgnoreQueryFilters** que fará com que todas os filtros globais de um determinado objeto sejam ignorados.

    [HttpGet]
    [Route("all")]
    public IEnumerable<Product> GetAll([FromServices]DataContext context)
    {
        return context.Products.IgnoreQueryFilters().AsNoTracking();
    }

O código final do nosso controlador fica assim.

    using System.Collections.Generic;
    using backoffice.Data;
    using backoffice.Models;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.EntityFrameworkCore;

    namespace backoffice.Controllers
    {
        [ApiController]
        [Route("products")]
        public class HomeController : ControllerBase
        {

            [HttpGet]
            public IEnumerable<Product> Get([FromServices]DataContext context)
            {
                return context.Products.AsNoTracking();
            }

            [HttpPost]
            public Product Post([FromServices]DataContext context, [FromBody]Product product)
            {
                context.Products.Add(product);
                context.SaveChanges();
                return product;
            }

            [HttpGet]
            [Route("all")]
            public IEnumerable<Product> GetAll([FromServices]DataContext context)
            {
                return context.Products.IgnoreQueryFilters().AsNoTracking();
            }
        }
    }

De volta aos testes, agora você pode executar um **GET** no endereço _https://localhost:5001/products/all_ para visualizar todos os produtos novamente, incluindo os excluídos via **Soft Delete**.

Código Fonte
------------

[https://github.com/andrebaltieri/aspnetcore-efcore-soft-delete](https://github.com/andrebaltieri/aspnetcore-efcore-soft-delete)

<div role="main" id="blog-s1-dotnet-134e3db1eea6c9829db1"></div>
<script type="text/javascript" src="https://d335luupugsy2.cloudfront.net/js/rdstation-forms/stable/rdstation-forms.min.js"></script>
<script type="text/javascript"> new RDStationForms('blog-s1-dotnet-134e3db1eea6c9829db1', 'UA-48664517-12').createForm();</script>