---
title: Realtime analyse-use cases met Azure Synapse link voor Azure Cosmos DB
description: Meer informatie over hoe de koppeling van Azure Synapse voor Azure Cosmos DB wordt gebruikt in Supply Chain Analytics, prognose, rapportage, realtime personalisatie en IOT predictief onderhoud.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: ramkris
ms.custom: synapse-cosmos-db
ms.openlocfilehash: 253257564ffc96108f282d7de3dbd9f5e8ce68fd
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869851"
---
# <a name="azure-synapse-link-for-azure-cosmos-db-near-real-time-analytics-use-cases"></a>Azure Synapse Link voor Azure Cosmos DB: Toepassingsscenario’s voor bijna realtime-analyse
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

De [Azure Synapse-koppeling](synapse-link.md) voor Azure Cosmos DB is een native HTAP-functie (hybride transactionele) van de Cloud waarmee u bijna realtime analyses kunt uitvoeren via operationele gegevens. Synapse-koppeling maakt een strakkere integratie tussen Azure Cosmos DB en Azure Synapse Analytics.

Het kan zijn dat u wilt weten welke industrie use-cases gebruikmaken van deze Cloud systeem eigen HTAP-mogelijkheid voor bijna realtime analyse over operationele gegevens. Hier volgen drie veelvoorkomende use cases voor Azure Synapse link for Azure Cosmos DB:

* Supply Chain Analytics, prognose van & rapportage
* Real-time personalisatie
* Voor speld onderhoud, afwijkings detectie in IOT-scenario's

> [!NOTE]
> De Azure Synapse-koppeling voor Azure Cosmos DB streeft naar het scenario waarin bedrijfs teams bijna realtime analyses willen uitvoeren. Deze analyses worden uitgevoerd zonder ETL over operationele gegevens die zijn gegenereerd voor transactionele toepassingen die zijn gebouwd op Azure Cosmos DB. Dit vervangt niet de nood zaak van een afzonderlijk Data Warehouse wanneer er traditionele vereisten voor het Data Warehouse zijn, zoals het beheer van de werk belasting, het samen voegen van een hoge gelijktijdigheid, persistentie aggregatie over meerdere gegevens bronnen.

## <a name="supply-chain-analytics-forecasting--reporting"></a>Supply Chain Analytics, prognose van & rapportage

Onderzoek onderzoek laat zien dat het insluiten van big data Analytics in toeleverings ketens leidt tot verbeteringen in de lever tijden en de efficiëntie van de toeleverings keten.

Fabrikanten doen de voor bereiding op Cloud-systeem eigen technologieën om te voor komen dat de beperkingen van verouderde ERP-(Enter prise resource planning) en SCM-systemen (Supply Chain Management) worden onderbroken. Met toeleverings ketens die elke minuut steeds grotere hoeveel heden operationele gegevens genereren (order, verzen ding, transactie gegevens), hebben fabrikanten een operationele data base nodig. Deze operationele data base moet worden geschaald om de gegevens volumes en een analytisch platform te kunnen afhandelen om een niveau van real-time contextuele intelligentie te krijgen om de curve voor te blijven.

De volgende architectuur toont de kracht van het gebruik van Azure Cosmos DB als de Cloud-native operationele data base en Synapse-koppeling in Supply Chain Analytics:

:::image type="content" source="./media/synapse-link-use-cases/supply-chain-analytics.png" alt-text="Azure Synapse-koppeling voor Azure Cosmos DB in Supply Chain Analytics " border="false":::

Op basis van de vorige architectuur kunt u de volgende use-cases gebruiken met Synapse link voor Azure Cosmos DB:

* **Voorspellende pijp lijn voorbereiden & trainen:** Genereer inzichten met betrekking tot de operationele gegevens in de toeleverings keten met machine learning. Op deze manier kunt u de inventarisatie, de bewerkings kosten verlagen en de order-naar-leverings tijden voor klanten verlagen.

  Met de Synapse-koppeling kunt u de veranderende operationele gegevens in Azure Cosmos DB analyseren zonder hand matige ETL-processen. Het bespaart u van extra kosten, latentie en complexiteit. Met Synapse link kunnen gegevens technici en gegevens wetenschappers robuuste voorspellende pijp lijnen bouwen:

  * Query's uitvoeren op operationele gegevens uit Azure Cosmos DB Analytical Store door gebruik te maken van systeem eigen integratie met Apache Spark Pools in azure Synapse Analytics. U kunt een query uitvoeren op de gegevens in een interactief notitie blok of op geplande externe taken zonder complex data engineering.

  * Bouw Machine Learning modellen (ML) met Spark ML-algoritmen en integratie van Azure ML in azure Synapse Analytics.

  * Schrijf de resultaten terug nadat het model is vertraagd in Azure Cosmos DB voor operationele bijna-realtime-scores.

* **Operationele rapportage:** Toeleverings keten teams hebben flexibele en aangepaste rapporten nodig in realtime, nauw keurige operationele gegevens. Deze rapporten zijn vereist voor het verkrijgen van een momentopname weergave van de effectiviteit, rentabiliteit en productiviteit van de toeleverings keten. Hiermee kunnen analisten en andere belang rijke belanghebbenden het bedrijf voortdurend opnieuw evalueren en gebieden identificeren die moeten worden afgesteld om de operationele kosten te verlagen. 

  Synapse link voor Azure Cosmos DB maakt geavanceerde business intelligence (BI)/Reporting-scenario's mogelijk:

  * Query's uitvoeren op operationele gegevens uit Azure Cosmos DB Analytical Store door gebruik te maken van systeem eigen integratie met serverloze SQL-pool en een volledige uitdrukking van de T-SQL-taal.

  * Model en publiceer automatisch vernieuwen van BI-Dash boards over Azure Cosmos DB via serverloze ondersteuning van SQL-groepen voor bekende BI-hulpprogram ma's. Bijvoorbeeld Azure Analysis Services, Power BI Premium, enzovoort.

Hier volgen enkele richt lijnen voor gegevens integratie voor het uitvoeren van batch-& streaminggegevens in Azure Cosmos DB:

* **Integratie van batch gegevens & indeling:** Met toeleverings ketens kunnen gegevens platformen voor toeleverings ketens complexer worden geïntegreerd met verschillende gegevens bronnen en-indelingen. Azure Synapse is ingebouwd met dezelfde gegevens integratie-engine en ervaring als Azure Data Factory. Met deze integratie kunnen gegevens technici uitgebreide gegevens pijplijnen maken zonder een afzonderlijke Orchestration-Engine:

  * Verplaats gegevens van 85 + ondersteunde gegevens bronnen naar [Azure Cosmos DB met Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

  * Schrijf de gratis ETL-pijp lijnen van de code naar Azure Cosmos DB, waaronder [relationele hiërarchische en hiërarchische toewijzingen met toewijzing van gegevens stromen](../data-factory/how-to-sqldb-to-cosmosdb.md).

* **Verwerking van streaming-gegevens &:** Met de groei van industriële IoT (Sens oren die de activa volgen van ' floor-to-Store ', verbonden logistiek-vloots, enzovoort), is er een explosie van realtime-gegevens die worden gegenereerd op het gebied van streaming die moeten worden geïntegreerd met de traditionele, trage verplaatsing van gegevens voor het genereren van inzichten. Azure Stream Analytics is een aanbevolen Service voor het streamen van ETL en verwerking op Azure met een [breed scala aan scenario's](../stream-analytics/streaming-technologies.md). Azure Stream Analytics ondersteunt [Azure Cosmos DB als een systeem eigen gegevens Sink](../stream-analytics/stream-analytics-documentdb-output.md).

## <a name="real-time-personalization"></a>Real-time personalisatie

De detail handelaren moeten vandaag veilige en schaal bare e-commerce-oplossingen bouwen die voldoen aan de vereisten van klanten en bedrijven. Deze e-commerce-oplossingen moeten klanten betrekken via aangepaste producten en aanbiedingen, trans acties snel en veilig verwerken en zich richten op de uitvoering en de klanten service. Azure Cosmos DB samen met de meest recente Synapse-koppeling voor Azure Cosmos DB kunnen detail handelaren in realtime gepersonaliseerde aanbevelingen voor klanten genereren. Ze gebruiken consistentie-instellingen met lage latentie en instel bare voor direct inzicht, zoals wordt weer gegeven in de volgende architectuur:

:::image type="content" source="./media/synapse-link-use-cases/real-time-personalization.png" alt-text="Azure Synapse-koppeling voor Azure Cosmos DB in realtime personalisatie" border="false":::

Synapse-koppeling voor Azure Cosmos DB use-case:

* **Voorspellende pijp lijn voorbereiden & trainen:** U kunt inzichten genereren over de operationele gegevens in uw bedrijfs eenheden of klant segmenten met behulp van Synapse Spark-en machine learning-modellen. Dit wordt omgezet in een persoonlijke levering aan doel klant segmenten, voorspellende ervaringen van eind gebruikers en gerichte marketing voor uw eindgebruikers vereisten.

## <a name="iot-predictive-maintenance"></a>IOT-voor speld onderhoud

Industriële IOT-innovaties hebben drastische gereduceerde downtime van machines en de algehele efficiëntie van alle sectoren. Een van deze innovaties is voorspellende onderhouds analyses voor machines aan de rand van de Cloud.

Hier volgt een architectuur die gebruikmaakt van de HTAP-mogelijkheden in de cloud van Azure Synapse-koppeling voor Azure Cosmos DB in IoT-voorspellende onderhoud:

:::image type="content" source="./media/synapse-link-use-cases/iot-predictive-maintenance.png" alt-text="Azure Synapse-koppeling voor Azure Cosmos DB in IOT-voorspellend onderhoud" border="false" :::

Synapse-koppeling voor Azure Cosmos DB use cases:

* **Voorspellende pijp lijn voorbereiden & trainen:** De historische operationele gegevens van IoT-apparaat-Sens oren kunnen worden gebruikt om voorspellende modellen, zoals afwijkende Detectors, te trainen. Deze afwijkingsdetectoren worden vervolgens weer op de rand geïnstalleerd voor realtime-bewaking. Met een dergelijke waardevolle gebruiks-lus kan de voorspellende modellen continu worden getraind.

* **Operationele rapportage:** Met de groei van digitale dubbele initiatieven verzamelen bedrijven grote hoeveel heden operationele gegevens van een groot aantal Sens oren om een digitale kopie van elke machine te bouwen. Deze gegevens bevoegdheden BI moet inzicht hebben in trends op historische gegevens en in realtime toepassingen via recente, dynamische gegevens.

## <a name="sample-scenario-htap-for-azure-cosmos-db"></a>Voorbeeld scenario: HTAP voor Azure Cosmos DB

Azure Cosmos DB is gedurende een periode van bijna een jaar gebruikt door duizenden klanten voor essentiële toepassingen waarvoor elastische schaling is vereist, kant-en-klare, multi-regio write-replicatie voor lage latentie en hoge Beschik baarheid van Lees & schrijf bewerkingen in hun transactionele werk belastingen.
 
De volgende lijst bevat een overzicht van de verschillende werkbelasting patronen die worden ondersteund met operationele gegevens met behulp van Azure Cosmos DB:

* Real-time apps & Services
* Verwerken van gebeurtenisstromen
* BI-Dash boards
* Big data-analyse
* Machine learning

Met de koppeling Azure Synapse kunnen Azure Cosmos DB niet alleen Power transactionele werk belastingen, maar ook bijna realtime analytische werk belastingen uitvoeren op historische operationele gegevens. Dit gebeurt zonder ETL-vereisten en gegarandeerde prestatie isolatie van de transactionele werk belastingen.

De volgende afbeelding toont de werkbelasting patronen met behulp van Azure Cosmos DB: :::image type="content" source="./media/synapse-link-use-cases/synapse-link-workload-patterns.png" alt-text="Azure Synapse-koppeling voor Azure Cosmos DB werkbelasting patronen" border="false":::

Laat ons het voor beeld van een e-commerce-bedrijf CompanyXYZ met wereld wijde bewerkingen in 20 landen/regio's om de voor delen te illustreren van het kiezen van Azure Cosmos DB als de single realtime-data base, zowel transactionele als analytische vereisten van een platform voor voorraad beheer.

* De kern activiteit van CompanyXYZ is afhankelijk van het voorraadbeheer systeem, dus Beschik baarheid & betrouw baarheid zijn kern vereisten voor de pijler. Voor delen van het gebruik van Azure Cosmos DB:

  * In het kader van diep gaande integratie met Azure-infra structuur en transparante multi-regio schrijf bewerkingen, biedt Azure Cosmos DB wereld wijde replicatie een [hoge Beschik baarheid van 99,999%](high-availability.md) voor regionale uitval.

* De partners van de toeleverings keten van CompanyXYZ kunnen zich op verschillende geografische locaties bevinden, maar het kan ook nodig zijn om een enkele weer gave van de product voorraad over de hele wereld te bekijken om de lokale bewerkingen te ondersteunen. Dit omvat de nood zaak om updates te kunnen lezen die zijn gemaakt door andere partners in de toeleverings keten in real-time. En u kunt updates zonder zorgen te maken over conflicten met andere partners bij een hoge door voer. Voor delen van het gebruik van Azure Cosmos DB:

  * Met zijn unieke multi-regio writes-replicatie protocol en een vergrendelde transactionele opslag ruimte met schrijf optimalisatie, Azure Cosmos DB gegarandeerd minder dan 10 MS-latenties voor zowel geïndexeerde Lees bewerkingen als schrijf bewerkingen in het 99e percentiel wereld wijd.

  * Opname door hoge door Voer van zowel batch-& streaming gegevensfeeds met [realtime-indexering](index-policy.md) in transactionele opslag.

  * Azure Cosmos DB transactionele Store biedt drie meer opties dan de twee extreme, sterkste en uiteindelijke consistentie niveaus om de [Beschik baarheid en prestaties te kunnen afnemen](./consistency-levels.md) die het dichtst bij de bedrijfs behoefte zijn.

* De partners van de toeleverings keten van CompanyXYZ hebben zeer schommelende verkeers patronen van honderden tot miljoenen aanvragen/s en daarom moet het voorraadbeheer platform omgaan met onverwachte burstiness in het verkeer.  Voor delen van het gebruik van Azure Cosmos DB:

  * De transactionele Store van Azure Cosmos DB biedt ondersteuning voor elastische schaal baarheid van opslag en door Voer met horizontale partitionering. Containers en data bases die zijn geconfigureerd in de modus automatisch testen, kunnen de ingerichte door Voer automatisch en direct schalen op basis van de behoeften van de toepassing, zonder dat dit van invloed is op de beschik baarheid, latentie, door Voer of de prestaties van de werk belasting.

* CompanyXYZ moet een veilig Analytics-platform tot stand brengen met de historische inventaris gegevens voor het hele systeem om analyses en inzichten in te scha kelen tussen de toeleverings keten partner, bedrijfs eenheden en functies. Het analyse platform moet samen werking mogelijk maken in het systeem, traditionele BI/rapportage-use cases, geavanceerde analyse-use cases en voorspellende intelligente oplossingen ten opzichte van de operationele inventaris gegevens. Voor delen van het gebruik van de Synapse-koppeling voor Azure Cosmos DB:

  * Met behulp van [Azure Cosmos DB-analytische opslag](analytical-store-introduction.md)kan een volledig geïsoleerde column Store geen ETL-analyse (extract-Transform-Load) uitvoeren in [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md) voor wereld wijd gedistribueerde operationele gegevens op schaal.  Bedrijfs analisten, gegevens technici en gegevens wetenschappers kunnen nu Synapse Spark of Synapse SQL op een interoperabele manier gebruiken om bijna realtime business intelligence, analyses en machine learning pijp lijnen uit te voeren zonder dat dit invloed heeft op de prestaties van hun transactionele werk belastingen op Azure Cosmos DB. Zie de [voor delen van Synapse link in azure Cosmos DB](synapse-link.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende documenten voor meer informatie:

* [Azure Synapse Link voor Azure Cosmos DB](synapse-link.md) 

* [Analytische opslag van Azure Cosmos DB](analytical-store-introduction.md)

* [Werken met de Azure Synapse-koppeling voor Azure Cosmos DB](configure-synapse-link.md)

* [Veelgestelde vragen over Azure Synapse Link voor Azure Cosmos DB](synapse-link-frequently-asked-questions.md)

* [Apache Spark in Azure Synapse Analytics](../synapse-analytics/spark/apache-spark-concepts.md)

* [SQL pool runtime-ondersteuning zonder server in azure Synapse Analytics](../synapse-analytics/sql/on-demand-workspace-overview.md)