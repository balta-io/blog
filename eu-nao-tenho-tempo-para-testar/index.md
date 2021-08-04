Quantas vezes você ignorou os testes, quantas vezes começou um projeto com testes de unidade e abandonou no meio do caminho por falta de tempo?

Testar é polir sua aplicação, e ao mesmo tempo que isto toma tempo, é isto que difere seu produto dos concorrentes.

Pare de remover tempo das tarefas de testes
-------------------------------------------

É incrível! O tempo aperta e o primeiro item a ser descartado é o teste de unidade. A tarefa que foi estimada em 8 horas e agora atrasou, ao invés de cumprir o prometido e testar, usamos a desculpa “está atrasado” e deixamos os testes de lado.

Muitas funcionalidades, deixam para ser testadas no final da Sprint e a mesma história se repete. 40 horas para produzir uma funcionalidade, 1 hora para testá-la.

Seja sincero, você acha que uma entrega com o mínimo de testes possíveis, tem chances de dar certo? De passar tudo redondo?

Talvez, quando seu cliente recebe um bug, ele está tão acostumado com erros, que nem se manifesta mais. Não significa que ele está feliz e satisfeito, mas sim que já desistiu de brigar por algo melhor.

Não testar por estar atrasado é a mesma coisa que sair de casa com o tanque na reserva e não parar no posto de gasolina para “não perder tempo”.

Se você já atrasou, muitas vezes para entregar algo melhor, entregue ao menos algo testado. Se você sempre remover tempo dos testes **VOCÊ NUNCA TERÁ TEMPO PARA TESTAR**.

Esqueça os CRUDs
----------------

Já rodei muitas empresas fazendo consultoria, e muitas delas falando sobre DDD, domínios ricos e outras palavras bonitas.

Em diversos cenários, vi times implementando interfaces, modelando entidades, aplicando Design Patterns no cadastro de categoria, de estado civil, de unidade de produto. Estes itens não têm sequer regras de negócio!

Me lembro de chegar em um cliente e ele comentar que 90% do sistema dele era CRUD apenas, e realmente era. Sentamo-nos na frente do computador, escrevemos as entidades ANÊMICAS, geramos o banco e Scaffold da API. Pronto, 90% do sistema estava concluído, em menos de duas horas.

Se você não tem regras de negócio, não implemente complexidade. Se tem apenas validações simples, se são apenas entidades simples, seja simples com elas e economize este tempo.

Nesta simples decisão, 90% do projeto de seis meses estava pronto, em um dia, sobrando assim seis meses para o que realmente importava, a modelagem do domínio, com testes de unidade.

Talvez você esteja gastando tempo (e esforço) na parte errada do seu sistema, e por este motivo **VOCÊ NUNCA TERÁ TEMPO PARA TESTAR**.

SOLID, Clean Code
-----------------

Um código bom é um código testável! Eu sempre digo esta frase e sempre que crio algo eu penso em como vou testá-lo, se está fácil de entender.

SOLID e Clean Code são dois assuntos batidos pela comunidade, com muitos artigos sobre o assunto e até cursos aqui no balta.io (1975 – Modelando Domínios Ricos), mas será que são realmente utilizados no dia-a-dia?

Quanto mais tempo você “gasta” escrevendo um código utilizando estes padrões, mais fácil serão seus testes de unidade, mais simples serão de testar.

Se você não se prende a escrever um bom código, **VOCÊ NUNCA TERÁ TEMPO PARA TESTAR**.

A propósito, se tem um curso **MANDATÓRIO** para todo Dev, este é o **1975 - Modelando domínios ricos**, que está **GRATUITO** no balta.io.

[Curso 1975 - Modelando Domínios Ricos](https://balta.io/cursos/1975)

Regras de Negócio
-----------------

Quantas vezes você já ouviu que a regra de negócio deve permanecer na aplicação, mais especificamente no domínio? Mas por quê? Qual real motivo desta afirmação?

Com certeza, o negócio é o pilar da empresa, é o motivo pelo qual o software está sendo construído, e o domínio é o pilar do software, é o coração dele, então obviamente as regras ficam ali.

Parte está correta nesta afirmação, mas a meu ver, trazemos as regras para a aplicação pois a OOP é mais flexível que um MER, o C# é mais poderoso que o T-SQL e principalmente porque podemos testar unitariamente nossas entidades e regras.

Se você faz todo esforço para trazer as regras para seu domínio e não executa testes de unidade, você está perdendo a melhor parte desta mudança.

Dependências Externas
---------------------

Você provavelmente já ouviu dizer que os domínios não devem ter dependências externas, apenas do CLR, que deve ser o mais puro possível.

As dependências externas, além de trazer acoplamento ao nosso domínio, trazem também a dificuldade na hora de testar, e lembre-se, um bom código é um código testável.

Utilize sempre interfaces, dependa sempre de abstração e não de implementação e mantenha seu domínio sempre livre de dependências externas.

Se você precisa de informações que vem do banco de dados ou mesmo de serviços externos, deixe-as nas assinaturas dos métodos. Não é problema do domínio resolver isto, afinal, ele não precisa saber de onde vem a taxa XYZ, ele só precisa saber como utilizá-la no cálculo.

Códigos Testáveis
-----------------

Se você chegou até este ponto, significa que já tem insumo suficiente para escrever um código testável, utilizando SOLID, Clean Code e afins.

Mas o principal ponto na escrita de um código testável é pensar em como ele será testado, calcular o tempo de escrita e execução dos testes, além da criação do código.

Sei que muitas vezes deixamos de lado esta estimativa, mas ela é importante. Embora aplicando os conceitos anteriores os testes se tornem mais fáceis, ainda leva tempo escrever bons testes de unidade.

Então não deixe de lado a estimativa para escrever BONS testes de unidade, leve em conta o tempo necessário para testar, refatorar os testes e tudo mais.

[Curso 7182 - Refatorando para Testes de Unidade](https://balta.io/cursos/7182)

Cultura de Testes
-----------------

A diferença entre os testes serem uma **CULTURA** na sua empresa ou serem apenas TAREFAS é que a **CULTURA SEMPRE SERÁ LEVADA A SÉRIO**.

Cultura é aquilo que está no sangue, que é mantido não importa a situação. Um time que tem uma forte cultura de testes, entende sua importância e não deixa uma entrega sem seus testes de unidade, mesmo que o prazo esteja apertado, ou mesmo já tenha estourado.

Implementar uma cultura de testes também não é um trabalho fácil. Todos os envolvidos têm que estar cientes das vantagens e principalmente tem que acreditar que é algo que funciona. Se você não acredita que testes de unidade são importantes, você sempre terá uma desculpa para não os escrever.

Uma dica é começar testando o mais importante, algo que envolva financeiro por exemplo, que não pode ocorrer uma falha, um cálculo importante.

De fato, as vezes é complicado enxergar a necessidade dos testes e faço até uma analogia ao seguro automotivo. Enquanto você não tem seu carro roubado ou bate ele, parece que todo dinheiro investido no seguro não tem valor algum.

À medida que, ao modifica uma simples linha de código na sua aplicação, no contexto A e um teste no contexto B, que não tem nada a ver com a modificação falhar, todos se darão conta da importância de ter uma boa cobertura de testes.

Conclusão
---------

Quase tudo pode ser testado e os testes não tomam tempo, talvez você esteja focando em algo que toma mais tempo do que deveria.

Foque sempre no seu domínio, escreva códigos legíveis, testáveis e será muito fácil testá-los unitariamente.

Lidere uma cultura de testes no seu ambiente de trabalho, seja você a mudança que espera que aconteça.