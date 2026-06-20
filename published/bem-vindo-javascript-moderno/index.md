Durante o ASP.NET Cast do dia 16/10/2019 falamos muito sobre JavaScript, sua história e mudanças, e eu percebi que teve muita coisa que talvez tenha passado batido para muitos, inclusive para mim.  
  
Eu sei que boa porcentagem dos alunos do site são pessoas buscando atualizações, então resolvi fazer este artigo para ajudar quem está desatualizado sobre JavaScript.

Let e Const
-----------

Anteriormente no JS utilizávamos sempre o var, tanto para constantes (Existia?) quanto para variáveis.  
  
Uma das novas mudanças é a implementação do **let** para variáveis e **const** para constantes. Depois de criada uma constante, se você tentar passar um novo valor a ela, receberá um erro.

    let minhaVariavel = "Algum valor";
    const minhaConstante = "Algum valor";

Arrow Functions
---------------

As Arrow Functions são uma forma menos verbosa de escrever funções, removendo a keyword **function**.  
  
Fora a estética, o uso das Arrow Functions elimina a necessidade da keyword **this** dentro da função. Desta forma, em determinadas situações é quase obrigatório o uso de Arrow Functions. Para ser sincero, utilize apenas Arrow Functions daqui para frente.

    // Modelo antigo
    function minhaFuncao() {
        return "Valor";
    }

    // Modelo novo
    const minhaFuncao = () => {
        return "Valor";
    }

Podemos receber parâmetros nas funções também, bastando informar seus nomes na assinatura da mesma.

    // Recebendo parâmetros
    const minhaFuncao = (meuParametro) => {
        return meuParametro;
    }

    // Recebendo N parâmetros
    const minhaFuncao = (meuParametro, meuParametro2) => {
        return meuParametro + meuParametro2;
    }

Para finalizar, se sua Arrow Function tem apenas uma linha de retorno, podemos utilizar a chamada “sem corpo -- bodyless”, e se existir apenas um parâmetro de entrada, também não é necessário uso dos parênteses.

    // Bodyless com apenas um parâmetro
    const minhaFuncao = meuParametro =>  meuParametro;

    // Bodylless com N parâmetros
    const minhaFuncao = (meuParametro, meuParametro2) =>  return meuParametro + meuParametro2;

Exports e Imports
-----------------

Outro recurso importante foi a chegada da importação e exportação de módulos e classes, facilitando a modularização dos nossos Apps.  
  
Podemos simplesmente utilizar o **export default** SEUOBJETO para exportar um objeto de uma classe.

    // course.js
    const course = {
        title: "JS Moderno",
        tag: "7199"
    }

    export default course

Ou podemos utilizar o **export const** diretamente no objeto, que é o meio mais comum e que particularmente prefiro.

    // course.js
    export const Course = {
        title: "JS Moderno",
        tag: "7199"
    }

Com tudo exportado, podemos importar os objetos, utilizando o **import**. Para importar objetos exportados utilizando o default, basta criar um nome e fazer sua importação.  
  
Para objetos exportados via const, devemos utilizar seu mesmo nome, e entre colchetes.

    // Importação quando o módulo usa export default
    import course from 'caminho/para/course.js';

    // Importação quando o módulo usa export const
    import { Course } from 'caminho/para/course.js';

Podemos também utilizar alias para ambos os casos, como mostrado no código abaixo.

    // Importação quando o módulo usa export default
    import course from 'caminho/para/course.js';

    // Importação quando o módulo usa export default
    // utilizando nome personalizado
    import meuCurso from 'caminho/para/course.js';

    // Importação quando o módulo usa export const
    import { Course } from 'caminho/para/course.js';

    // Importação quando o módulo usa export const
    // Utilizando nome personalizado
    import { Course as meuCurso } from 'caminho/para/course.js';

E se você tiver muitos exports em uma classe e quiser importa-los todos de uma vez (Somente exports via const), você pode utilizar o wildcard * e criar um alias para ele.

    // Importa todas as constantes do arquivo e cria um alias cursos
    import { * as cursos } from 'caminho/para/course.js';

Classes
-------

Anteriormente havia um suporte a OOP mas com prototypes, algo diferente do comum que temos em outras linguagens. Hoje temos classes e heranças no JS.

    class Payment {
        customer = "";
        pay = () => {
            // pagar...
        }
    }

    const payment = new Payment();

    class CreditCardPayment extends Payment {
        creditCardNumber = "";
        installment = () => {
            // parcelamento
        }
    }

Lembre-se que as classes são sempre passadas por referência, ou seja, nunca será criada uma cópia dela, diferente de como temos com objetos primitivos (string, int, bool).

Spread Operator
---------------

Se arrays e objetos complexos são passados como referência, como fazemos para adicionar propriedades ou elementos a eles?  
  
Para utilizá-lo, basta adicionar “...OBJETOANTERIOR” que anexamos o objeto ou array anterior no novo.  
  
Isto vale tanto para arrays quanto para objetos.

    // Spread Operators em arrays
    const numbers = [1, 2, 3];
    const novo = [...numbers, 4];


    // Spread Operators em objetos
    const course = {
        title: "Curso do balta.io",
        tag: "7198"
    }

    const newCourse = {
        ...course,
        category: "Frontend"
    }

Rest Operator
-------------

O Rest Operator permite que façamos o envio de um array de parâmetros para uma função utilizando os mesmos “...” do Spread.  
  
Uma vez enviado para função como parâmetro, temos ele materializado como um array, podendo inclusive executar métodos como filter, map, entre outros.

    const filter = (...args) => {
        return args.filter(el => el === 1);
    }

    // Deve exibir 1
    console.log(filter(1, 2, 3));

Destructuring
-------------

Utilizamos o Destructuring para extrair elementos de um array ou propriedades de um objeto, diretamente para uma variável.  
  
No caso dos arrays, basta criar um array (literalmente) e igualar a sua lista de objetos. Ele irá mapear cada item da sua lista para seu novo array.  
  
Caso queira pular algum item, é só não criar uma variável e deixar um espaço em branco ou somente a virgula no array.

    const numbers = [1, 2, 3, 4];
    [v1, v2] = numbers;
    // Podemos pular elementos deixando apenas uma virgula
    // Neste caso, estamos pulando o segundo item
    [v1, ,v3] = numbers;

    // Vai exibir 1 e 2
    console.log(v1, v2);

    // Vai exibir 1 e 3
    console.log(v1, v3);

No caso de objetos, basta criar uma variável entre chaves com o mesmo nome de alguma propriedade do objeto.  
  
Caso não haja uma propriedade com este nome, a variável ficará **undefined**.

    const curso = {
        title: "Curso do balta.io",
        tag: "7199"
    }

    {title} = curso;
    {tag} = curso;
    {price} = curso;

    // Vai exibir: Curso do balta.io
    console.log(title);

    // Vai exibir: 7199
    console.log(tag);

    // Vai exibir: Undefined
    // Não existe a propriedade price no curso
    console.log(price);

Map
---

Além do filter que vimos anteriormente, temos também o map, que percorre uma lista de elementos permitindo interagir com eles.  
  
Podemos por exemplo percorrer uma lista de números e dobrar seu valor, armazenando-os em uma nova variável, com poucas linhas de código.

    const numbers = [1, 2, 3, 4];
    const dobro = numbers.map((n)=>{
        return n * 2;
    });

    // Vai exibir [1, 2, 3, 4]
    console.log(numbers);

    // Vai exibir [2, 4, 6, 8]
    console.log(dobro);

Se lembrar-mos que podemos remover os parênteses das Arrow Functions quando temos apenas um parâmetre e utilizar funções sem corpo, o código fica ainda menor.

    const dobro = numbers.map(n => n * 2);

Conclusão
---------

O JavaScript vem evoluindo muito nos últimos anos e deixou de ser aquela linguagem arcaica e abandonada. Existem funcionalidades exclusivas dele e que facilitam muito nossa vida.

Fontes
------

[https://www.academind.com/learn/javascript/reference-vs-primitive-values/](https://www.academind.com/learn/javascript/reference-vs-primitive-values/)