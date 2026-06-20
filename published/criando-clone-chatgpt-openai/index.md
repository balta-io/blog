Recentemente a Open AI, uma instituição de pesquisa na área de inteligência artificial, liberou para o público uma ferramenta denominada ChatGPT. Trata-se de uma IA capaz de gerar textos e código-fonte de forma muito realista, que pode ser utilizada em um formato de chat.

O que poucos sabem é que a Open AI disponibiliza algumas APIs públicas que permitem integrar os modelos da organização em sua aplicação. Neste artigo veremos como utilizar estes recursos em uma aplicação .NET, criando um clone do ChatGPT.

## Criando o projeto
Vamos criar uma console application denominada MyChatGPT. Para isso, execute o comando abaixo:

```PS1
dotnet new console -o MyChatGPT
```
Para realizar as requisições HTTP utilizaremos o pacote Refit. Você pode obter mais informações sobre o Refit [neste artigo](https://balta.io/blog/aspnet-refit2). Instale o pacote com o comando abaixo:

```PS1
dotnet add package Refit
```

## Completions endpoint

Iremos consumir um endpoint que permite submeter um prompt (texto de entrada) a uma IA e receber como resposta uma lista de completions, que são textos gerados pela IA a partir do prompt.

Existem outros endpoints disponíveis que permitem realizar outras operações, como geração de imagens. A documentação completa da API pode ser encontrada [aqui](https://beta.openai.com/docs/introduction).

Na [documentação do endpoint completions](https://beta.openai.com/docs/api-reference/completions/create) podemos ver qual o conteúdo esperado para uma requisição a este endpoint. Vamos criar uma classe `CompletionRequest` para representar o corpo da request:

```C# 
namespace MyChatGPT;

using System.Text.Json.Serialization;
 
public class CompletionRequest
{
    public string Model { get; set; } = "text-davinci-003";
    public string? Prompt { get; set; }
    public double Temperature { get; set; } = 0;
 
    [JsonPropertyName("max_tokens")]
    public int MaxTokens { get; set; } = 2048;
}
```

A propriedade `Model` é uma string indicando qual IA utilizaremos. Neste tutorial vamos utilizar a `text-davinci-003`. Em `Prompt` iremos informar o texto de entrada. A propriedade `Temperature`, resumidamente, controla o quão aleatórias ou “criativas” serão as respostas. Aqui utilizaremos 0, mas você pode experimentar outros valores para descobrir qual se adequa melhor ao seu caso de uso.  Por último, a propriedade `MaxTokens` controla o tamanho máximo das completions geradas. Existem outros parâmetros previstos, mas são opcionais e não se aplicam neste caso.

Para desserializar a resposta, vamos criar duas classes. A `CompletionResponse` representa o objeto mais externo da resposta e contêm um `IEnumerable` de `ChoiceCompletionResponse`. A propriedade `Text` desta classe corresponde ao completion gerado pela IA.

```C#
namespace MyChatGPT;

public class CompletionResponse
{
    public IEnumerable<ChoiceCompletionResponse> Choices { get; set; } = new List<ChoiceCompletionResponse>();
}

 
public class ChoiceCompletionResponse
{
    public string? Text { get; set; }
}
```

O endpoint retorna várias outras informações, mas não as utilizaremos neste tutorial. Consulte a documentação para verificar o schema completo do response.

Para realizar requisições, o Refit exige que criemos uma interface que expõe métodos que correspondem aos endpoints que serão chamados. Para isso, criamos a interface `ICompletionsAPI`:

```C# 
namespace MyChatGPT;

using Refit;

public interface ICompletionsAPI
{
    [Post("/v1/completions")]
    Task<CompletionResponse> CreateCompletion(CompletionRequest request);
}
```

Perceba que o método `CreateCompletion` está anotado com `[Post("/v1/completions")]`. Isso indica para o Refit que ao chamar esse método deve ser feita uma requisição POST para o endpoint `/v1/completions`, enviando um `CompletionRequest` no corpo da requisição, e recebendo um `CompletionResponse` como resposta.

## Gerando a API key
Para utilizar a API precisamos realizar autenticação com uma API key, que pode ser gerada [aqui]( https://beta.openai.com/account/api-keys).
Lembre-se que esta chave é um segredo, e portanto não deve ser publicamente visível.

Utilizaremos um arquivo de configuração `appsettings.json` para armazenar a API key, evitando que ela fique fixa na aplicação. Para isso, adicionamos os pacotes 
`Microsoft.Extensions.Configuration`
e `Microsoft.Extensions.Configuration.Json`, que nos dão a capacidade de carregar configurações de um arquivo json. Estes pacotes podem ser instalados com os comandos abaixo:

```PS1
dotnet add package Microsoft.Extensions.Configuration    
```
```PS1
dotnet add package Microsoft.Extensions.Configuration.Json
```

Criamos um arquivo `appsettings.json` e adicionamos a key como no exemplo abaixo:
```json
{
    "ApiKey": "SUA CHAVE AQUI"
}
```

Por último, precisamos alterar a interface utilizada pelo Refit para enviar a chave a cada requisição. Isso é feito adicionando a anotação `[Headers("Authorization: Bearer")]` sobre a declaração da interface:

```C# 
namespace MyChatGPT;

[Headers("Authorization: Bearer")]
public interface ICompletionsAPI
{
    [Post("/v1/completions")]
    Task<CompletionResponse> CreateCompletion(CompletionRequest request);
}
```

## Juntando todas as peças
Estamos quase prontos. No `Program.cs` vamos criar a lógica que une tudo o que fizemos.

Primeiro vamos carregar o appsetings.json, que possui a API key.

```C#
IConfiguration config = new ConfigurationBuilder()
    .AddJsonFile("appsettings.json")
    .Build();
```

Em seguida, pedimos ao Refit para criar uma implementação da `ICompletionsAPI`, configurando-o para buscar o header de autorização das configurações que carregamos anteriormente:

```C#
var completionsApi = RestService.For<ICompletionsAPI>("https://api.openai.com", new RefitSettings
{
    AuthorizationHeaderValueGetter = () => Task.FromResult(config["ApiKey"] ?? string.Empty),
});
```

Por útlimo, um laço simples que espera uma pergunta do usuário e faz uma requisição à API da Open AI para gerar a resposta:

```C#
while (true)
{
    Console.Write(">>> ");
 
    string? userPrompt = Console.ReadLine();
 
    CompletionResponse response = await completionsApi.CreateCompletion(new CompletionRequest
    {
        Prompt = userPrompt
    });
 
    ChoiceCompletionResponse? choice = response.Choices.FirstOrDefault();
 
    Console.WriteLine(choice?.Text);
}
```

O `Program.cs` completo fica assim:

```C#
using Microsoft.Extensions.Configuration;
using Refit;
using MyChatGPT;

IConfiguration config = new ConfigurationBuilder()
    .AddJsonFile("appsettings.json")
    .Build();

var completionsApi = RestService.For<ICompletionsAPI>("https://api.openai.com", new RefitSettings
{
    AuthorizationHeaderValueGetter = () => Task.FromResult(config["ApiKey"] ?? string.Empty),
});

Console.WriteLine("Welcome to MyChatGPT! Please, enter a prompt:");

while (true)
{
    Console.Write(">>> ");

    string? userPrompt = Console.ReadLine();

    CompletionResponse response = await completionsApi.CreateCompletion(new CompletionRequest
    {
        Prompt = userPrompt
    });

    ChoiceCompletionResponse? choice = response.Choices.FirstOrDefault();

    Console.WriteLine(choice?.Text);
}
```

Com tudo pronto, podemos testar a aplicação:

```
PS C:\meus-repositorios\MyChatGPT> dotnet run
Welcome to MyChatGPT! Please, enter a prompt:
>>> Hi! How are you?


I'm doing well, thank you. How about you?
>>> Generate a Hello World in C#


using System;

namespace HelloWorld
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Hello World!");
        }
    }
}
>>>
```

Você pode consultar o código completo deste tutorial [neste repositório](https://github.com/danielmatoscastro/MyChatGPT).

## Conclusão

Neste tutorial vimos como podemos integrar uma aplicação .NET à API pública da Open AI e criamos um console application que imita o funcionamento do ChatGPT. Como você pode perceber durante os testes, os modelos disponíveis publicamente não são tão precisos quanto o ChatGPT, mas ainda assim o resultado é bem satisfatório. Você pode experimentar os diferentes parâmetros aceitos pela API para adequar o exemplo ao seu caso de uso específico.

Até a próxima!