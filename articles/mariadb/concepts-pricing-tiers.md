---
title: Prijscategorieën - Azure Database for MariaDB
description: Meer informatie over de verschillende prijs categorieën voor Azure Database for MariaDB, waaronder reken generaties, opslag typen, opslag grootte, vCores, geheugen en bewaar perioden voor back-ups.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: b5b5a506b2f932d20a617634ace7ebf02093fbfa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664245"
---
# <a name="azure-database-for-mariadb-pricing-tiers"></a>Azure Database for MariaDB prijs Categorieën

U kunt een Azure Database for MariaDB-server maken in een van drie verschillende prijs Categorieën: basis, Algemeen en geoptimaliseerd voor geheugen. De prijs categorieën worden onderscheiden van de hoeveelheid Compute in vCores die kan worden ingericht, het geheugen per vCore en de opslag technologie die wordt gebruikt om de gegevens op te slaan. Alle resources worden ingericht op het niveau van de MariaDB-server. Een server kan een of meer data bases bevatten.

| Resource | **Basic** | **Algemeen doel** | **Geoptimaliseerd voor geheugen** |
|:---|:----------|:--------------------|:---------------------|
| Compute genereren | Gen 5 |Gen 5 | Gen 5 |
| vCores | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Geheugen per vCore | 2 GB | 5 GB | 10 GB |
| Opslag grootte | 5 GB tot 1 TB | 5 GB tot 4 TB | 5 GB tot 4 TB |
| Bewaar periode voor database back-ups | 7 tot 35 dagen | 7 tot 35 dagen | 7 tot 35 dagen |

Als u een prijs categorie wilt kiezen, gebruikt u de volgende tabel als uitgangs punt.

| Prijscategorie | Beoogde workloads |
|:-------------|:-----------------|
| Basic | Werk belastingen waarvoor lichte reken kracht en I/O-prestaties zijn vereist. Voor beelden zijn servers die worden gebruikt voor ontwikkeling of testen of kleinschalige, niet-veelgebruikte toepassingen. |
| Algemeen gebruik | De meeste zakelijke workloads die evenwichtige reken kracht en geheugen vereisen met schaal bare I/O-door voer. Voorbeelden zijn onder meer servers voor het hosten van web- en mobiele apps en andere zakelijke toepassingen.|
| Geoptimaliseerd geheugen | Data bases met hoogwaardige prestaties waarvoor de prestaties in het geheugen zijn vereist voor een snellere transactie verwerking en hogere gelijktijdigheid. Voorbeelden zijn onder meer servers voor het verwerken van realtime gegevens en transactionele of analytische toepassingen met hoge prestaties.|

Nadat u een server hebt gemaakt, kunt u het aantal vCores en de prijs categorie (met uitzonde ring van en van basis) binnen enkele seconden omhoog of omlaag wijzigen. U kunt ook onafhankelijk de hoeveelheid opslag ruimte en de Bewaar periode voor back-ups op afstand aanpassen zonder uitval tijd van de toepassing. U kunt het opslag type voor back-ups niet wijzigen nadat een server is gemaakt. Zie de sectie [resources schalen](#scale-resources) voor meer informatie.

## <a name="compute-generations-and-vcores"></a>Berekende generaties en vCores

Reken bronnen worden weer gegeven als vCores, die de logische CPU van de onderliggende hardware vertegenwoordigen. Logische Cpu's van de vijfde generatie zijn gebaseerd op Intel E5-2673 v4 (Broadwell) 2,3-GHz processors.

## <a name="storage"></a>Storage

De opslag ruimte die u inricht, is de hoeveelheid opslag capaciteit die beschikbaar is voor uw Azure Database for MariaDB-server. De opslag wordt gebruikt voor de database bestanden, tijdelijke bestanden, transactie logboeken en de MariaDB-server Logboeken. De totale hoeveelheid opslag ruimte die u hebt ingericht, definieert ook de I/O-capaciteit die beschikbaar is voor uw server.

| Opslag kenmerken   | Basic | Algemeen gebruik | Geoptimaliseerd geheugen |
|:---|:----------|:--------------------|:---------------------|
| Opslagtype | Basis opslag | Opslag Algemeen | Opslag Algemeen |
| Opslag grootte | 5 GB tot 1 TB | 5 GB tot 4 TB | 5 GB tot 4 TB |
| Grootte van toename van opslag | 1 GB | 1 GB | 1 GB |
| IOPS | Variabele |3 IOPS/GB<br/>Min. 100 IOPS<br/>Maxi maal 6000 IOPS | 3 IOPS/GB<br/>Min. 100 IOPS<br/>Maxi maal 6000 IOPS |

U kunt extra opslag capaciteit toevoegen tijdens en na het maken van de-server en het systeem toestaan om opslag automatisch te laten groeien op basis van het opslag verbruik van uw werk belasting.

>[!NOTE]
> Opslag kan alleen omhoog en niet omlaag worden geschaald.

De laag basis biedt geen IOPS-garantie. In de prijs Categorieën Algemeen en geoptimaliseerd voor geheugen, wordt de IOPS-schaal met de ingerichte opslag grootte in een verhouding van 3:1.

U kunt uw I/O-gebruik bewaken in de Azure Portal of met behulp van Azure CLI-opdrachten. De relevante metrische gegevens die moeten worden bewaakt [, zijn opslag limiet, opslag percentage, gebruikte opslag en i/o-percentage](concepts-monitoring.md).

### <a name="large-storage-preview"></a>Grote opslag (preview-versie)

We verg Roten de opslag limieten in onze lagen van Algemeen en geoptimaliseerd voor geheugen. Nieuw gemaakte servers die zich aanmelden voor de preview-versie, kunnen tot 16 TB aan opslag ruimte inrichten. De IOPS-schaal bij een verhouding van 3:1 tot 20.000 IOPS. Net zoals bij de huidige algemeen beschik bare opslag ruimte, kunt u extra opslag capaciteit toevoegen na het maken van de server en het systeem toestaan om opslag automatisch te laten groeien op basis van het opslag verbruik van uw werk belasting.

| Opslag kenmerken | Algemeen gebruik | Geoptimaliseerd geheugen |
|:-------------|:--------------------|:---------------------|
| Opslagtype | Azure Premium Storage | Azure Premium Storage |
| Opslag grootte | 32 GB tot 16 TB| 32 tot 16 TB |
| Grootte van toename van opslag | 1 GB | 1 GB |
| IOPS | 3 IOPS/GB<br/>Min. 100 IOPS<br/>Maxi maal 20.000 IOPS| 3 IOPS/GB<br/>Min. 100 IOPS<br/>Maxi maal 20.000 IOPS |

> [!IMPORTANT]
> Grote opslag ruimte bevindt zich momenteel in de open bare preview in de volgende regio's: VS-Oost, VS-Oost 2, Brazilië-zuid, VS-West, VS-Noord-Centraal, Zuid-Centraal VS, Europa-noord, Europa-west, UK-zuid, UK-west, Zuidoost-Azië, Azië-oost, Japan-Oost, Japan-West, Korea-centraal, Korea-zuid, Australië-oost, Australië-Zuid-Oost, Canada-oost en Canada-centraal.
>
> Alle andere regio's ondersteunen Maxi maal 4 TB opslag ruimte en Maxi maal 6000 IOPS.
>

### <a name="reaching-the-storage-limit"></a>De opslaglimiet wordt bereikt

Servers met precies 100 GB ingerichte opslag, of minder, worden gemarkeerd als alleen-lezen, wanneer de vrije opslag minder dan 5% van de ingerichte opslaggrootte beslaat. Servers met meer dan 100 GB ingerichte opslag worden als alleen-lezen gemarkeerd als de vrije opslag minder is dan 5 GB.

Als u bijvoorbeeld 110 GB aan opslag hebt ingericht en het werkelijke gebruik meer dan 105 GB overschrijdt, is de server gemarkeerd als alleen-lezen. Als u 5 GB aan opslag hebt ingericht, is de server gemarkeerd als alleen-lezen wanneer de vrije opslag minder dan 256 MB bedraagt.

Terwijl de service probeert om de server alleen-lezen te maken, worden alle nieuwe transactieaanvragen voor schrijven geblokkeerd en worden bestaande actieve transacties verder uitgevoerd. Indien de server op alleen-lezen is ingesteld, zullen alle daaropvolgende schrijfbewerkingen en transactiedoorvoeringen mislukken. Leesquery’s blijven gewoon werken. Pas nadat u de ingerichte opslagruimte hebt vergroot, is de server weer klaar om nieuwe schrijftransacties te accepteren.

U wordt aangeraden opslag ruimte automatisch te verg Roten of een waarschuwing in te stellen om u te waarschuwen wanneer uw server opslag de drempel waarde nadert, zodat u kunt voor komen dat u de status alleen-lezen krijgt. Zie de documentatie over het [instellen van een waarschuwing](howto-alert-metric.md)voor meer informatie.

### <a name="storage-auto-grow"></a>Opslag automatisch verg Roten

Met opslag automatisch verg Roten kan de opslag van uw server niet worden uitgevoerd en wordt deze alleen-lezen. Als automatische groei van opslag is ingeschakeld, wordt de opslag automatisch uitgebreid zonder dat dit van invloed is op de werk belasting. Voor servers met minder dan 100 GB ingerichte opslag, wordt de ingerichte opslag grootte verhoogd met 5 GB wanneer de vrije opslag minder is dan 10% van de ingerichte opslag ruimte. Voor servers met meer dan 100 GB ingerichte opslag wordt de ingerichte opslag grootte verhoogd met 5% wanneer de beschik bare opslag ruimte lager is dan 10 GB van de ingerichte opslag grootte. De maximale opslag limieten, zoals hierboven is beschreven, zijn van toepassing.

Als u bijvoorbeeld 1000 GB aan opslag hebt ingericht en het werkelijke gebruik meer dan 990 GB overschrijdt, wordt de opslag grootte van de server verhoogd naar 1050 GB. Als u 10 GB aan opslag ruimte hebt ingericht, wordt de opslag grootte verhoogd tot 15 GB wanneer minder dan 1 GB aan opslag ruimte vrij is.

Houd er rekening mee dat opslag alleen omhoog kan worden geschaald.

## <a name="backup"></a>Backup

Azure Database for MariaDB biedt Maxi maal 100% van uw ingerichte Server opslag als back-upopslag zonder extra kosten. Back-upopslag die u voor dit bedrag overschrijdt, wordt in GB per maand in rekening gebracht. Als u bijvoorbeeld een server met 250 GB opslag ruimte inricht, hebt u 250 GB extra opslag ruimte beschikbaar voor Server back-ups. Opslag voor back-ups die groter is dan 250 GB, wordt in rekening gebracht volgens het [prijs model](https://azure.microsoft.com/pricing/details/mariadb/). U kunt de [back-updocumentatie](concepts-backup.md)raadplegen voor meer informatie over factoren die van invloed zijn op het gebruik van back-ups, het bewaken en beheren van back-upopslag.

## <a name="scale-resources"></a>Resources schalen

Nadat u de server hebt gemaakt, kunt u de vCores, de prijs categorie (met uitzonde ring van en van basis), de hoeveelheid opslag en de Bewaar periode voor back-ups afzonderlijk wijzigen. U kunt het opslag type voor back-ups niet wijzigen nadat een server is gemaakt. Het aantal vCores kan omhoog of omlaag worden geschaald. De Bewaar periode van de back-up kan worden uitgebreid of omlaag van 7 tot 35 dagen. De opslag grootte kan alleen worden verhoogd. U kunt de resources verg Roten of verkleinen via de portal of Azure CLI. 

Wanneer u het aantal vCores of de prijs categorie wijzigt, wordt er een kopie van de oorspronkelijke server gemaakt met de nieuwe reken toewijzing. Wanneer de nieuwe server actief is, worden de verbindingen hiernaartoe overgeschakeld. Op het moment dat het systeem overschakelt naar de nieuwe server, kunnen er geen nieuwe verbindingen worden vastgelegd en worden alle niet-doorgevoerde transacties teruggedraaid. Dit tijdvenster is variabel, maar is in de meeste gevallen minder dan een minuut.

Het schalen van de opslag en het wijzigen van de Bewaar periode voor back-ups is waar online bewerkingen. Er is geen downtime en uw toepassing heeft geen last van dit probleem. Als IOPS wordt geschaald met de grootte van de ingerichte opslag, kunt u de IOPS die beschikbaar is voor uw server verhogen door opslag ruimte te schalen.

## <a name="pricing"></a>Prijzen

Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/mariadb/)voor services voor de meest actuele prijs informatie. Als u de kosten voor de gewenste configuratie wilt zien, geeft de [Azure Portal](https://portal.azure.com/#create/Microsoft.MariaDBServer) de maandelijkse kosten op het tabblad **prijs categorie** weer op basis van de opties die u selecteert. Als u geen Azure-abonnement hebt, kunt u de Azure-prijs calculator gebruiken om een geschatte prijs te krijgen. Selecteer op de website [Azure-prijs calculator](https://azure.microsoft.com/pricing/calculator/) de optie **items toevoegen**, vouw de categorie **data bases** uit en kies **Azure database for MariaDB** om de opties aan te passen.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over de beperkingen van de [service](concepts-limits.md).
- Meer informatie over [het maken van een MariaDB-server in de Azure Portal](quickstart-create-mariadb-server-database-using-azure-portal.md).