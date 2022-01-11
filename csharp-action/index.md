As Actions no C# funcionam como uma espécie de delegate onde podemos armazenar ações para serem executadas posteriormente.

## Action
Um `Action` no C# é um tipo que pode receber uma função. Existe também o `Func<T>` que falaremos nos próximos artigos aqui.

Para definir uma `Action` basta criar seu tipo e apontar para uma função seja no momento da declaração da variável ou posteriormente.

```csharp
Action acao;

acao = MinhaFuncao;

acao();

void MinhaFuncao() 
{
    Console.WriteLine("Eu sou uma função");
}
```

No exemplo acima, temos uma função chamada `MinhaFuncao` que imprime um texto na tela. Após definida, podemos criar uma ação como definimos em `Action acao` e depois assimilar a função a esta ação, como fizemos com `acao = MinhaFuncao`.

Por fim podemos executar esta ação invocando-a, como fazemos com qualquer função (Abrindo e fechando parênteses) desta forma `acao();`.

## Funções anônimas

Dada uma função que não recebe parâmetros (Parameterless), podemos assimilá-la diretamente a um `Action` como fizemos anteriormente em `acao = MinhaFuncao;`.

Porém quando temos parâmetros, a invocação da função exige os mesmos, e neste caso, precisamos da assinatura completa da função para poder invocá-la posteriormente.

Isto pode ser feito utilizando as funções anônimas do C#, cujo podem ser definidas como `() =>` e são exemplificadas abaixo.

```csharp
Action acao;

acao = () => MinhaFuncao("Eu sou um parâmetro");

acao();

void MinhaFuncao(string texto) 
    => Console.WriteLine(texto);
```

Note que neste caso, é como se estivessemos executando a função, podemos passar os parâmetros necessários para ela ser executada, tudo conforme estamos acostumados já no C#.

Porém, a execução desta função não será realizada no momento que associamos ela com a `Action` e sim na hora que executamos a `Action`, neste caso a linha `acao();`.

## Action Chaining
Mas qual a vantagem em ter uma função que armazena uma função? Bem, uma delas é o **Action Chaining** ou encadeamento de funções.

Em diversas conversas durante os [**Encontros Premium**](https://balta.io/agenda) eu mostrei alguns exemplos de como usamos CQRS aqui no balta e como temos uma cadeia de eventos que acontecem em uma ordem específica.

Vamos tomar como base um cadastro de novos clientes/alunos, com os seguintes métodos abaixo.

```csharp
Action handle;

handle = VerificaRequisicao;
handle += VerificaSeEmailEstaEmUso;
handle += GeraOUsuario;
handle += GeraOAluno;
handle += PersisteOsDados;
handle += EnviaCodigoDeAtivacao;
handle += LogaANovaContaCriada;

handle();

void VerificaRequisicao() { }
void VerificaSeEmailEstaEmUso() { }
void GeraOUsuario() { }
void GeraOAluno() { }
void PersisteOsDados() { }
void EnviaCodigoDeAtivacao() { }
void LogaANovaContaCriada() { }
```

Neste caso, podemos utilizar o `+=` para anexar funções ao `Action` e posteriormente executá-las na ordem que foram atribuídas.

Utilizamos este modelo por conta do reuso das funções em diversas partes do sistema. Imagina uma simples validação de E-mail, queremos saber se o E-mail já está cadastrado em nossa base, então isto vira uma função que pode ser invocada em diferentes `Handlers`.

## Conclusão
O C# permite criarmos ações ou cadeia delas para serem executadas posteriormente. Isto permite uma maior reusabilidade das funções e alocação dinâmica das mesmas.


