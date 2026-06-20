No dia 25 de setembro tivemos o evento oficial de lançamento do .NET Core 3.0 e neste artigo eu vou resumir as novidades e deixar alguns vídeos, dos lançamentos e de uma live que fizemos sobre o assunto.

Suporte ao C# 8.0
-----------------

O C# é uma das minhas linguagens favoritas, e com certeza uma das melhores e mais completas do mercado. Embora a linguagem já esteja bem madura, sempre estão vindo novidades boas, e ela chegou à versão 8.0, com seu suporte pelo .NET Core 3.0.

.NET Standard 2.1
-----------------

Com a chegada do .NET Core 3.0 também veio o .NET Standard 2.1, uma Surface API para padronizar os projetos. Para usar o .NET Standard 2.1 basta setar o netstandard2.1 no Target Framework do seu arquivo de configuração.  
Quer saber o que cada versão do .NET Standar suporta? Então consulte este link: [https://docs.microsoft.com/pt-br/dotnet/standard/net-standard](https://docs.microsoft.com/pt-br/dotnet/standard/net-standard).

EXE único
---------

Agora Podemos adicionar as tags true ao arquivo de configuração ou utilizar o comando dotnet publish -r win10-x64 /p:PublishSingleFile=true para que os executáveis sejam gerados em um único arquivo. Chega de pastas e vários arquivos para suas Apps, agora pode ficar tudo em um só.

Melhorias no tamando dos Assemblies
-----------------------------------

Agora temos uma tag chamada PublishTrimmed que ao adicionada ao arquivo de configuração do projeto, analisa o IL e remove os Assemblies não utilizados.

ReadyToRun
----------

Agora podemos melhorar o tempo de inicialização dos Apps .NET Core compilando-os no formato ReadyToRun (R2R), que é uma compilação AOT (Ahead of Time).  
Para utilizar a feature basta adicionar as tags true ao arquivo de configuração.

Build e Dependências
--------------------

Agora no Build, todas as dependências externas (NuGet) são copiadas para o seu App. Antes isto acontecia apenas no dotnet publish.

Local Tools
-----------

Agora podemos ter comandos específicos locais, assim como temos os globais, mas apenas locais, para determinada aplicação.

Windows Forms e WPF
-------------------

Chegou finalmente o suporte a aplicações Windows Forms e WPF (Apenas Windows) com o .NET Core 3.0. Tivemos atualizações para suportá-las no Visual Studio 2019 também, e a adição de dois comandos:  
dotnet new wpf  
dotnet new winforms

MSIX
----

MSIX é um novo formato de pacote de aplicativos do Windows. Ele pode ser usado para instalar aplicativos (Windows Forms/WPF) do .NET Core 3.0 no Windows 10.

Compatibilidade com HTTP/2
--------------------------

O System.Net.Http.HttpClient agora vem com suporte para o HTTP 2!

Docker e melhorias
------------------

Houve melhoras significantes no consumo de memória das apps .NET Core em máquinas Linux e no tamanho também. Em uma demo eles demonstraram uma máquina Alpine com um App .NET Core 3 instalado (RunTime) com apenas 88MB.

Suporte de GPIO para o Raspberry Pi
-----------------------------------

Foram lançados dois pacotes para o NuGet que você pode usar para programação de GPIO:  
System.Device.Gpio  
Iot.Device.Bindings

Suporte a ARM64 no Linux
------------------------

O .NET Core 3.0 adiciona suporte para ARM64 para Linux. Um grande avanço para criação de aplicações para processadores ARM64.

Resumo do Evento – Live ASP.NET Cast
------------------------------------
[Clique aqui para assistir](https://www.youtube.com/watch?v=LiDoUVMkT38)

Vídeos que gostei do .NET Conf
------------------------------
*   [Entity Framework Core 3.0 and Beyond](https://www.youtube.com/watch?v=PXdgyPpfaz4)
*   [The Future of the Blazor on the Client](https://www.youtube.com/watch?v=qF6ixMjCzHA)
*   [Blazor and Azure Functions for Serverless Websites](https://www.youtube.com/watch?v=noG3rxt38VI)
*   [DevOps for the .NET Developers](https://www.youtube.com/watch?v=iQ4HhdxCYN0)
*   [Durable Functions 2.0](https://www.youtube.com/watch?v=aHue7XuNYZA)
*   [Create Interactive Documentations](https://www.youtube.com/watch?v=XWetRp1f5xg)
*   [Azure App Configuration](https://www.youtube.com/watch?v=zRstfC3Nn7M)
*   [Azure Services Every .NET Developer Needs to Know](https://www.youtube.com/watch?v=Z9OdipwevSM)
*   [Diagnostics Improvements on .NET Core 3.0](https://www.youtube.com/watch?v=fkjetdIdcyg)
*   [Secure your NuGet package Ecosystem](https://www.youtube.com/watch?v=UTPct8FKu8I)
*   [Building Cloud Native Apps with .NET Core 3.0 and Kubernetes](https://www.youtube.com/watch?v=A0i5BUoKu6s)
*   [What`s new in SignalR with .NET Core 3.0](https://www.youtube.com/watch?v=dHiETzo6GB8)

Fonte:
------

[https://docs.microsoft.com/pt-br/dotnet/core/whats-new/dotnet-core-3-0](https://docs.microsoft.com/pt-br/dotnet/core/whats-new/dotnet-core-3-0).

<div role="main" id="blog-s1-dotnet-134e3db1eea6c9829db1"></div>
<script type="text/javascript" src="https://d335luupugsy2.cloudfront.net/js/rdstation-forms/stable/rdstation-forms.min.js"></script>
<script type="text/javascript"> new RDStationForms('blog-s1-dotnet-134e3db1eea6c9829db1', 'UA-48664517-12').createForm();</script>