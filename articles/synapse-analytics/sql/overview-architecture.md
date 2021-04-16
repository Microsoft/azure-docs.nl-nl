---
title: Synapse SQL-architectuur
description: Leer hoe Azure Synapse SQL mogelijkheden voor gedistribueerde queryverwerking combineert met Azure Storage voor hoge prestaties en schaalbaarheid.
services: synapse-analytics
author: mlee3gsd
manager: rothja
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: f342f39b62956cd85f269918e8e1ef1a2478a3d8
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566355"
---
# <a name="azure-synapse-sql-architecture"></a>Azure Synapse SQL-architectuur 

In dit artikel worden de architectuuronderdelen van Synapse SQL.

## <a name="synapse-sql-architecture-components"></a>Synapse SQL architectuuronderdelen

Synapse SQL maakt gebruik van een uitschaalarchitectuur om de rekenkundige verwerking van gegevens over meerdere knooppunten te verdelen. Compute staat los van opslag, waardoor u de rekenkracht onafhankelijk van de gegevens in uw systeem kunt schalen. 

Voor toegewezen SQL-pool is de schaaleenheid een abstractie van rekenkracht die ook wel een [datawarehouse-eenheid wordt genoemd.](resource-consumption-models.md) 

Voor serverloze SQL-pool, omdat deze serverloos is, wordt automatisch geschaald om te voldoen aan de vereisten voor queryresources. Naarmate de topologie na een periode verandert door knooppunten of failovers toe te voegen, wordt deze aangepast aan wijzigingen en zorgt deze ervoor dat uw query voldoende resources heeft en met succes wordt voltooid. In de onderstaande afbeelding ziet u bijvoorbeeld een serverloze SQL-pool die gebruikmaakt van 4 rekenknooppunten om een query uit te voeren.

![Synapse SQL-architectuur](./media//overview-architecture/sql-architecture.png)

Synapse SQL maakt gebruik van een architectuur op basis van knooppunt. Toepassingen maken verbinding en geven T-SQL-opdrachten uit aan een Beheer-knooppunt. Dit is het enige toegangspunt voor Synapse SQL. 

Het Azure Synapse SQL Control-knooppunt maakt gebruik van een gedistribueerde query-engine om query's te optimaliseren voor parallelle verwerking en geeft vervolgens bewerkingen door aan rekenknooppunten om hun werk parallel uit te voeren. 

Het beheerknooppunt van de serverloze SQL-pool maakt gebruik van de DQP-engine (Distributed Query Processing) om gedistribueerde uitvoering van gebruikersquery's te optimaliseren en in te delen door deze te splitsen in kleinere query's die worden uitgevoerd op rekenknooppunten. Elke kleine query wordt taak genoemd en vertegenwoordigt een gedistribueerde uitvoeringseenheid. Het leest een of meer bestanden uit de opslag, voegt resultaten van andere taken, groepen of orders toe die zijn opgehaald uit andere taken. 

De rekenknooppunten slaan alle gebruikersgegevens op in Azure Storage en voeren de parallelle query's uit. De DMS (Data Movement Service) is een interne service op systeemniveau die de gegevens naar de knooppunten verplaatst om tegelijkertijd query's te kunnen uitvoeren en nauwkeurige resultaten te retourneren. 

Bij gebruik van losgekoppelde opslag en rekenkracht kan Synapse SQL profiteren van onafhankelijke rekenkracht, ongeacht uw opslagbehoeften. Voor serverloze schaalbaarheid van SQL-pool wordt automatisch geschaald, terwijl voor toegewezen SQL-pool het volgende kan worden gedaan:

* U kunt de rekenkracht in een toegewezen SQL-pool uitbreiden of verkleinen zonder gegevens te verplaatsen.
* De rekencapaciteit onderbreekt terwijl gegevens intact blijven, zodat u alleen betaalt voor opslag.
* De rekencapaciteit hervat tijdens werktijden.

## <a name="azure-storage"></a>Azure Storage

Synapse SQL maakt gebruik van Azure Storage om uw gebruikersgegevens veilig te houden. Omdat uw gegevens worden opgeslagen en beheerd door Azure Storage, worden er afzonderlijke kosten in rekening brengen voor uw opslagverbruik. 

Met een serverloze SQL-pool kunt u query's uitvoeren op uw data lake-bestanden, terwijl u met een toegewezen SQL-pool gegevens kunt opvragen en opnemen uit data lake sql data lake bestanden. Wanneer gegevens worden opgenomen in een toegewezen SQL-pool, worden de gegevens geshard in **distributies** om de prestaties van het systeem te optimaliseren. Bij het definiëren van de tabel kunt u kiezen welk sharding-patroon u wilt gebruiken om de gegevens te distribueren. Deze shardingpatronen worden ondersteund:

* Hash
* Round Robin
* Repliceren

## <a name="control-node"></a>Beheerknooppunt

Het beheerknooppunt is het brein van de architectuur. Het is de front-end met interactie met alle toepassingen en verbindingen. 

In Synapse SQL wordt de gedistribueerde query-engine uitgevoerd op het beheer-knooppunt om parallelle query's te optimaliseren en te coördineren. Wanneer u een T-SQL-query naar een toegewezen SQL-pool indient, transformeert het beheer-knooppunt deze in query's die parallel worden uitgevoerd op elke distributie.

In een serverloze SQL-pool wordt de DQP-engine uitgevoerd op het beheerknooppunt om gedistribueerde uitvoering van gebruikersquery's te optimaliseren en te coördineren door deze te splitsen in kleinere query's die worden uitgevoerd op rekenknooppunten. Ook worden sets bestanden toegewezen die door elk knooppunt moeten worden verwerkt.

## <a name="compute-nodes"></a>Rekenknooppunten

De rekenknooppunten leveren de rekenkracht. 

In toegewezen SQL-pool worden distributies toegewezen aan rekenknooppunten voor verwerking. Wanneer u voor meer rekenresources betaalt, worden de distributies door de pool opnieuw aan de beschikbare rekenknooppunten toebedeeld. Het aantal rekenknooppunten varieert van 1 tot 60 en wordt bepaald door het serviceniveau voor de toegewezen SQL-pool. Elk reken knooppunt heeft een knooppunt-id die zichtbaar is in systeemweergaven. U kunt de id van het reken knooppunt bekijken door te zoeken naar de kolom node_id in systeemweergaven waarvan de namen beginnen met sys.pdw_nodes. Zie systeemweergaven voor een lijst [Synapse SQL systeemweergaven.](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=azure-sqldw-latest&preserve-view=true)

In een serverloze SQL-pool wordt aan elk reken knooppunt een taak en een set bestanden toegewezen om de taak op uit te voeren. Taak is een gedistribueerde eenheid voor het uitvoeren van query's, die deel uitmaakt van de verzonden querygebruiker. Automatisch schalen is van kracht om ervoor te zorgen dat er voldoende rekenknooppunten worden gebruikt om gebruikersquery's uit te voeren.

## <a name="data-movement-service"></a>Data Movement Service

Data Movement Service (DMS) is de technologie voor gegevenstransport in toegewezen SQL-pool die de verplaatsing van gegevens tussen de rekenknooppunten coördineert. Voor sommige query's is gegevensverkeer vereist om ervoor te zorgen dat de parallelle query's nauwkeurige resultaten retourneren. Wanneer gegevens worden geplaats, zorgt DMS ervoor dat de juiste gegevens op de juiste locatie worden gevonden.

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="distributions"></a>Distributies

Een distributie is de basiseenheid voor opslag en verwerking voor parallelle query's die worden uitgevoerd op gedistribueerde gegevens in toegewezen SQL-pool. Wanneer toegewezen SQL-pool een query uitvoert, wordt het werk onderverdeeld in 60 kleinere query's die parallel worden uitgevoerd. 

Elk van de 60 kleinere query's wordt uitgevoerd op een van de gegevensdistributies. Elk reken knooppunt beheert een of meer van de 60 distributies. Een toegewezen SQL-pool met het maximale aantal rekenbronnen heeft één distributie per reken knooppunt. Een toegewezen SQL-pool met minimale rekenbronnen heeft alle distributies op één reken knooppunt. 

## <a name="hash-distributed-tables"></a>Met hash gedistribueerde tabellen
Een met hash gedistribueerde tabel kan de hoogste queryprestaties leveren voor samenvoegingen en aggregaties in grotere tabellen. 

Voor het sharden van gegevens in een met hash gedistribueerde tabel gebruikt een toegewezen SQL-pool een hash-functie om elke rij deterministisch toe te wijzen aan één distributie. In de tabeldefinitie wordt een van de kolommen ingesteld als de distributiekolom. De hashfunctie maakt gebruik van de waarden in de distributiekolom om elke rij toe te wijzen aan een distributie.

In het volgende diagram ziet u hoe een volledige (niet-gedistribueerde tabel) wordt opgeslagen als een met hash gedistribueerde tabel. 

![Gedistribueerde tabel](media//overview-architecture/hash-distributed-table.png "Gedistribueerde tabel") 

* Elke rij behoort tot één distributie. 
* Een deterministisch hash-algoritme wijst elke rij toe aan één distributie. 
* Het aantal tabelrijen per distributie varieert, zoals wordt weergegeven door de verschillende grootten van tabellen.

Er zijn prestatieoverwegingen voor de selectie van een distributiekolom, zoals de uniekheid, gegevensverschil en de typen query's die op het systeem worden uitgevoerd.

## <a name="round-robin-distributed-tables"></a>Met round robin gedistribueerde tabellen

Een round robin-tabel is de eenvoudigste tabel om snelle prestaties te maken en te leveren wanneer deze wordt gebruikt als een faseringstabel voor belastingen.

Een met round robin gedistribueerde tabel distribueert de gegevens gelijkmatig in de tabel, maar zonder verdere optimalisatie. Een distributie wordt eerst willekeurig gekozen en vervolgens worden buffers van rijen opeenvolgend toegewezen aan distributies. In een round robin-tabel kunnen gegevens snel worden geladen. De prestaties van query's van met hash gedistribueerde tabellen zijn echter vaak beter. Voor joins op round robin-tabellen moeten gegevens opnieuw worden geshuffed. Dit kost extra tijd.

## <a name="replicated-tables"></a>Gerepliceerde tabellen
Een gerepliceerde tabel biedt de snelste prestaties van query's voor kleine tabellen.

Een tabel die wordt gerepliceerd, cachet een volledige kopie van de tabel op elk reken knooppunt. Het repliceren van een tabel maakt het dus niet meer nodig om gegevens over te dragen tussen rekenknooppunten vóór een samenvoeging of aggregatie. Gerepliceerde tabellen zijn het meest geschikt als het gaat om kleine tabellen. Extra opslag is vereist en er wordt extra overhead in rekening gebracht bij het schrijven van gegevens, waardoor grote tabellen niet praktisch zijn. 

In het onderstaande diagram ziet u een gerepliceerde tabel die in de cache wordt opgeslagen bij de eerste distributie op elk reken knooppunt. 

![Gerepliceerde tabel](media/overview-architecture/replicated-table.png "Gerepliceerde tabel") 

## <a name="next-steps"></a>Volgende stappen

Nu u iets weet over Synapse SQL, leert u hoe u snel een toegewezen [SQL-pool](../quickstart-create-sql-pool-portal.md) kunt maken en voorbeeldgegevens [kunt](../sql-data-warehouse/sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) laden (./sql-data-warehouse-load-sample-databases.md). Of begin met [het gebruik van een serverloze SQL-pool.](../quickstart-sql-on-demand.md) Als u niet bekend bent met Azure, kan de [Azure-woordenlijst](../../azure-glossary-cloud-terminology.md) handig zijn bij het opzoeken van nieuwe terminologie. 
