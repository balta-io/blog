# Criando workflows reutilizáveis no GitHub Actions

Requisitos
----------

- [ ] Criar uma conta GitHub;

## História

Em muitos projetos, a entrega de um software passa por um processo manual para realizar a instalação, testes, validação e implantação copiando os arquivos quando as etapas anteriores forem realizadas com sucesso para fazer uma publicação para uma nova versão gerada. Neste fluxo, muitas etapas requerem esforços manuais e a cada atividade, a chance de um erro ou falha serem ocasionados acabam aumentando a medida que o seu produto continuará recebendo funcionalidades.

A automação desse processo pode ser realizada através de um método chamado CI/CD. Essa é apenas uma visão macro para apresentarmos a motivação e apresentar o exemplo.

## Motivação

Com alguns fluxos de CI/CD criados e duplicados entre os repositórios, uma dúvida surgiu: será que é possível reutilizar workflows? E a resposta é: sim, é possível.

## Contexto

Podemos criar templates e reutilizá-los de duas maneiras diferentes que serão apresentadas.