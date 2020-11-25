---
title: Veelgestelde vragen over de Cassandra-API voor Azure Cosmos DB
description: Krijg antwoorden op veelgestelde vragen over de Cassandra-API voor Azure Cosmos DB.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 08/12/2020
ms.author: thvankra
ms.openlocfilehash: 1368a3174af08f557b6d08f298fba015601d568c
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96030829"
---
# <a name="frequently-asked-questions-about-the-cassandra-api-in-azure-cosmos-db"></a>Veelgestelde vragen over de Cassandra-API in Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

In dit artikel worden de functionaliteits verschillen tussen Apache Cassandra en Cassandra-API in Azure Cosmos DB beschreven. Ook vindt u hier antwoorden op veelgestelde vragen over de Cassandra-API in Azure Cosmos DB.

## <a name="key-differences-between-apache-cassandra-and-the-cassandra-api"></a>De belangrijkste verschillen tussen Apache Cassandra en de Cassandra-API

- Apache Cassandra raadt een limiet van 100 MB aan voor de grootte van een partitie sleutel. De Cassandra-API voor Azure Cosmos DB mag Maxi maal 20 GB per partitie bevatten.
- Met Apache Cassandra kunt u duurzame door voeringen uitschakelen. U kunt de schrijf bewerkingen naar het commit-logboek overs Laan en direct naar de memtables gaan. Dit kan leiden tot verlies van gegevens als het knoop punt uitvalt voordat memtables naar SSTables op schijf worden leeg gemaakt. Azure Cosmos DB bevat altijd duurzame door voeringen om gegevens verlies te voor komen.
- Apache Cassandra kan afnemende prestaties zien als de werk belasting veel vervangingen of verwijderingen omvat. De reden is tombstones dat de leeswerk belasting moet overs Laan om de meest recente gegevens op te halen. Het Cassandra-API ziet geen afnemende Lees prestaties wanneer de werk belasting veel vervangingen of verwijderingen heeft.
- Tijdens scenario's met hoge vervangen werk belastingen moet compressie worden uitgevoerd om SSTables op schijf samen te voegen. (Een samen voeging is vereist omdat de schrijf bewerkingen van Apache Cassandra alleen worden toegevoegd. Meerdere updates worden opgeslagen als afzonderlijke SSTable-vermeldingen die periodiek moeten worden samengevoegd. Deze situatie kan ook leiden tot minder Lees prestaties tijdens het comprimeren. Deze prestatie-impact treedt niet op in de Cassandra-API omdat de API geen compressie implementeert.
- Het instellen van een replicatie factor van 1 is mogelijk met Apache Cassandra. Het leidt echter tot een geringe Beschik baarheid als het enige knoop punt met de gegevens uitvalt. Dit is geen probleem met de Cassandra-API voor Azure Cosmos DB omdat er altijd een replicatie factor van 4 (quorum van 3) is.
- Het toevoegen of verwijderen van knoop punten in Apache Cassandra vereist hand matige interventie, samen met een hoog CPU-gebruik op het nieuwe knoop punt, terwijl bestaande knoop punten een aantal van hun token bereik naar het nieuwe knoop punt verplaatsen. Deze situatie is hetzelfde wanneer u een bestaand knoop punt buiten gebruik stelt. De Cassandra-API kan echter worden geschaald zonder problemen die in de service of toepassing zijn waargenomen.
- Het is niet nodig om **num_tokens** in te stellen op elk knoop punt in het cluster als Apache Cassandra. Met Azure Cosmos DB worden knoop punten en Token reeksen volledig beheerd.
- De Cassandra-API wordt volledig beheerd. U hebt de **nodetool** -opdrachten, zoals herstellen en buiten gebruik, niet nodig die worden gebruikt in Apache Cassandra.

## <a name="other-frequently-asked-questions"></a>Andere veelgestelde vragen

### <a name="what-protocol-version-does-the-cassandra-api-support"></a>Welke Protocol versie ondersteunt de Cassandra-API?

De Cassandra-API voor Azure Cosmos DB ondersteunt CQL versie 3. x. De compatibiliteit van het CQL is gebaseerd op de open bare [Apache Cassandra github-opslag plaats](https://github.com/apache/cassandra/blob/trunk/doc/cql3/CQL.textile). Als u feedback hebt over de ondersteuning van andere protocollen, laat u ons weten via [feedback van gebruikers spraak](https://feedback.azure.com/forums/263030-azure-cosmos-db) of het verzenden van e-mail naar [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) .

### <a name="why-is-choosing-throughput-for-a-table-a-requirement"></a>Waarom wordt de door Voer voor een vereiste voor een tabel gekozen?

Azure Cosmos DB stelt de standaard doorvoer voor uw container in op basis van waar u de tabel maakt vanuit: Azure Portal of CQL.

Azure Cosmos DB biedt garanties voor prestaties en latentie, met een bovengrens voor bewerkingen. Deze garanties zijn mogelijk wanneer de engine governance kan afdwingen voor de bewerkingen van de Tenant. Door de door Voer in te stellen, zorgt u ervoor dat u de gegarandeerde door Voer en latentie krijgt, omdat het platform deze capaciteit reserveert en de bewerking is geslaagd.
U kunt de [door Voer elastisch wijzigen](manage-scale-cassandra.md) om te profiteren van de seizoensgebondenheid van uw toepassing en kosten besparingen.

Het concept van de door Voer wordt uitgelegd in de [aanvraag eenheden in azure Cosmos DB](request-units.md) artikel. De door Voer voor een tabel wordt gelijkmatig verdeeld over de onderliggende fysieke partities.

### <a name="what-is-the-throughput-of-a-table-thats-created-through-cql"></a>Wat is de door Voer van een tabel die is gemaakt via CQL?

Azure Cosmos DB gebruikt aanvraag eenheden per seconde (RU/s) als een valuta voor het leveren van door voer. Tabellen die zijn gemaakt via CQL hebben standaard 400 RU. U kunt de RU van de Azure Portal wijzigen.

CQL

```shell
CREATE TABLE keyspaceName.tablename (user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=1200
```

.NET

```csharp
int provisionedThroughput = 400;
var simpleStatement = new SimpleStatement($"CREATE TABLE {keyspaceName}.{tableName} (user_id int PRIMARY KEY, lastname text)");
var outgoingPayload = new Dictionary<string, byte[]>();
outgoingPayload["cosmosdb_provisioned_throughput"] = Encoding.UTF8.GetBytes(provisionedThroughput.ToString());
simpleStatement.SetOutgoingPayload(outgoingPayload);
```

### <a name="what-happens-when-throughput-is-used-up"></a>Wat gebeurt er wanneer de door Voer wordt gebruikt?

Azure Cosmos DB biedt garanties voor prestaties en latentie, met een bovengrens voor bewerkingen. Deze garanties zijn mogelijk wanneer de engine governance kan afdwingen voor de bewerkingen van de Tenant. Door de door Voer in te stellen, zorgt u ervoor dat u de gegarandeerde door Voer en latentie krijgt, omdat het platform deze capaciteit reserveert en de bewerking is geslaagd.

Wanneer u deze capaciteit overschrijdt, wordt het volgende fout bericht weer gegeven dat aangeeft dat uw capaciteit is gebruikt:

**0x1001 overbelast: de aanvraag kan niet worden verwerkt omdat de aanvraag frequentie groot is** 

Het is essentieel om te zien welke bewerkingen (en welk volume) dit probleem veroorzaken. U kunt een idee krijgen over verbruikte capaciteit over de ingerichte capaciteit met metrische gegevens op de Azure Portal. U moet er vervolgens voor zorgen dat de capaciteit bijna gelijkmatig over alle onderliggende partities wordt verbruikt. Als u ziet dat de ene partitie de meeste door Voer gebruikt, hebt u de werk belasting scheef.

Er zijn metrische gegevens beschikbaar die laten zien hoe de door Voer wordt gebruikt in plaats van uren, meer dan dagen en per zeven dagen, verdeeld over partities of aggregatie. Zie voor meer informatie [bewaking en fout opsporing met metrische gegevens in azure Cosmos DB](use-metrics.md).

Diagnostische logboeken worden beschreven in het artikel [Azure Cosmos DB diagnostische logboek registratie](./monitor-cosmos-db.md) .

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>Is de primaire sleutel toegewezen aan het partitie sleutel concept van Azure Cosmos DB?

Ja, de partitie sleutel wordt gebruikt om de entiteit op de juiste locatie te plaatsen. In Azure Cosmos DB wordt dit gebruikt om de juiste logische partitie te vinden die is opgeslagen op een fysieke partitie. Het schema voor partitionering wordt duidelijk uitgelegd in de [partitie en schaal in azure Cosmos DB](partitioning-overview.md) -artikel. De essentiële maakt hier is dat een logische partitie de limiet van 20 GB niet mag overschrijden.

### <a name="what-happens-when-i-get-a-notification-that-a-partition-is-full"></a>Wat gebeurt er wanneer ik een melding ontvang dat een partitie vol is?

Azure Cosmos DB is een systeem op basis van de Service Level Agreement (SLA). Het biedt onbeperkte schaal mogelijkheden, met garanties voor latentie, door Voer, Beschik baarheid en consistentie. Deze onbeperkte opslag is gebaseerd op horizontale schaal bare gegevens, met behulp van partitionering als het belangrijkste concept. Het schema voor partitionering wordt duidelijk uitgelegd in de [partitie en schaal in azure Cosmos DB](partitioning-overview.md) -artikel.

U moet rekening houden met de limiet van 20 GB voor het aantal entiteiten of items per logische partitie. Om ervoor te zorgen dat uw toepassing goed wordt geschaald, wordt u aangeraden *geen* Hot-partitie te maken door alle gegevens in de ene partitie op te slaan en er query's op uit te voeren. Deze fout kan alleen worden opgehaald als uw gegevens worden schuingetrokken: dat wil zeggen dat u veel gegevens voor één partitie sleutel (meer dan 20 GB) hebt. U kunt de distributie van gegevens vinden met behulp van de opslag Portal. De manier om deze fout op te lossen is het opnieuw maken van de tabel en het kiezen van een gedetailleerde, primaire (partitie sleutel), zodat de gegevens beter kunnen worden gedistribueerd.

### <a name="can-i-use-the-cassandra-api-as-a-key-value-store-with-millions-or-billions-of-partition-keys"></a>Kan ik de Cassandra-API gebruiken als sleutel waarde Store met miljoenen of miljarden partitie sleutels?

Azure Cosmos DB kunnen onbeperkte gegevens opslaan door de opslag te schalen. Deze opslag is onafhankelijk van de door voer. Ja, u kunt altijd de Cassandra-API gebruiken om sleutels en waarden op te slaan en op te halen door de juiste primaire/partitie sleutel op te geven. Deze afzonderlijke sleutels krijgen hun eigen logische partitie en hierop een fysieke partitie zonder problemen.

### <a name="can-i-create-more-than-one-table-with-the-cassandra-api"></a>Kan ik meer dan een tabel maken met de Cassandra-API?

Ja, u kunt meer dan één tabel maken met de Cassandra-API. Elk van deze tabellen wordt behandeld als eenheid voor door Voer en opslag.

### <a name="can-i-create-more-than-one-table-in-succession"></a>Kan ik meer dan een tabel achter elkaar maken?

Azure Cosmos DB is bron gerichte systeem voor activiteiten op het vlak van gegevens en besturings elementen. Containers, zoals verzamelingen en tabellen, zijn runtime-entiteiten die zijn ingericht voor een bepaalde doorvoer capaciteit. Het maken van deze containers is in voorkomend geval geen verwachte activiteit en wordt mogelijk beperkt. Als u tests hebt die onmiddellijk neerzetten of maken van tabellen, kunt u proberen deze uit te voeren.

### <a name="what-is-the-maximum-number-of-tables-that-i-can-create"></a>Wat is het maximum aantal tabellen dat ik kan maken?

Er is geen fysieke limiet voor het aantal tabellen. Als u een groot aantal tabellen hebt (waarbij de totale stabiele grootte meer dan 10 TB aan gegevens gaat) die moeten worden gemaakt, niet de gebruikelijke tien tallen of honderden, moet u een e-mail verzenden naar [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) .

### <a name="what-is-the-maximum-number-of-keyspaces-that-i-can-create"></a>Wat is het maximum aantal benodigde code ruimte dat ik kan maken?

Er is geen fysieke limiet voor het aantal Keys, omdat het meta gegevens containers zijn. Als u een groot aantal spaties hebt, verzendt u een e-mail naar [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) .

### <a name="can-i-bring-in-a-lot-of-data-after-starting-from-a-normal-table"></a>Kan ik veel gegevens maken nadat ik vanuit een normale tabel heb gestart?

Ja. Uitgaande van gelijkmatig gedistribueerde partities, wordt de opslag capaciteit automatisch beheerd en verhoogd naarmate er meer gegevens worden gepusht. Zo kunt u met vertrouwen zoveel gegevens importeren als u nodig hebt zonder dat u knoop punten hoeft te beheren en inrichten en nog veel meer. Maar als u een groot aantal onmiddellijke groei van gegevens verwacht, is het beter om rechtstreeks te [voorzien in de verwachte door Voer](set-throughput.md) , in plaats van deze te verlagen en direct te verhogen.

### <a name="can-i-use-yaml-file-settings-to-configure-api-behavior"></a>Kan ik YAML-Bestands instellingen gebruiken om het API-gedrag te configureren?

De Cassandra-API voor Azure Cosmos DB biedt compatibiliteit op protocol niveau voor het uitvoeren van bewerkingen. Het verbergt de complexiteit van beheer, bewaking en configuratie. Als ontwikkelaar/gebruiker hoeft u zich geen zorgen te maken over beschik baarheid, tombstones, sleutel cache, rij-cache, bloei filter en een groot aantal andere instellingen. De Cassandra-API is gericht op het bieden van de lees-en schrijf prestaties die u nodig hebt zonder de overhead van configuratie en beheer.

### <a name="will-the-cassandra-api-support-node-addition-cluster-status-and-node-status-commands"></a>Worden de opdrachten voor het toevoegen van knoop punten, cluster status en knooppunt status ondersteund door de Cassandra-API?

De Cassandra-API vereenvoudigt de capaciteits planning en reageert op de elasticiteits vereisten voor door Voer en opslag. Met Azure Cosmos DB kunt u de door Voer die u nodig hebt inrichten. Vervolgens kunt u de schaal van de app omhoog en omlaag schalen, zonder dat u zich zorgen hoeft te maken over het toevoegen, verwijderen of beheren van knoop punten. U hoeft geen hulpprogram ma's voor het beheer van knoop punten en clusters te gebruiken.

### <a name="what-happens-with-various-configuration-settings-for-keyspace-creation-like-simplenetwork"></a>Wat gebeurt er met verschillende configuratie-instellingen voor het maken van de opslag ruimte, zoals eenvoudig/netwerk?

Azure Cosmos DB biedt wereld wijde distributie van het vak voor Beschik baarheid en redenen voor lage latentie. U hoeft geen replica's of andere zaken in te stellen. Schrijf bewerkingen zijn altijd blijvend quorum die in elke regio waar u schrijft, en tegelijkertijd prestatie garanties bieden.

### <a name="what-happens-with-various-settings-for-table-metadata"></a>Wat gebeurt er met verschillende instellingen voor de meta gegevens van de tabel?

Azure Cosmos DB biedt prestatie garanties voor lees bewerkingen, schrijf bewerkingen en door voer. U hoeft zich geen zorgen te maken over het aanraken van de configuratie-instellingen en deze per ongeluk te manipuleren. Deze instellingen zijn onder andere het bloeien van het filter, de caching, de kans op Lees reparaties, gc_grace en compressie memtable_flush_period.

### <a name="is-time-to-live-supported-for-cassandra-tables"></a>Wordt time-to-Live ondersteund voor Cassandra-tabellen?

Ja, TTL wordt ondersteund.

### <a name="how-can-i-monitor-infrastructure-along-with-throughput"></a>Hoe kan ik de infra structuur naast de door Voer controleren?

Azure Cosmos DB is een platform service waarmee u de productiviteit kunt verhogen en geen zorgen hoeft te maken over het beheer en de bewaking van de infra structuur. U hoeft bijvoorbeeld niet eerder de knooppunt status, replica status, GC en OS-para meters te bewaken met verschillende hulpprogram ma's. U hoeft alleen maar een door voer te nemen die beschikbaar is in de metrische gegevens van de portal om te controleren of u een beperking krijgt en vervolgens de door voer te verg Roten of verkleinen. U kunt:

- [Sla's](./monitor-cosmos-db.md) controleren
- [Metrische gegevens](use-metrics.md) gebruiken
- [Diagnostische logboeken](./monitor-cosmos-db.md) gebruiken

### <a name="which-client-sdks-can-work-with-the-cassandra-api"></a>Welke client-Sdk's kunnen samen werken met de Cassandra-API?

De client Stuur Programma's van Apache Cassandra SDK die gebruikmaken van CQLv3, zijn gebruikt voor client Programma's. Als u andere Stuur Programma's hebt die u gebruikt of als u problemen ondervindt, stuurt u een e-mail naar [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) .

### <a name="are-composite-partition-keys-supported"></a>Worden samengestelde partitie sleutels ondersteund?

Ja, u kunt reguliere syntaxis gebruiken voor het maken van samengestelde partitie sleutels.

### <a name="can-i-use-sstableloader-for-data-loading"></a>Kan ik sstableloader gebruiken voor het laden van gegevens?

Nee, sstableloader wordt niet ondersteund.

### <a name="can-i-pair-an-on-premises-apache-cassandra-cluster-with-the-cassandra-api"></a>Kan ik een on-premises Apache Cassandra-cluster koppelen met de Cassandra-API?

Momenteel heeft Azure Cosmos DB een geoptimaliseerde ervaring voor een cloud omgeving zonder de overhead van bewerkingen. Als u wilt koppelen, moet u een e-mail verzenden naar [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) met een beschrijving van uw scenario. We werken aan een aanbieding om het on-premises of Cloud Cassandra-cluster te koppelen aan de Cassandra-API voor Azure Cosmos DB.

### <a name="does-the-cassandra-api-provide-full-backups"></a>Biedt de Cassandra-API volledige back-ups?

Azure Cosmos DB biedt twee gratis volledige back-ups die zijn gemaakt met een interval van vier uur voor alle Api's. U hoeft dus geen back-upschema in te stellen. 

Als u de retentie en frequentie wilt wijzigen, verzendt u een e-mail naar [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) of verhoogt u een ondersteunings aanvraag. Informatie over de back-upfunctie vindt u in het artikel [Automatische online back-up en herstellen met Azure Cosmos DB](online-backup-and-restore.md) .

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>Hoe verwerkt het Cassandra-API-account failover als een regio uitvalt?

Het Cassandra-API leent zich van het wereld wijd gedistribueerde platform van Azure Cosmos DB. Om ervoor te zorgen dat uw toepassing downtime van data centers kan verdragen, moet u ten minste één regio voor het account inschakelen in de Azure Portal. Zie [hoge Beschik baarheid met Azure Cosmos DB](high-availability.md)voor meer informatie.

U kunt zoveel regio's toevoegen als u wilt voor het account en het beheer waar de failover kan worden uitgevoerd door een failover-prioriteit op te geven. Als u de Data Base wilt gebruiken, moet u ook een toepassing opgeven. Als u dit doet, zijn uw klanten geen last van uitval.

### <a name="does-the-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>Indexeert de Cassandra-API standaard alle kenmerken van een entiteit?

Nee. De Cassandra-API ondersteunt [secundaire indexen](cassandra-secondary-index.md), die op een vergelijk bare manier werken als Apache Cassandra. De API indexeert niet elk kenmerk standaard.  


### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>Kan ik de nieuwe Cassandra-API SDK lokaal met de emulator gebruiken?

Ja, dit wordt ondersteund. Meer informatie over het inschakelen van deze optie vindt u in het artikel [Azure Cosmos DB emulator gebruiken voor lokale ontwikkeling en test doeleinden](local-emulator.md#cassandra-api) .


### <a name="how-can-i-migrate-data-from-apache-cassandra-clusters-to-azure-cosmos-db"></a>Hoe kan ik gegevens van Apache Cassandra-clusters migreren naar Azure Cosmos DB?

U vindt meer informatie over migratie opties in het [account uw gegevens migreren naar Cassandra-API in azure Cosmos DB](cassandra-import-data.md) zelf studie.


### <a name="where-can-i-give-feedback-on-cassandra-api-features"></a>Waar kan ik feedback geven over Cassandra-API-functies?

Feedback geven via [feedback van gebruikers](https://feedback.azure.com/forums/263030-azure-cosmos-db).

[azure-portal]: https://portal.azure.com
[query]: ./sql-query-getting-started.md

## <a name="next-steps"></a>Volgende stappen

- Ga aan de slag met [het elastisch schalen van een Azure Cosmos DB Cassandra-API-account](manage-scale-cassandra.md).
