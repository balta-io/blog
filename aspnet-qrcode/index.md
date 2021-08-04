Neste artigo vamos aprender a gerar um QRCode via ASP.NET Core de forma simples e rápida. 

## Versão em vídeo

<iframe width="560" height="315" src="https://www.youtube.com/embed/Teezqtgk6Qc" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


## QRCoder

Para facilitar nosso trabalho, vamos utilizar um pacote chamado **QRCoder**, um projeto Open Source disponível no GitHub e via Nuget.


```
dotnet new mvc -o MeuApp
cd MeuApp
dotnet add package QRCoder
```

Pronto, temos nosso projeto criado com o pacote necessário para começarmos.

## Organizando o projeto

A ideia aqui é criar uma classe que vai permitir o reuso do QRCode em diferentes pontos do nosso App.

O pacote em sí já faz muita coisa, mas parte de gerar imagens, particulamente prefiro separar para um reuso ainda maior.

### Gerando o QRCode

O trecho para gerar um QRCode utilizando o **QRCoder** é bem simples, precisamos do <code>QRCodeGenerator</code> e dos dados do QRCode.

```csharp
var qrGenerator = new QRCodeGenerator();
var qrCodeData = qrGenerator.CreateQrCode(url, QRCodeGenerator.ECCLevel.Q);
var qrCode = new QRCode(qrCodeData);
var qrCodeImage = qrCode.GetGraphic(10);
return qrCodeImage;
```

Para ficar ainda melhor, vamos separar este código em um método que já retorna um <code>Bitmap</code>. Isto vai nos ajudar em todo processo.

```csharp
public static Bitmap GenerateImage(string url)
{
    var qrGenerator = new QRCodeGenerator();
    var qrCodeData = qrGenerator.CreateQrCode(url, QRCodeGenerator.ECCLevel.Q);
    var qrCode = new QRCode(qrCodeData);
    var qrCodeImage = qrCode.GetGraphic(10);
    return qrCodeImage;
}
```

Em alguns pontos também precisei dos bytes da imagem ao invés do Bitmap, então decidi criar dois métodos adicionais, um para retornar os bytes da imagem e outro para retornar os bytes dado uma URL.

```csharp
public static byte[] GenerateByteArray(string url)
{
    var image = GenerateImage(url);
    return ImageToByte(image);
}

private static byte[] ImageToByte(Image img)
{
    using var stream = new MemoryStream();
    img.Save(stream, System.Drawing.Imaging.ImageFormat.Png);
    return stream.ToArray();
}
```

Como resultado final, nossa classe ficou com os seguintes métodos.

```csharp
public static class QrCodeGenerator
{
    public static Bitmap GenerateImage(string url)
    {
        var qrGenerator = new QRCodeGenerator();
        var qrCodeData = qrGenerator.CreateQrCode(url, QRCodeGenerator.ECCLevel.Q);
        var qrCode = new QRCode(qrCodeData);
        var qrCodeImage = qrCode.GetGraphic(10);
        return qrCodeImage;
    }

    public static byte[] GenerateByteArray(string url)
    {
        var image = GenerateImage(url);
        return ImageToByte(image);
    }

    private static byte[] ImageToByte(Image img)
    {
        using var stream = new MemoryStream();
        img.Save(stream, System.Drawing.Imaging.ImageFormat.Png);
        return stream.ToArray();
    }
}
```

## Utilizando o QRCode

Agora tudo que temos a fazer é chamar nosso gerador em algum método do controlador.

```csharp
var image = QrCodeGenerator.GenerateByteArray("https://balta.io");
```

Com a imagem gerada, podemos responder com um <code>FileResult</code> passando os bytes da imagem e o Mime/Type.

```csharp
[HttpGet("qrcode")]
public IActionResult GetQrCode()
{
    var image = QrCodeGenerator.GenerateByteArray("https://balta.io");
    return File(image, "image/jpeg");
}
```

Desta forma, ao executar nossa aplicação, teremos o **QRCode** sendo exibido na URL abaixo.

```
https://localhost:5001/qrcode
```

### Utilizando no HTML

Para renderizar o QRCode na tela, basta utilizar a tag <code>img</code> com a URL anterior.

```html
<img src="https://localhost:5001/qrcode" />
```

Pronto! Temos um gerador de QRCode com ASP.NET.

<div role="main" id="blog-s1-dotnet-134e3db1eea6c9829db1"></div>
<script type="text/javascript" src="https://d335luupugsy2.cloudfront.net/js/rdstation-forms/stable/rdstation-forms.min.js"></script>
<script type="text/javascript"> new RDStationForms('blog-s1-dotnet-134e3db1eea6c9829db1', 'UA-48664517-12').createForm();</script>