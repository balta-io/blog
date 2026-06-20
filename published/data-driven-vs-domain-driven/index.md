Recentemente publiquei um vídeo, no canal do balta.io no YouTube, onde [criamos uma API com ASP.NET Core 3 e EF Core 3 em menos de 15 minutos](https://www.youtube.com/watch?v=but7jqjopKM).

Claro que é apenas uma demo, e ainda faltam recursos na API como versionamento, cache, compressão, documentação entre outros, mas se levarmos em conta que no fim temos um cadastro de produtos com categorias, em menos de 15 minutos, é algo de se pensar.

Data Driven
-----------

Em alguns vídeos sobre testes de unidade, eu comento que se você “perder tempo” testando a coisa errada, como por exemplo tamanho de campo e outros itens que não são regras de negócio, você terá BEM MENOS tempo para testar o que realmente importa.  
  
Isto é um fato, e algo que assombra muitos projetos que passo, onde o pessoal diz não ter tempo para testar, mas na verdade estão dedicando esforços no lugar errado.  
  
Se não tem regra de negócio, talvez a melhor escolha seja o modelo Data Driven, que nada mais é do que um “espelho” do banco de dados, ou seja, o famoso CRUD.  
  
Se não tem regras, não necessitamos de tantos testes, nem de tanto esforço no domínio. Não é interessante ficar implementando classes, herança, interfaces e várias outras coisas da OOP/SOLID/Clean Code em um cadastro de categoria, de unidade de medida, de estado civil por exemplo.  
  
Seja simples e objetivo no seu código, e se for partir para o modelo Data Driven, minha recomendação é a do vídeo no começo deste artigo, simplesmente criar os modelos, adicionar os Data Annotations e mandar bala! Gerar as migrações, publicar e pronto, API no ar!

Domain Driven
-------------

O famoso DDD, onde você vai utilizar tudo que aprendeu sobre OOP, SOLID, Clean Code, padrões e muito mais.  
  
O DDD funciona quando temos regras de negócio, quando temos domínios mais ricos. Aplicar ele em projetos onde você não tem estes requisitos é suicídio.  
  
Imagine um projeto onde você precisa executar cálculos de impostos por exemplo, algo que não pode errar. Com certeza é um cenário para você trabalhar bem no domínio, nas entidades e nos testes de unidade.  
  
Muita gente confunde o uso do DDD com tamanho do projeto. As vezes seu projeto pode ser grande, mas todo orientado a dados, sem regras complexas de negócio. Isto não justifica o uso do DDD.  
  
Então dada a complexidade do seu domínio, aí sim é definido o uso de DDD ou não.

Por que não os dois juntos?
---------------------------

Em diversos projetos que passo a primeira coisa que fazemos é analisar o contexto e domínio da aplicação, em boa parte dos cenários, mais de 50% da aplicação é apenas CRUD, ou sejam, apenas cadastro básicos.  
  
Em um dos últimos projetos que participei recentemente, no fim da análise, chegamos a conclusão que 72% da aplicação eram cadastros básicos, como os que citei, de categoria, unidade de medida e por aí vai, sem regras de negócio.  
  
O que fizemos? Em um dia e meio geramos todos os CRUDs via T4 Templates, já com as Views em um projeto separado. Ou seja, em um dia e meio tínhamos mais de 70% do projeto concluído.  
  
Com este andamento, ainda sobraram 3 meses de projeto, para aí sim o time focar em DDD, SOLID, Clean Code, criar testes de unidade, trabalhar com calma no que realmente importava.

Conclusão
---------

One size doesn\`t fits all (Um tamanho não serve para tudo), ou seja, assim como Data Driven não resolve tudo, DDD também não. E por que não utilizar o melhor dos dois? Fazemos isto com Dapper e EF por exemplo.  
  
A estratégia certa na criação do projeto é o que define o sucesso dele. Pensar fora da caixa, entender que não existe bala de prata, são coisas fundamentais para garantir entregas concisas e de valor.