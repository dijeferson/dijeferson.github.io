---
title: "Macbook Pro 2011 GPU (Update 2020)"
date: 2019-12-30T14:44:13+02:00
---

![laptop-overheating](https://s3-eu-west-1.amazonaws.com/jefersonsantos-blog/2019/12/laptop-overheating.jpg)

Alguns meses atrás escrevi um [post](https://jefersonsantos.com/macbook-pro-2011-gpu-consertado/) descrevendo o processo que utilizei para consertar meu MacBook Pro early-2011 15", cuja GPU AMD Radeon (dGPU) havia "morrido". Desde então utilizei muito pouco essa maquina, apenas para tarefas que dependiam de algo específico (como gravar ou ler DVDs) que não conseguiria obter utilizando o MacBook Pro 2016.

Alguns dias atrás, enquanto utilizava o Macbook Pro 2011, o HD queimou, me deixando uma oportunidade interessante de fazer umas instalação limpa do macOS do macOS High Sierra (10.13.6) driblando os problemas da GPU AMD Radeon.

Por que o High Sierra? Essa é a ultima versão suportada oficialmente pela Apple e não deve haver nenhuma dificuldade ou impecílio para instalá-lo.

Em um futuro próximo, pretendo fazer o upgrade para o macOS Mojave e manter nessa versão. Um upgrade para o macOS Catalina (o mais recente macOS no momento) não seria interessante pois:

- O suporte a aplicativos de 32-bits deixou de existir e alguns apps antigos que uso não funcionam mais.
- A iGPU Intel dificilmente será suportada no macOS Catalina pela falta de suporte a Metal.
- Sem uma eGPU a experiencia no macOS Catalina será bem ruim.

Dito isso, vamos ao processo.

### Criando o flash drive de boot

O primeiro passo é criar um flash drive para fazer o boot para o instalador.

Para tal, usei outro mac e os comandos abaixo:

1. Baixe o macOS High Sierra (10.13.6) via linha de comando.

```bash
softwareupdate --fetch-full-installer --full-installer-version 10.13.6
```

2. Com um flash drive formatado, crie o instalador bootável (ref. [How to create a bootable installer for macOS - Apple Support](https://support.apple.com/en-us/HT201372)) utilizando o comando abaixo:

```bash
sudo /Applications/Install\ macOS\ High\ Sierra.app/Contents/Resources/createinstallmedia --volume /Volumes/Nome-Do-Flash-Drive
```

_**Nota:** Caso vc não saiba o nome do flash drive, veja os volumes `ls /Volumes` e um deles será o flash drive. Muita atenção pois se utilizar o nome errado, vc corre o risco de formatar um dispositivo externo que esteja conectado, portanto, por medida de segurança, desconecte todos os dispositivos e mantenha somente o flash drive._

3. Por fim, apague o instalador do diretório `/Applications` para recuperar ~6Gb de espaço.

### Instalando macOS High Sierra

1. Ligue o mac segurando a tecla _Option_, e na tela apresentada selecione a opção _Install MacOS High Sierra_.
2. Siga o instalador normalmente até o fim.

Quando a instalação terminar, o macOS tentará fazer o boot e consequentemente abrir a tela de login. **O mac vai falhar**, pois a tela de login depende da dGPU. Nada deve acontecer, nenhuma tela, cursor ou "sinal de vida". No meu caso, a tela ficou cinza, e a maquina travou.

Neste ponto, reinicie em modo restore (_Command + R_), e siga os passos do primeiro [post](https://jefersonsantos.com/macbook-pro-2011-gpu-consertado/) para desabilitar o CSR, desabilitar a dGPU na NVRAM, e remover os kexts.

### Por fim…

O macOS High Sierra não deve receber mais atualizações frequentes, apenas patches de segurança. Tenha em mente que [provavelmente] toda vez que houver uma atualização do macOS High Sierra, será necessário desabilitar a dGPU na NVRAM e remover os kernel extensions (kexts).
