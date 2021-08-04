Sinceramente, achei que este já era um assunto esgotado, mas vi que ainda tem margem para boas discussões e entendimentos.  
Então, lá vamos nós de novo!

One size doesn\`t fits all
--------------------------

Antes de começar a ler este artigo, quero que você tenha algo muito importante em mente, talvez a coisa MAIS IMPORTANTE que você deve aprender como desenvolvedor.

**ONE SIZE DOESN\`T FITS ALL**

Esta é uma frase usada no meio dos arquitetos de software cujo a tradução significa 'Um tamanho não se encaixa em tudo', ou como costumamos falar aqui no Brasil 'Arquitetura não tem receita de bolo' ou simplesmente 'Arquitetura não tem bala de prata'.  
  
Mas por que isto é importante? Porque tudo no mundo da tecnologia nasce com um propósito e cada item destes tem seu valor e seu lugar certo para ser utilizado.  
  
Não resolvemos tudo com SetState, muito menos com BLoC e menos ainda com MobX, cada App é um caso à parte, que exige um estudo, tecnologia, implementação específica.  
  
Se você resolve tudo de uma forma só, ou está SUBESTIMANDO o projeto ou SUPERESTIMANDO. Talvez em alguns casos seja até assertivo, mas é impossível atender todos de uma forma viável.  
  
Cabe ao desenvolvedor saber diferentes formas, implementações, arquiteturas, para dado o problema, aí sim prover a solução.

SetState
--------

Vamos começar do mais básico e se você pulou isto, já começou errado na minha opinião. É importante saber o que é o SetState, como utilizá-lo, pois, ele é a base da mudança de estado no Flutter.  
  
Como um desenvolvedor pode aprender BLoC, MobX, Gestão de estado, sem saber de fato o que acontece no SetState, ou por que tem alternativas a ele?  
  
Nosso primeiro problema, é simples, temos um App que faz validação de CPF, apenas isto.  
  
Você tem um controle na tela, precisa recuperar o valor deste controle e em seguida apresentar se o CPF é válido ou não.

**PERGUNTA DE ENTREVISTA:**  
Como você resolveria este problema?

Sinceramente, se eu estivesse te entrevistando neste momento, e para um App simples deste você me apresentasse uma estrutura com BLoC ou MobX, você estaria reprovado.  
  
Quando falamos em solucionar problemas, falamos em fazer isto da melhor forma possível, no tempo que temos.  
  
Se você adiciona complexidade no projeto, você adiciona risco ao projeto, exige mais conhecimento do time, toma mais tempo e por aí vai.  
  
Muitas vezes o simples é o melhor que podemos ter!  
  
Mas e a arquitetura? Que arquitetura? Olha a simplicidade do App, não precisamos de uma arquitetura para isto.  
  
Outros exemplos bem simples são as aplicações que já desenvolvemos nos cursos de Flutter aqui do site, como a de Álcool ou Gasolina, Perfect Eggs, Todo List.  
  
São Apps simples, que precisam de soluções simples! Ponto!  
  
Dados estes FATOS, você ainda acha que o SetState é ruim? Que não precisa estudar ele? Que pode ir direto para o MobX ou qualquer coisa que seja?

BLOC
----

O mais incompreendido de todos, simplesmente pelo uso ser estritamente ligado a uma implementação, o que é errado.  
  
Antes de falar de BLOC temos que falar sobre padrões. Um padrão é uma espécie de especificação, de sugestão a ser seguida, ele não tem a ver com código necessariamente, o que significa que você pode implementar um padrão de diversas formas e está tudo bem.  
  
Desta forma, um padrão nunca vai ser “limitado” ou “ruim”, pois ele depende do problema e não ao contrário.  
  
BLOC pode não te atender em um cenário, mas te atender em outro, assim como todos os padrões que conhecemos. Não usamos MVC em tudo, não usamos BLOC em tudo, não usamos MobX em tudo. Dado o problema, damos a solução.  
  
Pois bem, o BLOC é um padrão “novo” com base em coisas antigas e até óbvias. O nome significa Business Logic Object Component, que traduzido fica Objeto/Component de Lógica de Negócio.  
  
A premissa dele é separar a lógica de negócio da interface do usuário, algo extremamente óbvio já que podemos ter duas interfaces distintas no Flutter, uma para Android outra para iOS (Fora Desktop e Web que estão chegando).  
  
Desta forma, reescrever um código em toda tela seria suicídio, concorda? É aí que o BLOC entra em ação. Se você tem uma regra, um cálculo, algo que tenha que ser executado em um CLICK de um botão e você tem mais de uma interface, você simplesmente coloca este código em uma terceira classe e compartilha eles com as telas.  
  
Se você vai fazer isto com ChangeNotifier, com Streams, com MobX, não importa, estamos falando de padrão, de conceito e não de implementação. Inclusive, amanhã pode mudar a estrutura de atualização de componentes do Flutter e mudar a implementação dos BLOCs, mas o conceito NUNCA vai mudar.  
  
Em resumo, sempre que você separa sua regra da tela você tem um BLOC. Então, se você trabalha com MVC e nos seus Controllers você tem a regra, separando-a das Views, você tem um BLOC, se você trabalha com MobX e deixa as regras nas Actions, você tem um BLOC.  
  
Claro que nos casos de MVC, MobX, é mais simples usarmos os nomes Controllers e Stores/Actions respectivamente, para facilitar o entendimento dos padrões, mas se analisarmos bem, temos uma “forma” de BLOC dentro deles.  
  
**Mas se o BLOC resolve tudo isto, por que movemos dele?**  
  
Eu sempre aconselho meus alunos a aprenderem conceitos, pois eles são atemporais, enquanto implementações mudam com o tempo.  
  
Quando começamos as implementações com o BLOC puro, precisamos notificar as telas sobre as mudanças, e embora isto possa ocorrer pelo SetState, ele só funciona para árvore de componentes abaixo dele, ou seja, se você precisar atualizar um componente que está no seu NavBar, o SetState não vai te atender.  
  
Passamos então a olhar para o ChangeNotifier, mas logo vimos que ele funciona bem para um número limitados de notificações/observadores, que no caso são apenas 2 (Isto mesmo, dois...). No caso de Apps maiores, como muitas atualizações de telas ele não é recomendado.  
  
O que achamos conveniente no caso, foi o uso de Streams, que são fluxos de dados, onde os Widgets podem ficar conectados a estes Streams e serem atualizados “automagicamente” sem o uso do SetState.  
  
Porém, os Stream stem um perigo terrível, que é deixar o Stream aberto, ou seja, se você se conecta a um Stream na hora que o Widget é montado e esquece de dar um dispose neste Stream depois, ele fica aberto, consumindo memória.  
  
Em adicional, os Streams no Flutter precisam de controladores para funcionar, o que gera uma quantidade significativa de código, que me incomodava bastante.  
  
Neste cenário, o MobX chegou detonando tudo. Embora ele gere arquivos adicionais por baixo dos panos, ainda assim nosso código fica mais limpo.

Estado Global VS Estado Local
-----------------------------

Antes de falar de MobX precisamos entender sobre gestão de estado, e temos dois cenários aqui, o estado global da aplicação, que é aquele que está presente em todas as páginas (Ou pelo menos disponível para todas elas) e o local, que é o estado de uma página ou componente em si.  
  
O estado global pode ocorrer quando por exemplo, queremos autenticar um usuário, receber seu nome, E-mail, imagem, token e deixar estas informações disponíveis globalmente, ou seja, para todas as páginas.  
  
Imagina que exibimos algum dado do usuário no cabeçalho de alguma página ou mesmo no rodapé. Esta informação TEM QUE estar disponível, mas foi capturada lá no começo, na tela de login por exemplo.  
  
O que muita gente esquece é que ter um estado global não significa usar MobX por exemplo, eu posso ter meu estado em variáveis globais.  
  
Imagina que temos um App onde o usuário é OBRIGADO a se autenticar. Depois de autenticado ele é direcionado para Home e segue o jogo. Além disso, depois de autenticado, seus dados não mudam, se mudarem, ele precisa fazer Logout e Login novamente.

**PERGUNTA DE ENTREVISTA:**  
Qual a forma mais simples de resolver este cenário?

Se você colocou MobX, Provider, qualquer coisa para gestão de estado, ERROU! A maneira mais simples neste caso é deixar seu usuário em uma variável global, disponível para todo App.  
  
Se você não precisa de reação nas mudanças, se não precisa atualizar Widgets nem nada, não precisa de MobX ou Provider para ter seu estado global.  
  
O mesmo ocorre caso o local onde você precise utilizar as variáveis globais esteja “alcançável” pelo SetState. Imagina que você quer adicionar itens ao carrinho, mas só precisa exibir eles no carrinho de compras... Pode usar esta técnica também.  
  
O mesmo ocorre para estados locais, onde você tem como exemplo um formulário que faz a submissão dos dados.  
  
Para estes casos eu também não uso MobX ou estado global (Nem faz sentido), eu tenho apenas um formulário na tela e se precisar de alguma atualização, eu utilizo um SetState e pronto. Simples e direto.  
  
Entenda que ESTADO é a MEMÓRIA do dispositivo, e se você fica carregando tudo na memória, já sabe né.  
  
Recentemente vi vários códigos, talvez exemplos, de CRUDs usando MobX para tudo, em todas as telas, pior ainda, com Provider.  
  
Imagina você carregar todo (Ou boa parte) do seu catálogo de produtos para a memória do dispositivo? Para mim é suicídio!

O que é o MobX?
---------------

Bom, agora que já falamos sobre SetState, BLOC e estados, vamos falar sobre o MobX, que funciona com uma tríade de ações, reações e observáveis.  
  
O MobX é um pacote que implementa a gestão de estado no Flutter de uma forma simples. Na verdade, ele escreve o código para nós (Copie a explicação do Gabriel Fróes do CDFTV) por baixo dos panos.  
  
Basicamente, definimos um Store (Estado) que pode ser gerenciado por Actions (Ações) e observado nos Widgets por Observers (Observadores).  
  
Podemos tornar este Store global através do Provider por exemplo, e consumi-lo em diversos Widgets da aplicação.  
  
A grande vantagem dele sobre as outras implementações para gestão de estado é que ele faz uso de anotações, tornando nosso código muito mais limpo, simples e fácil de visualizar.  
  
O uso do MobX parece mágica comparado a mesma implementação utilizando BLOC com Streams por exemplo.

Devo usar MobX em todas as telas?
---------------------------------

Então bora usar MobX em tudo!!!! NÃO!!!! Quem só conhece prego só dá martelada, já ouviu?  
  
Pois é, o MobX é incrível, tem um ótimo suporte da comunidade e dos criadores do pacote, mas não podemos definir o que vamos usar antes de ter o problema que precisamos resolver.  
  
Como comentei em quase todo artigo, DADO O PROBLEMA fornecemos uma solução e deste modo, o MobX não resolve tudo, assim como o BLoC.

O que eu aprendo então?
-----------------------

Aprender a desenvolver Apps é diferente de aprender Backend ou Web Apps. Os Apps tem diferentes formas e necessidades e não existe um padrão, uma receita de bolo para seu App.  
  
Você pode precisar desenvolver um eCommerce amanhã, algo que demande uma melhor gestão de estado global, e optar por usar MobX.  
  
Você pode em seguida, precisar desenvolver um App que é apenas um catálogo de produtos, ou um App mais conceitual como chamamos, que não necessita se quer de BLOC.  
  
O que você precisa, com certeza é APRENDER diversas formas diferentes de fazer as coisas, para não ficar preso em uma só, para não saber como resolver tudo do mesmo jeito.  
  
Quanto mais soluções você tiver em seu leque, mais especialista você se torna, melhor você resolver os problemas, mais rápido você entrega.

Qual a minha recomendação?
--------------------------

Depende do problema, se você me chamar para desenvolver um App agora, muito antes de falar sobre tecnologia, vamos falar sobre negócio. Talvez nem em Flutter seja.

Cadê o código?
--------------

Primeiramente, se você leu este artigo até aqui, parabéns! É um artigo longo, extenso, e creio que poucos vão ler até o fim.  
  
Mas garanto que você aprendeu muita coisa aqui hoje, e tudo sem ver código, correto? Este é o ponto chave aqui, estamos falando de conceitos, a implementação cada um pode ter a sua.  
  
Os conceitos são a parte mais importante, pois como comentei anteriormente, são ATEMPORAIS, ou seja, não mudam com o tempo.  
  
Linguagens, tecnologias, implementações, cada hora tem uma nova, mas os conceitos, os fundamentos, isto nunca muda.  
Então se você tem uma base sólida, você está apto a ir para qualquer lugar!


<div role="main" id="blog-s1-flutter-15c15f0b7777b4675fad"></div>
<script type="text/javascript" src="https://d335luupugsy2.cloudfront.net/js/rdstation-forms/stable/rdstation-forms.min.js"></script>
<script type="text/javascript"> new RDStationForms('blog-s1-flutter-15c15f0b7777b4675fad', 'UA-48664517-12').createForm();</script>
