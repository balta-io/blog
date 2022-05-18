# Criando workflows reutilizáveis no GitHub Actions

Requisitos
----------

- [ ] Criar uma conta GitHub;

## História

Em muitos projetos, a entrega de um software passa por um processo manual para que a sua entrega e implementação sejam realizadas. Passando por um processo de instalação, testes, validação e implantação copiando os arquivos quando as etapas anteriores forem realizadas com sucesso. Neste fluxo, muitas etapas requerem esforços manuais e a cada etapa, a chance de um erro ou falha serem ocasionados acabam aumentando a medida que o seu produto continuará recebendo funcionalidades ou um arquivo de configuração seja modificado.

A automação desse processo pode ser realizada através de um método chamado CI/CD. Essa é apenas uma visão muito macro para apresentarmos a motivação e apresentar o exemplo.

## Motivação

Dentro do repositório da sua organização, você provavelmente pode ter se deparado com projetos que utilizam o mesmo fluxo de trabalho para automatizar o processo comentado. Isso pode gerar workflows duplicados entre os repositórios e que, a longo prazo, dependendo do contexto de negócio e saúde das aplicações, a sua manutenção pode acabar ficando complexa quando uma mudança surgir ou uma etapa for adicionada. E a duplicação não necessariamente garante que as etapas adicionadas sejam similares. Partindo desse princípio, que talvez pode não ser identificado com o seu contexto, uma dúvida surgiu: será que é possível reutilizar workflows? E a resposta é: sim, é possível.

![Criando workflows em repositórios diferentes](images/repositories.png?raw=true)

## Contexto

Podemos criar templates e reutilizá-los de duas maneiras diferentes. Mas antes, utilizaremos o modelo abaixo definido em uma versão simplificada no exemplo de reutilização do workflow.

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

![CI simplificado](images/criando-templates.png?raw=true)

## Criando templates

## Reutilizando workflows