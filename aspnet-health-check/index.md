
## Implementando Health Checks em uma aplicação ASP NET 5
Trabalhar com parques de aplicações distribuídas é uma tarefa desafiadora e que exige bastante atenção principalmente no monitoramento das aplicações. Antigamente tínhamos o costume de criar um endpoint que simplesmente nos retornava um `` statusCode 200 `` para verificarmos se a aplicação estava em funcionamento. Com o advento dos microsserviços principalmente, essa tarefa vem se tornando cada vez mais indispensável. A partir da versão 2.2 do ASP NET Core, temos a possibilidade de utilizar o Health Checks.

### O que é Health Checks ?
O Health Checks nada mais é que um middleware que nos fornecem um endpoint configurável que nos retorna o estado atual da aplicação.

### Iniciando o projeto
Nesse exemplo utilizaremos uma simples WebApi utilizando o dotnet 5.

Para criar o projeto basta selecionar a pasta destino e utilizar o comando :
```
dotnet new webapi --framework net5.0
```
Agora basta abrir o projeto com seu editor ou IDE favorito.

### Registrando o serviço
No arquivo ``startup.cs`` da nossa aplicação iremos adicionar o ```AddHealthChecks()``` via interface ``IServiceCollection`` no método ``ConfigureServices``

O método ficará dessa forma:
``` csharp
public void ConfigureServices(IServiceCollection services)
{
services.AddHealthChecks(); //Aqui adicionamos o HealthChecks

services.AddControllers();
services.AddSwaggerGen(c =>
{

c.SwaggerDoc("v1", new  OpenApiInfo { Title = "HealthCheck", Version = "v1" });
});
}
```
Agora iremos configurar o middleware e a rota da chamada adicionando o ``UseHealthChecks("/health")`` no método ``Configure``, ainda na classe ``startup.cs`` mas dessa vez, utilizando a interface ``IApplicationBuilder``

Teremos o seguinte código:
``` csharp
public void Configure(IApplicationBuilder  app, IWebHostEnvironment  env)
{
if (env.IsDevelopment())
{
app.UseDeveloperExceptionPage();
app.UseSwagger();
app.UseSwaggerUI(c => c.SwaggerEndpoint("/swagger/v1/swagger.json", "HealthCheck v1"));
}
app.UseHttpsRedirection();
app.UseRouting();  
app.UseAuthorization();  
app.UseEndpoints(endpoints =>
{
endpoints.MapControllers();
});

app.UseHealthChecks("/health"); //Aqui ativamos o serviço e o caminho da chamada
}
```
>O endpoint que irá responder ao Health Checks poderá ser configurado de acordo com sua necessidade, em nosso exemplo estamos utilizando o /health

Agora podemos executar a nossa aplicação com o comando:
```
dotnet run
```
Com a aplicação rodando, ao solicitarmos uma requisição via ``Postman`` para o caminho ``https://localhost:5001/health`` teremos o seguinte resultado:


![Requisição](https://raw.githubusercontent.com/diegostan/healthchecks-aspnet5/main/images/request1.png)
Podemos notar que o resultado da requisição nos retorna um ``statusCode 200`` além da mensagem ``Healthy``.

### Verificando a integridade de um banco de dados
Além de termos a possibilidade de verificarmos a saúde da nossa aplicação, ainda podemos extender o funcionamento do Health Checks para outros tipos de serviços. Nesse exemplo iremos verificar a saúde de uma instância do ``SQL Server``.

Para isso precisaremos instalar o pacote ``AspNetCore.HealthChecks.SqlServer`` utilizando o comando:
```
dotnet add package AspNetCore.HealthChecks.SqlServer
```


> Podemos verificar diversos tipos de banco de dados além do SQL Server, existe diversos pacotes que suportam uma vasta gama de serviços.

Agora basta adicionarmos o método ``AddSqlServer("connectionString", "name")`` após a chamada do ``AddHealthChecks()`` na classe ``startup.cs``

Teremos a seguinte configuração:
``` csharp
public void ConfigureServices(IServiceCollection  services)
{
//Configurando o SQL Server
services.AddHealthChecks()
.AddSqlServer(connectionString: Mapping.SqlConnectionString.GetConnectionString(), 
name: "Instancia do Sql Server");

services.AddControllers();
services.AddSwaggerGen(c =>
{
c.SwaggerDoc("v1", new  OpenApiInfo { Title = "HealthCheck", Version = "v1" });
});
}
```

No meu caso eu criei uma classe estática chamada ``Mapping.SqlConnectionString.GetConnectionString()`` onde eu retorno a seguinte string de conexão do SQL Server:
````
"Server=.\\SQLExpress; Database=master; Integrated Security=True"
````

Bom, se rodarmos essa aplicação teremos a mesma mensagem e resultado da imagem anterior, porém ao desativarmos a instância do SQL Server (simulando uma indisponibilidade por exemplo) teremos o seguinte resultado:

![Requisição](https://raw.githubusercontent.com/diegostan/healthchecks-aspnet5/main/images/request2.png)
Percebemos que o retorno dessa vez nos trouxe a mensagem ``Unhealthy`` e o ``statusCode 503``.

### Utilizando uma interface gráfica
Mais uma funcionalidade interessante dos Health Checks, é a possibilidade de trabalharmos com uma interface gráfica para vizualizarmos os eventos e históricos via uma espécie de dashboard.

Para isso precisaremos instalar os seguintes pacotes na nossa aplicação:
* AspNetCore.HealthChecks.UI
* AspNetCore.HealthChecks.UI.Client
* AspNetCore.HealthChecks.UI.InMemory.Storage

Esses pacotes podem ser instalados via gerenciador de pacotes do *NuGet* ou se preferir podemos utilizar os comandos no CLI:
```
dotnet add package AspNetCore.HealthChecks.UI
dotnet add package AspNetCore.HealthChecks.UI.Client
dotnet add package AspNetCore.HealthChecks.UI.InMemory.Storage
```

Com os pacotes instalados iremos registrar o serviços ``AddHealthChecksUI()`` e ``AddInMemoryStorage()`` no arquivo ``startup.cs`` como fizemos anteriormente, teremos a seguinte configuração:
```csharp
public  void  ConfigureServices(IServiceCollection  services)
{
services.AddControllers();
services.AddCors();

services.AddHealthChecks()
.AddSqlServer(connectionString:Mapping.SqlConnectionString.GetConnectionString(),
name: "Instancia do Sql Server");

//Configurando a interface gráfica e o armazenamento do histórico
services.AddHealthChecksUI(options =>
{
options.SetEvaluationTimeInSeconds(5);
options.MaximumHistoryEntriesPerEndpoint(10);
options.AddHealthCheckEndpoint("API com Health Checks", "/health");
})
.AddInMemoryStorage(); //Aqui adicionamos o banco em memória

services.AddSwaggerGen(c =>
{
c.SwaggerDoc("v1", new  OpenApiInfo { Title = "HealthCheck", Version = "v1" });
});
}
```
Aqui vale frisar alguns pontos importantes, estamos passando algumas opções via construtor:
* ***SetEvaluationTimeInSeconds()*** define o intervalo que será disparado a verificação dos serviços
* ***MaximumHistoryEntriesPerEndpoint()*** define a quantidade máxima de registros permitidos no histórico
* ***AddHealthCheckEndpoint()*** define o endpoint do Health Check

O método ``AddInMemoryStorage()`` configura um banco de dados em memória que será utilizado para armazerar o histórico das verificações.

Agora no método ``Configure``, primeiro vamos adicionar no método ``UseHealthChecks()`` a classe de opções ``HealthCheckOptions`` que irá permitir que configuremos os delegates ``ResponseWriter`` e ``Predicate`` . 
Depois iremos adicionar o ``UseHealthChecksUI()`` que de fato irá habilitar a interface gráfica, teremos a seguinte configuração:
```csharp
public  void  Configure(IApplicationBuilder  app, IWebHostEnvironment  env)
{
if (env.IsDevelopment())
{
app.UseDeveloperExceptionPage();
app.UseSwagger();
app.UseSwaggerUI(c => c.SwaggerEndpoint("/swagger/v1/swagger.json", "HealthCheck v1"));
}
app.UseCors(x => x.AllowAnyOrigin().AllowAnyHeader().AllowAnyMethod());
app.UseRouting();
app.UseAuthorization();
app.UseEndpoints(endpoints =>
{
endpoints.MapControllers();
});
 
app.UseHealthChecks("/health", new  HealthCheckOptions
{
Predicate = p => true,
ResponseWriter = UIResponseWriter.WriteHealthCheckUIResponse
});

app.UseHealthChecksUI(options => { options.UIPath = "/dashboard"; });
}
```

Pronto! Agora ao rodarmos a aplicação e fizermos o direcionamento para *localhost:5001/dashboard*, iremos nos deparar com o dashboard do Health Checks:

![Requisição](https://raw.githubusercontent.com/diegostan/healthchecks-aspnet5/main/images/dashboard1.png)

### Personalizando a página do Health Checks
Os recursos do Health Checks não param por aqui, ainda temos a possibilidade de personalizarmos essa tela de dashboard utilizando um arquivo CSS! Isso mesmo podemos deixar o dashboard com a cara da nossa aplicação.	
Para isso iremos criar um arquivo de estilo CSS, no meu caso eu o chamei de baltaStyle.css e deixei na raiz do projeto.

O código CSS ficou da seguinte forma:
```CSS
:root {
--bgMain: #1f1f1f; /* background color */
--fcBase: #fff; /* font color */
--bgMenuActive: #2f2f2f; /* background color of active menu item */
--bcMenuActive: #fffb00; /* border color of active menu item */
--primaryColor: #8625d2; /* primary color */
--secondaryColor: #9343d1; /* secondary color */
--bgMenuActive: #7a0bcf6e; /* background color of menu item when active */
--bgButton: #63199b; /* background color of button */
--logoImageUrl: url('https://raw.githubusercontent.com/diegostan/healthchecks-aspnet5/main/images/baltaLogo.png'); /* logo image url */
--bgAside: var(--primaryColor); /* background color of aside */
--bgTable: #2f2f2f; /* background color of table */
--bgTableSecondary: #63199b; /* secondary background color of table */
}
``` 
> Para saber mais configurações de estilo basta acessar a página no GitHub https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks/blob/master/doc/styles-branding.md

No caso do exemplo eu estilizei a página com as cores e o logo aqui do ``balta.io``.
Agora basta adicionarmos a opção``AddCustomStylesheet`` passando como parâmetro o caminho do CSS que queremos utilizar. Nosso ``UseHealthChecksUI`` ficará da seguinte forma:
```csharp
app.UseHealthChecksUI(options => { options.UIPath = "/dashboard";
options.AddCustomStylesheet("baltaStyle.css");});
```
Agora ao rodarmos a aplicação teremos o resultado:
![Requisição](https://raw.githubusercontent.com/diegostan/healthchecks-aspnet5/main/images/dashboard2.png)

### Criando nosso próprio Health Checks
Bom, sabemos que temos uma vasta lista de provedores de banco de dados e diversos serviços disponíveis para o Health Checks, porém podemos criar nossas proprias checagens possibilitando maior flexibilidade do uso dessa ferramenta. Para isso basta utilizarmos a interface ``IHealthCheck``.

Vamos criar uma nova classe chamada ``CustomHealthChecks.cs`` para utilizarmos de exemplo.

O código dessa classe ficará dessa forma:
```csharp
using  System.Threading;
using  System.Threading.Tasks;
using  Microsoft.Extensions.Diagnostics.HealthChecks;
namespace  HealthCheck
{
	public  class  CustomHealthChecks : IHealthCheck
	{
	 public  Task<HealthCheckResult> CheckHealthAsync(HealthCheckContext  context, CancellationToken
	 cancellationToken = default)
	 {
		return  Task.FromResult(new  HealthCheckResult(
		status: HealthStatus.Unhealthy,
		description: "API está doente"
		));
	 }
	}
}
```
Vamos aos pontos importantes:
* A interface ``IHealthCheck`` traz como assinatura um método chamado ``CheckHealthAsync``
*  Esse método nos obriga a retornar um objeto do tipo ``HealthCheckResult``
*  O objeto ``HealthCheckResult`` irá informar ao Health Checks o status desse serviço

Uma das propriedades do ``HealthCheckResult`` é a ``status``, ela permite informarmos a saúde do serviço que estamos verificando.
Como nesse caso estamos apenas exemplificando seu uso, eu defini essa propriedade para ``Unhealthy``, ou seja, informando que o serviço está doente.

Agora vamos implementar esse serviço no nosso arquivo ``startup.cs`` mais especificamente, em ``services.AddHealthChecks()``.
Nosso código ficará dessa forma:
```csharp
services.AddHealthChecks()
.AddSqlServer(connectionString: Mapping.SqlConnectionString.GetConnectionString(), name: "Instancia do Sql Server")
.AddCheck<CustomHealthChecks>("Health Checks customizavel"); // Aqui colocamos nosso Health Check
```

Agora rodaremos essa aplicação para vermos o resultado:

![Requisição](https://raw.githubusercontent.com/diegostan/healthchecks-aspnet5/main/images/dashboard3.png)

### Usando o Health Checks para aplicações customizadas
Vimos que temos a possibilidade de utilizarmos o Health Checks para verificarmos os serviços e também configuramos um dashboard onde temos a visão expandida e amigável dessa ferramenta.

Ainda existe a possibilidade de utilizarmos o arquivo JSON disponibilizado no endpoint configurado, dessa forma podemos criar paineis unificados onde podemos visualizar o estado de varias aplicações com Health Checks.

Ao acessarmos o endpoint do ``/health`` (configuramos no inicio do artigo) teremos o seguinte resultado:

![Requisição](https://raw.githubusercontent.com/diegostan/healthchecks-aspnet5/main/images/request3.png)
Dessa forma é possível consumirmos esse endpoint para utilizarmos como bem entendermos.

### Finalizando
Nesse artigo vimos o que é o Health Checks e como configurarmos essa ferramenta muito útil na verificação de integridade de uma aplicação ASP NET. Espero que tenham gostado.

Para mais informações acessem:
https://docs.microsoft.com/pt-br/aspnet/core/host-and-deploy/health-checks?view=aspnetcore-5.0

Para acesso ao código fonte desse exemplo acessem o link no meu GitHub:
https://github.com/diegostan/ExemploHealthChecks

Até a próxima. 
