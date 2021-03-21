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
ms.openlocfilehash: 82b4c127f983f3133326bf7fb538e40713ef9655
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100580390"
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

