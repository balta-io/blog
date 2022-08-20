Injeção de Dependência é uma técnica que implementa um princípio de design chamado de IoC. Neste artigo vamos aprender a utilizá-las no ASP.NET.

## Injeção de Dependência no ASP.NET
O ASP.NET possui recursos nativos para trabalharmos com **Dependency Injection** (Injeção de Dependência) de forma simples e fácil.

Porém, precisamos entender como ele resolve as dependências para aproveitar melhor estes recursos e não cometer possíveis erros.

Caso esteja com dúvidas em relação a DI, sugiro que veja [**este artigo**](https://balta.io/blog/dependency-injection) no qual abordo mais a fundo o assunto.

## Versão em vídeo
[![IMAGE ALT TEXT HERE](https://img.youtube.com/vi/9n0jOl7L9oM/0.jpg)](https://www.youtube.com/watch?v=9n0jOl7L9oM)

## Criando dependências
Antes de resolver as dependências, precisamos entender o que são dependências. E o código abaixo ilustra bem esta visão, note que temos uma dependência do `ICustomerRepository`.

```csharp
public class OrderController : Controller
{
    private readonly ICustomerRepository _repository;

    public OrderController(ICustomerRepository repository)
    {
        _repository = repository;
    }
}
```

Isto ocorre pois no **construtor** do nosso controlador, explicitamos a necessidade de uma abstração ou implementação externa, tornando impossível instanciar um `OrderController` sem informar um `ICustomerRepository`.

Acredite se quiser, mas isto é tudo o que precisamos para criar uma dependência no ASP.NET. Porém agora vem o segundo problema... resolvê-las.

## Resolvendo dependências
Existem três formas de resolver dependências no ASP.NET, **AddTransient**, **AddScoped** e **AddSingleton**, e todas estão ligadas ao tempo de vida do objeto resolvido.

Em todos os cenários, iremos resolver as dependências no `Program.cs`, antes do `builder.Services.AddControllers` por exemplo.

```csharp
builder.Services.AddTransient<IInterface, Classe>();
builder.Services.AddScoped<IInterface, Classe>();
builder.Services.AddSingleton<IInterface, Classe>();
```
A resolução sempre será dada pela abstração (Interface) seguida da implementação (Classe concreta), apenas pela classe concreta ou por uma nova instância do objeto.

```csharp
builder.Services.AddTransient<Classe>();
builder.Services.AddScoped(new Classe());
```

## AddTransient
Embora a resolução seja parecida, o tempo de vida é bem diferente. No caso do **AddTransient** por exemplo, você **sempre terá uma nova instância** do objeto.

```csharp
builder.Services.AddTransient<ICustomerRepository, CustomerRepository>();

...

public class OrderController : Controller
{
    private readonly ICustomerRepository _repository;

    public OrderController(ICustomerRepository repository)
    {
        _repository = repository;
    }
}
```

Tomando como base o código acima, tendo o **AddTransient** como forma de resolução, **TODA VEZ** que você o controlador for instânciado, será gerada uma nova instância do repositório.

> Este cenário é ideal para quando queremos executar ações pontuais e já dispor o objeto, que normalmente é a maioria dos casos.

## AddScoped
O **AddScoped** trabalha de forma parecida com o **AddTransient** porém ele retém o objeto durante toda a requisição, e sempre que invocado, retorna o mesmo objeto.

```csharp
builder.Services.AddScoped<IDatabase, Database>();
builder.Services.AddTransient<ICustomerRepository, CustomerRepository>();
builder.Services.AddTransient<IProductRepository, ProductRepository>();

...

public class CustomerRepository : ICustomerRepository
{
    private readonly IDatabase _database;

    public CustomerRepository(IDatabase database)
    {
        _database = database;
    }
}

public class ProductRepository : IProductRepository
{
    private readonly IDatabase _database;

    public ProductRepository(IDatabase database)
    {
        _database = database;
    }
}

public class OrderController : Controller
{
    private readonly ICustomerRepository _customerRepository;
    private readonly IProductRepository _productRepository;

    public OrderController(
        ICustomerRepository customerRepository,
        IProductRepository productRepository)
    {
        _customerRepository = customerRepository;
        _productRepository = productRepository;
    }
}
```

Tomando como base o código acima, temos agora o **CustomerRepository** e **ProductRepository** no qual ambos dependem do **Database**.

A diferença é que o **Database** está sendo resolvido com **AddScoped** e os dois repositórios com **AddTransient**.

Isto nos diz que toda vez que o `OrderController` for instanciado, ele vai criar uma insância para **CustomerRepository** e outra para **ProductRepository**, mas apenas **UMA INSTÂNCIA** para o **Database**.

Uma vez que ele passe pelo construtor do **CustomerRepository** e resolva a dependência do **Database**, ele vai armazenar a instância na memória e toda requisição para **Database** retornará a mesma insância.

Isto termina no fim da requisição. Após enviar os dados para o client, o ASP.NET se encarrega de remover os objetos da memória.

[![Dominando Injeção de Dependência](https://baltaio.blob.core.windows.net/static/images/articles/dominando-injecao-de-dependencia-banner.jpg)](https://balta.io/cursos/dominando-injecao-de-dependencia)

## Singleton
Antes de falar do **AddSingleton** precisamos entender que **Singleton** também é o nome de um padrão, que garante que um objeto tem uma e apenas uma instância para a aplicação como um todo.

Felizmente esta é a mesma definição do que o **AddSingleton** faz aqui no ASP.NET.

## AddSingleton
O maior desafio no caso do **AddSingleton** é saber quando utilizá-lo, já que sempre teremos as mesmas informações (Mesma instância) do objeto para todos os usuários da aplicação.

Em um App Mobile ou Desktop, onde temos apenas um usuário ou até mesmo no Blazor WASM, isto não é um problema, mas na Web isto é diferente.

As aplicações ASP.NET ficam em constante execução, atendendo diferentes usuários, então **muito cuidado** ao utilizar o **Singleton**, já que os dados serão compartilhados entre todas as requisições.


```csharp
builder.Services.AddSingleton<Configuration>();

...


public class OrderController : Controller
{
    private readonly Configuration _config;

    public OrderController(Configuration config)
    {
        _config = config;
    }
}
```
Um bom exemplo de uso são as configurações da aplicação (Desde que não sejam por usuário), que podem ser carregadas uma única vez no início da aplicação e reutilizada posteriormente.

## Artigos Relacionados
* [Dependency Injection](https://balta.io/blog/dependency-injection)
* [ASP.NET Core Dependency Injection](https://balta.io/blog/aspnet-core-dependency-injection)
* [Vídeo - DI](https://www.youtube.com/watch?v=va9wX68lAfA)
