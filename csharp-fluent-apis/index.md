Neste artigo vamos entender o que são e como podemos criar Fluent APIs no .NET com C#!

## O que são Fluent APIs
Fluent APIs ou APIs fluentes é uma forma que temos de compor nosso código de forma a facilitar o uso do mesmo.

Vamos supor que queremos fazer uma composição de código, para execução de algumas tarefas, e que estas precisam ser executadas em uma determinada ordem. Neste caso, os Fluent APIs se encaixam perfeitamente.

Não podemos confundir Fluent APIs com Fluent Mapping, utilizado no Entity Framework, embora o Fluent Mapping faça uso da técnica Fluent API.

Também não devemos confundir Fluent APIs com APIs Web ou Web APIs, que criamos no ASP.NET por exemplo.

## Exemplo de uso
Vamos tomar como base um exemplo que trabalhei há tempo atrás, onde havia a necessidade de gerar um arquivo `yml` a ser utilizado pelo GitHub Actions para deployment da aplicação.

Um arquivo `yml` é basicamente um texto, que deve ser escrito em uma ordem, utilizando determinados parâmetros e respeitando espaçamento.

```yml
name: Build and deploy package

on:
  push:
    branches:
      - main

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@main

    - name: Set up .NET Core
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '5.0.x'    

    - name: Build with dotnet
      run: dotnet build --configuration Release

    - name: Test
      run: dotnet test

    - name: Create the Package
      run: dotnet pack --configuration Release

    - name: Publish
      run: dotnet nuget push "./Gravatar/bin/Release/Gravatar.1.1.0.nupkg" -k ${{ secrets.NUGET_TOKEN }} -s https://api.nuget.org/v3/index.json
```

O problema é que um simples espaço fora de lugar e boom, deployment quebrado. Desta forma, criamos uma espécie de gerador de `yml` onde podemos validar via código, com testes de unidade.

Desta forma, chegamos ao uso das **Fluent APIs** para estruturar o código de uma maneira limpa e previnir possíveis erros.

```csharp
var workflow = GitHubActionBuilder
    .Configure()
    .WithName("My workflow name")
    .OnBranch("main")
    .WithJob("Build and deploy", x =>
    {
        x.Key = "MyEnvVar";
        x.Value = "MyValue";
    })
    .RunsOn("ubuntu-latest")
    .AddStep("Setup .NET", "actions/setup-dotnet@v1")
        .With(x =>
        {
            x.Key = "dotnet-version";
            x.Value = "5.0.x";
        })
    .AddStep("Add GitHub Packages Source", "dotnet nuget add source ...")
    .AddStep("Build with dotnet", "dotnet build ...")
    .Build("main-workflow.yml");
```

## Iniciando uma Fluent API
A primeira coisa que precisamos para iniciar uma Fluent API é criar uma classe que contém um construtor. Normalmente mantemos este contrutor privado, já que não vamos precisar dele futuramente.

```csharp
public class GitHubActionBuilder 
{
    private GitHubActionBuilder()
    {
    }
}
```

## Adicionando ações
Com a Fluent API iniciada, o próximo passo é criar algumas ações, e aqui normalmente começamos com um método que não tem muito significado externamente ou que realiza apenas uma configuração.

```csharp
public static GitHubActionBuilder Configure()
{
    return new GitHubActionBuilder();
}
```

Entretanto, todos os métodos de uma Fluent API precisam retornar ela mesma, seja utilizando um `new GitHubActionBuilder()` (Nome da nossa classe) ou `this`.

No [**Flunt**](https://github.com/andrebaltieri/Flunt/tree/main/Flunt) por exemplo, o primeiro método chama-se `Requires` e não tem nada nele, é apenas um **Syntax Sugar**.

Desta forma, já podemos iniciar nosso código, utilizando a chamada ao `GitHubActionBuilder.Configure()` como mostrado abaixo.

```csharp
var workflow = GitHubActionBuilder
    .Configure();
```

## Problemas neste código
Por mais que este código funcione, há um problema nele. Vamos adicionar mais um método na classe `GitHubActionBuilder` para analisar.

```csharp
public class GitHubActionBuilder 
{
    private GitHubActionBuilder()
    {
    }

    public static GitHubActionBuilder Configure()
    {
        return new GitHubActionBuilder();
    }
    
    public static GitHubActionBuilder WithName()
    {
        return new GitHubActionBuilder();
    }
}
```

Mesmo com um novo método adicionado, não conseguimos encadar as chamadas, não conseguimos por exemplo fazer uma chamada como abaixo.

```csharp
var workflow = GitHubActionBuilder
    .WithName()
    .Configure();
```

Pior ainda, e se houver a necessidade explícita do método `WithName` vir somente depois do `Configure`? Como resolvemos isto?

## Interfaces
A solução são as interfaces! Sim, com elas podemos limitar os retornos e permitir que o `GitHubActionBuilder` faça uso de várias delas.

É importante lembrar que nas novas versões do C# (9+) podemos ter implementações nas interfaces, o que provê herança múltipla aqui.

### Criando as interfaces
Vamos criar duas interfaces para entender o processo, mas este se repete quantas vezes você precisar, então não vamos fazer todo código aqui.

```csharp
public interface IWorkflow
{
    public IWorkflowName WithName(string name);
}

public interface IWorkflowName
{
}
```

Como podemos notar, temos duas interfaces, `IWorkFlow` e `IWorkFlowName` onde a primeira interface tem um método chamado `WithName` e retorna a segunda interface.

Aqui está o segredo, uma interface retorna ela mesma ou alguma outra interface. Assim conseguimos restringir o uso delas na ordem que seus retornos de métodos dizem.

Movendo adiante, vamos adicionar as duas interfaces a classe principal, separadas por vírgula e precedidas por `:`.

```csharp
public class GitHubActionBuilder : 
    IWorkflow,
    IWorkflowName
{
    ...
```

Para finalizar, vamos implementar as interfaces, mantendo o mesmo método `Configure` anterior, com exceção do seu retorno que agora é um `IWorkFlow`.

```csharp
public class GitHubActionBuilder : 
    IWorkflow,
    IWorkflowName
{
    private GitHubActionBuilder()
    {
    }

    public static IWorkflow Configure()
    {
        return new GitHubActionBuilder();
    }

    public IWorkflowName WithName()
    {
        return this;
    }
}
```

Note que adicionamos o método `WithName` que retorna `this`. Tanto o retorno do `new GitHubActionBuilder()` quanto `this` são possíveis aqui pois a classe implementa estas interfaces.

## Consumindo a Fluent API
Agora podemos notar que ao tentar consumir nossa API, não conseguimos chamar o método `WithName` antes do `Configure`.
```csharp
var workflow = GitHubActionBuilder
    .WithName("Build and Deploy")
    .Configure();
```
Somos obrigados a seguir a ordem correta de execução, conforme definimos nas interfaces previamente.

```csharp
var workflow = GitHubActionBuilder
    .Configure()
    .WithName("Build and Deploy");
```

## Conclusão
O C# é muito poderoso no quesito de organização e restrição de código. Podemos facilitar o consumo das nossas APIs já guiando os usuários para uma forma correta!