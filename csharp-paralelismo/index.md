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
Teremos a seguinte saída no console:

![image](https://github.com/diegostan/blog/blob/main/csharp-paralelismo/images/saida1.png)

>Note o tempo gasto para realizar o processamento total

## Utilizando o processamento paralelo
Agora iremos adaptar nossas chamadas e utilizarmos o processamento paralelo.
O primeiro passo é configurarmos uma instância da classe ``ParallelOptions`` para passarmos as configurações:
``` csharp
var parallelOptions = new ParallelOptions();
parallelOptions.MaxDegreeOfParallelism = 8;
```
A opção **MaxDegreeOfParallelism** está atribuida com 8 o que significa que iremos utilizar um máximo de 8 threads para o processamento.
Agora vamos substituir nosso loop foreach sequencial por um loop presente na classe parallel

Antes:
``` csharp
foreach(var cep in ceps)
{
	listaCep.Add(new ViaCepService().GetCep(cep));
}
```
Com a classe parallel:
``` csharp
Parallel.ForEach(ceps, parallelOptions, cep  =>
{
	listaCep.Add(new  ViaCepService().GetCep(cep));
});
```
Note como é simples utilizar o ForEach da classe Parallel.

O método exige 2 parâmetros obrigatórios, a coleção(ceps) a ser percorrida e o elemento da coleção(cep), as opções de paralelismo(parallelOptions) são opcionais.

Isso é o suficiente para realizarmos o processamento paralelo, agora vamos rodar a aplicação com o comando:
```
dotnet run
```

Agora podemos ver o resultado no console:
![image](https://github.com/diegostan/blog/blob/main/csharp-paralelismo/images/saida2.png)

Note como o tempo de processamento caiu drasticamente após o uso do paralelismo.

## O que está acontecendo ?
Basicamente a classe Parallel gerencia e distribui a carga de trabalho entre as Threads de CPU utilizando um agendador de tarefas, tudo isso é feito por trás do framework e nosso único trabalho é realizar a solicitação.
Nosso serviço de consulta de CEP realizava uma consulta por vez, ou seja, após o término de uma consulta iniciava-se outra, com o paralelismo essas chamadas são feitas concomitantemente sem necessariamente aguardar a anterior, porém isso pode se tornar um problema como veremos.

## Por que não rodar tudo em paralelo ?
Vimos um grande salto de performance nas nossas consultas, então, por que não transformar todo e qualquer processamento em paralelo ?
### Gerenciamento de estado
Um dos principais motivos é a dificuldade de gerenciar estados em processamentos paralelos.
Quando estamos atuando em um determinado objeto, apenas a Thread que executa esse objeto o conhece, vamos imaginar um cenário onde estamos tratando uma entidade de negócio da nossa aplicação, queremos gerenciar essa entidade sem perder seu estado e seus valores, se ficarmos passando de Thread em Thread teremos estados inconclusivos a cada processamento. 
### Dependências entre objetos
Dentro de um software temos diversas dependências entre objetos, o objeto A precisa de dados de um objeto B para ser construído, acoplamentos são necessários dentro da programação orientada a objeto, por mais que façamos o máximo para evitá-lo, sempre teremos uma dependência entre os objetos.

Ao trabalharmos com paralelismo temos de ter o cuidado para não afetar o estado de outro objeto que ainda nem existe, por isso não é tão simples assim utilizar essa abordagem.
### Cenários para processamentos paralelos
Se você precisa realizar muitas requisições para um serviço, o paralelismo é bem vindo para reduzir o tempo total de processamento.

Procure sempre montar os objetos antes de realizar o processamento paralelo, isso diminui a dependência e  facilita o gerenciamento de estado.

Tome cuidado ao utilizar essa abordagem em bancos de dados, cada Thread irá exigir uma conexão aberta com o mecanismo, isso pode ser um fator limitante, lembre-se que bancos de dados são extremamente velozes se utilizados de forma correta. Considere usar o paralelismo nessas situações após uma longa análise de performance em outros setores (queries, conexões etc).
 
 ## Finalizando
 Nesse artigo vimos como utilizar o processamento paralelo utilizando a classe Parallel do .NET.
 
É muito importante colocarmos na balança os prós e contras desse tipo de processamento antes de utilizá-lo.

Além do que foi visto aqui, temos também o laço For e o PLINQ que também fazem parte dessa abordagem.

Confira a documentação:
[Introdução ao PLINQ | Microsoft Docs](https://docs.microsoft.com/pt-br/dotnet/standard/parallel-programming/introduction-to-plinq)
[Paralelismo de dados (biblioteca de tarefas paralelas) | Microsoft Docs](https://docs.microsoft.com/pt-br/dotnet/standard/parallel-programming/data-parallelism-task-parallel-library)

Código desse exemplo:
[Paralelismo: Projeto referente a Paralelismo em .NET](https://github.com/diegostan/Paralelismo)

Com isso vou ficando por aqui, grande abraço.