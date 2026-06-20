# Sumário
1. [Introdução](#introducao)
2. [Conceito](#conceito)
    1. [Design Podre](#designpodre)
3. [Os Princípios](#principios)
    1. [Princípio da Responsabilidade Única](#srp)
    2. [Princípio do Aberto-Fechado](#ocp)
    3. [Princípio da Substituição de Liskov](#lsp)
    4. [Princípio da Segregação das Interfaces](#isp)
    5. [Princípio da Inversão de Dependência](#dip)

# Compreendendo os Princípios SOLID

<div id="introducao"></div>
## Introdução

Não é de hoje que a exigência por **profissionais que escrevem um código limpo e eficiente é evidente**, isso fica evidente em um [artigo](https://www.macoratti.net/09/01/net_ioc1.htm) publicado por [Macoratti](https://mvp.microsoft.com/pt-br/PublicProfile/4025407?fullName=%20%20Macoratti) sobre [Inversão de Controle](https://balta.io/blog/inversion-of-control), na qual ele diz o seguinte: 

> “O cenário atual aponta para uma **grande demanda por software de qualidade com prazo e custos cada vez mais reduzidos**. Não há mais lugar para desculpas como falta de recursos ou de tempo. Ou você oferece o que o mercado deseja ou esta fora”. 

[Robert Cecil Martin](https://pt.wikipedia.org/wiki/Robert_Cecil_Martin) já no século XX publicou uma série de princípios, claro, baseado nos estudos de outros cientistas, como forma de **facilitar, otimizar e aprimorar o design orientado à objetos**, tais as quais, são grandes responsáveis por promover o baixo acoplamento do código, promover uma boa manutenibilidade e promover a facilidade com relação a [realização de testes](https://balta.io/blog/fundamentos-testes-de-unidade). Nesse contexto, esse artigo estará apresentando os cinco principais princípios denotados pela palavra [SOLID](https://pt.wikipedia.org/wiki/SOLID) e falaremos um pouco das vantagens da utilização de cada um.

<div id="conceito"></div>
## Como surgiu o conceito

O conceito se originou a partir de um estudo feito sobre Princípios de Design e Padrões de Projeto, nas quais buscou contrapor aos sintomas de um design podre. Tal Design Podre é contemplado por algumas características:

<div id="designpodre"></div>
- **Rigidez**: Tendência em que um software possui com baixa capacidade de flexibilidade à mudanças.
- **Fragilidade**: Capacidade que um software tem em se quebrar quando uma parte do código é alterado em um efeito cascata.
- **Imobilidade**: Incapacidade que um software tem de reutilizar partes do código de um projeto.
- **Viscosidade**: Implementação de códigos eficientes são custosos, enquanto que a implementação de códigos ineficientes a primeira momento aparentam ser performáticos.

Tais conceitos, representam o momento em que o design de software está apodrecendo aos poucos, já que não contribuem para a boa manuteniblidade e flexibilidade de utilização do código. Dessa maneira, os princípios SOLID são responsáveis por **amenizar os impactos causados pela falta de manejo técnico do código**.

<div id="principios"></div>
## Os Princípios SOLID

<div id="srp"></div>
### Letra "S": Single Responsability Principle (Princípio da Responsabilidade Única)

Esse princípio se torna na ideia de que uma Classe/Método/Função deve ter apenas uma razão para mudar. Com outras palavras, ela tem que ter uma única responsabilidade, já que, desse modo, ela irá ter apenas uma razão para mudar como consequência. Isso acontece, pois quando uma classe não está segmentada do modo correto ela passa a ter mais que um objetivo de execução e assim sendo provocado um baixo reuso do código e um aumento no seu acoplamento, acrescentado do aumento de decisões que podem ser tomadas pela classe. Veja a seguir um exemplo ruim de SRP (Single Responsability Principle):

```cs
namespace APP.Classes{
    public class Mercado
    {
        public void CadastrarCliente(string NomeCliente){/* */}

        public void CriarPedido(int NumeroPedido){/* */}
    }
}
```

Como pode-se perceber a classe Mercado possui duas razões para mudar, ou dois objetivos de execução: 
- `CadastrarCliente(stringNomeCliente);`
- `CriarPedido(int NumeroPedido);`

Para reparar esse mau uso do código, podemos decompor a `Classe Mercado` em outras duas classes `Cliente` e `Pedido`, de modo que diminua o acoplamento entre as funcionalidades e consequentemente provoque liberdade para reuso facilitado do código:

```cs
namespace APP.Classes{
    public class Cliente
    {
        public void CadastrarCliente(string NomeCliente){/* */}
    }

    public class Pedido
    {
        public void CriarPedido(int NumeroPedido){/* */}
    }
}
```

Sendo assim esse princípio é importante para a segmentação das responsabilidades de cada classe, diminuir o acoplamento entre partes do código, facilitar a manutenção da solução, além de diminuir a [complexidade ciclomática](https://pt.wikipedia.org/wiki/Complexidade_ciclom%C3%A1tica) (possibilidades de decisões que um trecho do código pode conter).

<div id="ocp"></div>
### Letra "O": Open-Closed Principle (Princípio do Aberto-Fechado)

De acordo com esse princípio: "Um módulo deve estar aberto para extensão, porém fechado para modificação", ou seja, não devemos modificar módulos da solução e sim, apenas estender, de modo na qual não seja alterado o código-fonte. Assim sendo, é imprescindível que se busque abstrair as classes de utilização. Veja um exemplo a seguir:

```cs
namespace App;

public interface Trabalho
{
    public int ObterSalario();
}

public class Desenvolvedor : Trabalho
{
    public int ObterSalario()
    {
        return 3500;
    }
}

public class EngenheiroCivil : Trabalho
{
    public int ObterSalario()
    {
        return 5000;
    }
}
```

Dessa maneira, pode-se perceber que **trabalho** está aberto para ser estendido, mas fechado para ser modificado, já que as **interfaces** fazem que com **haja apenas o registro do contrato**. Vale ressaltar que é possível aplicar esse conceito criando **classes abstratas** com **sobrescrita de métodos**:

```cs
public abstract class Conta
{
    public virtual int ObterSaldo()
    {
        return 1000;
    }
}

public class ContaPoupanca : Conta
{
    public override int ObterSaldo()
    {
        return 1500;
    }
}

public class ContaPremium : Conta
{
    public override int ObterSaldo()
    {
        return 2000;
    }
}
```

No entanto, o que se pode perceber é que a classe abstrata exige uma implementação do método, o que não acontece com a utilização das interfaces.

<div id="lsp"></div>
### Letra "L": Liskov Substitution Principle (Princípio da Substituição de Liskov)

Em conformidade com Barbara Liskov: "As subclasses devem ser substituíveis por suas classes base", isso quer dizer que classes que se derivam de uma de origem devem ser substuíveis por sua classe base. Vamos utilizar as classes abstratas para poder demonstrar esse princípio:

```cs
public abstract class Conta
{
    public virtual int ObterSaldo()
    {
        return 1000;
    }
}

public class ContaPoupanca : Conta
{
    public override int ObterSaldo()
    {
        return 1500;
    }
}

public class ContaPremium : Conta
{
    public override int ObterSaldo()
    {
        return 2000;
    }
}
```

É interessante ressaltar que para não ferir essa prática é necessário ter em mente que as classes derivadas **não mudam de comportamento em relação a classe base**. Tendo isso em vista, pode-se realizar o LSP (Liskov Substitution Principle):

```cs
static void Main()
{
    Conta conta = new ContaPoupance();
    conta.ObterSaldo(); // 1500
}

// ou

static void Main()
{
    Conta conta = new ContaPremium();
    conta.ObterSaldo(); // 2000
}
```

Sendo assim, é possível que utilizamos comportamentos da classe base **quando for passado ao utilizador a classe derivada**. Vale ressaltar que esse estudo realizado por Bárbara Liskov sobre abstração de dados e teoria de tipos foi derivado do conceito de **Design by Contract** (Bertrand Meyer), dessa forma, podemos aplicar esse conceito utilizando **interfaces**:

```cs
public interface IConta
{
    public int ObterSaldo();
}

public class ContaPoupanca : IConta
{
    public int ObterSaldo()
    {
        return 1500;
    }
}

public class ContaPremium : IConta
{
    public int ObterSaldo()
    {
        return 2000;
    }
}

static void Main()
{
    IConta conta = new ContaPremium();
    conta.ObterSaldo(); // 2000
}
```

<div id="isp"></div>
### Letra "I": Interface Segregation Principle (Princípio da Segregação de Interfaces)

Esse princípio alega que: "Muitas interfaces específicas do utilizador são melhores que uma interface de propósito geral". Isso quer dizer que ao invés de haver uma interface que englobe toda a classe, devemos ter contratos segregados, de modo, que classes derivadas possam reutilizar o máximo possível do código. Veja a seguir um exemplo **ruim** desse princípio:

```cs
public interface IConta
{
    public int ObterSaldo();
    public int ObterDesconto();
}

public class ContaComum : IConta
{
    public int ObterSaldo()
    {
        return 1000;
    }

    public int ObterDesconto()
    {
        // Conta comum não possui desconto
    }
}

public class ContaPremium : IConta
{
    public int ObterSaldo()
    {
        return 2000;
    }

    public int ObterDesconto()
    {
        return 500;
    }
}
```

Como pode-se perceber, a classe Conta Comum implementa um contrato que ela não deveria implementar, já que pessoas com conta comum não possui desconto. Sendo assim, reparamos esse problema com a utilização do ISP (Interface Segregation Principle):

```cs
public interface IConta
{
    public int ObterSaldo();
}

public interface IContaPremium : IConta
{
    public int ObterDesconto();
}

public class ContaComum : IConta
{
    public int ObterSaldo() => 1000;
}

public class ContaPremium : IContaPremium
{
    public int ObterSaldo() => 2000;
    public int ObterDesconto() => 500;
}

```

Desse modo, a classe premium as abstrações necessárias para o funcionamento de seu comportamento e a classe conta comum não implementará abstrações inutílizáveis.

<div id="dip"></div>
### Letra "D": Dependency Inversion Principle (Princípio da Inversão de Dependência)

Esse princío tem como conceito: "Dependa de abstrações. Não dependa de concreções". Isso quer dizer que ao invés de depender das classes concretas, devemos depender de contratos criados por meio de interfaces. Vale lembrar, que apesar de [Dependency Inversion Principle](https://balta.io/blog/dependency-inversion-principle) estar muito relacionado à Inversão de Controle, os dois não são a mesma coisa, veja a seguir um mal exemplo do DIP:

```cs
public class DataConnection
{
    public void OpenConnection(int valor, SqlConnection connection)
    {
        connection.Open();
    }
}
```

Como pode-se perceber o método `Depositar Dinheiro` está dependendo da implementação do `Sql Connection`, o que fere o DIP. Para que não ocorra esse problema, podemos refatorar o código dessa maneira:

```cs
public interface IDataConnection
{
    public SqlConnection DatabaseConnection { get; set;}
}

public class DataConnection
{
    public void OpenConnection(IDataConnection dataConnection)
    {
        dataConnection.DatabaseConnection.Open();
    }
}
```