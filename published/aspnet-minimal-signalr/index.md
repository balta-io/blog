No artigo anterior, vimos um pouco sobre Minimal APIs e o novo template Web do ASP.NET. Neste artigo vamos conferir as principais mudanças do SignalR.

## Antes de começar
Se você ainda não viu o novo template do ASP.NET Minimal APIs, [clique aqui e confira este artigo](https://balta.io/blog/aspnet-minimal-apis).

## O que é o SignalR?
SignalR é a tecnologia da Microsoft, que roda sob o ASP.NET e encapsula chamadas ao WebSocket provendo comunicação em tempo real para seu Frontend (Client) e servidor.

### Iniciando o  projeto
Vamos começar criando nosso servidor, usando o comando abaixo:
```
dotnet new web -o SignalServer
cd SignalServer
dotnet add package Microsoft.AspNetCore.SignalR
```

O pacote `Microsoft.AspNetCore.SignalR` é tudo que precisamos para trabalhar com SignalR no servidor.

## Criando um Hub
Diferente dos projetos ASP.NET MVC, o SignalR utiliza **Hub** ao invés de controladores.

Os **Hubs** são responsáveis por receber e distribuir mensagens em tempo real. Por sua vez, um **Hub** nada mais é do que uma classe que herda da classe base `Hub`.

```csharp
class MyHub : Hub
{
    ...
}
```

### IAsyncEnumerable
Se estamos falando em **Tempo Real**, em ficar conectado ao servidor, estamos falando sobre Streaming de dados (Na maioria dos casos), e o C# incluiu nas últimas versões um tipo chamado `IAsyncEnumerable`.

Desta forma, podemos especificar nosso retorno como um enumerador assíncrono, de modo que possamos fazer streaming dos dados enquanto houverem conexões a este endpoint.

```csharp
public async IAsyncEnumerable<DateTime> Streaming(CancellationToken cancellationToken)
{
    while (true)
    {
        yield return DateTime.UtcNow;
        await Task.Delay(1000, cancellationToken);
    }
}
```

É importante notar o uso do `CancelationToken`, que permite que a ação seja cancelada a qualquer momento.

Em adicional, usamos o `yield` (Produtor) para definir que o retorno é um iterador. Desta forma o cliente pode consumir direto nosso conteúdo, sem necessitar de uma classe para armazenar o estado.

## Adicionando SignalR
Para adicionar suporte ao SignalR na nossa aplicação precisamos do `builder.Services.AddSignalR();` e do `app.MapHub<SeuHub>("ENDPOINT")`.

```csharp
using Microsoft.AspNetCore.SignalR;

var builder = WebApplication.CreateBuilder(args);
builder.Services.AddSignalR();

var app = builder.Build();
app.MapHub<MyHub>("/chat");
app.Run();

```

## Resultado final
O código final. completo do nosso App com SignalR fica assim.

```csharp
using Microsoft.AspNetCore.SignalR;

var builder = WebApplication.CreateBuilder(args);
builder.Services.AddSignalR();

var app = builder.Build();
app.MapHub<MyHub>("/chat");
app.Run();


class MyHub : Hub
{
    public async IAsyncEnumerable<DateTime> Streaming(CancellationToken cancellationToken)
    {
        while (true)
        {
            yield return DateTime.UtcNow;
            await Task.Delay(1000, cancellationToken);
        }
    }
}
```

### Executando
Agora basta executar o programa usando o comando:
```
dotnet watch run
```

> Não esqueça de anotar a URL na qual seu servidor está rodando. Vamos precisar dela para nos conectar no cliente a seguir.

## Trabalhando no Client
Para se conectar ao servidor, vamos criar um Console Application.

```
dotnet new console -o SignalClient
cd SignalClient
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

Novamente, tudo o que precisamos para consumir um serviço SignalR é o pacote `Microsoft.AspNetCore.SignalR.Client`.

### Conectando ao servidor
Para se conectar ao servidor precisamos do `HubConnectionBuilder` apenas.

```csharp
using Microsoft.AspNetCore.SignalR.Client;

var uri = "https://localhost:7182/chat";

await using var connection = new HubConnectionBuilder().WithUrl(uri).Build();
```

Em seguida, podemos iniciar a conexão.

```csharp
await connection.StartAsync();
```

### Recebendo dados
Como utilizamos o `IAsyncEnumerable`, para receber os dados, podemos chamar a função `StreamAsync<TIPO>("STREAM")`.

```csharp
await foreach (var date in connection.StreamAsync<DateTime>("Streaming"))
{
    Console.WriteLine(date);
}
```

Como resultado final, temos o seguinte código:

```csharp
using Microsoft.AspNetCore.SignalR.Client;

var uri = "https://localhost:7182/chat";

await using var connection = new HubConnectionBuilder().WithUrl(uri).Build();

await connection.StartAsync();

await foreach (var date in connection.StreamAsync<DateTime>("Streaming"))
{
    Console.WriteLine(date);
}
```
## Conclusão
Assim como os [Minimal APIs](https://balta.io/blog/aspnet-minimal-apis), outras implementações como ASP.NET SignalR também podem se beneficiar destas mudanças, tornando o ambiente mais simples.

### Fontes
* [David Fowler](https://twitter.com/davidfowl)
* [Código Fonte](https://github.com/andrebaltieri/minimal-signalr-example)