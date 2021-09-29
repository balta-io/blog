---
title: Criando uma API do zero com .NET Core 3.1 e EF Core
published: false
description: 
tags: 
cover_image: TBD
---

Olá, tudo bem? Vamos criar uma API do zero com .NET e EF de um modo fácil e passando por todos os detalhes do mundo .NET? 

Vamos lá!

Primeiro você precisa criar uma pasta para o seu projeto. Eu criei aqui uma pasta chamada ApiCartao onde vou guardar todos os arquivos referentes ao meu projeto. 

Eita, esqueci de falar um pouco mais sobre o que vamos criar. Deixa eu te explicar rapidinho...

Nosso projeto será um gerador de números de cartão de crédito virtual. Nós teremos uma API onde existirá dois endpoints:

- Um receberá o email da pessoa e retornará um objeto de resposta com o número do cartão de crédito.
- O outro endpoint deverá listar, em ordem de criação, todos os cartões de crédito virtuais de um solicitante (passando seu email como parâmetro).

Continuando...

Com a pasta criada, acesse ela pelo terminal de sua preferência para criarmos os projetos dotnet necessários.

Nossa API será pequena, com poucos arquivos e não necessitará de uma grande estrutura. Mas mesmo assim, vamos criar uma estrutura em que possamos escalar no futuro sem maiores problemas.

## Passo 1 - Estrutando Projeto

```bash
dotnet new classlib -o Domain
dotnet new classlib -o Infra
dotnet new classlib -o Api
dotnet new classlib -o Shared
dotnet new mstest -o Tests
```

Com esses 3 comandos acima, nós criamos:

- Um projeto para o domínio da aplicação, onde guardaremos todas regras de negócios, entidades e VOs.
- Um projeto para a Infra, onde teremos nossa infraestrutura com o banco desenvolvida aqui.
- Um projeto API, onde teremos a nossa API que irá consumir os outros projetos.
- Um projeto Shared que servirá para compartilhar código entre projetos quando nossa aplicação escalar
- E um projeto de testes para nós testarmos o que for necessário.

Agora vamos criar uma solution e associar estes projetos a esta solution.

```bash
dotnet new sln
dotnet sln add .\Domain\Domain.csproj
dotnet sln add .\Infra\Infra.csproj
dotnet sln add .\Api\Api.csproj
dotnet sln add .\Shared\Shared.csproj
dotnet sln add .\Tests\Tests.csproj
```

Com os comando acima nós criamos a solution e associamos nossos projetos a esta solution.

## Passo 2 - Criar Entidades

Para criar nossas entidades, precisamos apagar algumas classes que vem como padrão na criação dos projetos.

Delete as classes WeatherForecast.cs do projeto Api e Class1.cs dos projetos Domain, Infra e Shared.

Começaremos criando nossa entidade principal. De acordo com os requisitos do projeto precisamos ter uma propriedade para o email, uma para o número de cartão de crédito gerado e uma propriedade para guardar a data de geração deste número.

```csharp
using System;
using System.ComponentModel.DataAnnotations;

namespace Domain.Entities
{
    public class CartaoVirtual{
        public CartaoVirtual(string email, string numeroCartao, DateTime dataCriacao)
        {
            Email = email;
            NumeroCartao = numeroCartao;
            DataCriacao = dataCriacao;
        }

        [Key]
        public int Id { get; private set; }

        public string Email { get; private set; }

        [MaxLength(16, ErrorMessage = "Este campo deve conter no máximo 16 caracteres")]
        public string NumeroCartao { get; private set; }

        public DateTime DataCriacao { get; private set; }
    }
}
```

Antes de criar a classe acima, crie uma pasta dentro de Domain com o nome "Entities", será dentro dela que criaremos todas as nossas entidades.

Dentro de Entities, crie um novo arquivo chamado CartaoVirtual.cs e adicione o código acima.

As propriedades estão privadas para evitar que sejam alteradas externamente, corrompendo assim, o princípio aberto-fechado do SOLID.

Como precisamos obrigatoriamente ter o email, número do cartão e a data de criação, criei um construtor com as 3 propriedades como parâmetro para que não seja possível criar um cartão virtual sem essas 3 informações.

## Passo 3 - Criando os Commands

Para trazer os dados na requisição e retornar o resultado, não é recomendado que usemos a entidade para este fim. Por isso, criaremos dois commands(você pode chamar de DTO, Model ou outra nomenclatura do seu gosto), estes commands servirão para trazer o email necessário para gerar o cartão e para retornar os dados após a geração.

Logo, criei dois commands, um como input de dados e outro como resultado após a geração. Use o conhecimento que você já adquiriu até aqui e crie as pastas e arquivos dos commands exemplificados abaixo. Você consegue, vamos lá!!

![https://github.com/balta-io/blog/blob/92bff0d4b3690c54a2c5e8394bc99364bbddadd7/criando-uma-api-do-zero/images/Untitled.png](https://github.com/balta-io/blog/blob/92bff0d4b3690c54a2c5e8394bc99364bbddadd7/criando-uma-api-do-zero/images/Untitled.png?raw=true)

Após criar as pastas e arquivos, vamos adicionar um pacote do Nuget chamado Flunt para nos auxiliar na verificação do email e nas futuras validações que o projeto necessitar.

Acesse o terminal e entre no projeto Domain, depois adicione o pacote Flunt na versão 1.0.5. Veja o exemplo abaixo:

```bash
$ cd ./Domain
$ dotnet add package Flunt -v 1.0.5
```

Após isso, adicione estes códigos em suas classes

```csharp
using Flunt.Notifications;
using Flunt.Validations;

namespace Domain.Commands.Inputs
{
    public class GerarCartaoVirtualCommand : Notifiable
    {
        public string Email { get; set; }

        public void Validar(){
            AddNotifications(new Contract().IsEmail(Email, "Email", "Email Inválido"));
        }
    }
}
```

```csharp
namespace Domain.Commands.Results
{
    public class GerarCartaoVirtualCommandResult
    {
        public string Email { get; set; }
        public string NumeroCartao { get; set; }
    }
}
```

## Passo 4 - Criando os Handlers

Os handlers nada mais são que a execução das nossas regras de negócio. No nosso caso, iremos criar somente um Handler que será o de Geração do nosso número de cartão de crédito virtual.

Assim como nos passos anteriores, crie uma pasta em Domain chamada de Handlers e crie um novo arquivo dentro desta pasta o chamando de  GerarCartaoVirtualHandler.cs

![https://github.com/balta-io/blog/blob/92bff0d4b3690c54a2c5e8394bc99364bbddadd7/criando-uma-api-do-zero/images/Untitled%201.png](https://github.com/balta-io/blog/blob/92bff0d4b3690c54a2c5e8394bc99364bbddadd7/criando-uma-api-do-zero/images/Untitled%201.png?raw=true)

Neste arquivo iremos realizar os seguintes passos:

- Validar o command enviado pela requisição
- Gerar o número do cartão
- Criar entidade e salvar no banco
- Criar command result e retornar resultado

DICA: Após criar a classe, comente os passos que irá seguir antes de implementar, isto fará com que a implementação seja mais direcionada em sua mente.

![https://github.com/balta-io/blog/blob/92bff0d4b3690c54a2c5e8394bc99364bbddadd7/criando-uma-api-do-zero/images/Untitled%202.png](https://github.com/balta-io/blog/blob/92bff0d4b3690c54a2c5e8394bc99364bbddadd7/criando-uma-api-do-zero/images/Untitled%202.png?raw=true)

## Passo 5 - Criando os testes

É recomendado que criemos os testes antes de desenvolver nossas features, então vamos criar os testes do GerarCartaoVirtualHandler antes da implementação.

Como você já adquiriu um bom conhecimento na criação de pastas e arquivos, vou ser mais pragmático a partir de agora, ok? 

Vamos abrir nosso projeto no Visual Studio para facilitar a criação e execução dos testes.

Simbora!!

```csharp
using Domain.Commands.Inputs;
using Domain.Commands.Results;
using Domain.Handlers;
using Microsoft.VisualStudio.TestTools.UnitTesting;

namespace Tests.HandlerTests
{
    [TestClass]
    public class GerarCartaoVirtualHandlerTests
    {
        [TestMethod]
        public void DeveRetornarEmailInvalido()
        {
            var command = new GerarCartaoVirtualCommand();
            command.Email = "jhonatafernandes.com";

            var gerarCartaoHandler = new GerarCartaoVirtualHandler();
            var result = gerarCartaoHandler.Handle(command);

            Assert.AreEqual(result, typeof(string));
        }

        [TestMethod]
        public void DeveRetornarSucesso()
        {
            var command = new GerarCartaoVirtualCommand();
            command.Email = "jhonatafernandes.com";

            var gerarCartaoHandler = new GerarCartaoVirtualHandler();
            var result = gerarCartaoHandler.Handle(command);

            Assert.AreEqual(result, typeof(GerarCartaoVirtualCommandResult));
        }
    }
}
```

Provavelmente os testes vão falhar, mas só de tê-los criado já nos ajudará bastante no desenvolvimento do handler de geração do cartão. Não se preocupe, iremos refatorar o código em breve.

## Passo 6 - Desenvolvendo Handler de Geração de Cartão de Crédito Virtual

Seguindo os comentários no método, vamos implementar o handler da seguinte forma:

```csharp
using Domain.Commands.Inputs;
using Domain.Commands.Results;
using Domain.Entities;
using System;

namespace Domain.Handlers
{
    public class GerarCartaoVirtualHandler{
        public GerarCartaoVirtualCommandResult Handle(GerarCartaoVirtualCommand command){
            //Validar Command
            command.Validar();

            if (command.Invalid)
                return null;

            //Gerar número de cartão aleatório
            var numeroCartao = string.Empty;
            for (int i = 0; i < 4; i++)
            {
                var random = new Random();
                var blocoCartao = $"{random.Next(1000, 9999)}";
                numeroCartao = $"{numeroCartao + blocoCartao}";
            }

            //Criar entidade e salvar no banco
            var cartaoVirtual = new CartaoVirtual(command.Email, numeroCartao, DateTime.Now);

            //Criar command result
            var commandResult = new GerarCartaoVirtualCommandResult(command.Email, numeroCartao);

            return commandResult;
        }
    }
}
```

Você deve estar percebendo que ainda não estamos salvando nada no banco. Pois é, vamos chegar nessa fase. Mas o importante agora é deixar o Domain pronto antes de configurar o banco.

Rode novamente os seus testes e veja se a implementação está ok. Se estiver, vamos seguir em frente, se não, ajuste o handler para que passe nos testes.

## Passo 7 - Repositórios

Chegou a hora de criarmos nosso repositório para que possamos comunicar com o banco. No seu domain crie uma pasta chamada Repositories e crie uma interface dentro dela chamada ICartaoVirtualRepository.cs. Aproveite que está no Visual Studio e crie com auxílio da IDE. Como já pegamos a prática de criar tudo na "mão", podemos utilizar uma IDE com suas praticidades.

```csharp
using Domain.Entities;
using System.Collections.Generic;

namespace Domain.Repositories
{
    public interface ICartaoVirtualRepository
    {
        void AdicionarCartaoVirtual(CartaoVirtual cartaoVirtual);
        IEnumerable<CartaoVirtual> ListarCartaoVirtual(string email);
    }
}
```

Criamos esta interface para que possamos sempre depender de uma abstração e nunca da implementação. Esse é um dos princípios mais importantes a serem seguidos no desenvolvimento, então guarde bem esse conhecimento e aplique em seus projetos.

Com nossa interface criada, podemos criar agora a implementação do repositório e a comunicação com o banco.

## Passo 8 - Banco de Dados

Neste exemplo de API nós vamos utilizar o Entity Framework Core e um banco de dados em memória para facilitar nosso desenvolvimento. 

Para este passo, vamos instalar dentro do projeto INFRA os pacotes necessários para dar o start em nosso banco.

Instale o pacote Microsoft.EntityFramework.Core.InMemory dentro do projeto INFRA utilizando o gerenciador de pacotes nuget no VS dessa forma:

- Clique com o botão direito em cima do nome do projeto e depois em Manage Nuget Packages

    ![https://github.com/balta-io/blog/blob/92bff0d4b3690c54a2c5e8394bc99364bbddadd7/criando-uma-api-do-zero/images/Untitled%203.png](https://github.com/balta-io/blog/blob/92bff0d4b3690c54a2c5e8394bc99364bbddadd7/criando-uma-api-do-zero/images/Untitled%203.png?raw=true)

- Depois pesquise pelo nome do pacote e faça a instalação

![https://github.com/balta-io/blog/blob/92bff0d4b3690c54a2c5e8394bc99364bbddadd7/criando-uma-api-do-zero/images/Untitled%204.png](https://github.com/balta-io/blog/blob/92bff0d4b3690c54a2c5e8394bc99364bbddadd7/criando-uma-api-do-zero/images/Untitled%204.png?raw=true)

 

Após instalar este pacote, implemente os métodos do repositório seguindo os métodos indicados na interface.

```csharp
using Domain.Entities;
using Domain.Repositories;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.DependencyInjection;
using System;
using System.Collections.Generic;
using System.Linq;

namespace Infra.Repositories
{
    public class CartaoVirtualRepository : ICartaoVirtualRepository
    {
        private readonly DataContext _context;
        public CartaoVirtualRepository(IServiceProvider serviceProvider)
        {
            _context = serviceProvider.GetService<DataContext>();
        }

        public void AdicionarCartaoVirtual(CartaoVirtual cartaoVirtual)
        {
            _context.Cartoes.Add(cartaoVirtual);
            _context.SaveChanges();
        }

        public IEnumerable<CartaoVirtual> ListarCartaoVirtual(string email)
        {
            return _context.Cartoes.AsNoTracking().Where(it => it.Email == email).OrderBy(x => x.DataCriacao).ToList();
        }
    }
}
```

No código acima há conceitos que você pode ainda não conhecer, mas não se preocupe vamos passar por eles agora.

A classe DataContext é a classe responsável em criar nossa conexão e comunicação com o banco de dados. Como estamos utilizando o EF Core In Memory, nosso banco de dados está em memória, ou seja, ficará salvo na memória até a aplicação finalizar.

Outro ponto é a injeção de dependência que estou fazendo no construtor. Este conceito se refere a inclusão dentro de uma classe de uma dependência externa de outra classe. Nós injetamos uma dependência dentro do repositório para que ele possa realizar algumas tarefas em que ele sozinho não pode, mas a classe DataContext sim. 

Para ocorrer tudo bem nas nossas injeções de dependência, precisamos adicionar esses serviços dentro do nosso Startup.cs, lá no projeto API. Então vamos lá!!

```csharp
services.AddScoped<DataContext, DataContext>();
services.AddTransient<ICartaoVirtualRepository, CartaoVirtualRepository>();
```

Dentro do método ConfigureServices você deve adicionar estas duas linhas. A primeira irá chamar este serviço uma vez a cada requisição, ou seja, nós iremos abrir uma conexão com o banco de dados apenas uma vez a cada requisição do usuário.

Já a segunda linha irá iniciar o repositório sempre que ele for injetado em uma classe.

Com isso em mente, podemos criar nosso DataContext sem maiores problemas. Lá em infra crie um novo arquivo chamado DataContext.cs e adicione este código:

```csharp
using Domain.Entities;
using Microsoft.EntityFrameworkCore;

namespace Infra
{
    public class DataContext : DbContext
    {
        public DataContext(DbContextOptions<DataContext> options)
            : base(options)
        {
        }

        public DbSet<CartaoVirtual> Cartoes { get; set; }
    }
}
```

O construtor é padrão, quase sempre você irá usar desta forma, já a propriedade dbSet<> será criada sempre que houver uma entidade a ser mapeada no banco. Basta criar esta propriedade colocando sua entidade em DbSet<> e dar um nome a sua tabela que o Entity  Framework cuidará do mapeamento entre o banco e suas entidades.

Ufa! 

Já temos bastante coisa criada. Mas ainda falta a api para poder recebermos as requisições, certo? Então vamos lá!!

## Passo 9 - API

Dentro do projeto API, se não houver uma pasta Controllers, crie uma e adicione um controller chamado CartaoVirtualController.cs.

Dentro desta classe adicione o seguinte código:

```csharp
using Domain.Commands.Inputs;
using Domain.Entities;
using Domain.Handlers;
using Domain.Repositories;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.DependencyInjection;
using Shared.Commands;
using System;
using System.Collections.Generic;

namespace Api.Controllers
{
    [Route("v1/cartoes")]
    public class CartaoVirtualController : Controller
    {
        private readonly ICartaoVirtualRepository _repository;

        public CartaoVirtualController(IServiceProvider serviceProvider)
        {
            _repository = serviceProvider.GetService<ICartaoVirtualRepository>();
        }

        [HttpGet]
        [Route("{email}")]
        public IEnumerable<CartaoVirtual> Get(string email)
        {
            var cartoes = _repository.ListarCartaoVirtual(email);
                
            return cartoes;
        }

        
        [HttpPost]
        [Route("")]
        public ICommandResult Post([FromBody]GerarCartaoVirtualCommand command)
        {
            var handler = new GerarCartaoVirtualHandler(_repository);

            return handler.Handle(command);
        }
    }
}
```

Opaa! Você percebeu a injeção de dependência nesta classe? Acredito que sim, então vamos descobrir o que este controller faz.

Em [Route()] definimos nossa rota padrão quando utilizada antes da classe e quando chamada antes dos métodos definimos as sub-rotas.

Exemplo > "v1/cartoes" é a minha rota padrão, logo, tudo que chegar até meu controller deve ter "v1/cartoes" em sua URL. Já a rota "teste" indica que após o "v1/cartoes" eu irei adicionar teste nesta URL, onde ficaria "v1/cartoes/teste".

Nossa API terá somente duas funcionalidades, a primeira será para buscar todos os cartões gerados de um determinado email. E a segunda irá realizar a geração de um novo cartão virtual e a inclusão no banco.

O primeiro método não tem segredo, como já injetamos o repositório como dependência, podemos chamar ele direto no método e trazer todos os cartões que possuem o email igual ao enviado na requisição.

Já o segundo método nós chamamos o handler que criamos lá atrás e retornamos um ICommandResult que você deve ainda não saber o que é. 

Por isso vamos para a refatoração do código pra você ficar por dentro de tudo.

## Passo 10 - Refatoração

Chegou a hora de utilizar o projeto SHARED. Este projeto serve para compartilhar recursos em comum com outros projetos. No momento ele não será de tanta serventia, mas quando nossa aplicação crescer e escalar ele será de suma importância.

Dentro de SHARED eu criei uma interface chamada ICommandResult, ela servirá como base dos retornos das requisições PUT, POST e DELETE.

Dentro dela teremos o seguinte código:

```csharp
namespace Shared.Commands
{
    public interface ICommandResult
    {
        public bool Success { get; set; }
        public string Message { get; set; }
        public object Data { get; set; }
    }
}
```

Estas 3 propriedades serão sempre retornadas ao front, apenas a propriedade DATA poderá mudar de acordo com a requisição. 

Com a chegada desta interface, precisamos alterar também o nosso Command Result antigo. Agora ele ficará da seguinte forma:

```csharp
using Shared.Commands;

namespace Domain.Commands.Results
{
    public class CommandResult : ICommandResult
    {
        public CommandResult(bool success, string message, object data)
        {
            Success = success;
            Message = message;
            Data = data;
        }

        public bool Success { get; set; }
        public string Message { get; set; }
        public object Data { get; set; }
    }
}
```

Eu alterei o nome da classe para que possamos trabalhar com um Result único, já que a propriedade DATA pode ser qualquer tipo de objeto.

Continuando a refatoração...

Nosso handler também mudou um pouco após a alteração do Command Result:

```csharp
using Domain.Commands.Inputs;
using Domain.Commands.Results;
using Domain.Entities;
using Domain.Repositories;
using Microsoft.Extensions.DependencyInjection;
using Shared.Commands;
using System;

namespace Domain.Handlers
{
    public class GerarCartaoVirtualHandler{
        private readonly ICartaoVirtualRepository _repository;

        public GerarCartaoVirtualHandler(ICartaoVirtualRepository repository)
        {
            _repository = repository;
        }

        public ICommandResult Handle(GerarCartaoVirtualCommand command){
            //Validar Command
            command.Validar();

            if (command.Invalid)
                return new CommandResult(false, "Email Inválido", command.Notifications);

            //Gerar número de cartão aleatório
            var numeroCartao = string.Empty;
            for (int i = 0; i < 4; i++)
            {
                var random = new Random();
                var blocoCartao = $"{random.Next(1000, 9999)}";
                numeroCartao = $"{numeroCartao + blocoCartao}";
            }

            //Criar entidade e salvar no banco
            var cartaoVirtual = new CartaoVirtual(command.Email, numeroCartao, DateTime.Now);
            try
            {
                _repository.AdicionarCartaoVirtual(cartaoVirtual);
            }
            catch (Exception e)
            {
                return new CommandResult(false, "Erro ao salvar os dados", e.Message);
            }

            //Crar command result e retornar
            return new CommandResult(true, "Requisição Realizada com Sucesso", new { command.Email, numeroCartao });
        }
    }
}
```

Além de melhorar o retorno quando o Command de entrada estiver inválido, nós também injetamos o repositório e chamamos ele dentro de um try catch para assegurarmos de possíveis erros.

Neste handler você pode perceber bem a importância da refatoração do CommandResult e a criação da interface.

Se você rodar os testes agora, vai perceber que estão todos quebrados, então vamos refatorá-los também.

```csharp
using Domain.Commands.Inputs;
using Domain.Handlers;
using Domain.Repositories;
using Microsoft.VisualStudio.TestTools.UnitTesting;
using Tests.Repositories;

namespace Tests.HandlerTests
{
    [TestClass]
    public class GerarCartaoVirtualHandlerTests
    {
        [TestMethod]
        public void DeveRetornarEmailInvalido()
        {
            var command = new GerarCartaoVirtualCommand();
            command.Email = "jhonatafernandes.com";

            var gerarCartaoHandler = new GerarCartaoVirtualHandler(new FakeRepository());
            var result = gerarCartaoHandler.Handle(command);

            Assert.AreEqual("Email Inválido", result.Message);
        }

        [TestMethod]
        public void DeveRetornarSucesso()
        {
            var command = new GerarCartaoVirtualCommand();
            command.Email = "jhfernandes.oliveira@gmail.com";

            var gerarCartaoHandler = new GerarCartaoVirtualHandler(new FakeRepository());
            var result = gerarCartaoHandler.Handle(command);

            Assert.AreEqual(true, result.Success);
        }
    }
}
```

Viu uma nova classe chamada de FakeRepository?

Pois bem, essa é uma das vantagem de se trabalhar com a abstração das interfaces.

Dentro do projeto de testes eu criei uma pasta chamada Repositories e dentro dela uma classe chamada FakeRepository.cs com o seguinte código:

```csharp
using Domain.Entities;
using Domain.Repositories;
using System.Collections.Generic;

namespace Tests.Repositories
{
    public class FakeRepository : ICartaoVirtualRepository
    {
        public void AdicionarCartaoVirtual(CartaoVirtual cartaoVirtual)
        {
            // Método intencionalmente vazio
        }

        public IEnumerable<CartaoVirtual> ListarCartaoVirtual(string email)
        {
            return new List<CartaoVirtual>();
        }
    }
}
```

Esse método irá FAKEAR o meu repositório para que os testes aconteçam tranquilamente, já que na hora de testar eu não posso depender de serviços externos.

## Finalizando

Agora nossa API deve estar funcionando corretamente, eu espero... kkkk

Então vamos testar!

Primeiro rode os testes, se ocorrer tudo bem, vamos testar as requisições.

Você pode utilizar diretamente o POSTMAN para realizar suas requisições ou pode utilizar o swagger para testar com uma interface gráfica mais amigável.

Eu escolhi o Swagger :)

Adicione o pacote Swashbuckle.Core no projeto API e depois adicione estas linhas no seu Startup. No método ConfigureServices

```csharp
services.AddSwaggerGen(t =>
    {
        t.SwaggerDoc("v1", new OpenApiInfo { Title = "Api Cartão Virtual", Version = "v1" });
    });
```

No método Configure:

```csharp
app.UseSwagger();
    app.UseSwaggerUI(c =>
    {
        c.SwaggerEndpoint("/swagger/v1/swagger.json", "API Cartão Virtual - V1");
    });
```

## Testando as requisições

Acesse [https://localhost:5001/swagger/index.html](https://localhost:5001/swagger/index.html)

![https://github.com/balta-io/blog/blob/92bff0d4b3690c54a2c5e8394bc99364bbddadd7/criando-uma-api-do-zero/images/Untitled%205.png](https://github.com/balta-io/blog/blob/92bff0d4b3690c54a2c5e8394bc99364bbddadd7/criando-uma-api-do-zero/images/Untitled%205.png?raw=true)

Testando Post com email inválido

![https://github.com/balta-io/blog/blob/92bff0d4b3690c54a2c5e8394bc99364bbddadd7/criando-uma-api-do-zero/images/Untitled%206.png](https://github.com/balta-io/blog/blob/92bff0d4b3690c54a2c5e8394bc99364bbddadd7/criando-uma-api-do-zero/images/Untitled%206.png?raw=true)

Testando POST com email correto

![https://github.com/balta-io/blog/blob/92bff0d4b3690c54a2c5e8394bc99364bbddadd7/criando-uma-api-do-zero/images/Untitled%207.png](https://github.com/balta-io/blog/blob/92bff0d4b3690c54a2c5e8394bc99364bbddadd7/criando-uma-api-do-zero/images/Untitled%207.png?raw=true)

Testando GET

![https://github.com/balta-io/blog/blob/92bff0d4b3690c54a2c5e8394bc99364bbddadd7/criando-uma-api-do-zero/images/Untitled%208.png](https://github.com/balta-io/blog/blob/92bff0d4b3690c54a2c5e8394bc99364bbddadd7/criando-uma-api-do-zero/images/Untitled%208.png?raw=true)

## Conclusão

UFA!!

Agora sim chegamos ao fim. Criamos uma simples API mas com vários conceitos complexos e importantíssimos. Espero ter te ajudado de alguma forma e trazido algum conhecimento que você possa aplicar em seus projetos.

Se caso encontrar algum bug ou quiser sugerir alguma melhoria, acessa o repositório desta API que se encontra [NESTE LINK](https://github.com/jhonatafernandes/api-cartao-virtual) e envia um Pull Request lá. Ou se quiser, pode entrar em contato diretamente comigo, será um prazer aprender mais com você. 

Abraços!!!
