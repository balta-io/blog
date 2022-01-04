Uma das funcionalidades

## O que são conversões explícitas e implícitas
Conversões implícitas são aquelas feitas pelo runtime de forma automática, que ele entende naturalmente, como por exemplo.

```csharp
int x = 100;
double y = x;
```

Neste caso, foi possível converter `x` de `int` para `double` naturalmente, afinal os números reais suportam inteiros. Porém o mesmo não ocorre neste caso.

```csharp
double x = 100.0;
int y = x;
```

Os números inteiros não são capazes (Pelo menos não sozinhos) de resolver números reais. Neste caso, precisamos explicitar a conversão.

```csharp
double x = 100.98;
int y = (int) x;

Console.WriteLine(y); 
// Imprime 100 na tela, ignorando o que vem depois do "."
```

## O que são os Implicit Operators
Os **Implicit Operators** são formas que temos de dizer ao compilador como resolver uma conversão de forma implícita, o que diminui a quantidade de código escrita.

Vamos pegar o segundo cenário, onde o compilador não sabe como converter um `double` para `int` naturalmente (de forma implícita). Neste caso, podemos criar um método dizendo como isto pode ser feito (Ignorando os valores depois do "." por exemplo) e assim teremos a possibilidade de converter `double` para `int` mais fácil.

## Implicit Operators com Strings
O exemplo acima foi apenas para entendimento dos **Implicit Operators**, mas convenhamos que escrever um código apenas para não precisar fazer `(int) x` é um pouco de exagero, ou não tem um real ganho.

Algo que faço com frequência nas minhas classes é sobrescrever o método `ToString` que todo objeto no .NET possui.

> Todo objeto no .NET deriva de uma classe base chamada **System** que possui o método **ToString** com o modificador **Virtual**, permitindo o mesmo ser reescrito.

No caso abaixo, que é um cenário bem comum nos cursos aqui do balta, temos um **Objeto de Valor** e seu método `ToString` sendo sobrescrito.

```csharp
public class Phone 
{
    public string CountryCode { get; set; }
    public string Area { get; set; }
    public string Number { get; set; }

    public override string ToString()
    {
        return $"+{CountryCode} ({Area}) {Number}";
    }
}
```

Desta forma, sempre que quiser exibir o telefone na tela, basta chamar `phone.ToString()` ou em casos de interpolação de string `$"Meu telefone é phone"` que ele já fará a conversão.

### Convertendo para Implicit Operators
Este cenário é um caso legal para uma refatoração, onde os **Implicit Operators** cairiam muito bem, fazendo não só o papel de converter uma string para um telefone quanto um telefone (Objeto) para uma string.

```csharp
public record Phone(string CountryCode, string Area, string Number)
{
    public static implicit operator string(Phone phone) => $"+{phone.CountryCode} ({phone.Area}) {phone.Number}";

    public static implicit operator Phone(string phone)
    {
        var data = phone.Split(" ");
        return new Phone(data[0], data[1], data[2]);
    }
}
```

Desta forma, podemos utilizar nosso objeto recebendo um string ou se convertendo automaticamente para uma, de forma simples e prática.

```csharp
// Cria um telefone (Objeto)
var phone = new Phone("55", "11", "999999999");
// Converte para string
Console.WriteLine(phone);

// Cria uma string
var telephone = "55 11 987765544";
// Converte para um telefone (Objeto)
phone = telephone;
Console.WriteLine(phone);

// RESULTADO
// +55 (11) 999999999
// +55 (11) 987765544
```

## DTOs e ViewModels
Chega de **AutoMapper**! Brincadeira! Mas se você acompanha meus cursos e conteúdos já deve ter percebido que quase nunca utilizo **AutoMapper** nos meus projetos.

O motivo é simples, eu posso fazer conversões dos meus objetos diretamente, sem **Reflection**. Talvez não seja tão produtivo quanto o **AutoMapper**, mas a performance vale essa queda na produtividade ao meu ver... pelo menos na maioria dos casos!

Vamos supor que temos uma entidade chamada `User` com as seguintes propriedades.

```csharp
public class User
{
    public int Id { get; set; }
    public string Username { get; set; }
    public string Password { get; set; }
    public bool Active { get; set; }
}
```

Agora queremos exibir apenas algumas propriedades deste usuário (Ocultar a senha por exemplo), então partimos para criação de um DTO ou ViewModel.

```csharp
public class UserViewModel
{
    public UserViewModel(string userName) 
        => UserName = userName;
    
    public string UserName { get; }
}
```

Neste caso, podemos criar fazer uso dos **Implicit Operators** para criar um método que converte um `User` em um `UserViewModel`, como fizemos anteriormente com tipos primitivos.

```csharp
public static implicit operator UserViewModel(User user) 
    => new UserViewModel(user.Username);
```

Como resultado, temos o seguinte código no `UserViewModel`.
```csharp
public class UserViewModel
{
    public UserViewModel(string userName) 
        => UserName = userName;
    
    public string UserName { get; }

    public static implicit operator UserViewModel(User user) 
        => new UserViewModel(user.Username);
}
```

E agora podemos ter a conversão de `User` para `UserViewModel` (E vice versa se implementarmos um método que faz a conversão de `UserViewModel` para `User`) de forma simples e fácil, sem necessidade do **AutoMapper** por exemplo.

```csharp
// Cria uma instância de um usuário
var user = new User
{
    Id = 1,
    Username = "balta",
    Password = "balta1234",
    Active = true
};

// Cria uma instância do ViewModel
// Recebe um usuário (Conversão implícita)
UserViewModel viewModel = user;
```
## Conclusão
O C# é rico em conteúdo, mas muitas vezes deixamos alguns pontos passarem que pode nos ajudar muito na redução de código e uso de pacotes externos.