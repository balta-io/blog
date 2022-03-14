# Sumário

 1. [Introdução](#introducao)
 2. [O que são sites estáticos](#o-que-sao-sites-estaticos)
 3. [Criando o site](#criando-o-site)
 4. [Referências](#ref)

<div id='introducao'></div> 

# Introdução

A ferramenta que nos permite publicar sites estáticos gratuitamente no GitHub é conhecida como *GitHub Pages*. Apesar do tamanho limite de 1 GB de armazenamento, 100 GB de banda larga e 10 compilações por hora, é possível utilizar esta ferramenta para diversos fins.

O GitHub Pages oferece a possíbilidade de publicar sites em repositórios públicos. É possível utilizar repositórios privados e organizacionais também porém em planos não gratuitos. Neste artigo vamos nos atentar a mostrar o funcionamento da ferramenta para título de introdução utilizando um destes meios.

<div id='o-que-sao-sites-estaticos'></div> 

# O que são sites estáticos

Sites estáticos são aqueles que envolvem HTML (HyperText Markup Language), CSS (Cascading Style Sheets) e JS (JavaScript) sem recursos de gerenciamento de conteúdo. Isto quer dizer que para fazer atualizações ou modificações no site é preciso modificar diretamente o código fonte.

Com o GitHub Pages você criar um site estático como seu portfólio profissional, site de informações e contato, apresentação do seu projeto e diversas outras usabildiades. 

<div id='criando-o-site'></div> 

# Criando o site

Para que seja possível publicar o site no GitHub Pages é necessário acessar o repositório onde este site está localizado. Neste caso usaremos o repositório da organização que criamos nos artigos anteriores.

1 - Acesse as configurações do repositório.

<!-- organization-settings.jpg -->

2 - Encontre a sessão de 'Código e automação' e selecione o item 'Páginas'.

<!-- pages-option.jpg -->

3 - Ao chegar em uma página semelhante ao exemplo abaixo você verá a opção de selecionar a fonte onde está localizado o código. Você deve selecionar a branch do repositório que você armazenou o mesmo. Logo abaixo você pode escolher temas que o próprio GitHub oferece para o seu site.

<!-- pages-settings.jpg -->

E então podemos salvar. A partir de agora o conteúdo da branch seleciona será lido pelo github pages e pode ser acessado através do link fornecido ao salvar as configurações que tem o seguinte formato:

```
https://nome-da-organizacao.github.io/nome-do-repositorio/
```

Agora precisamos de um arquivo chamado `index.html` na raiz desta branch e a partir dele a naveção do site pode ser feita. Você pode adicionar pastas e acessar estas normalmente através das referências dentro do seu código. Para termos de exemplo vamos publicar uma página de exemplo com código estático da W3C.

Para clonar o repositório para sua máquina utilize em seu terminal o comando:

```
git clone https://github.com/repositorio
```

Após adicionar os arquivos desejados dentro do repositório comente as mudanças com os comandos:
```
git add --all
git commit -m "seu comentário"
```

E então envie as modificações para o GitHub com o comando:
```
git push -u origin main
```
`Nota 1`: Mais detalhes sobre o funcionamento destes comandos podem ser encontrados em artigos relacionados. Os links estão no final deste artigo.

Ao inserir o código dentro do index.html e subir as modificações para a branch definida anteriormente, o GitHub começará o processo automático de deploy (publicação) que pode ser acompanhado ao clicar na opção de `Actions` no menu superior do repositório.

<!-- deploy-page.jpg -->

Quando o processo for concluído podemos conferir o resultado no link do site estático que nos foi informando durante o processo de habilitação do GitHub Pages. Veja o resultado abaixo:

<!-- result-page.jpg -->

# Agora é com você

Utilize esta ferramenta para publicar seus sites. As pessoas poderão ter a experiência de utilizar algo feito por você e outros desenvolvedores podem se inspirar no resultado do seu trabalho. Crie seu portfólio e adicione o link no seu currículo ou crie o seu currículo online e compartilhe o link. As possibilidades são infinitas.

# Continue lendo:

[Git e GitHub - Instalação, Configuração e Primeiros Passos](https://balta.io/blog/git-github-primeiros-passos)

[Gestão de projetos com GitHub](https://balta.io/blog/gestao-de-projetos-com-github)

[Gestão de equipes com GitHub](https://balta.io/blog/gestao-de-equipes-com-github)

## Cursos relacionados

`Gratuito` | [Fundamentos do Git e Azure DevOps](https://balta.io/cursos/fundamentos-git-azure-devops)

<div id='ref'></div> 

## Referências
[Documentação do GitHub](https://docs.github.com/)

[W3C - Template utilizado neste artigo](https://www.w3schools.com/w3css/tryit.asp?filename=tryw3css_templates_blog&stacked=h)