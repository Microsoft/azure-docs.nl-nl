---
title: De architectuur van de exclusieve SQL-groep (voorheen SQL DW)
description: Meer informatie over hoe toegewezen SQL-pool (voorheen SQL DW) in azure Synapse Analytics combineert verwerkings mogelijkheden voor gedistribueerde query's met Azure Storage om hoge prestaties en schaal baarheid te garanderen.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 0e87451531750e502f67dc30e6fbd26c8c944d22
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678590"
---
# <a name="dedicated-sql-pool-formerly-sql-dw-architecture-in-azure-synapse-analytics"></a>Exclusieve architectuur van een SQL-groep (voorheen SQL DW) in azure Synapse Analytics

Azure Synapse Analytics is een analyseservice die datawarehousing voor ondernemingen en big data-analyses combineert. Het biedt u de vrijheid om gegevens op basis van uw voor waarden op te vragen.

> [!NOTE]
>Verken de [Documentatie voor Azure Synapse Analytics](../overview-what-is.md).
>


> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="synapse-sql-architecture-components"></a>Synapse SQL-architectuur onderdelen

Een [toegewezen SQL-groep (voorheen SQL DW)](sql-data-warehouse-overview-what-is.md) maakt gebruik van een scale-out architectuur voor het distribueren van reken processen van gegevens over meerdere knoop punten. De schaal eenheid is een abstractie van reken kracht die wordt aangeduid als [Data Warehouse-eenheid](what-is-a-data-warehouse-unit-dwu-cdwu.md). Compute staat los van de opslag, waarmee u de reken kracht onafhankelijk van de gegevens in uw systeem kunt schalen.

![De architectuur van de exclusieve SQL-groep (voorheen SQL DW)](./media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

Een toegewezen SQL-groep (voorheen SQL DW) maakt gebruik van een architectuur op basis van een knoop punt. Toepassingen maken verbinding met T-SQL-opdrachten en geven ze een besturings element. Het controle knooppunt fungeert als host voor de gedistribueerde query-engine, waarmee query's voor parallelle verwerking worden geoptimaliseerd. vervolgens worden bewerkingen aan reken knooppunten door gegeven om hun werk parallel uit te voeren.

De rekenknooppunten slaan alle gebruikersgegevens op in Azure Storage en voeren de parallelle query's uit. De DMS (Data Movement Service) is een interne service op systeemniveau die de gegevens naar de knooppunten verplaatst om tegelijkertijd query's te kunnen uitvoeren en nauwkeurige resultaten te retourneren.

Bij het gebruik van een toegewezen SQL-groep (voorheen SQL DW) kan een exclusieve opslag en reken kracht worden gebruikt:

- Reken kracht onafhankelijk van de grootte van uw opslag behoeften.
- Verg root of verklein reken kracht binnen een toegewezen SQL-groep (voorheen SQL DW) zonder gegevens te hoeven verplaatsen.
- De rekencapaciteit onderbreekt terwijl gegevens intact blijven, zodat u alleen betaalt voor opslag.
- De rekencapaciteit hervat tijdens werktijden.

### <a name="azure-storage"></a>Azure Storage

De exclusieve SQL-groep SQL (voorheen SQL DW) maakt gebruik van Azure Storage om uw gebruikers gegevens veilig te maken.  Omdat uw gegevens worden opgeslagen en beheerd door Azure Storage, worden er afzonderlijke kosten in rekening gebracht voor uw opslag verbruik. De gegevens worden in **distributies** Shard om de prestaties van het systeem te optimaliseren. Bij het definiëren van de tabel kunt u kiezen welk sharding-patroon u wilt gebruiken om de gegevens te distribueren. Deze sharding-patronen worden ondersteund:

- Hash
- Round Robin
- Repliceren

### <a name="control-node"></a>Beheerknooppunt

Het beheerknooppunt is het brein van de architectuur. Het is de front-end met interactie met alle toepassingen en verbindingen. De gedistribueerde query-engine wordt uitgevoerd op het beheer knooppunt om parallelle query's te optimaliseren en te coördineren. Wanneer u een T-SQL-query verzendt, transformeert het knoop punt van de controle in query's die parallel worden uitgevoerd op elke distributie.

### <a name="compute-nodes"></a>Rekenknooppunten

De rekenknooppunten leveren de rekenkracht. Distributies worden toegewezen aan reken knooppunten voor verwerking. Wanneer u betaalt voor meer reken bronnen, worden distributies opnieuw toegewezen aan beschik bare reken knooppunten. Het aantal Compute-knoop punten ligt tussen 1 en 60, en wordt bepaald door het service niveau voor Synapse SQL.

Elk Compute-knoop punt heeft een knoop punt-ID die zichtbaar is in systeem weergaven. U kunt de ID van het reken knooppunt zien door te zoeken naar de node_id kolom in systeem weergaven waarvan de namen beginnen met sys.pdw_nodes. Zie [Synapse SQL System views](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)(Engelstalig) voor een lijst met deze systeem weergaven.

### <a name="data-movement-service"></a>Data Movement Service

Gegevens verplaatsings service (DMS) is de gegevens transport technologie die de gegevens verplaatsing tussen de reken knooppunten coördineert. Voor sommige query's is gegevens verplaatsing vereist om ervoor te zorgen dat de parallelle query's accurate resultaten retour neren. Wanneer gegevens verplaatsing vereist is, zorgt DMS ervoor dat de juiste gegevens naar de juiste locatie worden opgehaald.

## <a name="distributions"></a>Distributies

Een distributie is de basiseenheid voor opslag en verwerking van parallelle query's die op gedistribueerde gegevens worden uitgevoerd. Wanneer Synapse SQL een query uitvoert, wordt het werk onderverdeeld in 60 kleinere query's die parallel worden uitgevoerd.

Elk van de 60 kleinere query's worden uitgevoerd op een van de gegevens distributies. Elk Compute-knoop punt beheert een of meer van de 60-distributies. Een toegewezen SQL-groep (voorheen SQL DW) met een maximum reken resource heeft één distributie per reken knooppunt. Een toegewezen SQL-groep (voorheen SQL DW) met minimale Compute-resources heeft alle distributies op één reken knooppunt.  

## <a name="hash-distributed-tables"></a>Met hash gedistribueerde tabellen

Een met hash gedistribueerde tabel kan de hoogste queryprestaties leveren voor samenvoegingen en aggregaties in grotere tabellen.

Als u gegevens wilt Shard in een hash-gedistribueerde tabel, wordt een hash-functie gebruikt om per onwaar elke rij aan één verdeling toe te wijzen. In de tabeldefinitie wordt een van de kolommen ingesteld als de distributiekolom. De hashfunctie maakt gebruik van de waarden in de distributiekolom om elke rij toe te wijzen aan een distributie.

In het volgende diagram ziet u hoe een volledige (niet-gedistribueerde tabel) wordt opgeslagen als een hash-Distributed Table.

![Gedistribueerde tabel](./media/massively-parallel-processing-mpp-architecture/hash-distributed-table.png "Gedistribueerde tabel")  

- Elke rij behoort tot één distributie.  
- Een deterministische hash-algoritme wijst elke rij toe aan één verdeling.  
- Het aantal tabel rijen per distributie varieert, zoals wordt weer gegeven in de verschillende grootten van tabellen.

Er zijn prestatie overwegingen voor de selectie van een distributie kolom, zoals de distinctiteit, de schei ding van gegevens en de typen query's die op het systeem worden uitgevoerd.

## <a name="round-robin-distributed-tables"></a>Met round robin gedistribueerde tabellen

Een Round Robin-tabel is de eenvoudigste tabel om snelle prestaties te maken en op te halen wanneer deze wordt gebruikt als faserings tabel voor belasting.

Een met round robin gedistribueerde tabel distribueert de gegevens gelijkmatig in de tabel, maar zonder verdere optimalisatie. Een distributie wordt in wille keurige volg orde gekozen en vervolgens worden buffers van rijen opeenvolgend toegewezen aan distributies. In een round robin-tabel kunnen gegevens snel worden geladen. De prestaties van query's van met hash gedistribueerde tabellen zijn echter vaak beter. Voor het samen voegen van Round-Robin tabellen zijn gegevens met een andere volg orde nodig. dit duurt meer tijd.

## <a name="replicated-tables"></a>Gerepliceerde tabellen

Een gerepliceerde tabel biedt de snelste prestaties van query's voor kleine tabellen.

Een tabel die wordt gerepliceerd, plaatst een volledige kopie van de tabel op elk reken knooppunt. Hierdoor hoeven de gegevens niet te worden overgedragen tussen rekenknooppunten voor een samenvoeging of aggregatie. Gerepliceerde tabellen zijn het meest geschikt als het gaat om kleine tabellen. Extra opslag ruimte is vereist en er worden extra overhead kosten in rekening gebracht voor het schrijven van gegevens, waardoor grote tabellen onbruikbaar worden.  

In het onderstaande diagram ziet u een gerepliceerde tabel die in de cache wordt opgeslagen op de eerste distributie op elk reken knooppunt.  

![Gerepliceerde tabel](./media/massively-parallel-processing-mpp-architecture/replicated-table.png "Gerepliceerde tabel")

## <a name="next-steps"></a>Volgende stappen

Nu u een beetje kent over Azure Synapse, leert u hoe u snel [een toegewezen SQL-groep (voorheen SQL DW) maakt](create-data-warehouse-portal.md) en [voorbeeld gegevens laadt](./load-data-from-azure-blob-storage-using-copy.md). Als u niet bekend bent met Azure, kan de [Azure-woordenlijst](../../azure-glossary-cloud-terminology.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) handig zijn bij het opzoeken van nieuwe terminologie. Of Bekijk enkele van deze andere Azure Synapse-resources.  

- [Succesverhalen van klanten](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [Blogs](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [Functieverzoeken](https://feedback.azure.com/forums/307516-sql-data-warehouse)
- [Video's](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [Ondersteuningsticket maken](sql-data-warehouse-get-started-create-support-ticket.md)
- [Microsoft Q&A-vragenpagina](/answers/topics/azure-synapse-analytics.html)
- [Stack Overflow forum](https://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)