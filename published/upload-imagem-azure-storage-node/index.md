No artigo passado comentei sobre os motivos para se fazer upload de imagem para o Azure, bem como uma implementação da mesma utilizando C#. Neste artigo vou abordar o envio de imagens para o Azure Storage novamente, porém utilizando JavaScript (Node).

**IMPORTANTE:** Não utilize este tutorial para envio de imagens direto do Frontend, ou seja, não deixe nada disso no seu App Angular, React, Vue ou o quer que seja, mantenha isto no Backend. O motivo deste cuidado é que precisaremos da Connection String para nosso Storage e deixar isto no Front é tornar fácil o acesso a esta informação.

Iniciando o projeto
-------------------

Para este exemplo, estou usando uma API básica criada com o Express, se quiser, pode iniciar seu projeto com este comando:  
  
    npm install –g express-generator  
    express minhaapi –no-view  
    cd minhaapi  
    npm install  
  
Isto vai gerar um projeto apenas API, em adicional, vamos criar um controlador (controllers/controller.js) com o seguinte método:

    exports.uploadImage = async (req, res, next) => {
        // TODO
    }

Para finalizar vamos para o arquivo de rotas (routes/index.js) e criar uma nova rota para o método POST apontando para o controlador que criamos:

    var express = require('express');
    var router = express.Router();
    const controller = require('../controllers/controller');

    /* GET home page. */
    router.get('/', function (req, res, next) {
        res.render('index', {
            title: 'Express'
        });
    });

    router.post('/', controller.uploadImage);

    module.exports = router;

Instalando os pacotes
---------------------

Para este caso, vamos utilizar uma lib do Azure (Sim, tem pronto) para facilitar. O time do Azure disponibiliza pacotes para diferentes linguagens, se engana quem acha que é apenas para .NET.  
  
Neste caso vamos utilizar o pacote chamado azure-storage, que pode ser instalado pelo seguinte comando: 

    npm install azure-storage –save  
  
Isto é tudo que precisamos do Azure para fazer upload das nossas imagens.  
  
Em adicional, para gerar nomes aleatórios eu uso sempre o GUID, e tem um pacote que recomendo que é o UUID:  

    npm install uuid –save

Referenciando os Pacotes
------------------------

O primeiro passo é importar os módulos do Azure e UUID que vamos precisar para trabalhar, e insto é feito no topo do nosso controlador, desta forma:

    const uuid = require('uuid/v1');
    const azure = require('azure-storage');

    exports.uploadImage = async (req, res, next) => {
        // TODO
    }

Acessando o Blob Storage
------------------------

A primeira coisa que precisamos para enviar um arquivo para o Azure e a referência de um Blob/Container, e para isto precisamos antes de uma chave de acesso que pode ser obtida no portal do Azure na opção Azure Storage > Seu Storage > Access Keys.  
  
Com a chave em mãos, vamos criar o objeto de refência que vai permitir a transferência da imagem:

    const uuid = require('uuid/v1');
    const azure = require('azure-storage');

    exports.uploadImage = async (req, res, next) => {
        const blobSvc = azure.createBlobService("SEU ACCESS KEY");
    }

Preparando o arquivo
--------------------

Nossa imagem chegará no formato Base64 e este será divido em duas partes, sendo a primeira o tipo da imagem e a segunda a imagem em si.  
  
Além disso precisamos gerar um nome único para esta imagem para não ter problemas de conflito. Desta forma nosso código fica assim:

    const uuid = require('uuid/v1');
    const azure = require('azure-storage');

    exports.uploadImage = async (req, res, next) => {
        const blobSvc = azure.createBlobService("SEU ACCESS KEY");

        // Gera um nome único para o arquivo
        let filename = uuid().toString() + '.jpg';

        // Obtem a imagem em base64 do corpo da requisição
        let rawdata = req.body.image;

        // Separa a hash recebida em duas partes
        let matches = rawdata.match(/^data:([A-Za-z-+\/]+);base64,(.+)$/);

        // Obtém o tipo da imagem
        let type = matches[1];

        // Obtém a imagem em si
        let buffer = new Buffer(matches[2], 'base64');
    }

Salvando a Imagem
-----------------

Com tudo pronto, podemos usar o método CreateBlockBlobFromText do pacote do Azure Storage para fazer upload de uma imagem no formato Base64 para o servidor.  
  
Neste caso estou utilizando Async/Await (Não esqueça do Try/Catch) para aguardar a execução do método, e caso algo dê errado, eu simplesmente defino o nome do arquivo como padrão.  
  
Isto é apenas para o exemplo e precisa ser melhorado no seu código, OK?

    const uuid = require('uuid/v1');
    const azure = require('azure-storage');

    exports.uploadImage = async (req, res, next) => {
        const blobSvc = azure.createBlobService("SEU ACCESS KEY");

        // Gera um nome único para o arquivo
        let filename = uuid().toString() + '.jpg';

        // Obtem a imagem em base64 do corpo da requisição
        let rawdata = req.body.image;

        // Separa a hash recebida em duas partes
        let matches = rawdata.match(/^data:([A-Za-z-+\/]+);base64,(.+)$/);

        // Obtém o tipo da imagem
        let type = matches[1];

        // Obtém a imagem em sí
        let buffer = new Buffer(matches[2], 'base64');

        // Salva a imagem
        await blobSvc.createBlockBlobFromText('SEUCONTAINER', filename, buffer, {
            contentType: type
        }, function (error, result, response) {
            if (error) {
                filename = 'default.png'
            }
        });
    }

Retornando a URL da imagem
--------------------------

Se tudo deu certo, basta retornarmos a URL da imagem na resposta, como mostrado abaixo, que também demonstra nossa versão final do controlador.

    const uuid = require('uuid/v1');
    const azure = require('azure-storage');

    exports.uploadImage = async (req, res, next) => {
        const blobSvc = azure.createBlobService("SEU ACCESS KEY");

        // Gera um nome único para o arquivo
        let filename = uuid().toString() + '.jpg';

        // Obtem a imagem em base64 do corpo da requisição
        let rawdata = req.body.image;

        // Separa a hash recebida em duas partes
        let matches = rawdata.match(/^data:([A-Za-z-+\/]+);base64,(.+)$/);

        // Obtém o tipo da imagem
        let type = matches[1];

        // Obtém a imagem em sí
        let buffer = new Buffer(matches[2], 'base64');

        // Salva a imagem
        await blobSvc.createBlockBlobFromText('SEUCONTAINER', filename, buffer, {
            contentType: type
        }, function (error, result, response) {
            if (error) {
                filename = 'default.png'
            }
        });

        const fileUrl = `https://SEUSTORAGE.blob.core.windows.net/SEUCONTAINER/${filename}`;
        return res.status(200).json({
            url: fileUrl
        });
    }