Em boa parte das aplicações que desenvolvemos, temos o envio de imagens. Hoje com as facilidades que temos no Frontend, é muito simples tratar uma imagem, recortar, aplicar efeitos e depois enviar para o servidor.  
  
O que antes tinha de ser feito todo no Backend, hoje é dividido entre ambos. Porém, ainda temos o trabalho de capturar e armazenar esta imagem, mas onde?

No banco de dados não!
----------------------

Quando comecei o balta.io (na época era ABT – André Baltieri Treinamentos), eu optei pelo mínimo de infraestrutura possível, e hospedava o site, API, app, tudo em um lugar só, na mesma máquina, sem escalonamento nem nada.  
  
Nesta época, eu salvava as imagens em um banco de dados, afinal elas vinham como String da tela (Base64), então era simples.  
  
O problema é que o banco cresce demais em tamanho, as imagens não podem ser reaproveitadas (Não temos uma URL), e sempre que precisamos de uma imagem, tem que ir até o banco.  
  
Fora que sua thread não finaliza enquanto você não envia todas as informações para a tela, e neste caso, se você tem imagens grandes, você segura a thread da sua API e a conexão com o banco abertas enquanto o tráfego acontece.  
  
Acabou que existem maneiras MUITO melhores de se armazenar imagens, como vou mostrar aqui.

Na mesma máquina não!
---------------------

Bom, se não é legal colocar no banco de dados, então vamos salvar em disco, correto? Bem... quase...  
  
Sempre que você salva uma imagem em disco, você está consumindo I/O desta máquina, em adicional, as requisições para esta imagem vão consumir recursos que podem ser vitais para sua API.  
  
Como se tudo isto não bastasse, salvar local (Na mesma máquina) não escala!  
  
Um dos modelos de escalonamento que mais usamos hoje é o horizontal, onde temos máquinas menores e várias delas.  
  
Neste cenário, se nada estiver em uso, pagamos apenas por uma máquina pequena, que está ali em stand-by. Diferente de termos uma máquina mais poderosa, que custa mais caro.  
  
Sendo assim, máquinas são criadas e destruídas a todo tempo, e quando elas são criadas, elas se baseiam em uma imagem, e advinha só... esta imagem contém apenas o SO.  
  
Então é feito um novo build/deploy do seu App e a máquina é disponibilizada, o que significa que nada que estava na memória ou disco da(s) máquina(s) anterior(es) foi copiado junto.  
  
Logo, se você salvou alguma imagem em uma máquina, ela ficou lá, apenas lá, e será destruída junto com a máquina assim que ela não estiver mais em uso.

Por que utilizar um Storage?
----------------------------

Se não recomendamos salvar no banco, e salvar em disco não escala, então para onde podemos correr? Para um Storage... no nosso caso o Azure Storage.  
  
Como o nome já diz os Storages foram feitos para isto, eles armazenam arquivos e disponibilizam com backup, geolocalização, CDN e muito mais.  
  
Os Storages ficam na mesma rede (Grupo de Afinidade) dos nossos Apps, porém rodam independentes, o que significa que não afetam o I/O ou processamento dos mesmos e estão sempre disponíveis... pode cair máquina, pode subir máquina, o Storage permanece lá.  
  
Os Storages tem segurança, você pode obrigar a passagem de um Token para poder acessar um arquivo e todo arquivo tem uma URL, o que o torna rastreável e reutilizável.  
  
Para finalizar os Storages são um dos recursos mais baratos que temos no Azure hoje.

Envio em Base64
---------------

Os Storages suportam envio de arquivos em Array de Bytes, porém, é comum no Frontend trabalharmos com Base64 quando falamos de imagens.  
  
Existem várias bibliotecas para tratamento de imagem, desde recorte até efeitos, e a maioria delas tem como resultado a imagem como Base64.  
  
Se você já tiver o Array de Bytes fica até mais fácil, pulamos um processo no código, mas na maioria das vezes você terminará com uma String.  
  
Em termos de performance, Strings são mais pesadas que Arrays de Bytes para transferência, então se o seu foco é performance, talvez seja legal converter para binário antes de enviar para a API.

Criando o método de Upload
--------------------------

Eu particularmente crio um serviço de Upload de imagem, abstraindo o Azure da minha API, isto é importante para futuras mudanças na API.  
  
Como o envio de imagem está estritamente ligado a infraestrutura, e ela pode mudar, recomendo que faça o mesmo, criando um método separado, em um serviço, na camada de infraestrutura da sua API.  
  
Este método vai fazer o envio da imagem e retornar a URL dela.

    public string UploadBase64Image(string base64Image, string container) { }

Pensando em melhorias, poderíamos até criar uma interface para este serviço, mas não vou cobrir isto aqui.

Gerando o nome do arquivo
-------------------------

Ao tentar enviar uma imagem já existente par ao Storage, você pode se deparar com um erro, ou forçar a sobrescrita da mesma.  
  
Eu particularmente gosto de gerar nomes randômicos para evitar este tipo de problema. Em adicional, o Storage do Azure tem um ótimo cache, o que significa que se você sobrescrever uma imagem, ele possivelmente vai levar algumas horas para refletir esta mudança.

    public string UploadBase64Image(string base64Image, string container) { 
        // Gera um nome randomico para imagem
        var fileName = Guid.NewGuid().ToString() + ".jpg";
    }

Neste caso eu já aproveito também para definir o formato da imagem, que neste caso sempre será JPG, porém você pode extrair esta informação do Base64 que receberá, caso queira salvar no formato correto.

Preparando a imagem
-------------------

Sempre que uma imagem no formato Base64 é enviada, ela contém primeiramente o formato da imagem, seguido pelo formato do Hash, neste caso, Base64.  
  
Porém, para persistir estas informações isto não é necessário, e gera um erro. Temos então que remover esta parte.  
  
Para isto, utilizei um Regex que faz a limpeza destas informações e já deixa tudo pronto para ser enviado.

    public string UploadBase64Image(string base64Image, string container) { 
        // Gera um nome randomico para imagem
        var fileName = Guid.NewGuid().ToString() + ".jpg";
        
        // Limpa o hash enviado
        var data = new Regex(@"^data:image\/[a-z]+;base64,").Replace(base64Image, ""); 
    }

Gerando o Array de Bytes
------------------------

Conforme comentei previamente, para realizar o envio do arquivo, precisamos de um Array de Bytes, e isto pode ser feito utilizando o método Convert.FromBase64String do próprio C#.

    public string UploadBase64Image(string base64Image, string container) { 
        // Gera um nome randomico para imagem
        var fileName = Guid.NewGuid().ToString() + ".jpg";
        
        // Limpa o hash enviado
        var data = new Regex(@"^data:image\/[a-z]+;base64,").Replace(base64Image, ""); 
        
        // Gera um array de Bytes
        byte[] imageBytes = Convert.FromBase64String(data);
    }

Enviando a imagem
-----------------

O primeiro passo é definir para onde vai a imagem. No Azure Storage temos os BLOBs e Containers, que definem o arquivo e “pasta” respectivamente.  
  
Neste caso, estamos recebendo o Container por parâmetro e é importante frisar que este deve existir, caso contrário será retornado um erro.  
  
Com tudo definido, podemos utilizar um MemoryStream para ler o Array de Bytes e fazer o upload deles para o Azure.

    public string UploadBase64Image(string base64Image, string container) { 
        // Gera um nome randomico para imagem
        var fileName = Guid.NewGuid().ToString() + ".jpg";
        
        // Limpa o hash enviado
        var data = new Regex(@"^data:image\/[a-z]+;base64,").Replace(base64Image, ""); 
        
        // Gera um array de Bytes
        byte[] imageBytes = Convert.FromBase64String(data);
        
        // Define o BLOB no qual a imagem será armazenada
        var blobClient = new BlobClient("SUA CONN STRING", container, fileName);

        // Envia a imagem
        using(var stream = new MemoryStream(imageBytes)) {
            blobClient.Upload(stream);
        }
    }

Obtendo a URL da imagem enviada
-------------------------------

Por fim, tudo que precisamos fazer é retornar a URL da imagem que acabamos de enviar.

    public string UploadBase64Image(string base64Image, string container) { 
        // Gera um nome randomico para imagem
        var fileName = Guid.NewGuid().ToString() + ".jpg";
        
        // Limpa o hash enviado
        var data = new Regex(@"^data:image\/[a-z]+;base64,").Replace(base64Image, ""); 
        
        // Gera um array de Bytes
        byte[] imageBytes = Convert.FromBase64String(data);
        
        // Define o BLOB no qual a imagem será armazenada
        var blobClient = new BlobClient("SUA CONN STRING", container, fileName);

        // Envia a imagem
        using(var stream = new MemoryStream(imageBytes)) {
            blobClient.Upload(stream);
        }
        
        // Retorna a URL da imagem
        return blobClient.Uri.AbsoluteUri;
    }

Pronto, temos upload de imagem para o Azure com C# em 20 linhas de código.