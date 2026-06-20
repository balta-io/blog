---
title: Como instalar o Windows 11 - Preview
published: true
description: Tentou atualizar seu Windows 10 para o Windows 11 e não conseguiu? Veja aqui algumas possíveis soluções
tags: 
cover_image: https://res.cloudinary.com/practicaldev/image/fetch/s--4bSQExND--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/uploads/articles/j92u9sxuxxy5egc9jyat.png
---

![Windows 11](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/j92u9sxuxxy5egc9jyat.png "Windows 11")
 
## Windows Insider
A primeira versão do Windows 11 está disponível desde 27/06 para membros do [**Windows Insider**](https://insider.windows.com/pt-br/), então seu primeiro passo é se cadastrar lá.

O cadastro é simples e não requer nenhum pagamento, basta associar uma conta Microsoft com sua conta "Insider".

## Ligando sua conta Microsoft ao Windows
No seu Windows 10 atual, vá para as configurações e busque por "Minha conta" ou "Account Info". Nesta tela, você poderá linkar sua conta Microsoft com seu computador (Windows).

## Habilitando o Windows Insider
Ainda nas configurações, procure por **Windows Insider** e na tela desta configuração haverão três opçÕes:
* Updates convencionais
* Betas e Previews
* Dev

Informações sobre o Windows 11 já devem aparecer nas duas últimas opções.

### IMPORTANTE
As releases beta, preview e dev não são estáveis ou seja, não são recomendadas para produção. Use por sua conta e risco.

No meu caso, utilizo a **Dev**, mas recomendo que se é seu primeiro contato com o Insider, vá de Beta/Preview.

## TPM 2.0
Nestas primeiras previews do Windows 11, muitas pessoas tem recebido a seguinte mensagem:

![image](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/rjul7b8vkx147xndhi3o.png)
 
Na maioria dos casos ela se refere ao chip TPM 2.0 utilizado para encriptação de informações, que vem presente em diversas placas mãe/processadores desde 2016.

Para verificar se você está com o TPM habilitado, execute o seguinte comando no **Run** (Win + R):
`tpm.msc`

Caso receba uma mensagem negativa, não significa que você não tem este chip, mesmo que a mensagem diga explicitamente isto.

No meu caso, mesmo com uma configuração atual como a abaixo, eu estava recebendo esta mensagem:
![image](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/m50b0ivi7fgd1w2l0q13.png)

## Habilitando o TPM 2.0
Aqui é a parte mais chata, pois a configuração do TPM 2.0 fica em lugares diferentes na **BIOS**, então pode variar de acordo com cada configuração.

No meu caso, ela fica em **Advanced Options** > **fTPM**, e ela já aparecia como **Enabled**, o que fiz foi mudar a opção de qual TPM utilizar para **Firmware**.

Acredito que na maioria dos CPUs Ryzen (AM4) esta configuração deve ser parecida. Então recorra ao manual ou ao amigo Google e depois repita o processo do `tpm.msc` para ver se está tudo certo.

## Instalando o Windows 11
Feito isto, tudo pronto, você verá uma atualização para o Windows 11 que é bem "grandinha" por sinal.

Durante a atualização foi tudo tranquilo aqui, e até o momento estou no Windows 11 sem nenhum problema.

## Futuras Builds
Todo este processo de habilitar o TPM provavelmente será modificado, pois um usuário comum não teria condições de executar estes procedimentos.

Temos que lembrar que estes Previews são focados em um público mais técnico mesmo, então não precisam se preocupar, a transição do Windows 10 para o Windows 11 provavelmente vai ser tão tranquila quanto as anteriores.