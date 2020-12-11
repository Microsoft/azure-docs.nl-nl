---
title: Hoge beschikbaarheid
titleSuffix: Azure SQL Database and SQL Managed Instance
description: Meer informatie over de mogelijkheden en functies voor hoge Beschik baarheid van de Azure SQL Database en SQL Managed instance-service
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: sashan
ms.author: sashan
ms.reviewer: sstein, sashan
ms.date: 10/28/2020
ms.openlocfilehash: 15067a046d8adc0ba38101bbe24cdc48cd433d56
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97095437"
---
# <a name="high-availability-for-azure-sql-database-and-sql-managed-instance"></a>Hoge Beschik baarheid voor Azure SQL Database en SQL Managed instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Het doel van de architectuur voor hoge Beschik baarheid in Azure SQL Database en SQL Managed instance is om te garanderen dat uw data base ten minste 99,99% van de tijd actief is (Zie voor meer informatie over specifieke SLA voor verschillende lagen de [Sla voor Azure SQL database en SQL Managed instance](https://azure.microsoft.com/support/legal/sla/sql-database/)) zonder dat u zich zorgen hoeft te maken over de invloed van onderhouds bewerkingen en storingen. Azure beheert automatisch essentiële onderhouds taken, zoals patches, back-ups, Windows-en Azure SQL-upgrades, en niet-geplande gebeurtenissen, zoals onderliggende hardware, software of netwerk fouten.  Wanneer de onderliggende data base in Azure SQL Database wordt gerepareerd of een failover wordt uitgevoerd, is de downtime niet merkbaar als u [probeert logica](develop-overview.md#resiliency) in uw app te gebruiken. SQL Database en SQL Managed instance kunnen snel worden hersteld, zelfs in de meest kritieke omstandigheden om ervoor te zorgen dat uw gegevens altijd beschikbaar zijn.

De oplossing voor hoge Beschik baarheid is zodanig ontworpen dat doorgevoerde gegevens nooit verloren gaan als gevolg van fouten, dat de onderhouds bewerkingen geen invloed hebben op uw werk belasting en dat de data base geen Single Point of Failure is in uw software architectuur. Er zijn geen onderhouds Vensters of downtime die vereisen dat u de werk belasting stopt wanneer de data base wordt bijgewerkt of onderhouden.

Er zijn twee architectuur modellen met hoge Beschik baarheid:

- **Standaard beschikbaarheids model** dat is gebaseerd op een schei ding van Compute en opslag.  Het is afhankelijk van de hoge Beschik baarheid en betrouw baarheid van de laag voor externe opslag. Deze architectuur streeft naar budget gerichte zakelijke toepassingen die tijdens onderhouds activiteiten een prestatie vermindering kunnen verdragen.
- **Premium-beschikbaarheids model** dat is gebaseerd op een cluster met data base-engine processen. Het is afhankelijk van het feit dat er altijd een quorum van de beschik bare data base-engine knooppunten is. Deze architectuur streeft naar essentiële bedrijfs toepassingen met hoge i/o-prestaties, een hoge transactie snelheid en garandeert minimale prestatie gevolgen voor uw werk belasting tijdens onderhouds activiteiten.

SQL Database en SQL Managed instance worden beide uitgevoerd op de nieuwste stabiele versie van de SQL Server-data base-engine en het Windows-besturings systeem, en de meeste gebruikers merken niet dat er voortdurend upgrades worden uitgevoerd.

## <a name="basic-standard-and-general-purpose-service-tier-locally-redundant-availability"></a>Basic, Standard en Algemeen servicelaag lokaal redundante Beschik baarheid

De service lagen Basic, Standard en Algemeen maken gebruik van de standaard beschik baarheids architectuur voor zowel serverloze als ingerichte reken kracht. In de volgende afbeelding ziet u vier verschillende knoop punten met de gescheiden reken-en opslag lagen.

![Schei ding van Compute en opslag](./media/high-availability-sla/general-purpose-service-tier.png)

Het standaard beschikbaarheids model bevat twee lagen:

- Een stateless Compute-laag die het `sqlservr.exe` proces uitvoert en bevat alleen tijdelijke en in de cache opgeslagen gegevens, zoals TempDB, model databases op de gekoppelde SSD en de plannings cache, de buffer groep en de column Store-groep in het geheugen. Dit stateless knoop punt wordt gebruikt door Azure Service Fabric dat initialiseert `sqlservr.exe` , de status van het knoop punt beheert en failover naar een ander knoop punt uitvoert, indien nodig.
- Een stateful gegevenslaag met de database bestanden (. MDF/. ldf) die zijn opgeslagen in Azure Blob Storage. Azure Blob-opslag heeft ingebouwde functie voor gegevens beschikbaarheid en redundantie. Hiermee wordt gegarandeerd dat elke record in het logboek bestand of de pagina in het gegevens bestand behouden blijft, zelfs als het `sqlservr.exe` proces vastloopt.

Wanneer de data base-engine of het besturings systeem wordt bijgewerkt, of als er een fout wordt gedetecteerd, wordt het stateless `sqlservr.exe` proces door Azure service Fabric verplaatst naar een ander stateless reken knooppunt met voldoende vrije capaciteit. Gegevens in Azure Blob-opslag worden niet beïnvloed door de verplaatsing en de gegevens/logboek bestanden zijn gekoppeld aan het zojuist geïnitialiseerde `sqlservr.exe` proces. Dit proces garandeert een Beschik baarheid van 99,99%, maar een zware werk belasting kan tijdens de overgang enige prestatie vermindering voordoen, omdat het nieuwe `sqlservr.exe` proces wordt gestart met koude cache.

## <a name="general-purpose-service-tier-zone-redundant-availability-preview"></a>Redundante Beschik baarheid Algemeen service tier-zone (preview-versie)

Zone redundante configuratie voor de servicelaag voor algemeen gebruik [Azure-beschikbaarheidszones](../../availability-zones/az-overview.md)   om data bases te repliceren op meerdere fysieke locaties binnen een Azure-regio.Door zone redundantie te selecteren, kunt u uw nieuwe en bestaande afzonderlijke data bases en elastische Pools voor algemeen gebruik tot een veel grotere set storingen leiden, met inbegrip van een onherstelbare uitval van het Data Center, zonder dat u de toepassings logica hoeft te wijzigen.

De redundante configuratie zone voor de laag voor algemeen gebruik heeft twee lagen:  

- Een stateful gegevenslaag met de database bestanden (. MDF/. ldf) die zijn opgeslagen in ZRS PFS (zone-redundante [opslag Premium-bestands share](../../storage/files/storage-how-to-create-premium-fileshare.md). Met [zone-redundante opslag](../../storage/common/storage-redundancy.md) worden de gegevens en logboek bestanden synchroon gekopieerd over drie fysiek geïsoleerde Azure-beschikbaarheids zones.
- Een stateless Compute-laag die het sqlservr.exe proces uitvoert en bevat alleen tijdelijke en in de cache opgeslagen gegevens, zoals TempDB, model databases op de gekoppelde SSD en de plannings cache, de buffer groep en de column Store-groep in het geheugen. Dit stateless knoop punt wordt gebruikt door Azure Service Fabric dat sqlservr.exe initialiseert, de status van het knoop punt beheert en een failover naar een ander knoop punt uitvoert, indien nodig. Voor zone-redundante algemene doel databases zijn knoop punten met reserve capaciteit direct beschikbaar in andere Beschikbaarheidszones voor failover.

De zone redundante versie van de architectuur met hoge Beschik baarheid voor de servicelaag voor algemeen gebruik wordt geïllustreerd in het volgende diagram:

![Zone redundante configuratie voor algemeen gebruik](./media/high-availability-sla/zone-redundant-for-general-purpose.png)

> [!IMPORTANT]
> Zie [Services ondersteunen per regio](../../availability-zones/az-region.md)voor actuele informatie over de regio's die zone redundante data bases ondersteunen. De zone-redundante configuratie is alleen beschikbaar wanneer de GEN5 Compute-hardware is geselecteerd. Deze functie is niet beschikbaar in het door SQL beheerde exemplaar.

> [!NOTE]
> Algemeen-data bases met een grootte van 80 VCore kan de prestaties afnemen met de zone redundante configuratie. Bewerkingen zoals back-ups maken, herstellen, kopiëren van data bases en instellen van geo-DR-relaties kunnen leiden tot tragere prestaties voor afzonderlijke data bases die groter zijn dan 1 TB. 

## <a name="premium-and-business-critical-service-tier-locally-redundant-availability"></a>Premium en Bedrijfskritiek servicelaag lokaal redundante Beschik baarheid

Premium-en Bedrijfskritiek-service lagen maken gebruik van het Premium-beschikbaarheids model, dat reken bronnen ( `sqlservr.exe` proces) en opslag (lokaal gekoppelde SSD) integreert op één knoop punt. Hoge Beschik baarheid wordt bereikt door zowel Compute als Storage te repliceren naar extra knoop punten die een drie-tot vier knoop punt-cluster maken.

![Cluster van data base-engine knooppunten](./media/high-availability-sla/business-critical-service-tier.png)

De onderliggende database bestanden (. MDF/. ldf) worden geplaatst op de gekoppelde SSD-opslag om een lage latentie-IO te bieden voor uw werk belasting. Hoge Beschik baarheid wordt geïmplementeerd met behulp van een technologie die vergelijkbaar is met SQL Server AlwaysOn- [beschikbaarheids groepen](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Het cluster bevat één primaire replica die toegankelijk is voor werk belastingen van de klant lezen en schrijven, en Maxi maal drie secundaire replica's (Compute en opslag) die kopieën van gegevens bevatten. Het primaire knoop punt duwt voortdurend wijzigingen in de secundaire knoop punten en zorgt ervoor dat de gegevens worden gesynchroniseerd naar ten minste één secundaire replica voordat elke trans actie wordt doorgevoerd. Dit proces zorgt ervoor dat als het primaire knoop punt om een of andere reden vastloopt, er altijd een volledig gesynchroniseerd knoop punt wordt uitgevoerd. De failover wordt gestart door de Azure-Service Fabric. Zodra de secundaire replica het nieuwe primaire knoop punt wordt, wordt er een andere secundaire replica gemaakt om ervoor te zorgen dat het cluster voldoende knoop punten (quorum set) heeft. Zodra de failover is voltooid, worden Azure SQL-verbindingen automatisch omgeleid naar het nieuwe primaire knoop punt.

Als extra voor deel heeft het Premium-beschikbaarheids model de mogelijkheid om alleen-lezen Azure SQL-verbindingen om te leiden naar een van de secundaire replica's. Deze functie heet [Uitschalen lezen](read-scale-out.md). Het biedt 100% extra reken capaciteit zonder extra kosten voor het laden van alleen-lezen bewerkingen, zoals analytische werk belastingen, van de primaire replica.

## <a name="premium-and-business-critical-service-tier-zone-redundant-availability"></a>Redundante Beschik baarheid voor Premium en Bedrijfskritiek-servicelaag 

Het cluster met knoop punten voor het Premium-beschikbaarheids model wordt standaard in hetzelfde Data Center gemaakt. Met de introductie van [Azure-beschikbaarheidszones](../../availability-zones/az-overview.md)kan SQL database verschillende replica's van de bedrijfskritiek-data base plaatsen in verschillende beschikbaarheids zones in dezelfde regio. Om een Single Point of Failure te elimineren, wordt de controle ring ook gedupliceerd over meerdere zones als drie gateway ringen (GW). De route ring naar een specifieke gateway ring wordt beheerd door [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) (ATM). Omdat de zone redundante configuratie in de service lagen van Premium of Bedrijfskritiek geen extra database redundantie maakt, kunt u deze zonder extra kosten inschakelen. Als u een zone redundante configuratie selecteert, kunt u uw Premium-of Bedrijfskritiek-data bases tot een veel grotere set storingen leiden, inclusief een onherstelbare uitval van het Data Center, zonder dat u de toepassings logica hoeft te wijzigen. U kunt ook bestaande Premium-of Bedrijfskritiek-data bases of-groepen converteren naar de zone redundante configuratie.

Omdat de redundante data bases van de zone replica's hebben in verschillende data centers met een aantal onderlinge afstanden, kan de verhoogde netwerk latentie de doorvoer tijd verhogen en de prestaties van bepaalde OLTP-workloads beïnvloeden. U kunt altijd terugkeren naar de configuratie met één zone door de instelling voor zone redundantie uit te scha kelen. Dit proces is een online bewerking die vergelijkbaar is met de normale upgrade van de servicelaag. Aan het einde van het proces wordt de data base of groep gemigreerd van een redundante ring zone naar een enkele zone ring of andersom.

> [!IMPORTANT]
> Wanneer u de laag Bedrijfskritiek gebruikt, is de redundante configuratie van de zone alleen beschikbaar wanneer de GEN5 Compute-hardware is geselecteerd. Zie [Services ondersteunen per regio](../../availability-zones/az-region.md)voor actuele informatie over de regio's die zone redundante data bases ondersteunen.

> [!NOTE]
> Deze functie is niet beschikbaar in het door SQL beheerde exemplaar.

De zone redundante versie van de architectuur met hoge Beschik baarheid wordt geïllustreerd in het volgende diagram:

![architectuur zone met hoge Beschik baarheid, redundant](./media/high-availability-sla/zone-redundant-business-critical-service-tier.png)


## <a name="hyperscale-service-tier-availability"></a>Beschik baarheid van grootschalige-servicelaag

De grootschalige-architectuur van de service tier wordt beschreven in de [architectuur gedistribueerde functies](./service-tier-hyperscale.md#distributed-functions-architecture) en is momenteel alleen beschikbaar voor SQL database, niet voor SQL beheerd exemplaar.

![Functionele architectuur grootschalige](./media/high-availability-sla/hyperscale-architecture.png)

Het beschikbaarheids model in grootschalige bevat vier lagen:

- Een stateless Compute-laag waarmee de `sqlservr.exe` processen worden uitgevoerd en die alleen tijdelijke en in de cache opgeslagen gegevens bevatten, zoals niet-bedekkende RBPEX-cache, tempdb, model database, enzovoort, op de gekoppelde SSD en de plannings cache, buffer groep en de column Store-groep in het geheugen. Deze stateless laag bevat de primaire Compute-replica en optioneel een aantal secundaire Compute-replica's die als failover-doelen kunnen fungeren.
- Een stateless opslagbus gevormd door pagina servers. Deze laag is de gedistribueerde opslag engine voor de `sqlservr.exe` processen die worden uitgevoerd op de reken replica's. Elke pagina Server bevat alleen tijdelijke en in de cache opgeslagen gegevens, zoals het bedekken van RBPEX cache op de gekoppelde SSD en gegevens pagina's in het cache geheugen. Elke pagina Server heeft een gekoppelde pagina server in een actief/actief-configuratie om taak verdeling, redundantie en hoge Beschik baarheid te bieden.
- Een stateful opslag laag voor transactie logboeken, gevormd door het reken knooppunt waarop het logboek service proces wordt uitgevoerd, de overloop zone voor het transactie logboek en de lange termijn opslag van transactie Logboeken. Voor de opslag zone en de lange termijn wordt Azure Storage gebruikt. Dit biedt Beschik baarheid en [Redundantie](../../storage/common/storage-redundancy.md) voor het transactie logboek en zorgt voor de duurzaamheid van gegevens voor doorgevoerde trans acties.
- Een stateful gegevensopslag laag met de database bestanden (. MDF/. ndf) die zijn opgeslagen in Azure Storage en worden bijgewerkt door pagina servers. Deze laag maakt gebruik van de functies voor Beschik baarheid en [Redundantie](../../storage/common/storage-redundancy.md) van gegevens van Azure Storage. Hiermee wordt gegarandeerd dat elke pagina in een gegevens bestand blijft behouden, zelfs als processen in andere lagen van de grootschalige-architectuur vastlopen of als reken knooppunten mislukken.

Reken knooppunten in alle grootschalige-lagen worden uitgevoerd op Azure Service Fabric, waarmee de status van elk knoop punt wordt gecontroleerd en waar nodig failovers worden uitgevoerd naar beschik bare, gezonde knoop punten.

Zie voor meer informatie over hoge Beschik baarheid in grootschalige [Data Base hoge Beschik baarheid in grootschalige](./service-tier-hyperscale.md#database-high-availability-in-hyperscale).


## <a name="accelerated-database-recovery-adr"></a>Versneld database herstel (ADR)

[Versneld database herstel (ADR)](../accelerated-database-recovery.md) is een nieuwe functie van een data base-engine waarmee de beschik baarheid van de data base aanzienlijk wordt verbeterd, met name in de aanwezigheid van langlopende trans acties. ADR is momenteel beschikbaar voor Azure SQL Database, Azure SQL Managed instance en Azure Synapse Analytics.

## <a name="testing-application-fault-resiliency"></a>Toepassings fout tolerantie testen

Hoge beschikbaarheid is een fundamenteel onderdeel van het SQL Database- en SQL Managed Instance-platform dat transparant werkt voor uw databasetoepassing. We erkennen echter dat u wellicht wilt testen hoe de automatische failoverbewerkingen die worden geïnitieerd tijdens geplande of niet-geplande gebeurtenissen van invloed zijn op een toepassing voordat u deze implementeert voor productie. U kunt een failover hand matig activeren door een speciale API aan te roepen om een Data Base, een elastische pool of een beheerd exemplaar opnieuw te starten. In het geval van een zone redundante data base of elastische pool zou de API-aanroep ertoe leiden dat client verbindingen worden omgeleid naar de nieuwe primaire in een beschikbaarheids zone die afwijkt van de beschikbaarheids zone van de oude primaire. Naast het testen van de manier waarop failover van invloed is op bestaande database sessies, kunt u ook controleren of de end-to-end-prestaties worden gewijzigd vanwege wijzigingen in de netwerk latentie. Omdat de computer opnieuw moet worden opgestart, is er slechts één failover-aanroep elke 15 minuten toegestaan voor elke Data Base, een elastische pool of een beheerd exemplaar.

Een failover kan worden gestart met behulp van Power shell, REST API of Azure CLI:

|Implementatie type|PowerShell|REST-API| Azure CLI|
|:---|:---|:---|:---|
|Database|[Invoke-AzSqlDatabaseFailover](/powershell/module/az.sql/invoke-azsqldatabasefailover)|[Data base-failover](/rest/api/sql/databases/failover)|[AZ rest](/cli/azure/reference-index#az-rest) kan worden gebruikt voor het aanroepen van een rest API aanroep vanuit Azure cli|
|Elastische pool|[Invoke-AzSqlElasticPoolFailover](/powershell/module/az.sql/invoke-azsqlelasticpoolfailover)|[Failover van elastische groep](/rest/api/sql/elasticpools(failover)/failover/)|[AZ rest](/cli/azure/reference-index#az-rest) kan worden gebruikt voor het aanroepen van een rest API aanroep vanuit Azure cli|
|Beheerd exemplaar|[Invoke-AzSqlInstanceFailover](/powershell/module/az.sql/Invoke-AzSqlInstanceFailover/)|[Beheerde instanties-failover](/rest/api/sql/managed%20instances%20-%20failover/failover)|[AZ SQL mi failover](/cli/azure/sql/mi/#az-sql-mi-failover)|

> [!IMPORTANT]
> De opdracht failover is niet beschikbaar voor lees bare secundaire replica's van grootschalige-data bases.

## <a name="conclusion"></a>Conclusie

Azure SQL Database en Azure SQL Managed instance bieden een ingebouwde oplossing voor hoge Beschik baarheid, die nauw is geïntegreerd met het Azure-platform. Het is afhankelijk van Service Fabric voor fout detectie en herstel, in Azure Blob-opslag voor gegevens beveiliging en op Beschikbaarheidszones voor een hogere fout tolerantie (zoals eerder in het document niet van toepassing op Azure SQL Managed instance). SQL Database en SQL Managed instance maken bovendien gebruik van de technologie van de AlwaysOn-beschikbaarheids groep van het SQL Server-exemplaar voor replicatie en failover. De combi natie van deze technologieën stelt toepassingen in staat om de voor delen van een gemengd opslag model volledig te benutten en de meest veeleisende Sla's te ondersteunen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure-beschikbaarheidszones](../../availability-zones/az-overview.md)
- Meer informatie over [service Fabric](../../service-fabric/service-fabric-overview.md)
- Meer informatie over [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md)
- Meer informatie over [het initiëren van een hand matige failover op een SQL-beheerd exemplaar](../managed-instance/user-initiated-failover.md)
- Zie [bedrijfs continuïteit](business-continuity-high-availability-disaster-recover-hadr-overview.md) voor meer opties voor hoge Beschik baarheid en herstel na nood gevallen.