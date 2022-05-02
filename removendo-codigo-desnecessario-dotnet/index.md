Neste artigo vamos conferir os recursos do C#/.NET que nos permite desabilitar ou omitir parte código em produção.

## Quen nunca...
Quem nunca esqueceu um `Console.WriteLine` no código e enviou tudo para produção? Pois é, isto é uma prática comum que fazemos mas que pode ter consequências maiores.

Já vi casos onde havia `Console.WriteLine(ConnectionString)`, imprimindo usuário e senha de acesso ao banco de dados. Ruim, não é?

### Debug.WriteLine
Uma abordagem bem mais sutil que você pode começar a adotar é o `Debug.WriteLine`, desta forma, a não ser que seu código seja compilado como `DEBUG`, você não terá acesso a esta linha em produção.

Tudo contido no `Debug` só será realizado no modo **Debug**, previnindo automaticamente qualquer exposição de informações em produção.

### A melhor maneira
Não printar este tipo de informação! Esta é a melhor maneira de previnir qualquer tipo de vazamento de informação da sua aplicação.

Se você precisa obter uma configuração específica ou inspecionar algum valor em produção, utilize o **Remote Debugger** para isto, mas evite deixar logs desnecessários.

## Conditional Debug
Além do `Debug.WriteLine` e todos outros métodos contidos no `Debug`, podemos decorar nossos métodos com o atributo `Conditional` e especificar que o mesmo só estará disponível durante o modo **DEBUG**.

```csharp
[System.Diagnostics.Conditional("DEBUG")]
public void MeuMetodo(string parametro)
{ ... }
```

Este trecho de código só poderá ser invocado durante o modo **DEBUG**, inclusive a própria IDE te alertará sobre isto, colocando um destaque/warning sempre que fizer uso de um método como este.

O `ConditionalAttribute` trabalha com símbolos do compilador e podemos criar várias condições que atendam este símbolos, como mostrado abaixo.

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
Como resultado neste caso, teríamos os métodos disponíveis apenas se as condições fossem verdadeiras.

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

Um dos cenários mais comuns é usar a condicional `DEBUG`, contina no próprio ambiente do .NET para esta tomada de decisão.

```csharp
#if DEBUG
    public void MeuMetodo(string parametro)
    { ... }
#endif
```

## O que vai para produção?
Mas se ambos teoricamente fazem a mesma coisa, qual a real diferença? Quando utilizar um ou outro. Bem, na verdade existe uma grande diferença.

No caso do `#if DEBUG` o código nem chegará ao IL, ele simplesmente será eliminado antes, como se não existisse.

No caso do `Conditional Attribute` o código chegará no IL mas será omitido a menos que a condição seja satisfatória.

Em resumo, se você quer de fato **descartar** este código durante a compilação, o `#if DEBUG` é a melhor opção, vai economizar alguns bytes.

Porém, se você precisa deste código na compilação final da sua aplicação, se eventualmente necessitará trocar do modo **RELEASE** para **DEBUG** para qualquer tipo de testes, o `Conditional Attribute` é a melhor opção.


## Fonte
 * https://docs.microsoft.com/en-us/dotnet/api/system.diagnostics.conditionalattribute?redirectedfrom=MSDN&view=net-6.0