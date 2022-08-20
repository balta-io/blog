O DIP ou Dependency Inversion Principle (Princípio da Inversão de Dependência) prega que devemos depender de abstrações e não de implementações.

## Dependency Inversion Principle
O princípio da inversão de dependência diz que devemos depender de abstrações (interfaces) ao invés de implementações (classes concretas) a fim de ter um menor acoplamento entre as camadas do sistema.

Embora esteja relacionado a DI ([Dependency Injection](https://balta.io/blog/dependency-injection)) e IoC ([Inversion of Control](https://balta.io/blog/inversion-of-control)) o DIP é diferente em diversos aspectos.

## Dependency Inversion Principle e DI
DI ([Dependency Injection](https://balta.io/blog/dependency-injection)) é uma técnica que auxilia na implementação de outro princípio de design chamado IoC. O DIP se relaciona diretamente com DI, pois podemos usufruir da técnica para gerar dependências (Neste caso de abstrações).

### Dependency Injection sem Dependency Inversion Principle
Podemos ter DI sem necessariamente ter o DIP, como mostrado no exemplo abaixo. Neste caso, estamos dependendo de uma abstração concreta e não de uma implementação.

Isto resolve o problema de reuso de código que poderia ser causado pela responsabilidade de ler o cliente do banco estar no controlador, mas ainda temos um problema aqui.

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

Neste caso, como dependemos de uma implementação concreta, não temos a flexibilidade para ter diversas implementações deste repositório.

[![Dominando Injeção de Dependência](https://baltaio.blob.core.windows.net/static/images/articles/dominando-injecao-de-dependencia-banner.jpg)](https://balta.io/cursos/dominando-injecao-de-dependencia)

## Dependency Inversion Principle e testes de unidade
Mas por que ter diversas implementações de uma mesma interface? Um cenário simples e objetivo são os **testes de unidade**, cujo não devem depender de itens externos.

Sendo assim, no exemplo anterior, temos uma clara dependências do SQL Server, visto que nossos clientes estão armazenados lá. Mas como podemos executar testes sem depender do SQL Server então?

**Mocks** ou **Fakes**, ou seja, falsificar uma implementação, fingir que fomos no banco e recuperamos o cliente. Este cenário só é possível mediante a implementação do DIP.

## Dependency Inversion Principle na prática
A implementação do DIP é relativamente simples, basta criarmos uma interface `ICustomerRepository` e fazer com que nosso controlador dependa dela e não da implementação concreta (`CustomerRepository`).

```csharp
public interface ICustomerRepository
{
    Task<Customer?> GetByIdAsync(Guid id);
}
```

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
}
```
Como as assinaturas são as mesmas, o código no controlador (Exceto a depend6encia) não muda.

## Múltiplas implementações
Como vantagem temos a possibilidade de ter múltiplas implementações desta abstração, como no caso `CustomerRepository` e `FakeCustomerRepository`.

```csharp
public class CustomerRepository : ICustomerRepository
{
    public async Task<Customer?> GetByIdAsync(Guid id)
    {
        var using (var conn = new SqlConnection("CONN_STRING"))
        const query = "SELECT * FROM CUSTOMER WHERE ID=@id";
        return customer = conn.FirstOrDefaultAsync<Customer>(query, new { id = Id });
    }
}
```

```csharp
public class FakeCustomerRepository : ICustomerRepository
{
    public async Task<Customer?> GetByIdAsync(Guid id)
    {
        await Task.Delay(0);
        return new Customer("André", "Baltieri");
    }
}
```
## Testes de Unidade
Como nosso controlador depende de uma abstração `ICustomerRepository`, ele aceita tanto um `CustomerRepository` quanto um `FakeCustomerRepository` como entrada, nos permitindo **mockar** os testes.

```csharp
[TestMethod]
public void ShouldPlaceAnOrder() {
    IDeliveryService service = new FakeDeliveryService();
    var controller = new OrderController(service);
    ...
}
```

Desta forma aplicamos o DIP e ainda temos testes livres de dependências externas.

## Conclusão
Dependency Inversion Principle é um dos cinco princípios do SOLID, que tem uma implementação relativamente fácil e que nos auxilia a desacoplar camadas do sistema.
