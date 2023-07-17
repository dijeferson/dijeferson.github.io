---
title: "Usando TouchId como Sudo no Terminal"
date: 2017-11-27T14:54:11+02:00
---

Desde que o Macbook Pro com Touch Bar foi lançado em 2016, uma feature que esperei por muito tempo foi o Touch ID (ou qualquer outro tipo de biometria) no Mac.

A praticidade de utilizar um mecanismo biométrico como senha em um notebook não se compara a outras estratégias de segurança como Yubikeys, Smartcards, ou tokens.

O Touch Id funciona muito bem no MacOS mas há uma situação em que ele não é utilizado: no terminal.

Uso o terminal do MacOS (mais precisamente o iTerm2) diariamente e isso me incomodava desde que troquei o antigo e cansado Macbook Pro 2011 pelo Macbook Pro 2016 com Touch Bar.

Esta semana sem querer encontrei o seguinte tweet do usuário [@cabel](http://twitter.com/cabel):

<br />

<center>

<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">Pro MacBook Pro Tip: have a Touch Bar with Touch ID? If you edit /etc/pam.d/sudo and add the following line to the top…<br><br>auth sufficient pam_tid.so<br><br>…you can now use your fingerprint to sudo!</p>&mdash; Cabel Sasser (@cabel) <a href="https://twitter.com/cabel/status/931292107372838912?ref_src=twsrc%5Etfw">November 16, 2017</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<br />

</center>

Testei e realmente funciona (para a maioria dos casos).

### Atenção
1. Se você não tem certeza se quer ativar, melhor não ativar.
2. Testei no MacOS High Sierra (10.13.1). Pelo que pesquisei não funciona no MacOS Sierra nativamente, é necessário compilar e instalar o arquivo pam (veja [aqui](http://www.oradba.ch/2017/01/using-touchid-for-sudo-on-macos-sierra/)).
3. Não testei, mas há relatos de que o Touch Id não é transmitido por SSH, logo, se você acessa o Macbook Pro via SSH, e executa sudo's, há uma grande possibilidade de não funcionar.

### Passo-a-Passo

1. Abra o terminal e execute o comando
```
sudo vim /etc/pam.d/sudo
```

<center>

![Screen-Shot-2017-11-29-at-00.35.28](https://s3-eu-west-1.amazonaws.com/jefersonsantos-blog/2017/11/Screen-Shot-2017-11-29-at-00.35.28.png)

</center>

2. Adicione uma  nova linha ao arquivo
```
auth sufficient pam_tid.so
```

<center>

![Screen-Shot-2017-11-29-at-00.35.32](https://s3-eu-west-1.amazonaws.com/jefersonsantos-blog/2017/11/Screen-Shot-2017-11-29-at-00.35.32.png)

</center>

3. Salve o arquivo:
4. Reiniciar terminal.

A partir de agora, toda vez que executar um comando sudo será solicitado o Touch Id:

<center>

![Screen-Shot-2017-11-29-at-00.36.04](https://s3-eu-west-1.amazonaws.com/jefersonsantos-blog/2017/11/Screen-Shot-2017-11-29-at-00.36.04.png)

</center>

### UPDATE (2017/11/29):

Quando o popup solicitando o touch id é exibido é possível clicar em "_Use Password ..._" e inserir a senha no popup, ou cancelar o popup e digitar a senha no terminal normalmente.

<center>

![Screen-Shot-2017-11-29-at-00.37.31](https://s3-eu-west-1.amazonaws.com/jefersonsantos-blog/2017/11/Screen-Shot-2017-11-29-at-00.37.31.png)

</center>

Quando utilizado o Touch Id para autorizar um `sudo`, a autorização permanece ativa por algum tempo (provavelmente 15 minutos como a App Store). Então os comandos `sudo` subsequentes **já estarão pré aprovados sem necessidade de senha**. Isso é ótimo para acelerar o trabalho, mas péssimo caso seja inserido um comando incorreto.