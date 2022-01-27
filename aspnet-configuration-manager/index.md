Neste artigo vamos conferir as mudanças na leitura e carregamento das configurações que tivemos no ASP.NET 6.

## Configuration Manager
O .NET possui alguns recursos de configurações bem interessantes, que nos permitem carregar informações definidas em um arquivo JSON em nossas aplicações.

Ao criar uma aplicação Web por exemplo, nós temos a adição de dois arquivos de configuração em na raiz da aplicação, o `appsettings.json` e o `appsettings.Development.json`, que normalmente vem com o seguinte conteúdo.

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```
> **IMPORTANTE:** É possível (porém não recomendado) alterar os nomes destes arquivos. Para este exemplo, vamos manter os padrões.

### Development VS Production

Como pudemos notar, temos dois arquivos diferentes, um com a palavra `Development` e outro sem. O ASP.NET trabalha com variáveis de ambiente, e dentre elas temos uma muito importante, chamada `ASPNETCORE_ENVIRONMENT` que fica no arquivo `launchSettings.json` na pasta **Properties**.

É importante frisar que este arquivo (`launchSettings.json`) é o responsável pelas configurações de execução da aplicação. Tanto o Visual Studio, Visual Studio Code ou Rider, utilizam ele para depurar nossos Apps.

Vamos começar nossa brincadeira editando os arquivos `appsettings.json` e `appsettings.Development.json` adicionando os respectivos valores para a chave `Env` que vamos criar.

<small>**appsettings.Development.json**</small>
```json
{
  "Env": "development",
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  }
}
```

<small>**appsettings.json**</small>
```json
{
  "Env": "production",
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

## Lendo configurações
Podemos ler as configurações em dois momentos distintos na aplicação, durante a construção do App (build) e após o App ser construído. Em ambos o formato é sempre o mesmo, conforme mostrado abaixo.

```csharp
// App está na fase de build
builder.Configuration.GetValue<string>("Env");

// App já está pronto para ser executado
app.Configuration.GetValue<string>("Env");
```

> **IMPORTANTE:** Não confunda o build do App com a compilação da aplicação. Isto já ocorreu, aqui é a execução do código.

Note que usamos o `Configuration.GetValue` que permite definirmos um tipo, ou usar algo dinâmico. Neste caso, vamos tentar fazer um parse do valor para `string`, por isto utilizamos `Configuration.GetValue<string>("Env")`.

Para exemplificar, vamos exibir o valor na tela do Browser usando o exemplo padrão do projeto Web que o ASP.NET traz.

```csharp
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

app.MapGet("/", () => app.Configuration.GetValue<string>("Env"));

app.Run();
```

## Executando a aplicação
Para executar a aplicação em modo de desenvolvimento, você pode apertar o **play** na sua IDE favorita ou rodar o comando `dotnet run`. Tanto faz, o **Run** ou **Debug** vão passar pelo `launchSettings.json` que vai definir a variável de ambiente `ASPNETCORE_ENVIRONMENT` como `Development`.

Para rodar a aplicação no modo **Release**, podemos utilizar a sequência abaixo, para compilar em modo **Release** e executar o `exe` gerado.

```
dotnet build -c Release
dotnet publish -c Release
cd /bin/Release/net6.0/publish
./NomeDoSeuProjeto.exe
```

Neste momento, rodando a aplicação em modo **Development** você deve ter visto um valor diferente do exibido ao executar a aplicação em modo **Release**, cada um lido do seu respectivo arquivo de configuração.

## Lendo blocos de configurações


```csharp
var smtp = new Configuration.SmtpConfiguration();
app.Configuration.GetSection("SmtpConfiguration").Bind(smtp);
```

## App e Builder

## Connection Strings

## Variáveis de ambiente