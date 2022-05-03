Um breve resumo sobre os mecanismos internos que o Git usa para gerenciar as mudanças nos arquivos de um diretório.

Sumário

 1. [Introdução](#introducao)
 2. [Um novo repositório é um novo diretório](#novorepositorio)
 3. [O arquivo HEAD](#head)
 4. [O arquivo master](#master)
 5. [Inspecionando um commit](#commit)
 6. [Inspecionando a árvore](#arvore)
 7. [Encontrando o conteúdo do arquivo](#conteudo)
 8. [Conclusão](#conclusao)
 9. [Referências](#referencias)
 
<div id='introducao'></div> 

## Introdução

Tempos atrás, li o [e-Book oficial do Git](https://git-scm.com/book/en/v2) e fiquei especialmente interessado pelo capítulo 10 que fala sobre o funcionamento interno do sistema. Depois da leitura deste capítulo, fiquei impressionado como o Git se tornou uma ferramenta tão poderosa usando ideias extremamente simples. Nesse texto, vou compartilhar um pouco do que eu aprendi com este livro, especialmente com o capítulo Git Internals. 

O Git é uma ferramenta de gerenciamento de código fonte que faz isso de forma bem simples: Ele guarda a lista de pastas e arquivos que estão dentro do diretório monitorado e também guarda uma cópia do conteúdo destes arquivos. Para saber se algo mudou, ele compara o conteúdo da pasta monitorada com as cópias que ele fez. 

Para nos aprofundarmos um pouco mais nessa ideia, te convido a seguir um tutorial simples, onde criaremos, manipularemos e investigaremos um pequeno repositório Git.

<div id='novorepositorio'></div> 

## Um novo repositório é um novo diretório

O trecho abaixo mostra os comandos para criar uma pasta chamada git-demo, inicializar um repositório dentro dela, criar um arquivo de texto e realizar um commit. Quando o Git inicializa um repositório ele apenas cria um diretório oculto chamada .git e, dentro dele, adiciona pastas e arquivos que são usados para monitorar e gerenciar as mudanças ocorridas dentro da pasta git-demo.

```
mkdir git-demo
cd git-demo
git init
echo "Conteúdo do arquivo 1" > Arquivo1.txt
git add *
git commit -m "Criação do arquivo 1"
```

Depois de criar o repositório, entre na pasta .git (que foi criada dentro de git-demo) e dentro dela você verá os arquivos mostrados na imagem abaixo.

![image-1](https://user-images.githubusercontent.com/50338986/166343814-56ad80fe-4173-4d99-881e-7e6e15a1c841.png)

<div id='head'></div> 

## O arquivo HEAD

Algumas destas pastas e arquivos são muito importantes pra entender o funcionamento interno do Git e quero começar falando do arquivo HEAD. Esse arquivo funciona como a agulha em um tocador de discos de vinil. A agulha sempre aponta pra um único local. A música que será tocada depende da posição da agulha. Este arquivo faz o mesmo papel, ele aponta pra onde o Git está "olhando" no momento. Se você abrir esse arquivo em um editor de texto qualquer, verá o seguinte conteúdo.

```
ref: refs/heads/master
```

<div id='master'></div> 

## O arquivo master

O texto que consta dentro do arquivo HEAD aponta para o arquivo master dentro da pasta .git\refs\heads (vale a pena observar que master é o nome da minha branch, então, se a sua branch tiver outro nome, o seu arquivo HEAD será diferente do meu). Se você abrir a pasta .git\refs\heads, verá um arquivo chamado master lá dentro. 

![image-2](https://user-images.githubusercontent.com/50338986/166343832-e1e80595-78d9-40de-9ff7-5322886f39de.png)

Se você olhar o conteúdo do arquivo master (usando cat no Linux ou get-content no PowerShell do Windows) verá algo parecido com o que eu mostro abaixo. Esse número grande é o hash que identifica o commit que acabamos de fazer. Então podemos perceber que o arquivo HEAD aponta pro nome da branch, enquanto o arquivo master aponta pro último commit feito na branch.

```
cd .git\refs\heads
get-content master
18862b88e8bec5920f321d08a27dc32289566ba0
```

<div id='commit'></div> 

## Inspecionando um commit

Agora vamos usar o hash que está dentro do arquivo master pra achar o nosso commit. Os commits ficam dentro da pasta .git\objects. Na próxima imagem você pode ver o conteúdo da minha pasta objects. No meu caso, o hash do commit começa com 18, então eu sei que os dados do meu commit estão dentro da pasta 18. Dentro deste diretório, existe um arquivo cujo nome é um hash que se inicia com 86. Note que estes dois números correspondem ao terceiro e quarto dígitos do hash que está dentro do arquivo master. Logo, percebemos que o Git usou os dois primeiros dígitos do hash do commit pra nomear a pasta e usou o resto dos dígitos pra nomear o arquivo. 

![image-3](https://user-images.githubusercontent.com/50338986/166343851-65957b72-af83-4422-8e9e-ef84bdb994e7.png)

Usando novamente os comandos cat (no Linux) ou get-content (no PowerShell do Windows) passando o nome do arquivo como parâmetro, veremos um monte de caracteres sem sentido. Estes caracteres são o conteúdo do seu arquivo concatenado com um cabeçalho gerado pelo Git. Depois, todo esse conteúdo foi compactado, sendo que o resultado é algo parecido com o que eu mostro abaixo.  

```
get-content 862b88e8bec5920f321d08a27dc32289566ba

x☺µŽA
Â0►=÷§{↨KÒ4K♥"‚gñà
¶Iª¦Ñ$-èw<ˆïèÇ,ø♠¯s˜↓↔¼w↓*¶«∟♣Ö¡A&4r©–J™š¤bºi9¢!U)ÖÉJó‚Æ|
◄Niþ8JÎ®Ž¶³↕%‼¢Q
®ÃÓÿØ~L6¦ÑÞúGyqù:¶¥~∟ë♠¥\ô°a‚±b¡ËW¶ÿ+¶‡èh~Ï¯&Åûè¦¼ø☻KNYy0
```

Felizmente temos o comando [git cat-file](https://git-scm.com/docs/git-cat-file) pra ver o conteúdo deste arquivo. Este comando lê o arquivo, o descompacta e mostra o seu conteúdo sem os dados extras adicionados pelo Git. Observação: O parâmetro do comando cat-file deve ser aquele hash que está dentro do arquivo master.

```
git cat-file -p 18862b88e8bec5920f321d08a27dc32289566ba0

tree 0f6d603c61cca4599d4a590c8b166da9290f52c1
author Oséias da Silva Martins <usuario@dominio.com>
committer Oséias da Silva Martins <usuario@dominio.com>

Criação do arquivo 1
```

Este comando fornece várias informações interessantes e vou comentar cada uma delas (de baixo para cima):

- "Criação do arquivo 1" é a mensagem que eu coloquei no meu commit.
- "committer" e "author" correspondem aos dados registrados nas configurações user.name e user.email do seu Git (use [git config -l](https://git-scm.com/docs/git-config) para ver as suas próprias configurações).
- "tree" é a parte mais interessante, pois ela aponta para o arquivo que contém o nome dos arquivos e pastas que existem dentro do diretório git-demo. 

<div id='arvore'></div> 

## Inspecionando a árvore

O último trecho de código mostra um hash ao lado da palavra tree. Este hash, no meu caso, se inicia com 0f, então sei que o arquivo tree está dentro da pasta .git\objects\0f.

![image-4](https://user-images.githubusercontent.com/50338986/166343880-415338c5-b170-456e-a5e4-ed71ac6a5648.png)

Se passarmos o hash da tree para o comando git cat-file podemos ver o seu conteúdo. Perceba, no trecho abaixo, que o comando cat-file retornou quatro campos. O último campo é o nome do arquivo que criamos dentro da pasta git-demo, enquanto  o penúltimo campo é outro hash. 

```
git cat-file -p 0f6d603c61cca4599d4a590c8b166da9290f52c1

100644 blob 2c4dd2f5fd2a1affc6be3bdc326d220357979611    Arquivo1.txt
```

<div id='conteudo'></div> 

## Encontrando o conteúdo do arquivo

Esse é o último hash que vamos ter nesse texto (eu prometo). Este hash corresponde ao arquivo que guarda uma cópia do conteúdo do Arquivo1.txt. Como este hash começa com 2c, sei que o arquivo está dentro da pasta .git\objects\2c. Vamos usar o comando git cat-file uma última vez pra ver o conteúdo deste último arquivo. Note, no trecho abaixo, que o comando retorna exatamente o mesmo conteúdo do Arquivo1.txt que criamos na pasta git-demo. 

```
git cat-file -p 2c4dd2f5fd2a1affc6be3bdc326d22035797961

Conteúdo do arquivo 1
```

<div id='conclusao'></div> 

## Conclusão

Com este breve texto,  podemos perceber que o Git monitora as mudanças em nossa pasta criando cópias dos nossos arquivos dentro da pasta .git\objects. Além disso, o Git mantém um arquivo tree para cada commit, o que lhe permite saber quais arquivos existiam na sua pasta após determinado commit. Por fim, podemos ver que os commits são "fotos" de um determinado estado da nossa pasta. Graças a essas fotos o Git consegue manter um histórico de todas as mudanças ocorridas no diretório. 

Evidentemente que esse meu texto apenas arranha a superfície do Git, mas creio que ele já é suficiente pra você entender como esse sistema consegue ser poderoso usando uma abordagem extremamente simples. Obrigado pela leitura e um abraço pra você.

<div id='referencias'></div> 

## Referências
[Git Internals - Plumbing and Porcelain](https://git-scm.com/book/en/v2/Git-Internals-Plumbing-and-Porcelain)