Ao separarmos e dividirmos responsabilidades, criamos dependências e uma hora temos que resolvê-las.  
  
É comum e viável, focarmos na resolução de um problema e ignorar o que está ao redor dele, como por exemplo nos Controllers de nossas APIs.  
  
Lá temos a execução dos nossos fluxos, deixando de fora itens como acesso à dados por exemplo. Então é possível dizermos que um Controller não deve se preocupar com acesso à dados, isto é problema do repositório.  
  
É possível afirmarmos também, que para uma determinada Action de um Controller funcionar, ela precisa de um Repositório, e como este repositório vai ser instanciado, ou o que ele vai fazer, não é problema dela.  
  
Em suma, delegamos as responsabilidades. É como seu primeiro dia na empresa, onde você precisa de uma máquina para trabalhar, mas pouco importa quem ou como esta máquina chegará até você.

Dependa sempre da abstração
---------------------------

Em diferentes pontos da sua aplicação, você criará interfaces, que nada mais são do que contratos que somente dizem o que deve ser feito, e não como. Estas interfaces são abstrações entre o que deve ser feito e como deve ser feito.  
  
Se você assistiu o curso 1975 - Modelando Domínios Ricos, você provavelmente percebeu que criamos interfaces para nossos repositórios, para abstrair sua implementação e podermos testá-los depois, criando versões FALSAS dele (FakeRepository).  
  
Embora você possa fazer injeção de dependência sobre duas implementações, como mostrado abaixo:

    public void Teste(MeuRepositorio repositorio) {
    ...
    }

Se o MeuRepositorio apresentado no código acima conter outra dependência, que contém outra dependência e assim por dia, ficaria uma bagunça.  
  
Veja este exemplo sem o uso de abstrações:


    void main() {
        var command = new Command("A", "B");
        var uow = new UnitOfWork();
        var repo = new UserRepository(uow);
        var handler = new UserHandler(repo);
        var result = handler.handle(command);  
    }

    public class DataContext { 
        public DataContext(UnitOfWork uow) { ... }
    }

    public class UserRepository {
        public UserRepository(DataContext context) { ... }
    }

    public class UserHandler {
        public UserHandler(UserRepository repository) { ... }
    }


Note que para chamar o Handler do usuário, nós precisamos primeiro do repositório, que por sua vez precisa do DataContext que por sua vez precisa do UnitOfWork.  
  
Estas dependências são inviáveis pelo simples fato de não possuírem uma abstração, e TODA VEZ que você precisar chamar o Handler, terá todo este trabalho.

Resolvendo as Dependências
--------------------------

A solução para isto é justamente resolver as dependências em algum lugar, e um ótimo local para isto é ao iniciar sua aplicação.  
  
O ASP.NET Core possui uma forma simples e direta para resolver dependências, já integrada a ele, sem necessidade de nenhum pacote adicional. Embora você possa utilizar outro framework como Ninject por exemplo.  
  
O fato é que a resolução de dependências nativa do ASP.NET Core é boa e sustenta boa parte dos cenários. O Ninject resolve X milhões de objetos por segundo, mas espera aí... você vai precisar de tudo isso? X milhões de objetos na memória?  
  
Sendo assim, o ASP.NET Core possui duas formas de resolver dependências:

### AddScoped

É o que chamamos de Singleton, onde dado objeto/abstração A, ela será resolvida com uma instância do objeto B.  
  
O Singleton recebe este nome por possuir apenas uma instância do objeto na memória para aquela requisição, ou seja, sempre que uma dependência desta abstração/objeto for resolvida, ela utilizará o mesmo objeto.  
  
Este cenário é muito comum no uso de DataContexts ou DataFactories ou itens relacionados a banco de dados, onde não queremos ter mais de uma conexão por requisição. Sendo assim, dada a primeira resolução de dependência do seu DataContext, as próximas farão uso do mesmo objeto, que ficará na memória enquanto a requisição estiver viva.  
  
Ao término da requisição o GC se encarrega de remover os objetos da memória.

### AddTransient

Diferente do Singleton, no AddTransient, para cada resolução de dependência, temos um novo objeto, ideal para cenáriosonde você não quer reaproveitar nada.

### Código

Tanto o AddScopped quanto o Addtransient são utilizados no Startup.cs da sua API, como mostrado no código abaixo:`

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc();
        
        services.AddScoped<StoreDataContext, StoreDataContext>();
        services.AddTransient<ProductRepository, ProductRepository>();
    }

Note que utilizamos o DataContext com AddScopped pois ele manterá as conexões com bancos de dados e também as transações, e não queremos múltiplas conexões ou transações abertas caso haja necessidade de chamá-lo em mais de um lugar.  
  
Já o ProductRepository ficou com AddTransient, pois não necessitamos manter estado de nada dentro dele, ou seja, a cada injeção dele, será uma nova instância.

Injetando objetos nos Controllers
---------------------------------

Com as dependências resolvidas, podemos injetar objetos de duas formas nos Controllers, sendo elas via FromServices ou manualmente.

### Injetando Manualmente

No exemplo abaixo, fazemos a injeção manual, onde temos uma propriedade, normalmente privada e somente leitura, que recebe a instância do nosso objeto.

    public class ProductController : Controller
    {
        private readonly IProductRepository _repository;

        public ProductController(IProductRepository repository)
        {
            _repository = repository;
        }

        [Route("v1/products")]
        [HttpGet]
        public IEnumerable<ListProductViewModel> Get()
        {
            return _repository.Get();
        }
    }

Posteriormente, no construtor da classe, fazemos a injeção de dependência, que será resolvida pelo ASP.NET Core, gerando a instância do nosso objeto.  
  
Para finalizar, passamos a instância do objeto gerado para nossa propriedade privada, finalizando o processo.

### FromServices

Há também uma forma mais limpa de injetar nossas dependências nos Controllers, utilizando o FromServices como no código abaixo:

    public class ProductController : Controller
    {
        [Route("v1/products")]
        [HttpGet]
        public IEnumerable<ListProductViewModel> Get([FromServices]IProductRepository repository)
        {
            return repository.Get();
        }
    }

Ao adicionar o FromServices a um parâmetro em algum método do seu Controller, o ASP.NET Core automaticamente buscará nas dependências (AddScopped, AddTransiente) suas implementações e fará a resolução.  
  
O legal desta forma é que economizamos várias linhas de código e deixamos os Controllers mais limpos.

Conclusão
---------

Criar dependências é algo que está cada vez mais comum nas aplicações, mas a conta uma hora chega. Que bom que e o ASP.NET Core facilita muito a resolução de dependências já nativamente, sem necessidade de nenhum framework externo.

<div role="main" id="blog-s1-dotnet-134e3db1eea6c9829db1"></div>
<script type="text/javascript" src="https://d335luupugsy2.cloudfront.net/js/rdstation-forms/stable/rdstation-forms.min.js"></script>
<script type="text/javascript"> new RDStationForms('blog-s1-dotnet-134e3db1eea6c9829db1', 'UA-48664517-12').createForm();</script>