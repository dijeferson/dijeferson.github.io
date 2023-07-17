---
title: "IoT no Dia-a-Dia"
date: 2018-03-07T14:52:47+02:00
---

Hoje em dia, mais do que nunca, vemos noticias e lançamentos na área de IoT (Internet of Things => Internet das Coisas). Desde lampadas controladas pelo smartphone a torneiras, irrigadores, e geladeiras. Tudo pra tornar a vida mais futurista, como nos Jetsons.

Em um primeiro momento ao ver esse tipo de dispositivo sempre pensei "ah, legal, deve ser divertido ligar a luz com o iPhone, mas nada vá mudar a vida de alguém". Isso foi até me mudar para a África do Sul.

Na Africa do Sul há uma lei que proíbe construtoras de colocar interruptores e tomadas dentro dos banheiros, exceto para o uso de eletrodomésticos como maquina de lavar (que sim, aqui elas ficam na cozinha ou no banheiro. O conceito de "lavanderia" é quase inexistente na maioria dos apartamentos e casas). Com isso os interruptores para luzes do banheiro ficam do lado de fora. Sim, eu sei, é uma questão de se acostumar... exceto quando o interruptor do banheiro fica a 3 metros da porta do banheiro.

Pesquisando soluções que não envolvessem nem quebrar paredes e nem quebrar as regras, encontrei o **Philips Hue Switch**:

![Philips Hue Switch](https://s3-eu-west-1.amazonaws.com/jefersonsantos-blog/2018/03/philips_hue_switch.jpg)

A princípio parece um interruptor normal, com botao para ligar, desligar, e modificar a intensidade da luz, que deve ser uma lâmpada Philips Hue compatível. No entanto há um _catch_: Ele é 100% wireless, usando apenas uma bateria CR com duração média de 2 anos, e pode até ser separado e usado como controle remoto por rf. Com isso é possível controlar a luz do banheiro de dentro do banheiro, sem precisar me locomover até o outro lado do quarto, e sem precisar me preocupar em quebrar paredes, passar fios, etc.

Por ser compatível com HomeKit da Apple, é possível até mesmo alterar a funcionalidade de cada botão ou criar _triggers_ encadeados do tipo "quando a luz do banheiro for apagada, acenda a luz do quarto em intensidade 20% caso horário seja entre 00:00 e 5:00".

Outra opção seria utilizar um sensor de presença Philips Hue Motion dentro do banheiro, desta forma poderia configurar o sensor para acender e apagar a luz automaticamente e ainda criar rotinas para que a luz não seja acionada automaticamente durante o dia (por exemplo) ou sejam acionadas dado a quantidade de luz do ambiente (lux).

Pode parecer banal, mas se refletirmos bem sobre os usos de IoT em um ambiente doméstico, muitas coisas podem ser otimizadas, como por exemplo:

- Luzes que podem ser acionadas por sensor de movimento para auxiliar idosos durante a noite.
- Interruptores sem fio que podem ser colocados em qualquer lugar, podendo auxiliar pessoas com mobilidade reduzida.
- Redução do consumo elétrico já que as pode-se configurar ações que apaguem as luzes automaticamente quando não houver ninguem em casa ou no ambiente.

Estes são apenas alguns casos onde o investimento inicial relativamente elevado (considerando que são "apenas" lâmpadas) é superado pelos benefícios.
