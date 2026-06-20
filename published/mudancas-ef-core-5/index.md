Mudanças no EF Core 5 estão a caminho, mas o que de fato devemos esperar delas? Será que as preces da comunidade serão ouvidas?

O que é um ORM?
---------------

Acho que o primeiro item que devemos pontuar antes de prosseguir é o que de fato é um ORM. ORM é a sigla para Object/Relational Mapping ou mapeamento objeto/relacional.

Sua função é mapear o resultado que recebemos de um banco de dados relacional para um objeto. Note que eu disse OBJETO e não entidade, ou seja, pode ser um DTO, uma entidade anêmica, qualquer coisa.

Caso queira saber mais sobre Modelagem de Domínios Ricos, acesse meu curso GRATUITO sobre o assunto [clicando aqui](https://balta.io/cursos/modelando-dominios-ricos).

Criação do EF Core
------------------

Na versão 1.x do .NET Core muita coisa foi prometida, mas como as mudanças eram muitas, confesso que minha expectativa não estava lá em cima, pois eu sabia que o .NET Core ainda não estava maduro e o EF Core muito menos.

Porém, o item que mais me deixou desapontado foi o mapeamento NxN, que antes dado por Navigation Properties agora exigia uma classe exclusiva para isto.

Em termos de modelagem, isto é muito ruim. Sabemos que não conseguimos ter o cenário perfeito de OOP usando um ORM, mas afetar desta forma a modelagem chega a doer na alma.

Na versão 2.x vieram várias melhorias de performance, query, e na 3.x isto continuou, mas nada do NxN mudar, pior, o time do EF disse publicamente que ele estava pronto!

Proposta do EF Core
-------------------

Foi então que decidi parar de brigar e assumir que a proposta do EF Core é mais Data Driven do que Domain Driven. Olhando o cenário NxN podemos ver claramente uma alusão aos relacionamentos de bancos de dados e não a OOP.

Encarar desta forma facilitou encaixar o uso do EF Core no dia-a-dia, ou seja, em cenário Data Driven ele brilha, em cenários focados em domínios ricos, ou usamos outra coisa, ou desmanchamos nossas entidades.

Se quiser saber mais sobre a criação de APIs Data Driven com ASP.NET Core 3 e EF Core 3, acesse meu curso [clicando aqui](https://balta.io/cursos/criando-apis-data-driven-com-aspnet-core-3-e-ef-core-3).

Time não ouvia a comunidade
---------------------------

Outro ponto que me incomodava bastante é que muitas das features que vou comentar abaixo estão como “sugestão” no GitHub do EF Core há tempos, mas vinham sendo ignoradas pelo time.

Não estou julgando pois não se internamente como acontecem as coisas lá, mas sei que a implementação destas features na nova versão mostra que o time está se posicionando diferente, ouvindo mais a comunidade.

Mudanças no EF Core 5
---------------------

A primeira mudança é na versão, pois não teremos o EF Core 4.x, pularemos para o EF Core 5 para acompanhar o .NET Core 5.

Embora a mudança seja grande, não haverá muitas breaking changes aparentemente e vale salientar que estas informações ainda são BEM RECENTES e muita coisa pode mudar.

Sem mais delongas, vamos as 6 mudanças mais aguardadas no Entity Framework Core 5.

### Mudanças no EF Core 5: Many-to-Many

Eu sei que deveria deixar esta feature para o fim, mas não estou aguentando de felicidade. Finalmente teremos uma mudança no modelo de mapeamento NxN, com a volta dos Navigation Properties.

O modelo atual ainda será suportado, porém teremos a alternativa de fazer como antigamente, apenas tendo duas listas uma em cada entidade, sendo gerada uma terceira tabela apenas a nível de banco de dados.

### Mudanças no EF Core 5: Table per Type

Outra mudança legal é a possibilidade de mapear classes abstratas (Tipos) como tabela. Esta feature permite que trabalhemos com herança de forma melhor no EF caso seja necessário separar apenas o tipo em uma tabela a parte.

### Mudanças no EF Core 5: Query nos Includes

Fazer consultas quando há Include envolvido é sempre um desafio. Muitas vezes partimos para o LINQ ou mesmo para o Dapper ou ADO, mas agora isto também será melhorado.

Será incluso o ThenInclude (Nome temporário) para que possamos aninhar Includes e também a possibilidade de consultas dentro do próprio Include.

### Mudanças no EF Core 5: Migration fora do Startup

Para os fãs de DevOps, os Migrations serão desacoplados da aplicação, tornando fácil executar a migração.

Hoje normalmente o Migration é feito no Startup do App e traz muitos problemas, incluindo a necessidade de um novo deploy para mudanças na migração.

Com as Migrations fora do App, podemos incluí-las no processo de deployment, como deve ser de fato.

### Mudanças no EF Core 5: Plataform Experience

Outro tópico comentado é sobre melhorias no uso do EF com Xamarin, Blazor, WinForms/WPF, mas este tópico vou ficar devendo pois não tenho conhecimento para falar sobre.

Abaixo deixo o link para o artigo oficial, você pode obter mais informações lá!

### Mudanças no EF Core 5: Melhorias nas queries/Performance

Além dos tópicos que mencionei, teremos melhorias nas queries e performance, assim como o time vem fazendo nas versões anteriores.

Não sei o que esperar, mas acho que não será algo muito grande, serão pontos específicos e vamos melhorando o framework aos poucos!

Fonte
-----

[Plan for Entity Framework Core 5.0](https://docs.microsoft.com/en-us/ef/core/what-is-new/ef-core-5.0/plan)

<div role="main" id="blog-s1-dotnet-134e3db1eea6c9829db1"></div>
<script type="text/javascript" src="https://d335luupugsy2.cloudfront.net/js/rdstation-forms/stable/rdstation-forms.min.js"></script>
<script type="text/javascript"> new RDStationForms('blog-s1-dotnet-134e3db1eea6c9829db1', 'UA-48664517-12').createForm();</script>