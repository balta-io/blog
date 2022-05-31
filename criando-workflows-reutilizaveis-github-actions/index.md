# Criando e reutilizando fluxos de trabalho no GitHub Actions

Requisitos
----------

- [ ] Criar uma conta GitHub;
- [ ] Habilitar o fluxo de trabalho para a sua organização;

## História

Em muitos projetos, um software necessita passar por um processo manual para que a sua entrega e implementação sejam realizadas. Nisto, podemos destacar os processos de instalação, testes, validação e implantação copiando os arquivos quando as etapas anteriores forem realizadas com sucesso. Neste fluxo, muitas etapas requerem esforços manuais e a chance de um erro ou falha serem ocasionados acabam aumentando a medida que o seu produto continuará recebendo funcionalidades ou um arquivo de configuração seja modificado.

A automação desse processo pode ser realizada através de um método chamado CI/CD. Essa é apenas uma visão muito macro para apresentarmos a motivação e o exemplo.

## Motivação

Dentro da sua organização, você pode ter se deparado com um cenário que para reutilizar um fluxo de trabalho (workflow), você pode ter utilizado o famoso ctrl+c -> ctrl+v para copiar os principais comandos e adicioná-los ao seu repositório. Partindo desse exemplo, uma dúvida surgiu: será que é possível reutilizar workflows? E a resposta é: sim!

![Criando workflows em repositórios diferentes](images/repositories.png?raw=true)

## Contexto

Vamos apresentar duas maneiras de reutilizar fluxos de trabalho e como realizar suas configurações. Mas antes, utilizaremos o modelo abaixo definido em uma versão simplificada para apresentarmos as etapas de configuração posteriores para os dois contextos apresentados.

![CI simplificado](images/ci-cd.png?raw=true)

```yml
name: Sample react workflow

on:
  push:
    branches: [master]
  pull_request:
    branches: [master]

jobs:
  build:
    runs-on: ubuntu-latest

    strategy:
      matrix:
        node-version: [16.x]

    steps:
      - name: Checkout repository
        uses: actions/checkout@v2

      - name: Set up Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v1
        with:
          node-version: ${{ matrix.node-version }}

      - name: Install dependencies
        run: npm install

      - name: Run the tests
        run: npm test

      - name: Generate build
        run: npm run build
```

## Criando fluxos de trabalho iniciais

Talvez você já tenha se deparado com os modelos de exemplo abaixo ao tentar adicionar um novo workflow na seção Actions do repositório do GitHub:

![Selecionando um template de exemplo](images/github-actions-samples.png?raw=true)

Pensando em um cenário em que repositórios diferentes da sua organização utilizem a mesma base de configuração para os seus fluxos de trabalho, e que você tenha um fluxo pré-definido, podemos adicionar templates customizados que atendam a nossa demanda e que ficam internas para os repositórios.

Para isto, para adicionarmos um template de início para a criação de workflows para a sua organização, é necessário realizar as etapas:

- [ ] Criar um repositório na organização chamado .github;
- [ ] Criar uma pasta chamada `workflow-templates`;
- [ ] Criar 3 arquivos:
  - [ ] react-workflow.yml
  - [ ] react-workflow.properties.json
  - [ ] icon-wechoo.svg

![Criando uma pasta .github na organização](images/github-template.png?raw=true)

O arquivo de metadados que contém o nome properties.json em sua extensão deve conter o mesmo nome do arquivo do fluxo de trabalho.

[react-workflow].yml && [react-workflow].properties.json

A imagem svg pode ser o ícone da sua aplicação, da sua empresa ou organização.

O conteúdo do **react-workflow.properties.json** de exemplo que iremos utilizar será:

```json
{
  "name": "Wechoo Organization React Workflow",
  "description": "Wechoo Organization CI workflow template.",
  "iconName": "icon-wechoo",
  "categories": ["JavaScript"]
}
```

As propriedades name e description são informações obrigatórias. A imagem e a categoria são de sua escolha.

Com este fluxo de trabalho, podemos criar fluxos iniciais para serem reutilizados em sua organização.

![Criando um template](images/criando-templates.png?raw=true)

Isso permite que cada repositório utilize um fluxo de trabalho inicial adicionando e alterando as etapas conforme a sua necessidade. O resultado da configuração anterior pode ser visualizado ao entrarmos em um outro repositório de sua organização e no mesmo caminho de *Actions*, você poderá visualizar seu fluxo de trabalho.

![Utilizando um fluxo de trabalho inicial](images/workflow-setup.png?raw=true)

## Reutilizando fluxos de trabalho

Uma outra maneira de reutilizar fluxos de trabalho é referenciando arquivos de chamada através de uma única linha reduzinho ainda mais o custo e padronizando seus fluxos, além de utilizar um fluxo que pode ter sido previamente validado e certificado. Perceba que na imagem abaixo, diferente do primeiro fluxo, a seta está com a direção contrária. Isso significa que o fluxo não estará com sua alteração ou configuração inicial no repositório em que será utilizado, mas em um contexto da organização.

![Reutilizando fluxos de trabalho](images/reusing-workflows.png?raw=true)

Eu criei um repositório de modelo com o nome de *workflow-templates* para realizar os testes para essa abordagem com o fluxo de trabalho abaixo:

![Reutilizando fluxos de trabalho](images/reusing-workflows-template.png?raw=true)

Para que o fluxo de trabalho seja reutilizável, a chave `workflow_call` deve ser adicionada ao conjunto de valores de `on`:

```yml
on:
  workflow_call:
```

Após a criação do repositório com o fluxo de trabalho adicionado, no repositório de destino em que você deseja reutilizar o fluxo, podemos criar a pasta .github com workflows e um nome de fluxo de trabalho de sua escolha. Para demonstração, irei identificar apenas com um fluxo de trabalho reutilizável.

![Reutilizando fluxos de trabalho](images/calling-reusable-workflow.png?raw=true)

A visualização do fluxo poderá ser encontrada em Actions no menu do repositorio.

![Visualizando a execução do fluxo de trabalho](images/showing-reusable-workflow.png?raw=true)
