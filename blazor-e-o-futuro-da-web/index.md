Recentemente publiquei meu primeiro conteúdo sobre Blazor, um [vídeo onde mostro algumas funcionalidades básicas](https://www.youtube.com/watch?v=z_22O4eZLnk) sobre o assunto em menos de 15 minutos.

Este vídeo provocou vários comentários e perguntas nas minhas redes sociais. Desde pessoas odiando a ideia, até pessoas querendo já colocar em produção (Ainda está em preview pessoal).  
Este artigo é para falar sobre Blazor, WebAssembly e o que esta **alternativa** nos traz de benefícios.

JavaScript é ruim?
------------------

Todo mundo já fez alguma piadinha infame com o JavaScript, isto é fato, e outra realidade é que ele realmente não foi criado para ser o que é hoje. Em 1995 sua fundação era apenas para manipular alguns elementos na tela, e pronto.  
  
Porém, indiscutivelmente ele é a linguagem mais utilizada no mundo, e enquanto um Dev Java/C#/PHP conhece JS por tabela, ao contrário o mesmo não acontece. Fora que aprender JS, para quem está começando é bem menos **doloroso** do que linguagens tipadas.  
  
Pois bem, em meados de 2008, com a chegada do Chrome e do V8, uma engine criada pela Google para não apenas interpretar o JS, mas para transformá-lo em ByteCode, ter um Profiler e outros recursos que deram um **gás** no JS, tivemos nossa primeira grande mudança.  
  
O JS evoluiu tanto desta época para cá, que o NodeJs por um bom tempo era mais rápido que .NET, PHP e Java. Me lembro que em 2012 quando comecei com Node, ele suportava MUITO MAIS requisições que estas outras linguagens, e era tudo JS.  
  
Outra grande mudança que tivemos foram as SPAs, ao invés de processar tudo no servidor e trazer o HTML pronto, trazemos apenas os dados (JSON) e processamos mais coisas na tela.  
  
Processar mais coisas no cliente significa mais uso do JS, mais processador e mais memória. O que eu particularmente acho que tivemos uma boa evolução também. Hoje tem Smartphones com 8GB de ram já.  
  
Hoje falamos de PWAs, de aplicações com feeling nativo, de substituir Apps nativas por Apps Web, ou seja, ainda dá para dizer que JS é ruim?  
  
O JS de hoje não é o mesmo de antigamente, sua especificação é atualizada frequentemente, recursos novos são adicionados todo ano e temos até um superset incrível para ele, o TypeScript.  
  
Na minha humilde opinião, JS é a linguagem do presente e futuro sim, e é uma ótima linguagem mesmo com suas limitações.  

Por que WebAssembly?
--------------------

Mas se o JS é bom, por que precisamos do WebAssembly ou WASM? Bem, para responder está pergunta, precisamos antes entender o que é o WASM.  
  
O WASM é a compilação do nosso App, apenas isto. Sabemos que linguagens interpretadas tendem a ser piores que as compiladas, então por que não compilar nossas Apps Web e executá-las como fazemos com as nativas?  
  
Isto daria um ganho IMENSO de performance para aplicação, e uma redução significativa no tamanho dela, já que sairíamos de texto para binário.  
  
Fora que por ser um IL, podemos escrever em outras linguagens como C, C++, C# e Rusty (As que conheço que suportam WASM hoje). Ou seja, é o mesmo processo que tínhamos com .NET antigamente, onde escrevíamos em VB ou C# e o IL compilava para a linguagem de máquina.

Então não vou mais usar JS?
---------------------------

Em um futuro, pode ser que você possa optar mais tranquilamente por outra linguagem sem ser o JS, porém hoje ainda tem muita coisa escrita em JS, e algumas limitações atuais no WASM que te farão escrever JS hora ou outra.  
  
Outro ponto importante é que WASM não concorre com JS, podemos invocar métodos JS direto de aplicações escritas em outras linguagens podendo por exemplo ter ambas na mesma aplicação.  
  
Eu também acredito que a comunidade JS é uma das mais fortes que temos, e vai durar um bom tempo, fora que o próprio JS evolui, cada ano a linguagem melhora.

Concorrência gera coisa boa
---------------------------

Por que falei de Blazor, WASM se defendo tanto o JS? Porque eu acredito que a concorrência faz as coisas melhorarem.  
  
Nada como uma linguagem de peso como C# para dar aquele susto e fazer o JS evoluir para sua melhor versão.  
  
Fora isto, eu acredito que o mercado é muito grande para ser dominado por apenas uma linguagem, um Stack, então quanto mais opções, melhor!

E o Blazor?
-----------

O Blazor é o projeto que compila WASM da Microsoft, utilizando C# como linguagem e possuindo duas formas de ser utilizado, via Client/Server com SignalR e via WASM.  
  
O que estamos comparando aqui é sua versão WASM que ainda está em preview (Até a escrita deste artigo), e que me interessa mais.  
  
O Blazor nada mais do que a possibilidade de criar aplicações utilizando puramente C# para Web, mesmo assim tendo que invocar Backend via HTTP e tudo mais. O modelo da aplicação não muda, todas as limitações que temos no Client continuam.

Isto é o futuro?
----------------

WASM com certeza! Já faz um tempo que dizemos que WebAssembly é o futuro da Web e que JS viraria uma linguagem de baixo nível para ele. Isto é indiscutível.  
  
Agora em relação ao Blazor, tudo vai da adoção do mercado em relação a ele e do suporte WASM nos Browsers. Recentemente tivemos uma grande **vitória** com a adoção da Apple (Safari iOS -- [https://caniuse.com/#feat=wasm](https://caniuse.com/#feat=wasm) ) dos WASM, mas ainda é cedo para dizer algo sobre.  
  
Eu gravei um [vídeo falando o que poderia acontecer de ruim com o Blazor](https://www.youtube.com/watch?v=Fv9vS3QsbRE). Embora seja um vídeo antigo e algumas coisas já tenham melhorado, ainda tem pontos de vista bem válidos nele.

### Fonte:

*   [https://medium.com/hackernoon/webassembly-the-journey-jit-compilers-dfa4081a6ffb](https://medium.com/hackernoon/webassembly-the-journey-jit-compilers-dfa4081a6ffb)

<div role="main" id="blog-s1-dotnet-134e3db1eea6c9829db1"></div>
<script type="text/javascript" src="https://d335luupugsy2.cloudfront.net/js/rdstation-forms/stable/rdstation-forms.min.js"></script>
<script type="text/javascript"> new RDStationForms('blog-s1-dotnet-134e3db1eea6c9829db1', 'UA-48664517-12').createForm();</script>