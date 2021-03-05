---
title: Azure VM-netwerk doorvoer testen
titlesuffix: Azure Virtual Network
description: Gebruik NTTTCP om het netwerk te richten op het testen en Minimaliseer het gebruik van andere bronnen die van invloed kunnen zijn op de prestaties.
services: virtual-network
documentationcenter: na
author: steveesp
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/06/2020
ms.author: steveesp
ms.openlocfilehash: abcd2f6e23ea0c7c26b2ee26b2f6ca4ad502b769
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102177178"
---
# <a name="bandwidththroughput-testing-ntttcp"></a>Bandbreedte/doorvoer testen (NTTTCP)

Bij het testen van de prestaties van netwerk doorvoer in azure, kunt u het beste een hulp programma gebruiken dat gericht is op het netwerk om te testen, en minimaliseert u het gebruik van andere bronnen die van invloed kunnen zijn op de prestaties. NTTTCP wordt aanbevolen.

Kopieer het hulp programma naar twee virtuele Azure-machines met dezelfde grootte. Eén VM fungeert als afzender en de andere als ontvanger.

#### <a name="deploying-vms-for-testing"></a>Vm's implementeren voor testen
Voor de doel einden van deze test moeten de twee virtuele machines zich in dezelfde [nabijste plaatsings groep](../virtual-machines/co-location.md) bevinden of op dezelfde beschikbaarheidsset zodat we hun interne ip's kunnen gebruiken en de load balancers van de test uitsluiten. Het is mogelijk om met het VIP te testen, maar dit type tests valt buiten het bereik van dit document.

Noteer het IP-adres van de ontvanger. We bellen het IP-adres "a. b. c. r"

Noteer het aantal kern geheugens op de virtuele machine. We bellen u de \# nummer \_ kernen

Voer de NTTTCP-test gedurende 300 seconden (of 5 minuten) uit op de VM van de verzender en de ontvanger van de virtuele machine.

Tip: als u deze test voor de eerste keer instelt, kunt u een kortere test periode proberen om feedback te krijgen. Wanneer het hulp programma werkt zoals verwacht, breidt u de test periode uit tot 300 seconden voor de meest nauw keurige resultaten.

> [!NOTE]
> De verzender **en** ontvanger moeten **dezelfde** test duration-para meter (-t) opgeven.

Eén TCP-stroom gedurende 10 seconden testen:

Receiver-para meters: ntttcp-r-t 10-P 1

Sender para meters: ntttcp-s 10.27.33.7-t 10-n 1-P 1

> [!NOTE]
> Het voor gaande voor beeld mag alleen worden gebruikt om uw configuratie te bevestigen. Geldige voor beelden van testen worden verderop in dit document besproken.

## <a name="testing-vms-running-windows"></a>Vm's met WINDOWS testen:

#### <a name="get-ntttcp-onto-the-vms"></a>NTTTCP op de virtuele machines ophalen.

De nieuwste versie downloaden: https://github.com/microsoft/ntttcp/releases/download/v5.35/NTttcp.exe

Of Bekijk de GitHub-pagina op het hoogste niveau: <https://github.com/microsoft/ntttcp>\

Overweeg NTTTCP in een afzonderlijke map te plaatsen, zoals c: \\ tools

#### <a name="allow-ntttcp-through-the-windows-firewall"></a>NTTTCP via de Windows Firewall toestaan
Maak op de ontvanger een regel voor toestaan op de Windows Firewall zodat het NTTTCP-verkeer kan worden ontvangen. Het is eenvoudig om het hele NTTTCP-programma op naam toe te staan in plaats van specifieke TCP-poorten toestaan.

Ntttcp toestaan via de Windows Firewall als volgt:

Netsh advfirewall firewall add rule Program = \<PATH\> \\ntttcp.exe name = "ntttcp-protocol = any dir = in Action = Enable = Yes profiel = any

Als u bijvoorbeeld ntttcp.exe hebt gekopieerd naar de map c: \\ tools, is dit de opdracht: 

Netsh advfirewall firewall add rule Program = c: \\ tools \\ntttcp.exe name = "ntttcp-protocol = any dir = in Action = Enable = Yes profiel = any

#### <a name="running-ntttcp-tests"></a>NTTTCP-tests uitvoeren

NTTTCP starten op de ontvanger (**uitvoeren vanuit cmd**, niet vanuit Power shell):

ntttcp-r – m [2 \* \# num \_ cores], \* , a. b. c. r-t 300

Als de VM vier kernen en een IP-adres van 10.0.0.4 heeft, zou dit er als volgt uitzien:

ntttcp-r – m 8, \* , 10.0.0.4-t 300


NTTTCP starten op de afzender (**uitvoeren vanuit cmd**, niet vanuit Power shell):

ntttcp-s – m 8, \* , 10.0.0.4-t 300 

Wacht op de resultaten.


## <a name="testing-vms-running-linux"></a>Vm's met LINUX testen:

Gebruik nttcp-for-Linux. Deze is beschikbaar via <https://github.com/Microsoft/ntttcp-for-linux>

Voer op de virtuele Linux-machines (zowel de afzender als de ontvanger) deze opdrachten uit om ntttcp-for-Linux op uw virtuele machines voor te bereiden:

CentOS-gcc en git installeren:
``` bash
  yum install gcc -y  
  yum install git -y
```
Ubuntu-build-essential en git installeren:
``` bash
 apt-get -y install build-essential  
 apt-get -y install git
```
SUSE-Installeer git-core, gcc en doe het volgende:
``` bash
  zypper in -y git-core gcc make
```
Maken en installeren op beide:
``` bash
 git clone https://github.com/Microsoft/ntttcp-for-linux
 cd ntttcp-for-linux/src
 make && make install
```

Net als in het Windows-voor beeld gaan we ervan uit dat het IP-adres van de Linux-ontvanger 10.0.0.4 is

Start NTTTCP-for-Linux op de ontvanger:

``` bash
ntttcp -r -t 300
```

En voer de volgende handelingen uit op de afzender:

``` bash
ntttcp -s10.0.0.4 -t 300
```
 
De test lengte wordt standaard ingesteld op 60 seconden als er geen tijd parameter is opgegeven

## <a name="testing-between-vms-running-windows-and-linux"></a>Testen tussen virtuele machines met Windows en LINUX:

In deze scenario's moet u de modus voor niet synchroniseren inschakelen, zodat de test kan worden uitgevoerd. Dit wordt gedaan met behulp van de **vlag-N** voor Linux en **-NS** voor Windows.

#### <a name="from-linux-to-windows"></a>Van Linux naar Windows:

Ontvanger \<Windows> :

``` bash
ntttcp -r -m <2 x nr cores>,*,<Windows server IP>
```

Afzender \<Linux> :

``` bash
ntttcp -s -m <2 x nr cores>,*,<Windows server IP> -N -t 300
```

#### <a name="from-windows-to-linux"></a>Van Windows naar Linux:

Ontvanger \<Linux> :

``` bash
ntttcp -r -m <2 x nr cores>,*,<Linux server IP>
```

Afzender \<Windows> :

``` bash
ntttcp -s -m <2 x nr cores>,*,<Linux  server IP> -ns -t 300
```
## <a name="testing-cloud-service-instances"></a>Cloud service-exemplaren testen:
U moet de volgende sectie toevoegen aan uw ServiceDefinition. csdef
```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" />
</Endpoints> 
```

## <a name="next-steps"></a>Volgende stappen
* Afhankelijk van de resultaten is er mogelijk voldoende ruimte om de [netwerk doorvoer machines te optimaliseren](virtual-network-optimize-network-bandwidth.md) voor uw scenario.
* Meer informatie over hoe [band breedte wordt toegewezen aan virtuele machines](virtual-machine-network-throughput.md)
* Meer informatie met behulp van veelgestelde [vragen over Azure Virtual Network](virtual-networks-faq.md)
