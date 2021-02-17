---
title: Bewaking-Azure Database for MySQL-flexibele server
description: In dit artikel worden de metrische gegevens voor bewaking en waarschuwingen voor Azure Database for MySQL flexibele server beschreven, met inbegrip van CPU-, opslag-en verbindings statistieken.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 9/21/2020
ms.openlocfilehash: c2883a2ddba9a322c09a318aa9f232de5f4704b4
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100595153"
---
# <a name="monitor-azure-database-for-mysql-flexible-servers-with-built-in-metrics"></a>Azure Database for MySQL flexibele servers bewaken met ingebouwde metrische gegevens

> [!IMPORTANT] 
> Azure Database for MySQL - Flexible Server is momenteel beschikbaar als openbare preview.

Azure Database for MySQL flexibele server biedt bewaking van servers via Azure Monitor. Metrische gegevens zijn numerieke waarden die een aspect van de resources van uw server op een bepaald moment beschrijven. Door de resources van uw server te bewaken, kunt u problemen met uw werk belasting oplossen en optimaliseren door u te laten zien wat het meest voor u belang rijk is. Door de juiste metrische gegevens te controleren, kunt u de prestaties, betrouw baarheid en beschik baarheid van uw server en toepassingen houden.

In dit artikel vindt u informatie over de verschillende metrische gegevens die beschikbaar zijn voor de flexibele server die inzicht geven in het gedrag van uw server.

## <a name="available-metrics"></a>Beschikbare metrische gegevens

Azure Database for MySQL flexibele server biedt diverse metrische gegevens om inzicht te krijgen in de prestaties van uw werk belasting en op basis van deze informatie. u kunt de gevolgen van uw server en toepassing begrijpen. Zo kunt u in een flexibele server bijvoorbeeld het **CPU-percentage** van de host, **actieve verbindingen**, het **io-percentage** en het **host geheugen percentage** bewaken om te identificeren wanneer er sprake is van invloed op de prestaties. Van daaruit moet u mogelijk uw werk belasting optimaliseren, verticaal schalen door de reken lagen te wijzigen of horizon taal schalen met behulp van replica lezen.

Alle metrische gegevens van Azure hebben een frequentie van één minuut, en elke metriek bevat een geschiedenis van 30 dagen. U kunt waarschuwingen configureren voor de metrische gegevens. Zie voor stapsgewijze instructies voor [het instellen van waarschuwingen](./how-to-alert-on-metric.md). Andere taken zijn onder andere het instellen van geautomatiseerde acties, het uitvoeren van geavanceerde analyses en het archiveren van de geschiedenis. Zie het overzicht van Azure- [metrische](../../azure-monitor/data-platform.md)gegevens voor meer informatie.

### <a name="list-of-metrics"></a>Lijst met metrische gegevens
Deze metrische gegevens zijn beschikbaar voor Azure Database for MySQL:

|Weergave naam voor metrische gegevens|Metrisch|Eenheid|Description|
|---|---|---|---|
|CPU-percentage van host|cpu_percent|Percentage|Het percentage CPU-gebruik op de server, inclusief het CPU-gebruik van zowel de werk belasting van de klant als de Azure MySQL-processen|
|Host-netwerk in |network_bytes_ingress|Bytes|Binnenkomend netwerk verkeer op de server, inclusief verkeer van zowel de klanten database als Azure MySQL-functies, zoals replicatie, bewaking, logboeken, enzovoort.|
|Host Netwerk-uitgaand|network_bytes_egress|Bytes|Uitgaand netwerk verkeer op de server, inclusief verkeer van zowel de klanten database als Azure MySQL-functies, zoals replicatie, bewaking, logboeken enzovoort.|
|Replicatie vertraging|replication_lag|Seconden|De tijd sinds de laatste geplayte trans actie. Deze metriek is alleen beschikbaar voor replica servers.|
|Actieve verbindingen|active_connection|Count|Het aantal actieve verbindingen met de server.|
|Gebruikte back-upopslag|backup_storage_used|Bytes|De hoeveelheid back-upopslag die wordt gebruikt.|
|IO-percentage|io_consumption_percent|Percentage|Het percentage i/o dat in gebruik is.|
|Percentage van host geheugen|memory_percent|Percentage|Het percentage geheugen dat in gebruik is op de server, inclusief geheugen gebruik van zowel de werk belasting van de klant als de Azure MySQL-processen|
|Opslag limiet|storage_limit|Bytes|De maximale opslag voor deze server.|
|Opslag percentage|storage_percent|Percentage|Het percentage van de opslag ruimte dat uit het maximum van de server wordt gebruikt.|
|Gebruikte opslag|storage_used|Bytes|De hoeveelheid opslag ruimte die wordt gebruikt. De opslag die door de service wordt gebruikt, kan de database bestanden, transactie logboeken en de server logboeken bevatten.|
|Totaal aantal verbindingen|total_connections|Count|Het totale aantal verbindingen met de server|
|Afgebroken verbindingen|aborted_connections|Count|Het aantal mislukte pogingen om verbinding te maken met de MySQL, bijvoorbeeld een mislukte verbinding vanwege ongeldige referenties.|
|Query's|aanvragen|Count|Het aantal query's per seconde|

## <a name="next-steps"></a>Volgende stappen
- Zie [waarschuwingen instellen](./how-to-alert-on-metric.md) voor hulp bij het maken van een waarschuwing op metrische gegevens.
- Meer informatie over het [schalen van IOPS](./concepts/../concepts-compute-storage.md#iops) om de prestaties te verbeteren.