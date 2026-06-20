O .NET suporta diferentes plataformas como Windows, Mac e Linux, mas você sabe como criar um código específico para uma delas?

## OperatingSystem
O .NET 6 traz ainda mais plataformas para ampliar o leque, contando com iOS, Android, MacCatalyst e muito mais.

Isto nos permite criar sistemas para múltiplas plataformas com o mesmo **codebase** e muitas vezes sem precisar alterar nada do código.

Porém, em alguns casos queremos executar determinadas ações somente em uma (Ou mais de uma) plataforma específica, e para isto temos o `OperatingSystem`, um enumerador que podemos indagar sobre qual SO estamos utilizando.

```csharp
if (OperatingSystem.IsWindows())
    Console.WriteLine("Estou no Windows");

if (OperatingSystem.IsMacOS())
    Console.WriteLine("Estou no Mac");
```

O `OperatingSystem` tem todos as plataformas suportadas pelo .NET e futuras plataformas, quando adicionadas, também serão visíveis aqui.

## SupportedOSPlatform
Além do `OperatingSystem` podemos utilizar o atributo `SupportedOSPlatform` que adiciona um **warning** a nível de compilação dizendo que um método só pode se executado em determinadas plataformas.

Algumas IDEs como o JetBrain Raider e Visual Studio já interpretam este alerta e notificam em tempo de escrita que a chamada ao método é inválida.

Em caso de invocação de um método não suportado pela plataforma atual, o **warning** abaixo será exibido.

```
 warning CA1416: This call site is reachable on all platforms. 'CustomFileWriter.WindowsOnly()' is only supported on: 'windows'. 
```

Neste exemplo estou no Mac e criei um método com atributo `SupportedOSPlatform` informando que o mesmo só suporta a plataforma **Windows**, como mostrado abaixo.

```csharp
using System.Runtime.Versioning;

[SupportedOSPlatform("windows")]
public void WindowsOnly()
{
    //...
}
```

### Suportando múltiplas plataformas
O atributo `SupportedOSPlatform` é cumulativo, o que significa que podemos suportar diferentes plataformas em um mesmo atributo, como mostrado no exemplo abaixo.

```csharp
using System.Runtime.Versioning;

[SupportedOSPlatform("windows"), SupportedOSPlatform("macos")]
public void WindowsAndMac()
{
}
```
Por fim, aqui vai o exemplo completo, suportando **Mac**, **Windows** e ambos. Infelizmente não existe um enumerador com o nome das plataformas, é preciso informá-las como `string`, o que pode conter erros.

```csharp
using System.Runtime.Versioning;

public class CustomFileWriter
{
    [SupportedOSPlatform("windows")]
    public void WindowsOnly()
    {
    }
    
    [SupportedOSPlatform("macos")]
    public void MacOnly()
    {
    }
    
    [SupportedOSPlatform("windows"), SupportedOSPlatform("macos")]
    public void WindowsAndMac()
    {
    }
}
```

## Conclusão
O .NET suporta múltiplas plataformas e embora possamos escrever a maior parte do código de forma bem genérica, alguns pontos ainda precisam ser específicos, então podemos fazer uso do `OperatingSystem` e `SupportedOSPlatform` para resolver este problema.


<div role="main" id="blog-s1-dotnet-134e3db1eea6c9829db1"></div>
<script type="text/javascript" src="https://d335luupugsy2.cloudfront.net/js/rdstation-forms/stable/rdstation-forms.min.js"></script>
<script type="text/javascript"> new RDStationForms('blog-s1-dotnet-134e3db1eea6c9829db1', 'UA-48664517-12').createForm();</script>