## O que é a compressão de respostas?

Sempre que fazemos uma requisição para uma aplicação, seja ela ASP.NET, PHP, Java, ou qualquer outra, a mesma é processada e um retorno nos é enviado.

Tanto no trabalho com APIs quanto na geração de conteúdo dinâmico (HTML) no servidor, o retorno sempre será um texto, hora HTML, hora JSON.

Todo texto que sai do servidor por sua vez tem um tamanho, que pode ser diminuído utilizando uma **compressão**, assim como fazemos com os arquivos .ZIP em nossos sistemas operacionais.

Habilitar a compressão de resposta reduz o tamanho do retorno do servidor, o que consome menos banda, torna as aplicações mais rápidas de serem trafegadas e até aumentam o [rankeamento do site](https://web.dev/uses-text-compression/).

Em resumo, basicamente o conteúdo a ser retornado é **zipado** no servidor e o browser **descompacta** ele antes de fazer a leitura.

Sempre que um browser possuir a capacidade de processar uma resposta compactada, ele enviará o cabeçalho <code>Accept-Encoding</code> informando os dados da compressão.

Assim que o servidor recebe uma requisição com o cabeçalho mencionado, ele adiciona o item <code>Content-Encoding</code> no cabeçalho da resposta.

Abaixo estão os possíveis valores para o cabeçalho da requisição a ser comprimida.

| Accept-Encoding            | Descrição                        |
| -------------------------- | -------------------------------- |
| br **<sup>(Padrão)</sup>** | Brotli Compressed Data Format    |
| gzip                       | Formato GZip                     |
| deflate                    | Deflate Compression              |
| identity                   | Resposta NÃO DEVE ser comprimida |
| \*                         | Qualquer um disponível           |

Existe um [lista de tipos de compressores](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Headers/Accept-Encoding), porém, podemos criar um totalmente customizado.

A compressão é definida pela [RFC 7231](https://tools.ietf.org/html/rfc7231#section-5.3.4) e dentre vários detalhes, está o "peso" que define a ordem de execução da mesma, especificado por um <code>q</code> seguido do valor.

Por exemplo, podemos especificar que desejamos tentar a compressão via **gzip** primeiro, seguido pelo **identity** e caso nenhum seja encontrado, seguimos com **\*** que significa qualquer compressão disponível.

Neste caso, o seguinte código seria enviado no cabeçalho:

```
Accept-Encoding: gzip;q=1.0, identity; q=0.5, *;q=0
```

Como podemos notar, definimos <code>gzip;q=1</code> com peso 1, e os demais itens vamos diminuindo o peso, que consequentemente afeta sua ordem de execução.

> O algoritmo de compressão é subjetivo ao custo-benefício entre a velocidade e efetividade (Tamanho final do resultado da compressão) da execução. O menor tamanho final sempre será resultado da compressão mais otimizada.

### Compressão nativa

Alguns servidores como IIS e Nginx possuem uma compressão nativa para arquivos como JS, CSS, HTML, JSON e XML. Caso seu servidor já ofereça compressão, não é necessário utilizar esta compressão adicional do ASP.NET.

Em adicional, é desnecessário comprimir imagens. Os formatos mais comuns como PNG e JPG por exemplo, já são compactados. O mesmo vale para arquivos muito pequenos, menores de 1KB por exemplo.

Nestes casos, a relação custo X benefício entre a compressão e tráfego dos arquivos acaba não compensando.

### Cabeçalhos de compressão

| Header           | Descrição                                                                                                                                |
| ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| Accept-Encoding  | Enviado pelo cliente para definir o tipo de compressão                                                                                   |
| Content-Encoding | Enviado pelo servidor para saber o tipo de compressão utilizada no resultado                                                             |
| Content-Length   | Quando a compressão ocorre, este cabeçalho é removido, visto que o tamanho do corpo da resposta mudou e seu hash já não é mais válido    |
| Content-MD5      | Quando a compressão ocorre, este cabeçalho é removido, visto que o tamanho do corpo da resposta mudou e seu hash já não é mais válido    |
| Content-Type     | Este cabeçalho define o formato (HTML, JSON, XML) do conteúdo e isto diz se o mesmo pode ou não ser compactado, como vimos anteriormente |
| Vary             | Indica que o retorno deve ser cacheado                                                                                                   |

## ASP.NET Response Compression

Agora que sabemos o que são as compressões de respostas e como funcionam as requisições e respostas comprimidas, vamos implementar este recurso utilizando o ASP.NET.

Em suma, tudo que precisamos fazer é adicionar os cabeçalhos nas requisições e respostas que chegam e saem da nossa aplicação, e para isto, precisamos **interceptar** estas requisições e respostas.

Sempre que falamos em interceptar algo no ASP.NET, utilizamos os **Middlewares** para isto, e para nossa felicidade, o ASP.NET já possui um Middleware de compressão.

> **Middlewares** são agentes que podem ser executados entre as requisições para as nossas aplicações. Podemos definir vários Middlewares, que vão agir em uma requisição, adicionando cache, compressão e diversas outras coisas.
>
> Os Middlewares são executados na ordem que são adicionados a nossa aplicação e no final, temos o que chamamos de **Pipeline**, que é o túnel que a requisição percorre até chegar em uma Action de um Controller por exemplo.

### Instalação

O ASP.NET possui um pacote integrado, chamado <code>Microsoft.AspNetCore.ResponseCompression</code>, então não precisamos instalar nada adicional para isto.

### Configuração

Normalmente precisamos configurar um Middleware adicionando seu serviço no método <code>ConfigureServices</code> e posteriormente definindo sua execução no méotod <code>Configure</code>. Com o **ResponseCompression** não é diferente.

Desta forma, toda configuração inicial que precisamos é esta:

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddResponseCompression();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseResponseCompression();
    }
}
```

Como comentado anteriormente, os Middlewares são executados na ordem que são declarados, então é importante definir o <code>UseResponseCompression</code> antes de qualquer outro Middleware de resposta.

## Compondo a requisição

Após a adição e configuração do pacote de compressão, já podemos executar a aplicação e fazer uma requisição para qualquer URL dela.

Para uma melhor visualização, recomendamos os testes em uma aplicação ASP.NET MVC, utilizando <code>dotnet net mvc</code> por exemplo.

Com tudo pronto, vamos realizar a requisição sem nenhuma compressão especificada. Abaixo os exemplos são realizados utilizando **CURL**, porém você pode utilizar outras ferramentas como **Insomnia**, **Fiddler** e **Postman**.

```
curl -i -H "Content-Type: text/html" https://localhost:5001
```

Como retorno podemos esperar o HTML padrão que temos nas aplicações ASP.NET MVC, totalmente legível para nós.

Porém, se informarmos o <code>Accept-Encoding: br</code> no cabeçalho, conforme mostrado abaixo, devemos esperar um retorno compactado, ilegível.

```
curl -i -H "Accept-Encoding: br" -H "Content-Type: text/html" https://localhost:5001
```

> **IMPORTANTE** - Algumas ferramentas podem descompactar automaticamente as respostas e exibir o HTML formatado. Verifique sempre o tamanho (Size) das respostas.

## Utilizando outros providers

No exemplo anterior vimos como utilizar a compressão padrão, porém, como vimos na primeira sessão deste artigo, podemos utilizar outros formatos de compressão como **Brotli Compression**.

Para utilizar diferentes tipos de compressão, podemos utilizar as opções do método <code>AddResponseCompression</code> juntamente com a opção <code>Providers.Add\<PROVIDER></code>. Os tipos de compressão serão executados na ordem que são adicionados aqui.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression(options =>
    {
        options.Providers.Add<BrotliCompressionProvider>();
        options.Providers.Add<GzipCompressionProvider>();
        options.Providers.Add<CustomCompressionProvider>();
    });
}
```

## Alterando o nível de compressão

Por fim, conforme comentamos no começo deste artigo, o ASP.NET toma conta da decisão sobre a otimização da compressão, levando em conta o tamanho da resposta e esforço necessário para codificação.

Para alterar este fator e ter controle sobre o nível de compressão a ser executado, podemos dentro de cada provider, especificar seu <code>Compression Level</code>.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();

    services.Configure<BrotliCompressionProviderOptions>(options =>
    {
        options.Level = CompressionLevel.Fastest;
    });
}
```

Desta forma, o ASP.NET Core sempre optará pela compressão mais rápida possível, mesmo que o provider seja otimizado para compactação.

Abaixo segue a tabela com as opções de **níveis de compressão** disponíveis no ASP.NET.

| Nível                          | Descrição              |
| ------------------------------ | ---------------------- |
| CompressionLevel.Fastest       | Favorece a performance |
| CompressionLevel.NoCompression | Sem compressão         |
| CompressionLevel.Optimal       | Favorece a compressão  |

### Tipos de documento

Por padrão, a compressão pode ser adicionada aos seguintes tipos de documento (**MIME Types**):

- application/javascript
- application/json
- application/xml
- text/css
- text/html
- text/json
- text/plain
- text/xml

Caso queira comprimir algum tipo de documento não especificada nesta lista, você pode criar um [provider customizado](https://docs.microsoft.com/pt-br/aspnet/core/performance/response-compression?view=aspnetcore-3.1#custom-providers) como mostrado na documentação oficial do ASP.NET.

### Fonte

- [Documentação oficial do ASP.NET](https://docs.microsoft.com/pt-br/aspnet/core/performance/response-compression?view=aspnetcore-3.1)

<div role="main" id="blog-s1-dotnet-134e3db1eea6c9829db1"></div>
<script type="text/javascript" src="https://d335luupugsy2.cloudfront.net/js/rdstation-forms/stable/rdstation-forms.min.js"></script>
<script type="text/javascript"> new RDStationForms('blog-s1-dotnet-134e3db1eea6c9829db1', 'UA-48664517-12').createForm();</script>