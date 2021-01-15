---
title: Netwerk latentie van Azure virtual machine testen in een virtueel Azure-netwerk | Microsoft Docs
description: Meer informatie over het testen van de netwerk latentie tussen virtuele machines van Azure in een virtueel netwerk
services: virtual-network
documentationcenter: na
author: steveesp
manager: Marina Lipshteyn
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: steveesp
ms.openlocfilehash: f8f167a7947c42ce837ec83b336ae636f593f2e4
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98219255"
---
# <a name="test-vm-network-latency"></a>Netwerklatentie van VM testen

Meet de netwerk latentie van uw virtuele Azure-machine (VM) met een hulp programma dat is ontworpen voor de taak om de meest nauw keurige resultaten te krijgen. Openbaar beschik bare hulpprogram ma's zoals SockPerf (voor Linux) en latte.exe (voor Windows) kunnen netwerk latentie isoleren en meten tijdens het uitsluiten van andere typen latentie, zoals latentie van toepassingen. Deze hulpprogram ma's zijn gericht op het soort netwerk verkeer dat van invloed is op de prestaties van toepassingen (met name Transmission Control Protocol [TCP] en het UDP-verkeer (User Data gram Protocol). 

Andere veelgebruikte connectiviteits hulpprogramma's, zoals ping, kunnen latentie meten, maar de resultaten vertegenwoordigen mogelijk niet het netwerk verkeer dat wordt gebruikt in echte werk belastingen. Dit komt doordat de meeste van deze hulpprogram ma's gebruikmaken van de Internet Control Message Protocol (ICMP), die anders kan worden behandeld vanuit toepassings verkeer en waarvan de resultaten mogelijk niet van toepassing zijn op werk belastingen die gebruikmaken van TCP en UDP. 

Voor een nauw keurige test van de netwerk latentie van de protocollen die worden gebruikt door de meeste toepassingen, SockPerf (voor Linux) en latte.exe (voor Windows) de meest relevante resultaten opleveren. Dit artikel heeft betrekking op beide hulpprogram ma's.

## <a name="overview"></a>Overzicht

Met twee Vm's, een als afzender en een als ontvanger, maakt u een communicatie kanaal in twee richtingen. Met deze methode kunt u pakketten in beide richtingen verzenden en ontvangen en de RTT (round trip time) meten.

U kunt deze methode gebruiken om de netwerk latentie tussen twee Vm's te meten of zelfs tussen twee fysieke computers. Latentie metingen kunnen nuttig zijn voor de volgende scenario's:

- Stel een bench Mark in voor de netwerk latentie tussen de geïmplementeerde Vm's.
- Vergelijk de effecten van wijzigingen in de netwerk latentie nadat de gerelateerde wijzigingen zijn aangebracht in:
  - Besturings systeem (OS) of netwerk stack software, inclusief configuratie wijzigingen.
  - Een VM-implementatie methode, zoals implementeren naar een beschikbaarheids zone of proximity placement Group (PPG).
  - VM-eigenschappen, zoals versneld netwerken of grootte wijzigingen.
  - Een virtueel netwerk, zoals wijzigingen in de route ring of het filteren.

### <a name="tools-for-testing"></a>Hulpprogram ma's voor testen
Om latentie te meten, hebt u twee verschillende hulp middelen:

* Voor Windows-systemen: [latte.exe (Windows)](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)
* Voor Linux-systemen: [SockPerf (Linux)](https://github.com/mellanox/sockperf)

Met deze hulpprogram ma's kunt u ervoor zorgen dat alleen TCP-of UDP-Payload-leverings tijden worden gemeten en niet ICMP (ping) of andere pakket typen die niet worden gebruikt door toepassingen en niet van invloed zijn op de prestaties.

### <a name="tips-for-creating-an-optimal-vm-configuration"></a>Tips voor het maken van een optimale VM-configuratie

Houd bij het maken van de VM-configuratie de volgende aanbevelingen in acht:
- Gebruik de nieuwste versie van Windows of Linux.
- Versneld netwerken inschakelen voor de beste resultaten.
- Implementeer Vm's met een [Azure proximity-plaatsings groep](../virtual-machines/co-location.md).
- Grotere Vm's doen over het algemeen beter dan kleinere Vm's.

### <a name="tips-for-analysis"></a>Tips voor analyse

Houd bij het analyseren van test resultaten rekening met de volgende aanbevelingen:

- Maak een vroege basis lijn, zodra de implementatie, configuratie en optimalisaties zijn voltooid.
- Vergelijk nieuwe resultaten altijd met een basis lijn of, in andere gevallen, van de ene test naar de andere met beheerde wijzigingen.
- Herhaal tests wanneer wijzigingen worden waargenomen of gepland.


## <a name="test-vms-that-are-running-windows"></a>Vm's testen waarop Windows wordt uitgevoerd

### <a name="get-latteexe-onto-the-vms"></a>latte.exe op de Vm's ontvangen

Down load de [nieuwste versie van latte.exe](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b).

Overweeg latte.exe in een afzonderlijke map te plaatsen, zoals *c:\Tools*.

### <a name="allow-latteexe-through-windows-defender-firewall"></a>latte.exe via Windows Defender Firewall toestaan

Maak op de *ontvanger* een regel voor toestaan op Windows Defender firewall om toe te staan dat het latte.exe verkeer binnenkomt. Het is eenvoudig om het hele latte.exe-programma toe te staan op naam in plaats van specifieke TCP-poorten toestaan.

latte.exe via Windows Defender Firewall toestaan door de volgende opdracht uit te voeren:

```cmd
netsh advfirewall firewall add rule program=<path>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY
```

Als u bijvoorbeeld latte.exe hebt gekopieerd naar de map *c:\Tools* , is dit de opdracht:

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>Latentie tests uitvoeren

* Start latte.exe op de *ontvanger*(Voer de opdracht uit vanuit het CMD-venster, niet vanuit Power shell):

    ```cmd
    latte -a <Receiver IP address>:<port> -i <iterations>
    ```

    Rond 65.000 iteraties is lang genoeg om representatieve resultaten te retour neren.

    Elk beschikbaar poort nummer is prima.

    Als de virtuele machine een IP-adres heeft van 10.0.0.4, ziet de opdracht er als volgt uit:

    `latte -a 10.0.0.4:5005 -i 65100`

* Start latte.exe op de *afzender*(Voer de opdracht uit vanuit het CMD-venster, niet vanuit Power shell):

    ```cmd
    latte -c -a <Receiver IP address>:<port> -i <iterations>
    ```

    De resulterende opdracht is hetzelfde als op de ontvanger, behalve met de toevoeging van &nbsp; *-c* om aan te geven dat dit de *client* of *afzender* is:

    `latte -c -a 10.0.0.4:5005 -i 65100`

Wacht op de resultaten. Afhankelijk van hoe ver de Vm's bestaan, kan het enkele minuten duren voordat de test is voltooid. Overweeg te beginnen met minder herhalingen om te testen op geslaagde pogingen voordat u langere tests uitvoert.

## <a name="test-vms-that-are-running-linux"></a>Vm's testen waarop Linux wordt uitgevoerd

Gebruik [SockPerf](https://github.com/mellanox/sockperf)om virtuele machines met Linux te testen.

### <a name="install-sockperf-on-the-vms"></a>SockPerf installeren op de virtuele machines

Voer op de virtuele Linux-machines, zowel de *afzender* als de *ontvanger*, de volgende opdrachten uit om SockPerf op de virtuele machines voor te bereiden. Er zijn opdrachten voor de primaire distributies.

#### <a name="for-red-hat-enterprise-linux-rhelcentos"></a>Voor Red Hat Enterprise Linux (RHEL)/CentOS

Voer de volgende opdrachten uit:

```bash
#RHEL/CentOS - Install Git and other helpful tools
    sudo yum install gcc -y -q
    sudo yum install git -y -q
    sudo yum install gcc-c++ -y
    sudo yum install ncurses-devel -y
    sudo yum install -y automake
    sudo yum install -y autoconf
```

#### <a name="for-ubuntu"></a>Voor Ubuntu

Voer de volgende opdrachten uit:

```bash
#Ubuntu - Install Git and other helpful tools
    sudo apt-get install build-essential -y
    sudo apt-get install git -y -q
    sudo apt-get install -y autotools-dev
    sudo apt-get install -y automake
    sudo apt-get install -y autoconf
```

#### <a name="for-all-distros"></a>Voor alle distributies

Kopieer, compileer en installeer SockPerf aan de hand van de volgende stappen:

```bash
#Bash - all distros

#From bash command line (assumes Git is installed)
git clone https://github.com/mellanox/sockperf
cd sockperf/
./autogen.sh
./configure --prefix=

#make is slower, may take several minutes
make

#make install is fast
sudo make install
```

### <a name="run-sockperf-on-the-vms"></a>SockPerf uitvoeren op de Vm's

Nadat de SockPerf-installatie is voltooid, zijn de Vm's klaar om de latentie tests uit te voeren. 

Start eerst SockPerf op de *ontvanger*.

Elk beschikbaar poort nummer is prima. In dit voor beeld gebruiken we poort 12345:

```bash
#Server/Receiver - assumes server's IP is 10.0.0.4:
sudo sockperf sr --tcp -i 10.0.0.4 -p 12345
```

Nu de server luistert, kan de client beginnen met het verzenden van pakketten naar de server op de poort waarop deze luistert (in dit geval 12345).

Ongeveer 100 seconden is lang genoeg om representatieve resultaten te retour neren, zoals wordt weer gegeven in het volgende voor beeld:

```bash
#Client/Sender - assumes server's IP is 10.0.0.4:
sockperf ping-pong -i 10.0.0.4 --tcp -m 350 -t 101 -p 12345  --full-rtt
```

Wacht op de resultaten. Afhankelijk van hoe ver de Vm's bestaan, is het aantal iteraties afhankelijk. Als u wilt testen op geslaagd voordat u langere tests uitvoert, kunt u overwegen om te beginnen met korte tests van ongeveer 5 seconden.

In dit SockPerf-voor beeld wordt een 350-byte bericht grootte gebruikt. Dit is normaal voor een gemiddeld pakket. U kunt de grootte verg Roten of verkleinen om de resultaten nauw keuriger weer te geven die op uw Vm's worden uitgevoerd.


## <a name="next-steps"></a>Volgende stappen
* Verbeter de latentie met een [plaatsings groep voor Azure nabijheid](../virtual-machines/co-location.md).
* Meer informatie over het [optimaliseren van netwerken voor vm's](../virtual-network/virtual-network-optimize-network-bandwidth.md) voor uw scenario.
* Meer informatie over [hoe band breedte wordt toegewezen aan virtuele machines](../virtual-network/virtual-machine-network-throughput.md).
* Zie [Veelgestelde vragen over Azure Virtual Network](../virtual-network/virtual-networks-faq.md)voor meer informatie.