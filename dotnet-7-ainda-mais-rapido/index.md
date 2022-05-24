Existe um motivo pelo qual o .NET 7 vai ser muito mais rápido do que o .NET 6, e você não precisar alterar nenhuma linha de código para se beneficiar disto.

## Vídeo
[![O .NET 7 vai ser muito mais rápido por este motivo...](https://baltaio.blob.core.windows.net/static/images/articles/dotnet-7-ainda-mais-rapido_play.png)](https://www.youtube.com/watch?v=Vae07KDcpaw)

## Transcrição
Estamos falando do AOT ou **Ahead of time**, um tipo de compilação que deixa tudo muito mais otimizado, mas é claro que isto tem um preço.

O .NET é um dos Frameworks mais rápidos do mercado, chegando a ser até 10x mais rápido que o Node e só perdendo (Por pouca diferença) para o Rust.

Atualmente o .NET que conhecemos, na versão 6, trabalha com um tipo de compilação chamado JIT, ou Just In Time, algo como “sob demanda”.
O que acontece é que nossas aplicações não são completamente compiladas, alguns pedaços dela são compilados sob demanda, conforme a aplicação é utilizada.

Este processo permite que o pacote final da aplicação fique menor, e que o tempo de compilação demore menos, o que para alguns casos pode impactar bastante.

Porém, existe uma outra forma de entregar as aplicações, completamente compiladas, com o que chamamos de AOT ou Ahead of Time, algo como a frente do tempo.

Neste modelo nós temos tudo exatamente compilado, extremamente otimizado, mas como resultado temos pacotes maiores e o tempo de compilação também demora.

A grande questão é que hoje, no .NET 6 só é possível utilizar o JIT, mais especificamente o Ryu JIT que é o responsável por “pré-compilar” nossas aplicações.

Mas isto vai mudar no .NET 7, com a inclusão de um compilador AOT nativo, o que permite fazer este tradeoff sem mudar uma ÚNICA LINHA de código.

Então se você precisa da máxima performance e não está preocupado com tempo de Build do seu projeto, o AOT é uma mudança significativa para você.

Claro que o JIT será mantido, da mesma forma como temos hoje, só será uma adição, um item a mais no leque de possibilidades.

O mais interessante de tudo isto, é que esta novidade será aplicada ao .NET de uma forma geral, e poderá beneficiar diversos frameworks que utilizamos como ASP.NET, Blazor e MAUI.

Particularmente, eu estou bem ansioso pelo .NET 7 que sai em Novembro deste ano. Já saiu a Preview 3 e você pode acompanhar todas as novidades aqui, então fica de olho no nosso conteúdo para não perder nada.

### Código Fonte
* [GitHub - Steven Sanderson](https://github.com/SteveSandersonMS/BlazeOrbital)