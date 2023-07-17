---
title: "Golang: Preparando o Ambiente de Desenvolvimento"
date: 2019-06-27T14:46:59+02:00
---

Para iniciar o desenvolvimento com Go é necessário preparar o ambiente que utilizaremos para desenvolver. O guia a seguir foi criado com base em macOS, mas pode também ser utilizado no Linux. Para saber mais sobre como instalar em outras plataformas consulte a [seção de Downloads do site oficial do Golang](https://golang.org/dl/).

1. Instale o compilador do Go utilizando Homebrew:

```bash
brew install go
```

Alternativamente, baixe o pacote de instalação diretamente do [site oficial do Golang](https://golang.org/dl/)

2. Configurar os diretórios e variáveis de ambiente

- Crie um diretório para armazenar os módulos, códigos-fonte, binários, etc utilizados pelo Go. Este será o seu workspace.

```bash
mkdir -p ~/Developer/go
```

- No meu caso, eu uso o arquivo `.profile` para armazenar minhas configurações de ambiente, logo:

```bash
# Go-lang specifics
# Go Workspace directory
export GOPATH=$HOME/Developer/go
# This is the folder where Go is installed by homebrew
export GOROOT=/usr/local/opt/go/libexec
export PATH=$PATH:$GOPATH/bin:$GOROOT/bin
```

- Em seguida, lembre-se de reiniciar seu terminal ou recarregar o profile:

```bash
source ~/.profile
```

3. A IDE que tenho utilizado e gostado muito é o [VSCode](https://code.visualstudio.com). Além de gratuito e multiplataforma, é muito mais leve e rápido do que o [Goland da Jetbrains](https://www.jetbrains.com/go/). Se ainda não tiver instalado o Visual Studio Code, agora é o momento.

4. Instale a extensão _Go_ fornecida pela Microsoft ([Go - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-vscode.Go)). Esta extensão vai permitir sintax highlighting, intellisence (autocomplete), debugger, entre outras excelentes funcionalidades.

_Opcional_: Instale também a extensão [Code Runner](https://github.com/formulahendry/vscode-code-runner). Esta extensão facilita a execução de codigo.

5. Após instalar a extensão (e reiniciar o VSCode), pressione `⌘ + Shift + P` (Command + Shift + P) e digite `Go: Install/Update Tools`. _Nota: Caso o VSCode pergunte qual tool você quer instalar, selecione todas._

No terminal do VSCode você deve ter um output similar ao seguinte:

```plaintext
Installing 17 tools at ~/Developer/go/bin
  gocode
  gopkgs
  go-outline
  go-symbols
  guru
  gorename
  dlv
  gocode-gomod
  godef
  goreturns
  golint
  gotests
  gomodifytags
  impl
  fillstruct
  goplay
  godoctor

Installing github.com/mdempsky/gocode SUCCEEDED
Installing github.com/uudashr/gopkgs/cmd/gopkgs SUCCEEDED
Installing github.com/ramya-rao-a/go-outline SUCCEEDED
Installing github.com/acroca/go-symbols SUCCEEDED
Installing golang.org/x/tools/cmd/guru SUCCEEDED
Installing golang.org/x/tools/cmd/gorename SUCCEEDED
Installing github.com/go-delve/delve/cmd/dlv SUCCEEDED
Installing github.com/stamblerre/gocode SUCCEEDED
Installing github.com/rogpeppe/godef SUCCEEDED
Installing github.com/sqs/goreturns SUCCEEDED
Installing golang.org/x/lint/golint SUCCEEDED
Installing github.com/cweill/gotests/... SUCCEEDED
Installing github.com/fatih/gomodifytags SUCCEEDED
Installing github.com/josharian/impl SUCCEEDED
Installing github.com/davidrjenni/reftools/cmd/fillstruct SUCCEEDED
Installing github.com/haya14busa/goplay/cmd/goplay SUCCEEDED
Installing github.com/godoctor/godoctor SUCCEEDED

All tools successfully installed. You're ready to Go :).
```

<br />

🎉 Pronto! Seu ambiente de desenvolvimento está preparado para compilar, testar, e debugar codigo em Go.


Para testar, crie um arquivo `hello.go` com o seguinte conteúdo:


```go
package main

import "fmt"

func main() {
    fmt.Println("Hey, Ho, Let's Go! 🔥")
}
```

#### ➤ **Usando o terminal para compilar e executar**
Abra o terminal e digite:
```bash
$ go run hello.go
```

O output deve ser:

```plaintext
Hey, Ho, Let's Go! 🔥
```

#### ➤ **Usando Code Runner para compilar e executar**
E clique no ▶︎ ("Play") no lado superior direito da tela (alternativamente use o atalho: `^ + ⎇ + N` ou `Control + Option + N`), ou em `Debug > Start Without Debugging`.

O output deve ser algo como:


```plaintext
[Running] go run "~/Developer/golang-101/hello.go"
Hey, Ho, Let's Go! 🔥

[Done] exited with code=0 in 0.326 seconds
```