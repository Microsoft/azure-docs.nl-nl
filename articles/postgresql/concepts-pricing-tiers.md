---
title: Prijs categorieën-Azure Database for PostgreSQL-één server
description: In dit artikel worden de berekenings-en opslag opties in Azure Database for PostgreSQL-één server beschreven.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 119a68c4081cfc4fff6dc35b21badfac0397970b
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605107"
---
# <a name="pricing-tiers-in-azure-database-for-postgresql---single-server"></a>Prijscategorieën in Azure Database for PostgreSQL - enkele server

U kunt een Azure Database for PostgreSQL-server maken in een van drie verschillende prijs Categorieën: basis, Algemeen en geoptimaliseerd voor geheugen. De prijs categorieën worden onderscheiden van de hoeveelheid Compute in vCores die kan worden ingericht, het geheugen per vCore en de opslag technologie die wordt gebruikt om de gegevens op te slaan. Alle resources worden ingericht op het niveau van de PostgreSQL-server. Een server kan een of meer data bases bevatten.

| Resource/laag | **Basic** | **Algemeen doel** | **Geoptimaliseerd voor geheugen** |
|:---|:----------|:--------------------|:---------------------|
| Compute genereren | Gen 4, Gen 5 | Gen 4, Gen 5 | Gen 5 |
| vCores | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Geheugen per vCore | 2 GB | 5 GB | 10 GB |
| Opslag grootte | 5 GB tot 1 TB | 5 GB tot 16 TB | 5 GB tot 16 TB |
| Bewaar periode voor database back-ups | 7 tot 35 dagen | 7 tot 35 dagen | 7 tot 35 dagen |

Als u een prijs categorie wilt kiezen, gebruikt u de volgende tabel als uitgangs punt.

| Prijscategorie | Beoogde workloads |
|:-------------|:-----------------|
| Basic | Werk belastingen waarvoor lichte reken kracht en I/O-prestaties zijn vereist. Voor beelden zijn servers die worden gebruikt voor ontwikkeling of testen of kleinschalige, niet-veelgebruikte toepassingen. |
| Algemeen gebruik | De meeste zakelijke workloads die evenwichtige reken kracht en geheugen vereisen met schaal bare I/O-door voer. Voorbeelden zijn onder meer servers voor het hosten van web- en mobiele apps en andere zakelijke toepassingen.|
| Geoptimaliseerd geheugen | Data bases met hoogwaardige prestaties waarvoor de prestaties in het geheugen zijn vereist voor een snellere transactie verwerking en hogere gelijktijdigheid. Voorbeelden zijn onder meer servers voor het verwerken van realtime gegevens en transactionele of analytische toepassingen met hoge prestaties.|

Nadat u een server hebt gemaakt, kunt u binnen enkele seconden het aantal vCores, de generatie van de hardware en de prijs categorie (met uitzonde ring van en van basis) wijzigen. U kunt ook onafhankelijk de hoeveelheid opslag ruimte en de Bewaar periode voor back-ups op afstand aanpassen zonder uitval tijd van de toepassing. U kunt het opslag type voor back-ups niet wijzigen nadat een server is gemaakt. Zie de sectie [resources schalen](#scale-resources) voor meer informatie.

## <a name="compute-generations-and-vcores"></a>Berekende generaties en vCores

Reken bronnen worden weer gegeven als vCores, die de logische CPU van de onderliggende hardware vertegenwoordigen. China-oost 1, China-noord 1, US DoD-centraal en US DoD-oost logische Cpu's van generatie 4 gebruiken die zijn gebaseerd op Intel E5-2673 v3 (Haswell) 2,4-GHz processors. Alle andere regio's maken gebruik van generatie 5 logische Cpu's die zijn gebaseerd op Intel E5-2673 v4 (Broadwell) 2,3-GHz processors.

## <a name="storage"></a>Storage

De opslag ruimte die u inricht, is de hoeveelheid opslag capaciteit die beschikbaar is voor uw Azure Database for PostgreSQL-server. De opslag wordt gebruikt voor de database bestanden, tijdelijke bestanden, transactie logboeken en de PostgreSQL-server Logboeken. De totale hoeveelheid opslag ruimte die u hebt ingericht, definieert ook de I/O-capaciteit die beschikbaar is voor uw server.

| Opslag kenmerken | **Basic** | **Algemeen doel** | **Geoptimaliseerd voor geheugen** |
|:---|:----------|:--------------------|:---------------------|
| Opslagtype | Basis opslag | Opslag Algemeen | Opslag Algemeen |
| Opslag grootte | 5 GB tot 1 TB | 5 GB tot 16 TB | 5 GB tot 16 TB |
| Grootte van toename van opslag | 1 GB | 1 GB | 1 GB |
| IOPS | Variabele |3 IOPS/GB<br/>Min. 100 IOPS<br/>Maxi maal 20.000 IOPS | 3 IOPS/GB<br/>Min. 100 IOPS<br/>Maxi maal 20.000 IOPS |

> [!NOTE]
> Opslag tot 16TB en 20.000 IOPS wordt ondersteund in de volgende regio's: Australië-oost, Australië-Zuid-Oost, Brazilië-zuid, Canada-centraal, Canada-oost, VS-West, China-oost 2, China-noord 2, Azië-oost, VS-Oost, VS-Oost 1, VS-Oost 2, Japan-Oost, Japan-West, Korea-centraal, Korea-zuid, North-Azië, Europa-Noord, Zuid-Centraal VS, Zuidoost-Zuidoost , Zwitserland-west, US Gov Oost, US Gov SouthCentral, US Gov Zuidwest, UK-zuid, UK-west, Europa-west, VS-West-Centraal, West-VS en VS-West 2.
>
> Alle andere regio's ondersteunen Maxi maal 4 TB opslag ruimte en 6000 IOPS.
>

U kunt extra opslag capaciteit toevoegen tijdens en na het maken van de-server en het systeem toestaan om opslag automatisch te laten groeien op basis van het opslag verbruik van uw werk belasting.

>[!NOTE]
> Opslag kan alleen omhoog en niet omlaag worden geschaald.

De laag basis biedt geen IOPS-garantie. In de prijs Categorieën Algemeen en geoptimaliseerd voor geheugen, wordt de IOPS-schaal met de ingerichte opslag grootte in een verhouding van 3:1.

U kunt uw I/O-gebruik bewaken in de Azure Portal of met behulp van Azure CLI-opdrachten. De relevante metrische gegevens die moeten worden bewaakt [, zijn opslag limiet, opslag percentage, gebruikte opslag en i/o-percentage](concepts-monitoring.md).

### <a name="reaching-the-storage-limit"></a>De opslaglimiet wordt bereikt

Servers met minder dan 100 GB ingerichte opslag zijn gemarkeerd als alleen-lezen als de vrije opslag minder is dan 512 MB of 5% van de ingerichte opslag grootte. Servers met meer dan 100 GB ingerichte opslag worden als alleen-lezen gemarkeerd als de vrije opslag minder is dan 5 GB.

Als u bijvoorbeeld 110 GB aan opslag hebt ingericht en het werkelijke gebruik meer dan 105 GB overschrijdt, is de server gemarkeerd als alleen-lezen. Als u 5 GB aan opslag hebt ingericht, is de server gemarkeerd als alleen-lezen wanneer de vrije opslag minder dan 512 MB bedraagt.

Wanneer de server is ingesteld op alleen-lezen, worden alle bestaande sessies losgekoppeld en worden niet-doorgevoerde trans acties teruggedraaid. Eventuele volgende schrijf bewerkingen en trans acties worden niet doorgevoerd. Alle volgende Lees query's werken niet meer.  

U kunt de hoeveelheid ingerichte opslag naar uw server verg Roten of een nieuwe sessie starten in de modus lezen-schrijven en gegevens verwijderen om vrije opslag vrij te maken. Met uitvoeren `SET SESSION CHARACTERISTICS AS TRANSACTION READ WRITE;` wordt de huidige sessie ingesteld op de schrijf modus lezen. Om beschadiging van gegevens te voor komen, moet u geen schrijf bewerkingen uitvoeren wanneer de server nog steeds de status alleen-lezen heeft.

U wordt aangeraden opslag ruimte automatisch te verg Roten of een waarschuwing in te stellen om u te waarschuwen wanneer uw server opslag de drempel waarde nadert, zodat u kunt voor komen dat u de status alleen-lezen krijgt. Zie de documentatie over het [instellen van een waarschuwing](howto-alert-on-metric.md)voor meer informatie.

### <a name="storage-auto-grow"></a>Opslag automatisch verg Roten

Met opslag automatisch verg Roten kan de opslag van uw server niet worden uitgevoerd en wordt deze alleen-lezen. Als automatische groei van opslag is ingeschakeld, wordt de opslag automatisch uitgebreid zonder dat dit van invloed is op de werk belasting. Voor servers met minder dan 100 GB ingerichte opslag, wordt de ingerichte opslag grootte verhoogd met 5 GB zodra de gratis opslag onder het hoogste van 1 GB of 10% van de ingerichte opslag komt. Voor servers met meer dan 100 GB ingerichte opslag wordt de ingerichte opslag grootte verhoogd met 5% wanneer de beschik bare opslag ruimte lager is dan 10 GB of 5% van de ingerichte opslag grootte. De maximale opslag limieten, zoals hierboven is beschreven, zijn van toepassing.

Als u bijvoorbeeld 1000 GB aan opslag hebt ingericht en het werkelijke gebruik meer dan 950 GB overschrijdt, wordt de opslag grootte van de server verhoogd naar 1050 GB. Als u 10 GB aan opslag ruimte hebt ingericht, wordt de opslag grootte verhoogd tot 15 GB wanneer minder dan 1 GB aan opslag ruimte vrij is.

Houd er rekening mee dat opslag alleen omhoog kan worden geschaald.

## <a name="backup-storage"></a>Back-upopslag

Azure Database for PostgreSQL biedt Maxi maal 100% van uw ingerichte Server opslag als back-upopslag zonder extra kosten. Back-upopslag die u voor dit bedrag overschrijdt, wordt in GB per maand in rekening gebracht. Als u bijvoorbeeld een server met 250 GB opslag ruimte inricht, hebt u 250 GB extra opslag ruimte beschikbaar voor Server back-ups. Opslag voor back-ups die groter is dan 250 GB, wordt in rekening gebracht volgens het [prijs model](https://azure.microsoft.com/pricing/details/postgresql/). U kunt de [back-updocumentatie](concepts-backup.md)raadplegen voor meer informatie over factoren die van invloed zijn op het gebruik van back-ups, het bewaken en beheren van back-upopslag.

## <a name="scale-resources"></a>Resources schalen

Nadat u de server hebt gemaakt, kunt u de vCores, de generatie van de hardware, de prijs categorie (met uitzonde ring van en van basis), de hoeveelheid opslag en de Bewaar periode voor back-ups afzonderlijk wijzigen. U kunt het opslag type voor back-ups niet wijzigen nadat een server is gemaakt. Het aantal vCores kan omhoog of omlaag worden geschaald. De Bewaar periode van de back-up kan worden uitgebreid of omlaag van 7 tot 35 dagen. De opslag grootte kan alleen worden verhoogd. U kunt de resources verg Roten of verkleinen via de portal of Azure CLI. Zie [een Azure database for postgresql server bewaken en schalen met behulp van Azure cli](scripts/sample-scale-server-up-or-down.md)voor een voor beeld van schalen met behulp van Azure cli.

> [!NOTE]
> De opslag grootte kan alleen worden verhoogd. U kunt niet teruggaan naar een kleinere opslag grootte na de verhoging.

Wanneer u het aantal vCores, het genereren van de hardware of de prijs categorie wijzigt, wordt er een kopie van de oorspronkelijke server gemaakt met de nieuwe reken toewijzing. Wanneer de nieuwe server actief is, worden de verbindingen hiernaartoe overgeschakeld. Op het moment dat het systeem overschakelt naar de nieuwe server, kunnen er geen nieuwe verbindingen worden vastgelegd en worden alle niet-doorgevoerde transacties teruggedraaid. Dit tijdvenster is variabel, maar is in de meeste gevallen minder dan een minuut.

Het schalen van de opslag en het wijzigen van de Bewaar periode voor back-ups is waar online bewerkingen. Er is geen downtime en uw toepassing heeft geen last van dit probleem. Als IOPS wordt geschaald met de grootte van de ingerichte opslag, kunt u de IOPS die beschikbaar is voor uw server verhogen door opslag ruimte te schalen.

## <a name="pricing"></a>Prijzen

Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/PostgreSQL/)voor services voor de meest actuele prijs informatie. Als u de kosten voor de gewenste configuratie wilt zien, geeft de [Azure Portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) de maandelijkse kosten op het tabblad **prijs categorie** weer op basis van de opties die u selecteert. Als u geen Azure-abonnement hebt, kunt u de Azure-prijs calculator gebruiken om een geschatte prijs te krijgen. Selecteer op de website [Azure-prijs calculator](https://azure.microsoft.com/pricing/calculator/) de optie **items toevoegen**, vouw de categorie **data bases** uit en kies **Azure database for PostgreSQL** om de opties aan te passen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [maken van een postgresql-server in de portal](tutorial-design-database-using-azure-portal.md).
- Meer informatie over [service limieten](concepts-limits.md).
- Meer informatie over hoe u kunt [uitschalen met lees replica's](howto-read-replicas-portal.md).
