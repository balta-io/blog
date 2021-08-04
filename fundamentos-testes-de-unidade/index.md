Ter uma base sólida em testes de unidade é fundamental para que você possa se mover por diversas tecnologias e linguagens, e neste artigo, vou colocar os principais fundamentos que você precisa para ser fera em testes.

Código Testável
---------------

Seja programação orientada à objetos ou programação funcional, escrever um bom código é a base para execução de testes com facilidade.  
  
O uso de SOLID/Clean Code na OOP ou Pure Functions/outras boas práticas com JS é indispensável para criarmos um ambiente propício a testes.  
  
Bons códigos são testados mais facilmente, tomam menos tempo, dão menos trabalho, logo são muito mais simples de serem executados.  
  
Se você quer chegar a uma ótima cobertura de testes, passe primeiro pela base da linguagem que você utiliza, por padrões, boas práticas e um código limpo.

Saiba o que testar
------------------

Se você tentar testar tudo em sua aplicação, talvez você desanime e abandone os testes. Eu digo isto porque eventualmente vejo testes sobre entidades anêmicas ou validações simples como se um nome aceita X caracteres.  
  
Para intuito de aprendizado eu acho excelente, quanto mais exercitar os testes, melhor. Mas se não está sobrando tempo, foque em testar o que realmente importa, como os cálculos da aplicação, validações de fluxos mais complexos e por aí vai.  
  
Se sobrar tempo, testa o resto! Quanto mais testes, mais coberto você está, mas saber de fato o que precisa ser testado é importante.  
  
Embora essa seja uma decisão mais de negócio, é quase que intuitivo que nossos testes sejam sobre as entidades do nosso domínio, afinal, as regras ficam lá.  
  
Apenas atente-se para não trazer entidade irrelevantes, que poderiam ser apenas CRUD para seu domínio.

Compartilhamento e Reuso de Código
----------------------------------

Quantas vezes você testou se um E-mail era válido? Se um CPF era válido? Pois é!  
  
No .NET temos o NuGet e no JS o NPM, que são ótimos gerenciadores de pacote e você pode ter um para sua empresa, privado.  
  
Sempre que falamos em Primitive Obsession e Value Objects, falamos em compartilhar exatamente este tipo de código. Quantos VOs de E-mail, CPF e afins você já criou, e quantos você compartilhou?  
  
Compartilhamento e reuso de código afetam diretamente os testes de unidade. Quanto mais você reusa, menos testes necessita.  
  
Sigo este princípio com o Flunt, onde temos um pacote de validações já testadas. Desta forma, não precisamos executar os testes simples, pois já são garantidos pelo framework.  
  
O mesmo acontece com EF/NHibernate. Não testamos seus Contexts ou nada deles, pois eles já garantem que isto está funcionando.  
  
Pense desta forma, crie, teste, distribua, e seus testes cairão pela metade!

Red, Green, Refactor
--------------------

Sabendo o que temos que testar, escreva todos os testes e faça os falhar. Você vai perceber que enquanto escreve o título dos testes, virão novas ideias, novos testes a serem escritos.  
  
Com uma certa frequência, enquanto estou desenvolvendo o domínio, me vem alguns testes na cabeça, e sempre anoto eles nos testes de unidade.  
  
Minha recomendação é que sempre que lembrar de algo que precisa ser testado, vá até os testes daquela entidade, anote o título do teste e faça-o falhar.  
  
Ao término da escrita do domínio e dos testes, você terá uma gama de testes, todos vermelhos, e a ideia do próximo passo, é fazê-los passarem com o mínimo de esforço possível.  
  
Deixe tudo verde, tudo passando, e vai notar que há uma certa bagunça em seu código. Isto deve-se ao fato que escrever testes também requer uma estruturação do código.  
  
Se simplesmente deixarmos o código acumular, uma hora pagaremos por isto, pois tal bagunça pode influenciar nos testes.  
  
Passamos então para o Refactor, onde vamos refatorar os testes, de forma a reutilizar a massa de dados em diversos testes de unidade, criar repositórios falsos e por aí vai.  
  
Esta é uma das técnicas que mais gosto na escrita de testes de unidade. Ela nos dá uma visão do que precisamos testar e a obrigação de criar testes bem escritos.

Cultura de Testes
-----------------

Já comentei sobre cultura de testes em artigos aqui no balta.io e reforço a importância dela aqui.  
  
Ter uma visão única da importância dos testes, como um todo na empresa, é fundamental para ter códigos testáveis.  
Sinceramente, não adianta a equipe ter os melhores Devs, escrever os melhores testes, se na hora que o prazo aperta, os testes são deixados de lado. A cultura vem para evitar isto.

Resumo
------

Escreva códigos testáveis, saiba bem o que testar, reuse e distribua seu código testado para otimizar seus testes de unidade.  
  
Utilize técnicas como o Red, Green, Refactor e principalmente semeie a cultura de testes na sua empresa.