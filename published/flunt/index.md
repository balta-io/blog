Flunt é uma forma de implementar um padrão de notificações em sua aplicação para concentrar erros e mudanção em determinadas ações e entidades.

## Breaking Changes
Se você está usando o Flunt em versões inferiores a `2.0.0` recomendo ver a documentação e código fonte antes de migrar. A versão `2.x` inclui diversas mudanças que vão causar quebras no seu código.

## Instalação
Para começar a trabalhar com o Flunt, basta realizar sua instalação por dentro do Visual Studio ou via terminal com o comando abaixo.

```
dotnet add package Flunt --version 2.0.0
```

O Flunt se baseia no `.NET Standard 2.0`, então se o seu projeto tem como target qualquer versão do .NET suportada nesta especificação, você não terá problemas em instalar este pacote.

## Notificações

O Flunt nasceu de duas necessidades, a implementação do **Domain Notification Pattern** para [substituir Exceptions](https://balta.io/blog/exception-vs-domain-notification) a nível de domínio na aplicação e para reduzir a quantidade de IFs (Complexidade) utilizando uma abordagem por contratos.

Desta forma, basicamente o que Flunt faz é adicionar uma lista de `Notification` (Notificações) a sua classe e diversos métodos para interagir com ela.

```csharp
public class Notification
{
    public Notification()
    {

    }

    public Notification(string key, string message)
    {
        Key = key;
        Message = message;
    }

    public string Key { get; set; }
    public string Message { get; set; }
}
```

Acima temos a notificação base, mais simples possível, do qual o Flunt faz uso através da class `Notifiable`.

```csharp
public abstract class Notifiable<T> where T : Notification
{
    ...
}
```

Desta forma, para começar a concentrar notificações em uma classe, tudo o que você precisa fazer é herdar de `Notifiable<T>` onde `T` é um tipo de `Notification`, como por exemplo.

```csharp
public class Customer : Notifiable<Notification>
{
    ...
}
```

Feito isto, você abrirá várias possíbilidades para interagir com as notificações desta classe, como por exemplo o `AddNotification()` e o `IsValid`.

```csharp
var customer = new Customer();
customer.AddNotification("Chave", "Valor");
customer.IsValid; // Retorna falso pois adicionamos uma notificação

foreact(var item in customer.Notifications)
{
    // Percorre todas as notificações
}
```

### Notificações customizadas
Caso queira ter mais informações nas suas notificações, você pode criar uma `Custom Notification` que herda de `Notification` e tem a propridades adicionais, como por exemplo.

```csharp
public class CustomNotification : Notification
{
    public CustomNotification(string key, string message)
    {
        Key = key;
        Message = message;
        Date = DateTime.Now;
    }

    public CustomNotification(string key, string message, DateTime date)
    {
        Key = key;
        Message = message;
        Date = date;
    }

    // Nova propriedade
    public DateTime Date { get; set; }
}
```

Como por padrão o Flunt não sabe como interagir com sua notificação customizada, você precisa "explicar" para ele como converter uma `Notification` para uma `CustomNotification` e isto é feito através do `CustomNotifiable`.

```csharp
public class CustomNotifiable : Notifiable<CustomNotification>, INotifiable
{
    public void AddNotifications(IEnumerable<Notification> notifications)
    {
        foreach (var item in notifications)
        {
            var notification = new CustomNotification(item.Key, item.Message, DateTime.Now);
            AddNotification(notification);
        }
    }
}
```

Seu `CustomNotifiable` precisa implementar a interface `INotifiable` que conterá o método para converter as notificações em `CustomNotification`.

```csharp
public class Customer : CustomNotifiable<CustomNotification>
{
    ...
}

var customer = new Customer();
AddNotification(new CustomNotification("Chave", "Valor"));
customer.IsValid; // Retorna falso pois adicionamos uma notificação

foreact(var item in customer.Notifications)
{
    // Percorre todas as notificações
}
```

Note que agora o `Customer` herda de `CustomNotifiable<CustomNotification>` ao invés de `Notifiable<Notification>`. Isto define que você está usando as notificações customizadas e não as padrões do Flunt.

## Validações

Porém só a implementação das notificações não justificaria o pacote, então o Flunt também traz muitas validações (Muitas mesmo) nas quais você pode adicionar aos seus contratos.

A cada versão adicionamos mais notificações e elas ficam testadas, o que remove a necessidade de duplicar estes testes na sua aplicação.

Para ver todas as notificações disponíveis para o Flunt, visite o projeto no GitHub.

* [https://github.com/andrebaltieri/Flunt/tree/main/Flunt/Validations](https://github.com/andrebaltieri/Flunt/tree/main/Flunt/Validations)

## Contratos/Especificações

Para utilizar uma validação é preciso de um contrato, e este contrato pode ser criado sobre qualquer classe. Normalmente criamos eles para entidades ou comandos no domínio, mas fique à vontade para dar asas a sua imaginação.

```csharp
public class CreateEmailContract : Contract<Email>
{
    public CreateEmailContract(Email email)
    {
        Requires()
            .IsEmail(email.Address, "Email");
    }
}
```

No exemplo acima, temos um contrato para validar a criação de um E-mail. Este contrato herda de `Contract<Email>` e desta forma, podemos utilizar o método `Requires()` seguido da validação `IsEmail` contida no Flunt.

Este mesmo código, sem a adição de contratos e as validações do Flunt, ficar assim.

```csharp
public class CreateEmailContract : Contract<Email>
{
    public CreateEmailContract(Email email)
    {
        if (!Regex.IsMatch(email.Address ?? "", @"^\w+([-+.']\w+)*@\w+([-.]\w+)*\.\w+([-.]\w+)*$"))
            AddNotification("Email", "E-mail inválido");
    }
}
```

Desta forma sempre que chamarmos o método construtor do `CreateEmailContact` passando um E-mail, teremos a validação sendo executada.

Podemos também concentrar as notificações em outras entidades, simplesmente passando um contrato para elas.

```csharp
public class Email : Notifiable<Notification>
{
    public Email(string address)
    {
        Address = address;
        AddNotifications(new CreateEmailContract(this));
    }

    public string Address { get; private set; }
}
```

O exemplo completo [você pode conferir aqui](https://github.com/andrebaltieri/Flunt/tree/main/Flunt.Samples).

## Localização

Outra novidade da versão 2.0 do Flunt são as mensagens padrão. Sempre que você chama uma validação como `IsEmail` por exemplo, pode optar por informar uma mensagem customizada ou simplesmente utilizar a mensagem padrão, que para este caso seria `should be an email`.

As mensagens no Flunt são constantes, desta forma você pode sobrescrevê-las simplesmente substituindo seus valores.

```csharp
FluntErrorMessages.IsEmailLocalizationErrorMessage = "deve ser um E-mail";
```

## Problemas e bugs
Caso encontre um bug ou qualquer outro problema, fique à vontade em abrir uma issue no [GitHub do projeto](https://github.com/andrebaltieri/Flunt/issues).

## Como contribuir
Agradeço de coração a todos os contribuidores que ajudam no Flunt. O pacote sozinho não seria nada. [Confira a lista de contribuidores aqui](https://github.com/andrebaltieri/Flunt/graphs/contributors).

Caso queria contribuir com o pacote, é só seguir estes passos.
* Não faça Pull Request para a branch `main`
* Siga a estrutura do projeto
* Escreva pelo menos um teste de unidade

<div role="main" id="blog-s1-dotnet-134e3db1eea6c9829db1"></div>
<script type="text/javascript" src="https://d335luupugsy2.cloudfront.net/js/rdstation-forms/stable/rdstation-forms.min.js"></script>
<script type="text/javascript"> new RDStationForms('blog-s1-dotnet-134e3db1eea6c9829db1', 'UA-48664517-12').createForm();</script>