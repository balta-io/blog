Neste artigo iremos falar um pouco sobre o .NET CLI e os seu principais comandos.

Sumário
1. [Introdução.](#introducao)
2. [O que é .NET CLI?](#oqueedotnetcli)
3. [Como usamos a .NET CLI?](#comousamosadotnetcli)
4. [Comando **dotnet**:](#comandodotnet)
5. [Opções para o comando **dotnet** (driver).](#opcoesparaocomandodotnet)
6. [Comandos gerais.](#comandosgerais)
7. [Comandos para referências de projeto.](#comandosparareferênciasdeprojeto)
8. [Comandos para pacotes NuGet.](#comandosparapacotesnuget)
9. [Comandos globais de ferramentas.](#comandosglobaisdeferramentas)
11. [Opções do SDK para executar um comando.](#opçõesdosdkparaexecutarumcomando)
12. [Sujestão de conteúdo relacionado:](#sujestaodeconteudorelacionado)
13. [Referências.](#referencias)


*******

<div id='introducao'></div> 

## Introdução

Cli, Comand Line Interface, ou Interface de Linha de Comando, é uma forma de interagir com programas atravéz de comandos em forma de texto. 
Cada sistema operacional possui um programa padrão (shell, console, ou terminal) para interpretar esses comandos em texto, e executar as tarefas 
que são específicas a cada comando. Um Shell Cli se inicia com uma sequência de caracteres, a qual chamamos de prompt de comando,  indicando 
qual a prontidão para aceitar comandos. O prompt pode conter as informações: 

- Nome do computador (Hostname) ou usuário (username), indicando quem irá executar o comando; 
- Caminho (path) do diretório atual (directory), pasta atual (folder) ou arquivo atual (file), indicando onde o comando será executado; 
- E é comumente terminado pelos caracteres '$', '%', '#', ':', '>', indicando o previlégio sobre o qual as permissões serão definidas para o tipo 
de comando a ser executado, ou seja, se a execução do comando será permitida ou não, dependendo de quem e de onde o comando irá ser 
executado.

![image-1]()

<div id='oqueedotnetcli'></div> 

## O que é .NET CLI?

.NET CLI, .NET Command Line Interface, ou Interface de Linha de Comando do .NET, é a ferramenta usada para desenvolver, criar, executar e publicar 
aplicativos .NET, multiplataforma, atravéz de comandos de texo em um console. Ela está inclusa na SDK do .NET, cujo o driver da CLI é chamado de dotnet, 
e tem duas responsabilidades:

- Executar um aplicativo, o qual deve ser dependente da estrutura do .NET, ou seja, que inclui somente seu aplicativo e suas dependências, o que 
implica na necessidade da instalação do Runtime do .NET separadamente, para possibilitar a execução do nosso programa.

- Executar um comando, o qual será executado a partir da pasta, ou diretório, especificado no prompt.

<div id='comousamosadotnetcli'></div> 

### Como usamos a .NET CLI?

Para ter acesso à .NET CLI, e aos comando "**dotnet**", devemos ter instalado a SDK do .NET. Disponível em [https://dotnet.microsoft.com/en-us/](https://dotnet.microsoft.com/en-us/), 
e você também pode encontrar maiores informações em nosso artigo [.NET - Instalação, Configuração e Primeiros Passos](https://balta.io/blog/dotnet-instalacao-configuracao-e-primeiros-passos).
Após a instalação, os comandos poderão ser executados em um terminal (console). Existem opções de terminais para Windows 
(Windows Power Shel, Windows Terminal, etc), Mac (ITerm2, Hyper, etc), ou Linux (Gnome Terminal, Terminal Xfce, etc).

Assim, no terminal, sem a necessidade do previlégio de administrador, devemos navegar até a pasta onde vamos trabalhar, atravéz do comando "cd" 
seguido do caminho da pasta. Lembrando que é recomendado armazenarmos os nossos projetos em uma pasta segura, com um caminho curto (raíz 
do computador), o que irá facilitar o acesso. E assim,  iniciando com comando **"dotnet"**, estaremos identificando que os comandos 
a seguir serão executados pelo driver da CLI. do .NET, e é a partir desse ponto que vamos passar as informações que irão estruturar o comando 
**dotnet** a ser executado.

<div id='comandodotnet'></div> 

### Comando **dotnet**:

Após termos executado os comandos que definem o caminho no prompt de comandos, iremos estruturar os comandos da CLI do .NET, sempre iniciando
com a referência ao driver da CLI .NET, com o texto **"dotnet"** , e, separados com espaço, seguimos com o comando  a ser executado, o qual é 
implementado da mesma forma que aplicativos de console, seguindo a convenção: **"dotnet"** {comando} {opção} {argumento}. O comando 
também pode receber instruções para as opções, cujos valores podem ser passados ao comando invocado,  e os argumentos pertencentes a ele.

```
dotnet new classlib --framework "netstandard2.0" -o MyLibrary
```

Neste exemplo estamos criando um novo projeto, através do comando **new**, o qual vai receber a instrução para a optar por um projeto do tipo 
**classlib** (biblioteca de classes), com os argumentos: **--framework "netstandard2.0"**, onde **--framework** ou **--f** é o argumento 
referenciando o framework .NET Standard versão 2.0; e **-o MyLibrary**, onde **-o** ou **--output** é o argumento especificando o diretório 
MyLirary.

<div id='#opcoesparaocomandodotnet'></div> 

### Opções para o comando **dotnet** (driver).

**dotnet --info** => Imprime informações detalhadas sobre uma instalação do .NET e o ambiente do computador, como o sistema operacional atual, e 
o SHA do commit da versão do .NET.

**dotnet --version** => Imprime a versão do SDK do .NET usado pelos comandos dotnet. Inclui os efeitos de qualquer global.json

**dotnet --list-runtimes** => Imprime uma lista dos runtimes do .NET instalados. Uma versão x86 do SDK lista apenas runtimes x86 e uma versão x64 do 
SDK lista apenas runtimes x64.

**dotnet --list-sdks** => Imprime uma lista dos SDKs do .NET instalados.

**dotnet -? | dotnet -h | dotnet --help** => Imprime uma lista de comandos disponíveis.

<div id='comandosgerais'></div> 

### Comandos gerais.

**[dotnet build](https://docs.microsoft.com/pt-br/dotnet/core/tools/dotnet-build)** => Cria um aplicativo .NET.

**[dotnet clean](https://docs.microsoft.com/pt-br/dotnet/core/tools/dotnet-clean)** => Limpa saídas de build no terminal.

**[dotnet help](https://docs.microsoft.com/pt-br/dotnet/core/tools/dotnet-help)** => Mostra uma documentação mais detalhada online para o comando.

**[dotnet new](https://docs.microsoft.com/pt-br/dotnet/core/tools/dotnet-new)** => Inicializa um projeto do C# ou F# de um modelo especificado.

**[dotnet restore](https://docs.microsoft.com/pt-br/dotnet/core/tools/dotnet-restore)**	=> Restaura as dependências para um determinado aplicativo.

**[dotnet run](https://docs.microsoft.com/pt-br/dotnet/core/tools/dotnet-run)** => Executa o aplicativo do caminho especificado.

**[dotnet sln](https://docs.microsoft.com/pt-br/dotnet/core/tools/dotnet-sln)** => Opções para adicionar, remover e listar projetos em um arquivo de solução.

**[dotnet test](https://docs.microsoft.com/pt-br/dotnet/core/tools/dotnet-test)** =>	Executa testes usando um executor de teste.

<div id='comandosparareferênciasdeprojeto'></div> 

### Comandos para referências de projeto.

**[dotnet add reference](https://docs.microsoft.com/pt-br/dotnet/core/tools/dotnet-add-reference)** => Adiciona uma referência ao projeto.

**[dotnet list reference](https://docs.microsoft.com/pt-br/dotnet/core/tools/dotnet-list-reference)** => Lista referências ao projeto.

**[dotnet remove reference](https://docs.microsoft.com/pt-br/dotnet/core/tools/dotnet-remove-reference)** => Remove uma referência ao projeto.

<div id='comandosparapacotesnuget'></div> 

### Comandos para pacotes NuGet.

**[dotnet add package](https://docs.microsoft.com/pt-br/dotnet/core/tools/dotnet-add-package)** => Adiciona um pacote NuGet.

**[dotnet remove package](https://docs.microsoft.com/pt-br/dotnet/core/tools/dotnet-remove-package)** => Remove um pacote NuGet.

<div id='comandosglobaisdeferramentas'></div> 

### Comandos globais de ferramentas. 

**Ferramentas locais**

**[dotnet tool install](https://docs.microsoft.com/pt-br/dotnet/core/tools/dotnet-tool-install)** => Instala uma ferramenta em seu computador.

**[dotnet tool list](https://docs.microsoft.com/pt-br/dotnet/core/tools/dotnet-tool-list)** => Lista todas as ferramentas globais, caminhos de ferramentas,  ou ferramentas locais instaladas no computador.

**[dotnet tool search](https://docs.microsoft.com/pt-br/dotnet/core/tools/dotnet-tool-search)** => Pesquisa NuGet.org em busca de ferramentas que tenham o item da pesquisa especificado em seu nome ou metadados.

**[dotnet tool uninstall](https://docs.microsoft.com/pt-br/dotnet/core/tools/dotnet-tool-uninstall)** => Desinstala uma ferramenta do computador.

**[dotnet tool update](https://docs.microsoft.com/pt-br/dotnet/core/tools/dotnet-tool-update)** => Atualiza ferramentas instaladas em seu computador.

**Ferramentas adicionais**

**[dotnet ef](https://docs.microsoft.com/pt-BR/ef/core/cli/dotnet)** =>	Ferramentas de linha de comando do Entity Framework Core.

**[dotnet user-secrets](https://docs.microsoft.com/pt-BR/aspnet/core/security/app-secrets?view=aspnetcore-6.0&tabs=windows)** => Gerencia os segredos do usuário de desenvolvimento.

**[dotnet watch](https://docs.microsoft.com/pt-br/dotnet/core/tools/dotnet-watch)** => Um recurso que acompanha a execução do aplicativo e quando detecta alterações no código-fonte, reinicia ou recarrega a execução.

<div id='opçõesdosdkparaexecutarumcomando'></div> 

### Opções do SDK para executar um comando.

dotnet {comando} -d | dotnet {comando} --diagnostics => Habilita a saída de diagnóstico.

dotnet {comando} -v | dotnet {comando} --verbosity <LEVEL> => Define o nível de detalhes do comando. Os valores são q (quiet), m (minimal), 
n (normal), d (detailed) e diag (diagnostic). Não suporta todos os comandos, consulte a página específica do comando e verifique a disponibilidade.

dotnet {comando} -? | dotnet {comando} -h | dotnet {comando} --help => Imprime a documentação para um determinado comando.

<div id='sujestaodeconteudorelacionado'></div> 

### Sujestão de conteúdo relacionado:

[Criando projetos .NET via console.](https://balta.io/blog/criando-projetos-dotnet-via-console)

[.NET - Instalação, Configuração e Primeiros Passos](https://balta.io/blog/dotnet-instalacao-configuracao-e-primeiros-passos)

[Windows Terminal](https://balta.io/blog/windows-terminal)

<div id='referencias'></div> 

## Referências

[Fundamentos do C#, balta.io](https://balta.io/cursos/fundamentos-csharp)

[https://docs.microsoft.com/pt-br/dotnet/core/tools/](https://docs.microsoft.com/pt-br/dotnet/core/tools/)

[https://docs.microsoft.com/pt-br/dotnet/core/tools/dotnet](https://docs.microsoft.com/pt-br/dotnet/core/tools/dotnet)



