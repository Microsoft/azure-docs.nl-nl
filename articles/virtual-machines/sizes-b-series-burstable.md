---
title: B-serie-Burstable-Azure Virtual Machines
description: Beschrijft de B-serie van de afbreekbaar Azure VM-grootten.
services: virtual-machines
ms.subservice: vm-sizes-general
author: styli365
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: sttsinar
ms.openlocfilehash: c19c47ac64f1e41fa998a29d3ea0480bf0d92a63
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102554459"
---
# <a name="b-series-burstable-virtual-machine-sizes"></a>Grootte van de B-serie bebreekbaar virtuele machines

De virtuele machines uit de B-serie zijn ideaal voor werk belastingen die de volledige prestaties van de CPU niet continu nodig hebben, zoals webservers, het testen van concepten, kleine data bases en ontwikkel bouw omgevingen. Deze werk belastingen hebben doorgaans een burstieve prestatie vereisten. De B-serie biedt u de mogelijkheid om een VM-grootte aan te schaffen met basislijn prestaties, waarmee tegoeden kunnen worden gemaakt wanneer deze minder dan de basis lijn gebruiken. Wanneer de virtuele machine tegoed heeft gecrediteerd, kan de virtuele machine met Maxi maal 100% van de vCPU worden gebonden aan de basis lijn wanneer uw toepassing hogere CPU-prestaties vereist.

De B-serie is beschikbaar in de volgende VM-grootten:

[Azure Compute Unit (ACU)](./acu.md): varieert *<br>
[Premium Storage](premium-storage-performance.md): ondersteund<br>
[Premium Storage caching](premium-storage-performance.md): niet ondersteund<br>
[Livemigratie](maintenance-and-updates.md): ondersteund<br>
[Updates voor geheugen behoud](maintenance-and-updates.md): ondersteund<br>
[Ondersteuning](generation-2.md)voor het genereren van vm's: generatie 1 en 2<br>
[Versneld netwerken](../virtual-network/create-vm-accelerated-networking-cli.md): ondersteund * *<br>
[Tijdelijke besturingssysteem schijven](ephemeral-os-disks.md): ondersteund <br>

* Virtuele machines uit de B-serie zijn bursteel en daarom kunnen ACU nummers variëren, afhankelijk van de werk belasting en het kern gebruik.<br>
* * Versneld netwerken worden alleen ondersteund voor *Standard_B12ms*, *Standard_B16ms* en *Standard_B20ms*.
<br>
<br>

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Basis CPU-prestaties van VM | Maximale CPU-prestaties van VM | Eerste tegoeden | Tegoeden per uur | Maximum aantal gestorte tegoeden | Max. aantal gegevensschijven | Max. door Voer in cache en tijdelijke opslag: IOPS/MBps | Maxi maal aantal niet-opgeslagen schijf doorvoer: IOPS/MBps | Max. aantal NIC's |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| Standard_B1ls<sup>1</sup> | 1  | 0,5 | 4   | 5%   | 100%  | 30  | 3   | 72   | 2  | 200/10    | 160/10    | 2  |
| Standard_B1s              | 1  | 1   | 4   | 10%  | 100%  | 30  | 6   | 144  | 2  | 400/10    | 320/10    | 2  |
| Standard_B1ms             | 1  | 2   | 4   | 20%  | 100%  | 30  | 12  | 288  | 2  | 800/10    | 640/10    | 2  |
| Standard_B2s              | 2  | 4   | 8   | 40%  | 200%  | 60  | 24  | 576  | 4  | 1600/15   | 1280/15   | 3  |
| Standard_B2ms             | 2  | 8   | 16  | 60%  | 200%  | 60  | 36  | 864  | 4  | 2400/22.5 | 1.920-22.5 | 3  |
| Standard_B4ms             | 4  | 16  | 32  | 90%  | 400%  | 120 | 54  | 1296 | 8  | 3600/35   | 2880/35   | 4  |
| Standard_B8ms             | 8  | 32  | 64  | 135% | 800%  | 240 | 81  | 1944 | 16 | 4320/50   | 4320/50   | 4  |
| Standard_B12ms            | 12 | 48  | 96  | 202% | 1200% | 360 | 121 | 2909 | 16 | 6480/75   | 4320/50   | 6  |
| Standard_B16ms            | 16 | 64  | 128 | 270% | 1600% | 480 | 162 | 3888 | 32 | 8640/100  | 4320/50   | 8  |
| Standard_B20ms            | 20 | 80  | 160 | 337% | 2000% | 600 | 203 | 4860 | 32 | 10800/125 | 4320/50   | 8  |

<sup>1</sup> B1ls wordt alleen ondersteund op Linux

## <a name="workload-example"></a>Voor beeld workload

Overweeg een Office-toepassing voor in-en uitchecken. De toepassing heeft CPU-bursts nodig tijdens kantoor uren, maar er is geen veel reken kracht tijdens de periode. In dit voor beeld vereist de werk belasting een 16vCPU virtuele machine met 64GiB RAM-geheugen om efficiënt te werken.

De tabel toont de verkeers gegevens per uur en de grafiek is een visuele representatie van dat verkeer.

B16 kenmerken:

Maximale CPU-prestaties: 16vCPU * 100% = 1600%

Basis lijn: 270%

![Grafiek van verkeers gegevens per uur](./media/b-series-burstable/office-workload.png)

| Scenario | Tijd | CPU-gebruik (%) | Geaccumuleerde tegoeden<sup>1</sup> | Tegoed beschikbaar |
| --- | --- | --- | --- | --- |
| B16ms-implementatie | Implementatie | Implementatie  | 480 (eerste tegoed) | 480 |
| Geen verkeer | 0:00 | 0 | 162 | 642 |
| Geen verkeer | 1:00 | 0 | 162 | 804 |
| Geen verkeer | 2:00 | 0 | 162 | 966 |
| Geen verkeer | 3:00 | 0 | 162 | 1128 |
| Geen verkeer | 4:00 | 0 | 162 | 1290 |
| Geen verkeer | 5:00 | 0 | 162 | 1452 |
| Laag verkeer | 6:00 | 270 | 0 | 1452 |
| Werk nemers komen naar Office (app heeft 80% vCPU) | 7:00 | 1280 | -606 | 846 |
| Werk nemers blijven Office bezoeken (app heeft 80% vCPU) | 8:00 | 1280 | -606 | 240 |
| Laag verkeer | 9:00 | 270 | 0 | 240 |
| Laag verkeer | 10:00 | 100 | 102 | 342 |
| Laag verkeer | 11:00 | 50 | 132 | 474 |
| Laag verkeer | 12:00 | 100 | 102 | 576 |
| Laag verkeer | 13:00 | 100 | 102 | 678 |
| Laag verkeer | 14:00 | 50 | 132 | 810 |
| Laag verkeer | 15:00 | 100 | 102 | 912 |
| Laag verkeer | 16:00 | 100 | 102 | 1014 |
| Uitchecken van werk nemers (app heeft 100% vCPU) | 17:00 | 1600 | -798 | 216 |
| Laag verkeer | 18:00 | 270 | 0 | 216 |
| Laag verkeer | 19:00 | 270 | 0 | 216 |
| Laag verkeer | 20:00 | 50 | 132 | 348 |
| Laag verkeer | 21:00 | 50 | 132 | 480 |
| Geen verkeer | 22:00 | 0 | 162 | 642 |
| Geen verkeer | 23:00 | 0 | 162 | 804 |

<sup>1</sup> crediteringen die in een uur worden gebruikt, zijn gelijk aan: `((Base CPU perf of VM - CPU Usage) / 100) * 60 minutes` .  

Voor een D16s_v3 met 16 Vcpu's en 64 GiB geheugen is het uurtarief $0,936 per uur (maandelijks $673,92) en voor B16ms met 16 Vcpu's en 64 GiB geheugen de rente per uur $0,794 (maandelijks $547,86). <b> Dit resulteert in een besparing van 15%.</b>

## <a name="q--a"></a>Vragenronde

### <a name="q-what-happens-when-my-credits-run-out"></a>V: wat gebeurt er wanneer mijn tegoed is verlopen?
**A**: wanneer de tegoeden zijn uitgeput, keert de virtuele machine terug naar de basislijn prestaties.

### <a name="q-how-do-you-get-135-baseline-performance-from-a-vm"></a>V: Hoe krijg ik 135% basislijn prestaties van een VM?

**A**: de 135% wordt gedeeld met de 8 vCPU die de VM-grootte vormen. Als uw toepassing bijvoorbeeld 4 van de 8 kernen gebruikt die aan batch verwerking werken en elk van deze vier vCPUs wordt uitgevoerd met 30% gebruik, is de totale hoeveelheid VM-CPU-prestaties gelijk aan 120%.  Dit betekent dat uw virtuele machine de tegoed tijd zou bouwen op basis van de 15% Delta van de basislijn prestaties.  Maar dit betekent ook dat wanneer u een tegoed hebt dat op dezelfde virtuele machine 100% van alle 8 vCPU de maximale CPU-prestaties van 800% mag gebruiken.

### <a name="q-how-can-i-monitor-my-credit-balance-and-consumption"></a>V: hoe kan ik mijn credit saldo en verbruik bewaken?

**A**: met de **krediet** metriek kunt u zien hoeveel tegoeden uw virtuele machine heeft gebankd en hoe de **ConsumedCredit** -metriek laat zien hoeveel CPU-tegoeden uw VM van de Bank heeft verbruikt.    U kunt deze metrische gegevens bekijken in het deel venster metrische gegevens in de portal of via een programma via de Azure Monitor-Api's.

Voor meer informatie over het verkrijgen van toegang tot de metrische gegevens voor Azure, verwijzen we u naar het [overzicht van metrieken in Microsoft Azure](../azure-monitor/data-platform.md).

### <a name="q-how-are-credits-accumulated-and-consumed"></a>V: hoe worden tegoed verzameld en verbruikt?

**A**: de accumulatie van de VM en de verbruiks tarieven zijn zodanig ingesteld dat een virtuele machine die op exact het basis prestatie niveau draait, geen netaccumulatie of het verbruik van bursting-tegoeden heeft.  Een virtuele machine heeft een netto toename van de verantwoording als deze wordt uitgevoerd onder het basis prestatie niveau en heeft een net-afname in het tegoed wanneer de virtuele machine de CPU meer dan het basis prestatie niveau gebruikt.

**Voor beeld**: Ik implementeer een virtuele machine met behulp van de B1ms-grootte voor mijn kleine tijd-en aanwezigheids Database toepassing. Met deze grootte kan mijn toepassing Maxi maal 20% van een vCPU gebruiken als mijn basis lijn. Dit is 0,2 tegoed per minuut die ik kan gebruiken of bank.

Mijn toepassing is aan het begin en einde van mijn werkdag, tussen 7:00-9:00 uur en 4:00-6:13:00. Gedurende de andere 20 uur van de dag is mijn toepassing meestal niet actief, maar alleen met 10% van de vCPU. Voor de niet-piek uren verdienen we 0,2 tegoeden per minuut, maar verbruiken we slechts 0,1 tegoed per minuut, dus mijn virtuele machine Bank 0,1 x 60 = 6 tegoed per uur.  Voor de 20 uur dat ik buiten de piek ben, Bank 120 tegoed.  

Tijdens piek uren heeft mijn toepassing een gemiddelde van 60% vCPU-gebruik, ik krijg nog steeds 0,2 tegoed per minuut, maar ik gebruik meer dan 0,6 tegoed per minuut, voor een nettokosten van 0,4 Credit van een minuut of 0,4 x 60 = 24 tegoed per uur. Ik heb vier uur per dag voor piek gebruik, dus kosten 4 x 24 = 96 tegoed voor mijn piek gebruik.

Als ik de 120-tegoeden heb behaald en u de 96 tegoeden aftrekt die ik heb gebruikt voor mijn piek tijden, Bank ik nog eens 24 tegoed per dag aan die ik kan gebruiken voor andere bursts van de activiteit.

### <a name="q-how-can-i-calculate-credits-accumulated-and-used"></a>V: hoe kan ik tegoed berekenen dat wordt gecumuleerd en gebruikt?

**A**: u kunt de volgende formule gebruiken:

(Basis CPU-prestaties van de VM-CPU-gebruik)/100 = Credit Bank of gebruik per minuut

bijvoorbeeld: in het bovenstaande exemplaar is uw basis lijn 20%. Als u 10% van de CPU gebruikt, wordt er een accumulatie uitgevoerd (20%-10%)/100 = 0,1 tegoed per minuut.

### <a name="q-does-the-b-series-support-premium-storage-data-disks"></a>V: biedt de B-serie ondersteuning Premium Storage gegevens schijven?

**A**: Ja, alle grootten van de B-serie ondersteunen Premium Storage gegevens schijven.

### <a name="q-why-is-my-remaining-credit-set-to-0-after-a-redeploy-or-a-stopstart"></a>V: Waarom is mijn resterende tegoed ingesteld op 0 na het opnieuw implementeren of stoppen/starten?

**A** : wanneer een virtuele machine opnieuw wordt geïmplementeerd en de VM naar een ander knoop punt wordt verplaatst, gaat het geaccumuleerde tegoed verloren. Als de virtuele machine is gestopt/gestart, maar blijft op hetzelfde knoop punt, behoudt de VM het geaccumuleerde tegoed. Wanneer de VM wordt vernieuwd op een knoop punt, ontvangt deze een eerste tegoed, voor Standard_B8ms deze 240 is.

### <a name="q-what-happens-if-i-deploy-an-unsupported-os-image-on-b1ls"></a>V: wat gebeurt er als ik een niet-ondersteunde installatie kopie van het besturings systeem Implementeer op B1ls?

**A** : B1ls biedt alleen ondersteuning voor Linux-installatie kopieën en als u een andere installatie kopie van het besturings systeem implementeert, is het mogelijk dat u niet de beste klant ervaring krijgt.

## <a name="other-sizes-and-information"></a>Andere grootten en informatie

- [Algemeen gebruik](sizes-general.md)
- [Geoptimaliseerde rekenkracht](sizes-compute.md)
- [Geoptimaliseerd voor geheugen](sizes-memory.md)
- [Geoptimaliseerd voor opslag](sizes-storage.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Krachtig rekenvermogen](sizes-hpc.md)

Prijs calculator: [prijs calculator](https://azure.microsoft.com/pricing/calculator/)

Meer informatie over schijven typen: [schijf typen](./disks-types.md#ultra-disk)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe [Azure Compute units (ACU)](acu.md) u kan helpen bij het vergelijken van de reken prestaties in azure-sku's.
