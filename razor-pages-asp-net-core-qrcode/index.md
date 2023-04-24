# Razor Pages ASP.NET Core QRCode
Neste artigo vamos aprender a gerar um QRCode usando o template Razor Pages em ASP.NET Core de forma simples e rápida.

## Criando a aplicação
Este projeto mostra como trabalhar com geração e renderização de um QRCode em um projeto Razor Pages no ASP.NET Core.

```
dotnet new razor -o render-qrcode
cd render-qrcode
```

## Instalando os pacotes via NuGet
Para facilitar nosso trabalho, vamos utilizar dois pacotes: <code>QRCoder</code> e <code>System.Drawing.Common</code>

```
dotnet add package QRCoder -v 1.4.2
dotnet add package System.Drawing.Common
```

Pronto, temos nosso projeto criado com os pacotes necessários para começarmos.

## Organizando o projeto
A ideia aqui é criar uma classe estática chamada <code>QrCodeServices</code> dentro de um arquivo chamado <code>Services.cs</code>, que vai permitir o reuso do QRCode em diferentes pontos da nossa aplicação.

```csharp
public static class QrCodeServices
{
}
```

## Gerando o QRCode
O trecho para gerar um QRCode utilizando o QRCoder é bem simples, precisamos do <code>QRCodeGenerator</code> e dos dados do QRCode.

```csharp
public static Bitmap GenerateImage(string content)
{
    var qrGenerator = new QRCodeGenerator();
    var qrCodeData = qrGenerator.CreateQrCode(content, QRCodeGenerator.ECCLevel.Q);
    var qrCode = new QRCode(qrCodeData);
    var qrCodeImage = qrCode.GetGraphic(20);

    return qrCodeImage;
}
```

Agora vamos escrever um método para injetar nosso bitmap em uma tag html ```<img>```. Para isso, vamos converter o objeto do tipo <code>System.Drawing.Bitmap</code> em uma imagem que pode ser usada em uma tag html ```<img>```, primeiro precisamos salvar o bitmap em um fluxo de memória e, em seguida, converter o fluxo em uma sequência de bytes. Por fim, é possível criar uma string com a sintaxe correta para uma tag html ```<img>``` e definir o valor do atributo ```src``` como um data URI contendo a sequência de bytes da imagem.

```csharp
public static string BitpmapToImageSource(string content)
{
    var bitmap = GenerateImage(content);
    var imageSource = string.Empty;
    
    using (MemoryStream memoryStream = new MemoryStream())
    {
        bitmap.Save(memoryStream, System.Drawing.Imaging.ImageFormat.Png);

        byte[] imageBytes = memoryStream.ToArray();

        imageSource = $"data:image/png;base64,{Convert.ToBase64String(imageBytes)}";
    }

    return imageSource;
}
```

Como resultado final, nossa classe ficou com os seguintes métodos.

```csharp
using QRCoder;
using System.Drawing;

public static class QrCodeServices
{
    public static Bitmap GenerateImage(string content)
    {
        var qrGenerator = new QRCodeGenerator();
        var qrCodeData = qrGenerator.CreateQrCode(content, QRCodeGenerator.ECCLevel.Q);
        var qrCode = new QRCode(qrCodeData);
        var qrCodeImage = qrCode.GetGraphic(20);

        return qrCodeImage;
    }

    public static string BitmapToImageSource(string content)
    {
        var bitmap = GenerateImage(content);
        var imageSource = string.Empty;
        
        using (MemoryStream memoryStream = new MemoryStream())
        {
            bitmap.Save(memoryStream, System.Drawing.Imaging.ImageFormat.Png);

            byte[] imageBytes = memoryStream.ToArray();

            imageSource = $"data:image/png;base64,{Convert.ToBase64String(imageBytes)}";
        }

        return imageSource;
    }
}
```

## Utilizando o QRCode
Agora vamos trabalhar na interface para obtenção dos dados que darão origem ao QRCode. Vamos criar uma página com o nome <code>GenerateQrCode</code>, vamos adicionar um formulário que será submetido com o método post contendo um ```<input>``` e um ```<button>```.

```
<form method="post">
  <div class="form-floating mb-3">
    <input asp-for="ContentQrCode" class="form-control" id="floatingInput" placeholder="Conteúdo QR Code">
    <span asp-validation-for="ContentQrCode" class="text-danger"></span>
    <label for="floatingInput">Conteúdo QR Code</label>
  </div>
  
  <div class="col-12">
      <button asp-page="RenderQrCode" class="btn btn-primary" type="submit">Gerar QR Code</button>
  </div>
</form>
```

Vamos fazer também a chamado do jQuery para validar nosso formulário do lado do cliente.

```
@section scripts{
   <partial name="_ValidationScriptsPartial" />
}
```

Nossa página ficará com o seguinte conteúdo:

```
@page
@model GenerateQrCodeModel
@{
    ViewData["Title"] = "Gerar QRCode ";
}
<h1>@ViewData["Title"]</h1>

<form method="post">
  <div class="form-floating mb-3">
    <input asp-for="ContentQrCode" class="form-control" id="floatingInput" placeholder="Conteúdo QR Code">
    <span asp-validation-for="ContentQrCode" class="text-danger"></span>
    <label for="floatingInput">Conteúdo QR Code</label>
  </div>
  
  <div class="col-12">
      <button asp-page="RenderQrCode" class="btn btn-primary" type="submit">Gerar QR Code</button>
  </div>
</form>

@section scripts{
   <partial name="_ValidationScriptsPartial" />
}
```

Em Razor Pages, todas as páginas com extensão <code>.cshtml</code> vem acompanhada de um arquivo <code>.cshtml.cs</code> de mesmo nome, onde escrevemos nossos códigos em C#, que serão processados do lado servidor.

Nesse arquivo, vamos criar uma propriedade do tipo string que estará ligada ao nosso input, segue como ficou nosso arquivo <code>.cshtml.cs</code>:

```csharp
using Microsoft.AspNetCore.Mvc.RazorPages;
using System.ComponentModel.DataAnnotations;

namespace render_qrcode.Pages;

public class GenerateQrCodeModel : PageModel
{
    private readonly ILogger<GenerateQrCodeModel> _logger;

    public GenerateQrCodeModel(ILogger<GenerateQrCodeModel> logger)
    {
        _logger = logger;
    }

   [Required(ErrorMessage="Favor informar o conteúdo do QR Code!")]
    public string ContentQrCode { get; set; } = null!;

    public void OnGet()
    {
    }
} 
```

Agora vamos trabalhar na interface que vai propriamente gerar e renderizar nosso QRCode, vamos adicionar uma nova página com o nome <code>RenderQrCode</code> post contendo um ```<img>``` onde iremos renderizar o QRCode e um ```<button>``` onde daremos a opção ao usuário de voltar a página anterior, da seguinte forma:

```
@page "~/renderqrcode/{content?}"
@model RenderQrCodeModel
@{
    ViewData["Title"] = "Renderização QRCode ";
}
<h1>@ViewData["Title"]</h1>

<div class="text-center">
  <img src=@Model.imageSource class="rounded" alt="QRCode" width="400" height="400">
</div>

<div class="col-12">
    <a asp-page="GenerateQrCode"><button class="btn btn-primary">Voltar</button></a>
</div>
```

Vamos agora trabalhar no arquivo <code>.cshtml.cs</code> da nossa página, precisamos incluir uma propriedade que irá armazenar o source da imagem que iremos injetar na tag ```<img>``` e capturar o conteúdo do QRCode que nos foi enviado por post. Teremos o seguinte resultado:

```csharp
using Microsoft.AspNetCore.Mvc.RazorPages;

namespace render_qrcode.Pages;

public class RenderQrCodeModel : PageModel
{
    private readonly ILogger<RenderQrCodeModel> _logger;

    public RenderQrCodeModel(ILogger<RenderQrCodeModel> logger)
    {
        _logger = logger;
    }

    public string imageSource = "";

    public void OnPost(string content)
    {
        imageSource = QrCodeServices.BitpmapToImageSource(Request.Form["ContentQrCode"]);
    }
}
```

Parar finalizarmos, vamos fazer a chamada da página <code>GenerateQrCode</code> no leiaute do nosso template.

```
<li class="nav-item">
    <a class="nav-link text-dark" asp-area="" asp-page="/GenerateQrCode">Gerar QRCode</a>
</li>
```

Pronto! Temos um gerador de QRCode em Razor Pages ASP.NET Core.

## Código fonte:
https://github.com/jfs-dev/render-qrcode-web/

## Referências
https://learn.microsoft.com/pt-br/aspnet/core/razor-pages/?view=aspnetcore-7.0&tabs=visual-studio/

https://github.com/codebude/QRCoder/

https://balta.io/blog/aspnet-qrcode/
