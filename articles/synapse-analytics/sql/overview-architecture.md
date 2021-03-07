---
title: Synapse SQL-architectuur
description: Ontdek hoe Azure Synapse SQL verwerkings mogelijkheden voor gedistribueerde query's combineert met Azure Storage om hoge prestaties en schaal baarheid te garanderen.
services: synapse-analytics
author: mlee3gsd
manager: rothja
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 28940272d39a08d790fe2cd913df808b02e7f426
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102441887"
---
# <a name="azure-synapse-sql-architecture"></a>Azure Synapse SQL-architectuur 

In dit artikel worden de architectuur onderdelen van Synapse SQL beschreven.

## <a name="synapse-sql-architecture-components"></a>Synapse SQL-architectuur onderdelen

Synapse SQL maakt gebruik van een scale-out architectuur voor het distribueren van reken kundige verwerking van gegevens over meerdere knoop punten. Compute staat los van de opslag, waarmee u de reken kracht onafhankelijk van de gegevens in uw systeem kunt schalen. 

Voor een toegewezen SQL-pool is de schaal eenheid een abstractie van reken kracht die wordt aangeduid als een [Data Warehouse-eenheid](resource-consumption-models.md). 

Voor een serverloze SQL-groep wordt serverloos geschaald, zodat deze automatisch kan worden aangepast aan query resource vereisten. Als topologie wijzigingen in de loop van de tijd worden gewijzigd door het toevoegen, verwijderen van knoop punten of failovers, wordt deze gewijzigd in wijzigingen en wordt gecontroleerd of uw query voldoende resources heeft en goed is voltooid. De onderstaande afbeelding toont bijvoorbeeld de serverloze SQL-pool die 4 reken knooppunten gebruikt om een query uit te voeren.

![Synapse SQL-architectuur](./media//overview-architecture/sql-architecture.png)

Synapse SQL maakt gebruik van een architectuur op basis van een knoop punt. Toepassingen maken verbinding met T-SQL-opdrachten en geven ze aan een besturings element knoop punt. Dit is het enige invoer punt voor Synapse SQL. 

Het Azure Synapse SQL-beheer knooppunt maakt gebruik van een gedistribueerde query-engine voor het optimaliseren van query's voor parallelle verwerking en geeft vervolgens bewerkingen aan reken knooppunten door om hun werk parallel te doen. 

Het beheer knooppunt van de serverloze SQL-groep maakt gebruik van de engine voor gedistribueerde query verwerking (DQP) om gedistribueerde uitvoering van gebruikers query te optimaliseren en te organiseren door deze te splitsen in kleinere query's die worden uitgevoerd op reken knooppunten. Elke kleine query heet taak en vertegenwoordigt een gedistribueerde uitvoerings eenheid. Het bestand (en) van de opslag wordt gelezen, de resultaten worden samengevoegd van andere taken, groepen of order gegevens die zijn opgehaald uit andere taken. 

De rekenknooppunten slaan alle gebruikersgegevens op in Azure Storage en voeren de parallelle query's uit. De DMS (Data Movement Service) is een interne service op systeemniveau die de gegevens naar de knooppunten verplaatst om tegelijkertijd query's te kunnen uitvoeren en nauwkeurige resultaten te retourneren. 

Bij het gebruik van Synapse SQL One kan een onafhankelijke omvang van reken kracht worden benut, ongeacht uw opslag behoeften. Voor het schalen van een serverloze SQL-groep wordt automatisch uitgevoerd, terwijl voor de toegewezen SQL-groep één kan:

* De reken kracht binnen een toegewezen SQL-groep verg Roten of verkleinen zonder gegevens te verplaatsen.
* De rekencapaciteit onderbreekt terwijl gegevens intact blijven, zodat u alleen betaalt voor opslag.
* De rekencapaciteit hervat tijdens werktijden.

## <a name="azure-storage"></a>Azure Storage

Synapse SQL maakt gebruik van Azure Storage om uw gebruikers gegevens veilig te maken. Omdat uw gegevens worden opgeslagen en beheerd door Azure Storage, worden er afzonderlijke kosten in rekening gebracht voor uw opslag verbruik. 

Met serverloze SQL-pool kunt u een query uitvoeren op uw data Lake-bestanden, terwijl u met exclusieve SQL-groep gegevens uit uw data Lake-bestanden kunt opvragen en opnemen. Wanneer gegevens worden opgenomen in de toegewezen SQL-pool, worden de gegevens Shard in **distributies** om de prestaties van het systeem te optimaliseren. Bij het definiëren van de tabel kunt u kiezen welk sharding-patroon u wilt gebruiken om de gegevens te distribueren. Deze sharding-patronen worden ondersteund:

* Hash
* Round Robin
* Repliceren

## <a name="control-node"></a>Beheerknooppunt

Het beheerknooppunt is het brein van de architectuur. Het is de front-end met interactie met alle toepassingen en verbindingen. 

In Synapse SQL wordt de gedistribueerde query-engine uitgevoerd op het beheer knooppunt om parallelle query's te optimaliseren en te coördineren. Wanneer u een T-SQL-query naar een toegewezen SQL-groep verzendt, transformeert het knoop punt deze in query's die worden uitgevoerd op elke distributie parallel.

In een serverloze SQL-pool wordt de DQP-Engine uitgevoerd op knoop punt beheer om gedistribueerde uitvoering van gebruikers query te optimaliseren en te coördineren door deze te splitsen in kleinere query's die worden uitgevoerd op reken knooppunten. Ook worden er sets bestanden toegewezen die door elk knoop punt moeten worden verwerkt.

## <a name="compute-nodes"></a>Rekenknooppunten

De rekenknooppunten leveren de rekenkracht. 

Distributies worden in toegewezen SQL-groep toegewezen aan reken knooppunten voor verwerking. Wanneer u betaalt voor meer reken bronnen, wijst de groep de distributies opnieuw toe aan de beschik bare reken knooppunten. Het aantal Compute-knoop punten ligt tussen 1 en 60, en wordt bepaald door het service niveau voor de toegewezen SQL-groep. Elk Compute-knoop punt heeft een knoop punt-ID die zichtbaar is in systeem weergaven. U kunt de ID van het reken knooppunt zien door te zoeken naar de node_id kolom in systeem weergaven waarvan de namen beginnen met sys.pdw_nodes. Zie [Synapse SQL System views](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=azure-sqldw-latest&preserve-view=true)(Engelstalig) voor een lijst met deze systeem weergaven.

In een serverloze SQL-groep wordt aan elk reken knooppunt een taak en een set bestanden toegewezen om taken uit te voeren. De taak is een gedistribueerde eenheid voor query uitvoering, die eigenlijk deel uitmaakt van de query gebruiker die is verzonden. Automatisch schalen is actief om er zeker van te zijn dat er voldoende reken knooppunten worden gebruikt voor het uitvoeren van een gebruikers query.

## <a name="data-movement-service"></a>Data Movement Service

Gegevens verplaatsings service (DMS) is de technologie voor gegevens overdracht in een exclusieve SQL-groep die de verplaatsing van gegevens tussen de reken knooppunten coördineert. Voor sommige query's is gegevens verplaatsing vereist om ervoor te zorgen dat de parallelle query's accurate resultaten retour neren. Wanneer gegevens verplaatsing vereist is, zorgt DMS ervoor dat de juiste gegevens naar de juiste locatie worden opgehaald.

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="distributions"></a>Distributies

Een distributie is de basis eenheid voor opslag en verwerking voor parallelle query's die worden uitgevoerd op gedistribueerde gegevens in de toegewezen SQL-groep. Wanneer een exclusieve SQL-pool een query uitvoert, wordt het werk onderverdeeld in 60 kleinere query's die parallel worden uitgevoerd. 

Elk van de 60 kleinere query's worden uitgevoerd op een van de gegevens distributies. Elk Compute-knoop punt beheert een of meer van de 60-distributies. Een toegewezen SQL-groep met een maximum reken resource heeft één distributie per reken knooppunt. Een toegewezen SQL-groep met minimale Compute-resources heeft alle distributies op één reken knooppunt. 

## <a name="hash-distributed-tables"></a>Met hash gedistribueerde tabellen
Een met hash gedistribueerde tabel kan de hoogste queryprestaties leveren voor samenvoegingen en aggregaties in grotere tabellen. 

Voor het Shard van gegevens in een hash-gedistribueerde tabel gebruikt een toegewezen SQL-groep een hash-functie voor het per onbewerkte toewijzing van elke rij aan één verdeling. In de tabeldefinitie wordt een van de kolommen ingesteld als de distributiekolom. De hashfunctie maakt gebruik van de waarden in de distributiekolom om elke rij toe te wijzen aan een distributie.

In het volgende diagram ziet u hoe een volledige (niet-gedistribueerde tabel) wordt opgeslagen als een hash-Distributed Table. 

![Gedistribueerde tabel](media//overview-architecture/hash-distributed-table.png "Gedistribueerde tabel") 

* Elke rij behoort tot één distributie. 
* Een deterministische hash-algoritme wijst elke rij toe aan één verdeling. 
* Het aantal tabel rijen per distributie varieert, zoals wordt weer gegeven in de verschillende grootten van tabellen.

Er zijn prestatie overwegingen voor de selectie van een distributie kolom, zoals de distinctiteit, de schei ding van gegevens en de typen query's die op het systeem worden uitgevoerd.

## <a name="round-robin-distributed-tables"></a>Met round robin gedistribueerde tabellen

Een Round Robin-tabel is de eenvoudigste tabel om snelle prestaties te maken en op te halen wanneer deze wordt gebruikt als faserings tabel voor belasting.

Een met round robin gedistribueerde tabel distribueert de gegevens gelijkmatig in de tabel, maar zonder verdere optimalisatie. Een distributie wordt in wille keurige volg orde gekozen en vervolgens worden buffers van rijen opeenvolgend toegewezen aan distributies. In een round robin-tabel kunnen gegevens snel worden geladen. De prestaties van query's van met hash gedistribueerde tabellen zijn echter vaak beter. Voor het samen voegen van Round-Robin tabellen zijn gegevens met een andere volg orde nodig. dit duurt meer tijd.

## <a name="replicated-tables"></a>Gerepliceerde tabellen
Een gerepliceerde tabel biedt de snelste prestaties van query's voor kleine tabellen.

Een tabel die wordt gerepliceerd, plaatst een volledige kopie van de tabel op elk reken knooppunt. Het repliceren van een tabel verwijdert dus de nood zaak om gegevens over te dragen tussen reken knooppunten vóór een samen voeging of aggregatie. Gerepliceerde tabellen zijn het meest geschikt als het gaat om kleine tabellen. Extra opslag ruimte is vereist en er worden extra overhead kosten in rekening gebracht voor het schrijven van gegevens, waardoor grote tabellen onbruikbaar worden. 

In het onderstaande diagram ziet u een gerepliceerde tabel die in de cache wordt opgeslagen op de eerste distributie op elk reken knooppunt. 

![Gerepliceerde tabel](media/overview-architecture/replicated-table.png "Gerepliceerde tabel") 

## <a name="next-steps"></a>Volgende stappen

Nu u een beetje weet over Synapse SQL, leert u hoe u snel [een specifieke SQL-groep kunt maken](../quickstart-create-sql-pool-portal.md) en [voorbeeld gegevens](../sql-data-warehouse/sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) (./SQL-Data-Warehouse-load-sample-databases.MD) kunt laden. Of begin [met serverloze SQL-groep](../quickstart-sql-on-demand.md). Als u niet bekend bent met Azure, kan de [Azure-woordenlijst](../../azure-glossary-cloud-terminology.md) handig zijn bij het opzoeken van nieuwe terminologie. 
