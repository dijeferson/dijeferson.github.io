---
title: "Macbook Pro 2011 (Consertado!)"
date: 2018-08-30T14:50:32+02:00
---

O **MacBook Pro 2011 de 15"** foi minha introdução ao mundo Apple, e sempre considerei uma máquina sensacional que me prestou bons 6 anos de trabalho intenso.

![macbook_pro_2011](https://s3-eu-west-1.amazonaws.com/jefersonsantos-blog/2018/08/macbook_pro_2011.jpg)

Mas.. como muitos sabem essa versão (2011) do MacBook Pro tem uma falha na GPU AMD Radeon X3000 (_discrete gpu_ ou dGPU) que com o tempo e uso intenso o chipset apresenta problemas.

Os sintomas mais comuns são:

- Travamento com tela cinza ou preta durante o processo boot
- Imagens distorcidas

Alguns dizem que o problema é o tipo de solda e que basta o _reballing_ e melhorar a refrigeração pra resolver o problema, outros dizem que o problema é além da refrigeração, uma série de chips AMD Radeon problemáticos e de baixa qualidade.

Independente da causa, o problema da dGPU sempre me chateou pois a única forma de resolvê-lo de forma “definitiva” seria com a troca da placa mãe que custa cerca R$ 2.500 (em 2017), o que compraria um notebook (PC) novo relativamente decente. O grande problema é que como é uma questão de design térmico e chips AMD falhos, o problema pode (e vai) acontecer novamente mesmo trocando a placa mãe.

Algumas lojas de assistência técnica não autorizada fazem o processo de _reballing_, que consistem basicamente remover o chip e refazer a solda. Tentei o processo, mas (como esperado) 4 meses depois o problema reapareceu.

Em 2015 a Apple foi forçada, por uma decisão judicial, a consertar as máquinas com este problema. Fiz a troca da placa mãe pelo programa de garantia estendida... o que deu uma sobrevida de 1 ano e meio, até que o problema reapareceu, e desisti e comprei a versão mais recente do MacBook Pro 15” com TouchBar (2017).

Mesmo já tendo uma máquina nova, sempre me incomodou o fato de ter também uma máquina que não funcionava e que não seria possível nem mesmo vender ou fazer algo útil com ela.

... Até agora.

Em um vídeo no YouTube vi um rapaz criticando à Apple sobre a qualidade do hardware e software, até que ele diz _"se vc vai comprar um Mac, pelo menos instale um sistema operacional decente como Linux"_. Fiquei me questionando que teoricamente conseguiria configurar uma distro pra usar somente a iGPU Intel (integrated gpu ou iGPU) e ignorar a dGPU AMD Radeon. Será que funcionaria?

Ao pesquisar mais descobri que assim como nos PCs, há como modificar a configuração na _BIOS_ do MacBook de forma a desativar a dGPU e utilizar somente a iGPU, como acontece nos modelos 2011 de 13".

Utilizando as instruções à seguir foi possível voltar a usar o MacBook com performance relativamente boa.

## Antes de começar:

1. O que vc vai encontrar abaixo é a lista de procedimentos que executei na minha máquina pessoal e obtive sucesso, ou seja, funcionou pra mim mas pode não funcionar pra vc.
2. Tudo funciona até vc precisar fazer um PRAM reset, pois o  PRAM ou SMC reset vão apagar as configurações "extras" que vc fizer.
3. Upgrades do macOS também podem voltar a PRAM/SMC para o estado original.
4. Teoricamente nada extremo deve acontecer, mas tenha em mente que vc está a seu próprio risco.

## Procedimentos
1. Faça SMC e PRAM reset:
	1. SMC: https://support.apple.com/en-us/HT201295
	2. PRAM: https://support.apple.com/en-us/HT204063
2. Faça o boot em modo Restore os (CMD + R)
3. Abra o _Utilities > Terminal_ e desabilite a proteção do sistema:
	1. `csrutil disable`
4. Reinicie para aplicar as modificações.
5. Inicie em modo Single User (CMD + S)
6. Crie um novo diretório para armazenar o kext (kernel extension) da dGPU que será desabilitado, e mova os arquivos:
	1. `sudo mkdir -p /System/Library/Extensions-off/`
 2. `sudo mv /System/Library/Extensions/AMDRadeonX3000.kext /System/Library/Extensions-off/`
7. Execute o comando `sudo touch /System/Library/Extensions/` para indicar para o sistema que houveram mudanças no diretório.
	1. NOTA: O comando _touch_ muda a ultima data de modificação do arquivo/diretório informado. Para mais informações veja o comando `man touch`
8. Por precaução, apague o diretório de caches dos kexts e recrie:
	1. `sudo rm -rf /System/Library/Caches/com.apple.kext.caches/ `
	2. `sudo mkdir /System/Library/Caches/com.apple.kext.caches/`
	3. NOTA: Teoricamente não é necessário recriar o diretório, mas preferi recriar caso o macOS não soubesse fazer isso.

Neste momento, se vc reiniciar o mac, ele deve iniciar até o desktop, mas com uma performance muito ruim, pois o Mac OS vai tentar utilizar o dGPU com kexts genéricos, já que o kext “correto” não foi encontrado. Para resolver isso, vamos desabilitar/desligar a dGPU e manter somente o iGPU.

Reinicie em modo Single User novamente e execute os procedimentos abaixo:

1. Configure a dGPU para estar sempre desligada:
	1. `nvram fa4ce28d-b62f-4c99-9cc3-6815686e30f9:gpu-power-prefs=%01%00%00%00`
2. Opcional: Force o boot a usar o modo verbose, ou seja, ao invés da imagem da maça com barra de progresso, será exibido o console e tudo que o macOS está fazendo. Isso pode ajudar bastante no diagnostico:
	1. `nvram boot-args=“-v”`
3. Reinicie.

Neste momento, ao iniciar você estará utilizando 100% iGPU e a dGPU estará desabilitada. A performance gráfica é bem parecida com os MacBook Pros 2011 de 13", que possuem somente iGPU.

Em alguns casos o macOS pode não saber lidar com a falta de dGPU e super aquecer e/ou manter os fans em 100%.  Para sanar isso podemos criar um _LoginHook_ para carregar o kext da dGPU depois do boot para que o macOS veja que a dGPU está desligada.

1. No terminal digite `sudo mkdir -p /Library/LoginHook` para criar um diretório para manter o script de _LoginHook_
2. Digite `sudo vim /Library/LoginHook/LoadX3000.sh`  e insira o seguinte conteúdo:

```rb
#!/bin/bash
kextload /System/Library/Extensions-off/AMDRadeonX3000.kext
pmset -a force gpuswitch 0
exit 0
```

3. Execute o seguinte para permitir execução do script:  `sudo chmod a+x /Library/LoginHook/LoadX3000.sh`
4. Execute o seguinte para indicar o script como _LoginHook_:  `sudo defaults write com.apple.loginwindow LoginHook /Library/LoginHook/LoadX3000.sh`
5. Reinicie.
6. Agora todas as vezes que o processo de logon iniciar, o script será executado e o kext será carregado e o auto-switch de GPU será desativado. Desta forma o macOS consegue "ver" que existe uma dGPU mas está desabilitada.

No meu caso como a máquina ficou parada por muito tempo (+/- 1 ano e meio) a bateria "morreu" e isso fez com que o gerenciamento de energia "ACPI" se perdesse e o processo *kernel_task* estava usando 1500% a 2000% (!!!!) de processamento.

Usei os seguintes comandos para normalizar:

```
$ cd /System/Library/Extensions/IOPlatformPluginFamily.kext/Contents/PlugIns/ACPI_SMC_PlatformPlugin.kext/Contents/Resources

$ sudo mv MacBookPro8_2.plist MacBookPro8_2.bak
```


Aparentemente tudo está funcionando normalmente. Fiz o upgrade para o High Sierra (10.13.6 [17G65]) e está em pleno funcionamento.


## Referências

- [unix - Can I use Linux in a 2011 Macbook Pro with a defective discrete GPU? - Ask Different](https://apple.stackexchange.com/questions/168167/can-i-use-linux-in-a-2011-macbook-pro-with-a-defective-discrete-gpu)
- [Resolved - Force 2011 MacBook Pro 8,2 with failed AMD GPU to ALWAYS use Intel integrated GPU (EFI variable fix) | MacRumors Forums](https://forums.macrumors.com/threads/force-2011-macbook-pro-8-2-with-failed-amd-gpu-to-always-use-intel-integrated-gpu-efi-variable-fix.2037591/)
- [gpu-switch/gpu-switch at master · 0xbb/gpu-switch · GitHub](https://github.com/0xbb/gpu-switch/blob/master/gpu-switch)
- [Resolved - Force 2011 MacBook Pro 8,2 with failed AMD GPU to ALWAYS use Intel integrated GPU (EFI variable fix) | Page 35 | MacRumors Forums](https://forums.macrumors.com/threads/force-2011-macbook-pro-8-2-with-failed-amd-gpu-to-always-use-intel-integrated-gpu-efi-variable-fix.2037591/page-35#post-24956091)
- [Resolved - Force 2011 MacBook Pro 8,2 with failed AMD GPU to ALWAYS use Intel integrated GPU (EFI variable fix) | Page 74 | MacRumors Forums](https://forums.macrumors.com/threads/force-2011-macbook-pro-8-2-with-failed-amd-gpu-to-always-use-intel-integrated-gpu-efi-variable-fix.2037591/page-74)
- [Resolved - Force 2011 MacBook Pro 8,2 with failed AMD GPU to ALWAYS use Intel integrated GPU (EFI variable fix) | Page 57 | MacRumors Forums](https://forums.macrumors.com/threads/force-2011-macbook-pro-8-2-with-failed-amd-gpu-to-always-use-intel-integrated-gpu-efi-variable-fix.2037591/page-57#post-25751208)
- [Resolved - Force 2011 MacBook Pro 8,2 with failed AMD GPU to ALWAYS use Intel integrated GPU (EFI variable fix) | Page 43 | MacRumors Forums](https://forums.macrumors.com/threads/force-2011-macbook-pro-8-2-with-failed-amd-gpu-to-always-use-intel-integrated-gpu-efi-variable-fix.2037591/page-43#post-25307678)
- [Macbook Pro 2011 - Disable AMD GPU · GitHub](https://gist.github.com/blackgate/17ac402e35d2f7e0f1c9708db3dc7a44)
- [How to solve kernel_task high CPU usage? | Page 7 | MacRumors Forums](https://forums.macrumors.com/threads/how-to-solve-kernel_task-high-cpu-usage.1706948/page-7)
- [How to fix kernel_task CPU usage on Yosemite | Viktor’s ramblings](https://blog.viktorpetersson.com/2014/10/16/how-to-fix-kerneltask-cpu-usage-on-yosemite.html)
- [How to fix kernel_task CPU usage on El Capitan | Viktor’s ramblings](https://blog.viktorpetersson.com/2016/01/03/how-to-fix-kerneltask-cpu-usage-on-el-capitan.html)
- [macos - Is there a list of available boot-args for darwin / OS X - Super User](https://superuser.com/questions/255176/is-there-a-list-of-available-boot-args-for-darwin-os-x)