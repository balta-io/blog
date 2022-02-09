Neste artigo vamos aprender a criar e consumir aplicações em tempo real com ASP.NET utilizando WebSockets nativos do .NET.

## O que são WebSockets?
WebSockets ou carinhosamente chamados de WS é uma tecnologia que permite mantermos clientes conectados aos nossos servidores e trafegar mensagens mais rápidas.

Como eu comento no curso [**Fundamentos do ASP.NET**](https://balta.io/cursos/fundamentos-aspnet), uma requisição tem alguns passos a serem seguidos, dentre eles o **handshake** (Aperto de mão) e abertura da conexão.

Somente depois destes passos trafegamos os dados, e em seguida a conexão é fechada. Neste modelo mais convencional, precisamos realizar estes passos a cada requisição ao servidor, enquanto nos **WebSockets**, uma vez conectados, trafegamos apenas os dados.

### SignalR
O ASP.NET possui um pacote chamado SignalR que encapsula os **WebSockets**, trazendo mais funcionalidades. Inclusive o Blazor Server se beneficia destes pontos.

Embora o **SignalR** seja um excelente pacote e minha recomendação principal no trabalho com **WS**, aqui vamos utilizar apenas recursos nativos do .NET.

## Iniciando a aplicação
Para começar, vamos criar uma aplicação Web mais simples possível. Aliás, o intuito aqui é fazer o menor App Cliente/Servidor com WebSockets que você codou!

Para este exemplo vamos criar duas aplicações, uma que agirá como servidor, onde teremos os **WebSockets** e outra como cliente, que irá consumí-los. Vamos começar pelo servidor.

```
dotnet new web -o Server
```

## Adicionando suporte a WebSockets
Incrivelmente, tudo que precisamos para adicionar suporte a **WebSockets** nas nossa aplicações ASP.NET é a linha `app.UseWebSockets()`. Esta chamada também não exige nenhum pacote adicional.

```csharp
var app = builder.Build();
app.UseWebSockets();
```
Incrível, não é? Agora vamos melhorar nosso App, tornando-o assíncrono e executando-o da seguinte maneira.

```csharp
app.Map("/", async context =>
{
    // TODO
});
await app.RunAsync();
```

Note que temos apenas uma rota, `"/"` que será a raiz da aplicação, também servindo como um **WebSocket** para nosso cliente.

### Verificando a conexão
Após adicionarmos suporte aos **WebSockets** utilizando `app.UseWebSockets`, temos acesso aos mesmos dentro do `context.WebSockets`, que nos permite manipular a requisição como um **WS**.

Como nosso endpoint pode receber vários tipos de requisição, podemos tratar apenas requisições que fazem sentido para nosso cenário, verificando se `IsWebSocketRequest` é verdadeiro.

```csharp
if (!context.WebSockets.IsWebSocketRequest)
    context.Response.StatusCode = (int)HttpStatusCode.BadRequest;
```

### Manipulando as requisições
O primeiro passo para manipular nossas requisições é ter uma instância do **WebSocket** e isto pode ser feito utilizando o método `AcceptWebSocketAsync`, conforme mostrado abaixo.

```csharp
using var webSocket = await context.WebSockets.AcceptWebSocketAsync();
```

Com a requisição em mãos, podemos enviar mensagens para os clientes conectados utilizando o `SendAsync`, que espera os parâmetros:
* Mensagem (Array de bytes)
* Tipo da mensagem (Texto neste caso)
* Fim da mensagem (Sim, não enviaremos mensagens segmentadas)
* Cancelation Token (Token para cancelamento da requisição)

```csharp
var data = Encoding.ASCII.GetBytes($".NET Rocks -> {DateTime.Now}");
await webSocket.SendAsync(
        data, // Message
        WebSocketMessageType.Text, // Type
        true, //EndOfMessage
        CancellationToken.None);
```
Para exemplificar melhor, vou deixar este conteúdo dentro de um loop infinito, com delay de um segundo entre as mensagens. Basicamente ele ficará enviando a mesma mensagem para o cliente a cada segundo.

```csharp
while (true)
{
    await webSocket.SendAsync(
        Encoding.ASCII.GetBytes($".NET Rocks -> {DateTime.Now}"),
        WebSocketMessageType.Text,
        true, 
        CancellationToken.None);
    await Task.Delay(1000);
}
```

### Servidor
Acredite se quiser, mas isto é tudo que precisamos do lado do servidor. Temos uma aplicação que faz uso dos **WebSockets** e nossos clientes já podem se conectar a ela para receber mensagens.

Segue abaixo o código completo do servidor. Vamos agora executar a aplicação utilizando `dotnet run` e anotar a URL que a mesma está rodando.

```csharp
using System.Net;
using System.Net.WebSockets;
using System.Text;

var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();
app.UseWebSockets();
app.Map("/", async context =>
{
    if (!context.WebSockets.IsWebSocketRequest)
        context.Response.StatusCode = (int) HttpStatusCode.BadRequest;
    else
    {
        using var webSocket = await context.WebSockets.AcceptWebSocketAsync();
        while (true)
        {
            await webSocket.SendAsync(
                Encoding.ASCII.GetBytes($".NET Rocks -> {DateTime.Now}"),
                WebSocketMessageType.Text,
                true, CancellationToken.None);
            await Task.Delay(1000);
        }
    }
});
await app.RunAsync();
```

## Cliente
Para consumir as mensagens da aplicação, vamos criar um cliente, do tipo **Console Application** que ficará lendo as mensagens infinitamente.

```
dotnet new console -o Cliente
```

### Adicionando suporte a WebSocket
Assim como fizemos no servidor, aqui teremos que adicionar suporte aos **WebSockets** também, e isto é feito sem adição de qualquer pacote, apenas usando o `ClientWebSocket`.

```csharp
using var ws = new ClientWebSocket();
await ws.ConnectAsync(new Uri("ws://localhost:5065/"), CancellationToken.None);
```

É importante notar que sua aplicação (Servidor) provavelmente rodará em uma porta diferente da minha (**5065**), então lembre-se de alterar a URL informada acima.

### Consumindo as mensagens
Assim como a produção de mensagens foi um array de bytes, o consumo também será, e vamos iniciar definindo um buffer para a mesma.

```csharp
var buffer = new byte[256];
```
Em seguida, podemos ficar ouvindo as mensagens do servidor enquanto a conexão estiver aberta, e para isto verificamos se o `ws.State` está como `Open`. Enquanto isto for verdade, queremos receber mensagens.
```csharp
while (ws.State == WebSocketState.Open)
{
    // TODO
}
```

### Recebendo as mensagens
Para receber as mensagens vamos utilizar o método `ReceiveAsync` que precisará do `buffer` que definimos anteriormente e um `CancelationToken` que não temos neste caso.

```csharp
var result = await ws.ReceiveAsync(buffer, CancellationToken.None);
```
Caso a mensagem seja diferente de `WebSocketMessageType.Close`, que significa que a conexão foi encerrada, vamos convertê-la para uma `string` e mostrá-la na tela.

Caso a mensagem seja de conexão encerrada, vamos fechar o **WebSocket** e encerrar tudo por aqui.

```csharp
 if (result.MessageType == WebSocketMessageType.Close)
    await ws.CloseAsync(WebSocketCloseStatus.NormalClosure, null, CancellationToken.None);
else
    Console.WriteLine(Encoding.ASCII.GetString(buffer, 0, result.Count));
```

### Cliente
E isto é tudo o que precisamos do lado do cliente. Pronto, temos uma aplicação que produz mensagens e outra que consome, ambas usando **WebSockets** nativos do .NET, sem necessidade de pacotes externos.

```csharp
using System.Net.WebSockets;
using System.Text;

using var ws = new ClientWebSocket();
await ws.ConnectAsync(new Uri("ws://localhost:5065/"), CancellationToken.None);

var buffer = new byte[256];
while (ws.State == WebSocketState.Open)
{
    var result = await ws.ReceiveAsync(buffer, CancellationToken.None);
    if (result.MessageType == WebSocketMessageType.Close)
        await ws.CloseAsync(WebSocketCloseStatus.NormalClosure, null, CancellationToken.None);
    else
        Console.WriteLine(Encoding.ASCII.GetString(buffer, 0, result.Count));
}
```

## Conclusão
Utilizar **WebSockets** no ASP.NET/.NET é uma tarefa simples e fácil, que não necessita sequer de bibliotecas externas para o mesmo.