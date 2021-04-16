---
title: Azure Synapse Link voor Azure Cosmos DB, voordelen en wanneer u deze gebruikt
description: Meer informatie over Azure Synapse Link voor Azure Cosmos DB. Synapse Link kunt u bijna realtime analyses (HTAP) uitvoeren met behulp van Azure Synapse Analytics over operationele gegevens in Azure Cosmos DB.
author: Rodrigossz
ms.author: rosouz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/30/2020
ms.reviewer: sngun
ms.custom: synapse-cosmos-db
ms.openlocfilehash: 123c443e1afaf8eaded7021b963b68b3d8a8f554
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483755"
---
# <a name="what-is-azure-synapse-link-for-azure-cosmos-db"></a>Wat is Azure Synapse Link voor Azure Cosmos DB?
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Synapse Link voor Azure Cosmos DB is een cloudeigen HTAP-functie (hybride transactionele en analytische verwerking) waarmee u bijna realtime analyses kunt uitvoeren op operationele gegevens in Azure Cosmos DB. Azure Synapse Link zorgt voor een naadloze integratie tussen Azure Cosmos DB en Azure Synapse Analytics.

Met [Azure Cosmos DB](analytical-store-introduction.md)analytische opslag, een volledig geïsoleerde kolomopslag, maakt Azure Synapse Link geen ETL-analyses (Extract-Transform-Load) in [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md) mogelijk op basis van uw operationele gegevens op schaal. Bedrijfsanalisten, data engineers en gegevenswetenschappers kunnen Synapse Spark of Synapse SQL nu door elkaar gebruiken om bijna realtime business intelligence-, analyse- en machine learning-pijplijnen uit te voeren. U kunt dit doen zonder dat dit van invloed is op de prestaties van uw transactionele werkbelastingen op Azure Cosmos DB. 

In de volgende afbeelding ziet u Azure Synapse koppelingsintegratie met Azure Cosmos DB en Azure Synapse Analytics: 

:::image type="content" source="./media/synapse-link/synapse-analytics-cosmos-db-architecture.png" alt-text="Architectuurdiagram voor Azure Synapse Analytics integratie met Azure Cosmos DB" border="false":::

## <a name="benefits"></a><a id="synapse-link-benefits"></a> Voordelen

Om grote operationele gegevenssets te analyseren en tegelijkertijd de impact op de prestaties van essentiële transactionele workloads te minimaliseren, worden de operationele gegevens in Azure Cosmos DB traditioneel geëxtraheerd en verwerkt door ETL-pijplijnen (Extract-Transform-Load). ETL-pijplijnen vereisen veel lagen van gegevensver movement, wat resulteert in veel operationele complexiteit en prestatie-impact op uw transactionele workloads. Het verhoogt ook de latentie voor het analyseren van de operationele gegevens vanaf het tijdstip van oorsprong.

In vergelijking met de traditionele ETL-oplossingen biedt Azure Synapse Link for Azure Cosmos DB verschillende voordelen, zoals:  

### <a name="reduced-complexity-with-no-etl-jobs-to-manage"></a>Minder complexiteit met geen ETL-taken om te beheren

Azure Synapse Link kunt u rechtstreeks toegang krijgen tot Azure Cosmos DB analytische opslag met behulp Azure Synapse Analytics zonder complexe gegevens verplaatsen. Updates van de operationele gegevens zijn in bijna realtime zichtbaar in de analytische opslag zonder ETL- of wijzigingenfeedtaken. U kunt grootschalige analyses uitvoeren voor analytische opslag, Azure Synapse Analytics, zonder aanvullende gegevenstransformatie.

### <a name="near-real-time-insights-into-your-operational-data"></a>Bijna realtime inzichten in uw operationele gegevens

U kunt nu in bijna realtime uitgebreide inzichten krijgen in uw operationele gegevens met behulp van Azure Synapse Link. ETL-systemen hebben vaak een hogere latentie voor het analyseren van uw operationele gegevens, omdat er veel lagen nodig zijn om de operationele gegevens te extraheren, transformeren en laden. Met systeemeigen integratie van Azure Cosmos DB analytische opslag met Azure Synapse Analytics kunt u operationele gegevens in bijna realtime analyseren, waardoor nieuwe bedrijfsscenario's mogelijk zijn. 

### <a name="no-impact-on-operational-workloads"></a>Geen invloed op operationele workloads

Met Azure Synapse Link kunt u analytische query's uitvoeren op een Azure Cosmos DB Analytical Store (een afzonderlijk kolomopslag) terwijl de transactionele bewerkingen worden verwerkt met behulp van ingerichte doorvoer voor de transactionele workload (een transactionele opslag op rijbasis).  De analytische workload wordt onafhankelijk van het transactionele workloadverkeer bediend zonder gebruik te maken van de doorvoer die is ingericht voor uw operationele gegevens.

### <a name="optimized-for-large-scale-analytics-workloads"></a>Geoptimaliseerd voor grootschalige analyseworkloads

Azure Cosmos DB analytische opslag is geoptimaliseerd om schaalbaarheid, elasticiteit en prestaties te bieden voor analytische workloads zonder afhankelijk te zijn van de rekenrun times. De opslagtechnologie wordt zelf beheerd om uw analyseworkloads te optimaliseren. Met ingebouwde ondersteuning in Azure Synapse Analytics biedt toegang tot deze opslaglaag eenvoud en hoge prestaties.

### <a name="cost-effective"></a>Rendabel

Met Azure Synapse Link kunt u een voor kosten geoptimaliseerde, volledig beheerde oplossing voor operationele analyse krijgen. Het elimineert de extra opslag- en rekenlagen die nodig zijn in traditionele ETL-pijplijnen voor het analyseren van operationele gegevens. 

Azure Cosmos DB analytische opslag volgt een prijsmodel op basis van verbruik, dat is gebaseerd op gegevensopslag en analytische lees-/schrijfbewerkingen en query's die worden uitgevoerd. U hoeft geen doorvoer in terichten, zoals u nu doet voor de transactionele workloads. Door toegang tot uw gegevens met zeer elastische berekeningsen engines Azure Synapse Analytics worden de totale kosten voor het uitvoeren van opslag en rekenkracht zeer efficiënt.


### <a name="analytics-for-locally-available-globally-distributed-multi-region-writes"></a>Analyse voor lokaal beschikbare, wereldwijd gedistribueerde schrijf- en schrijf schrijfgegevens in meerdere regio's

U kunt analytische query's effectief uitvoeren op de dichtstbijzijnde regionale kopie van de gegevens in Azure Cosmos DB. Azure Cosmos DB biedt de meest moderne mogelijkheid om de wereldwijd gedistribueerde analytische workloads samen met transactionele workloads op een actief/actief-manier uit te voeren.

## <a name="enable-htap-scenarios-for-your-operational-data"></a>HTAP-scenario's voor uw operationele gegevens inschakelen

Synapse Link brengt een analytische opslag Azure Cosmos DB samen met Azure Synapse analytics runtime-ondersteuning. Met deze integratie kunt u cloudeigen HTAP-oplossingen (hybride transactionele/analytische verwerking) bouwen die inzichten genereren op basis van realtime updates van uw operationele gegevens over grote gegevenssets. Het ontgrendelt nieuwe bedrijfsscenario's om waarschuwingen te geven op basis van live trends, dashboards in bijna realtime te bouwen en zakelijke ervaringen te maken op basis van gebruikersgedrag.

### <a name="azure-cosmos-db-analytical-store"></a>Azure Cosmos DB analytische opslag

Azure Cosmos DB analytische opslag is een kolomgeoriënteerde weergave van uw operationele gegevens in Azure Cosmos DB. Deze analytische opslag is geschikt voor snelle, rendabele query's op grote operationele gegevenssets, zonder gegevens te kopiëren en de prestaties van uw transactionele workloads te beïnvloeden.

Analytische opslag haalt automatisch in bijna realtime invoegingen, updates en verwijderen van hoge frequentie in uw transactionele workloads op als een volledig beheerde mogelijkheid ('automatische synchronisatie') van Azure Cosmos DB. Er is geen wijzigingsfeed of ETL vereist. 

Als u een wereldwijd gedistribueerd account Azure Cosmos DB opslagaccount hebt, is het account na het inschakelen van analytische opslag voor een container beschikbaar in alle regio's voor dat account. Zie het artikel Overzicht van analytische opslag Azure Cosmos DB analytische opslag voor meer informatie over [de analytische](analytical-store-introduction.md) opslag.

### <a name="integration-with-azure-synapse-analytics"></a><a id="synapse-link-integration"></a>Integratie met Azure Synapse Analytics

Met Synapse Link kunt u nu rechtstreeks verbinding maken met uw Azure Cosmos DB-containers vanuit Azure Synapse Analytics en toegang krijgen tot de analytische opslag zonder afzonderlijke connectors. Azure Synapse Analytics ondersteunt momenteel Synapse Link met [Synapse Apache Spark](../synapse-analytics/spark/apache-spark-concepts.md) en [serverloze SQL-pool](../synapse-analytics/sql/on-demand-workspace-overview.md).

U kunt de gegevens opvragen uit Azure Cosmos DB analytische opslag tegelijk, met interop tussen verschillende analyserun times die worden ondersteund door Azure Synapse Analytics. Er zijn geen aanvullende gegevenstransformaties vereist voor het analyseren van de operationele gegevens. U kunt de analytische opslaggegevens opvragen en analyseren met behulp van:

* Synapse Apache Spark volledige ondersteuning voor Scala, Python, SparkSQL en C#. Synapse Spark staat centraal in data engineering scenario's voor gegevenswetenschap

* Serverloze SQL-pool met T-SQL-taal en ondersteuning voor vertrouwde BI-hulpprogramma's (bijvoorbeeld Power BI Premium, enzovoort)

> [!NOTE]
> Vanuit Azure Synapse Analytics hebt u toegang tot zowel analytische als transactionele winkels in Azure Cosmos DB container. Als u echter grootschalige analyses of scans wilt uitvoeren op uw operationele gegevens, raden we u aan analytische opslag te gebruiken om prestatie-impact op transactionele workloads te voorkomen.

> [!NOTE]
> U kunt analyses met lage latentie uitvoeren in een Azure-regio door uw Azure Cosmos DB container te verbinden met synapse-runtime in die regio.

Deze integratie maakt de volgende HTAP-scenario's mogelijk voor verschillende gebruikers:

* Een BI-engineer die een rapport wil modelleren en publiceren Power BI om rechtstreeks toegang te krijgen tot de live operationele gegevens in Azure Cosmos DB via Synapse SQL.

* Een gegevensanalist die inzichten wil afleiden uit de operationele gegevens in een Azure Cosmos DB-container door er query's op uit te voeren met Synapse SQL, de gegevens op schaal te lezen en deze bevindingen te combineren met andere gegevensbronnen.

* Een data scientist die Synapse Spark wil gebruiken om een functie te vinden om het model te verbeteren en dat model te trainen zonder complexe data engineering. Ze kunnen ook de resultaten van de modelpostdeferentie naar de Azure Cosmos DB voor het in realtime scoren van de gegevens via Spark Synapse.

* Een data engineer die gegevens toegankelijk wil maken voor consumenten door SQL- of Spark-tabellen te maken via Azure Cosmos DB containers zonder handmatige ETL-processen.

Zie voor meer informatie Azure Synapse Analytics ondersteuning voor runtime voor Azure Cosmos DB ondersteuning Azure Synapse Analytics [voor Cosmos DB ondersteuning.](../synapse-analytics/synapse-link/concept-synapse-link-cosmos-db-support.md)

## <a name="when-to-use-azure-synapse-link-for-azure-cosmos-db"></a>Wanneer gebruikt u Azure Synapse Link voor Azure Cosmos DB?

Synapse Link wordt aanbevolen in de volgende gevallen:

* Als u een Azure Cosmos DB bent en u analyses, BI en machine learning uw operationele gegevens wilt uitvoeren. In dergelijke gevallen biedt Synapse Link een meer geïntegreerde analyse-ervaring zonder dat dit van invloed is op de inrichtende doorvoer van uw transactionele opslag. Bijvoorbeeld:

  * Als u analyses of BI op uw Azure Cosmos DB operationele gegevens rechtstreeks met behulp van afzonderlijke connectors, of

  * Als u ETL-processen gebruikt om operationele gegevens te extraheren in een afzonderlijk analysesysteem.
 
In dergelijke gevallen biedt Synapse Link een meer geïntegreerde analyse-ervaring zonder dat dit van invloed is op de inrichtende doorvoer van uw transactionele opslag.

Synapse Link wordt niet aanbevolen als u op zoek bent naar traditionele vereisten voor datawarehouses, zoals hoge gelijktijdigheid, workloadbeheer en persistentie van statistische gegevensbronnen. Zie algemene scenario's die mogelijk worden gemaakt met Azure Synapse Link voor [Azure Cosmos DB voor meer Azure Cosmos DB.](synapse-link-use-cases.md)

## <a name="limitations"></a>Beperkingen

* Azure Synapse Link voor Azure Cosmos DB wordt ondersteund voor SQL API en Azure Cosmos DB voor MongoDB-API. Het wordt niet ondersteund voor Gremlin API, Cassandra-API en Table-API.

* Analytische opslag kan alleen worden ingeschakeld voor nieuwe containers. Als u analytische opslag wilt gebruiken voor bestaande containers, migreert u gegevens van uw bestaande containers naar nieuwe containers met behulp [Azure Cosmos DB migratiehulpprogramma's.](cosmosdb-migrationchoices.md) U kunt deze Synapse Link voor nieuwe en bestaande Azure Cosmos DB accounts.

* Voor de containers waarin analytische opslag is ingeschakeld, wordt automatische back-up en herstel van uw gegevens in de analytische opslag op dit moment niet ondersteund. Wanneer Synapse Link is ingeschakeld voor een databaseaccount, blijft Azure Cosmos DB automatisch [](./online-backup-and-restore.md) back-ups maken van uw gegevens in de transactionele opslag (alleen) van containers met een gepland back-upinterval, zoals altijd. Het is belangrijk te weten dat wanneer een container met analytische opslag wordt ingeschakeld, wordt hersteld naar een nieuw account, de container wordt hersteld met alleen transactionele opslag en geen analytische opslag ingeschakeld.

* Toegang tot Azure Cosmos DB analytics-opslag met Synapse SQL is momenteel niet beschikbaar.

## <a name="security"></a>Beveiliging

Synapse Link kunt u bijna realtime analyses uitvoeren voor uw bedrijfskritische gegevens in Azure Cosmos DB. Het is essentieel om ervoor te zorgen dat kritieke bedrijfsgegevens veilig worden opgeslagen in zowel transactionele als analytische opslag. Azure Synapse Link for Azure Cosmos DB is ontworpen om te voldoen aan deze beveiligingsvereisten via de volgende functies:

* **Netwerkisolatie met behulp van** privé-eindpunten: u kunt de netwerktoegang tot de gegevens in de transactionele en analytische opslag onafhankelijk van elkaar bepalen. Netwerkisolatie wordt uitgevoerd met behulp van afzonderlijke beheerde privé-eindpunten voor elke winkel, binnen beheerde virtuele netwerken in Azure Synapse werkruimten. Zie het artikel Privé-eindpunten configureren voor analytische opslag [voor meer](analytical-store-private-endpoints.md) informatie.

* **Gegevensversleuteling** met door de klant beheerde sleutels: u kunt de gegevens naadloos op een automatische en transparante manier versleutelen in transactionele en analytische opslag met dezelfde door de klant beheerde sleutels. Zie het artikel Door de klant beheerde sleutels configureren [voor meer](how-to-setup-cmk.md) informatie.

* **Beveiligd sleutelbeheer:** voor toegang tot de gegevens in de analytische opslag vanuit serverloze SQL-pools van Synapse Spark en Synapse moet u Azure Cosmos DB-sleutels in Synapse Analytics werkruimten beheren. In plaats van de Azure Cosmos DB-accountsleutels inline te gebruiken in Spark-taken of SQL-scripts, biedt Azure Synapse Link veiligere mogelijkheden.

  * Wanneer u serverloze SQL-pools van Synapse gebruikt, kunt u een query uitvoeren op de analytische opslag van Azure Cosmos DB door vooraf SQL-referenties te maken voor het opslaan van de accountsleutels en deze in de functie te `OPENROWSET` verwijzen. Zie Query with [a serverless SQL pool (Query uitvoeren met een serverloze SQL-pool) in het Azure Synapse Link voor meer](../synapse-analytics/sql/query-cosmos-db-analytical-store.md) informatie.

  * Wanneer u Synapse Spark gebruikt, kunt u de accountsleutels opslaan in gekoppelde serviceobjecten die verwijzen naar een Azure Cosmos DB-database en hier tijdens runtime naar verwijzen in de Spark-configuratie. Zie het artikel [Copy data into a dedicated SQL pool using Apache Spark (Gegevens kopiëren naar een toegewezen SQL-pool](../synapse-analytics/synapse-link/how-to-copy-to-sql-pool.md) met behulp Apache Spark artikel).


## <a name="pricing"></a>Prijzen

Het factureringsmodel van Azure Synapse Link bevat de kosten die worden gemaakt met behulp van Azure Cosmos DB analytische opslag en de Synapse-runtime. Zie de artikelen prijzen voor [analytische Azure Cosmos DB en](analytical-store-introduction.md#analytical-store-pricing) Azure Synapse Analytics voor [meer](https://azure.microsoft.com/pricing/details/synapse-analytics/) informatie.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende documenten voor meer informatie:

* [Azure Cosmos DB analytische opslag](analytical-store-introduction.md)

* [Aan de slag met Azure Synapse Link voor Azure Cosmos DB](configure-synapse-link.md)
 
* [Wat wordt er ondersteund in Azure Synapse Analytics-runtime?](../synapse-analytics/synapse-link/concept-synapse-link-cosmos-db-support.md)

* [Veelgestelde vragen over Azure Synapse Link voor Azure Cosmos DB](synapse-link-frequently-asked-questions.md)

* [Use cases voor Azure Synapse Link voor Azure Cosmos DB](synapse-link-use-cases.md)
