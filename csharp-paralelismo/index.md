# Paralelismo em C#
Paralelismo é quando realizamos mais de uma tarefa de forma concomitante, ou seja, essas tarefas são processadas lado a lado sem a dependência uma da outra;
Antigamente era bastante complexo lidar com múltiplas threads de forma manual, configurar bloqueios e semáforos era uma tarefa bem complicada. 
Dentro da plataforma .NET, a partir da versão 4.0 foi introduzido a classe **Parallel** que abstrai todo o trabalho de baixo nível e nos fornece uma forma muito mais simples de lidar com esse tipo de programação. Nesse artigo iremos conhecer essa classe e seus principais recursos.

## A biblioteca Parallel
Através da classe [System.Threading.Tasks.Parallel](https://docs.microsoft.com/pt-br/dotnet/api/system.threading.tasks.parallel) teremos acesso aos recursos para prosseguir com esse artigo.
## Criando o projeto
Para esse exemplo iremos utilizar uma simples aplicação de console.
Vamos criar o projeto usando o comando:
```
dotnet new console -o Paralelismo
```
Agora basta abrir o projeto com seu editor ou IDE favorito.
## O projeto
Para simularmos um caso real de uso da programação paralela, iremos consumir a API pública do [ViaCEP](https://viacep.com.br/).
Esse WebService fornece dados de uma determinada localidade, basta informarmos o CEP e teremos o retorno das informações via arquivo JSON.
## Criando um Model
Vamos criar uma classe chamada CepModel com o seguinte código:

``` csharp
public  class  CepModel
{
[JsonPropertyName("cep")]
public  string  Cep { get; set; }

[JsonPropertyName("bairro")]
public  string  Bairro { get; set; }

[JsonPropertyName("localidade")]
public  string  Localidade { get; set; }

[JsonPropertyName("uf")]
public  string  Uf { get; set; }

public  override  string  ToString()
{
	return  $"{this.Cep} - {this.Uf} - {this.Bairro} - {this.Localidade}";
}
```
> Observe que estamos sobreescrevendo o método ToString() para retornarmos as propriedades da classe e não sua reflexão.
## Criando a classe de serviço
Agora vamos criar uma classe chamada **ViaCepService** onde iremos consumir o WebService:
``` csharp
public class ViaCepService
{
	public CepModel GetCep(string  cep)
	{
		var client  =  new  HttpClient();
		var response  =  client.GetAsync($"https://viacep.com.br/ws/{cep}/json/").Result;
		var content  =  response.Content.ReadAsStringAsync().Result;
		var cepResult  =  JsonSerializer.Deserialize<CepModel>(content);
		
		return cepResult;
	}
}
```
## Consultando o serviço de forma sequencial
Vamos criar um Array de String onde iremos passar alguns CEPS:
``` csharp
string[] ceps  =  new  string[5];
ceps[0] = "07155081";
ceps[1] = "15800100";
ceps[2] = "38407369";
ceps[3] = "77445100";
ceps[4] = "78015818";
```
Agora iremos utilizar um loop simples do tipo foreach para percorrermos esse Array e a cada novo elemento iremos chamar o serviço sequencialmente e adicionar o resultado em uma lista:
``` csharp
var listaCep = new List<CepModel>();
foreach(var cep in ceps)
{
listaCep.Add(new ViaCepService().GetCep(cep));
}
```
## Medindo os tempos com a classe Stopwatch
Através do namespace ``System.Diagnostics``, podemos ter acesso à classe **StopWatch** onde iremos computar o tempo de processamento das operações.
Vamos criar uma instância:
``` csharp
var stopWatch = new Stopwatch();
```
Agora vamos disparar o stopwatch antes do loop e pará-lo após:
``` csharp
stopWatch.Start(); // Inicia a contagem
var listaCep = new List<CepModel>();
foreach(var cep in ceps)
{
listaCep.Add(new ViaCepService().GetCep(cep));
}
stopWatch.Stop(); // Para a contagem
```

Após o termino iremos apresentar o resultado no console:
``` csharp
Console.WriteLine($"O Tempo de processamento total é de {stopWatch.ElapsedMilliseconds} ms");
```

Agora temos de apresentar o resultado das consultas, vamos fazer um loop simples na variável genérica ``listaCep`` e passar o resultado para o console:
``` csharp
listaCep.ToList().ForEach(cep => Console.WriteLine(cep));
```

Com tudo finalizado podemos rodar a aplicação com o comando:
```
dotnet run
```
Termos a seguinte saída no console:
![image](https://github.com/diegostan/blog/blob/main/csharp-paralelismo/images/saida1.png)
>Note o tempo gasto para realizar o processamento total