Um dos recursos mais importantes da orientação a objetos é a possibilidade de sobrescrever ou sobrecarregar métodos e propriedades. Neste artigo vamos ver na prática como isto acontece.

## Sobrescrita

O ato de sobrescrever um método ou propriedade significa dar uma nova forma ao mesmo, uma nova versão. Vimos isto [no artigo anterior](https://balta.io/blog/orientacao-a-objetos) onde falamos sobre os pilares da orientação a objetos.

A sobrescrita de no C# é dada pelo uso em conjunto do modificador **virtual** e **override**. Sempre que marcamos uma propriedade ou método como **virtual**, significa que o mesmo pode ser sobrescrito.

A execução desta sobrescrita então é dada pela palavra reservada **override**, indicada antes do retorno da propriedade ou método.

Para contextualizar melhor, vamos tomar como base um objeto que possui uma finalidade.

```csharp
public class Garrafa
{
	public void Finalidade()
	{
		Console.WriteLine("Garrafa genérica");
	}
}
```

Embora este objeto esteja bem materializado, uma das coisas legais da orientação a objetos é poder criar novos objetos a partir de um objeto base (pai). Este conceito é chamado de herança, como vimos no artigo anterior.

Queremos então criar novos modelos de garrafa, que tem características similares, mas finalidades diferentes, como por exemplo uma garrafa térmica.

Podemos então definir o método finalidade como **virtual** para que o mesmo possa ser sobrescrito posteriormente.

```csharp
public class Garrafa
{
	public virtual void Finalidade()
	{
		Console.WriteLine("Garrafa genérica");
	}
}
```

Desta forma, dada uma nova garrafa, com uma finalidade diferente, podemos ter o mesmo nome no método, porém realizando uma ação completamente diferente.

Para executar então a sobrescrita do método _Finalidade_ que está no objeto pai, utiizamos o **override**.

```csharp
public class GarrafaTermica : Garrafa
{
	public override void Finalidade()
	{
		Console.WriteLine("Manter a temperatura");
	}
}
```

Ainda podemos, caso necessário, chamar o método _Finalidade_ do objeto pai utilizando a palavra reservada **base**.

```csharp
public class GarrafaTermica : Garrafa
{
	public override void Finalidade()
	{
		base.Finalidade();
		Console.WriteLine("Manter a temperatura");
	}
}
```

Neste caso o output do programa seria como mostrado abaixo, afinal, chamamos tanto o método _Finalidade_ do pai quanto do filho.

```
Garrafa genérica
Manter a temperatura
```

## Sobrecarga

Sobrecarregar métodos significa ter vários métodos com nomes iguais mais assinaturas diferentes.

A assinatura de um método no C# é dada pela junção do seu nome e seus parâmetros de entrada.

Tomando como base o método abaixo, sua assinatura seria <code>Encher(int mililitros)</code>, o que nos permitiria ter outros métodos com o nome **Encher** desde que os mesmos contenham parâmetros diferentes.

```csharp
public void Encher(int mililitros) {
    ...
}
```

No caso, os métodos abaixo **NÃO SERIAM** permitidos, resulando em um erro de compilação.

```csharp
public void Encher(int mililitros) {
    ...
}

// Não permitido
public void Encher(int litros) {
    ...
}

// Não permitido
public double Encher(int ml) {
    ...
}
```

Mesmo mudando o retorno do método, ainda assim teríamos um erro de compilação, pelo fato dele (retorno do método) não fazer parte da assinatura.

Agora temos alguns exemplos que funcionariam, pois trocamos o tipo de entrada (Parâmetros) do método. Mesmo os parâmetros tendo nomes iguais, seus tipos são diferentes, o que permite o método ser sobrecarregado.

```csharp
public void Encher(int mililitros) {
    ...
}

public void Encher(double litros) {
    ...
}

public double Encher(float mililitros) {
    ..
}
```

Um outro exemplo comum é ter diferentes métodos, com mesmo nome, mas acumulando parâmetros, como é comum vermos em várias classes do próprio .NET.

```csharp
public void Data(int dia) {
    ...
}

public void Data(int dia, int mes) {
    ...
}

public void Data(int dia, int mes, int ano) {
    ...
}
```

Por fim, não se esqueça que podemos reduzir bastante código e a quantidade de métodos utilizando os parâmetros opcionais no C#.

```csharp
public void Data(int dia, int mes = 1, int ano = 1990) {
    ...
}
```

## Cursos

Não deixe de conhecer nosso material sobre C#, SOLID, Clean Code e muita orientação a objetos. Nossos cursos reúnem teoria e prática de forma direta e objetiva. Vou deixar a lista relacionada a orientação a objetos aqui, já na ordem de estudo.

### Fundamentos do C

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
