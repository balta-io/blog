Delegates são a possibilidade que temos de delegar funções para métodos externos a execução atual.

## Delegates
Sendo mais teórico, delegates ou delegados, são ponteiros que apontam para funções, normalmente externas ao método em execução.

Podemos assemelhar os delegates aos callbacks que temos em outras linguagens como JavaScript por exemplo, e embora fora do mundo C# ele tenha um forte uso, particularmente não vejo muito sua aplicabilidade no C#.

Talvez isto se deva ao mal entendimento do assunto ou falta de exemplos mais práticos, como o que veremos aqui.

## Contexto
Antes de começar, quero contextualizar o uso dos delegates neste cenário, e frisar que existem várias formas de resolver este problema, mas focaremos aqui no tema em pauta.

Quando começamos a segmentar nossas aplicações em pacotes, as coisas começam a ficar complicadas. Não é fácil pensar em como vamos expor o sucesso ou fracasso da execução de um serviço.

Vamos tomar como base os serviços de pagamento. Hoje por exemplo, no balta temos suporte a PayPal e PagarMe, além de alguns testes que já fiz no Stripe.

Desta forma, são três serviços distintos para gerenciar, cada um tem seu retorno, seu objeto de sucesso e seu objeto de erro.

Do outro lado, nossa API tem uma padronização, por exemplo, TODA E QUALQUER resposta vem no seguinte formato.

```csharp
public class Response<T> : Notifiable<Notification>
{
    public Response()
    {
    }

    public Response(T data)
    {
        Status = 200;
        Data = data;
    }

    public string Message { get; set; }
    public int Status { get; set; }
    public T Data { get; set; }
}
```

A resposta é composta pelo `Notiiable` que vem do [Flunt](https://balta.io/blog/flunt), seguido da mensagem, status e dados genéricos. Tem bem mais coisas, mas reduzi a isto apenas para nosso entendimento.

Em resumo, qualquer comunicação entre serviços do balta ou qualquer resposta da API segue este padrão.

## Desafio
O uso dos **delegates** neste cenário surgiu justamente para gerenciar os erros ocorridos em APIs externas. No caso, tanto para o PayPal quanto para o PagarMe, fazemos um `POST` e esperamos apenas uma `string` que é o ID da assinatura criada no serviço.

O mais difícil é a parte de gerenciar erros. Ambos serviços são chamados via `RestSharp`, então você já imagina que podemos ter erros de:
* URL inválida
* Serialização para JSON
* Serviço fora
* Deserialização do JSON
* E por ai vai

ENTÃO JOGA TUDO DENTRO DE UM TRY/CATCH! Brincadeira, ai não rolaria por não sabermos onde o erro aconteceu nem qual o motivo. Então fazemos vários blocos `try` por que precisamos ser precisos sobre o erro que está acontecendo.

Em pouco tempos chegamos em algo parecido com isto, mas claro, que queríamos muito reaproveitar estas chamadas.

```csharp
var response = new Response<IEnumerable<PagarMeSubscriptionModel>>();
var client = new RestClient("URL");
var httpRequest = new RestRequest("URL", DataFormat.Json);
httpRequest.AddParameter("count", pageSize);

IRestResponse<IEnumerable<PagarMeSubscriptionModel>> httpResponse;
try
{
    httpResponse = client.Get<IEnumerable<PagarMeSubscriptionModel>>(httpRequest);
}
catch (Exception ex)
{
    response.ToInternalServerError(ex);
    return response;
}

if (httpResponse.IsSuccessful)
{
    response.ToSuccess(httpResponse.Data);
    return response;
}

try
{
    var error = JsonConvert.DeserializeObject<PagarMeErrorJson>(httpResponse.Content);
    response.Message = "Não foi possível listar as assinaturas";
    foreach (var item in error.Errors)
        response.AddNotification(item.ParameterName, item.Message);

    return response;
}
catch (Exception ex)
{
    response.ToInternalServerError(ex);
    return response;
}
```

Se você notar, boa parte do código pode ser reutilizado, podemos utilizar `T` como parâmetro na requisição e tornar o método todo genérico.

## Exemplo real
O que acontece no exemplo acima é a tratativa de sucesso e diversas falhas diferentes dentros do mesmo método, e todas acopladas a um modelo apenas.

Para o caso do PayPal, que o código seria bem parecido, o mesmo precisaria ser reescrito, e terminaríamos com dois serviços fazendo a mesma coisa.

> IMPORTANTE: Não vale criar flag isPayPal ou isPagarMe e chavear dentro do método hein. A ideia aqui é reduzir complexidade e código.

### RestSharp
O RestSharp faz requisições HTTP em APIs e podemos já serializar o resultado em um objeto, o que nos facilita muito a vida.

Tudo o que ele precisa é de um `Request`, um `Client` e irá retornar um `Response`, que pode ser tipado, inclusive utilizando genérics.

Nosso maior problema então é, caso a requisição seja executada com sucesso, tratar os dados, e caso haja algo errado, tratar os erros, porém, fora do método que faz a requisição.

Por que fora? Porque a maior parte do código acim é só para fazer isto, e ele que está engessando nossas mudanças.

## Iniciando o projeto
Para este exemplo você só precisa criar um Console App e adicionar as duas classes abaixo nele.

```
dotnet new -o DelegatesSample
cd DelegatesSample
code .
```

Com o VS Code aberto, crie dois arquivos chamado `ArticleModel` e `ErrorModel` na pasta `Models`.

## Criando Delegates
Nosso primeiro passo aqui é criar os delegates, eles ficam acima do método `main` no `Program.cs`.

```csharp
private delegate void OnSuccessDelegate(IEnumerable<ArticleModel> data);
private delegate void OnErrorDelegate(ErrorModel data);
```
Com oa "ponteiros" definidos, podemos utiliza-los como parâmetros nos métodos, e se você observou bem, eles remetem a sucesso e erro.

Nosso objetivo agora é colocar estes delegates como parâmetros em nosso serviço e gerenciarmos sucesso ou erro externo a ele. É importante notar também que estamos enviando parâmetros para os delegates.

```csharp
private static void GetArticles(
    OnSuccessDelegate onSuccess, 
    OnErrorDelegate onError)
{
    ...
}
```

Com tudo declarado, agora temos dois métodos que podem ser chamados na execução do nosso código, o `onError` e o `onSuccess`, que podem ser invocados da seguinte forma.

```csharp
onSuccess(data);
onError(new ErrorModel($"Ocorreu um erro: {ex.Message}"));
```

Você já consegue ter ideia do que vai acontecer neste ponto? Se sim, paarabéns <3

Continuando, como resultado final do nosso serviços temos os seguinte código.

```csharp
private static void GetArticles(
    OnSuccessDelegate onSuccess, 
    OnErrorDelegate onError)
{
    try
    {
        var data = new List<ArticleModel>();
        data.Add(new ArticleModel(1, "Orientação a objetos"));
        data.Add(new ArticleModel(2, "Fundamentos do C#"));
        //throw new Exception("Deu ruim");
        onSuccess(data);
    }
    catch (Exception ex)
    {
        onError(new ErrorModel($"Ocorreu um erro: {ex.Message}"));
    }
}
```

Note que deixei um `throw new Exception...` para forçarmos um erro e vermos os dois scenários.

## Gerenciando os callbacks
Neste caso, vamos ter dois métodos, um para sucesso e outro para erro, e como eu comentei nas aulas de delegates do curso de Fundamentos do C#, as assinaturas precisam ser a mesmas.

```csharp
private static void OnSuccess(IEnumerable<ArticleModel> data)
{
    foreach (var item in data)
    {
        Console.WriteLine(item.Title);
    }
}

private static void OnError(ErrorModel data)
{
    Console.WriteLine($"ERRO: {data.Message}");
}
```

## Ligando tudo
Até o momento temos as declarações dos delegates, o uso destes como parâmetro no método `GetArticles` e internamente no mesmo método.

Precisamos agora conectar tudo, e a chave para isto são os `Handlers` que vão manipular estas interações entre nossos códigos.

O primeiro passo a ser realizado é ligar os delegados com os métodos que criamos, e que irão manipular os resultados.

```csharp
OnSuccessDelegate onSuccessHandler = OnSuccess;
OnErrorDelegate onErrorHandler = OnError;
```

E depois precisamos chamar o método `GetArticles` que vai esperar os dois manipuladores que criamos acima.

```csharp
 GetArticles(onSuccessHandler, onErrorHandler);
 ```
## Resultado

Execute o programa com `dotnet run` e verá uma lista com dois artigos. Comente a linha onde forçamos um `Exception` e rode novamente para ver o erro na tela,

Em suma o que está acontecendo é que os delegates são declarados no topo, ele ficam reporesentando literamente ponteiros neste momento, e assim que criamos uma função que atende os requisitos (Assinatura) eles já podem ser ligados.

Esta ligação é feita através de um handler, cujo pode invocar o método e que também podemos passar para outras funções.

Por fim, não temos a menor preocupação em relação a execução da função pois os casos estão sendo tratados externos a ela.

As funções ficam menores, mais expressivas e evitamos ficar utilizando Try/Catch em tudo ou mesmo retornar um objeto de sucesso que representa um erro.

 ## Outras dicas
 Fizemos um exemplo inicial utilizando conceitos básicos, mas já pensou no uso dos delegates com generics? Algo como:
 ```csharp
 private delegate void OnSuccessDelegate<T>(T data) where T : Entity;
 ```

 Dado este delegate, podemos ter vários handlers para manipular um ou vários callbacks, cada um de uma forma diferente!

 ## Conclusão
Muitas vezes subestimamos o uso de funcionalidades. Normalmente focamos em propriedades e métodos, mas esquecemos de delegates, event e vários outros recursos legais que o C# nos fornece.


 ## Fontes
 *  [Código Fonte - GitHub](https://github.com/andrebaltieri/csharp-delegate)
 * [Delegates - Docs Oficiais](https://docs.microsoft.com/pt-br/dotnet/csharp/programming-guide/delegates/)

<div role="main" id="blog-s1-dotnet-134e3db1eea6c9829db1"></div>
<script type="text/javascript" src="https://d335luupugsy2.cloudfront.net/js/rdstation-forms/stable/rdstation-forms.min.js"></script>
<script type="text/javascript"> new RDStationForms('blog-s1-dotnet-134e3db1eea6c9829db1', 'UA-48664517-12').createForm();</script>