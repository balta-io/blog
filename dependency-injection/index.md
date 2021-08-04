Dependency Injection (Injeção de dependência) ou DI é a técnica que implementa o padrão IoC que veremos mais abaixo neste artigo. 

Quando falamos em arquitetura de software, independente da nossa aplicação ser backend, frontend ou mobile, sempre queremos algo parecido, a organização.

Organizar bem as aplicações permite testes melhores, facilita encontrar bugs e principalmente crescer. Buscamos, quando novas funcionalidades forem adicionadas, que nossa aplicação não resulte em um Frankstrein, toda remendada.

Mas o que a **Injeção de Dependência (Dependency Injection)** tem a ver com isto? O **IoC**, **DIP** e **Service Locator** tem em comum com ela?

Vamos detalhar tudo aqui neste artigo.

## Um mal exemplo

Provavelmente você deve ter ouvido falar que nossas aplicações devem conter um baixo acoplamento entre os módulos, mas o que isto significa na prática?

Vou tomar como base um código do [curso 7182 - Refatorando para testes de unidade](https://balta.io/cursos/refatorando-para-testes-de-unidade). O código é extenso, mas dê uma boa olhada nele.

```csharp
public class OrderController : Controller
    {
        [Route("v1/orders")]
        [HttpPost]
        public async Task<string> Place(string customerId, string zipCode, string promoCode, int[] products)
        {
            // #1 - Recupera o cliente
            Customer customer = null;
            using (var conn = new SqlConnection("CONN_STRING"))
            {
                customer = conn.Query<Customer>
                    ("SELECT * FROM CUSTOMER WHERE ID=" + customerId)
                    .FirstOrDefault();
            }

            // #2 - Calcula o frete
            decimal deliveryFee = 0;
            var request = new HttpRequestMessage(HttpMethod.Get, "URL/" + zipCode);
            request.Headers.Add("Accept", "application/json");
            request.Headers.Add("User-Agent", "HttpClientFactory-Sample");

            using (HttpClient client = new HttpClient())
            {
                var response = await client.SendAsync(request);
                if (response.IsSuccessStatusCode)
                {
                    deliveryFee = await response.Content.ReadAsAsync<decimal>();
                }
                else
                {
                    // Caso não consiga obter a taxa de entrega o valor padrão é 5
                    deliveryFee = 5;
                }
            }

            // #3 - Calcula o total dos produtos
            decimal subTotal = 0;
            for (int p = 0; p < products.Length; p++)
            {
                var product = new Product();
                using (var conn = new SqlConnection("CONN_STRING"))
                {
                    product = conn.Query<Product>
                        ("SELECT * FROM PRODUCT WHERE ID=" + products[p])
                        .FirstOrDefault();
                }
                subTotal += product.Price;
            }

            // #4 - Aplica o cupom de desconto
            decimal discount = 0;
            using (var conn = new SqlConnection("CONN_STRING"))
            {

                var promo = conn.Query<PromoCode>
                    ("SELECT * FROM PROMO_CODES WHERE CODE=" + promoCode)
                    .FirstOrDefault();
                if (promo.ExpireDate > DateTime.Now)
                {
                    discount = promo.Value;
                }
            }

            // #5 - Gera o pedido
            var order = new Order();
            order.Code = Guid.NewGuid().ToString().ToUpper().Substring(0, 8);
            order.Date = DateTime.Now;
            order.DeliveryFee = deliveryFee;
            order.Discount = discount;
            order.Products = products;
            order.SubTotal = subTotal;

            // #6 - Calcula o total
            order.Total = subTotal - discount + deliveryFee;

            // #7 - Retorna
            return $"Pedido {order.Code} gerado com sucesso!";
        }
    }
```

Bom, você deve ter notado e até se sentiu incomodado com o tanto de coisa que esta ação do controlador realiza. Isto claramente fere o S (Single Responsibility Principle) do SOLID.

Isto ocorre por termos diversas responsabilidades, que poderiam (Deveriam) ser externalizadas, no mesmo método:
* Conxão com banco de dados
* Requisição para API externa
* Cálculo de regra de negócio

Como podemos melhorar o código visto anteriormente? Na verdade talvez você até saiba o que precisa ser feito, mas não como fazer.

Precisamos de fato separar as coisas, manter a conexão com o banco, fora daquele método por exemplo, afinal, diversos outros métodos utilizam conexão com o banco.

Basicamente, não queremos escrever o mesmo código em dois ou mais lugares. Sempre que isto acontece, é legal externalizar este código e fazer uma chamada a ele apenas.

Com isto, definimos também o **SPOF** (Single Point of Failure -- Ponto único de falha). 

Atingindo este objetivo, sempre que houver algum problema com conexão ao banco por exemplo, sabemos que será naquela classe, naquele código, pois ele só está lá, os demais lugares apenas fazem uso dele.

## Divisão de responsabilidades

Claro que a injeção de dependência (Dependency Injection) não é a solução para tudo, mas fica mais fácil explicar quando estamos alinhados. Então, partindo deste código, o que e como você mudaria?

Vamos dar nosso primeiro passo e separar por exemplo, a chamada a API de cálculo de frete da nossa aplicação.

```csharp
public class DeliveryService {
    public decimal GetDeliveryFee(string zipCode) {
        var request = new HttpRequestMessage(HttpMethod.Get, "URL/" + zipCode);
        request.Headers.Add("Accept", "application/json");
        request.Headers.Add("User-Agent", "HttpClientFactory-Sample");

        using (HttpClient client = new HttpClient())
        {
            var response = await client.SendAsync(request);
            if (response.IsSuccessStatusCode)
            {
                deliveryFee = await response.Content.ReadAsAsync<decimal>();
            }
            else
            {
                deliveryFee = 5;
            }
        }
    }
}
```

Neste caso, criamos uma classe <code>DeliveryService</code> que contém o método <code>GetDeliveryFee</code> para obter o valor do frete de acordo com o CEP do usuário.

Agora podemos chamar este serviço em nosso controller.

```csharp
public class OrderController : Controller
    {
        [Route("v1/orders")]
        [HttpPost]
        public async Task<string> Place(string customerId, string zipCode, string promoCode, int[] products)
        {
            ...
            var deliveryService = new DeliveryService();
            decimal deliveryFee = deliveryService.GetDeliveryFee(zipCode);
            ...
        }
    }
```

Embora a refatoração e reuso da obtenção do valor do frete tenha sido feita, ainda temos um alto acoplamento a ele neste código.

Sabemos que os testes de unidade NÃO PODEM depender de serviços externos, então como você realizaria um teste deste método?

## O que é Dependency Injection?

Dependency Injection (Injeção de dependência) ou DI é a técnica que implementa o padrão IoC que veremos mais abaixo neste artigo.

O ideal neste cenário, é que o nosso controlador dependa de serviço de cálculo de frete, mas que não seja responsável por gerenciá-lo.

Contextualizando, você chega na empresa, no seu primeiro dia de trabalho e precisa de um computador para trabalhar. Quem vai te prover isto? Como isto chega até você? Isto não é sua responsabilidade (Pelo menos não deveria ser).

Voltamos ao S do SOLID. Qual a responsabilidade da ação de realizar um pedido? Realizar um pedido!!!!

Para realizar um pedido, precisamos calcular o frete, e para calcular o frete precisamos do serviço de cálculo de fretes.

Notou como nosso código criou dependências? Notou que quanto mais separamos as responsabilidades, mais evidente fica o uso de dependências?

## IoC

Vamos dar mais um passo adiante e refatorar nosso código, explicitando a dependência do serviço, e isto será feito com base em um princípio chamado IoC (Inversion of Control) ou inversão de controle.

No IoC nós externalizamos as responsabilidade invertendo o controle. Ao invés do controlador ser responsável pelo controle do serviço de frete, ele apenas depende dele.

Podemos notar o uso do IoC sempre que vemos classes ou interfaces sendo passadas no método construtor.

```csharp
public class OrderController : Controller
{
    private readonly DeliveryService _deliveryService;

    OrderController(DeliveryService deliveryService) {
        _deliveryService = deliveryService;
    }

    [Route("v1/orders")]
    [HttpPost]
    public async Task<string> Place(string customerId, string zipCode, string promoCode, int[] products)
    {
        ...
        decimal deliveryFee = _deliveryService.GetDeliveryFee(zipCode);
        ...
    }
}
```

O que fizemos foi mover o <code>DeliveryService</code> para o método <code>construtor</code> do nosso controlador, criando uma dependência do mesmo (invertendo o controle).

Neste momento, toda vez que o controlador for instanciado, será obrigatório informar um serviço de frete. Como este serviço será fornecido? De onde ele vem? Isto não é responsabilidade do controlador, teremos de resolver depois.

Nosso código deu um passo adiante, e como você pode imaginar, dependências vão ocorrer o tempo todo, em todo nosso código.

Em uma aplicação simples por exemplo, temos casos como o controlador depender de um serviço, que depende de um repositório, que depende de um contexto de dados, que depende de uma SqlConnection e por aí vai.

## Testando o controlador

Agora vamos melhorar ainda mais nosso código. Vamos criar testes de unidade para o método <code>Place</code> por exemplo.

```csharp
[TestMethod]
public void ShouldPlaceAnOrder() {
    var service = new DeliveryService();
    var controller = new OrderController(service);
    ...
}
```

Opa, espera um minuto. Existe uma premissa nos testes de unidade, que não devemos depender de itens externos, como banco de dados e APIs por exemplo.

A publicação do nosso código provavelmente será automatizada, o que significa que por exemplo um Job do GitHub fará o build, publish e deploy.

Durante este processo, temos a parte de CI (Continuous Integration) do DevOps, onde queremos por exemplo, executar TODOS os testes de unidade para ver se nada quebrou.

Os testes de unidade servem justamente para garantir a integridade da nossa aplicação, ou seja, se eles falharem, nossa aplicação não será publicada.

Agora o que acontece se durante a execução dos testes, a API de cálculo de frete estiver fora do ar? E se os dados no banco de origem do deploy (Produção) forem diferentes dos locais fazendo meus testes falharem?

Além disso, em ambientes maiores, o tempo de deploy das aplicações é importante e se dependemos de serviços externos, isto é muito afetado.

Imagina o deploy e execução destes testes em 20, 30 máquinas ao mesmo tempo? Por estes e outros motivos não devemos depender de itens externos nos testes.

## DIP

Você deve estar se perguntando, como resolver este problema então? Bem, a resposta está novamente no SOLID, mais especificamente na letra D.

O DIP (Dependency Inversion Principle) ou princípio da inversão de dependência prega que devemos depender de abstrações e não de implementações.

Abstrações são contratos, cascas que escondem os detalhes de sua execução. Por exemplo neste momento estou sentado na concessionária esperando meu carro ser revisado.

Neste caso, eles são a abstração da mecânica automóvel para mim. Eu não sei o que está acontecendo lá dentro, o que estão fazendo ou mesmo o que precisa ser feito, eu apenas trago meu carro e ele sai novo.

O DIP é um princípio de design que complementa o IoC. Vamos então realizar sua implementação, e nosso primeiro passo é criar uma abstração do nosso serviço.

```csharp
public interface IDeliveryService {
    decimal GetDeliveryFee(string zipCode);
}
```

O que fizemos foi criar um contrato, uma interface, uma casca, que contém apenas o que um serviço de entrega precisa conter, mas não o que de fato ele faz.

Devemos então refatorar nosso serviço, implementando a interface <code>IDeliveryService</code> que acabamos de criar.

```csharp
public class DeliveryService : IDeliveryService {
    public decimal GetDeliveryFee(string zipCode) {
        ...
    }
}
```

Este procedimento não muda nada nosso controlador e os testes, ainda precisamos refatorá-los para depender das abstrações e não das implementações.

### Refatorando o controlador

Agora que temos a interface, podemos depender dela ao invés da classe no construtor do controlador, aplicando o DIP.

```csharp
public class OrderController : Controller
{
    private readonly IDeliveryService _deliveryService;

    OrderController(IDeliveryService deliveryService) {
        _deliveryService = deliveryService;
    }
    ...
}
```

Parece uma mudança simples e sem expressão, correto? Mas veja o impacto disso nos testes.

### Mockando o serviço

Se não devemos depender de serviços externos e nosso controlador depende de um serviço de frete, como resolver este problema?

A saída é criar um serviço falso, que simula a ida a API externa por exemplo. Chamamos estes de **Mocks**, e embora hajam frameworks para isto, vamos criar um para teste aqui.

```csharp
public FakeDeliveryService : IDeliveryService {
    public decimal GetDeliveryFee(string zipCode) {
        return 10;
    }
}
```

No caso, sempre retornaremos o valor <code>10</code>, mas você pode usar sua imaginação aqui para atender as necessidades dos testes.

### Refatorando os testes

Tendo o <code>FakeDeliveryService</code> como sendo um <code>IDeliveryService</code>, como dependemos da abstração e não da implementação, podemos injetar ele no controlador agora.

```csharp
[TestMethod]
public void ShouldPlaceAnOrder() {
    IDeliveryService service = new FakeDeliveryService();
    var controller = new OrderController(service);
    ...
}
```

Desta forma, não só desacoplamos nosso serviço de cálculo de frete do controlador, como também permitimos que ele seja mockado e o controlador seja testável.

Entende como DI é apenas um pedacinho no topo do Iceberg?

## Service Locator

Nosso trabalho ainda não terminou. Na verdade nós geramos mais trabalho, mas a boa notícia é que vamos automatizar ele.

Todas as dependências que geramos, e no mundo real não serão poucas, precisam ser resolvidas em algum momento.

Toda vez que dizemos "isto não é problema meu", o problema não deixa de existir, só passa para um lugar diferente.

Convenhamos que seria suicídio resolver todas estas dependências manualmente. Imagina se houvesse uma nova implementação de um contrato? Teria uma refatoração imensa.

Para isto, existe o **Service Locator**, um padrão que nos auxilia a descobrir a implementação de cada interface e instanciar ela.

Os **Service Locators** também são comumente encontrados como **IoC Containers**, que são suas implementações, sendo as mais comuns o Unit e Ninject.

O ASP.NET traz uma implementação padrão que resolve nossas dependências, e sinceramente, atende muito bem a maioria dos cenários.

Particularmente, faz um bom tempo que não uso um pacote externo para resolver as dependências.

Para resolver as dependências utilizando o padrão do ASP.NET, basta adicionar o <code>AddTransient</code>, <code>AddScoped</code> ou <code>AddSingleton</code> no método <code>ConfigureServices</code> do <code>Startup</code>.

```csharp
public class Startup
{
    ...
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddTransient<IDeliveryFeeService, DeliveryFeeService>();
        ...
    }
    ...
}
```

Para saber mais sobre DI no ASP.NET Core, [veja este artigo](https://balta.io/blog/aspnet-core-dependency-injection) que publiquei há um tempo atrás.

## Fontes
* [ASP.NET Core DI](https://docs.microsoft.com/pt-br/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-3.1)
* [Macoratti](http://www.macoratti.net/11/07/ioc_di1.htm)
* [gago.io](https://gago.io/blog/ioc-di-voce-esta-fazendo-isso-errado/)
* [Tutorials Teacher](https://www.tutorialsteacher.com/ioc/introduction)

<div role="main" id="blog-s1-dotnet-134e3db1eea6c9829db1"></div>
<script type="text/javascript" src="https://d335luupugsy2.cloudfront.net/js/rdstation-forms/stable/rdstation-forms.min.js"></script>
<script type="text/javascript"> new RDStationForms('blog-s1-dotnet-134e3db1eea6c9829db1', 'UA-48664517-12').createForm();</script>