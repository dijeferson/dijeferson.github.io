---
title: "Configurando Jenkins Para Dotnet"
date: 2015-11-19T19:44:47+02:00
---

## O que é o Jenkins?

Jenkins é um sistema de integração continua (continuous integration) e entrega continua (continuous delivery) utilizado para automação de builds, relatórios de erros, execução automatizada de testes unitários, deploy automático, e muito mais.

## Por que o Jenkins?

Existem alguns motivos para escolher o Jenkins como seu servidor de CI ao invés de outras alternativas do mercado:

1. Completamente gratuito.
2. Instalação e configuração simples.
3. Extensível e customizável
4. Existe um plugin para quase tudo que você precisar / imaginar.
5. E caso não exista um plugin, é relativamente simples desenvolver o seu próprio.

## Instalando e configurando

Instalar e configurar o Jenkins para .NET não é tão complexo como pode parecer. Vamos aos passos:

1. Entrar [aqui](http://mirrors.jenkins-ci.org/windows/latest) e baixar a última versão disponível do Jenkins-CI para Windows.
2. Dezipar e instalar o .msi com as opções padrão
3. O Jenkins vai ser instalado na porta 8080 e na pasta *C:\Program Files (x86)\Jenkins*
4. Abrir o link **Gerenciar Plugins**. Estou usando o GitHub como meu SCM padrão, logo instalei os seguintes plugins: - Git Client Plugin

- Git Changelog
- SCM API Plugin
- GIT Plugin
- Git Parameter Plug-in
- GitHub API Plugin
- GitHub authentication Plugin
- VSTest Runner
- Change Assembly Version
- Mailer Plugin

1. Reiniciar Jenkins com [http://localhost:8080/restart](http://localhost:8080/restart) ou [http://localhost:8080/safeRestart](http://localhost:8080/safeRestart)
2. Se não houve nenhum problema, parabéns, seu Jenkins está instalado e rodando bem.

## Criando um build job

Uma das tarefas mais importantes do Jenkins é fazer o build periódico do seu repositório. Vamos criar um *build job* passo-a-passo:

Do lado esquerdo, no menu, clique em **New Item**.

![2015-11-18-Jenkins-CI-1](https://s3-eu-west-1.amazonaws.com/jefersonsantos-blog/2018/03/2015-11-18-Jenkins-CI-1.png)

Ao clicar em **New Item** são exibidas as opções de projetos disponíveis no Jenkins. Vamos utilizar a opção **Freestyle Project**. No Campo **Item Name** insira o nome do seu projeto:

![2015-11-18-Jenkins-CI-2](https://s3-eu-west-1.amazonaws.com/jefersonsantos-blog/2018/03/2015-11-18-Jenkins-CI-2.png)

Dependendo dos plugins que você instalou no Jenkins, podem aparecer algumas opções diferentes, então vamos considerar somente os plugins que foram descritos neste post.

Na área **Source Code Management** selecione o SCM que vocês está utilizando. No meu caso é o Git:

![2015-11-18-Jenkins-CI-3](https://s3-eu-west-1.amazonaws.com/jefersonsantos-blog/2018/03/2015-11-18-Jenkins-CI-3.png)

Ao adicionar um repositório você pode, caso seja um repositório privado, adicionar as respectivas credenciais.

É possível também dizer qual o branch (ou mais de um) específico que se deseja fazer o build.

Logo em seguida temos a configuração de **Build Triggers**. São esses os triggers que disparam o build automatizado. Vamos utilizar a opção **Poll SCM** e não vamos adicionar nenhum dado no schedule (vamos alterar isso mais tarde).

Agora devemos adicionar os **Build Steps** que são as ações que são realmente executadas quando o build é iniciado.

Vamos adicionar um step de **Build a Visual Studio project or solution using MSBuild**, e utilizar os parametros:
 – MSBuild Version: (Default)
 – MSBuild File: Path para o arquivo que será compilado (.sln ou .csproj)
 – Command Line Arguments: Sempre uso como padrão as configurações a seguir para compilar em release e fazer o clean e depois o build : `/p:configuration="Release" /target:Clean;Build`


![2015-11-18-Jenkins-CI-4](https://s3-eu-west-1.amazonaws.com/jefersonsantos-blog/2018/03/2015-11-18-Jenkins-CI-4.png)

> **Importante 1:** É necessário que o path para o MSBuild.exe esteja na variável global Path, ou que esteja setado nas configurações do Jenkins o path completo do executável, caso contrário não irá compilar.

> **Importante 2:** É necessário que o MSBuild esteja instalado no servidor. A forma mais simples fazer isso é instalar o Visual Studio e as SDKs que você pretende usar para compilar seu código.

Logo em seguida, abaixo das ações de build temos as ações de post-build. Neste exemplo vamos adicionar somente a ação de **Archive the artefacts**, ou seja, o resultado do build será adicionado a um arquivo zip. Desta forma podemos ter um zip por build.

![2015-11-18-Jenkins-CI-5](https://s3-eu-west-1.amazonaws.com/jefersonsantos-blog/2018/03/2015-11-18-Jenkins-CI-5.png)

Lembrando que o parâmetro **Files to Archive** sempre leva em consideração o caminho da pasta *workspace* (se você fez a instalação padrão então deve estar em  `\Program Files (x86)\Jenkins\workspace`).

Ao concluir de configurar seu job, clique em save.

Ao clicar em save, somos direcionados para a dashboard do job, onde temos a opção de forçar a execução dele clicando em **Build Now**.

![2015-11-18-Jenkins-CI-6](https://s3-eu-west-1.amazonaws.com/jefersonsantos-blog/2018/03/2015-11-18-Jenkins-CI-6.png)

Se tudo foi configurado corretamente então o resultado é *success*, que é indicado por um icone redondo azul (no meu caso é verde por causa de um plugin).

Ao clicar no build, podemos ver detalhes como Console Output, Commit que foi compilado, a mensagem de commit, trigger que iniciou o build, o arquivo de artefacts, e tudo mais que foi configurado.

### Automatizando os Builds

Nas configurações selecionamos o trigger **Pool SCM** mas não adicionamos nenhum schedule. Este trigger faz consultas periódicas ao SCM (no nosso caso o git) e verifica se houveram commits. Em caso positivo, então é realizado um pull e o build é iniciado. Veja abaixo alguns exemplos de schedule:

- A cada 5 minutos: `H/5 * * * *`
- A cada 30 minutos: `H/30 * * * *`
- A cada 1 hora: `* H/1 * * *`
- A cada minuto: `* * * * *`

> Os schedules do Jenkins seguem o padrão dos crontabs

## Conclusão

O Jenkins é muito mais poderoso do que o que foi descrito neste post. Com ele é possível fazer o delivery dos artefatos de build, enviar notificações para o time com o status do build via email, slack, e outros, e com ele é possível até mesmo executar os testes unitários do seu projeto e enviar o resultado ao time. Tudo isso com a simplicidade de configuração dos plugins.
