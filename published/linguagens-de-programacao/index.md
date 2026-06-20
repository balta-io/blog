Uma breve história contextual rica em fundamentos, entenda linguagens de programação antes de programar.

Sumário
 1. [Introdução](#introducao)
 2. [O que são linguagens de programação?](#oquesaolps)
    1. [O início da programação](#iniciodaprogramacao) 
    2. [A primeira linguagem de programação](#aprimeiralp)
    3. [As gerações seguintes das linguagens de programação](#geracoesseguintesdelp)
    4. [Linguagens de baixo nível](#lpbaixonivel)
    5. [Linguagens de alto nível](#lpaltonivel)
 3. [O que são Compiladores?](#oquesaocompiladores)
 4. [Linguagens Interpretadas ](#lpsinterpretadas)
 5. [Linguagens Compiladas](#lpscompiladas)
 6. [Linguagens Tipadas e não tipadas](#lpstipadasenaotipadas)
 7. [Linguagens Gerenciadas](#lpscodigogerenciado)
 8. [Quais são as principais linguagens de programação?](#principaislps)
 9. [Hello World! Em diferentes linguagens de programação](#helloworldem)
 10. [C# como sua primeira linguagem de programação](#csharpsuaprimeiralp)
 11. [Hello World! Em C#](#hellocsharp)
 12. [Referências](#ref)

*******

<div id='introducao'></div> 

## Introdução

Para entendermos linguagens de programação teremos que saber a história de sua criação, objetivos e como elas funcionam na prática. Então faremos isto. Começaremos em sua história, passaremos pelas ferramentas que trabalham os códigos das linguagens até desenvolveremos exemplos com algumas linguagens. Então para começar vamos entender o que são essas tais linguagens de programação.

<div id='oquesaolps'></div> 

## O que são linguagens de programação?

<div id='iniciodaprogramacao'></div> 

### O início da programação
Os primeiros computadores eram programados através de válvulas (futuramente transistores), A cada necessidade de executar um novo programa era preciso reprogramar o computador reposicionando seus elementos.  Esta programação manual se trata do que entendemos como o **nível 0**  ou nível de lógica digital. Os computadores desta época eram enormes e com baixíssima capacidade de processamento comparado aos computadores atuais, além disso, gastavam bastante energia.

<div id='aprimeiralp'></div> 

### A primeira linguagem de programação
O próximo "estágio" da programação é o **nível 1** ou nível de micro programação. Os computadores agora entendiam uma linguagem muito simples, esta linguagem contava com dois caracteres que indicavam o estado de um circuito (0 como baixa ou nenhuma presença de energia e 1 como presença de energia) e podendo a partir disto, realizar pequenas tarefas pré-programadas. Esta linguagem por ser a única que os computadores realmente entendem ficou conhecida como linguagem de máquina.

**Curiosidade**

Um "Olá Mundo!" como costumamos ver nos exercícios iniciais de programação ficaria assim em linguagem de máquina:
```
01101111 01101100 11100001 00100000 01101101 01110101 01101110 01100100 01101111 
```
<div id='geracoesseguintesdelp'></div> 

### As gerações seguintes das linguagens de programação
A geração seguinte de computadores contavam com interpretadores. O interpretador recebe instruções menos específicas e mais simples e como seu nome sugere, as interpreta pelo computador. Executando assim as ações equivalentes ao nível de máquina. Os interpretadores foram um grande facilitador para o surgimento de novas linguagens de programação e também dos sistemas operacionais. A partir daqui, as linguagens são identificadas como de baixo nível ou de alto nível. Quanto mais próxima do nível da máquina, mais explicitas precisam ser as instruções. E quanto mais próxima do nível de usuário, mais abstratas e simples estas instruções podem ser.

<div id='lpbaixonivel'></div> 

### Linguagens de baixo nível
Podemos entender então linguagens de programação de baixo nível como aquelas muito próximas do nível de máquina. Estas linguagens têm a característica de serem muito específicas, tornando-as mais difíceis por necessitarem de instruções detalhadas sobre o que será feito e como será feito, estas linguagens não são indicadas para quem está começando agora a ter contato com o mundo da programação.

<div id='lpaltonivel'></div> 

### Linguagens de alto nível
As linguagens de alto nível são abstratas, facilitando o desenvolvimento de quem as utiliza. Estas linguagens "rodam" a um nível mais próximo do usuário do que da máquina. Isto quer dizer que é mais fácil para o usuário entender e desenvolver com ela, mas ela precisa passar por algumas etapas antes de ser levada ao hardware e este possa ter instruções do que deve ser executado.

Existem várias linguagens de programação diversas com propósitos diferentes. Algumas linguagens precisam ter seu código traduzido para linguagem de máquina para que possam ser executados, estas linguagens são conhecidas como linguagens compiladas, entenderemos melhor mais a frente. Existem também outras linguagens de programação que precisam apenas ser lidas sem a necessidade da etapa de tradução, chamamos estas de **linguagens interpretadas**.

![Estrutura nivelada de programação](https://i.imgur.com/TIvXRp9.png "estrutura de programação")

<div id='oquesaocompiladores'></div> 

## O que são Compiladores?
Compiladores são essencialmente tradutores, eles convertem o código escrito na linguagem de origem em (geralmente) código de máquina. Existem também diversas categorias de compiladores, porém neste artigo nos atentaremos a definir o conceito dos compiladores. Estes compiladores também são responsáveis por analisar o código de origem, reconhecer a estrutura do código e interpreta-lo para então traduzi-lo.

![arquivo sendo convertido para binário](https://i.imgur.com/oy2UGXA.png "compilando")

<div id='lpsinterpretadas'></div> 

## Linguagens Interpretadas 
As linguagens de programação identificadas como interpretadas são as linguagens que são executadas em tempo de execução. Ou seja, elas são interpretadas no momento em que estão sendo executadas, não sendo necessário passar pelo processo de compilação.

Estas linguagens são mais rápidas por não precisarem ser compiladas, tem fácil distribuição. Ou seja, caso você publique um código com erro, você só precisa sobrepor este código e pronto, ele já funciona. Porém, exatamente por não passarem pelo processo de compilação, fica mais difícil identificar erros, já que o resultado é exibido logo após a execução que acontece em tempo real.

<div id='lpscompiladas'></div> 

## Linguagens Compiladas
Estas linguagens não contam com a facilidade de execução das linguagens interpretadas e são dependentes de um compilador. O processo de compilação no que lhe concerne, traz benefícios como a análise do código, possibilitando o tratamento de erros que possam conter antes que o resultado chegue ao usuário final.

<div id='lpstipadasenaotipadas'></div>

## Linguagens Tipadas e não tipadas
Se tratando de linguagens de programação, temos linguagens que contém instruções relativas aos tipos de dados, estas chamamos de linguagens tipadas ou fortemente tipadas. Estas linguagens exigem que ao criar, especifiquemos o tipo de nossa variável (inteiro, cadeia de caracteres, número de ponto flutuante, etc.). Esta característica nos dá menos liberdade, porém traz mais otimizado, sabendo o tipo de dados e memória necessária para alocar, por exemplo.

Exemplo de dados tipados em C#:
```csharp
int idade = 18; // OK
int idade = 18.11; // ERRO
int idade = "18"; //ERRo
```

As linguagens de programação não tipadas são linguagens onde podemos declarar variáveis sem definir o seu tipo.

Exemplo de dados não tipados em JavaScript
```javascript
let idade = 18; // OK
let idade = 18.11; // OK
let idade = "18"; // ok
```

O `let` aloca sempre a mesma quantidade de memória, por tratar todas as informações igualmente ele não tem otimização para os tipos de dados.

<div id='lpscodigogerenciado'></div>

## Linguagens de Código Gerenciadas
Identificamos linguagens de programação de código gerenciado como linguagens que dependem de um gerenciador (conhecidos como RunTimes). Aqui no Balta.io utilizamos a linguagem C# que utiliza como gerenciador o CLR (Common language runtime)). Estes gerenciadores lidam também com questões de segurança e alocação de memória. Permitindo que nossas interações com os recursos da máquina sejam menos frequentes e focar em desenvolvimento de outros recursos do software.

<div id='principaislps'></div>

## Quais são as principais linguagens de programação?
Segundo levantamento anual do StackOverflow, entre as linguagens mais utilizadas de 2020 estão *JavaScript, Python, java, C#* dente outras. O link para a página deste levantamento estará no final do artigo para futuras consultas. Com base nestas linguagens iremos desenvolver um pequeno programa para exibir uma mensagem na tela/console. Vamos lá?

<div id='helloworldem'></div>

## Hello World! Em diferentes linguagens de programação
O "Hello World!" é uma tradição quando estamos começando o aprendizado com uma nova linguagem de programação, falamos um pouco sobre isto no nosso artigo ***Como programar***. Para imprimir na tela este pequeno texto podemos fazer da seguinte forma:

### Javascript
O JavaScript é uma linguagem não tipada e interpretada, então para executar o nosso  "Hello World!" faremos assim:

Para executar o código em javascript basta abrir o seu navegador e abrir o console de desenvolvedor. Basta apertar F12 e selecionar a aba *Console*.

![Exemplo de navegação até o console](https://i.imgur.com/e5HCiaV.png "abrindo console")

E digitar o seguinte código:

```javascript
alert('Hello, World!');
```
Ao apertar enter, você verá que uma pequena janela popup apareceu com o texto digitado.

***Aviso!*** para os próximos exemplos usaremos a ferramenta online `IdeaOne`.

Para exibir nosso texto basta selecionar no rodapé do editor do *IdeaOne* a linguagem de programação desejada e escrever o código equivalente:

### Python
O Python é uma linguagem interpretada, de tipagem forte e dinâmica. Isto quer dizer que o tipo da variável é atribuído durante o processo de execução. No lugar da compilação, o Python passa por um processo de transpilação semelhante ao que acontece com o C# e o Java porém não pode ser descrito como uma compilação já que o resultado é um código intermediário que é executado por um interpretador. 

```Python
print("Hello, World!")
```

### Java
O Java é uma linguagem de programação compilada e, ao mesmo tempo, interpretada. O seu compilador transforma o código de origem em uma linguagem intermediária, que no caso do java é o código binário que é executado por uma máquina virtual, e por ser executado pela máquina virtual e não por um interpretador é que chamamos o Java de linguagem compilada. 

```Java
import java.io.*;

class Exemplo
{
	public static void main (String[] args)
	{
		System.out.print("Hello, World!");
	}
}
```

**Ao clicar em `Run` o nosso código será compilado e executado. Veremos a saída na página que carregará automaticamente.**

## O que é o C#
O C# é uma linguagem de programação orientada a objetos, compilada e gerenciada. Ela também é fortemente tipada e faz parte do ecossistema da Microsoft. Ela pode ser utilizada para desenvolvimento com propósitos gerais.

Durante o processo de compilação o C# é compilado para uma linguagem intermediária assim como o Java, o código gerado é executado pelo .NET. E também por isto o C# é uma linguagem de compilação.

Com C# você pode desenvolver aplicações web, desktop, animações, e também mobile. Além de ser uma linguagem completa, ela também é uma linguagem madura, com documentação robusta e uma comunidade grande. Em relação ao mercado, esta linguagem tem a característica de ser utilizada por grandes empresas, sendo assim uma linguagem com um mercado muito bom.

<div id='csharpsuaprimeiralp'></div>

## C# como sua primeira linguagem de programação
Antes de tudo precisamos entender que C# não é a linguagem mais fácil. Existem linguagens mais fáceis assim como outras mais difíceis (como C, por exemplo). Porém, por ser uma linguagem orientada a objetos, você aprende sobre a *programação orientada a objetos* e desenvolve uma base sólida desde o início.

Aprender o básico com o C# prepara para o aprendizado de futuras linguagens, te dando uma gama maior de conhecimento e evitando que no futuro você precise voltar para aprender estes fundamentos.

<div id='hellocsharp'></div>

## Hello World em C#
O C# é uma linguagem de programação fortemente tipada e compilada. A estrutura do código abaixo. Ele é composto por um `NameSpace`, uma `Classe`, um `Método` e o comando necessário. Seguindo os paradigmas da orientação a objetos.

```csharp
namespace Artigo
{
    public class Exemplo
    {
        public static void Main()
        {
            System.Console.WriteLine("Hello, World!");
        }
    }
}
```
**Execute o código acima utilizando também a ferramenta `IdeaOne`.

## [Curso Fundamentos do C#]()

<div id='ref'></div> 

## Referências
[Enquete 2020 StackOverflow](https://insights.stackoverflow.com/survey/2020#technology-programming-scripting-and-markup-languages-all-respondents) | [IdeaOne](https://www.ideone.com/)
