Neste artigo vou abordar a estretégia que utilizei para distribuir algumas capacidades de negócio do balta em nosso projeto novo, bem como as motivações por trás de tudo.

## Contextos delimitados
O primeiro entendimento que fizemos foi sobre quais contextos delimitados teríamos no novo negócio.

Basicamente analisamos as áreas que temos e separamos em capacidades de negócios, pequenas e mais simples.

Como resultado obtivemos os seguintes contextos:
* Conteúdo
* Alunos
* Assinaturas

Estes são os primeiros recortes, depois teremos mais, a medida que as coisas forem expandindo, mas por enquanto, quanto mais simples melhor.

### Regras de negócio e testes de unidade
O ponto principal de todo sistema são as regras de negócio e consequentemente os testes de unidade.

Se você organiza tudo, mantém a regra na aplicação ao invés do banco e não executa testes de unidade (Automatizados), está jogando 50% dos benefícios no lixo.

Desta forma, a premissa era manter os processos centralizados. Hoje temos integrações com gateways de pagamento, site e futuramente App.

Todos estes devem seguir o mesmo fluxo, o mesmo processo, as mesmas regras.

Logo concentramos todas as interfaces (Contratos) e fluxos no `Core` da aplicação, separados nos contextos que mencionei acima.

Se você já fez os cursos [Modelando Domínios Ricos](https://balta.io/cursos/modelando-dominios-ricos) e [Refatorando para Testes de unidade](https://balta.io/cursos/refatorando-para-testes-de-unidade) sabe quanto eu valorizo utilizar a OOP na modelagem.

Inclusive eu brinco que o `Core`tem que funcionar só com os objetos do contexto, sem depender de API, dados nem nada.

Com eles funcionando, é só persistir seu estado em um banco e expor via API. Gosto de pensar assim.

### Múltiplas arquiteturas

Outro ponto importante é os contextos nem sempre seguem o mesmo padrão, eles agem independentes.

Por exemplo, o contexto de `conteúdos` é extremamente voltado para leitura, logo não faz sentido ter uma estrutura complexa e voltada para validação de dados nele. Neste caso, temos apenas repositórios, expondo informações.

Já o contexto de `assinaturas` precisa de uma validação muito mais consistente e firme, envolve pagamentos, dados sensíveis e muitos testes de unidade.

Então, `One size doesn't fits all`, ou no belo português, arquitetura não tem bala de prata.

## Distribuição
Mas por que separar um domínio? Por que segmentar em diversos sub domínios, em contextos delimitados?

### Quanto menor, melhor
Um componente pequeno é fácil de mudar ou até mesmo de se jogar fora, pode ser gerenciado por uma pessoa apenas, por exemplo.

### Fácil entendimento
Componentes menores também são mais fáceis de se entender. Mesmo dentro do meu negócio, tem coisas de marketing por exemplo que me tomam tempo.

Como funciona o processo de sinalização de uma nova assinatura para ferramenta de marketing? Como funciona o processo que coloca um aluno em um funíl de vendas?

Estes processos podem ser simples se forem tratados individualmente, mas podem se tornar complexos se conviverem muito juntos.

A ideia de um componente é conseguir realizar este processo com o mínimo de esforço e informação possível, de forma que vários outros componentes e sistemas possam fazer uso do mesmo.

### Fácil manutenção
Como disse Robert C. Martin (Uncle Bob), criar um software para rodar uma vez é fácil, criar um software para ser mantido por um bom tempo é difícil.

Na teoria é tudo lindo, mas na prática a teoria é outra, você sabe! Aqui não é diferente, tem horas que as coisas precisam acontecer para ontem.

Embora eu odeie esta situação e lute para que isto não aconteça, não podemos ser hipócritas e dizer que estamos livres disso.

Então, gambiarras vão acontecer, porém elas devem ser raras e se você tem uma divisão boa das coisas, pode refazer um componente com facilidade.

Por exemplo, na integração com um gateway de pagamentos, utilizamos a SDK que eles fornecem em .NET (uma péssima decisão), mas vimos que ela não atende em diversos pontos, e ainda depende de versões anteriores do .NET.

Porém como este componente é pequeno, já temos um issue aberto para refazê-lo, usando `RestSharp` e chamando a API do gateway diretamente.

Juntando isto com uma boa modelagem e testes de unidade, conseguimos uma boa manutenção nos componentes.

### Deploy
Outra vantagem de componentes independentes é a publicação deles. Se as integrações (Jobs), o site, a API, o App, dentre outros consomem estes componentes, uma simples modificação neles pode gerar um novo deploy de tudo.

O que fazemos é separar estes contextos em projetos (repositórios) separados e distribuí-los como pacotes `NuGet`.

Neste cenário, trabalhamos com versionamento semântico para não quebrar nada que já está rodando e conseguimos distribuir versões novas de formas independentes.

Por exemplo, houveram funcionalidades que precisavam ser implementadas no `Core` mas no momento só deveriam afetar as integrações, o site deveria ser mantido como está.

Em uma referência direta aos projetos em uma solução, isto não aconteceria de forma amena.

## Microsserviços
"Mas isto que você está falando são microsserviços?" Ok, tem algumas coisas parecidas, mas não são!

A ideia dos microsserviços também é serem pequenos e independentes, mas eles implicam em uma série de outras coisas que não temos hoje como IAM e múltiplos bancos de dados.

Inclusive não temos a mínima necessidade de ter algo neste nível hoje, as justificativas são inválidas.

> **Quanto custa?** só a nível de conhecimento, hoje todo ecossistema do balta roda no Azure e gastamos menos de $ 150.00/mês.

Em relação aos custos, ter cada contexto em uma API independente com seu próprio banco de dados, um servidor de autenticação (IAM) e um API gateway nos custaria pelo menos o dobro deste valor.

## GitHub Packages

A chave para distribuir os pacotes é o `GitHub`, na verdade estamos utilizando o GitHub para gerenciar tudo relacionado a código, inclusive os projetos, mas isto é pauta para outro assunto.

Os GitHub Packages funcionam como um NuGet privado, que só os repositórios da sua empresa tem acesso.

No nosso caso, temos uma organização dentro do GitHub e dentro dela temos os repositórios.

Estes contextos, nada mais são do que `ClassLibraries` que geramos o `nupkg` e publicamos no GitHub Packages utilizando GitHub Actions.

### Iniciando um projeto
Para criar um pacote, basta gerar um novo `Class Library` com qualquer nome que faça sentido para seu negócio.

`dotnet new classlib -o MeuContexto`

Não vou me atentar a código aqui, então abra o projeto, modifique como desejar e certifique-se que o mesmo está buildando!

### Gerando um pacote

Sempre que compilamos um `Class Library` o resultado é uma DLL, mas queremos gerar um pacote, então o primeiro passo é colocar algumas informações no arquivo `.csproj` do seu projeto.

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>net5.0</TargetFramework>

    <PackageId>MeuContexto</PackageId>
    <Version>1.0.0</Version>
    <Authors>André Baltieri</Authors>
    <Company>balta.io</Company>
    <PackageDescription>Meu primeiro pacote</PackageDescription>
    <RepositoryUrl>https://github.com/balta-io/MeuContexto</RepositoryUrl>
  </PropertyGroup>
</Project>
```

Estas informações serão inclusas no seu pacote e ficarão disponíveis quando alguém for baixá-lo.

Feito isto, podemos compilar e empacotar nosso projeto, utilizando os comandos abaixo.

```
dotnet build --configuration Release
dotnet pack --configuration Release
```

O comando `pack` vai gerar um arquivo `.nupkg` como resultado, nas pasta `bin/release` do projeto.

### Publicando o pacote no NuGet
Com o pacote `.nupkg` gerado, caso queira publicá-lo publicamente no NuGet e deixá-lo visível para todos que usam Visual Studio por exemplo, basta acessar [NuGet.org](https://nuget.org) se autenticar e fazer o upload do pacote.

Ou caso queira fazer isto via linha de comando, basta usar o seguinte.

```
dotnet nuget push "bin/Release/*.nupkg" -s nuget --skip-duplicate
```

O `-s nuget` significa que estamos enviando para o NuGet.org e o `--skip-duplicate` não causa erro caso o pacote (versão atual) já exista.

> A versão do pacote está descrita no .csproj que editamos anteriormente.

### Publicando no GitHub Packages
Quando criamos uma organização no GitHub, temos a possibilidade de usar pacotes privados (NPM, NuGet, Maven, etc...) e eles ficam disponíveis na página inicial da sua organização.

Toda vez que executamos um `dotnet add package <PACOTE>` ele busca este pacote de fontes que temos definidas em um arquivo global do NuGet na máquina ou em um arquivo NuGet.config na raiz da aplicação.

No caso, o arquivo NuGet.config na raiz sempre sobrescreve o arquivo NuGet.config global da sua máquina.

> O arquivo NuGet.config global está localizado em **%appdata%\NuGet\NuGet.Config** no Windows e **~/.config/NuGet/NuGet.Config** no Mac/Linux.

Enão tudo que o NuGet.org faz é armazenar os `.nupkg` e prover um JSON com todos os pacotes registrados.

No caso dos GitHub Packages a URL dos seus pacotes será esta.

```
https://nuget.pkg.github.com/<ORGANIZATION>/index.json
```

Dadas estas informações, se usamos o `-s nuget` para enviar um pacote para o NuGet público, só precisamos mudar o source para termos a publicação nos GitHub Packages.

O único detalhe é que os GitHub Packages requerem autenticação, afinal, só usuários da sua empresa podem publicar um pacote.

Então, caso você queira publicar seu pacote diretamente da sua máquina, vai precisar de um PAT (Public Access Token), que pode ser gerado no seu perfil no GitHub na sessão Developer Settings.

Desta forma a publicação nos GitHub Packages fica da seguinte forma.

```
dotnet nuget push "bin/Release/*.nupkg" -k <TOKEN> -s https://nuget.pkg.github.com/<ORGANIZATION>/index.json --skip-duplicate
```

Feito isto, você já deve ver seu pacote na URL `https://github.com/orgs/<ORGANIZATION>/packages`.

## GitHub Packages e GitHub Actions
Bom, não chegamos até aqui simplesmente para ficar publicando nossos pacotes manualmente, não é? Queremos deixar tudo automático e para isto temos os [GitHub Actions](https://balta.io/blog/aspnet-deploy-github-actions-azure).

Sabemos que precisamos executar um `dotnet build`, `dotnet pack` e `dotnet nuget push` para ter nosso pacote publicado, então nossa tarefa já está mais fácil.

Nosso único desafio aqui é o **token** necessário para autenticação. Não podemos simplesmente deixá-lo no arquivo `.yml`, senão todos com acesso ao repositório poderão vê-lo (very bad).

Por sorte, toda execução do GitHub Action tem uma variável chamada `GITHUB_TOKEN` que fica armazenada nos segredos.

Esta variável é gerada automaticamente e dura apenas a execução do action, o que a torna perfeita para nós.

Sendo assim podemos substituir nosso token por `${{ secrets.GITHUB_TOKEN }}` que o GitHub se encarrega do resto.

Vamos então criar a estrutura que os GitHub Actions dependem em nosso projeto, que nada mais é do que um arquivo `.yml` gerado na pasta `.github/worflows`.

`.github/worflows/main.yml`
```yml
name: Build and deploy MeuPacote Package

on:
  push:
    branches:
      - main

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@main

    - name: Set up .NET Core
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '5.0.x'
        source-url: https://nuget.pkg.github.com/<ORGANIZATION>/index.json
      env:
        NUGET_AUTH_TOKEN: ${{secrets.GITHUB_TOKEN}}

    - name: Build with dotnet
      run: dotnet build --configuration Release

    - name: Create the Package
      run: dotnet pack --configuration Release

    - name: Publish
      run: dotnet nuget push "bin/Release/*.nupkg" -k ${{ secrets.GITHUB_TOKEN }} -s https://nuget.pkg.github.com/<ORGANIZATION>/index.json --skip-duplicate
```

Feito isto, basta enviar o código para a branch `main` do seu repositório e pronto, você tem um pacote no GitHub Packages com deployment automatizado via GitHub Actions.

## Consumindo um GitHub Package
Para consumir este pacote localmente (na sua máquina) você precisará de permissão, afinal este pacote é privado.

Se você está apenas estudando por este artigo, a maneira mais fácil de fazer isto é criar um arquivo `NuGet.config` na raiz do projeto no qual deseja consumir o pacote criado e adicionar o `Package Source` da sua organização.

Você também precisará de um PAT para ter acesso a este pacote.

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <packageSources>
    <add key="nuget.org" value="https://api.nuget.org/v3/index.json" protocolVersion="3" />
    <add key="github" value="https://nuget.pkg.github.com/<ORGANIZATION>/index.json" />
  </packageSources>
  <packageSourceCredentials>
    <github>
        <add key="Username" value="USERNAME" />
        <add key="ClearTextPassword" value="TOKEN" />
    </github>
  </packageSourceCredentials>
</configuration>
```

Caso esteja em um projeto utilizando vários pacotes privados no GitHub Packages recomendo que copie este arquivo `NuGet.config` acima para e sobrescreva seu arquivo de configuração global do NuGet.

Este arquivo está localizado em:

```
Windows:%appdata%\NuGet\NuGet.Config
Mac/Linux: ~/.config/NuGet/NuGet.Config
```

## GitHub Actions que dependem de GitHub Packages

Por último, o desafio é utilizar pacotes privados do GitHub Packages com em outras aplicações que são publicadas via GitHub Actions.

Como você pode notar durante a publicação com GitHub Actions, executamos um `dotnet build` e este por sua vez executa um `dotnet restore` que fará o download dos pacotes que sua aplicação depende.

O problema aqui é que como os pacotes são privados, você se deparará com um erro de permissão, afinal a máquina gerada pelos GitHub Actions não tem acesso a estes pacotes privados.

Então, assim como fizemos localmente, temos que adicionar um `Package Source` as configurações do NuGet da máquina antes do comando `dotnet build`, porém não vamos editar ou enviar um arquivo `NuGet.config` e sim utilizar o comando `dotnet nuget add source`.

```
dotnet nuget add source https://nuget.pkg.github.com/<ORGANIZATION>/index.json -n github -u USERNAME -p PAT 
```

Novamente precisamos informar o usuário e token, porém, neste cenárion o `${{ secrets.GITHUB_TOKEN }}` não vai funcionar.

A maneira mais segura de armazenar seu usuário/token é utilizando os `Secrets` do GitHub em [https://github.com/balta-io/Balta.Web/settings/secrets/actions](https://github.com/balta-io/Balta.Web/settings/secrets/actions).

Os secrets funcionam como chave/valor e uma vez criados você não pode mais visualizá-los, sua chave será exibida uma única vez na tela e depois já era! Você pode remover e criar outra, mas não pode visualizar ou alterar ela.

Desta forma, vamos adicionamos um passo a mais no arquivo de publicação do action, que eu chamei de `Add GitHub Packages Source`.

```yml
- name: Set up .NET Core
    uses: actions/setup-dotnet@v1
    with:
    dotnet-version: '5.0.x'

- name: Add GitHub Packages Source
    run: dotnet nuget add source https://nuget.pkg.github.com/<ORGANIZATION>/index.json -n github -u ${{secrets.MEU_USER}}  -p ${{secrets.MEU_TOKEN}} 

- name: Build with dotnet
    run: dotnet build --configuration Release
```

Pronto, você tem um deployment automatizado com GitHub Actions que consome um pacote privado do GitHub Packages que também foi automatizado com GitHub Actions! Mind blow!

## Conclusão
Podemos criar e distribuir aplicações grandes sem a necessidade de criar microsserviços. Podemos criar pequenos e independentes pacotes privados e inclusive automatizar sua distribuição.

Mantenham as coisas o mais simples possível!