Esqueceu o Console.WriteLine e mandou o código para produção? Não cometa mais estes erros, vem entender como funciona o processo que remove todo lixo do seu código.

## O que vai para produção?
Quando compilamos nossa aplicação, quase todo código escrito é convertido em IL e posteriormente em linguagem de máquina. Assim temos nossa DLL, pronta para ser consumida pelo SO que hospeda nossa aplicação.

Neste processo, os comentários por exemplo, são removidos, já que não faz sentido algum para o resultado final da aplicação, um comentário. Mas este não é o motivo pelos qual não devemos ter comentários irrelevantes no código.

Por diversas vezes encontramos código com métodos antigos, sujeira e muitas linhas de código disperdiçadas. Tudo isto aumenta o tamanho do arquivo (Texto -- .cs) e do binário final da nossa aplicação.

Pior ainda é o cenário onde temos código específico para Debug, sejam mensagens ou mesmo execução de código. Sim, tem cenários que precisamos executar determinada ação apenas estando em DEBUG ou em PROD.

## Console.WriteLine não!
Ok ok, eu sei que você eventualmente coloca um `Console.WriteLine("passei aqui")`, eu também faço isto as vezes, porém tenho duas notícias para te dar, uma boa e uma ruim.

A ruim é que este código é compilado no resultado final da sua aplicação. Isto ocorre pois o `Console.WriteLine` não faz uso do `ConditionalAttribute` que possívelmente o inibiria do código final.

Então, sempre que precisar visualizar uma ou muitas informações e por algum motivo os Break Points/Debug não te atenderem, evite utilizar o `Console.WriteLine`. Caso esqueça ele no código, ele irá para produção.

A boa notícia é que existe uma outra opção, melhor, que faz o mesmo trabalho do `Console.WriteLine` neste cenário e que não vai para produção mesmo que você esqueça ele no código.

## Debug.WriteLine
Uma forma muito mais elegante de **printar** uma mensagem no console sem correr o risco da mesma ir para produção é utilizar o `Debug.WriteLine("Mensagem");`. Mesmo que você esqueça esta linha de código perdida, ela não vai para produção.

Isto ocorre pois o método `WriteLine` do `Debug` é marcado com o atributo `ConditionalAttribute`, que indica ao compilador para remover este trecho de código do resultado final da aplicação.

## ConditionalAttribute
Existe um atributo no .NET chamado `ConditionalAttribute` que permite **remover** um trecho de código durante o processo de build, previnindo que o mesmo vá para produção.

Na verdade o `ConditionalAttribute` trabalha com símbolos do compilador e podemos criar várias condições que atendam este símbolos, como mostrado abaixo.

```csharp
#define CONDITION1
#define CONDITION2

using System;
...
```
Com as condições definidas, podemos utilizar o `ConditionalAttribute` para indicar ao compiladore que uma chamada de método ou atributo deve ser ignorado, a menos que um símbolo de compilação condicional especificado seja definido.

```csharp
[Conditional("CONDITION1")]
public static void Method1(int x)
{
    Console.WriteLine("CONDITION1 is defined");
}

[Conditional("CONDITION1"), Conditional("CONDITION2")]
public static void Method2()
{
    Console.WriteLine("CONDITION1 or CONDITION2 is defined");
}
```
Como resultado neste caso, teríamos os métodos na aplicação final caso as condições fossem verdadeiras apenas.

Da mesma forma, podemos utilizar o `#undef CONDITIONAL1` para remover esta condição e compilar a aplicação sem os métodos informados, já que as condições não atendem mais.

## IF
Além do atributo `Conditional` temos a possibilidade de utilizar `IF`, para atender trechos maiores de código por exemplo.

```csharp
public void Method2()
{
#if CONDITION1
   ...
#endif
}
```

## Variável de ambiente DEBUG
Agora vem a parte mais incrível, to


https://stackoverflow.com/questions/3788605/if-debug-vs-conditionaldebug


## Fonte
 * https://docs.microsoft.com/en-us/dotnet/api/system.diagnostics.conditionalattribute?redirectedfrom=MSDN&view=net-6.0