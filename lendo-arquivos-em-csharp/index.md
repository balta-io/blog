Neste artigo vamos aprender algumas formas que temos para ler arquivos em C# com o menor esforço possível.

## Lendo arquivos em C#
Ler arquivos em C# não é uma tarefa difícil e a forma mais simples e direta de fazer isto é utilizando o método `ReadAllText` da classe estática `File`.

No exemplo abaixo temos um programa completo que faz a leitura de um arquivo e exibe seu conteúdo na tela utilizando o `Console.WriteLine`.


```csharp
// Lê um arquivo inteiro
var data = File.ReadAllText("C:\\text-file.txt");  
Console.WriteLine(data);
```

## Lendo arquivos em C# linha a linha
Como notamos no método anterior, não iteramos sobre o arquivo, apenas pegamos seu conteúdo e exibimos na tela.

Para iterar pelas linhas do arquivo, temos um método chamado `ReadAllLines` que retorna um **Array** de `string`, que pode ser iterado futuramente.

No exemplo abaixo temos um programa completo que faz a leitura de um arquivo e exibe seu conteúdo na tela utilizando o `Console.WriteLine`.

```csharp
// Lê um arquivo linha a linha
var lines = File.ReadAllLines("C:\\text-file.txt");
foreach (var line in lines)
    Console.WriteLine(line);
```

## StreamReader
Uma outra forma muito solicitada é a leitura de arquivos linha a linha. Na verdade se um arquivo é muito grande, ler ele por completo pode acarretar em estouro de memória.

Para isto, uma ótima solução é utilizar o `StreamReader` em conjunto ao `using` que fará a leitura do arquivo em blocos (Streaming).

Toda vez que fazemos um **Stream**, ocorre a possibilidade do arquivo ficar aberto por mais tempo que o necessário em memória, por isto é quase que obrigatório o uso do `Dispose` que neste caso já é feito pelo `using`.

No exemplo abaixo temos um programa completo que faz a leitura de um arquivo e exibe seu conteúdo na tela utilizando o `Console.WriteLine`.

```csharp
using var file = new StreamReader("C:\\text-file.txt");
string? line;

while ((line = file.ReadLine()) != null)
    Console.WriteLine(line);

file.Close();
```

## Conclusão
C# não é difícil, muitas vezes nós complicamos muito as coisas que podem (E devem) ser simples! Ler arquivos é uma tarefa comum em todas as linguagens e o C# torna isto simples e direto ao ponto!
