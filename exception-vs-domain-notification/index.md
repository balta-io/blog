Então você precisa validar suas entidades de domínio e tem duas opções. Talvez a mais clara seria utilizar Exceptions, mas será que ela é a melhor escolha?  
  
Neste artigo vamos abrir espaço para o eterno debate sobre Exceptions VS Domain Notifications e vou colocar alguns motivos nos quais utilizo Domain Notifications nas validações do meu domínio.

### O que é uma Exception?

Uma exceção oras, algo que não deveria acontecer mas aconteceu, algo quase que imprevisível.

### O que é um Domain Notification

Uma notificação, uma mensagem sobre algum acontecimento no seu domínio.

### O que acontece quando temos uma Exception?

Sua execução é parada e o evento ocorrendo no momento é gravado no Event Viewer (Caso do IIS).  
  
É gerado um alarde maior, como se fosse um erro na sua aplicação e temos um custo maior, afinal logamos tudo nos eventos do host.

### Devo parar de usar Exceptions?

Não! As exceções tem seu uso. Por exemplo, se você não consegue se conectar ao banco de dados, adianta prosseguir com o request? Este é um belo exemplo em que devemos utiliza-las.

### Devo utiliza-las no Domínio?

Depende! Em tese eu não vejo um cenário onde por exemplo teria uma conexão ao banco no meu domínio.  
  
Juntando isto com o fato da interrupção da execução e do log dos eventos, acaba complicando seu uso.  
  
Imagine um cenário onde você estoura uma exceção no cadastro de clientes, no momento em que valida seu documento por exemplo (Afinal, não podemos salvar um cliente com documento inválido).  
  
Em primeiro lugar, se esta fosse a primeira das suas N validações, seu fluxo seria interrompido aí, acarretando no retorno pra tela, o que incomodaria bastante o usuário, pois fica aquela validação picada. Corrige documento, nome errado corrige nome, data de nascimento errado, e por aí vai.  
  
Em adicional, esta exceção, como comentei anteriormente, consumiria mais recursos, pelo fato de logar nos eventos da máquina, no caso, mais recursos de disco.  
  
Agora imagina um cenário com milhões de cadastros? Ou um simples Brute Force ali, seria o bastante pra lotar a máquina.  
  
Desta forma, quase nunca utilizo Exceptions no meu domínio. Utilizo Notification Pattern, com Flunt.

[Repositório do Flunt](https://github.com/andrebaltieri/flunt)

### Fail Fast Validations

Outra abordagem que utilizo são os Fail Fast Validations, onde faço uma validação inicial nos meus Commands, removendo possíveis validações bestas das minhas entidades e não sobrecarregando elas. Afinal, partimos do princípio que as informações devem ao menos chegar as entidades.

### Testes de Unidade

Trazer a regra de negócio para seu domínio tem um motivo: Testes de Unidade! Se você brigou tanto com o DBA pra sair das PROCS e não faz testes, não adianta nada.  
  
É possível testarmos por exeções (ExpectedException), mas elas são mais falhas, pois se você espera uma exceção do tipo ArgumentNullException no Name do Customer e ocorre uma outra exceção do tipo ArgumentNullException no Email do Customer, seu teste vai dar falso positivo.

### Curso 1975

Tem mais um mundo de coisas que eu poderia escrever aqui, mas falei tanto sobre isto que até transformei em um curso, o 1975 - Modelando Domínios Ricos, que está gratuito no balta.io.

[1975 - Modelando Domínios Ricos](https://balta.io/cursos/1975)

<div role="main" id="blog-s1-dotnet-134e3db1eea6c9829db1"></div>
<script type="text/javascript" src="https://d335luupugsy2.cloudfront.net/js/rdstation-forms/stable/rdstation-forms.min.js"></script>
<script type="text/javascript"> new RDStationForms('blog-s1-dotnet-134e3db1eea6c9829db1', 'UA-48664517-12').createForm();</script>