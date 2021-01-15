---
title: Netwerk doorvoer van virtuele Azure-machine | Microsoft Docs
description: Meer informatie over de netwerk doorvoer van virtuele Azure-machines, inclusief hoe band breedte aan een virtuele machine wordt toegewezen.
services: virtual-network
documentationcenter: na
author: steveesp
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/26/2019
ms.author: steveesp
ms.reviewer: kumud, mareat
ms.openlocfilehash: b11bdf9b82352c15b7f7236168494f32fe4a4f9f
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98221507"
---
# <a name="virtual-machine-network-bandwidth"></a>Bandbreedte van virtuele machines

Azure biedt verschillende VM-grootten en-typen, elk met een andere combi natie van prestatie mogelijkheden. Eén mogelijkheid is netwerk doorvoer (of band breedte), gemeten in megabits per seconde (Mbps). Omdat virtuele machines worden gehost op gedeelde hardware, moet de netwerk capaciteit redelijk worden gedeeld tussen de virtuele machines die dezelfde hardware delen. Grotere virtuele machines hebben relatief meer band breedte toegewezen dan kleinere virtuele machines.
 
De netwerk bandbreedte die aan elke virtuele machine wordt toegewezen, wordt gemeten op basis van uitgaand verkeer van de virtuele machine. Al het netwerk verkeer dat de virtuele machine verlaat, wordt meegeteld bij de toegewezen limiet, ongeacht de bestemming. Als een virtuele machine bijvoorbeeld een limiet van 1.000 Mbps heeft, is deze limiet van toepassing of het uitgaande verkeer bestemd is voor een andere virtuele machine in hetzelfde virtuele netwerk of buiten Azure.
 
Ingangs rechten worden niet rechtstreeks in een Data limiet gemeten. Er zijn echter andere factoren, zoals CPU-en opslag limieten, die van invloed kunnen zijn op de mogelijkheid van een virtuele machine om binnenkomende gegevens te verwerken.

Versneld netwerken is een functie die is ontworpen om de netwerk prestaties te verbeteren, inclusief latentie, door Voer en CPU-gebruik. Hoewel versnelde netwerken de door Voer van een virtuele machine kunnen verbeteren, kan dit alleen tot aan de toegewezen band breedte van de virtuele machine. Zie versnelde netwerken voor virtuele [Windows](create-vm-accelerated-networking-powershell.md) -of [Linux](create-vm-accelerated-networking-cli.md) -machines voor meer informatie over versneld netwerken.
 
Virtuele Azure-machines moeten één hebben, maar er kunnen verschillende, netwerk interfaces aan zijn gekoppeld. De band breedte die is toegewezen aan een virtuele machine is de som van alle uitgaand verkeer voor alle netwerk interfaces die zijn gekoppeld aan een virtuele machine. Met andere woorden, de toegewezen band breedte is per virtuele machine, ongeacht het aantal netwerk interfaces dat aan de virtuele machine is gekoppeld. Zie Azure [Windows](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -en [Linux](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -VM-grootten voor meer informatie over het aantal netwerk interfaces dat ondersteuning biedt voor verschillende Azure VM-grootten. 

## <a name="expected-network-throughput"></a>Verwachte netwerk doorvoer

De verwachte uitgaande door Voer en het aantal netwerk interfaces dat wordt ondersteund door elke VM-grootte, worden in azure [Windows](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -en [Linux](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -VM-grootten gedetailleerd beschreven. Selecteer een type, zoals algemeen doel, en selecteer vervolgens een teken reeks op de resulterende pagina, zoals de dv2-serie. Elke reeks heeft een tabel met netwerk specificaties in de laatste kolom met de titel **maximum aantal nic's/verwachte netwerk prestaties (Mbps)**. 

De doorvoer limiet is van toepassing op de virtuele machine. De door Voer wordt niet beïnvloed door de volgende factoren:
- **Aantal netwerk interfaces**: de bandbreedte limiet is cumulatief van al het uitgaande verkeer van de virtuele machine.
- **Versneld netwerken**: Hoewel de functie nuttig kan zijn bij het bereiken van de gepubliceerde limiet, wordt de limiet niet gewijzigd.
- **Verkeers bestemming**: alle doelen tellen mee voor de uitgaande limiet.
- **Protocol**: al het uitgaande verkeer via alle protocollen telt de limiet.

## <a name="network-flow-limits"></a>Limieten voor netwerk stroom

Naast band breedte kan het aantal netwerk verbindingen dat op een bepaald moment op een virtuele machine aanwezig is, invloed hebben op de netwerk prestaties. De Azure-netwerk stack houdt status in voor elke richting van een TCP/UDP-verbinding in gegevens structuren die ' stromen ' worden genoemd. Een typische TCP/UDP-verbinding heeft twee stromen die zijn gemaakt, één voor de binnenkomende en een andere voor de uitgaande richting. 

Voor gegevens overdracht tussen eind punten is het maken van verschillende stromen vereist naast die voor het uitvoeren van de gegevens overdracht. Enkele voor beelden zijn stromen die zijn gemaakt voor de DNS-omzetting en stromen die zijn gemaakt voor load balancer status controles. Houd er ook rekening mee dat virtuele netwerk apparaten (Nva's), zoals gateways, proxy's, firewalls, stromen kunnen zien die worden gemaakt voor verbindingen die op het apparaat worden beëindigd en afkomstig zijn van het apparaat. 

![Aantal flows voor TCP-conversatie via een doorstuur apparaat](media/virtual-machine-network-throughput/flow-count-through-network-virtual-appliance.png)

## <a name="flow-limits-and-recommendations"></a>Stroom limieten en aanbevelingen

Vandaag ondersteunt de Azure-netwerk stack 250.000 totale netwerk stromen met goede prestaties voor Vm's met meer dan 8 CPU-kernen en het totale aantal stromen in 100.000 met goede prestaties voor Vm's met minder dan 8 CPU-kernen. In de meeste gevallen worden de netwerk prestaties op de juiste wijze gedegradeerd voor extra stromen tot een vaste limiet van 500.000 totale stromen, 250.000 inkomend en 250.000 uitgaand, waarna de extra stromen worden verwijderd.

| Prestatieniveau | Vm's met <8 CPU-kernen | Vm's met 8 en CPU-kernen |
| ----------------- | --------------------- | --------------------- |
|<b>Goede prestaties</b>|100.000 stromen |250.000 stromen|
|<b>Verminderde prestaties</b>|Boven 100.000 stromen|Boven 250.000 stromen|
|<b>Stroom limiet</b>|500.000 stromen|500.000 stromen|

Er zijn metrische gegevens beschikbaar in [Azure monitor](../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines) om het aantal netwerk stromen en de frequentie van het maken van de stroom op uw virtuele machine-of VMSS-instanties bij te houden.

![Scherm afbeelding toont de pagina metrische gegevens van Azure Monitor met een lijn diagram en totalen voor binnenkomende en uitgaande stromen.](media/virtual-machine-network-throughput/azure-monitor-flow-metrics.png)

Het instellen en het beëindigen van de verbinding kan ook invloed hebben op de netwerk prestaties als de verbinding tot stand brengen en het beëindigen van de CPU met pakket verwerkings routines. We raden u aan werk belastingen te laten voldoen aan de verwachte verkeers patronen en de werk belasting adequaat uit te breiden zodat deze overeenkomen met uw prestatie behoeften. 

## <a name="next-steps"></a>Volgende stappen

- [Netwerkdoorvoer optimaliseren voor het besturingssysteem van een virtuele machine](virtual-network-optimize-network-bandwidth.md)
- [Test de netwerk doorvoer](virtual-network-bandwidth-testing.md) voor een virtuele machine.