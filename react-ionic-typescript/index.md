No dia 15/10/2019 tivemos a release oficial do Ionic React, que é o suporte ao Framework React utilizando os componentes do Ionic.  
  
Eu sou um grande fã do Ionic, porém sou Time Angular, mas neste artigo vou comentar um pouco sobre esta novidade e mostrar como fazemos um App para listar os cursos do balta com as tecnologias mencionadas.

Lançamento do Ionic React
-------------------------
[Confira o vídeo de lançamento do Ionic React](https://www.youtube.com/watch?v=hjTb4bInons)

Atualizando o Ionic
-------------------

A primeira coisa que precisamos fazer para trabalhar com Ionic+React+TS é instalar ou atualizar o Ionic, e ambos são feitos pelo mesmo comando **'npm install -g ionic'**. No meu caso a versão instalada foi a 5.4.4.  
  
Você pode conferir a versão instalada do seu Ionic utilizando o comando **'ionic --version'**.

Criando o App
-------------

Com o Ionic atualizado, basta executar o comando **'ionic start NOMEDOSEUAPP'** para ser gerado um novo App.  
  
Durante o processo de geração, ele irá te perguntar se deseja criar um projeto com Angular ou React, selecione React.

Estrutura do App
----------------

Ao abrir o App criado no Visual Studio Code, podemos notar uma estrutura simples de pastas, similar a criada pelo **'create-react-app'**, conhecido CLI do React.  
  
Ele também já configura nosso App para usar TypeScript e arquivos TSX, que contém toda estrutura (Comportamento, Estilos e Marcacão) no mesmo arquivo.  
  
Temos então no arquivo **App.tsx** a estrutura inicial da nossa aplicação, com a chamada para o carregamento da Home.tsx nas definições de rotas.

    import React from 'react';
    import { Redirect, Route } from 'react-router-dom';
    import { IonApp, IonRouterOutlet } from '@ionic/react';
    import { IonReactRouter } from '@ionic/react-router';
    import Home from './pages/Home';

    /* Core CSS required for Ionic components to work properly */
    import '@ionic/react/css/core.css';

    /* Basic CSS for apps built with Ionic */
    import '@ionic/react/css/normalize.css';
    import '@ionic/react/css/structure.css';
    import '@ionic/react/css/typography.css';

    /* Optional CSS utils that can be commented out */
    import '@ionic/react/css/padding.css';
    import '@ionic/react/css/float-elements.css';
    import '@ionic/react/css/text-alignment.css';
    import '@ionic/react/css/text-transformation.css';
    import '@ionic/react/css/flex-utils.css';
    import '@ionic/react/css/display.css';

    /* Theme variables */
    import './theme/variables.css';

    const App: React.FC = () => (
        <IonApp>
            <IonReactRouter>
            <IonRouterOutlet>
                <Route path="/home" component={Home} exact={true} />
                <Route exact path="/" render={() => <Redirect to="/home" />} />
            </IonRouterOutlet>
            </IonReactRouter>
        </IonApp>
    );

    export default App;

Suporte a TypeScript nativo
---------------------------

Como podemos notar, utilizamos o TSX ao invés do JSX, o que significa que estamos utilizando o Superset TypeScript, que particularmente eu gosto bastante.  
  
Tudo relacionado ao TypeScript já vem configurado, mas pode ser facilmente modificado no arquivo tsconfig.json.  
  
Lembre-se que além da tipagem o TS permite transpilarmos para versões anteriores do EcmaScript, ou seja, podemos utilizar features do futuro e transpilar para Browsers mais antigos. No caso, vem configurado sempre o ES5.

Instalando o Axios
------------------

Para este App vamos fazer uma requisição HTTP a API do balta.io para listar os cursos na tela, e para isto vamos utilizar o Axios, que vai gerenciar nossas requisições HTTP.  
  
Sendo assim, vamos executar o comando **'npm install axios --save'** para instalá-lo e armazenar sua referência no packages.json.  
  
Com isto feito, vamos mover para Home.tsx e importar o Axios no cabeçalho.

    import { IonContent, IonHeader, IonPage, IonTitle } from '@ionic/react';
    import React from 'react';
    import axios from 'axios';

Estados
-------

Movendo adiante temos a estrutura do nosso componente, que nada mais é do que uma classe que implementa o React.FC (Function Components).  
  
Nele vamos declarar uma constante contendo os cursos (courses) e a função que vamos utilizar para populá-los (setData).  
  
Em seguida fazemos uso do **useState** (Não esqueça de importá-lo no topo) para gerenciar o estado deste componente.

    import { IonContent, IonHeader, IonPage, IonTitle } from '@ionic/react';
    import React, { useState } from 'react';
    import axios from 'axios';

    const Home: React.FC = () => {
        const [courses, setData] = useState([]);
        
        ...
        
    };

    export default Home;

React Hooks – useEffects
------------------------

Com os estados definidos, vamos utilizar o **useEffect**, um Hook para fazer a requisição na API assim que o componente é gerado.  
  
Não esqueça de importar o useEffect no topo da página.

    import { IonContent, IonHeader, IonPage, IonTitle } from '@ionic/react';
    import React, { useState, useEffect } from 'react';
    import axios from 'axios';

    const Home: React.FC = () => {
        const [courses, setData] = useState([]);

        useEffect(() => {
            axios.get('https://api.balta.io/v1/courses')
            .then(res => {
                setData(res.data);
            })
            .catch(err => {
                console.log(err);
            })
        }, []);

        ...
    
    };

    export default Home;

Na função useEffect fazemos a chamada a API utilizando o Axios (axios.get), manipulando o resultado posteriormente com then ou catch.  
  
No caso de erros, não estou tratando nada aqui, mas fica a seu critério melhorar isto ou não.  
  
No then, após retornar os dados, fazemos uso do setData, que definimos previamente no estado para popular os cursos.  
  
Tudo pronto, nossa lista de cursos obtidas da API já está pronta para ser utilizada no HTML.

Componentes do Ionic
--------------------

Diferente do Angular, aqui cada componente deve ser importado, então à medida que formos utilizando os componentes, é só executar 'CTRL+.' sobre eles para fazer a importação.  
  
Outra diferença está na chamada dos componentes, onde temos 'ion-page' no Angular e 'IonPage' no React, mas seguindo a convenção que o React utiliza, isto é correto.  
  
A estrutura de uma página Ionic é composta por um IonPage e um IonContent, podendo conter um IonHeader e IonFooter também, mas neste caso, vamos focar no IonContent.  
  
Dentro dele vamos utilizar a função Map do JS/TS para percorrer os cursos e exibi-los na tela.

    import { IonContent, IonHeader, IonPage, IonTitle, IonToolbar, IonCard, IonItem, IonLabel } from '@ionic/react';
    import React, { useState, useEffect } from 'react';
    import axios from 'axios';

    const Home: React.FC = () => {
        const [courses, setData] = useState([]);

        useEffect(() => {
            axios.get('https://api.balta.io/v1/courses')
            .then(res => {
                setData(res.data);
            })
            .catch(err => {
                console.log(err);
            })
        }, []);

        return (
            <IonPage>
                <IonContent>
                    <br />
                    {courses.map((course: any) => (
                    <IonCard key={course.tag}>
                        <img src={`https://baltaio.blob.core.windows.net/social/${course.tag}.png`}></img>
                        <IonItem>
                        <IonLabel>
                            <h3>{course.tag}</h3>
                            <p>{course.title}</p>
                        </IonLabel>
                        </IonItem>
                    </IonCard>
                    ))}
                </IonContent>
            </IonPage>
        );
    };

    export default Home;

Pronto, temos uma aplicação funcional, lendo dados de uma API, feita com Ionic, React e TypeScript.

Conclusão
---------

Para mim é uma ótima novidade o suporte a React no Ionic, significa o Ionic ainda mais forte com apoio do pessoal de React.  
  
Confesso que tenho que me dedicar mais a React, sempre foquei muito em Angular, que é meu público alvo, porém gostei muito do que vi com Ionic e TS! Estou empolgado!