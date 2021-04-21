---
title: Opslaglagen Azure File Sync cloud bewaken | Microsoft Docs
description: Details over metrische gegevens die moeten worden gebruikt voor het bewaken van uw beleid voor cloudopslaglagen.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: cbdde16ff214077d7fe4814c8841d805fe05c37c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796701"
---
# <a name="monitor-cloud-tiering"></a>Opslag in cloudlagen bewaken
Er zijn twee manieren waarop u uw beleid voor cloudopslaglagen kunt bewaken: de blade eigenschappen van het server Azure Monitor.

## <a name="monitoring-via-server-endpoint"></a>Bewaking via server-eindpunt

Meld u aan bij [de Azure Portal](https://portal.azure.com/), navigeer naar de blade eigenschappen van het **server-eindpunt** voor het server-eindpunt dat u wilt bewaken en schuif omlaag naar de sectie Opslag in cloudlagen. 

![Een schermopname van de sectie cloudopslaglagen in de eigenschappen van server-eindpunten.](media/storage-sync-monitoring-cloud-tiering/cloud-tiering-monitoring-5.png)

**Ruimtebesparing** is de hoeveelheid ruimte die wordt bespaard door opslag in cloudlagen in te stellen. Als uw lokale volumegrootte groot genoeg is voor al uw gegevens, hebt u 0% ruimtebesparing. Als u een besparing van 0% of weinig ruimte hebt, kan dit erop wijzen dat opslag in cloudlagen niet voordelig is met uw huidige lokale cachegrootte. 

**Het trefferpercentage** van de cache is het percentage bestanden dat u in uw lokale cache opent. De treffers in de cache worden berekend door bestanden die rechtstreeks vanuit de cache/het totale aantal geopende bestanden worden geopend. Een cachefrequentie van 100% betekent dat alle bestanden die u in het afgelopen uur hebt geopend, zich al in uw lokale cache hebben geplaatst. Als het uw doel is om het aantal uit te voeren gegevens te verminderen, geeft dit aan dat uw huidige beleid goed werkt.

> [!NOTE]
> Workloads met willekeurige toegangspatronen hebben doorgaans lagere treffers in de cache. 

**Totale grootte (cloud)** is de grootte van uw bestanden die zijn gesynchroniseerd met de cloud. 

**De grootte van de cache (server)** is de totale grootte van bestanden op uw server, zowel gedownload als gelaagd. Soms is de cachegrootte groter dan de totale grootte van uw bestanden in de cloud. Variabelen zoals de clustergrootte van het volume op de server kunnen dit veroorzaken. Als de grootte van de cache groter is dan u wilt, kunt u overwegen uw beleid voor vrije volumeruimte te verhogen. 

**Effectief volumebeleid** wordt gebruikt door Azure File Sync om te bepalen hoeveel vrije ruimte er op het volume van uw server-eindpunt moet worden verlaten. Wanneer er meerdere server eindpunten op hetzelfde volume, het hoogste volume vrije ruimte beleid tussen de server-eindpunten wordt het effectieve volumebeleid voor alle server-eindpunten op dat volume. Als er bijvoorbeeld twee server eindpunten op hetzelfde volume zijn, één met 30% vrije volumeruimte en een andere met 50% vrije volumeruimte, is het effectieve beleid voor vrije volumeruimte voor beide server-eindpunten 50%.

**De huidige beschikbare volumeruimte** is de beschikbare volumeruimte op uw on-premises server. Als u een hoog aantal uit te voeren, maar meer vrije ruimte op het volume beschikbaar voordat uw beleid voor vrije ruimte op volume wordt ingegaan, kunt u overwegen uw datumbeleid uit te stellen. Een ander probleem kan zijn dat van de momenteel gelaagde bestanden het bestand dat het laatst is gebruikt, groter is dan de beschikbare volumeruimte die overblijft voordat het beleid wordt uitgevoerd. In dit geval kunt u overwegen om het lokale volume te vergroten. 

## <a name="monitoring-via-azure-monitor"></a>Bewaking via Azure Monitor

Ga naar de **opslagsynchronisatieservice** en selecteer **Metrische** gegevens in het navigatiedeelvenster aan de zijkant. 

Er zijn drie verschillende metrische gegevens die u kunt gebruiken om uw egress specifiek vanuit opslag in cloudlagen te bewaken:

- In cloudlagen terughalen grootte
    - Dit is de totale grootte van de gegevens die in bytes worden ingeroepen en kan worden gebruikt om te bepalen hoeveel gegevens er worden ingeroepen.
- In cloudlagen terughalen grootte per toepassing
    - Dit is de totale grootte van gegevens die in bytes worden ingeroepen door een toepassing en kan worden gebruikt om te identificeren wat de gegevens inroept.
- Terughalen van doorvoer in cloudlagen
    - Hiermee meet u hoe snel de gegevens in bytes worden ingeroepen en moet worden gebruikt als u zich zorgen maakt over de prestaties. 

Als u specifieker wilt zijn wat uw grafieken moeten weergeven, kunt u Filter **toevoegen** en **Splitsen toepassen gebruiken.**
 
Zie Monitor Azure File Sync voor meer informatie over de verschillende typen metrische gegevens voor Azure File Sync en hoe u [deze Azure File Sync.](file-sync-monitoring.md)

Zie Aan de slag met Azure Metrics Explorer. voor meer informatie [over het gebruik van metrische gegevens.](../../azure-monitor/essentials/metrics-getting-started.md)

Zie Beleid voor cloudopslaglagen kiezen als u uw beleid voor cloudopslaglagen [wilt wijzigen.](file-sync-choose-cloud-tiering-policies.md)

## <a name="next-steps"></a>Volgende stappen

* [Planning voor een Azure Files Sync-implementatie](file-sync-planning.md)