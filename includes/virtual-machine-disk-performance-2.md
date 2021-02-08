---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 10/12/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 3c4ab8362b2a717a348a59c0baf829b61e1a8006
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2021
ms.locfileid: "99808467"
---
![Grafiek met de specificaties van D s v 3.](media/vm-disk-performance/dsv3-documentation.jpg)

- De maximale door Voer van een schijf *in cache* is de standaard opslag limiet die de virtuele machine kan verwerken.
- De maximale doorvoer limiet voor opslag *in de cache* is een afzonderlijke limiet bij het inschakelen van de caching van hosts.

Bij het opslaan van de host wordt de opslag dichter bij de virtuele machine geplaatst die snel kan worden geschreven of gelezen. De hoeveelheid opslag ruimte die voor de virtuele machine beschikbaar is, is in de documentatie. U kunt bijvoorbeeld zien dat de Standard_D8s_v3 wordt geleverd met 200 GiB van cache opslag.

U kunt caching van host inschakelen wanneer u de virtuele machine maakt en schijven koppelt. U kunt ook de opslag van hosts in-of uitschakelen op uw schijven op een bestaande virtuele machine.

![Scherm opname van host-caching.](media/vm-disk-performance/host-caching.jpg)

U kunt de caching van de host aanpassen zodat deze overeenkomt met de vereisten van de werk belasting voor elke schijf. U kunt de caching van de host als volgt instellen:

- **Alleen-lezen**: voor workloads waarvoor alleen lees bewerkingen worden uitgevoerd
- **Lezen/schrijven**: voor werk belastingen die een evenwicht hebben met lees-en schrijf bewerkingen

Als uw werk belasting niet aan een van deze patronen voldoet, raden we u aan de opslag in de host niet te gebruiken.

Laten we een paar voor beelden van verschillende instellingen voor de host-cache door lopen om te zien hoe dit van invloed is op de gegevens stroom en prestaties. In dit eerste voor beeld kijken we wat er gebeurt met IO-aanvragen wanneer de instelling voor het opslaan van de host is ingesteld op **alleen-lezen**.

**Sample**

- Standard_D8s_v3
  - IOPS in cache: 16.000
  - IOPS niet in cache: 12.800
- P30-gegevens schijf
  - IOPS: 5.000
  - Caching van host: **alleen-lezen**

Wanneer een lees bewerking wordt uitgevoerd en de gewenste gegevens beschikbaar zijn op de cache, retourneert de cache de aangevraagde gegevens. Het is niet nodig om te lezen van de schijf. Deze Lees bewerking wordt geteld bij de cache limieten van de virtuele machine.

![Diagram waarin een lees bewerking voor het lezen van host-caching wordt weer gegeven.](media/vm-disk-performance/host-caching-read-hit.jpg)

Wanneer een lees bewerking wordt uitgevoerd en de gewenste gegevens *niet* beschikbaar zijn in de cache, wordt de Lees aanvraag door gegeven aan de schijf. Vervolgens wordt de schijf geoppereerd op zowel de cache als de virtuele machine. Deze Lees bewerking wordt meegeteld bij zowel de niet-cache limiet van de virtuele machine als de limiet voor de cache van de virtuele machine.

![Diagram van het lezen van een read host caching-Missing.](media/vm-disk-performance/host-caching-read-miss.jpg)

Wanneer een schrijf bewerking wordt uitgevoerd, moet de schrijf bewerking naar de cache en de schijf worden geschreven voordat deze wordt beschouwd als voltooid. Deze schrijf bewerking wordt geteld bij de limiet van de virtuele machine die in de cache is opgeslagen en de limiet van de virtuele machine.

![Diagram waarin de schrijf bewerking voor het lezen van de host wordt opgeslagen.](media/vm-disk-performance/host-caching-write.jpg)

Nu gaan we kijken wat er gebeurt met IO-aanvragen wanneer de cache-instelling voor de host is ingesteld op **lezen/schrijven**.

**Sample**

- Standard_D8s_v3
  - IOPS in cache: 16.000
  - IOPS niet in cache: 12.800
- P30-gegevens schijf
  - IOPS: 5.000
  - Host-caching: **lezen/schrijven**

Een lees bewerking wordt op dezelfde manier behandeld als een alleen-lezen. Schrijf bewerkingen zijn het enige wat er anders is dan in de cache voor lezen/schrijven. Wanneer u schrijft met host-caching is ingesteld op **lezen/schrijven**, moet de schrijf bewerking naar de host-cache worden geschreven om als voltooid te worden beschouwd. De schrijf bewerking wordt vervolgens naar de schijf vertraagd geschreven als een achtergrond proces. Dit betekent dat een schrijf bewerking naar de in cache geplaatste IO wordt gerekend wanneer deze naar de cache wordt geschreven. Wanneer de vertraagd naar de schijf wordt geschreven, telt deze naar de niet-gecachede IO.

![Diagram van de schrijf bewerking voor het lezen/schrijven van hosts in de cache.](media/vm-disk-performance/host-caching-read-write.jpg)

We gaan nu verder met onze Standard_D8s_v3 virtuele machine. Met uitzonde ring van deze tijd gaan we caching van host inschakelen op de schijven. Nu is de limiet voor IOPS van de VM 16.000 IOPS. Gekoppeld aan de VM zijn drie onderliggende P30-schijven die elk 5.000 IOPS kunnen verwerken.

**Sample**

- Standard_D8s_v3
  - IOPS in cache: 16.000
  - IOPS niet in cache: 12.800
- P30-besturingssysteem schijf
  - IOPS: 5.000
  - Host-caching: **lezen/schrijven**
- Twee P30-gegevens schijven × 2
  - IOPS: 5.000
  - Host-caching: **lezen/schrijven**

![Diagram waarin een voor beeld van het opslaan van hosts wordt weer gegeven.](media/vm-disk-performance/host-caching-example-without-remote.jpg)

De toepassing maakt gebruik van een Standard_D8s_v3 virtuele machine waarvoor caching is ingeschakeld. Er wordt een aanvraag voor 15.000 IOPS gemaakt. De aanvragen worden uitgesplitst tot 5.000 IOPS voor elke onderliggende schijf die is gekoppeld. Er treedt geen prestatie limiet op.

## <a name="combined-uncached-and-cached-limits"></a>Gecombineerde en in cache opgeslagen limieten

De limieten voor de cache van een virtuele machine zijn gescheiden van de limieten voor de niet-cache. Dit betekent dat u de caching van hosts kunt inschakelen op schijven die zijn gekoppeld aan een virtuele machine terwijl het in de cache opslaan van hosts op andere schijven niet is ingeschakeld. Met deze configuratie kunnen uw virtuele machines een totale opslag-IO ontvangen van de limiet van de cache plus de limiet voor niet-cache geheugen.

Laten we een voor beeld gebruiken om te begrijpen hoe deze limieten samen werken. We gaan verder met de Standard_D8s_v3 virtuele machine en Premium-schijven die zijn gekoppeld aan de configuratie.

**Sample**

- Standard_D8s_v3
  - IOPS in cache: 16.000
  - IOPS niet in cache: 12.800
- P30-besturingssysteem schijf
  - IOPS: 5.000
  - Host-caching: **lezen/schrijven**
- Twee P30-gegevens schijven × 2
  - IOPS: 5.000
  - Host-caching: **lezen/schrijven**
- Twee P30-gegevens schijven × 2
  - IOPS: 5.000
  - Caching van host: **uitgeschakeld**

![Diagram waarin een voor beeld van het opslaan van hosts met externe opslag wordt weer gegeven.](media/vm-disk-performance/host-caching-example-with-remote.jpg)

In dit geval voert de toepassing die op een Standard_D8s_v3 virtuele machine wordt uitgevoerd, een aanvraag voor 25.000 IOPS. De aanvraag wordt opgesplitst in 5.000 IOPS voor elk van de gekoppelde schijven. Drie schijven gebruiken caching en twee schijven gebruiken geen host-caching.

- Aangezien de drie schijven die gebruikmaken van de caching binnen de cache limieten van 16.000, worden deze aanvragen voltooid. Er zijn geen prestatie limieten voor opslag.
- Omdat de twee schijven die geen gebruik maken van de caching binnen de niet-cache grenzen van 12.800, worden deze aanvragen ook voltooid. Er is geen sprake van afgetopting.

## <a name="disk-performance-metrics"></a>Metrische schijf prestaties

We hebben metrische gegevens over Azure die inzicht bieden in hoe uw virtuele machines en schijven worden uitgevoerd. Deze metrische gegevens kunnen worden weer gegeven via de Azure Portal. Ze kunnen ook worden opgehaald via een API-aanroep. Metrische gegevens worden berekend over een interval van één minuut. De volgende metrische gegevens zijn beschikbaar om inzicht te krijgen in de virtuele machine en de schijf-i/o en de prestaties van de door Voer:

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

## <a name="storage-io-utilization-metrics-example"></a>Voor beeld van metrische gegevens voor gebruik van i/o-opslag

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
