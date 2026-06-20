Pouco mais de duas semanas após o lançamento do Preview 1, ontem tivemos o lançamento do Preview 2 do .NET, com novidades principalmente para o MAUI.

Esta nova versão inclui algumas novas APIs, melhorias de performance, um build inicial do MAUI e um build inicial para o Apple Silicon, que não foi shipado no Preview 1.

Também foi anunciado que serão realizados Previews mensais até Novembro, que será o lançamento oficial do .NET 6. Caso ainda não tenha lido nosso post sobre [Novidades do .NET 6 - Preview 1](https://balta.io/blog/novidades-dotnet-6-preview-1), recomendo que faça antes de prosseguir aqui.

## Melhorias de performance
Performance sempre foi o foco do .NET e eles estão sempre melhorando estes pontos nas versões novas, mas no .NET 6 eles tem um objetivo ainda maior.

Eles querem garantir que não apenas o os Apps que produzimos estão o mais otimizado possível, mas que nosso "dia-a-dia" também, que possamos ser produtivos com o .NET, otimizando tarefas alteração de código, build e teste.

Então há um foco grande em otimizar o startup dos Apps, o `dotnet cli`, o MSBuild. Veja este gráfico que comparam as melhorias nos builds do .NET 5 VS .NET 6.

![Gráfico de Performance](https://devblogs.microsoft.com/dotnet/wp-content/uploads/sites/10/2021/03/dotnet6-build-improvements.png)

## Melhor experiência de desenvolvimento

Uma das partes mais aguardadas do .NET 6 é o suporte ao desenvolvimento mobile, que hoje é entregue em um produto separado, o Xamarin. Durante o crescimento do Xamarin, ele foi ficando mais similar ao que temos no .NET, inclusive deve ter inspirado boas coisas dentro das versões novas .NET, mas agora é a hora de trazê-lo para dentro.

Nos últimos anos eles tem trabalhado para trazer o `Mono` para dentro do .NET, o que faz muito sentido já que eles tem bastante similaridade, mas ainda assim precisam de targets diferentes.

No .NET 5 o Blazor Wasm foi movido desta forma e agora o Xamarin também será, aumentando ainda mais a quantidade de código compartilhado entre os projetos .NET.

Esta é a experiência que eles querem entregar com o .NET 6, não apenas performance, mas conforto no desenvolvimento, um mesmo ambiente, mais compartilhamento de código.

## .NET MAUI
Foi adicionado o suporte a projeto único do MAUI, comentado no Preview 1, agora o mesmo projeto mantém Android, iOS, Desktop e o que mais vier pela frente.

Também foi adicionado suporte ao `Mac Catalyst` para criar Apps compartiveis com todo ecossistema Apple (Mac, iPad e iOS), basta adicionar a referência ao projeto.

```
<TargetFrameworks>net6.0-android;net6.0-ios</TargetFrameworks>
<TargetFrameworks Condition=" '$(OS)' != 'Windows_NT' ">$(TargetFrameworks);net6.0-maccatalyst</TargetFrameworks>
```

A nova estrutura do projeto ficou bem simples e agora podemos visualizar todas as plataformas na mesma solução de forma clara. O suporte a Windows depende do WinUI 3, que está em Preview também.

![Exemplo da solution](https://user-images.githubusercontent.com/41873/110378427-36651a80-801b-11eb-8707-ae3f7109ff16.png)

### Compartilhamento de assets

Fontes, imagens, ícones e outros assets agora podem ser compartilhados entre diversos projetos, ficando em uma pasta separada. Eles ainda serão tratados nativamente, e você pode referenciá-los no seu `*.csproj` assim.

```xml
<ItemGroup>
    <SharedImage Include="appicon.svg" ForegroundFile="appiconfg.svg" IsAppIcon="true" />
    <SharedFont Include="Resources\Fonts\ionicons.ttf" />
</ItemGroup>
```

ou assim:

```xml
<ItemGroup>
    <SharedImage Include="appicon.svg" ForegroundFile="appiconfg.svg" IsAppIcon="true" />
    <SharedImage Include="Resources\Images*" />
    <SharedFont Include="Resources\Fonts*" />
</ItemGroup>
```

### HosBuilder no MAUI
Para quem trabalha com ASP.NET, está familiarizado com o HostBuild para fazer o boostrap do App, e no MAUI também teremos este suporte, o que torna itens como DI mais fáceis de serem resolvidos.

Além deste novo processo, a interface `IWindow` foi adicionada, que permitirá a gestão de múltiplas janelas do seu App em um futuro.

```csharp
public class Application : MauiApp
{
    public override IAppHostBuilder CreateBuilder() => 
        base.CreateBuilder()
            .RegisterCompatibilityRenderers()
            .ConfigureServices((ctx, services) =>
            {
                services.AddTransient<MainPage>();
                services.AddTransient<IWindow, MainWindow>();
            })
            .ConfigureFonts((hostingContext, fonts) =>
            {
                fonts.AddFont("ionicons.ttf", "IonIcons");
            });

    public override IWindow CreateWindow(IActivationState state)
    {
        Microsoft.Maui.Controls.Compatibility.Forms.Init(state);
        return Services.GetService<IWindow>();
    }
}
```

### Novos controles
Nesta versão foram introduzidos os primeiros controles e propriedades que implementam o que há de mais novo no MAIU. Eles incluem a implementação dos novos botões, labels, inputs (Entry), Slider e Switch.

O repositório no GitHub já está aberto para contribuições, então podemos esperar boas novas em breve.

O exemplo dispobilizado junto a este Preview utiliza estes controles e o mesmo projeto roda no MacOs, iOS e Android, o resultado é este:

*MacOs*
![MacOs](https://user-images.githubusercontent.com/41873/110378734-8d6aef80-801b-11eb-95fa-25155b9216ce.png)

*iOS*
![iOS](https://user-images.githubusercontent.com/41873/110379248-33b6f500-801c-11eb-8ef5-b0193d26a70e.png)

*Android*
![Android](https://user-images.githubusercontent.com/41873/110379091-fce0df00-801b-11eb-9bf3-976174650f1b.png)

Também houveram melhorias nos SDK do Android e iOS, incluindo recursos como Remote iOS Simulator e usar o Mac como build remoto.

## .NET 

### System.Text.Json – ReferenceHandler.IgnoreCycles
O JsonSerializer (System.Text.Json) agora suporta ignorar ciclos de serialização. A opção `ReferenceHandler.IgnoreCycles` tem um comportamento similar ao `ReferenceLoopHandling.Ignore` do `Newtonsoft.Json`, com a diferença que o `System.Text.Json` substitui os loops de referência com nulo ao invés de ignorar a referência do objeto.

No exemplo abaixo, a propriedade `Next` é serializada como `null` visto que a mesma gera um ciclo.

```csharp
class Node
{
    public string Description { get; set; }
    public object Next { get; set; }
}

void Test()
{
    var node = new Node { Description = "Node 1" };
    node.Next = node;

    var opts = new JsonSerializerOptions { ReferenceHandler = ReferenceHandler.IgnoreCycles };

    string json = JsonSerializer.Serialize(node, opts);
    Console.WriteLine(json); // Prints {"Description":"Node 1","Next":null}
}
```

### PriorityQueue
`PriorityQueue<TElement, TPriority>` é uma nova coleção contida no namespace `System.Collections.Generic` que além do elemento possui sua prioridade. Ao ser desenfileirada o `PriorityQueue` retorna o elemento com menor prioridade.

Ela é similar ao `Queue<T>` mas neste caso, cada elemento tem sua prioridade que será respeitada.

```csharp
// creates a priority queue of strings with integer priorities
var pq = new PriorityQueue<string, int>();

// enqueue elements with associated priorities
pq.Enqueue("A", 3);
pq.Enqueue("B", 1);
pq.Enqueue("C", 2);
pq.Enqueue("D", 3);

pq.Dequeue(); // returns "B"
pq.Dequeue(); // returns "C"
pq.Dequeue(); // either "A" or "D", stability is not guaranteed.
```

### Melhorias no parse de números
Foram adicionadas melhorias a padronização dos tipos numéricos, em especial no `.ToString` e `.TryFormat`. Ele vão fornecer um resultado melhor em decimais com precisões maiores que 99 casas decimais.

### SignalR Nullable Annotations
O SignalR Client recebeu possibilidade de notação nula. Isto significa que o compilador C# vai prover o feedback apropriado quando você habilitar esta nulabilidade, baseado em como você manipula nulos no SignalR.

O SignalR Server já havia recebido esta melhor na versão 5.0 e novas boas mudanças foram feitas na versão 6.0.

Nullable Annotations foram um grande foco no .NET Core 3.x e você pode adicionar esta opção utilizando `<Nullable>enable</Nullable>` no seu projeto.

## Runtime

### Compilação com Crossgen 2

Todos os assmeblies do .NET agora são compilados com o crossgen 2, para todos os sistemas e arquiteturas disponíveis. Esta mudança traz principalmente uma economia no tamanho dos pacotes gerados.

```
Size [MB] FullName
--------- --------
64.22     C:Program FilesdotnetsharedMicrosoft.NETCore.App5.0.3
63.31     C:Program FilesdotnetsharedMicrosoft.NETCore.App6.0.0-preview.1.21102.12
63.00     C:Program FilesdotnetsharedMicrosoft.NETCore.App6.0.0-preview.2.21118.6
```

## ASP.NET

### Compilador Razor agora usa o Source Generators

O compilador do Razor foi alterado para fazer uso dos Source Generator, recurso novo do C# que inspeciona o código durante a compilação e pode gerar arquivos adicionais que serão compilados juntos ao projeto. Isto otimiza a forma com que o Razor é compilado.

![Tempo de compilação](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2021/03/razor-build-perf-improvements.png)

### Suporte para argumentos customizados no Blazor

Agora podemos passar argumentos customizados para eventos no Blazor.

```csharp
[EventHandler("oncustompaste", typeof(CustomPasteEventArgs), enableStopPropagation: true, enablePreventDefault: true)]
public static class EventHandlers
{
    // This static class doesn't need to contain any members. It's just a place where we can put
    // [EventHandler] attributes to configure event types on the Razor compiler. This affects the
    // compiler output as well as code completions in the editor.
}

public class CustomPasteEventArgs : EventArgs
{
    // Data for these properties will be supplied by custom JavaScript logic
    public DateTime EventTimestamp { get; set; }
    public string PastedData { get; set; }
}
```

### CSS Isolation

Agora podemos criar arquivos `*.cshtml.css` contendo nossos CSS específicos para aquele componente. Este será incluso no `bundle` da sua aplicação automaticamente.

*Index.cshtml.css* 
```css
h1 {
    color: red;
}
```

*_Layout.cshtml* 
```html
<link rel="stylesheet" href="MyWebApp.styles.css" />
```

*Resultado* 
![CSS Isolation](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2021/03/mvc-css-isolation.png)

### Mantendo estado dos Apps Blazor

Os Apps Blazor podem ser prerenderizados do servidor para melhorar sua performance e carregamento. O HTML prerenderizado pode ser imediatamente invocado no startup em background. Porém todo estado do App usado durante o prerender foi perdido e vai ser recriado quando o App for carregado completamente.

Para resolver este problema, foi adicionado um novo TagHelper, chamado `<preserve-component-state />`.

## Fontes
* [Blog oficial do .NET](https://devblogs.microsoft.com/dotnet/announcing-net-6-preview-2/)
* [GitHub do MAUI](https://github.com/dotnet/net6-mobile-samples)
