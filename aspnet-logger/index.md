O ILogger, presente no .NET (Extensions), nos permite retornar informações sobre execução, erros e avisos dos nossos sistemas de forma simples e fácil.

## O que é o Logging?
O Logging é uma API ou biblioteca presente no .NET (Extensions), do qual podemos utilizar para "escrever" alguma mensagem durante a execução do nosso código.

Existem várias bibliotecas de Logging, como NLog, Log4Net e Serilog, que fazem o mesmo trabalho, porém a padrão que vem nos templates da Microsoft é o `ILogger`.

### Instalação
A maioria dos templates vem com este pacote instalado, mas caso o seu projeto não possua a dependência, a mesma fica no `Microsoft.Extensions`.

```
dotnet add package Microsoft.Extensions
```

Este pacote proverá o namespace `Microsoft.Extension.Logging` que possui os três itens que utilizaremos aqui, `ILogger`, `ILoggerFactory`, e `ILoggingProvider`.

## ILoggerFactory
O `ILoggerFactory` é a interface da qual podemos criar as instâncias do `ILogger` e `ILoggingProvider`. Ela funciona como um facilitador para registrarmos e acessarmos os **loggers** de forma mais fácil.

Por definição o `ILoggerFactory` possui apenas dois métodos, o `CreateLogger` que serve para criarmos um novo **logger** (Uma categoria para nossos logs) e o `AddProvider` que nos permite registrar outros provedores.

```csharp
public interface ILoggerFactory : IDisposable
{
    ILogger CreateLogger(string categoryName);
    void AddProvider(ILoggerProvider provider);
}
```

## ILoggerProvider
O `ILoggerProvider` server para criarmos instâncias específicas dos loggers por categoria. Ele possui apenas um método chamado `CreateLogger` que recebe e cria um segmento de logs.

```csharp
public interface ILoggerProvider : IDisposable
{
    ILogger CreateLogger(string categoryName);
}
```
Porém, o `ILoggerProvider` do `Microsoft.Extensions` já possui vários provedores **built-in**, como:
* Console Provider
* Debug Provider
* EventSource Provider
* EventLog Provider (Windows)
* TraceSource Provider

Caso queira criar seu próprio logger basta implementar a interface `ILoggerProvider`.

## Utilizando o ILogger
Para utilizar o `ILogger` precisamos de uma instância do mesmo, e ela é dada através da class `ILogger<T>`, onde `T` normalmente é a própria classe.

Vamos tomar como base este exemplo abaixo, retirado do template padrão do ASP.NET MVC, onde temos uma instância do `ILogger` dentro do `HomeController`.

```csharp
private readonly ILogger<HomeController> _logger;
```
Com a variável definida, podemos exigir sua instância via construtor (DI), e a biblioteca de log já fará a instância do mesmo.

```csharp
public HomeController(ILogger<HomeController> logger) 
    => _logger = logger;
```
Para logar alguma informação, basta utilizar o método `LogInformation` que a mesma será exibida no **Console**, durante a execução da aplicação.

```csharp
public IActionResult Index()
{
    _logger.LogInformation("Estou no Index do HomeController");
    return View();
}
```
Como resultado, temos o seguinte texto sendo exibido durante a execução da aplicação. Note a última linha contendo o texto que informamos no log anteriormente.
```
dotnet run

Building...
info: Microsoft.Hosting.Lifetime[14]
      Now listening on: https://localhost:7244
info: Microsoft.Hosting.Lifetime[14]
      Now listening on: http://localhost:5288
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\dev\AspNetLoggerSample\
info: AspNetLoggerSample.Controllers.HomeController[0]
      Estou no Index do HomeController
```

## Conclusão
Neste primeiro artigo da série, pudemos ver como utilizar o `ILogger` padrão do .NET, mas ainda temos muitas coisas para especionar. Nos próximos capítulos veremos outras configurações e loggers.