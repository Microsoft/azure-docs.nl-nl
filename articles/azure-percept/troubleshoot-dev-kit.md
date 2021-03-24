---
title: Algemene problemen met Azure percept DK en IoT Edge oplossen
description: Tips voor het oplossen van problemen met een aantal van de meest voorkomende problemen met Azure percept DK
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 313ea98da0426af945dfdea00d33440ab2955cc7
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105023075"
---
# <a name="azure-percept-dk-dev-kit-troubleshooting"></a>Problemen oplossen met Azure percept DK (dev kit)

Zie de onderstaande richt lijnen voor algemene tips voor probleem oplossing voor Azure percept DK.

## <a name="general-troubleshooting-commands"></a>Algemene opdrachten voor probleem oplossing

Als u deze opdrachten wilt uitvoeren, 
1. Verbinding maken met de [Wi-Fi-AP van de dev kit](./quickstart-percept-dk-set-up.md)
1. [SSH in de dev kit](./how-to-ssh-into-percept-dk.md)
1. Voer de opdrachten in de SSH-terminal in

Als u de uitvoer naar een txt-bestand wilt omleiden voor verdere analyse, gebruikt u de volgende syntaxis:

```console
sudo [command] > [file name].txt
```

Wijzig de machtigingen van het txt-bestand zodat het kan worden gekopieerd:

```console
sudo chmod 666 [file name].txt
```

Nadat de uitvoer naar een txt-bestand is omgeleid, kopieert u het bestand naar uw host-PC via SCP:

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

```[local host file path]``` verwijst naar de locatie op de host-PC waarnaar u het txt-bestand wilt kopiëren. ```[remote username]``` is de SSH-gebruikers naam die u hebt gekozen tijdens de [installatie-ervaring](./quickstart-percept-dk-set-up.md). Als u geen SSH-aanmelding tijdens het OOBE hebt ingesteld, is uw externe gebruikers naam ```root``` .

Raadpleeg de documentatie voor het [oplossen van problemen met het Azure IOT edge-apparaat](../iot-edge/troubleshoot.md)voor meer informatie over de Azure IOT Edge-opdrachten.

|Categorie:         |Cmd                    |Functieassembly                  |
|------------------|----------------------------|---------------------------|
|Besturingssysteem                |```cat /etc/os-release```         |Controleer de versie van de Mariner-installatie kopie |
|Besturingssysteem                |```cat /etc/os-subrelease```      |versie van afgeleide installatie kopie controleren |
|Besturingssysteem                |```cat /etc/adu-version```        |Controleer de ADU-versie |
|Temperatuur       |```cat /sys/class/thermal/thermal_zone0/temp``` |de Tempe ratuur van Devkit controleren |
|Wi-Fi             |```sudo journalctl -u hostapd.service``` |SoftAP-logboeken controleren|
|Wi-Fi             |```sudo journalctl -u wpa_supplicant.service``` |Wi-Fi Services-logboeken controleren |
|Wi-Fi             |```sudo journalctl -u ztpd.service```  |controleren Wi-Fi nul aanraak service logboeken |
|Wi-Fi             |```sudo journalctl -u systemd-networkd``` |Controleer de logboeken van Mariner-netwerk stacks |
|Wi-Fi             |```sudo cat /etc/hostapd/hostapd-wlan1.conf``` |Details van de configuratie van het Wi-Fi-toegangs punt controleren |
|OOBE              |```sudo journalctl -u oobe -b```       |OOBE-logboeken controleren |
|Telemetrie         |```sudo azure-device-health-id```      |unieke telemetrie-HW_ID zoeken |
|Azure IoT Edge          |```sudo iotedge check```          |configuratie-en connectiviteits controles voor veelvoorkomende problemen uitvoeren |
|Azure IoT Edge          |```sudo iotedge logs [container name]``` |Raadpleeg container logboeken, zoals spraak-en Vision-modules |
|Azure IoT Edge          |```sudo iotedge support-bundle --since 1h``` |module Logboeken, Azure IoT Edge Security Manager-logboeken, logboeken van de container-engine, ```iotedge check``` JSON-uitvoer en andere nuttige informatie over fout opsporing van het afgelopen uur verzamelen |
|Azure IoT Edge          |```sudo journalctl -u iotedge -f``` |de logboeken van de Azure IoT Edge Security Manager weer geven |
|Azure IoT Edge          |```sudo systemctl restart iotedge``` |de Azure IoT Edge-beveiligings-daemon opnieuw opstarten |
|Azure IoT Edge          |```sudo iotedge list```           |de geïmplementeerde Azure IoT Edge modules weer geven |
|Overige             |```df [option] [file]```          |informatie over beschik bare/totale ruimte in opgegeven bestands systeem (en) weer geven |
|Overige             |`ip route get 1.1.1.1`        |apparaat-IP en interface-informatie weer geven |
|Overige             |<code>ip route get 1.1.1.1 &#124; awk '{print $7}'</code> <br> `ifconfig [interface]` |alleen IP-adres van apparaat weer geven |


De ```journalctl``` Wi-Fi-opdrachten kunnen worden gecombineerd met de volgende opdracht:

```console
sudo journalctl -u hostapd.service -u wpa_supplicant.service -u ztpd.service -u systemd-networkd -b
```

## <a name="docker-troubleshooting-commands"></a>Opdrachten voor het oplossen van problemen met docker

|Cmd                        |Functieassembly                  |
|--------------------------------|---------------------------|
|```sudo docker ps``` |[Hiermee wordt weer gegeven welke containers worden uitgevoerd](https://docs.docker.com/engine/reference/commandline/ps/) |
|```sudo docker images``` |[Hiermee wordt aangegeven welke installatie kopieën op het apparaat staan](https://docs.docker.com/engine/reference/commandline/images/)|
|```sudo docker rmi [image id] -f``` |[Hiermee wordt een installatie kopie van het apparaat verwijderd](https://docs.docker.com/engine/reference/commandline/rmi/) |
|```sudo docker logs -f edgeAgent``` <br> ```sudo docker logs -f [module_name]``` |[neemt container logboeken van de opgegeven module op](https://docs.docker.com/engine/reference/commandline/logs/) |
|```sudo docker image prune``` |[Hiermee verwijdert u alle Dangling-installatie kopieën](https://docs.docker.com/engine/reference/commandline/image_prune/) |
|```sudo watch docker ps``` <br> ```watch ifconfig [interface]``` |Download status van docker-container controleren |

## <a name="usb-updating"></a>USB-update

|Fout:                                    |Oplossing:                                               |
|------------------------------------------|--------------------------------------------------------|
|LIBUSB_ERROR_XXX tijdens USB-Flash via UUU |Deze fout wordt veroorzaakt door een USB-verbindings fout tijdens het bijwerken van UUU. Als de USB-kabel niet goed is aangesloten op de USB-poorten op de PC of de PE-10X, treedt er een fout op in dit formulier. Probeer beide uiteinden van de USB-kabel los te koppelen en opnieuw te koppelen en jiggling de kabel om een beveiligde verbinding te garanderen. Hiermee wordt het probleem bijna altijd opgelost. |

## <a name="azure-percept-dk-carrier-board-led-states"></a>LED-statussen voor Azure percept DK-Carrier Board

Er zijn drie kleine Led's boven op de draag huis kaart. Er wordt een Cloud pictogram afgedrukt naast LED 1, er wordt een Wi-Fi pictogram afgedrukt naast LED 2, en er wordt een uitroep teken pictogram afgedrukt naast LED 3. Zie de onderstaande tabel voor informatie over de status van elke LED.

|GELEID             |Staat      |Beschrijving                      |
|----------------|-----------|---------------------------------|
|LED 1 (IoT Hub) |Aan (vast) |Het apparaat is verbonden met een IoT Hub. |
|LED 2 (Wi-Fi)   |Langzaam knip peren |Het apparaat is klaar om te worden geconfigureerd door Wi-Fi Easy Connect en de aanwezigheid ervan wordt aangekondigd aan een Configurator. |
|LED 2 (Wi-Fi)   |Snel blinken |De verificatie is voltooid, de apparaats koppeling wordt uitgevoerd. |
|LED 2 (Wi-Fi)   |Aan (vast) |De verificatie en koppeling zijn geslaagd; het apparaat is verbonden met een Wi-Fi netwerk. |
|LED 3           |NA         |LED wordt niet gebruikt. |