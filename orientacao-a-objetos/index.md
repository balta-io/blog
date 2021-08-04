Orientação a objetos é um paradigma aplicado na programação que consiste na interação entre diversas unidades chamadas de objetos.

Depois desta definição formal, você deve estar se perguntando onde e quando podemos utilizar orientação a objetos? É normal este questionamento quando ficamos apenas na abstração.

Porém, se tem algo que é a base de todo programador, é a orientação a objetos, um paradigma criado há anos atrás e usado até hoje, nas principais linguagens e tecnologias do mercado.

Usamos a orientação a objetos para nos basear na vida real e resolver problemas de software, ou pelo menos tentamos. Ela acaba sendo uma base inclusive para outros paradigmas.

orientação a objetos é algo atemporal e não está ligado a uma linguagem, o que significa que você pode investir seu tempo e aprender pois não vai mudar.

## Pilares
A OOP (Object Oriented Programming -- Programação Orientada a Objetos) pode ser definida por quatro pilares principais, sendo eles **herança**, **encapsulamento**, **abstração** e **polimorfismo**.

### Encapsulamento

Na definição, dissemos que orientação a objetos é uma forma de interação entre unidades chamadas de objetos. Pois bem, este é o conceito por trás do seu primeiro pilar, o encapsulamento.

É comum ouvirmos o termo "encapsular" no dia-a-dia. "Encapsula isto em uma classe", mas o que de fato da significado a este termo?

Imagine o seguinte programa abaixo:

```csharp
int idade = 34;

...

public void VerificaIdade() {
    if(idade > 18) ...
}

public void AtribuiIdade(int idade) {
    idade = idade;
}

```

Neste pequeno cenário, temos uma variável representando uma idade e posteriormente duas funções que leem/escrevem nela. A medida que este programa cresce, as coisas podem se complicar.

Não sabemos onde a variável é usada ou modificada, não sabemos sequer quais funções são pertinentes a idade ou mesmo a que se refere esta idade.

Aplicando o conceito de encapsulamento, fazemos o agrupamento das coisas que fazem sentido estarem juntas, para podermos organizar e reutilizar melhor nosso código.

```csharp
public class Aluno {
    public int Idade { get; set; }

    public Aluno(int idade) {
        Idade = idade;
    }

    public void VerificaIdade() {
        if(idade > 18) ...
    }
}
```

Refatorando nosso código, temos então uma classe **Aluno**, agrupando as variáveis e funções que tinhamos anteriormente.

As variáveis passam então a ser o que chamamos de **propriedades** e as funções passam a ser o que chamamos de **métodos**. Esta composição dá origem ao nosso objeto e aplica o conceito de encapsulamento.

> As classes na orientação a objetos funcionam como um molde para os objetos. Os objetos são criados a partir de uma classe e muitos deles podem ser feitos da mesma classe.

### Herança

Assim como na vida real somos um acúmulo de características de nossos antepassados, na orientação a objetos isto também é possível. Na verdade isto é até uma boa prática.

O conceito de **herdar** é literalmente ser uma *cópia* de outra classe com algumas características adicionais.

Vamos tomar como base uma classe de pagamento simples, que contém a data de vencimento deste pagamento.

```csharp
public class Pagamento {
    public DateTime Vencimento { get; set; }
}

```

O pagamento por sua vez pode ser via Boleto, Cartão de Crédito, PayPal, Stripe, MoiP e futuros outros métodos de pagamento poderão existir.

Ao invés de tratar todas estas possibilidades dentro da mesma classe, tornando ela grande e complexa de ser gerenciada, podemos aplicar o conceito de herança e criar filhos desta classe.

```csharp
public class PagamentoBoleto : Pagamento {
    public string CodigoBarras { get; set; }
}
```

Neste caso, temos uma nova classe, chamada **PagamentoBoleto** que herda as características da sua classe pai **Pagamento**. Desta forma, na classe **PagamentoBoleto** temos tanto a data de vencimento quanto o código de barras.

### Abstração
Na orientação a objetos, o conceito de abtração ou abstrair, significa esconder os detalhes de uma implementação, ou seja, quanto menos souberem sobre nossas classes, mais fácil de consumí-las será.

```csharp
public class PagamentoBoleto : Pagamento {
    public int digitoVerificador = 0;

    public string CodigoBarras { get; set; }

    public int CalcularDigitoVerificador() {
        ...
    }
}
```

No evemplo anterior temos o cálculo do dígito verificador. Isto é exposto tanto pela propriedade **digitoVerificador** quanto pelo método **CalcularDigitoVerificador** neste classe.

A pergunta aqui é: É realmente necessário expor estas propriedades e métodos?

Tanto a propriedade quanto o método são usados exclusivamente no boleto, e caso precisem ser alterados, se consumidos externamente, causariam uma refatoração em vários outros componentes.

Desta forma, escondemos tudo que não é necessário o mundo externo ao nosso objeto saber, assim ficamos mais confortáveis com as mudanças, pois elas afetam somente o nosso objeto.

```csharp
public class PagamentoBoleto : Pagamento {
    private int digitoVerificador = 0;

    public string CodigoBarras { get; set; }

    private int CalcularDigitoVerificador() {
        ...
    }
}
```

### Polimorfismo

Poli significa muitos e morfo significa formas, então temos a possibilidade de um objeto assumir diversas formas diferentes na orientação a objetos.

Novamente no caso dos pagamentos, embora cada pagamento seja pertinente a uma operadora distinta, ainda temos comportamentos que serão padrão em todos eles.

Vamos tomar como base o método **PodeSerPago** que nos informa se um pagamento está vencido ou não, se pode ser pago ou não.

```csharp
public class Pagamento {
    public bool PodeSerPago() {
        ...
    }
}
```

Na classe pai, este comportamento é padrão, ou seja, passado a data de vencimento, caso o pagamento ainda não tenha sido realizado, ele está vencido e não pode ser pago.

Para os pagamentos via Cartão de Crédito e outras formas digitais, não haveria problemas, afinal, os pagamentos neste formato são cobrados em qualquer data e hora.

Porém, os pagamentos via boleto tem um formato diferente onde caso a data de vencimento seja em um fim de semana ou feriado, o mesmo poderá ser pago no próximo dia útil.

Não queremos reescrever a mesma regra de pagamento duas vezes, queremos que ela seja padrão para todos os pagamentos, porém, queremos poder sobrescreve-la caso haja necessidade.

```csharp
public class Pagamento {
    public virtual bool PodeSerPago() {
        ...
    }
}

public class PagamentoBoleto : Pagamento {
    public override bool PodeSerPago() {
        ...
    }
}
```

Nesta implementação, temos uma regra específica para pagamentos via boleto, que não afeta a regra base para quaisquer outros tipos de pagamento.

## Livros e referências

Em termos de livro, como comentamos no começo deste artigo, a orientação a objetos é atemporal e os melhores livros sobre o assunto são antigos.

 * [Design Patterns: Elements of Reusable Object-Oriented Software](https://www.amazon.com.br/Design-Patterns-Object-Oriented-Addison-Wesley-Professional-ebook/dp/B000SEIBB8)
 * [Clean Code: A Handbook of Agile Software Craftsmanship](https://www.amazon.com.br/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882)

## Cursos

Não deixe de conhecer nosso material sobre C#, SOLID, Clean Code e muita orientação a objetos. Nossos cursos reúnem teoria e prática de forma direta e objetiva. Vou deixar a lista relacionada a orientação a objetos aqui, já na ordem de estudo.

### Fundamentos do C# 
Este curso reune toda base necessária para evoluir na orientação a objetos com a linguagem C#. São mais de 140 aulas em 14 módulos e mais de 12 horas de conteúdo.

Com certeza é um conteúdo massivo, porém completo e necessário para você ter uma base sólida em sua carreira.
[https://balta.io/cursos/fundamentos-csharp](https://balta.io/cursos/fundamentos-csharp)

### Modelando domínios ricos
Neste curso nós colocamos a mão na massa do começo ao fim resolvendo um problema completo somente com orientação a objetos e SOLID.

Este é um curso essencial e um divisor de águas na carreira de muitos desenvolvedores.
[https://balta.io/cursos/modelando-dominios-ricos](https://balta.io/cursos/modelando-dominios-ricos)

### Refatorando para testes de unidade
Este é uma continuação do curso anterior, porém mostrando na prática como podemos manter a qualidade e manutenabilidade do software através de testes de unidade.

Este curso é o que vai te colocar um passo a frente de muitas pessoas no quesito qualidade e organização.
[https://balta.io/cursos/refatorando-para-testes-de-unidade](https://balta.io/cursos/refatorando-para-testes-de-unidade)

### Carreira Backend Developer .NET
Caso queira ver todos os cursos anteriores e ainda aprender mais sobre APIs, padronização, documentação, performance e Cloud, temos uma carreira completa.

Esta carreira é extensa mas te dará toda base para desenvolvimento de APIs robustas e de alta performance.
[https://balta.io/carreiras/backend-developer-dotnet](https://balta.io/carreiras/backend-developer-dotnet)

## Vídeo

Também fizemos uma versão em vídeo com exemplos destes códigos em TypeScript, JavaScript e Dart. Vale muito a pena conferir!

[![A base do programador: Orientação a objetos em C#, JavaScript, TypeScript e Dart](https://img.youtube.com/vi/o5g986Wpd18/0.jpg)](https://www.youtube.com/watch?v=o5g986Wpd18 "A base do programador: Orientação a objetos em C#, JavaScript, TypeScript e Dart")