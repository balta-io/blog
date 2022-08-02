IoC é um princípio de design que prega uma melhor plugabilidade, testabilidade, usabilidade e baixo acoplamento.

## Inversion of Control
IoC ou inversão de controle (Inversion of Control) é um princípio de software que inverte o fluxo de controle em comparação a outros fluxos tradicionais como por exemplo a programação procedural.

No IoC, os pedaços de software que criamos recebem o fluxo e não ao contrário. Então, enquanto a programação procedural por exemplo, expressa diretamente o precisa ser feito, no IoC nós delegamos para libs externas.

### Princípio Hollywoodiano

Este conceito também é conhecido **Princípio Hollywoodano** que diz o seguinte: "Não nos chame, nós chamamos você".

Esta brincadeira, reflete diretamente o que gostaríamos de ter em nosso código, pequenos pedaços, que agem de form independente e podem ser utilizados em diversas partes do nosso código. Estes pedaços não "chamam" nosso código... nós chamamos eles, quando precisamos deles.

### História
Inversion of Control ou IoC é utilizada para aumentar a modularidade dos sistemas, criar extensibilidade e usufruir melhor da OOP (Programação Orientada a Objetos). O termo inicial foi utilizado por Michael Mattson mas ficou popular em 2004 após citado por Martin Fowler e Robert C. Martin (Uncle Bob).

## Inversion of Control e Dependency Injection
Embora esteja relacionado com DI (Dependency Injection -- Injeção de Dependência) e DIP (Dependency Inversion Principle -- Princípio da inversão de dependência), o IoC (Inversion of Control) se difere de ambos.

DI é a técnica que utilizamos para alcançar o IoC e DIP provê um maior desacoplamento entre as camadas visando depender mais de abstrações do que de implementações.

## Inversion of Control na prática
Para entender melhor este princípio, vamos tomar como base o código abaixo, que por algum motivo precisa recuperar o cliente do banco e faz esta chamada de forma direta.

```csharp
public class OrderController : Controller
{
    [Route("v1/orders")]
    [HttpPost]
    public async Task<string> Place(PlaceOrderCommand command)
    {
        // #1 - Recupera o cliente
        Customer customer = null;
        using (var conn = new SqlConnection("CONN_STRING"))
        {
            const query = "SELECT * FROM CUSTOMER WHERE ID=@id";
            customer = conn.Query<Customer>
                (query, new { id = command.CustomerId })
                .FirstOrDefault();
        }
        ...
    }
```

[![Masterclass Dependency Injection](https://baltaio.blob.core.windows.net/temp/banner_masterclass.png)](https://go.balta.io/masterclass-injecao-dependencia)

Se notarmos, este código está **acoplado** o que significa que não podemos reutilizar esta pesquisa por cliente em nenhum outro ponto do sistema. A manutenção também é difícil neste momento, pois precisamos lembrar de alterar **todo** lugar onde este código é chamado, e para finalizar a visibilidade é ruim, já que tem muita coisa para **se preocupar** acontecendo no momento.

A solução é externalizar este código e chamá-lo apenas quando precisamos. Desta forma temos um pedaço pequeno, reusável, fácil de ler, fácil de testar e de dar manutenção.

```csharp
public class CustomerRepository
{
    public async Task<Customer?> GetByIdAsync(Guid id)
    {
        var using (var conn = new SqlConnection("CONN_STRING"))
        const query = "SELECT * FROM CUSTOMER WHERE ID=@id";
        return customer = conn.FirstOrDefaultAsync<Customer>(query, new { id = Id });
    }
}
```

Com o código separado, podemos fazer uso do **Dependency Injection** (Injeção de dependência) para desacoplar o código do controlador.

```csharp
public class OrderController : Controller
{
    private readonly CustomerRepository _repository;

    public OrderController(CustomerRepository repository)
        => _repository = repository;

    [Route("v1/orders")]
    [HttpPost]
    public async Task<string> Place(PlaceOrderCommand command)
    {
        // #1 - Recupera o cliente
        var customer = await _repository.GetByIdAsync(command.CustomerId);
        ...
    }
```

Note que agora temos uma **dependência** do `CustomerRepository`, ou seja, não conseguimos criar uma instância do `OrderController` sem informar um `CustomerRepository`.

## Dependency Injection no .NET
Como resultado, precisamos **resolver** estas dependências para poder executar nosso código e o .NET possui uma implementação para isto, que também chamamos de **Service Locator**.

```csharp
builder.Services.AddTransient<CustomerRepository>();
```
> Vale lembrar que o .NET possúi vários métodos para gerenciar o ciclo de vida dos objetos, como AddScoped e AddSingleton

## Dependency Inversion
Neste cenário, o DIP se encaixaria criando um desacoplamento ainda maior entre o controlador e o reposiório, por meio de interfaces (Abstrações).

```csharp
public class OrderController : Controller
{
    private readonly ICustomerRepository _repository;

    public OrderController(ICustomerRepository repository)
        => _repository = repository;

    [Route("v1/orders")]
    [HttpPost]
    public async Task<string> Place(PlaceOrderCommand command)
    {
        // #1 - Recupera o cliente
        var customer = await _repository.GetByIdAsync(command.CustomerId);
        ...
    }
```

```csharp
builder.Services.AddTransient<ICustomerRepository, CustomerRepository>();
```

## Conclusão
IoC, DI e DIP são termos comumente visto juntos, mas que tem finalidades diferentes como pudemos ver. Enquanto um é a técnica, os outros são princípios de design que pregam obter um baixo acoplamento.

## Fontes
* [WikiPedia](https://en.wikipedia.org/wiki/Inversion_of_control)
* [Artigo: Dependency Inversion](https://balta.io/blog/dependency-injection)