---
title: "Gerando um unique DeviceID no Windows Phone 8.1"
date: 2015-08-12T19:42:42+02:00
---

Em um projeto que trabalhei havia a necessidade de identificar os dispositivos por algum identificador único e imutável entre apps.

O WinRT prove a API de [`HardwareIdentification`](https://msdn.microsoft.com/en-us/library/windows/apps/windows.system.profile.hardwareidentification), onde é possível chamar o método [`GetPackageSpecificToken`](http://msdn.microsoft.com/en-us/library/windows/apps/windows.system.profile.hardwareidentification.getpackagespecifictoken) para obter um token único para o hardware. No entanto este token é diferente para cada aplicativo, logo, se o objetivo for verificar em vários apps (para envio para um serviço externo, por exemplo) o DeviceID então o HardwareIdentification não é uma opção.

Nas versões anteriores do Windows Phone OS era possível obter dados como IMEI, número de série, etc, já no Windows Phone 8.1 (WinRT) não é mais possível.

Uma opção que funciona muito bem é utilizar o [`NetworkAdapterId`](https://msdn.microsoft.com/en-us/library/windows/apps/windows.networking.connectivity.networkadapter.networkadapterid.aspx), que é similar ao MAC Address do dispositivo (mesmo que o WinRT não exponha nenhuma API para obter o MAC Address propriamente dito), e é único entre apps e só vai mudar caso haja alguma mudança no hardware de rede, algo muito raro de acontecer com um smartphone ou tablet.

Veja abaixo o snippet para obter o ID:

```
var networkProfiles = Windows.Networking
                    .Connectivity
                    .NetworkInformation
                    .GetConnectionProfiles();

var adapter = networkProfiles
            .First<Windows.Networking.Connectivity.ConnectionProfile>()
            .NetworkAdapter;

string networkAdapterId = adapter.NetworkAdapterId.ToString();
```

Este mesmo código também funciona no Windows 8 (desktop), no entanto tenha em mente que a chance de uma alteração de hardware de rede em um desktop é muito mais provável que em um smartphone.
