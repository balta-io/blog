AspNet Core, CQRS e Mediator, um trio que combina muito bem e pode elevar a organização das suas aplicações a um outro nível.

O que é o CQRS
--------------

CQRS é a sigla para Command and Queries Responsability Segregation, ou traduzido para o português, Separação de Responsabilidades em Consultas e Comandos.

Podemos interpretar as consultas como a parte de leitura da nossa aplicação e comandos como a parte de escrita, para facilitar nosso entendimento aqui.

Pensando desta forma, quando sabemos o que é CQRS, parece até óbvio, afinal em nossas quase nunca são balanceadas, ou tem muita leitura, ou tem muita escrita.

Particularmente, vejo que na maioria dos casos temos mais leitura do que escrita, como um exemplo de um eCommerce, onde navegamos por horas (Leitura apenas) e só fechamos o pedido uma vez (Escrita).

Agora que sabemos a parte fácil, vamos partir para implementação.

Iniciando a API
---------------

Para este exemplo vou construir tudo dentro da API mesmo, apenas em pastas separadas, mas tenha em mente, que em projetos maiores esta divisão seria realizada em projetos, como [mostramos no curso 7196](https://balta.io/cursos/todo-app-api-com-aspnet-core-cqrs-e-ef-core).

Desta forma, navegue para uma pasta segura e execute o comando abaixo para criar uma nova API.

dotnet new webapi -o Shop

Em seguida, abra a aplicação com o Visual Studio Code para iniciarmos o desenvolvimento.

Entidades
---------

Normalmente, quando começamos a modelagem de domínios ricos, começamos pelas entidades e objetos de valor como explico nos cursos [1975 - Modelando Domínios Ricos](https://balta.io/cursos/modelando-dominios-ricos) e [7182 - Refatorando para Testes de Unidade](https://balta.io/cursos/refatorando-para-testes-de-unidade).

Você pode usar entidades anêmicas e a abordagem Data Driven como mostro no curso [7194 - Criando APIs Data Driven](https://balta.io/cursos/criando-apis-data-driven-com-aspnet-core-3-e-ef-core-3) também, mas para este exemplo, vamos focar em entidades mais ricas.

Na verdade, vamos ser bem pragmáticos e "simular" que temos uma entidade rica, com propriedades privadas, objetos de valor e testes, mas no caso teremos apenas o código abaixo (Domain/Entities/Customer.cs).

```csharp
using System;

namespace Shop.Domain.Entities
{
    public class Customer
    {
        public Customer(string name, string email)
        {
            Id = Guid.NewGuid();
            Name = name;
            Email = email;
        }

        public Guid Id { get; private set; }
        public string Name { get; private set; }
        public string Email { get; private set; }
    }
}
```

O quem importa é saber que funciona para ambos modelos e entender a implementação do CQRS básico aqui.

Comandos (Request)
------------------

Se você acompanha meus cursos, sempre que falamos de algum padrão eu prefiro não usar Frameworks adicionais, assim aprendemos como as coisas funcionam de fato.

Para o CQRS é a mesma coisa, vamos iniciar criando o básico, e depois você pode melhorar este código, criar a sua implementação. Então não vou focar em reaproveitamento de código e coisas do tipo.

Sim, eu sei que podemos ter uma classe base para comandos, como fazemos no [curso 7196 por exemplo](https://balta.io/cursos/todo-app-api-com-aspnet-core-cqrs-e-ef-core), mas aqui é apenas uma demonstração, este código não deve ir para produção.

Desta forma, vamos criar nosso comando para criar um novo cliente. Como você pode notar, um comando é uma ação de escrita que será executada em nossa aplicação.

Esta ação (Comando) contém apenas as informações necessárias para criar um cliente, no caso, nome e e-mail apenas, tendo em mente que o ID é gerado de forma automática.

    using System;
    
    namespace Shop.Domain.Commands.Requests
    {
        public class CreateCustomerRequest
        {
            public string Name { get; set; }
            public string Email { get; set; }
        }
    }

Um outro exemplo seria um pedido, onde para criá-lo, precisamos apenas da lista de produtos do carrinho e do usuário que está realizando o pedido.

Todas as outras informações como a data do pedido, número, valor, estado, são gerados pela aplicação. Afinal, você não quer deixar o Frontend mandar o valor total do pedido não é mesmo?

Comandos (Response)
-------------------

Na sessão acima fizemos a requisição para criação de um novo cliente, mas e agora? Vamos supor que tenha dado tudo certo, o que devemos retornar para tela?

Melhor, vamos imaginar que queremos retornar o nome, E-mail, ID e a data da requisição, como podemos fazer?

Não podemos retornar a entidade Customer que criamos pois ela não tem a data, da mesma forma que não podemos retornar o CreateCustomerRequest pois ele não tem o ID nem a data.

Embora em parte dos cenários podemos enviar um objeto padrão de retorno como eu [mostro no curso 1974 na parte de padronização de APIs](https://balta.io/cursos/criando-apis-com-aspnetcore-2-e-dapper), em alguns casos queremos uma resposta customizada.

Desta forma, temos também o retorno dos comandos, que para facilitar, coloquei na pasta Domain/Commands/Responses, como CreateCustomerResponse.cs.

    using System;
    
    namespace Shop.Domain.Commands.Responses
    {
        public class CreateCustomerResponse
        {
            public Guid Id { get; set; }
            public string Name { get; set; }
            public string Email { get; set; }
            public DateTime Date { get; set; }
        }
    }

Pronto, agora temos um comando de entrada (Request) e um comando de retorno (Response), que compõe uma ação de criar um novo cliente.

Handlers
--------

Com nossos comandos criados, precisamos agora pensar no fluxo de execução da ação, e este ponto aqui varia bastante.

No curso [7182 - Refatorando para Testes de Unidade](https://balta.io/cursos/refatorando-para-testes-de-unidade) por exemplo, nós pegamos um fluxo de pedido que estava todo acoplado e refatoramos ele para uso com CQRS e muitos testes de unidade.

Então nesta fase, depende muito da quantidade de regras que você tem, talvez seja até interessante quebrar em mais fluxos sua ação, para facilitar o reuso do código.

Voltando ao nosso cenário, vamos imaginar que temos os seguintes passos na hora de cadastrar um cliente:

*   Verificar se o E-mail já está cadastrado
*   Incluir o cliente no banco
*   Enviar um E-mail de boas vindas

É um fluxo simples, mas não deixa de ser um fluxo, inclusive é interessante testar estes fluxos como mostramos no curso [7182 - Refatorando para Testes de Unidade](https://balta.io/cursos/refatorando-para-testes-de-unidade).

O que acontece quando recebemos um comando (Uma ação) é que precisamos de alguém para fazer algo com este comando, para manipular estas informações que recebemos.

Isto quem faz são os Handlers que traduzindo para o português seriam os manipuladores. Eles basicamente recebem um comando, executam um fluxo e retornam uma resposta.

Vamos começar criando uma interface para o nosso manipulador, chamada de ICreateCustomerHandler.cs, na pasta Domain/Handlers, com o seguinte código.

    using Shop.Domain.Commands.Requests;
    using Shop.Domain.Commands.Responses;
    
    namespace Shop.Domain.Handlers
    {
        public interface ICreateCustomerHandler
        {
            CreateCustomerResponse Handle(CreateCustomerRequest command);
        }
    }

Precisamos desta interface para gerenciar dependências posteriormente, e a implementação do nosso CreateCustomerHandler.cs que também fica na pasta Domain/Handlers segue abaixo.

    using System;
    using Shop.Domain.Commands.Requests;
    using Shop.Domain.Commands.Responses;
    using Shop.Domain.Entities;
    using Shop.Domain.Repositories;
    using Shop.Domain.Services;
    
    namespace Shop.Domain.Handlers
    {
        public class CreateCustomerHandler
        {
            ICustomerRepository _repository;
            IEmailService _emailService;
    
            public CreateCustomerHandler(ICustomerRepository repository, IEmailService emailService)
            {
                _repository = repository;
                _emailService = emailService;
            }
    
            public CreateCustomerResponse Handle(CreateCustomerRequest command)
            {
                // Aplicar Fail Fast Validations
    
                // Cria a entidade
                var customer = new Customer(command.Name, command.Email);
    
                // Persiste a entidade no banco
                _repository.Save(customer);
    
                // Envia E-mail de boas-vindas
                _emailService.Send(customer.Name, customer.Email);
    
                // Retorna a resposta
                return new CreateCustomerResponse
                {
                    Id = customer.Id,
                    Name = customer.Name,
                    Email = customer.Email,
                    Date = DateTime.Now
                };
            }
        }
    }

Basicamente, o que temos é um método chamado Handle que recebe um request (CreateCustomerRequest) e retorna uma resposta (CreateCustomerResponse).

Este Handler também faz uso do repositório para persistir a entidade de cliente, criada no fluxo e também do serviço de envio de E-mail.

Note que temos apenas as interfaces aqui, seguindo a boa prática de depender sempre de abstrações de nunca de implementações. Isto permitirá o Mock destes itens posteriormente para testes de unidade também.

Resolvendo as Dependências
--------------------------

Durante o processo de criação do Handler, geramos dependências que precisam ser resolvidas. Se você é novo no mundo de DI, [veja este artigo antes](https://balta.io/blog/aspnet-core-dependency-injection).

Vamos então para nosso Startup.cs, no método ConfigureServices e adicionar o trecho para gerenciar estas dependências que temos.

    public void ConfigureServices(IServiceCollection services)
            {
                services.AddControllers();
                services.AddTransient<ICreateCustomerHandler, CreateCustomerHandler>();
            }

Neste momento, já podemos chamar nosso Handler nos controlador, passando o CreateCustomerRequest como entrada e esperando o CreateCustomerResponse como resposta.

    using Microsoft.AspNetCore.Mvc;
    using Shop.Domain.Commands.Requests;
    using Shop.Domain.Handlers;
    
    namespace Shop.Controllers
    {
        [ApiController]
        [Route("customers")]
        public class CustomerController : ControllerBase
        {
            [HttpPost]
            [Route("")]
            public IActionResult Create(
                       [FromServices]ICreateCustomerHandler handler,
                       [FromBody]CreateCustomerRequest command
                   )
            {
                var response = handler.Handle(command);
                return Ok(response);
            }
        }
    }

Queries
-------

As consultas não são diferentes dos comandos, podemos ter uma requisição como mostrada abaixo.

    using System;
    
    namespace Shop.Domain.Queries.Requests
    {
        public class FindCustomerByIdRequest
        {
            public Guid Id { get; set; }
        }
    }

E uma resposta como mostrado abaixo.

    using System;
    
    namespace Shop.Domain.Queries.Responses
    {
        public class FindCustomerByIdResponse
        {
            public Guid Id { get; set; }
            public string Name { get; set; }
            public string Email { get; set; }
        }
    }

Novamente precisamos de um Handler para manipular esta requisição, e começamos pela interface.

    using Shop.Domain.Queries.Requests;
    using Shop.Domain.Queries.Responses;
    
    namespace Shop.Domain.Handlers
    {
        public interface IFindCustomerByIdHandler
        {
            FindCustomerByIdResponse Handle(FindCustomerByIdRequest command);
        }
    }

Em seguida implementamos o manipulador.

    using Shop.Domain.Queries.Requests;
    using Shop.Domain.Queries.Responses;
    using Shop.Domain.Repositories;
    
    namespace Shop.Domain.Handlers
    {
        public class FindCustomerByIdHandler : IFindCustomerByIdHandler
        {
            ICustomerRepository _repository;
    
            public FindCustomerByIdHandler(ICustomerRepository repository)
            {
                _repository = repository;
            }
            public FindCustomerByIdResponse Handle(FindCustomerByIdRequest command)
            {
                // TODO: Lógica de leitura se houver
    
                // Retorna o resultado
                return _repository.GetCustomerById(command.Id);
            }
        }
    }

Note que o repositório já retorna nosso Response, e não uma entidade. Além disso, se não houver lógica nenhuma de leitura, eu não vejo problema nenhum em chamar o repositório diretamente no controlador.

Vamos então resolver as dependências do novo Handler no Startup.cs e adicionar a chamada ao controlador.

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllers();
        services.AddTransient<ICreateCustomerHandler, CreateCustomerHandler>();
        services.AddTransient<IFindCustomerByIdHandler, FindCustomerByIdHandler>();
    }

Desta forma podemos chamar o manipulador no controlador, assim como fizemos com o método POST.

    using Microsoft.AspNetCore.Mvc;
    using Shop.Domain.Commands.Requests;
    using Shop.Domain.Handlers;
    using Shop.Domain.Queries.Requests;
    
    namespace Shop.Controllers
    {
        [ApiController]
        [Route("customers")]
        public class CustomerController : ControllerBase
        {
            [HttpGet]
            [Route("")]
            public IActionResult GetById(
                [FromServices]IFindCustomerByIdHandler handler,
                [FromQuery]FindCustomerByIdRequest command
            )
            {
                var result = handler.Handle(command);
                return Ok(result);
            }
    
            [HttpPost]
            [Route("")]
            public IActionResult Create(
                       [FromServices]ICreateCustomerHandler handler,
                       [FromBody]CreateCustomerRequest command
                   )
            {
                var response = handler.Handle(command);
                return Ok(response);
            }
        }
    }

Note que agora, por ser um método GET, nosso comando virá do FromQuery e não FromBody como fizemos anteriormente.

Pronto, finalizamos o exemplo com o básico do CQRS, sem nenhum Framework adicional nem nada, mas neste ponto entram algumas preocupações.

A medida que a aplicação cresce, como fica o Startup.cs? Como ficam as dependências, registrando uma a uma?

Além disso, para gerenciar dependências precisamos das interfaces, ou seja, para cada Handler temos uma e por aí vai.

Sempre que escrevermos aplicações mais detalhadas como esta, teremos mais arquivos, mais códigos, mas isto não é ruim, é organização.

Porém, podemos melhorar alguns pontos aqui, utilizando um padrão chamado Mediator.

O que é o Mediator?
-------------------

A tradução de Mediator para o português é mediador, justamente o que o padrão faz. Ele intermédia as comunicações da nossa abstraindo isto pra gente.

Em suma o que vamos fazer é simplesmente receber um comando e dizer para o Mediator se virar, e ele se encarregará de encontrar o Handler responsável e executá-lo.

### Precisamos do Mediator?

Não necessariamente! Como vimos no exemplo acima, fizemos o processo completo e não usamos o Mediator. Em diversos cursos, [como o 7198](https://balta.io/cursos/criando-um-todo-app-com-aspnet-core-firebase-angular-flutter), também usamos CQRS sem a necessidade do Mediator.

Outra coisa que precisamos ter em mente, é que abstração das dependências causa a quebra do Explicit Dependencies Principle, que prega o uso explícito de dependências.

Ocultando elas, podemos ter problemas em runtime e fica mais difícil capturar e descobrir erros.

Entenda, não estou dizendo que não devemos usar o padrão, mas sim que ele não está atrelado ao uso de CQRS.

Instalando o MediatR
--------------------

O pacote que implementa o padrão Mediator no .NET é o MediatR, e vamos instalar tanto seu pacote quanto o pacote para gerenciar dependências.

    dotnet add package MediatR
    dotnet add package MediatR.Extensions.Microsoft.DependencyInjection

Isto é tudo que precisamos para iniciar com Mediator em nossa aplicação.

Refatorando as requisições
--------------------------

Para que nossas dependências sejam resolvidas pelo MediatR, precisamos primeiramente que nossas requisições implementem a interface IRequest, como mostrado abaixo.

    using MediatR;
    using Shop.Domain.Commands.Responses;
    
    namespace Shop.Domain.Commands.Requests
    {
        public class CreateCustomerRequest : IRequest<CreateCustomerResponse>
        {
            public string Name { get; set; }
            public string Email { get; set; }
        }
    }

O mesmo vale para nossa consulta.

    using System;
    using MediatR;
    using Shop.Domain.Queries.Responses;
    
    namespace Shop.Domain.Queries.Requests
    {
        public class FindCustomerByIdRequest : IRequest<FindCustomerByIdResponse>
        {
            public Guid Id { get; set; }
        }
    }

Refatorando os manipuladores
----------------------------

Neste momento, não precisamos mais das interfaces para os Handlers que haviamos criado anteriormente, ao invés disso o MediatR se encarregará de buscar um Handler que possa manipular um comando que enviarmos a ele.

Ao invés disso, nosso Handler agora implementará a interface IRequestHandler<Request, Response> como no código abaixo.

    using System;
    using System.Threading;
    using System.Threading.Tasks;
    using MediatR;
    using Shop.Domain.Commands.Requests;
    using Shop.Domain.Commands.Responses;
    using Shop.Domain.Entities;
    using Shop.Domain.Repositories;
    using Shop.Domain.Services;
    
    namespace Shop.Domain.Handlers
    {
        public class CreateCustomerHandler : IRequestHandler<CreateCustomerRequest, CreateCustomerResponse>
        {
            ICustomerRepository _repository;
            IEmailService _emailService;
    
            public CreateCustomerHandler(ICustomerRepository repository, IEmailService emailService)
            {
                _repository = repository;
                _emailService = emailService;
            }
    
            public Task<CreateCustomerResponse> Handle(CreateCustomerRequest request, CancellationToken cancellationToken)
            {
                // Aplicar Fail Fast Validations
    
                // Cria a entidade
                var customer = new Customer(request.Name, request.Email);
    
                // Persiste a entidade no banco
                _repository.Save(customer);
    
                // Envia E-mail de boas-vindas
                _emailService.Send(customer.Name, customer.Email);
    
                // Retorna a resposta
                var result = new CreateCustomerResponse
                {
                    Id = customer.Id,
                    Name = customer.Name,
                    Email = customer.Email,
                    Date = DateTime.Now
                };
                return Task.FromResult(result);
            }
        }
    }

Note que a assinatura do método Handle mudou, seguindo os padrões de implementação da interface que implementamos. O mesmo vale para o manipulador da nossa consulta.

    using System.Threading;
    using System.Threading.Tasks;
    using MediatR;
    using Shop.Domain.Queries.Requests;
    using Shop.Domain.Queries.Responses;
    using Shop.Domain.Repositories;
    
    namespace Shop.Domain.Handlers
    {
        public class FindCustomerByIdHandler : IRequestHandler<FindCustomerByIdRequest, FindCustomerByIdResponse>
        {
            ICustomerRepository _repository;
    
            public FindCustomerByIdHandler(ICustomerRepository repository)
            {
                _repository = repository;
            }
    
            public Task<FindCustomerByIdResponse> Handle(FindCustomerByIdRequest request, CancellationToken cancellationToken)
            {
                // TODO: Lógica de leitura se houver
    
                // Retorna o resultado
                var result = _repository.GetCustomerById(request.Id);
                return Task.FromResult(result);
            }
        }
    }

Refatorando as dependências
---------------------------

Agora voltamos ao Startup.cs e podemos remover as resoluções de dependências anteriores, deixando apenas o MediatR cuidando disso.

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllers();
        services.AddMediatR(Assembly.GetExecutingAssembly());
    }

Desta forma, o MediatR se encarrega de receber um comando e verificar qual manipulador pode ser responsável por gerenciá-lo.

Refatorando o controlador
-------------------------

Agora nos controladores, podemos simplesmente chamar uma instância do MediatR e seu método Send para enviar um comando sem nos preocuparmos com nada.

    [HttpPost]
    [Route("")]
    public IActionResult Create(
                [FromServices]IMediator mediator,
                [FromBody]CreateCustomerRequest command
            )
    {
        var response = mediator.Send(command);
        return Ok(response);
    }

O mesmo vale para nossa consulta.

    [HttpGet]
    [Route("")]
    public IActionResult GetById(
        [FromServices]IMediator mediator,
        [FromQuery]FindCustomerByIdRequest command
    )
    {
        var result = mediator.Send(command);
        return Ok(result);
    }

Neste modelo fica mais fácil criar um controlador base que gerencia as requisições e padroniza nossa API.

Conclusão
---------

Não precisamos necessariamente utilizar o padrão Mediator junto ao CQRS, os prós em seu uso são a abstração das dependências e possíveis facilidades no código.

Seus contras são os possíveis problemas ocasionados pelo não direcionamento das dependências, podendo muitas vezes serem causados em Runtime.

<div role="main" id="blog-s1-dotnet-134e3db1eea6c9829db1"></div>
<script type="text/javascript" src="https://d335luupugsy2.cloudfront.net/js/rdstation-forms/stable/rdstation-forms.min.js"></script>
<script type="text/javascript"> new RDStationForms('blog-s1-dotnet-134e3db1eea6c9829db1', 'UA-48664517-12').createForm();</script>