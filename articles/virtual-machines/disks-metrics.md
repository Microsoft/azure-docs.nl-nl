---
title: Metrische schijf gegevens
description: Voor beelden van metrische gegevens voor schijf bursting
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 60486c41ad843cf193ee0648dfcfef66f7668e47
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101674976"
---
# <a name="disk-performance-metrics"></a>Metrische schijf prestaties
Azure biedt metrische gegevens in de Azure Portal die inzicht bieden in de manier waarop uw virtuele machines (VM) en schijven worden uitgevoerd. De metrische gegevens kunnen ook worden opgehaald via een API-aanroep. Dit artikel is onderverdeeld in drie subsecties:

- **Schijf-i/o, door Voer en wachtrij diepte metrische gegevens** : met deze metrische gegevens kunt u de opslag prestaties van het perspectief van een schijf en een virtuele machine bekijken.
- **Metrische gegevens over schijf bursting** : Dit zijn de metrische gegevens die u kunt gebruiken om de [bursting](disk-bursting.md) -functie op onze Premium-schijven in te delen.
- **Metrische gegevens over het gebruik van i/o-opslag** : met deze metrische gegevens kunt u knel punten in uw opslag prestaties vaststellen met schijven. 

Alle metrische gegevens worden elke minuut verzonden, met uitzonde ring van de waarde voor het bursting-krediet percentage, dat elke vijf minuten wordt verzonden.

## <a name="disk-io-throughput-and-queue-depth-metrics"></a>Metrische gegevens over de schijf-i/o-, doorvoer-en wachtrij diepte
De volgende metrische gegevens zijn beschikbaar om inzicht te krijgen in de prestaties van de VM en de schijf-i/o, door Voer en de wachtrij diepte:

- **Wachtrij niveau van de besturingssysteem schijf**: het aantal huidige openstaande i/o-aanvragen dat wacht om te lezen van of te worden geschreven naar de besturingssysteem schijf.
- **Besturingssysteem schijf gelezen bytes per seconde**: het aantal bytes dat in een seconde wordt gelezen van de besturingssysteem schijf.
- **Lees bewerkingen van de besturingssysteem schijf per seconde**: het aantal invoer bewerkingen dat in een seconde van de besturingssysteem schijf wordt gelezen.
- **Schrijf bewerkingen op de besturingssysteem schijf per seconde**: het aantal bytes dat is geschreven in een seconde van de besturingssysteem schijf.
- **Schrijf bewerkingen op de besturingssysteem schijf per seconde**: het aantal uitvoer bewerkingen dat is geschreven in een seconde van de besturingssysteem schijf.
- **Wachtrij-diepte van gegevens schijf**: het aantal huidige openstaande i/o-aanvragen dat wacht op het lezen van of schrijven naar de gegevens schijf (en).
- **Gegevens schijf gelezen bytes per seconde**: het aantal bytes dat is gelezen in een seconde van de gegevens schijf (s).
- **Lees bewerkingen op de gegevens schijf per seconde**: het aantal invoer bewerkingen dat in een seconde wordt gelezen vanaf een of meer gegevens schijven.
- **Geschreven bytes per seconde van gegevens schijf**: het aantal bytes dat is geschreven in een seconde van de gegevens schijf (s).
- **Schrijf bewerkingen op de gegevens schijf per seconde**: het aantal uitvoer bewerkingen dat in een seconde van een of meer gegevens schijven is geschreven.
- **Gelezen bytes per seconde**: het totale aantal bytes dat is gelezen in een seconde van alle schijven die zijn gekoppeld aan een virtuele machine.
- **Lees bewerkingen op de schijf per seconde**: het aantal invoer bewerkingen dat in een seconde wordt gelezen van alle schijven die zijn gekoppeld aan een virtuele machine.
- **Geschreven bytes per seconde**: het aantal bytes dat is geschreven in een seconde van alle schijven die zijn gekoppeld aan een virtuele machine.
- **Schrijf bewerkingen op de schijf per seconde**: het aantal uitvoer bewerkingen dat is geschreven in een tweede van alle schijven die zijn gekoppeld aan een virtuele machine.

## <a name="bursting-metrics"></a>Bursting-metrische gegevens
De volgende metrische gegevens helpen u bij het opsplitsen van de [bursting](disk-bursting.md) -functie op onze Premium-schijven:

- **Maximale burst-band breedte van de gegevens schijf**: de doorvoer limiet waarmee de gegevens schijven kunnen worden gesplitst.
- **Maximale burst-band breedte van de besturingssysteem schijf**: de doorvoer limiet waarmee de besturingssysteem schijf kan worden gesplitst.
- **Maximale burst-IOPS van de gegevens schijf**: de IOPS-limiet waarmee de gegevens schijven kunnen worden gesplitst.
- **Maximale burst-IOPS van de besturingssysteem schijf**: de limiet voor IOPS waarmee de besturingssysteem schijf kan worden gesplitst.
- **Doel bandbreedte van de gegevens schijf**: de doorvoer limiet die de gegevens schijf kan bereiken zonder bursting.
- **Doel bandbreedte van de besturingssysteem schijf**: de doorvoer limiet die de besturingssysteem schijf kan bereiken zonder bursting.
- **Doel-IOPS van de gegevens schijf**: de IOPS-limiet die de gegevens schijf (en) kan bereiken zonder bursting.
- **Doel-IOPS van de besturingssysteem schijf**: de IOPS-limiet die de gegevens schijf (en) kan bereiken zonder bursting.
- **Gebruikte gegevens schijf burst bps-tegoed percentage**: het gecumuleerde percentage van de door Voer burst dat wordt gebruikt voor de gegevens schijf (s). Verzonden met een interval van 5 minuten.
- **Gebruikte besturingssysteem schijf burst bps credit-percentage**: het gecumuleerde percentage van de door Voer-burst dat wordt gebruikt voor de besturingssysteem schijf. Verzonden met een interval van 5 minuten.
- **Gebruikte gegevens schijf burst io-tegoed percentage**: het gecumuleerde percentage van de IOPS-burst dat wordt gebruikt voor de gegevens schijf (s). Verzonden met een interval van 5 minuten.
- **Gebruikte besturingssysteem schijf burst io-tegoed percentage**: het gecumuleerde percentage van de IOPS burst dat wordt gebruikt voor de besturingssysteem schijf. Verzonden met een interval van 5 minuten.

## <a name="storage-io-utilization-metrics"></a>Metrische gegevens over gebruik van i/o-opslag
Met de volgende metrische gegevens kunt u knel punten in de combi natie van virtuele machines en schijven vaststellen. Deze metrische gegevens zijn alleen beschikbaar wanneer u een virtuele machine met Premium ingeschakeld gebruikt. Deze metrische gegevens zijn beschikbaar voor alle schijf typen, met uitzonde ring van Ultra. 

Metrische gegevens die helpen bij het vaststellen van de oorzaak van schijf-i/o-limiet:

- **Verbruikte percentage van de gegevens schijf (IOPS**): het percentage dat is berekend door de gegevens schijf IOPS is voltooid via de ingerichte gegevens schijf IOPS. Als dit bedrag ten 100% bedraagt, wordt de toepassing IO afgetopteerd van de IOPS-limiet van uw gegevens schijf.
- **Percentage gebruikte band breedte** van de gegevens schijf: het percentage dat is berekend door de door Voer van de gegevens schijf is voltooid voor de door Voer van de ingerichte gegevens schijf. Als dit bedrag ten 100% bedraagt, wordt de toepassing IO afgetopteerd van de bandbreedte limiet van uw gegevens schijf.
- **Verbruikte percentage van de besturingssysteem schijf**: het percentage dat is berekend door de schijf-IOPS van het besturings systeem dat is voltooid voor de ingerichte IOPS van de besturingssysteem schijf. Als dit bedrag ten 100% bedraagt, wordt de toepassing IO afgetopteerd van de IOPS-limiet van de besturingssysteem schijf.
- **Percentage van verbruikte schijf bandbreedte van het besturings systeem**: het percentage dat is berekend door de door Voer van de besturingssysteem schijf is voltooid voor de door Voer van de ingerichte besturingssysteem schijf. Als dit bedrag ten 100% bedraagt, wordt de toepassing IO afgetopteerd van de bandbreedte limiet van uw besturings systeem.

Metrische gegevens die helpen bij het vaststellen van de oorzaak van het opsporen van VM-IO:

- **In cache opgeslagen IOPS percentage** van de VM: het percentage dat is berekend door het totale aantal IOPS dat is voltooid voor de maximale IOPS-limiet van de virtuele machine in de cache. Als dit bedrag ten 100% bedraagt, wordt er in de toepassing IO afgetopteerd van de IOPS-limiet van de cache van de virtuele machine.
- **Percentage van verbruikte band breedte in de cache**: het percentage dat is berekend door de totale door Voer van de schijf is voltooid voor de maximale door Voer van de virtuele machine in de cache. Als dit bedrag 100% is, wordt er in de toepassing die wordt uitgevoerd, IO-limiet van de band breedte van de virtuele machine in de cache gebruikt.
- **VM ongecached percentage verbruikte IOPS**: het percentage dat is berekend door het totale aantal IOPS op een virtuele machine is voltooid met de Maxi maal aantal IOPS-limieten voor virtuele machines die niet in de cache zijn geplaatst. Als dit bedrag ten 100% bedraagt, wordt de toepassing IO afgetopteerd van de IOPS-limiet van uw VM.
- **Percentage van verbruikte band breedte voor VM niet in cache**: het percentage dat is berekend door de totale schijf doorvoer voor een virtuele machine, is voltooid met de Maxi maal ingerichte door Voer van de virtuele machine. Als dit bedrag ten 100% bedraagt, wordt de toepassing IO afgetopteerd van de bandbreedte limiet voor de niet-cache van uw VM.

## <a name="storage-io-metrics-example"></a>Voor beeld van metrische opslag-i/o-gegevens

Laten we een voor beeld zien van het gebruik van deze nieuwe metrische gegevens over i/o-capaciteits gebruik om te helpen bij het oplossen van fouten in het systeem. De installatie van het systeem is hetzelfde als in het vorige voor beeld, behalve wanneer de gekoppelde besturingssysteem schijf *niet* in de cache wordt opgeslagen.

**Sample**

- Standard_D8s_v3
  - IOPS in cache: 16.000
  - IOPS niet in cache: 12.800
- P30-besturingssysteem schijf
  - IOPS: 5.000
  - Caching van host: **uitgeschakeld**
- Twee P30-gegevens schijven × 2
  - IOPS: 5.000
  - Host-caching: **lezen/schrijven**
- Twee P30-gegevens schijven × 2
  - IOPS: 5.000
  - Caching van host: **uitgeschakeld**

Laten we een benchmark test uitvoeren op deze virtuele machine en schijf combinatie waarmee i/o-activiteiten worden gemaakt. Zie [Bench Mark your application on Azure Disk Storage](disks-benchmarks.md)voor meer informatie over de Bench Mark-opslag-io in Azure. Vanuit het hulp programma benchmarking kunt u zien dat de combi natie van de virtuele machine en schijf 22.800 IOPS kan behaalt:

![Scherm opname van f i o-uitvoer met r = 22.8 k gemarkeerd.](media/disks-metrics/utilization-metrics-example/fio-output.jpg)



Het Standard_D8s_v3 kan een totaal van 28.600 IOPS opleveren. Door de metrische gegevens te gebruiken, gaan we kijken wat er gebeurt en hoe we de opslag-i/o-knel punt kunnen identificeren. Selecteer **metrische gegevens** in het linkerdeel venster:

![Scherm afbeelding met metrische gegevens gemarkeerd in het linkerdeel venster.](media/disks-metrics/utilization-metrics-example/metrics-menu.jpg)

Laten we eerst kijken naar onze in het **cache geheugen geplaatste IOPS percentage van verbruikte** gegevens:

![Scherm opname van V M in cache opgeslagen I. P S verbruikt percentage.](media/disks-metrics/utilization-metrics-example/vm-cached.jpg)

Met deze metrische gegevens wordt aangegeven dat 61% van de 16.000 IOPS die aan de in cache geplaatste IOPS op de VM zijn toegewezen, wordt gebruikt. Dit percentage betekent dat de opslag-i/o-bottleneck zich niet bevindt op de schijven die in de cache zijn opgeslagen omdat deze niet 100% zijn. Laten we nu eens kijken naar onze **virtuele machine ongecached aantal verbruikte IOPS percentage** voor waarde:

![Scherm opname van V M, niet in cache geplaatste I & 1 S verbruikt percentage.](media/disks-metrics/utilization-metrics-example/vm-uncached.jpg)

Deze waarde is 100%. Hiermee wordt aangegeven dat alle 12.800 IOPS die aan de niet in cache geplaatste IOPS op de VM zijn toegewezen, worden gebruikt. Eén manier om dit probleem op te lossen is het wijzigen van de grootte van de VM naar een grotere grootte die de extra IO kan afhandelen. Maar voordat we dat doen, kijken we naar de gekoppelde schijf om erachter te komen hoeveel IOPS er worden weer gegeven. Controleer de besturingssysteem schijf door te kijken naar het **percentage verbruikte schijf van het besturings systeem**:

![Scherm opname van de O S schijf I O P S verbruikt percentage.](media/disks-metrics/utilization-metrics-example/os-disk.jpg)

Deze metriek vertelt ons dat ongeveer 90% van de 5.000 IOPS die is ingericht voor deze P30-besturingssysteem schijf wordt gebruikt. Dit percentage betekent dat er geen knel punt is op de besturingssysteem schijf. Nu gaan we de gegevens schijven die zijn gekoppeld aan de virtuele machine controleren door te kijken naar de **gegevens schijf percentage verbruikte geconsumeerde**%:

![Scherm opname van de gegevens schijf I O P S verbruikt percentage.](media/disks-metrics/utilization-metrics-example/data-disks-no-splitting.jpg)

Met deze metrische gegevens wordt aangegeven dat het gemiddelde aantal verbruikte IOPS-percentages voor alle gekoppelde schijven ongeveer 42% is. Dit percentage wordt berekend op basis van de IOPS die worden gebruikt door de schijven en worden niet vanuit de host-cache geleverd. Laten we in deze metrische gegevens dieper inzoomen door *splitsingen* op deze metrische gegevens toe te passen en te splitsen door de LUN-waarde:

![Scherm opname van de gegevens schijf I O P S verbruikt percentage met splitsen.](media/disks-metrics/utilization-metrics-example/data-disks-splitting.jpg)

Met deze metrische gegevens wordt aangegeven dat aan de aan LUN 3 en 2 gekoppelde gegevensschijven ongeveer 85% van de ingerichte IOPS wordt gebruikt. Hier volgt een overzicht van hoe de IO eruit ziet vanuit de architectuur van de VM en de schijven:

![Diagram van het voor beeld van opslag van I/O-metrische gegevens.](media/disks-metrics/utilization-metrics-example/metrics-diagram.jpg)

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure Monitor metrische gegevens](../azure-monitor/essentials/data-platform-metrics.md)
- [Uitleg bij aggregatie van metrische gegevens](../azure-monitor/essentials/metrics-aggregation-explained.md)
- [Metrische waarschuwing maken, bekijken en beheren met Azure Monitor](../azure-monitor/alerts/alerts-metric.md)