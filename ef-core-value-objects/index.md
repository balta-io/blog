Neste artigo vamos aprender a mapear objetos de valor no Entity Framework Core, de maneira simples e fácil.

## O que são objetos de valor?
Objetos de valor ou **Value Objects** são estruturas imutáveis que compoe entidades de negócio, concentrando regras e removendo a necessidade de reescrita de código.

### Primitive Obsession
Uma das premissas para o uso dos **Value Objects** é evitar a obsessão primitiva ou **Primitive Obsession**, um termo utilizado quando trabalhamos apenas com tipos primitivos nas nossas entidades.

Para ilustrar melhor, vamos tomar como base a classe `User`, que basicamente qualquer sistema tem.

```csharp
public class User 
{
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public string Email { get; set; }
    public string Password { get; set; }
}
```

Este é um caso clássico de entidade **anêmica**. Ela basicamente reflete os dados da *tabela* e não o negócio em sí. Para piorar ela ainda está totalmente obsecada por tipos primitivos (`int`, `string`, `bool` e por ai vai).

Agora imagina que você precisa validar este E-mail no usuário, depois precisa novamente do E-mail no Aluno, depois no Fornecedor, depois na Venda, e assim por diante.

Como você deve imaginar, o E-mail por exemplo, tem sua regra de negócio, e a mesma precisará se aplicar para todo e qualquer E-mail no sistema.

Desta forma, por que não criar um tipo chamado E-mail, que contém as validações.

```csharp
public class Email 
{
    public Email(string address)
    {
        if (string.IsNullOrEmpty(address) || address.Length < 5)
            throw new InvalidEmailException();

        Address = address.ToLower().Trim();
        const string pattern = @"^\w+([-+.']\w+)*@\w+([-.]\w+)*\.\w+([-.]\w+)*$";

        if (!Regex.IsMatch(address, pattern))
            throw new InvalidEmailException();
    }

    public string Address { get; }
}
```

Com nosso tipo criado, passamos a utilizar mais tipos complexos e menos tipos primitivos, e como o E-mail compoe uma entidade mas não tem uma razão para existir sozinho, ele é classificado como um **Value Object**.

```csharp
public class User 
{
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public Email Email { get; set; }
    public string Password { get; set; }
}
```

### Imutabilidade
Uma das coisas que sempre trago para as minhas entidades e objetos de valores é a imutabilidade, ou seja, uma vez construído, o objeto não é mais alterado (Pelo menos não externamente).

Isto nos leva a privar ou remover o `set` das propriedades por exemplo, como fizemos acima no `Email`. Este processo também permite que a classe seja passível de extensões mas fechada para modificações.

> Confira mais sobre DDD, modelagem de domínios, entidades e objetos de valor no nosso curso [**Modelando Domínios Ricos**](https://balta.io/cursos/modelando-dominios-ricos)

### Comparação
Apenas para intuito de comparação, vamos acompanhar a mudança de uma entidade anêmica para a segmentação em objetos de valores.

Como vimos o primeiro passo é sair de uma classe anêmica, obssecada por tipos primitivos, para algo mais estruturado, voltado a objetos de valores.

```csharp
// BAD 🚫
public class User 
{
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public string Email { get; set; }
    public string Password { get; set; }
}
```

```csharp
// GOOD ✅
public class User 
{
    public User(Name name, Email email, Password password)
    {
        Name = name;
        Email = email;
        Password = password;
    }

    public Name Name { get; }
    public Email Email { get; }
    public Password Password { get; }
}
```
> **IMPORTANTE** O código acima não está completo, é apenas um exemplo, não tome ele como base para seus projetos.

Concluímos que a medida que andamos com objetos de valor, começamos a mover as regras para dentro deles e ter menos regras nas entidades em sí.

Isto ocorre em todo sistema, onde teremos cada vez menos código, até chegar nos **Controllers** da API por exemplo, que terão uma ou duas linhas de código.

Este movimento de "dentro para fora" é importante e necessário, afinal, quanto mais próximo do núcleo, mais livre de infraestrutura e mais fácil de testar é seu código.

> Se quiser conferir mais sobre testes de unidade, confira nosso curso [**Refatorando para testes de unidade**](https://balta.io/cursos/refatorando-para-testes-de-unidade).

Enfim, para se ter uma noção desse movimento de regras de negócio, este é um objeto de valor para **Email** que temos aqui no balta.

```csharp
public class Email : ValueObject
{
    // Obrigatório para funcionar com EF
    protected Email()
    {
    }

    public Email(string address)
    {
        if (string.IsNullOrEmpty(address) || address.Length < 5)
            throw new InvalidEmailException();

        Address = address.ToLower().Trim();
        const string pattern = @"^\w+([-+.']\w+)*@\w+([-.]\w+)*\.\w+([-.]\w+)*$";

        if (!Regex.IsMatch(address, pattern))
            throw new InvalidEmailException();

        Verified = false;
        VerificationCode = GenerateVerificationCode();
        VerificationCodeExpireDate = DateTime.UtcNow.AddHours(2);
    }

    public string Address { get; } = string.Empty;
    public bool Verified { get; private set; }
    public string VerificationCode { get; private set; } = string.Empty;
    public DateTime VerificationCodeExpireDate { get; private set; } = DateTime.UtcNow.AddHours(2);

    public void Verify(string verificationCode)
    {
        if (verificationCode != VerificationCode)
            throw new ArgumentException("Código de ativação inválido");

        if (VerificationCodeExpireDate > DateTime.UtcNow)
            throw new ArgumentException("Código de ativação expirado");

        Verified = true;
    }

    public void GenerateNewVerificationCode()
    {
        Verified = false;
        VerificationCode = GenerateVerificationCode();
        VerificationCodeExpireDate = DateTime.UtcNow.AddHours(8);
    }

    public void Expire() => Verified = false;

    private static string GenerateVerificationCode() => Guid.NewGuid().ToString().ToUpper()[..8];

    public static implicit operator string(Email email) => email.Address;

    public static implicit operator Email(string address) => new(address);

    public override string ToString() => Address;
}
```

## Mapeamento
Depois desta longa introdução sobre o que são e para que servem os objetos de valor, vem a pergunta... como colocar esta informação no banco de dados?

Uma das premissas da modelagem de domínios neste formato é não se prender ao banco. Lembra que no começo deste artigo comentamos sobre entidades anêmicas e como elas são apenas uma visualização de uma tabela do banco?

Porém, uma hora precisamos persistir estes valores em algum lugar, e normalmente fazemos isto utilizando algum ORM como por exemplo o Entity Framework.

### Value Objects não tem Id
Diferente das entidades, objetos de valor não possuem um identificador. Isto se dá ao fato deles serem parte da composição de entidades e não haver motivo para os mesmos existirem sozinhos.

### Mapeando o usuário
Vamos tomar como base para o mapeamento inicial, ele faz herança de uma classe base chamada `Entity` que possui a definição do `Id` e tem apenas um campo que não é um VO, o `Active`.

```csharp
public class User : Entity
{
    // Obrigatório para o EF funcionar
    protected User() { }

    public User(Name name, Email email, Password password)
    {
        Name = name;
        Email = email;
        Password = password;
        Active = false;
    }

    public Name Name { get; }
    public Email Email { get; }
    public Password Password { get; }
    public bool Active { get; }
}

public class UserMap : IEntityTypeConfiguration<User>
{
    public void Configure(EntityTypeBuilder<User> builder)
    {
        builder.ToTable("User");

        builder.HasKey(x => x.Id);

        builder.Property(x => x.Active)
            .HasColumnName("Active")
            .IsRequired(true);
    }
}
```

Como podemos notar, aqui temos o uso do mapeamento do Entity Framework sob o usuário, porém ignoramos as propriedades que são objetos de valor.

> Caso queira aprender mais sobre Entity Framework e mapeamento, confere nosso curso [**Fundamentos do Entity Framework**](https://balta.io/cursos/fundamentos-entity-framework).

### Mapeando objetos de valor
O Entity Framework não possui um método para mapear objetos de valor explicitamente, e nem precisa disso. Como comentamos anteriormente, os objetos de valor não tem **Id**, eles funcionam apenas como uma composição das entidades.

Desta forma, podemos por exemplo utilizar o `OwnsOne` (Possui um...) para dizer que o usuário possui um objeto aninhado que será mapeado para a mesma tabela.

```csharp
builder.OwnsOne(x => x.Email)
    .Property(x => x.Address)
    .HasColumnName("Email")
    .IsRequired(true);

builder.OwnsOne(x => x.Email)
    .Property(x => x.Verified)
    .HasColumnName("EmailVerified")
    .IsRequired(true);

builder.OwnsOne(x => x.Email)
    .Property(x => x.VerificationCode)
    .HasColumnName("EmailVerificationCode")
    .IsRequired(true);

builder.OwnsOne(x => x.Email)
    .Property(x => x.VerificationCodeExpireDate)
    .HasColumnName("EmailVerificationCodeExpireDate")
    .IsRequired(true);
```

Também fazemos uso do `HasColumnName` para nomear a coluna a ser gerada na tabela de uma forma legível para nós posteriormente.

Isto é tudo o que precisamos para mapear objetos de valor com Entity Framework. Neste momento você deve conseguir ler, salvar, editar e excluir informações utilizando seus VOs.

```csharp
public class UserMap : IEntityTypeConfiguration<User>
{
    public void Configure(EntityTypeBuilder<User> builder)
    {
        builder.ToTable("User");

        builder.HasKey(x => x.Id);

        builder.Property(x => x.Active)
            .HasColumnName("Active")
            .IsRequired(true);

        builder.OwnsOne(x => x.Email)
            .Property(x => x.Address)
            .HasColumnName("Email")
            .IsRequired(true);

        builder.OwnsOne(x => x.Email)
            .Property(x => x.Verified)
            .HasColumnName("EmailVerified")
            .IsRequired(true);

        builder.OwnsOne(x => x.Email)
            .Property(x => x.VerificationCode)
            .HasColumnName("EmailVerificationCode")
            .IsRequired(true);

        builder.OwnsOne(x => x.Email)
            .Property(x => x.VerificationCodeExpireDate)
            .HasColumnName("EmailVerificationCodeExpireDate")
            .IsRequired(true);
    }
}
```

## Conclusão
Trabalhar com objetos de valor torna o código mais burocrático e um pouco mais complexo, porém nos trás diversas vantagens a longo prazo como reuso do código por exemplo.

Em adicional, persistir estas informações com Entity Framework é uma tarefa simples, basta utilizar o **OwnsOne**.