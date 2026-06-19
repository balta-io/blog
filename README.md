# Como funciona o processo de submissão

## Passo 1: Proposta do artigo
[👉 CLIQUE AQUI para submeter sua proposta de artigo](https://github.com/balta-io/blog/issues/new?template=submiss-o-de-artigo.md)

## Passo 2: Revisão da proposta
Após sua proposta ser submetida, nosso time fará uma análise da mesma. Durante este processo, seu artigo ficará com status `revisão`.
Não há ação sua neste passo.

## Passo 3: Produção
Caso a sua proposta seja aceita, ela entrará no modo `produção` e você já pode escrever seu artigo.
Leia atentamente a sessão **Produção do Artigo** para garantir que atenderá todas as expectativas desta fase.

## Passo 4: Revisão Técnica
Após a produção e submissão do artigo, o mesmo entrará novamente em `revisão`, desta vez técnica, sobre o conteúdo.
Caso seu artigo seja aprovado nesta fase, seu issue será então marcado como `markerting`.
Não há ação sua neste passo.

## Passo 5: Marketing
Nesta fase nosso time de marketing vai trabalhar nas imagens e outros itens do seu artigo.
Não há ação sua neste passo.

## Passo 6: Agendamento
Nesta fase nosso time vai agendar a publicação do seu artigo e te informar a data/horá que ele será publicado.
Não há ação sua neste passo.

## Passo 7: Publicação
Parabéns, seu artigo está publicado no balta!!!
Não há ação sua neste passo.

# Produção do artigo
IMPORTANTE: Só execute estes passos se o seu issue estiver com a tag `produção`.

## Passo 1: Clonar o repositório
```
git clone https://github.com/balta-io/blog
```

## Passo 2: Estrutura do artigo
Na issue criada, uma URL foi atribuída ao seu artigo e uma pasta foi criada no repositório.
Por exemplo, supondo que a URL `fundamentos-do-csharp` foi atribuída ao seu artigo, você verá uma pasta chamada `fundamentos-do-csharp` na raiz do repositório clonado.

## Passo 3: Criação do arquivo
Crie um arquivo `index.md` na pasta destinada ao seu artigo. Todo conteúdo deve ser escrito neste arquivo.
Utilize sempre o markdown como base, evitando ao máximo uso de HTML.
Separe sempre os cabeçalhos de forma concisa, usando #, ## e ### para separação do conteúdo.
Em caso de código, use sempre as sintaxes de código especificando a linguagem.
```
```csharp
```html
``javascript
```
Aqui tem um artigo de exemplo:
https://raw.githubusercontent.com/balta-io/blog/refs/heads/main/autenticacao-e-autorizacao-com-jwt-e-bearer-no-aspnet-7/index.md

### Imagens
- NÃO UTILIZE IMAGENS COM DIREITOS AUTORAIS
- Não é por que a imagem está na internet que pode ser utilizada. Só use imagens de fontes oficiais e publicas, como o site da Microsoft. Se for o caso, recrie a imagem!
- Coloque todas as imagens em uma pasta `images` dentro da pasta destinada ao seu artigo
- Não utilize espaços em branco ou caracteres especiais nos nomes da imagem. Prefencialmente renomeie-as como `01.jpg`, `02.jpg` e assim por diante.
- Não use imagens grandes, com mais de 500kb, prefira sempre imagens menores e no formato JPG.

