Neste artigo vamos falar um pouco sobre JWT, um dos padrões de Token mais utilizados no mundo.

## O que é JWT?

Então você está me dizendo que eu vou armazenar um Token em um local onde o usuário (Ou outra pessoa) pode ir lá e visualizar?

Isto mesmo, os Tokens são como chaves de acesso, com informações e uma duração, ou seja, se alguém obter seu Token, ele pode “impersonar” ou fingir que é você.

Por isto a segurança física é a primeira e mais importante etapa que temos. Se alguém tem acesso ao seu Browser, fisicamente (ou remotamente) ele pode ver seu Token.

Na verdade, este seria o menor dos seus problemas, já que os dados de navegação são armazenados localmente, ou seja, todas as suas sessões estão em um arquivo.

Basta copiar este arquivo da sua máquina para a minha e pronto, estarei logado com todas as suas sessões.

Por este motivo frequentemente somos recomendados a não clicar em links suspeitos, visto que uma simples cópia expõe todas as suas informações.

Mas voltando aos Tokens, embora você possa armazenar uma chave/valor no Local Storage, é legal encriptar estas informações, correto?

Desta forma, se alguém roubar seu Token, não verá nada além de uma Hash como esta abaixo:

```bash
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

Como este processo é algo comum entre as aplicações e APIs, criou-se um padrão chamado JWT (Pronuncia-se JÓT), que é a sigla para **Json Web Token**.

Ao desencriptar este Token, temos como resultado os seguintes JSON:

```json
{
  "alg": "HS256",
  "typ": "JWT"
}

{
  "sub": "1234567890",
  "name": "John Doe",
  "iat": 1516239022
}
```

Se você notar, o Token contém “.” para segmentar suas regiões e o mesmo é dividido em três partes principais.

![Screenshot 2023-05-16 at 12.41.01.png](Screenshot_2023-05-16_at_12.41.01.png)

A primeira parte, em vermelho, é chamada de Header ou cabeçalho, que define o algoritmo utilizado na encriptação e o tipo do Token, no nosso caso, JWT.

Quando desencriptamos ela, temos o seguinte JSON como resultado:

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

Podemos mudar estes valores, incluindo o algoritmo e assim teremos valores diferentes. No caso, mudando de HS256 para HS384, temos a seguinte Hash e Header.

```json
// eyJhbGciOiJIUzM4NCIsInR5cCI6IkpXVCJ9
{
  "alg": "HS384",
  "typ": "JWT"
}
```

O mais comum e recomendado até o momento da escrita deste artigo é o HS256, ele balanceia performance e segurança. Quanto mais alta a encriptação, mais processamento ela requer.

O segundo item, em rosa é o Payload ou carga, que são informações que podemos incluir no Token com algumas ressalvas.

```json
// eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWUsImlhdCI6MTUxNjIzOTAyMn0
{
  "sub": "1234567890",
  "name": "John Doe",
  "admin": true,
  "iat": 1516239022
}
```

Assim como nos Headers o Payload também varia de acordo com a quantidade de informações que colocamos nele. Ao incluirmos a informação “premium” temos um outro valor sendo gerado.

```json
// eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWUsInByZW1pdW0iOnRydWUsImlhdCI6MTUxNjIzOTAyMn0
{
  "sub": "1234567890",
  "name": "John Doe",
  "admin": true,
  "premium": true,
  "iat": 1516239022
}
```

Por fim temos a parte em azul, que representa a assinatura do Token, que só existe no lado do servidor.

Em resumo o que temos é uma encriptação SHA256 de três itens convertidos para Base64, um código simples assim:

```jsx
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  'MINHA CHAVE SECRETA'
)
```

Então quer dizer que, além de salvar o Token eu ainda posso visualizar ele? Sim, tem sites como o [jwt.io](https://www.notion.so/Seguran-a-em-APIs-ASP-NET-com-JWT-e-Bearer-Authentication-8b144f3e473f4014a7dad21f5c0b7d30?pvs=21) que te permite visualizar tudo que um Token contém.

Indo além disso, o JWT.io (Você pode fazer isto manualmente também) te permite alterar o código de um Token, adicionando informações extras.

Isto significa que se eu pegar o Token abaixo, gerado para mim que diz que eu só tenho o perfil “student”:

![Screenshot 2023-05-16 at 12.53.34.png](Screenshot_2023-05-16_at_12.53.34.png)

```json
{
  "name": "André Baltieri",
  "roles": ["student"],
  "iat": 1516239022
}
```

E adicionar o perfil “admin”:

```json
{
  "name": "André Baltieri",
  "roles": ["student", "admin"],
  "iat": 1516239022
}
```

Agora eu tenho um novo Token, com acesso de administrador:

![Screenshot 2023-05-16 at 12.56.00.png](Screenshot_2023-05-16_at_12.56.00.png)

## Entendendo os Payloads e assinatura

Muita calma nessa hora! É possível sim mudar um Token, mas para isto é necessário uma chave privada, que somente o servidor deve conter. 

Se o Token for gerado com qualquer outra chave, diferente da qual foi gerada, o mesmo é invalidado. Isto é o que torna os Tokens seguros em relações as mudanças.

Então não importa se o Token foi alterado no cliente ou mesmo no meio do caminho, se ele não for re-gerado com a chave privada (Que só o servidor deve conhecer) ele será inválido.

> **MAN IN THE MIDDLE** - Existe um ataque comum chamado “Homem no meio” que basicamente intercepta a comunicação entre o cliente e o servidor e rouba informações ou modifica elas.
> 

### Não armazene valores sensíveis no Payload

Outro ponto importantíssimo é sobre o uso do Payload. Embora você possa adicionar qualquer informação que desejar nele, não é recomendado trafegar informações sensíveis ali.

Cartão de crédito, telefone, endereço ou qualquer informação que comprometa os dados do seu usuário, devem ser mantidos apenas no servidor.

Em suma, tendo o E-mail ou ID do usuário no Payload já basta. Com estas informações você pode consultar o que quiser sobre ele.

### Tempo de vida do JWT

Uma recomendação e padrão dos JWTs é conter no Payload a informação “iat” que signifca “Issued At” ou “Gerado em”, que nada mais é do que um timestamp da data/hora que o Token foi gerado.

Desta forma, podemos criar uma validação para o Token, dizendo que o mesmo só pode existir por X tempo. Assim, se um Token for roubado, ele só vai ser útil durante X dias ou horas, passado isto ele se torna inválido.

```json
{
  "sub": "1234567890",
  "name": "John Doe",
  "iat": 1516239022 👈
}
```

Dependendo do sistema este tempo de expiração pode variar, mas em geral, ele não deve ser tão curto a ponto de incomodar o usuário, que precisará se autenticar o tempo todo, nem tão longo que alguém possa roubar e usar por meses.

## Conclusão

Neste artigo pudemos entender o que são e como funcionam os JWT, como os Tokens são criados e quais informações eles contém.