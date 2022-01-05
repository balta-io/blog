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

```csharp
File.Copy(filePath, @"C:\dev\outro.txt");
``` 

### Excluir um arquivo
```csharp
File.Delete(filePath);
```

### Mover um arquivo
```csharp
File.Move(filePath, @"C:\dev\temp\novo.txt", overwrite: true);
```

### Subscrituir um arquivo
```csharp
File.Replace(@"C:\dev\outro.txt", filePath, @"C:\dev\backup.txt");
```

### Verificar se um arquivo existe
```csharp
File.Exists(filePath);
```
### Mover um arquivo
```csharp
File.Move(filePath, @"C:\dev\temp\novo.txt", overwrite: true);
```

### Abrir um arquivo
```csharp
var file = File.Open(filePath, FileMode.Open);
var file = File.OpenRead(filePath); // FileStream
```

### Ler um arquivo
REFERENCIAR ARTIGO ANTERIOR
```csharp
var file = File.OpenText(filePath); // StreamReader
Console.WriteLine(file.ReadToEnd());
```

### Obter data de última modificação do arquivo
```csharp
var time = File.GetLastAccessTime(filePath); // DateTime
Console.WriteLine(time);
```

### Obter atributos do arquivo
```csharp
var attrs =  File.GetAttributes(filePath);
if ((attrs & FileAttributes.ReadOnly) == FileAttributes.ReadOnly)
    Console.WriteLine("read-only");

if ((attrs & FileAttributes.Compressed) == FileAttributes.Compressed)
    Console.WriteLine("compressed");
```