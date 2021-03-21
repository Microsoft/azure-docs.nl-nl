---
title: Azure File Sync Cloud lagen controleren | Microsoft Docs
description: Details over de metrische gegevens die moeten worden gebruikt voor het bewaken van uw Cloud-laag beleid.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/4/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: c27916afb0d199bcb32db9d43202e552a4a04f53
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104593132"
---
# <a name="monitor-cloud-tiering"></a>Cloud lagen bewaken
Er zijn twee manieren waarop u het beleid voor Cloud lagen kunt bewaken: de Blade eigenschappen van het server eindpunt en Azure Monitor.

## <a name="monitoring-via-server-endpoint"></a>Bewaking via server eindpunt

Meld u aan bij de [Azure Portal](https://portal.azure.com/), navigeer naar de Blade eigenschappen van het **Server** eindpunt voor het server eindpunt dat u wilt bewaken en schuif omlaag naar de sectie Cloud-laag. 

![Een scherm afbeelding van de sectie voor het Cloud lagen in de eigenschappen van het server eindpunt.](media/storage-sync-monitoring-cloud-tiering/cloud-tiering-monitoring-5.png)

**Ruimte besparing** is de hoeveelheid ruimte die wordt bespaard door Cloud lagen in te scha kelen. Als de grootte van uw lokale volume groot genoeg is voor al uw gegevens, hebt u 0% ruimte besparing. Als u 0% of een laag percentage ruimte besparingen hebt, kan dit erop wijzen dat de Cloud lagen niet gunstig zijn voor de huidige lokale cache grootte. 

**Percentage treffers in cache** is het percentage bestanden dat u in de lokale cache opent. Het percentage treffers in de cache wordt berekend op basis van bestanden die rechtstreeks vanuit cache worden geopend/totaal aantal geopende bestanden. Het percentage van de cache treffer van 100% betekent dat alle bestanden die u in het afgelopen uur hebt geopend, al in uw lokale cache waren. Als uw doel is om de uitgevallen te verminderen, zou dit erop wijzen dat uw huidige beleid goed werkt.

> [!NOTE]
> Werk belastingen met wille keurige toegangs patronen hebben doorgaans lagere tarieven voor cache treffers. 

**Totale grootte (Cloud)** is de grootte van uw bestanden die zijn gesynchroniseerd met de Cloud. 

**Cache grootte (Server)** is de totale grootte van de bestanden op uw server, zowel gedownload als gelaagd. Soms is de grootte van de cache groter dan de totale grootte van uw bestanden in de Cloud. Variabelen, zoals de cluster grootte van het volume op de server, kunnen dit tot gevolg hebben. Als de cache grootte groter is dan u wilt, kunt u overwegen om uw volume beschik bare ruimte-beleid te verg Roten. 

**Effectief volume beleid** wordt gebruikt door Azure file sync om te bepalen hoeveel beschik bare ruimte op het volume waarop uw server eindpunt is ingeschakeld, moet worden achtergelaten. Wanneer er meerdere server-eind punten op hetzelfde volume zijn, wordt het hoogste volume beschik bare ruimte beleid van de server eindpunten het effectief volume beleid voor alle server eindpunten op dat volume. Als er bijvoorbeeld twee server-eind punten op hetzelfde volume zijn, een met 30% volume beschik bare ruimte en een ander volume met 50% beschik bare ruimte, is het efficiënte volume beschik bare ruimte beleid voor beide server eindpunten 50%.

**Huidig volume** beschik bare ruimte is de hoeveelheid vrije ruimte op het volume dat momenteel beschikbaar is op uw on-premises server. Als u een hoge uitzonde ring hebt, maar nog meer volume beschik bare ruimte hebt voordat het volume van de beschik bare ruimte op uw apparaat wordt geactiveerd, kunt u uw datum beleid uitschakelen. Een ander probleem kan zich voordoen als de huidige gelaagde bestanden, het laatst geopende bestand is groter dan de hoeveelheid beschik bare volume ruimte voordat het beleid wordt gebruikt. In dit geval kunt u de grootte van het lokale volume verg Roten. 

## <a name="monitoring-via-azure-monitor"></a>Bewaking via Azure Monitor

Ga naar de **opslag synchronisatie service** en selecteer **metrische gegevens** aan de zijkant. 

Er zijn drie verschillende metrische gegevens die u kunt gebruiken om uw uitvoerder te controleren, specifiek vanuit Cloud lagen:

- Grootte van intrekken Cloud lagen
    - Dit is de totale grootte van de gegevens die worden ingetrokken in bytes en kan worden gebruikt om te bepalen hoeveel gegevens er worden ingetrokken.
- Grootte van intrekken van Cloud lagen op toepassing
    - Dit is de totale grootte van gegevens die door een toepassing worden ingetrokken in bytes en kan worden gebruikt om aan te geven wat de gegevens aanroept.
- Door Voer van Cloud lagen intrekken
    - Hiermee wordt gemeten hoe snel de gegevens in bytes worden ingetrokken en moeten ze worden gebruikt als u problemen hebt met de prestaties. 

Als u meer wilt weten over de weer gave van uw grafieken, kunt u overwegen **filter toevoegen** te gebruiken en splitsen toe te **passen**.
 
Zie [Azure file sync bewaken](storage-sync-files-monitoring.md)voor meer informatie over de verschillende soorten metrische gegevens voor Azure file sync en hoe u deze kunt gebruiken.

Zie aan de slag [met Azure Metrics Explorer](../../azure-monitor/essentials/metrics-getting-started.md)voor meer informatie over het gebruik van metrische gegevens.

Als u uw Cloud-laag beleid wilt wijzigen, raadpleegt u [beleid voor Cloud lagen kiezen](storage-sync-choose-cloud-tiering-policies.md).

## <a name="next-steps"></a>Volgende stappen
* [Planning voor een Azure Files Sync-implementatie](storage-sync-files-planning.md)