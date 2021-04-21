---
title: Hoge beschikbaarheid
titleSuffix: Azure SQL Database and SQL Managed Instance
description: Meer informatie over de mogelijkheden Azure SQL Database en SQL Managed Instance service voor hoge beschikbaarheid en functies
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: emlisa
ms.author: emlisa
ms.reviewer: sstein, emlisa
ms.date: 10/28/2020
ms.openlocfilehash: 21ac73b461ebcb171f48621aa27a16dfc0e8c936
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781699"
---
# <a name="high-availability-for-azure-sql-database-and-sql-managed-instance"></a>Hoge beschikbaarheid voor Azure SQL Database en SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Het doel van de architectuur voor hoge beschikbaarheid in Azure SQL Database en SQL Managed Instance is om te garanderen dat uw database minimaal 99,99% van de tijd actief is (raadpleeg SLA voor Azure SQL Database en [SQL Managed Instance)](https://azure.microsoft.com/support/legal/sla/sql-database/)voor meer informatie over de impact van onderhoudsbewerkingen en uitval. Azure verwerkt automatisch kritieke onderhoudstaken, zoals patching, back-ups, Windows- en Azure SQL-upgrades, evenals niet-geplande gebeurtenissen, zoals onderliggende hardware-, software- of netwerkfouten.  Wanneer de onderliggende database in Azure SQL Database wordt gepatcht of een uitval is mislukt, is de downtime niet zichtbaar als u logica voor opnieuw proberen [in](develop-overview.md#resiliency) uw app gebruikt. SQL Database en SQL Managed Instance kunnen zelfs in de meest kritieke omstandigheden snel worden hersteld, zodat uw gegevens altijd beschikbaar zijn.

De oplossing voor hoge beschikbaarheid is ontworpen om ervoor te zorgen dat vastgelegde gegevens nooit verloren gaan als gevolg van storingen, dat onderhoudsbewerkingen geen invloed hebben op uw workload en dat de database geen single point of failure in uw softwarearchitectuur is. Er zijn geen onderhoudsvensters of downtime waarvoor u de workload moet stoppen terwijl de database wordt bijgewerkt of onderhouden.

Er zijn twee architectuurmodellen met hoge beschikbaarheid:

- **Standaardbeschikbaarheidsmodel** dat is gebaseerd op een scheiding van rekenkracht en opslag.  Het is afhankelijk van hoge beschikbaarheid en betrouwbaarheid van de externe opslaglaag. Deze architectuur is gericht op budgetgerichte zakelijke toepassingen die een prestatievermindering tijdens onderhoudsactiviteiten kunnen tolereren.
- **Premium-beschikbaarheidsmodel** dat is gebaseerd op een cluster van database-engineprocessen. Het is afhankelijk van het feit dat er altijd een quorum van beschikbare database-engineknooppunten is. Deze architectuur is gericht op bedrijfskritieke toepassingen met hoge I/O-prestaties, hoge transactiesnelheid en garandeert minimale invloed op de prestaties van uw workload tijdens onderhoudsactiviteiten.

SQL Database en SQL Managed Instance beide uitgevoerd op de nieuwste stabiele versie van de SQL Server-database-engine en het Windows-besturingssysteem, en de meeste gebruikers merken niet dat upgrades continu worden uitgevoerd.

## <a name="basic-standard-and-general-purpose-service-tier-locally-redundant-availability"></a>Lokaal redundante beschikbaarheid in de servicelaag Basic, Standard en Algemeen servicelaag

De servicelagen Basic, Standard en Algemeen maken gebruik van de standaardbeschikbaarheidsarchitectuur voor zowel serverloze als inrichtende rekenkracht. In de volgende afbeelding ziet u vier verschillende knooppunten met de gescheiden reken- en opslaglagen.

![Scheiding van rekenkracht en opslag](./media/high-availability-sla/general-purpose-service-tier.png)

Het standaardbeschikbaarheidsmodel bevat twee lagen:

- Een staatloze rekenlaag die het proces wordt uitgevoerd en alleen tijdelijke gegevens bevat die in de cache zijn opgeslagen, zoals TempDB, modeldatabases op de gekoppelde SSD, en plan `sqlservr.exe` cache, bufferpool en columnstore-pool in het geheugen. Dit staatloze knooppunt wordt beheerd door Azure Service Fabric dat de status van het knooppunt initialiseert en indien nodig een failover naar een `sqlservr.exe` ander knooppunt uitvoert.
- Een stateful gegevenslaag met de databasebestanden (.mdf/.ldf) die zijn opgeslagen in Azure Blob Storage. Azure Blob Storage heeft een ingebouwde functie voor gegevensbeschikbaarheid en redundantie. Het garandeert dat elke record in het logboekbestand of de pagina in het gegevensbestand behouden blijft, zelfs als `sqlservr.exe` het proces vast loopt.

Wanneer de database-engine of het besturingssysteem wordt bijgewerkt of er een fout wordt gedetecteerd, verplaatst Azure Service Fabric het staatloze proces naar een ander staatloos reken knooppunt met voldoende vrije `sqlservr.exe` capaciteit. Gegevens in Azure Blob Storage worden niet beïnvloed door de verplaatsen en de gegevens-/logboekbestanden worden gekoppeld aan het zojuist initialiseerde `sqlservr.exe` proces. Dit proces garandeert een beschikbaarheid van 99,99%, maar een zware werkbelasting kan tijdens de overgang prestatievermindering ervaren omdat het nieuwe `sqlservr.exe` proces begint met koude cache.

## <a name="general-purpose-service-tier-zone-redundant-availability-preview"></a>Algemeen zone-redundante beschikbaarheid van de servicelaag (preview)

Zone-redundante configuratie voor de servicelaag algemeen gebruik wordt aangeboden voor zowel serverloze als inrichten rekenkracht. Deze configuratie maakt gebruik van [Azure-beschikbaarheidszones](../../availability-zones/az-overview.md)   databases te repliceren op meerdere fysieke locaties binnen een Azure-regio.Door zone-redundantie te selecteren, kunt u uw nieuwe en bestaande serverloze databases en inrichtende individuele databases voor algemeen gebruik en elastische pools bestand maken tegen een veel grotere set fouten, waaronder onherstelbare datacenterstoringen, zonder dat de toepassingslogica wordt gewijzigd.

Zone-redundante configuratie voor de laag Algemeen gebruik heeft twee lagen:  

- Een stateful gegevenslaag met de databasebestanden (.mdf/.ldf) die zijn opgeslagen in ZRS (zone-redundante opslag). Met [ZRS](../../storage/common/storage-redundancy.md) worden de gegevens en logboekbestanden synchroon gekopieerd naar drie fysiek geïsoleerde Azure-beschikbaarheidszones.
- Een staatloze rekenlaag die het sqlservr.exe-proces wordt uitgevoerd en alleen tijdelijke en in de cache opgeslagen gegevens bevat, zoals TempDB, modeldatabases op de gekoppelde SSD, en plan cache, bufferpool en columnstore-pool in het geheugen. Dit staatloze knooppunt wordt beheerd door Azure Service Fabric dat sqlservr.exe initialiseert, de status van het knooppunt beheert en indien nodig een failover naar een ander knooppunt uitvoert. Voor zone-redundante serverloze en inrichtende databases voor algemeen gebruik zijn knooppunten met reservecapaciteit direct beschikbaar in andere Beschikbaarheidszones voor failover.

De zone-redundante versie van de architectuur voor hoge beschikbaarheid voor de servicelaag voor algemeen gebruik wordt geïllustreerd door het volgende diagram:

![Zone-redundante configuratie voor algemeen gebruik](./media/high-availability-sla/zone-redundant-for-general-purpose.png)

> [!IMPORTANT]
> Zone-redundante configuratie is alleen beschikbaar wanneer de Gen5-rekenhardware is geselecteerd. Deze functie is niet beschikbaar in SQL Managed Instance. Zone-redundante configuratie voor serverloze en inrichtende laag voor algemeen gebruik is alleen beschikbaar in de volgende regio's: VS - oost, VS - oost 2, VS - west 2, Europa - noord, Europa - west, Azië - zuidoost, Australië - oost, Japan - oost, VK - zuid en Frankrijk - centraal.

> [!NOTE]
> Algemeen databases met een grootte van 80 vcores kunnen prestatievermindering ervaren met zone-redundante configuratie. Daarnaast kunnen bewerkingen zoals back-up, herstel, databasekopieren, het instellen van geo-DR-relaties en het downgraden van een zone-redundante database van Bedrijfskritiek naar Algemeen tragere prestaties ervaren voor individuele databases die groter zijn dan 1 TB. Raadpleeg onze [latentiedocumentatie over het schalen van een database](single-database-scale.md) voor meer informatie.
> 
> [!NOTE]
> De preview-versie valt niet onder Gereserveerde instantie

## <a name="premium-and-business-critical-service-tier-locally-redundant-availability"></a>Lokaal redundante beschikbaarheid Bedrijfskritiek Premium- en Bedrijfskritiek-servicelaag

Premium- en Bedrijfskritiek-servicelagen maken gebruik van het Premium-beschikbaarheidsmodel, waarmee rekenbronnen (proces) en opslag (lokaal gekoppelde SSD) op één knooppunt `sqlservr.exe` worden geïntegreerd. Hoge beschikbaarheid wordt bereikt door zowel reken- als opslagknooppunten te repliceren naar extra knooppunten, waardoor een cluster met drie tot vier knooppunten wordt gemaakt.

![Cluster van database-engine-knooppunten](./media/high-availability-sla/business-critical-service-tier.png)

De onderliggende databasebestanden (.mdf/.ldf) worden op de gekoppelde SSD-opslag geplaatst om uw workload een zeer lage latentie I/O te bieden. Hoge beschikbaarheid wordt geïmplementeerd met behulp van een technologie die vergelijkbaar is met SQL Server [Always On-beschikbaarheidsgroepen.](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) Het cluster bevat één primaire replica die toegankelijk is voor lees-/schrijfworkloads van klanten en maximaal drie secundaire replica's (berekening en opslag) met kopieën van gegevens. Het primaire knooppunt pusht wijzigingen voortdurend op volgorde naar de secundaire knooppunten en zorgt ervoor dat de gegevens worden gesynchroniseerd naar ten minste één secundaire replica voordat elke transactie wordt doorgevoerd. Dit proces garandeert dat als het primaire knooppunt om een of andere reden vast loopt, er altijd een volledig gesynchroniseerd knooppunt is om een fail over te nemen. De failover wordt geïnitieerd door de Azure Service Fabric. Zodra de secundaire replica het nieuwe primaire knooppunt wordt, wordt er een andere secundaire replica gemaakt om ervoor te zorgen dat het cluster voldoende knooppunten heeft (quorumset). Zodra de failover is voltooid, Azure SQL verbindingen automatisch omgeleid naar het nieuwe primaire knooppunt.

Als extra voordeel biedt het Premium-beschikbaarheidsmodel de mogelijkheid om alleen-lezen Azure SQL om te leiden naar een van de secundaire replica's. Deze functie heet [Uitschalen lezen.](read-scale-out.md) Het biedt 100% extra rekencapaciteit zonder extra kosten voor alleen-lezenbewerkingen zonder belasting, zoals analytische workloads, vanaf de primaire replica.

## <a name="premium-and-business-critical-service-tier-zone-redundant-availability"></a>Redundante beschikbaarheid Bedrijfskritiek premium- en Bedrijfskritiek-servicelaag 

Standaard wordt het cluster met knooppunten voor het Premium-beschikbaarheidsmodel gemaakt in hetzelfde datacenter. Met de introductie [van Azure-beschikbaarheidszones](../../availability-zones/az-overview.md)kunnen SQL Database verschillende replica's van de Bedrijfskritiek-database in verschillende beschikbaarheidszones in dezelfde regio plaatsen. Om één storingspunt te elimineren, wordt de besturingsring ook gedupliceerd in meerdere zones als drie gatewayringen (GW). De routering naar een specifieke gatewayring wordt beheerd door [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) (ATM). Omdat de zone-redundante configuratie in de Premium- of Bedrijfskritiek-servicelagen geen extra database-redundantie maakt, kunt u deze zonder extra kosten inschakelen. Door een zone-redundante configuratie te selecteren, kunt u uw Premium- of Bedrijfskritiek-databases bestand maken tegen een veel grotere set fouten, waaronder onherstelbare datacenterstoringen, zonder wijzigingen in de toepassingslogica. U kunt ook alle bestaande Premium- of Bedrijfskritiek databases of pools converteren naar de zone-redundante configuratie.

Omdat de zone-redundante databases replica's hebben in verschillende datacenters met enige afstand ertussen, kan de toegenomen netwerklatentie de doorvoeringstijd verhogen en zo de prestaties van sommige OLTP-workloads beïnvloeden. U kunt altijd terugkeren naar de configuratie met één zone door de instelling voor zone-redundantie uit te stellen. Dit proces is een onlinebewerking die vergelijkbaar is met de reguliere upgrade van de servicelaag. Aan het einde van het proces wordt de database of pool gemigreerd van een zone-redundante ring naar een ring met één zone of vice versa.

> [!IMPORTANT]
> Wanneer u de Bedrijfskritiek laag gebruikt, is zone-redundante configuratie alleen beschikbaar wanneer de Gen5-rekenhardware is geselecteerd. Zie Services-ondersteuning per regio voor actuele informatie [](../../availability-zones/az-region.md)over de regio's die ondersteuning bieden voor zone-redundante databases.

> [!NOTE]
> Deze functie is niet beschikbaar in SQL Managed Instance.

De zone-redundante versie van de architectuur voor hoge beschikbaarheid wordt geïllustreerd door het volgende diagram:

![zone-redundante architectuur met hoge beschikbaarheid](./media/high-availability-sla/zone-redundant-business-critical-service-tier.png)


## <a name="hyperscale-service-tier-availability"></a>Beschikbaarheid van de Hyperscale-servicelaag

De architectuur van de Hyperscale-servicelaag wordt beschreven [in](./service-tier-hyperscale.md#distributed-functions-architecture) Gedistribueerde functiearchitectuur en is momenteel alleen beschikbaar voor SQL Database, niet SQL Managed Instance.

![Hyperscale-functionele architectuur](./media/high-availability-sla/hyperscale-architecture.png)

Het beschikbaarheidsmodel in Hyperscale bevat vier lagen:

- Een staatloze rekenlaag die de processen wordt uitgevoerd en alleen tijdelijke en in de cache opgeslagen gegevens bevat, zoals `sqlservr.exe` niet-behandelende RBPEX-cache, TempDB, modeldatabase, enzovoort op de gekoppelde SSD, en plan cache, buffergroep en columnstore-pool in het geheugen. Deze staatloze laag bevat de primaire rekenreplica en eventueel een aantal secundaire rekenreplica's die als failoverdoelen kunnen fungeren.
- Een staatloze opslaglaag die wordt gevormd door paginaservers. Deze laag is de gedistribueerde opslagen engine voor `sqlservr.exe` de processen die worden uitgevoerd op de rekenreplica's. Elke paginaserver bevat alleen tijdelijke gegevens en gegevens in de cache, zoals RBPEX-cache op de gekoppelde SSD en gegevenspagina's die in het cachegeheugen zijn opgeslagen. Elke paginaserver heeft een gekoppelde paginaserver in een actief-actief-configuratie om taakverdeling, redundantie en hoge beschikbaarheid te bieden.
- Een stateful opslaglaag voor transactielogboek, gevormd door het reken knooppunt dat het Logboekserviceproces, de landingszone voor transactielogboek en langetermijnopslag voor transactielogboeken uitvoeren. Gebruik van landingszone en langetermijnopslag Azure Storage, [](../../storage/common/storage-redundancy.md) die beschikbaarheid en redundantie biedt voor transactielogboek, waardoor duurzaamheid van gegevens voor vastgelegde transacties wordt gewaarborgd.
- Een stateful gegevensopslaglaag met de databasebestanden (.mdf/.ndf) die zijn opgeslagen in Azure Storage en worden bijgewerkt door paginaservers. Deze laag maakt gebruik van gegevensbeschikbaarheid [en redundantiefuncties](../../storage/common/storage-redundancy.md) van Azure Storage. Het garandeert dat elke pagina in een gegevensbestand behouden blijft, zelfs als processen in andere lagen van de Hyperscale-architectuur vastvallen of als rekenknooppunten mislukken.

Rekenknooppunten in alle Hyperscale-lagen worden uitgevoerd op Azure Service Fabric, waarmee de status van elk knooppunt wordt gecontroleerd en waar nodig failovers worden uitgevoerd naar beschikbare knooppunten met een goede status.

Zie Hoge beschikbaarheid van databases in Hyperscale voor meer informatie over hoge [beschikbaarheid in Hyperscale.](./service-tier-hyperscale.md#database-high-availability-in-hyperscale)


## <a name="accelerated-database-recovery-adr"></a>Accelerated Database Recovery (ADR)

[Accelerated Database Recovery (ADR)](../accelerated-database-recovery.md) is een nieuwe database-enginefunctie die de beschikbaarheid van databases sterk verbetert, met name bij langdurige transacties. ADR is momenteel beschikbaar voor Azure SQL Database, Azure SQL Managed Instance en Azure Synapse Analytics.

## <a name="testing-application-fault-resiliency"></a>Tolerantie van toepassingsfouten testen

Hoge beschikbaarheid is een fundamenteel onderdeel van het SQL Database- en SQL Managed Instance-platform dat transparant werkt voor uw databasetoepassing. We erkennen echter dat u wellicht wilt testen hoe de automatische failoverbewerkingen die worden geïnitieerd tijdens geplande of niet-geplande gebeurtenissen van invloed zijn op een toepassing voordat u deze implementeert voor productie. U kunt een failover handmatig activeren door een speciale API aan te roepen om een database, een elastische pool of een beheerd exemplaar opnieuw op te starten. In het geval van een zone-redundante serverloze of inrichtende Algemeen-database of elastische pool, leidt de API-aanroep tot het omleiden van clientverbindingen naar de nieuwe primaire server in een beschikbaarheidszone die verschilt van de beschikbaarheidszone van de oude primaire database. Dus naast het testen van de invloed van failover op bestaande databasesessies, kunt u ook controleren of de end-to-end-prestaties worden gewijzigd als gevolg van wijzigingen in netwerklatentie. Omdat de herstartbewerking ingrijpender is en een groot aantal hiervan het platform kan bevallen, is slechts één failover-aanroep elke 15 minuten toegestaan voor elke database, elastische pool of beheerd exemplaar.

Een failover kan worden gestart met behulp van PowerShell, REST API of Azure CLI:

|Implementatietype|PowerShell|REST-API| Azure CLI|
|:---|:---|:---|:---|
|Database|[Invoke-AzSqlDatabaseFailover](/powershell/module/az.sql/invoke-azsqldatabasefailover)|[Database-failover](/rest/api/sql/databases/failover)|[az rest](/cli/azure/reference-index#az_rest) kan worden gebruikt om een REST API aan te roepen vanuit Azure CLI|
|Elastische pool|[Invoke-AzSqlElasticPoolFailover](/powershell/module/az.sql/invoke-azsqlelasticpoolfailover)|[Failover van elastische pool](/javascript/api/@azure/arm-sql/elasticpools#failover_string__string__string__msRest_RequestOptionsBase)|[az rest](/cli/azure/reference-index#az_rest) kan worden gebruikt om een REST API aan te roepen vanuit Azure CLI|
|Beheerd exemplaar|[Invoke-AzSqlInstanceFailover](/powershell/module/az.sql/Invoke-AzSqlInstanceFailover/)|[Beheerde exemplaren - Failover](/rest/api/sql/managed%20instances%20-%20failover/failover)|[az sql mi failover](/cli/azure/sql/mi/#az_sql_mi_failover)|

> [!IMPORTANT]
> De opdracht Failover is niet beschikbaar voor leesbare secundaire replica's van Hyperscale-databases.

## <a name="conclusion"></a>Conclusie

Azure SQL Database en Azure SQL Managed Instance een ingebouwde oplossing voor hoge beschikbaarheid, die diep is geïntegreerd met het Azure-platform. Het is afhankelijk van Service Fabric voor foutdetectie en -herstel, van Azure Blob Storage voor gegevensbeveiliging en van Beschikbaarheidszones voor hogere fouttolerantie (zoals eerder vermeld in het document dat nog niet van toepassing is op Azure SQL Managed Instance). Daarnaast maken SQL Database en SQL Managed Instance gebruik van de Always On-beschikbaarheidsgroeptechnologie van de SQL Server voor replicatie en failover. De combinatie van deze technologieën stelt toepassingen in staat om de voordelen van een gemengd opslagmodel volledig te realiseren en de meest veeleisende SLA's te ondersteunen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie [over Azure-beschikbaarheidszones](../../availability-zones/az-overview.md)
- Meer informatie [over Service Fabric](../../service-fabric/service-fabric-overview.md)
- Meer informatie [over Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md)
- Meer [informatie over het initiëren van een handmatige failover op SQL Managed Instance](../managed-instance/user-initiated-failover.md)
- Zie Bedrijfscontinuïteit voor meer opties voor hoge beschikbaarheid en herstel [na noodherstel](business-continuity-high-availability-disaster-recover-hadr-overview.md)
