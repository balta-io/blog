O Memory Cache é utilizado para otimizar a performance das nossas aplicações, evitando requisições desnecessarias a fontes de dados.

## Cache

É comum termos diversos dados das nossas aplicações que não mudam com frequência. Itens como **estado civil**, **unidade de medida** e até mesmo algumas **categorias**.

Tudo que tem baixa mutabilidade, pode ser cacheado, evitando que a requisição precise chegar até a fonte de dados.

O que o cache faz é agir entre a aplicação e nossa fonte de dados. Dada uma requisição, podemos cachea-la em algum local e posteriormente, toda vez que esta mesma requisição for chamada, lemos estes dados ao invés de consultar o banco por exemplo.

O cache pode ser feito em diversos lugares, incluindo **memória**, como veremos aqui. O cache possui um tempo de expiração, dado este tempo, o mesmo é renovado.

Essa renovação significa que a próxima requisição irá novamente até a fonte de dados e o processo de cacheamento se reiniciará.

Quando usado com sabedoria, o cache pode otimizar **MUITO** a performance das aplicações. Imagina quantos _hits_ no banco podem ser evitados com um bom cache?

> Em cenários de larga escala, é comum cachear itens por algumas horas ou até minutos apenas. O importante é reduzir a carga no banco.

Então, não tenha medo do cache, se não precisa de informações em tempo real, talvez ela possa ser cacheada de alguma forma.

## Memory Cache

Um dos caches mais comuns é o **Memory Cache**, onde armazenamos as informações na memória da servidor.

Para realizar esta ação, temos uma **chave** e um **tempo de expiração** para o cache, seguido do seu valor.

Feito isto, podemos verificar se os dados requisitados pelo usuário já estão na memória e devolvê-los mais rápido.

Caso não haja um cache com a chave informada, recorremos ao processo normal de acesso ao banco, também chamado de **fallback**.

O Memory Cache tem dois pontos extremamente importantes que você precisa levar em conta antes de implementar, a **memória** e a **escalabilidade**.

Como o nome diz, estamos colocando os dados em memória, e eles permanecerão lá até expirar. Posteriormente, serão renovados, ocupando novamente a memória.

Ou seja, quanto mais você cacheia, mais memória está em uso. O ASP.NET fica responsável por gerenciar a memória e desalocar caso necessário.

> Desalocar neste caso significa remover as informações cacheadas para priorizar a execução da aplicação.

Devemos ter cautela para não sobrecarregar a memória do servidor cacheando itens desnecessários.

O segundo ponto é referente a escalabilidade horizontal da aplicação, que se remete a quantidade de servidores ou contêiners que nosso app possui.

É comum termos aplicações com escalonamento automático, ou seja, atingiu X% de CPU ou memória, uma nova máquina ou contêiner será provisionado automaticamente.

Sempre que uma máquina ou contêiner é provisionado, ele possui apenas o essencial, a memória do servidor anterior não é replicada.

Isto significa que tudo que está cacheado no servidor A, fica apenas no servidor A, não se estende para o B, C, D entre outros que virão.

Para estes novos servidores, o processo de geração do cache será realizado, assim como feito no servidor A.

Caso precise de um cache compartilhado entre diversas máquinas ou contêineres, você pode optar por um **cache compartilhado** como o **Redis** por exemplo.

Porém, tenha em mente que agregando serviços, agregamos também complexidade e custo a nossa infraestrutura.

Em poucas palavras, utilize estes recursos para ampliar seu leque de possibilidades. O básico muitas vezes funciona bem e a um preço talvez menor.

## ASP.NET cache de memória

No ASP.NET, possuimos um Middleware nativo do Framework para cache de dados em memória.

Como todo Middleware, ele deve ser configurado no <code>ConfigureServices</code>, que fica no <code>Startup.cs</code>.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMemoryCache();
}
```

Esta é toda configuração que precisamos para utilizar o Memory Cache, e deste ponto em diante um serviço chamado <code>IMemoryCache</code> estará disponível para nós.

## Utilizando o cache

O primeiro passo para trabalhar com cache é recuperar uma instância do serviço <code>IMemoryCache</code> e isto pode ser feito utilizando DI.

```csharp
public IActionResult Index([FromServices]IMemoryCache cache)
{
    ...
}
```

Feito isto, temos disponível na variável **cache** que criamos, os métodos <code>GetOrCreate</code> e <code>GetOrCreateAsync</code> para obter ou criar um novo item no cache.

> Utilize o <code>GetOrCreateAsync</code> caso esteja em um método do tipo **Task**.

Vamos então implementar o código que fará a tratativa e tomara a decisão sobre qual fonte deve ser lida.

```csharp
public IActionResult Index([FromServices]IMemoryCache cache)
{
    var date = cache.GetOrCreate("MyCacheKey", item =>
    {
        item.SlidingExpiration = TimeSpan.FromSeconds(10);
        return DateTime.Now;
    });

    return View("Cache", date);
}
```

A opção <code>SlidingExpiration</code> define quanto tempo o cache vai durar na memória. Temos também a opção <code>AbsoluteExpirationRelativeToNow</code> que define um tempo de expiração relativo a data atual.

> **Atualização em 2020/06/10** - SlidingExpiration obtém ou define por quanto tempo uma entrada de cache pode ficar inativa (por exemplo, não acessada) antes de ser removida. Isso não estenderá o tempo de vida da entrada além do término absoluto (se definido).

## Dados complexos

No exemplo anterior, utilizamos uma simples data e hora como retorno, porém, podemos retornar dados mais estruturados e até chamar uma função.

```csharp
    var products = cache.GetOrCreate("MyCacheKey", entry =>
   	    {
  	        entry.AbsoluteExpirationRelativeToNow = TimeSpan.FromHours(1);
	       return repository.GetProducts();
        });
    return products;
```

Neste caso, se um cache com a chave <code>MyCacheKey</code> não for encontrado, a função <code>GetProducts</code> do repositório, e os produtos serão cacheados.

## Alterando a prioridade

Como comentamos anteriormente, o cache possui uma prioridade, sendo os menos prioritários, mais propensos a serem excluidos caso haja necessidade de mais memória no servidor.

Esta prioridade é definida pelo método <code>SetPriority</code> utilizando o enumerador <code>CacheItemPriority</code>.

```csharp
    var products = cache.GetOrCreate("MyCacheKey", entry =>
   	    {
  	        entry.AbsoluteExpirationRelativeToNow = TimeSpan.FromHours(1);
            entry.SetPriority(CacheItemPriority.High);
	       return repository.GetProducts();
        });
    return products;
```

## Fontes

- [Documentação Oficial](https://docs.microsoft.com/pt-br/aspnet/core/performance/caching/memory?view=aspnetcore-3.1)
- [Macoratti](http://www.macoratti.net/19/06/aspc_cache1.htm)

<div role="main" id="blog-s1-dotnet-134e3db1eea6c9829db1"></div>
<script type="text/javascript" src="https://d335luupugsy2.cloudfront.net/js/rdstation-forms/stable/rdstation-forms.min.js"></script>
<script type="text/javascript"> new RDStationForms('blog-s1-dotnet-134e3db1eea6c9829db1', 'UA-48664517-12').createForm();</script>