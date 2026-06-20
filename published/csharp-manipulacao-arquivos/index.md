Neste artigo vamos conferir como podemos criar, copiar, substituir, ler e escrever arquivos em C# de uma forma simples e direta!

## System.File
O .NET é um conjunto de bibliotecas (Framework) que nos auxilia na criação de aplicações, e como você deve imaginar, manipular arquivos é algo comum em boa parte dos sistemas.

Desta forma, o .NET traz o **System.File**, uma lib completa para manipulação de arquivos em qualquer plataforma que suporte o .NET.

> **IMPORTANTE:** Existem alguns métodos como o `File.Encrypt` que roda exclusivamente no Windows. Neste caso você será alertado pelo compilador e/ou IDE sempre que utilizar algum método específico de uma plataforma.

Para facilitar todos os exemplos a seguir, vamos começar criando uma constante que define o caminho do nosso arquivo. Como estou no Windows neste momento, vou usar o seguinte caminho:

```csharp
const string filePath = @"C:\dev\meuarquivo.txt";
```

## Tratamento de erros
Outro ponto importante é que a manipulação de arquivos é sensível, então recomenda-se que sempre utiliza-as dentro de um escopo `try/catch`, conforme abaixo:

```csharp
try {
    // Manipulação de arquivo
} catch (Exception ex) {
    // Capturar exceção
}
```

### Criar um arquivo
O primeiro método que vamos aprender é o `File.Create` que cria ou sobrescreve um arquivo no disco. Caso o arquivo não exista, o mesmo será criado, se já existir, será sobrescrito.

É importante salientar que o caminho (Pastas) deve existir, caso contrário, você receberá um `DirectoryNotFoundException`. Ele não cria a pasta automaticamente. Em adicional, caso não haja permissão para escrita do arquivo no diretório informado, você receberá a exceção `UnauthorizedAccessException`.

```csharp
File.Create(filePath);
```

### Adicionar conteúdo a um arquivo
Para adicionar conteúdo (Escrever) em um arquivo, podemos usar o `AppendText` ou `CreateText`. A diferença é que o `AppendText` adiciona um texto ao fim do arquivo, enquanto o `CreateText` sobrescreve o conteúdo do arquivo com um texto informado.

```csharp
using var file = File.AppendText(filePath);
file.WriteLine("Teste"); // Adiciona o texto ao fim do arquivo
file.Close();

using var file = File.CreateText(filePath);
file.WriteLine("Teste 2"); // Substitui o conteúdo do arquivo com este texto
file.Close();
```

É importante notar que ao manipular arquivos grandes, você não precisa ler o arquivo completamente para adicionar conteúdo ao mesmo. Neste caso é preferível utilizar o `AppendText`.

```csharp
// ERRADO 
var data = File.ReadText(filePath);
data += "Meu texto adicional";
using var file = File.CreateText(filePath);
file.WriteLine(data); // Substitui o conteúdo do arquivo com este texto
file.Close();

// CORRETO
using var file = File.AppendText(filePath);
file.WriteLine("Meu texto adicional"); // Adiciona o texto ao fim do arquivo
file.Close();
```

> **IMPORTANTE:** Em caso de arquivos grandes, é recomendado utilizar o **StreamWriter** para escrita e **StreamReader** para leitura.

### Copiar um arquivo
Para copiar um arquivo podemos usar o `File.Copy`, informando o caminho **completo** do arquivo atual e seu destino.

```csharp
File.Copy(filePath, @"C:\dev\outro.txt");
``` 
É importante notar que neste caso, se houver outro arquivo com mesmo nome, você receberá uma execeção. Para sobrescrever o arquivo de origem caso haja, utilize o parâmetro adicional para `overwrite` no método `Copy`.

```csharp
File.Copy(filePath, @"C:\dev\outro.txt", true); // Vai sobrescrever se precisar
``` 
### Mover um arquivo
Da mesma forma, podemos utilizar o `File.Move` para mover um arquivo de um diretório para outro, considerando os mesmos pontos do método anterior.

```csharp
File.Move(filePath, @"C:\dev\temp\novo.txt", overwrite: true);
```

### Substituir um arquivo
Para finalizar, podemos utilizar o `File.Replace` para substituir um arquivo, também considerando os mesmos pontos dos métodos anteriores.

```csharp
File.Replace(@"C:\dev\outro.txt", filePath, @"C:\dev\backup.txt");
```

### Excluir um arquivo
Caso queira excluir um arquivo, podemos utilizar o método `File.Delete` informando o caminho completo do mesmo. Caso o arquivo não seja encontrado ou não possa ser excluído, receberemos uma exceção.

```csharp
File.Delete(filePath);
```

### Verificar se um arquivo existe
Para verificar se um arquivo existe, podemos utilizar o `File.Exists` informando o caminho completo do arquivo. Este método nos retorna `true` ou `false` para dizer se o arquivo existe ou não.

```csharp
File.Exists(filePath); // Retorna um booleano
```

### Abertura e leitura de arquivos

Para saber mais sobre a abertura e leitura de arquivos, consulte nosso post anterior sobre [Como ler arquivos em C#](https://balta.io/blog/lendo-arquivos-em-csharp).

### Obter data de última modificação do arquivo
Outro método que já precisei utilizar e foi bem útil chama-se `File.GetLastAccessTime` que retorna a data/hora do último acesso a um arquivo.

```csharp
var time = File.GetLastAccessTime(filePath); // DateTime
Console.WriteLine(time);
```

### Obter atributos do arquivo
Para demais atributos do arquivo, podemos utilizar o `File.GetAttributes` que retorna um **enumerador**.

```csharp
var attrs =  File.GetAttributes(filePath);
if ((attrs & FileAttributes.ReadOnly) == FileAttributes.ReadOnly)
    Console.WriteLine("read-only");

if ((attrs & FileAttributes.Compressed) == FileAttributes.Compressed)
    Console.WriteLine("compressed");
```

## Conclusão
O `System.File` tem um enorme leque de opções para manipulação de arquivos, e seu uso é simples e direto. Devemos nos atentar apenas ao tratamento de erros pela sensibilidade na leitura/escrita de arquivos.


<div role="main" id="blog-s1-dotnet-134e3db1eea6c9829db1"></div>
<script type="text/javascript" src="https://d335luupugsy2.cloudfront.net/js/rdstation-forms/stable/rdstation-forms.min.js"></script>
<script type="text/javascript"> new RDStationForms('blog-s1-dotnet-134e3db1eea6c9829db1', 'UA-48664517-12').createForm();</script>