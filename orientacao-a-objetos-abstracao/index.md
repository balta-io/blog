Abstração é um dos conceitos mais importantes do paradigma orientado a objetos e também um de seus pilares.

Neste artigo vamos entender um pouco mais sobre abtrações no geral e como este conceito se aplica na orientação a objetos.

## Abstração
O conceito de abstração consiste em esconder os detalhes de algo, no caso, os detalhes desnecessários.

No mundo real, utilizamos abstrações o tempo todo. Tudo que não sabemos como funciona por baixo dos panos pode ser considerado uma abstração.

Para exemplificar melhor, vamos tomar como exemplo a concessionária que realiza manutenções no seu carro. Você leva ele até lá com um problema e ele volta funcionando.

Em suma, pouco importa os detalhes do que aconteceu durante a manutenção do seu carro, o que importa é que ele voltou funcionando.

## Abstração na orientação a objetos
Dentro da OOP (Object Oriented Programming -- Programação Orientada a Objetos) temos diversos conceitos de abtração, como por exemplo as interfaces e classes que escondam algo.

### Abstração na forma simples
Para começarmos com um exemplo mais simples, vamos tomar como base o seguinte código:

```csharp
public class Order 
{
    ...
    public decimal Total { get; set; }
    public void CalculateTotal() {...}
    public void AddItem(Item item)
    {
        Items.Add(item);
        CalculateTotal();
    }
    ...
}
```

Neste exemplo temos uma classe `Order` que representa um pedido, e dentro dela temos uma propridade chamada `Total` que define o valor total do pedido, e dois métodos, `CalculateTotal` e `AddItem`.

> **Listas** -> Para saber mais sobre listas, confira a aula "Listas" do curso [**Fundamentos da Orientação a Objetos**](https://balta.io/cursos/fundamentos-orientacao-objetos).

Quanto mais contato sua classe tem com o mundo externo, maiores são os impactos das mudanças quando algo for alterado nela.

Por exemplo, se esta classe estiver sendo utilizada em 20 outros lugares diferentes e você alterar a propriedade `Total` para `Amount`, geraria um grande refatoramento.

Embora em alguns cenários isto aconteça e seja necessário, quanto mais detalhes pudermos esconder sobre nossas classes, melhor.

Olhando novamente para o código, você identifica algo que poderia mudar?

```csharp
public class Order 
{
    ...
    public decimal Total { get; set; }
    public void CalculateTotal() {...}
    public void AddItem(Item item)
    {
        Items.Add(item);
        CalculateTotal();
    }
    ...
}
```

Se notarmos, o cálculo de total está sendo executado a cada item adicionado. E se pararmos para pensar melhor, não faz sentido alguém de fora acionar o método que calcula este pedido.

O pedido por si só, deve saber se gerenciar e com a chamada ao `CalculateTotal` já sendo feita no método `Add`, podemos simplesmente marca-lo como privado.

```csharp
public class Order 
{
    ...
    public decimal Total { get; set; }
    private void CalculateTotal() {...}
    public void AddItem(Item item)
    {
        Items.Add(item);
        CalculateTotal();
    }
    ...
}
```

Desta forma, privamos o acesso externo utilizando o modificador de acesso `private`, tornan o método `CalculateTotal` inacessível externamente.

> **Modificadores de acesso:** Todos os modificadores de acesso com exemplos práticos estão no curso [Fundamentos da Orientação a Objetos](https://balta.io/cursos/fundamentos-orientacao-objetos).


### Abstração por interfaces
As interfaces funcionam como contratos ([Módulo 1 - Aula 17 - Fundamentos da Orientação a Objetos](https://balta.io/cursos/fundamentos-orientacao-objetos)) que definem o que as implementações (Classes) devem conter.

Em suma as interfaces dizem "O que" e não "Como", sendo assim, podemos tomar o "Como" como os detalhes e o "O que" como uma abstração.

```csharp
public interface ICustomerRepository
{
    void Save(Customer customer);
}
```

Tomando como base a interface acima, tomamos ela como um contrato que diz que um `Customer` pode ser salvo, mas ela não diz **como** isto deve ser feito.

Este conceito de abstração por interfaces nos leva ao princípio DIP ([Veja mais sobre DI aqui](https://balta.io/artigos/dependency-injection)) que prega o seguinte:

> Sempre que puder, dependa de abstrações ao invés de implementaçõs.

Este simples processo resulta na possibilidade de criação de várias implementações de um mesmo contrato.

Vamos tomar como base esta primeira implementação, que seria uma versão do contrato que está direcionada ao uso do Entity Framework.

```csharp
public class CustomerRepository : ICustomerRepository
{
    public void Save(Customer customer)
    {
        _context.Customers.Add(customer);
        _context.SaveChanges();
    }
}
```

Porém, durante os testes de unidade, não podemos depender de dados externos, logo precisamos *simular* nosso repositório.

Sendo assim, podemos gerar uma outra implementação, baseada no contrato `ICustomerRepository` que vai *enganar* nossos testes.


```csharp
public class FakeCustomerRepository : ICustomerRepository
{
    public void Save(Customer customer)
    {
    }
}
```

Pronto, neste momento temos duas implementações (`CustomerRepository` e `FakeCustomerRepository`) se baseando no contrato `ICustomerRepository`.

## Aplicando a abstração na prática

Agora vamos para um cenário maior, vamos olhar para o objeto `OrderHandler` que manipula os pedidos.

Este objeto precisa se comunicar com a base de clientes para salvar os dados lá, porém, durante os testes, não teremos uma base para salvar.

Desta forma, nossos testes falhariam, ou teríamos que sempre preparar uma massa de testes (Que em diversas vezes toma tempo) para realizar esta execução.

A saída aqui é `Mockar`, ou seja, simular que fomos no banco de dados e algo aconteceu. Então, teremos as versões do `CustomerRepository` e `FakeCustomerRepository` sendo utilizadas.

> **Testes de unidade** -> Veja mais sobre Mocks e muitos testes de unidade no curso [Refatorando para Testes de Unidade](https://balta.io/cursos/refatorando-para-testes-de-unidade)

### Dependendo de abstrações
Nosso primeiro passo é depender de abstrações (`interfaces`) e não de implementações (`classes`). Este motivo deve-se ao fato de uma interface poder conter várias implementações, enquanto uma classe não tem essa habilidade.

```csharp
public class OrderHandler
{
    private readonly ICustomerRepository _repository;

    public OrderHandler(ICustomerRepository repository)
    {
        _repository = repository;
    }
}
```

### Realizando os testes

Tudo o que precisamos fazer agora é instanciar o `OrderHandler`, que nos obrigará a informar um `ICustomerRepository`.

A boa notícia é que tanto o `CustomerRepository` quanto o `FakeCustomerRepository` são compatíveis, mas neste cenário vamos ficar com o falso.

```csharp
[TestMethod]
public void Test01()
{
    var repo = new FakeCustomerRepository();
    var handler = new OrderHandler(repo);
}
```

### Utilizando nos Controllers

Caso estivessemos em um cenário Web com MVC, podemos utilizá-los também nos controladores.

```csharp
public IActionResult Get([FromServices]ICustomerRepository repo)
{
    ...
    repo.Save(customer);
    ...
}
```

#### Injeção de dependência
Neste momento você deve estar se perguntando como ele vai saber qual implementação chamar, a `CustomerRepository` ou a `FakeCustomerRepository`.

O que vai definir isto é a condiguração do Service Locator na sua aplicação.

```csharp
services.AddTransient<ICustomerRepository, CustomerRepository>();
```

Desta forma o ASP.NET sabe qual implementação utilizar, dada uma interface.

## Conclusão
Mesmo um assunto simple e básico, gera umas boas reflexões. É importante revisitar conteúdos de tempos em tempos para garantir que temos domínio sobre o assunto, ou que precisamos aprender mais.

Espero que tenham gostado!