---
title: Aanbevolen procedures bewaken-Azure Database for MySQL
description: In dit artikel worden de aanbevolen procedures beschreven voor het bewaken van uw Azure Database for MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: 59301e26f4d42056322ba5a7cdaff1555c531096
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96354987"
---
# <a name="best-practices-for-monitoring-azure-database-for-mysql--single-server"></a>Aanbevolen procedures voor het bewaken van Azure Database for MySQL-één server

Meer informatie over de aanbevolen procedures die kunnen worden gebruikt voor het bewaken van uw database bewerkingen en om ervoor te zorgen dat de prestaties niet worden aangetast naarmate de omvang van de gegevens toeneemt. Wanneer we nieuwe mogelijkheden toevoegen aan het platform, zullen we verder gaan met de best practices die in deze sectie worden beschreven.

## <a name="layout-of-the-current-monitoring-toolkit"></a>Indeling van de huidige bewakings Toolkit

Azure Database for MySQL voorziet in hulpprogram ma's en methoden die u kunt gebruiken om het gebruik van resources (zoals CPU, geheugen of I/O) te controleren of te verwijderen, om mogelijke problemen op te lossen en de prestaties van een Data Base te verbeteren. U kunt [metrische prestatie gegevens regel matig bewaken](concepts-monitoring.md#metrics) om het gemiddelde, het maximum en de minimum waarde voor een groot aantal Peri odes te bekijken.

U kunt [waarschuwingen instellen](howto-alert-on-metric.md#create-an-alert-rule-on-a-metric-from-the-azure-portal) voor een metrische drempel waarde, zodat u op de hoogte wordt gebracht als de server deze limieten heeft bereikt en de juiste acties onderneemt.  

Controleer de database server om ervoor te zorgen dat de resources die aan de Data Base zijn toegewezen, de werk belasting van de toepassing kunnen afhandelen. Als de data base bronnen limieten heeft, overweeg dan het volgende:
    * Het identificeren en optimaliseren van de belangrijkste query's die van resources worden verbruikt. 
    * Meer resources toevoegen door de servicelaag bij te werken.

### <a name="cpu-utilization"></a>CPU-gebruik
Bewaak het CPU-gebruik en als de data base CPU-bronnen uitgeput. Als het CPU-gebruik 90% of meer is dan moet u de compute schalen door het aantal vCores te verhogen of te schalen naar de volgende prijs categorie.  Zorg ervoor dat de door Voer of gelijktijdigheid naar verwachting is, terwijl u de CPU omhoog/omlaag schaalt. 

### <a name="memory"></a>Geheugen 
De hoeveelheid beschikbaar geheugen voor de database server is evenredig met het [aantal vCores](concepts-pricing-tiers.md). Zorg ervoor dat het geheugen voldoende is voor de werk belasting. Laad test uw toepassing om te controleren of het geheugen voldoende is voor lees-en schrijf bewerkingen. Als het verbruik van het database geheugen regel matig groter wordt dan een gedefinieerde drempel waarde, geeft dit aan dat u uw exemplaar moet bijwerken door de vCores of een hogere prestatie niveau te verhogen. Gebruik [query Store](concepts-query-store.md), [aanbevelingen voor query prestaties](concepts-performance-recommendations.md) om query's te identificeren met de langste duur, die het meest wordt uitgevoerd. Verken mogelijkheden om te optimaliseren. 

### <a name="storage"></a>Storage 
De [hoeveelheid opslag ruimte](howto-create-manage-server-portal.md#scale-compute-and-storage) die voor de mysql-server is ingericht, bepaalt de IOPs voor uw server. De opslag die door de service wordt gebruikt, omvat de database bestanden, transactie logboeken, de server logboeken en back-upmomentopnamen. Zorg ervoor dat de verbruikte schijf ruimte niet voortdurend hoger is dan 85 procent van de totale ingerichte schijf ruimte. Als dat het geval is, moet u de gegevens van de database server verwijderen of archiveren om ruimte vrij te maken. 

### <a name="network-traffic"></a>Netwerkverkeer 

**Ontvangst door Voer van netwerk, door Voer van verzen ding** van netwerk: de snelheid van het netwerk verkeer van en naar het MySQL-exemplaar in mega bytes per seconde. U moet de doorvoer vereiste voor de server evalueren en het verkeer continu controleren als de door Voer lager is dan verwacht. 

### <a name="database-connections"></a>Database verbindingen 
**Database verbindingen** : het aantal client sessies dat is verbonden met de Azure database for MySQL moet zijn afgestemd op de [verbindings limieten voor de geselecteerde SKU](concepts-server-parameters.md#max_connections) -grootte. 


## <a name="next-steps"></a>Volgende stappen

- [Aanbevolen procedures voor de prestaties van Azure Database for MySQL](concept-performance-best-practices.md)
- [Aanbevolen procedure voor Server bewerkingen met Azure Database for MySQL](concept-operation-excellence-best-practices.md)
