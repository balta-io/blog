## Consumindo API's de forma simplificada com Refit e ASP NET 5

Quando estamos trabalhando com uma aplicação que consome e consolida dados, sempre pensamos no conceito de repositórios, seja ele um banco de dados relacional como o SQL Server ou alguma base não relacional como o MongoDB.
Porém existem cenários na qual o repositório provedor desses dados pode ser uma API externa. Nesse caso precisamos realizar os procedimentos de conexão HTTP, criarmos diversos objetos para depois realizarmos o consumo desse provedor. 
O Refit chega justamente para facilitar a nossa vida na hora da criação desses procedimentos, nesse artigo vamos ver que com poucas linhas de código iremos consumir uma API externa por meio de uma API ASP NET 5.


### O que é Refit ?
O Refit é uma biblioteca para .NET desenvolvida para facilitar o consumo de API's REST (HTTP Client), a ideia é fornecer interfaces que abstraem a comunicação com os serviços HTTP possibilitando a escrita de um código clean e de baixo acoplamento usando poucas linhas de código.

### Iniciando o projeto
Nesse exemplo utilizaremos uma simples WebApi utilizando o dotnet 5.

Para criar o projeto basta selecionar a pasta destino e utilizar o comando :
```
dotnet new webapi --framework net5.0
```
Agora basta abrir o projeto com seu editor ou IDE favorito.

### Pacotes necessários

Iremos instalar dois pacotes para usarmos nesse exemplo:
* Refit
* Refit.HttpClientFactory

Via CLI do dotnet podemos usar os seguintes comandos:
```
dotnet add package Refit
```

```
dotnet add package Refit.HttpClientFactory
```

>Você pode instalá-los via gerenciador de pacotes do *Nuget* caso esteja utilizando a IDE do Visual Studio

Com o projeto criado e os pacotes instalados podemos iniciar.

### Tipos de dados
O nosso primeiro passo será o entendimento do tipos de dados que utilizaremos na API na qual iremos consumir.
Nesse exemplo eu criei uma API externa que irá retornar e cadastrar carros, teremos um método ``Get`` e um método ``Post`` que responde no endpoint ``http://localhost:3000/v1/cars/``


O metodo ``Get`` nos retorna o seguinte JSON:
```j
{
"id": "guid",
"createdAt": "datetime",
"name": "string",
"price": double,
"year": int,
"description": "string",
"category": "string"
}
```

No método ``Post`` iremos enviar no corpo da requisição o seguinte JSON:
```j
{
"name": "string",
"price": double,
"year": int,
"description": "string",
"category": "string"
}
```
e como resultado receberemos um resultado personalizado com os dados do carro que cadastramos:
```j
{
"statuscode": int,
"message": "string",
"isOk": bool,
"data": {
	"id": "guid",
	"createdAt": "datetime",
	"name": "string",
	"price": double,
	"year": int,
	"description": "string",
	"category": "string",
	"_listNotifications": []
	}
}
```

Agora que conhecemos o endereço, os métodos e os tipos de dados da API que iremos consumir, podemos iniciar a construção da nossa aplicação.

### Criando um objeto de transferência de dados
Os objetos de transferencia de dados (DTO) são estruturas sem comportamento algum na qual utilizamos apenas para transportar dados de um lugar para o outro. Como conhecemos bem a estrutura que iremos retornar da API, podemos criar um tipo bem definido para recebermos o dado.

Vamos criar um novo ``struct`` chamado ``CarDTO.cs`` e nele iremos definir as propriedades do nosso carro. 

O código ficará da seguinte forma:
``` csharp
using  System;

namespace  RefitExample.DTO
{
	public  struct CarDTO
	{
		public  Guid  Id { get; set; }
		public  string  Name { get; set; }
		public  string  Description { get; set; }
		public  double  Price { get; set; }
		public  int  Year { get; set; }
		public  string  Category { get; set; }
		public  DateTime  CreatedAt { get; set; }
	}
}
```

### Criando um modelo de dado
Agora vamos criar uma classe chamada ``CarModel`` que servirá para enviarmos para a API (no método POST). 

O código dessa classe ficará dessa forma:
``` csharp
namespace  RefitExample.Models
{
	public  class  CarModel
	{
		public  string  Name { get; set; }
		public  string  Description { get; set; }
		public  double  Price { get; set; }
		public  int  Year { get; set; }
		public  string  Category { get; set; }
	}
}
```

>Diferentemente de um DTO que serve apenas para tipar e trafegar o retorno de um repositório, um modelo de dados ou entidade pode ter comportamentos como por exemplo validar campos, assinar contratos e especificações etc. 
>O recomendado é sempre verificar a integridade dos dados que estamos inserindo em um repositório, não faremos isso nesse exemplo pois a ideia é apenas apresentar a ferramenta Refit.

### Tipando a resposta da requisição
Como vimos no retorno da API externa, quando fazemos um ``Post`` para criarmos um carro recebemos uma resposta personalizada com o resultado da requisição, vamos criar um struct chamado ``Result.cs`` para tiparmos esse resposta.

O código ficará dessa forma:
``` csharp
namespace  RefitExample.Results
{
	public struct Result
	{
		public  int  StatusCode { get; set; }
		public  string  Message { get; set; }
		public  bool  IsOk { get; set; }
		public  object  Data { get; set; }
	}
}
```
Pronto! Agora com todos os dados tipados, começaremos a consumir a nossa API externa.


### Criando a interface de comunicação com a API
Como mencionado anteriormente, o ``Refit`` nos fornecem interfaces para comunicação e interação com a API que queremos consumir. 

Então vamos criar uma interface chamada ``ICarRepository`` que irá utilizar o namespace do Refit.
```csharp
using  System.Threading.Tasks;
using  Refit; // < Referencia ao Refit
using  RefitExample.DTO;
using  RefitExample.Models;
using  RefitExample.Results;

namespace  RefitExample.Repositories.Interfaces
{
	public  interface  ICarRepository
	{
		[Get("/v1/cars")]
		Task<CarDTO> ReturnCar();

		[Post("/v1/cars")]
		Task<Result> InsertCar(CarModel  car);
	}
}
```
Analizando esse código percebemos que temos uma anotação nos métodos dessa interface, é justamente isso que o Refit nos fornece, podemos utilizar outras anotações como ``[Delete]`` e ``[Put]`` também, porém, o importante é entendermos essas anotações que basicamente seguem o padrão ``[MétodoHTTP("rota")]``.


### Registrando o serviço
No arquivo ``startup.cs`` da nossa aplicação iremos adicionar o ```AddRefitClient()``` via interface ``IServiceCollection`` no método ``ConfigureServices`` para utilizarmos a injeção de dependência posteriormente.

O método ficará dessa forma:
```csharp
public  void  ConfigureServices(IServiceCollection  services)
{
	services.AddControllers();
	services.AddRefitClient<ICarRepository>().ConfigureHttpClient(c =>
	{
		c.BaseAddress = new  Uri("http://localhost:3000/");
	});
	
	services.AddSwaggerGen(c =>
	{
		c.SwaggerDoc("v1", new  OpenApiInfo { Title = "RefitExample", Version = "v1" });
	});
}
```
Notem que estamos informando a interface que acabamos de criar no método ``AddRefitClient`` além de estarmos configurando o endereço base da API para ``http://localhost:3000/`` como vimos anteriormente.

>Podem acreditar, isso é tudo o que precisamos para consumirmos uma API externa com o Refit.

### Criando um controlador para nossa API ASP NET
O último passo é criarmos um ``controller`` para fazermos as requisições pela nossa API.

Vamos criar uma classe chamada ``CarController.cs`` e nele teremos os mesmos dois métodos da API externa, um ``Get`` e um ``Post``.

O código ficará dessa forma:
``` csharp
using  System.Threading.Tasks;
using  Microsoft.AspNetCore.Authorization;
using  Microsoft.AspNetCore.Mvc;
using  RefitExample.DTO;
using  RefitExample.Models;
using  RefitExample.Repositories.Interfaces;
using  RefitExample.Results;

namespace  RefitExample.Controllers
{
	[Route("api/[controller]")]
	[AllowAnonymous]
	public  class  CarController : ControllerBase
	{
		[HttpGet]
		public  Task<CarDTO> GetCars([FromServices] ICarRepository  repository)
		{
			return  repository.ReturnCar();
		}
		
		[HttpPost]
		public  Task<Result> PostCar([FromBody] CarModel  car, 
		[FromServices] ICarRepository  repository)
		{
			return  repository.InsertCar(car);
		}
	}
}
```
> Notem que em momento algum nós implementamos a interface ICarRepository e nem criamos instâncias de HttpClient, o Refit fará tudo isso por nós em tempo de runtime

*Nossos métodos seguem o padrão de um controller padrão do ASP NET MVC.*

No método ``GetCars`` estamos solicitando um instância do ICarRepository utilizando o ``[FromServices]`` e no método ``Post`` além do citado anteriormente, estamos solicitando um ``CarModel`` no corpo da requisição.

O mais interessante de tudo é que não estamos informando em momento algum que iremos passar o ``CarModel`` no corpo da requisição da API externa, o Refit já sabe disso e irá fazer o trabalho por nós.

### Executando a aplicação
Agora podemos executar a nossa aplicação com o comando:
```
dotnet run
```
Vamos acessar a interface do ``Swagger`` no caminho padrão de uma aplicação ASP NET 5 (``https://localhost:5001/swagger/index.html``) e veremos a interface gráfica:

![Requisição](https://github.com/diegostan/blog/blob/master/aspnet-refit2/images/swagger1.png?raw=true)

### Solicitando o método Get

Vamos executar o método ``Get``  para obtermos a resposta da API externa:

![Requisição](https://github.com/diegostan/blog/blob/master/aspnet-refit2/images/swagger3get.png?raw=true)
> Tivemos o retorno exatamente como o DTO que modelamos anteriormente

### Solicitando o método Post
Para executarmos o método ``Post`` precisamos passar no corpo da requisição da nossa API o ``CarModel`` :
```j
{
  "name": "",
  "description": "",
  "price": 0,
  "year": 0,
  "category": ""
}
```
Agora vamos executar o método para vermos a resposta:
![Requisição](https://github.com/diegostan/blog/blob/master/aspnet-refit2/images/swagger1post.png?raw=true)

Por mais que a requisição tenha sido realizada com sucesso, dentro da nossa API externa temos um tratamento de erro, recebemos no nosso retorno personalizado um  ``statusCode 400 bad request`` e algumas informações sobre o preenchimento dos campos.

Vamos corrigir a requisição conforme as mensagens que recebemos:
```j
{
  "name": "Opala", 
  "description": "Chevrolet Opala 4.1S 2p",
  "price": 50000,
  "year": 1988,
  "category": "Cupe de luxo"
}
```
Enviando a requisição corrigida temos o seguinte resultado:
![Requisição](https://github.com/diegostan/blog/blob/master/aspnet-refit2/images/swagger3post.png?raw=true)

Agora recebemos o ``statusCode 201 created`` e a mensagem de *Carro cadastrado com sucesso*. 

Com isso finalizamos nossa aplicação.

### Considerações finais
Existem ferramentas fantásticas no mundo do .NET, graças à pessoas que contribuem diariamente e criam formas de facilitar a vida de todos os desenvolvedores. 
O **Refit** sem dúvida alguma é o pacote que eu mais gosto quando se trata de ASP NET, nós vimos nesse artigo como o trabalho de consumir uma API externa fica extremamente simples com essa ferramenta. 

Espero que tenham gostado e até a próxima!


### Fontes
Contribua com melhorias para o Refit, segue o link do código fonte:
https://github.com/reactiveui/refit

O projeto utilizado nesse artigo está disponível em:
https://github.com/diegostan/RefitExample.git