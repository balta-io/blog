Junto ao C# 10 chegaram dois novos operadores, AS e IS para complementar a família e salvar algumas linhas de código dos nossos sistemas.

## Operador IS
O `is` é um operador de comparação, que podemos utilizar para comparar tipos tanto primitivos quanto complexos, eliminando a necessidade do `typeof`, até então presente como única opção.

```csharp
var someText = "This is a string";

// Retorna um bool
var result = someText.GetType() == typeof(string);
```

No exemplo acima, utilizamos o `GetType` para obter o tipo de uma variável e em seguida comparamos ela com algum outro tipo utilizando o `typeof`. Como retorno temos um `bool` informando se a variável é ou não do tipo comparado.

Embora seja uma expressão simples e fácil de entender, podemos substituí-la pelo `is`, tornando o código ainda mais simples e elegante, como podemos ver abaixo.

```csharp
var result = someText is string;
```
### Comparando tipos complexos
O `is` funciona para tipos complexos também, como `class` ou `record`, e para exemplificar vamos criar alguns registros que identificam documentos, como CPF, CNPJ e Passaporte.

```csharp
public record Documento { ... }
public record CPF : Document { ... }
public record CNPJ : Document { ... }
```

Como podemos notar, os documentos CPF, CNPJ e Passaporte, derivam de uma classe base chamada pagamento. Se ficou confuso esta parte, confere nosso [**CURSO DE ORIENTAÇÃO A OBJETOS**](https://balta.io/cursos/fundamentos-orientacao-objetos) com C# que tem tudo nos detalhes lá.

```csharp
var p = new Passaporte();
if (p is Documento)
    Console.WriteLine("P é um documento");
```

Da mesma forma, podemos aplicar uma comparação aos objetos utilizando o comparador `is` como exemplificado na linha `p is Documento`. O código fica bem mais legível.

### Utilizando IS na comparação de nulos
Talvez um dos cenários que vejo mais uso para o `is` seja na comparação de objetos com `null`, tanto para verificar se é nulo ou não é nulo. Vamos conferir a mudança olhando um código sem uso do `is`.

```csharp
if(student == null)
    Console.WriteLine("Nulo");

if(student != null)
    Console.WriteLine("Não Nulo");
```
Este mesmo código funcionaria apenas aplicando o `is` ou `is not` na comparação, algo sutil a nível de código mas que traz uma legibilidade infinitamente maior.

```csharp
if(student is null)
    Console.WriteLine("Nulo");

if(student is not null)
    Console.WriteLine("Não Nulo");
```

## Operador AS
Outro operador útil que pode melhorar bastante a legibilidade do código é o `as`, para fazer conversões explícitas de objetos. É comum vermos o uso do tipo em frente a variável, utilizando parênteses para fazer a conversão, como mostrado abaixo.

```csharp
var documento = new Documento();
var cpf = (CPF)documento;
```

Porém, podemos fazer esta conversão de uma forma bem mais elegante utilizando `as` ao invés de `(CPF)` como mostrado no exemplo anterior.

```csharp
var documento = new Documento();
var cpf = documento as CPF;
```

## Conclusão
Com dois operadores novos e simples, o `is` e `as` podemos melhorar muito o nosso código, entregando uma melhor legibilidade e facilitando o entendimento.