Neste artigo vamos aprender como podemos utilizar o pacote Serilog para logar informações das nossas aplicações.

## O que é o Serilog?
O **Serilog** é uma biblioteca para log de informações de qualquer aplicação .NET, que trabalha com **Sinks** como base.

O **Serilog** em sí, é uma estrutura que nos permite salvar quaisquer tipos de informação, de forma organizada, durante a execução da aplicação.

### Sinks
Quando trabalhamos com logs, os gostos e necessidades variam muito. Cada empresa ou time, opta por concentrar seus logs em um local diferente e o **Serilog** auxilia nisso.

O que acontece é que o Serilog possui apenas a implementação básica dos logs e os **Sinks** fazem todo o trabalho sujo aqui.

Desta forma, caso queira que o **Serilog** escreva seus logs em um arquivo, basta utilizar o **Sink.File**. O mesmo acontece para E-mails com **Sink.Email** e até para o Discord com **Sink.Discord**.

Você também pode criar seu próprio **Sink** caso queira armazenar os logs em algum local específico da sua empresa cujo os Sinks padrões não te atenda.

## Setup
O primeiro passo para utilizar o **Serilog** é fazer a instalação do seu pacote principal, cujo todos os Sinks derivarão. Isto pode ser feito executando o comando abaixo.

```
dotnet add package Serilog.AspNetCore
```
Para finalizar, precisamos adicionar o `UseSerilog` no `Host` da aplicação, conforme mostrado abaixo.

```csharp
builder.Host.UseSerilog(...);
```

É importante notar, que neste caso deixamos as configurações vazias por enquanto (`...`), o que causará um erro se tentarmos executar o programa.

Posteriormente veremos como configurar os **Sinks** da maneira correta, para diferentes tipos de log.

### Log no Console
A primeira forma e mais simples de termos quaisquer informações durante a execução das nossas aplicações é visualizar o **Console** (Terminal onde a aplicação está sendo executada).

Para logar as informações no **Console** utilizando **Serilog**, precisamos primeiro configurar o método `WriteTo`, apontando para `Console`.

```csharp
builder.Host.UseSerilog((ctx, lc) => lc
    .WriteTo.Console(LogEventLevel.Debug)
```

Note que neste exemplo utilizamos o `LogEventLevel.Debug` o que significa que **apenas mensagens de Debug** serão logadas no **Console**.

Neste ponto, todas as mensagens de **Debug**, incluindo as do próprio .NET, serão logadas. O mesmo vale para **Warnings** e **Errors**, é só mudar o `LogEventLevel` para tê-las logadas.

#### Mensagens customizadas
Caso queira disparar alguma mensagem customizada durante a execução da aplicação, podemos utilizar o `Log` do namespace `Serilog`, informando o tipo da mensagem e texto que desejamos logar.

```csharp
using Serilog;

Log.Error("Mensagem de erro");
Log.Information("Mensagem de informação");
Log.Fatal("Erro fatal");
Log.Debug("Mensagem de Debug");
Log.Warning("Mensagem de Warning");
```

### Log no E-mail
Seguindo a mesma linha de raciocínio anterior, podemos ter as mensagens logadas via E-mail. Neste caso temos que ter um cuidado especial para não encher a caixa de E-mails de mensagens.

O **Serilog** possui um **Sink** que faz este trabalho, e podemos instalá-lo através do pacote como demonstrado abaixo.
```
dotnet add package Serilog.Sinks.Email
```

Feito isto, tudo que precisamos fazer é configurar o **Sink** no `Program.cs` como fizemos anteriormente com o **Console**.

```csharp
builder.Host.UseSerilog((ctx, lc) => lc
    .WriteTo.Console(LogEventLevel.Debug)
    .WriteTo.Email(new EmailConnectionInfo
    {
        Port = 587,
        EmailSubject = "TESTE",
        EnableSsl = true,
        FromEmail = "hello@balta.io",
        MailServer = "smtp.sendgrid.net",
        NetworkCredentials = new NetworkCredential("apikey", "pwd"),
        ToEmail = "hello@balta.io",
        IsBodyHtml = true
    }));
```

### Log no arquivo

Por fim temos o `Serilog.Sinks.File` que nos permite logar mensagens em um arquivo (Texto neste caso). Este método particularmente é um dos que mais utilizo.

```csharp
builder.Host.UseSerilog((ctx, lc) => lc
    .WriteTo.Console(LogEventLevel.Debug)
    .WriteTo.File("log.txt",
        LogEventLevel.Warning,
        rollingInterval: RollingInterval.Day));
```

## Outros Sinks

Algo que gosto no **Serilog** é o ótimo suporte por parte da comunidade. É quase impossível precisar de um **Sink** que ainda não existe, [**confira a lista completa aqui**](https://github.com/serilog/serilog/wiki/Provided-Sinks).

Aqui no balta por exemplo, utilizamos o **Sink** do **Discord**, já que temos nossas informações todas concentradas lá. É muito legal conseguir ver o que está acontecendo no site e ainda poder agir de forma pro-ativa.

## Conclusão
Escrever logs de forma simples e fácil, com extesões e suporte para múltiplos formatos e plataformas no .NET é com o **Serilog**. Ele é um dos meus pacotes preferidos!