MAUI é a sigla para Mult Application User Interface, ou interfaces para múltiplas plataformas, uma tecnologia nova e moderna da Microsoft para criação de interfaces.

## .NET 6 - Preview 2

O MAUI passou teve sua primeira aparição agora em Março de 2021, no Preview 2 do .NET 6, que tem seu lançamento previsto para Novembro de 2021.

Se você ainda não viu as novidades do .NET 6, dá uma olhada nos artigos [.NET 6 - Preview 1](https://balta.io/blog/novidades-dotnet-6-preview-1) e [.NET 6 - Preview 2](https://balta.io/blog/novidades-dotnet-6-preview-2) onde mostramos tudo que rolou de bom nestas releases.

Voltando ao tópico principal, esta versão ainda é um `Preview`, ou seja, está longe de ter sua versão final para produção, então, use por sua conta e risco.

## MAUI e Xamarin

Muitas mudanças estão por vir e a primeira delas é que o MAIU na verdade é uma evolução do Xamarin, então se você já conhece e usa a tecnologia, estará bem em casa.

A ideia principal do MAUI é trazer todo o ecossistema do Xamarin para dentro do .NET, modernizando a forma como usamos a tecnologia hoje.

Um bom exemplo disso é que poderemos utilizar o [Visual Studio Code](https://balta.io/blog/visual-studio-code-instalacao-customizacao) para codar nossos Apps, junto a comandos como `dotnet new android` por exemplo, para iniciar um projeto.

Além disso, o MAUI unifica todos os projetos, dispensando a criação de projetos específicos para plataformas (Android/iOS) e melhorando ainda mais o compartilhamento de código.

Futuramente o MAUI trará integrações com Web utilizando Blazor, sintaxe declarativa (Especulação) e muito mais.

## Instalando o .NET 6 - Preview 2

Para executar estas demos você precisará instalar o [.NET 6 - Preview 2](https://dotnet.microsoft.com/download/dotnet/6.0), que rodará ao lado de outras SDKs e Runtimes do .NET na sua máquina. No caso, para os exemplos precisamos do SDK instalado.

Você pode verificar a versão do .NET instalada executando o comando `dotnet --version` ou `dotnet --list-sdks` para visualizar todas.

### Carregando os Workloads

Para poder compilar os Apps para diferentes plataformas, precisamos instalar alguns Workloads adicionais e este passo, depende do que você quer compilar.

No meu caso, como estou no Mac, acabei instalando todos os pacotes contidos [nesta página](https://github.com/dotnet/net6-mobile-samples#net-600-mobile-samples).

* Microsoft.NET.Workload.Android-11.0.200.148
* Microsoft.iOS.Bundle.14.4.100-ci.main.1192
* Microsoft.MacCatalyst.Bundle.14.3.100-ci.main.337

O Workload do Mac Catalyst é para podermos criar Apps que rodam tanto em MacOs quanto iOS com apenas alguns ajustes na interface.

## Seu primeiro App MAUI
Ainda não temos um comando para criar um App via CLI, então precisamos clonar o exemplo que temos do MAUI direto do GitHub oficial dele.

Caso você precise de ajuda com Git/GitHub, [leia este artigo](https://balta.io/blog/git-github-primeiros-passos) onde mostro como configurar e dar seus primeiros passos com eles.

Vamos então clonar o repositório utilizando o comando `git clone` como mostrado abaixo.
```
git clone https://github.com/dotnet/net6-mobile-samples.git
```

Feito isto, abra a pasta clonada com o **Visual Studio Code**, ele já vem com suporte e pedirá para instalar algumas extensions adicionais quando o projeto carregar.

Na página oficial do MAUI no GitHub (De onde clonamos o exemplo) tem mais informações sobre como utilizar o **Visual Studio** e **Visual Studio For Mac** com .NET 6 caso precise.

```
cd net6-mobile-samples
code .
```

### O que mudou?

Este exemplo trás quatro Apps, `HelloAndroid`, `HelloiOS`, `HelloMacCatalyst` e o que nós queremos, que é o `HelloMaui`.

Se você examinar estas outras pastas, verá que são aplicações Xamarin, dedicadas especificamente a uma plataforma, porém se você examinar a pasta `HelloMaui` verá que a mesma contém as pastas do Android, iOS e MacCatalyst também.

Isto deve-se ao fato que o MAUI unificou os projetos e já traz tudo integrado para nós. Em suma, vamos focar em um projeto e ter como resultado a interface para várias plataformas.

#### Build

Para compilar uma aplicação específica, como a do iOS por exemplo, podemos executar o comando:

```
dotnet build HelloiOS
dotnet build HelloiOS -t:Run
```

Utilizamos o flag `-t:Run` para executar nosso App em um emulador disponível no ambiente.

Para o projeto MAUI as coisas são diferentes, além do `-t:Run`, precisamos especificar a plataforma utiliza o flag `-f`.

```
dotnet build HelloMaui -t:Run -f net6.0-android --no-restore
dotnet build HelloMaui -t:Run -f net6.0-ios --no-restore
dotnet build HelloMaui -t:Run -f net6.0-maccatalyst --no-restore
```

No momento os nomes das plataformas ainda estão com o prefixo `net6.0-`, mas acredito que isto mudará com o tempo. Também precisei adicionar o flag `--no-restore` por conta de um bug que acredito ser corrigido nas próximas versões.

### Executando o App

Você pode executar os comandos anteriores (Não ao mesmo tempo) e visualizar o App em diversos dispositivos para notar que é realmente o mesmo visual.

No meu caso, executei no MacOs e simulador do iPad para conferir o resultado e realmente ficou muito bacana.

![MAUI](https://baltaio.blob.core.windows.net/static/content/articles/images/photo_2021-03-17_21-36-39.jpg)

Claro que é uma versão inicial ainda, os componentes não estão 100% polidos, mas podemos esperar uma boa compatibilidade entre diferentes dispositivos futuramente.

## Fonte
* [Página Oficial do MAUI](https://github.com/dotnet/net6-mobile-samples)