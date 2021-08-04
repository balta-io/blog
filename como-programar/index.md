## Visão geral
Há um grande número de pessoas que ingressam na área de TI e ao serem apresentadas para a programação acabam regressando e até mesmo desistindo da área por deduzirem que é complexo demais e não darão conta do trabalho.

Auxiliar estas pessoas no processo de ingressão é muito importante. Podemos ver constantemente nas mídias de informação que o mercado está com uma alta demanda de profissionais de TI. Isto acontece principalmente por falta de mão de obra qualificada que é um dos efeitos colaterais da situação acima descrita.

Então visando combater esta evasão da área de programação/desenvolvimento podemos começar melhorando a forma que a programação é apresentada. Vamos lá?

## Introdução
Pense que é possível se comunicar com computadores, sejam eles estações de trabalho, smartphones, dispositivos domésticos inteligêntes ou até mesmo máquinas industriais. Imaginou? Beleza! Agora pense no que seria possível e como seria mais fácil o dia a dia passando instruções para estes computadores e vê-los executando tarefas mais precisamente que nós mesmos e com resultados rápidos além de nos retornarem relatórios completos. Não é dificil presumir que isso já é possível, na maioria dos casos não de forma literal como uma conversa ou gesto mas sim por instruções escritas.

Passar para o computador, informações e instruções sobre como agir e o que fazer é a definição simples de programação, sabendo disso podemos ir para o próximo passo que é entender o ato de programar. Para isto vamos antes definir o processo lógico ou lógica de programação.

## O que é lógica de programação?
Bem, **lógica** assim como muitas outras palavras, tem seu significado derivado de uma linguagem antiga que, neste caso, é o grego. Derivada da palavra *logos* que significa dentre outros, argumentação. A lógica é uma forma de estruturar "pensamentos" aplicando algumas regras. Também é descrita como "A ciência que estuda princípios e métodos de inferência, tendo o objetivo principal de determinar em que condições certas coisas se seguem (são consequências), ou não, de outras", (Mortari, 2001). 

Já a **programação** é a junção de duas palavras: programar e ação. Enquanto programar significa passar instruções, planejar. Ação signifia praticar. O termo *lógica de programação* pode ser descrito como raciocínio lógico voltado para computadores e é ele (raciocínio lógico) que é desenvolvido quando estudamos e aplicação a programação. Então com isto em mente podemos partir para a próxima questão.

## Como programar?
Agora que temos alguma definição sobre os termos, podemos vir para a questão principal. Como programar? Para programar é importante primeiro que estude um pouco sobre lógica de programação/algoritmos, compilação, interpretação e claro, tipagem de dados. Esta base vai te preparar para utilizar as linguagens de programação para construir seus primeiros programas.

## O que são linguagens de programação?
Linguagens de programação é a escrita formal usada para passar comandos, instruções e regras para um computador. Um conjunto desses items pode ser descrito como um programa. Programas podem ser simples como exibir um texto em uma tela mas pode ser infinitamente complexo também, limitado apenas ao propósito para o qual foi escrito.

## Mas qual linguagem de programação usar?
Essa questão é uma das mais frequêntes entre aqueles que estão começando a programar ou estudando a possibilidade. A **linguagem de programação ideal** pode ser definida com base no propósito do software a ser desenvolvido, podendo ser mais performática, precisa ou destacar especificações.

Para te dar um exemplo, vamos utilizar a linguagem C# (C sharp) para escrever um texto de saída.

## Vamos lá, mão na massa!

Bem, existem alguns sites onde você pode escrever abertamente códigos em uma linguagem e analisar os resultados. Um destes sites é o DotNetFiddle que compila e exibe através do navegador o resultado do seu código.

Vamos seguir o ritual do *Hello World!* é dito que ao iniciar o estudo de uma nova linguagem de programação, devemos antes de tudo aprender a escrever o *Hello World!* com a linguagem em questão ou não aprenderemos corretamente. A brincadeira tem o intuito de passar que, primeiro precisamos aprender o básico e depois passar para passos mais complexos. Assim teremos uma base sólida que nos permitirá chegar mais longe.

Sem mais delongas, vamos acessar o [*DotNetFiddle*](https://dotnetfiddle.net/) e escrever o nosso texto. Na página ele já nos apresenta o código:

```
using System;
					
public class Program
{
	public static void Main()
	{
		Console.WriteLine("Hello World");
	}
}
```

Este é o código necessário para escrever o nosso *Hello World!* Vamos entender o que está acontecendo?

```
using System;
```

1 - Estamos passando para o compilador que ele deve trazer a biblioteca *System* para o contexto. Nela existem alguns recursos que utilizaremos mais a frente.

```
public class Program
{

}
```

2 - Aqui criamos a estrutura do nosso programa. `public` é a forma que usamos para indicar que nossa classe pode ser acessada pelo contexto, `class` ou classe é o conjunto de propriedades para definir um objetivo e `Program` foi o nome escolhido para identificarmos esta classe.

```
public static void Main()
{

}
```

3 - Agora dentro da classe inserimos o trecho acima para indicar o que chamamos de métodos. Os métodos são ações para indicar quais comportamentos nossa classe terá. O nome do nosso método acima foi definido como `Main`.

```
Console.WriteLine("Hello World!");
```

4 - Por fim, chamamos o console e em seguida chamamos o método `WriteLine`
para indicar o texto a ser escrito que está dentro das aspas. Ao executar teremos a saída:

```
Hello World!
```

Agora que tal aprender sobre os fundamentos e começar a construir outros programas? Você pode seguir aqui mesmo na plataforma, visite o [manual do aluno](https://balta.io/guia). Ah! Temos também [16 cursos gratuitos](https://balta.io/comece-de-graca) para você começar a estudar programação e se tornar um desenvolvedor disputado no mercado.

> **Melhorias no C#** - Desde a versão 9.0 do C#, podemos reduzir toda a escrita vista acima para apenas `System.Console.WriteLine("Hello world!")`. Isto funcionaria da mesma forma, com apenas uma linha, exibindo o conteúdo ***Hello world*** na tela, porém é importante entender os conceitos mostrados acima pois no mundo real eles são amplamente utilizados.

## Referências bibliográficas
MORTARI, Cesar A. **Introdução à lógica**, ed. UNESP: 2001
