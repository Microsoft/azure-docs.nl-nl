---
title: Azure Synapse-koppeling voor Azure Cosmos DB, voor delen en wanneer u deze gebruikt
description: Meer informatie over de koppeling van Azure Synapse voor Azure Cosmos DB. Met Synapse link kunt u bijna realtime analyses (HTAP) uitvoeren met behulp van Azure Synapse Analytics over operationele gegevens in Azure Cosmos DB.
author: Rodrigossz
ms.author: rosouz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/30/2020
ms.reviewer: sngun
ms.custom: synapse-cosmos-db
ms.openlocfilehash: c22a4030bb44c1499995071849ca8942ae2110c3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864343"
---
# <a name="what-is-azure-synapse-link-for-azure-cosmos-db"></a>Wat is Azure Synapse Link voor Azure Cosmos DB?
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

De Azure Synapse-koppeling voor Azure Cosmos DB is een Cloud-native hybride transactionele en analytische verwerking (HTAP) waarmee u bijna realtime analyses kunt uitvoeren via operationele gegevens in Azure Cosmos DB. Met Azure Synapse link maakt u een naadloze integratie tussen Azure Cosmos DB en Azure Synapse Analytics.

Met behulp van [Azure Cosmos DB-analytische opslag](analytical-store-introduction.md), een volledig geïsoleerde column Store, kunt u met Azure Synapse link geen ETL-analyse (extract-Transform-Load) uitvoeren in [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md) voor uw operationele gegevens op schaal. Bedrijfs analisten, gegevens technici en gegevens wetenschappers kunnen nu Synapse Spark of Synapse SQL-verwisselbaar gebruiken om bijna realtime business intelligence, analyses en machine learning pijp lijnen uit te voeren. U kunt dit doen zonder dat dit van invloed is op de prestaties van uw transactionele werkbelastingen op Azure Cosmos DB. 

In de volgende afbeelding ziet u de integratie van de Azure Synapse-koppeling met Azure Cosmos DB en Azure Synapse Analytics: 

:::image type="content" source="./media/synapse-link/synapse-analytics-cosmos-db-architecture.png" alt-text="Architectuur diagram voor Azure Synapse Analytics-integratie met Azure Cosmos DB" border="false":::

## <a name="benefits"></a><a id="synapse-link-benefits"></a> Verleend

Voor het analyseren van grote operationele gegevens sets en het minimaliseren van de impact op de prestaties van bedrijfskritische transactionele werk belastingen, zijn de operationele gegevens in Azure Cosmos DB echter geëxtraheerd en verwerkt door een ETL-pijp lijn (extract-Transform-Load). ETL-pijp lijnen vereisen veel lagen van gegevens verplaatsing, wat resulteert in veel operationele complexiteit, en invloed heeft op de prestaties van uw transactionele werk belastingen. Daarnaast neemt de latentie toe om de operationele gegevens te analyseren vanaf het tijdstip van de oorsprong.

In vergelijking met de traditionele op ETL gebaseerde oplossingen biedt Azure Synapse link voor Azure Cosmos DB verschillende voor delen, zoals:  

### <a name="reduced-complexity-with-no-etl-jobs-to-manage"></a>Minder complexiteit zonder ETL-taken om te beheren

Met Azure Synapse link kunt u rechtstreeks toegang krijgen tot Azure Cosmos DB Analytical Store met behulp van Azure Synapse Analytics zonder complexe gegevens verplaatsing. Alle updates die zijn aangebracht in de operationele gegevens, zijn in bijna realtime zichtbaar in het analytische archief zonder ETL of het wijzigen van invoer taken. U kunt grootschalige analyses uitvoeren op een analytische opslag locatie, vanuit Azure Synapse Analytics, zonder extra gegevens transformatie.

### <a name="near-real-time-insights-into-your-operational-data"></a>Bijna realtime inzichten in uw operationele gegevens

U kunt nu in bijna realtime inzicht krijgen in uw operationele gegevens met behulp van de Azure Synapse-koppeling. ETL-gebaseerde systemen hebben vaak een hogere latentie voor het analyseren van uw operationele gegevens, omdat er veel lagen nodig zijn om de operationele gegevens te extra heren, te transformeren en te laden. Met de systeem eigen integratie van Azure Cosmos DB analytische opslag met Azure Synapse Analytics kunt u operationele gegevens in bijna realtime analyseren door nieuwe bedrijfs scenario's in te scha kelen. 

### <a name="no-impact-on-operational-workloads"></a>Geen invloed op operationele werk belastingen

Met de koppeling Azure Synapse kunt u analytische query's uitvoeren op een Azure Cosmos DB analytische archief (een afzonderlijk kolom archief) terwijl de transactionele bewerkingen worden verwerkt met behulp van ingerichte door Voer voor de transactionele werk belasting (een op rijen gebaseerd transactioneel archief).  De analytische werk belasting wordt onafhankelijk van het transactionele werkbelasting verkeer bediend zonder gebruik te maken van de door Voer die is ingericht voor uw operationele gegevens.

### <a name="optimized-for-large-scale-analytics-workloads"></a>Geoptimaliseerd voor grootschalige analytische werk belastingen

Azure Cosmos DB-analytische opslag is geoptimaliseerd voor schaal baarheid, elasticiteit en prestaties voor analytische werk belastingen zonder enige afhankelijkheid op de reken tijden. De opslag technologie wordt zelf beheerd om uw analyse werkbelastingen te optimaliseren. Met ingebouwde ondersteuning in azure Synapse Analytics kunt u toegang krijgen tot deze opslag laag, eenvoud en hoge prestaties.

### <a name="cost-effective"></a>Rendabel

Met de koppeling Azure Synapse kunt u een door de kosten geoptimaliseerde, volledig beheerde oplossing voor operationele analyses krijgen. Het elimineert de extra opslag lagen en de reken kracht die vereist is in traditionele ETL-pijp lijnen voor het analyseren van operationele gegevens. 

Azure Cosmos DB Analytical Store volgt een prijs model op basis van verbruik, dat is gebaseerd op gegevens opslag en analytische Lees-en schrijf bewerkingen en query's die worden uitgevoerd. U hoeft geen door Voer in te richten, zoals u dat wel doet voor de transactionele werk belastingen. Als u toegang hebt tot uw gegevens met zeer elastische Compute-engines van Azure Synapse Analytics, worden de totale kosten voor het uitvoeren van opslag en de berekening zeer efficiënt berekend.


### <a name="analytics-for-locally-available-globally-distributed-multi-region-writes"></a>Analyse voor lokaal beschik bare, wereld wijd gedistribueerde, multi-regio schrijf bewerkingen

U kunt analytische query's effectief uitvoeren met de dichtstbijzijnde regionale kopie van de gegevens in Azure Cosmos DB. Azure Cosmos DB voorziet in geavanceerde mogelijkheden voor het uitvoeren van de wereld wijd gedistribueerde analytische werk belastingen samen met transactionele werk belastingen op een actief/actief manier.

## <a name="enable-htap-scenarios-for-your-operational-data"></a>HTAP-scenario's inschakelen voor uw operationele gegevens

Synapse-koppeling brengt Azure Cosmos DB analytische opslag samen met ondersteuning voor Azure Synapse Analytics runtime. Met deze integratie kunt u native HTAP (hybride transactionele/Analytical Processing)-oplossingen bouwen die inzichten genereren op basis van realtime-updates voor uw operationele gegevens via grote gegevens sets. Er worden nieuwe bedrijfs scenario's ontgrendeld voor het genereren van waarschuwingen op basis van Live trends, het bouwen van bijna realtime Dash boards en zakelijke ervaringen op basis van het gedrag van de gebruiker.

### <a name="azure-cosmos-db-analytical-store"></a>Azure Cosmos DB-analytische archief

Azure Cosmos DB Analytical Store is een kolom gerichte weer gave van uw operationele gegevens in Azure Cosmos DB. Dit analytische archief is geschikt voor snelle, rendabele query's voor grote operationele gegevens sets, zonder gegevens te kopiëren en de prestaties van uw transactionele werk belastingen te beïnvloeden.

Met de analytische opslag worden automatisch hoge frequentie toevoegingen, updates, verwijderingen in uw transactionele workloads in bijna realtime verzameld, als volledig beheerde mogelijkheid ("automatische synchronisatie") van Azure Cosmos DB. Geen wijzigings feed of ETL is vereist. 

Als u een wereld wijd gedistribueerd Azure Cosmos DB account hebt, is het beschikbaar in alle regio's voor dat account nadat u het analytische Archief voor een container hebt ingeschakeld. Zie [Azure Cosmos DB Analytical Store Overview](analytical-store-introduction.md) voor meer informatie over de analytische opslag.

### <a name="integration-with-azure-synapse-analytics"></a><a id="synapse-link-integration"></a>Integratie met Azure Synapse Analytics

Met de Synapse-koppeling kunt u nu rechtstreeks verbinding maken met uw Azure Cosmos DB containers vanuit Azure Synapse Analytics en toegang krijgen tot de analytische Store zonder afzonderlijke connectors. Azure Synapse Analytics biedt momenteel ondersteuning voor Synapse-koppeling met [Synapse-Apache Spark](../synapse-analytics/spark/apache-spark-concepts.md) en [serverloze SQL-groep](../synapse-analytics/sql/on-demand-workspace-overview.md).

U kunt de gegevens van Azure Cosmos DB analytische opslag tegelijk opvragen, met Interop over verschillende runtime-uitvoerings tijden die worden ondersteund door Azure Synapse Analytics. Er zijn geen aanvullende gegevens transformaties vereist om de operationele gegevens te analyseren. U kunt de gegevens van het analytische archief opvragen en analyseren met:

* Synapse Apache Spark met volledige ondersteuning voor scala, Python, SparkSQL en C#. Synapse Spark is van toepassing op data engineering-en data Science-scenario's

* Een serverloze SQL-pool met T-SQL-taal en ondersteuning voor bekende BI-hulpprogram ma's (bijvoorbeeld Power BI Premium, enzovoort)

> [!NOTE]
> Vanuit Azure Synapse Analytics hebt u toegang tot zowel analytische als transactionele winkels in uw Azure Cosmos DB-container. Als u echter grootschalige analyses of scans wilt uitvoeren op uw operationele gegevens, raden we u aan om analytische opslag te gebruiken om de prestaties van transactionele werk belastingen te voor komen.

> [!NOTE]
> U kunt analyses met lage latentie uitvoeren in een Azure-regio door uw Azure Cosmos DB-container te koppelen aan Synapse runtime in die regio.

Deze integratie maakt de volgende HTAP-scenario's mogelijk voor verschillende gebruikers:

* Een BI-engineer die een Power BI-rapport wil model leren en publiceren om de operationele gegevens in Azure Cosmos DB rechtstreeks te openen via Synapse SQL.

* Een gegevens analist die inzichten wil afleiden van de operationele gegevens in een Azure Cosmos DB-container door deze te doorzoeken met Synapse SQL, de gegevens te lezen op schaal en deze resultaten te combi neren met andere gegevens bronnen.

* Een gegevens wetenschapper die Synapse Spark wil gebruiken om een functie te vinden om het model te verbeteren en het model te trainen zonder ingewikkelde data engineering uit te voeren. Ze kunnen ook de resultaten van het model naar Azure Cosmos DB schrijven voor realtime scoreing op de gegevens via Spark Synapse.

* Een gegevens technicus die gegevens toegankelijk wil maken voor consumenten door SQL-of Spark-tabellen te maken via Azure Cosmos DB containers zonder hand matige ETL-processen.

Zie [Azure Synapse Analytics for Cosmos DB-ondersteuning](../synapse-analytics/synapse-link/concept-synapse-link-cosmos-db-support.md)voor meer informatie over Azure Synapse Analytics runtime-ondersteuning voor Azure Cosmos db.

## <a name="security"></a>Beveiliging

Met Synapse link kunt u bijna realtime analyses uitvoeren over uw essentiële gegevens in Azure Cosmos DB. Het is essentieel om ervoor te zorgen dat kritieke Bedrijfs gegevens veilig worden opgeslagen in zowel transactionele als analytische archieven. De koppeling van Azure Synapse voor Azure Cosmos DB is ontworpen om aan deze beveiligings vereisten te voldoen met de volgende functies:

* **Netwerk isolatie met behulp van privé-eind punten** : u kunt de netwerk toegang tot de gegevens in de transactionele en analytische archieven onafhankelijk beheren. Netwerk isolatie wordt uitgevoerd met behulp van afzonderlijke beheerde persoonlijke eind punten voor elke opslag plaats in beheerde virtuele netwerken in azure Synapse-werk ruimten. Zie voor meer informatie over het [configureren van privé-eind punten voor een analytisch archief](analytical-store-private-endpoints.md) -artikel.

* **Gegevens versleuteling met door de klant beheerde sleutels** : u kunt de gegevens in transactionele en analytische archieven naadloos versleutelen met behulp van dezelfde door de klant beheerde sleutels op een automatische en transparante manier. Zie het artikel door de [klant beheerde sleutels configureren](how-to-setup-cmk.md) voor meer informatie.

* **Beveiligd sleutel beheer** : voor toegang tot de gegevens in de analytische opslag van Synapse Spark-en Synapse SERVERloze SQL-groepen is het beheren van Azure Cosmos DB sleutels in Synapse Analytics-werk ruimten vereist. In plaats van de Azure Cosmos DB account sleutels inline te gebruiken in Spark-taken of SQL-scripts, biedt de koppeling van Azure Synapse meer veilige mogelijkheden.

  * Wanneer u Synapse serverloze SQL-groepen gebruikt, kunt u een query uitvoeren op de Azure Cosmos DB Analytical Store door de SQL-referenties vooraf te maken, waarbij u de account sleutels opslaat en ernaar verwijst in de `OPENROWSET` functie. Zie voor meer informatie [query uitvoeren met een serverloze SQL-groep in azure Synapse link](../synapse-analytics/sql/query-cosmos-db-analytical-store.md) -artikel.

  * Wanneer u Synapse Spark gebruikt, kunt u de account sleutels in gekoppelde service objecten opslaan die naar een Azure Cosmos DB-Data Base verwijzen en hiernaar verwijzen in de Spark-configuratie tijdens runtime. Zie [gegevens kopiëren naar een speciale SQL-groep met behulp van Apache Spark](../synapse-analytics/synapse-link/how-to-copy-to-sql-pool.md) artikel voor meer informatie.

## <a name="when-to-use-azure-synapse-link-for-azure-cosmos-db"></a>Wanneer gebruik ik de Azure Synapse-koppeling voor Azure Cosmos DB?

De koppeling Synapse wordt aanbevolen in de volgende gevallen:

* Als u een Azure Cosmos DB klant bent en u Analytics, BI en machine learning wilt uitvoeren over uw operationele gegevens. In dergelijke gevallen biedt Synapse link een meer geïntegreerde analyse-ervaring zonder dat dit van invloed is op de ingerichte door Voer van uw transactionele opslag. Bijvoorbeeld:

  * Als u Analytics of BI uitvoert op uw Azure Cosmos DB operationele gegevens rechtstreeks met afzonderlijke Connect oren, of

  * Als u ETL-processen uitvoert om operationele gegevens in een afzonderlijk analyse systeem uit te pakken.
 
In dergelijke gevallen biedt Synapse link een meer geïntegreerde analyse-ervaring zonder dat dit van invloed is op de ingerichte door Voer van uw transactionele opslag.

Synapse-koppeling wordt niet aanbevolen als u op zoek bent naar traditionele Data Warehouse vereisten, zoals een hoge gelijktijdigheid, werkbelasting beheer en persistentie van aggregaties over meerdere gegevens bronnen. Zie [algemene scenario's die kunnen worden ingeschakeld met de Azure Synapse-koppeling voor Azure Cosmos DB](synapse-link-use-cases.md)voor meer informatie.

## <a name="limitations"></a>Beperkingen

* Azure Synapse Link voor Azure Cosmos DB wordt ondersteund voor SQL API en Azure Cosmos DB voor MongoDB-API. Het wordt niet ondersteund voor Gremlin-API, Cassandra-API en Table-API.

* Analytische opslag kan alleen worden ingeschakeld voor nieuwe containers. Als u een analytische archief wilt gebruiken voor bestaande containers, migreert u gegevens vanuit uw bestaande containers naar nieuwe containers met [Azure Cosmos DB-migratie hulpprogramma's](cosmosdb-migrationchoices.md). U kunt de Synapse-koppeling inschakelen voor nieuwe en bestaande Azure Cosmos DB accounts.

* Voor de containers waarop het analytische archief is ingeschakeld, wordt automatische back-up en herstel van uw gegevens in de analytische opslag op dit moment niet ondersteund. Wanneer de Synapse-koppeling is ingeschakeld voor een database account, blijven Azure Cosmos DB automatisch [back-ups](./online-backup-and-restore.md) van uw gegevens in het transactionele archief (alleen) van containers maken op het geplande back-upinterval, zoals altijd. Het is belang rijk te weten dat wanneer een container waarop een analytische opslag is ingeschakeld, wordt hersteld naar een nieuw account, de container wordt hersteld met alleen transactionele opslag en er geen analytische opslag is ingeschakeld.

* Het is momenteel niet mogelijk om toegang te krijgen tot de Azure Cosmos DB Analytics Store met Synapse SQL provisioned.

## <a name="pricing"></a>Prijzen

Het facturerings model van de koppeling Azure Synapse bevat de kosten die zijn gemaakt met behulp van het Azure Cosmos DB-analytische archief en de Synapse-runtime. Voor meer informatie raadpleegt u de artikelen over prijzen van [Azure Cosmos DB Analytical Store](analytical-store-introduction.md#analytical-store-pricing) en [prijzen voor Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/synapse-analytics/) .

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende documenten voor meer informatie:

* [Overzicht van Azure Cosmos DB Analytical Store](analytical-store-introduction.md)

* [Aan de slag met Azure Synapse Link voor Azure Cosmos DB](configure-synapse-link.md)
 
* [Wat wordt er ondersteund in Azure Synapse Analytics-runtime?](../synapse-analytics/synapse-link/concept-synapse-link-cosmos-db-support.md)

* [Veelgestelde vragen over Azure Synapse Link voor Azure Cosmos DB](synapse-link-frequently-asked-questions.md)

* [Use cases voor Azure Synapse Link voor Azure Cosmos DB](synapse-link-use-cases.md)
