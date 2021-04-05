---
title: Overzicht elastische query's
description: Met elastische query's kunt u een Transact-SQL-query uitvoeren die meerdere databases omvat.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: overview
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 12/05/2019
ms.openlocfilehash: cac17bbac96d44d8d9bfce2e168de4ea6d4c5c08
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100364950"
---
# <a name="azure-sql-database-elastic-query-overview-preview"></a>Overzicht van elastische query's van Azure SQL Database (preview)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Met de functie voor elastische query's (preview) kunt u een Transact-SQL-query uitvoeren die meerdere databases in Azure SQL Database omvat. Hiermee kunt u query's uitvoeren op meerdere databases en externe tabellen openen, en kunt u hulpprogramma's van Microsoft en derden (Excel, Power BI, Tableau enzovoort) koppelen om een query uit te voeren voor gegevenslagen met meerdere databases. Met de functie kunt u query's uitschalen naar grote gegevenslagen en de resultaten visualiseren in business intelligence-rapporten (BI).

## <a name="why-use-elastic-queries"></a>Waarom elastische query's gebruiken?

### <a name="azure-sql-database"></a>Azure SQL Database

Query's kunnen volledig in T-SQL worden uitgevoerd op databases in Azure SQL Database. Hiermee kunnen alleen-lezenquery's van externe databases worden uitgevoerd en krijgen de huidige SQL Server-klanten de mogelijkheid om toepassingen naar SQL Database te migreren met behulp van uit drie en vier delen bestaande namen of een gekoppelde server.

### <a name="available-on-standard-tier"></a>Beschikbaar in de Standard-laag

Elastische query wordt ondersteund in de servicelagen Standard en Premium. Zie onderstaande sectie over de preview-beperkingen voor prestatiebeperkingen voor lagere servicelagen.

### <a name="push-parameters-to-remote-databases"></a>Parameters naar externe databases pushen

Met elastische query's kunnen nu SQL-parameters naar de externe databases worden gepusht om te worden uitgevoerd.

### <a name="stored-procedure-execution"></a>Uitvoering van een opgeslagen procedure

Voer externe aanroepen van opgeslagen procedures of externe functies uit met behulp van [sp\_execute \_remote](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database).

### <a name="flexibility"></a>Flexibiliteit

Met een elastische query kunnen externe tabellen verwijzen naar externe tabellen met een andere schema- of tabelnaam.

## <a name="elastic-query-scenarios"></a>Scenario's met elastische query's

Het doel is om scenario's met query's te vergemakkelijken waarbij meerdere databases rijen bijdragen aan één totaalresultaat. De query kan rechtstreeks worden samengesteld door de gebruiker of de toepassing, of indirect via hulpprogramma's die zijn verbonden met de database. Dit is vooral handig bij het maken van rapporten met behulp van commerciële BI- of gegevensintegratiehulpprogramma's, of een toepassing die niet kan worden gewijzigd. Met een elastische query kunt u een query uitvoeren op verschillende databases met behulp van de vertrouwde SQL Server-connectiviteitservaring in hulpprogramma's zoals Excel, Power BI, Tableau of Cognos.
Met een elastische query krijgt u eenvoudig toegang tot een volledige verzameling databases. Deze query's worden opgegeven via SQL Server Management Studio of Visual Studio, waardoor de uitvoering van query's op meerdere databases vanuit Entity Framework of andere ORM-omgevingen eenvoudiger wordt. In afbeelding 1 ziet u een scenario waarin een bestaande cloudtoepassing (die gebruikmaakt van de [clientbibliotheek voor elastische databases](elastic-database-client-library.md)) is gebaseerd op een uitgeschaalde gegevenslaag, waarbij een elastische query wordt gebruikt voor rapportage over meerdere databases.

**Afbeelding 1** Elastische query die wordt gebruikt voor een uitgeschaalde gegevenslaag

![Elastische query die wordt gebruikt voor een uitgeschaalde gegevenslaag][1]

Klantscenario's voor elastische query's worden gekenmerkt door de volgende topologieën:

* **Verticale partitionering: query's op meerdere databases** (topologie 1): De gegevens worden verticaal gepartitioneerd tussen een aantal databases in een gegevenslaag. Doorgaans bevinden verschillende sets tabellen zich in verschillende databases. Dit betekent dat het schema afwijkt in verschillende databases. Zo bevinden alle tabellen voor de inventarisatie zich in één database, terwijl alle aan de administratie gerelateerde tabellen zich in een tweede database bevinden. In veelvoorkomende gebruiksscenario's met deze topologie moet er op één database een query worden uitgevoerd of rapporten worden gecompileerd voor meerdere tabellen in verschillende databases.
* **Horizontale partitionering: sharding** (topologie 2): Gegevens worden horizontaal gepartitioneerd om rijen te verdelen over een uitgeschaalde gegevenslaag. Met deze methode is het schema identiek voor alle betrokken databases. Deze methode wordt ook wel ' sharding ' genoemd. Sharding kan worden uitgevoerd en beheerd met behulp van (1) de hulpprogrammabibliotheken voor elastische databases of (2) automatische sharding. Een elastische query wordt gebruikt voor het opvragen of compileren van rapporten in veel shards. Shards zijn doorgaans databases binnen een elastische pool. U kunt een elastische query beschouwen als een efficiënte manier om een query op alle databases van een elastische pool tegelijk uit te voeren, zolang databases het gemeenschappelijke schema delen.

> [!NOTE]
> Een elastische query werkt het beste voor rapportagescenario's waarbij de meeste verwerking (filteren, aggregatie) aan de zijde van de externe bron kan worden uitgevoerd. De functie is niet geschikt voor ETL-bewerkingen waarbij een grote hoeveelheid gegevens wordt overgebracht uit een of meer externe databases. U kunt ook overwegen om [Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics) te gebruiken voor zware rapportworkloads of scenario's voor gegevensopslag met complexere query's.
>  

## <a name="vertical-partitioning---cross-database-queries"></a>Verticale partitionering: query's op meerdere databases

Zie [Aan de slag met query's op meerdere databases (verticaal partitioneren)](elastic-query-getting-started-vertical.md) om te beginnen met coderen.

Een elastische query kan worden gebruikt om gegevens in een database in SQL Database beschikbaar te maken voor andere databases in SQL Database. Hierdoor kunnen query's van de ene database verwijzen naar tabellen in een andere, externe database in SQL Database. De eerste stap is het definiëren van een externe gegevensbron voor elke externe database. De externe gegevensbron wordt gedefinieerd in de lokale database van waaruit u toegang wilt krijgen tot de tabellen die zich op de externe database bevinden. Er zijn geen wijzigingen vereist voor de externe database. Voor gebruikelijke scenario's voor verticale partitionering waarbij verschillende databases verschillende schema's hebben, kunnen elastische query's worden gebruikt voor het implementeren van algemene gebruiksvoorbeelden, zoals toegang tot referentiegegevens en query's uitvoeren op meerdere databases.

> [!IMPORTANT]
> U moet een machtiging hebben voor ALTER ANY EXTERNAL DATA SOURCE. Deze machtiging is opgenomen in de machtiging ALTER DATABASE. Machtigingen voor ALTER ANY EXTERNAL DATA SOURCE zijn nodig om te verwijzen naar de onderliggende gegevensbron.
>

**Referentiegegevens**: De topologie wordt gebruikt voor het beheer van referentiegegevens. In de onderstaande afbeelding zijn twee tabellen (T1 en T2) met referentiegegevens in een toegewezen database opgeslagen. Met behulp van een elastische query hebt u nu externe toegang tot de tabellen T1 en T2 vanuit andere databases, zoals wordt weergegeven in de afbeelding. U kunt topologie 1 gebruiken als referentietabellen klein zijn of externe query's op een referentietabel over selectieve predikaten beschikken.

**Afbeelding 2** Verticale partitionering: een query uitvoeren op referentiegegevens met behulp van een elastische query

![Verticale partitionering: een query uitvoeren op referentiegegevens met behulp van een elastische query][3]

**Query's uitvoeren op meerdere databases**: Elastische query's maken gebruiksvoorbeelden mogelijk waarvoor query's moeten worden uitgevoerd op meerdere databases in SQL Database. In afbeelding 3 worden vier verschillende databases weergegeven: CRM, inventarisatie, HR en producten. Query's die in een van de databases worden uitgevoerd, hebben ook toegang nodig tot één of alle andere databases. Met behulp van een elastische query kunt u uw database voor dit gebruiksvoorbeeld configureren door een paar eenvoudige DDL-instructies uit te voeren op alle vier databases. Na deze eenmalige configuratie is het openen van een externe tabel net zo eenvoudig als verwijzen naar een lokale tabel vanuit uw T-SQL-query's of vanuit uw BI-hulpprogramma's. Deze methode wordt aanbevolen als de externe query's geen uitgebreide resultaten retourneren.

**Afbeelding 3** Verticale partitionering: een query uitvoeren op verschillende databases met behulp van een elastische query

![Verticale partitionering: een query uitvoeren op verschillende databases met behulp van een elastische query][4]

Met de volgende stappen worden elastische database-query's voor verticale partitioneringsscenario's geconfigureerd waarvoor toegang nodig is tot een tabel in externe databases in SQL Database met hetzelfde schema:

* mymasterkey [CREATE MASTER KEY](/sql/t-sql/statements/create-master-key-transact-sql)
* mycredential [CREATE DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)
* mydatasource van het type **RDBMS** [CREATE/DROP EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql)
* mytable [CREATE/DROP EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql)

Na het uitvoeren van de DDL-instructies, hebt u toegang tot de externe tabel ' mytable ', alsof het een lokale tabel is. Azure SQL Database opent automatisch een verbinding met de externe database, verwerkt uw aanvraag op de externe database en retourneert de resultaten.

## <a name="horizontal-partitioning---sharding"></a>Horizontale partitionering: sharding

Voor het uitvoeren van rapportagetaken met een elastische query via een shard-gegevenslaag, dat wil zeggen een horizontaal gepartitioneerde gegevenslaag, is een [shardkaart van elastische databases](elastic-scale-shard-map-management.md) vereist om de databases van de gegevenslaag weer te geven. Normaal gesproken wordt slechts één shardkaart gebruikt in dit scenario en een toegewezen database met elastische-querymogelijkheden (hoofdknooppunt) fungeert als toegangspunt voor rapportagequery's. Alleen deze toegewezen database moet toegang hebben tot de shardkaart. Afbeelding 4 illustreert deze topologie en de configuratie ervan met de elastische-querydatabase en de shardkaart. Zie [Shardkaartbeheer](elastic-scale-shard-map-management.md) voor meer informatie over de clientbibliotheek voor elastische databases en het maken van shardkaarten.

**Afbeelding 4** Horizontale partitionering: een elastische query gebruiken voor rapportage via shard-gegevenslagen

![Horizontale partitionering: een elastische query gebruiken voor rapportage via shard-gegevenslagen][5]

> [!NOTE]
> Een elastische-querydatabase (hoofdknooppunt) kan een afzonderlijke database zijn, of kan dezelfde database zijn die de shardkaart host.
> Zorg bij elke configuratie ervoor dat de servicelaag en de rekenkracht van die database voldoende zijn om de verwachte hoeveelheid aanmeldings-en queryaanvragen af te handelen.

Met de volgende stappen worden elastische databasequery's voor horizontale partitioneringsscenario's geconfigureerd waarvoor toegang nodig is tot een reeks tabellen op (doorgaans) externe databases in SQL Database:

* mymasterkey [CREATE MASTER KEY](/sql/t-sql/statements/create-master-key-transact-sql)
* mycredential [CREATE DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)
* Maak een [shardkaart](elastic-scale-shard-map-management.md) die uw gegevenslaag aanduidt met behulp van de clientbibliotheek voor elastische databases.
* mydatasource van het type **SHARD_MAP_MANAGER** [CREATE/DROP EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql)
* mytable [CREATE/DROP EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql)

Wanneer u deze stappen hebt uitgevoerd, hebt u toegang tot de horizon taal gepartitioneerde tabel ' mytable ', alsof het een lokale tabel is. Azure SQL Database opent automatisch meerdere parallelle verbindingen met de externe databases waarin de tabellen fysiek zijn opgeslagen, verwerkt de aanvragen voor de externe databases en retourneert de resultaten.
Meer informatie over de stappen die vereist zijn voor het horizontale partitioneringsscenario kunt u vinden in [Elastische query voor horizontale partitionering](elastic-query-horizontal-partitioning.md).

Zie [Aan de slag met elastische query's voor horizontale partitionering (sharding)](elastic-query-getting-started.md) om te beginnen met coderen.

> [!IMPORTANT]
> Een geslaagde uitvoering van elastische query's over een groot aantal databases is sterk afhankelijk van de beschikbaarheid van elke database tijdens de uitvoering van de query. Als een van de databases niet beschikbaar is, mislukt de volledige query. Als u van plan bent om op honderden of duizenden databases tegelijk een query uit te voeren, moet u ervoor zorgen dat uw clienttoepassing logica voor opnieuw proberen bevat. U kunt ook [Elastische databasetaken](./job-automation-overview.md) (preview) gebruiken en een query uitvoeren op kleinere subsets van databases, waarbij u de resultaten van elke query op één doelvoorziening wordt geconsolideerd.

## <a name="t-sql-querying"></a>T-SQL-query's uitvoeren

Wanneer u uw externe gegevensbronnen en uw externe tabellen hebt gedefinieerd, kunt u reguliere SQL Server-verbindingsreeksen gebruiken om verbinding te maken met de databases waarin u uw externe tabellen hebt gedefinieerd. U kunt vervolgens T-SQL-instructies uitvoeren voor uw externe tabellen op die verbinding met de beperkingen die hieronder zijn beschreven. Meer informatie en voorbeelden van T-SQL-query's kunt u in de documentatieonderwerpen voor [horizontale partitionering](elastic-query-horizontal-partitioning.md) en [verticale partitionering](elastic-query-vertical-partitioning.md) vinden.

## <a name="connectivity-for-tools"></a>Connectiviteit voor hulpprogramma's

U kunt gewone SQL Server-verbindingsreeksen gebruiken om uw toepassingen en BI- of gegevensintegratiehulpprogramma's aan databases met externe tabellen te koppelen. Zorg ervoor dat SQL Server wordt ondersteund als gegevensbron voor uw hulpprogramma. Als de verbinding tot stand is gebracht, raadpleegt u de Elastische query-database en de externe tabellen in die database, net zoals u zou doen met andere SQL Server-databases waarmee u verbinding maakt met uw hulpprogramma.

> [!IMPORTANT]
> Verificatie via Azure Active Directory met elastische query's wordt momenteel niet ondersteund.

## <a name="cost"></a>Kosten

Bij de kosten voor Azure SQL Database is de elastische query inbegrepen. Houd er rekening mee dat topologieën waarin uw externe databases zich in een ander datacentrum bevinden dan het eindpunt van de elastische query weliswaar worden ondersteund, maar dat de kosten van uitgaande gegevens van externe databases normaliter op basis van [Azure-tarieven](https://azure.microsoft.com/pricing/details/data-transfers/) in rekening worden gebracht.

## <a name="preview-limitations"></a>Preview-beperkingen

* De uitvoering van uw eerste elastische query kan wel een paar minuten duren in de Standard-servicelaag. Deze tijd is nodig om de elastische-queryfunctionaliteit te laden. De laadprestaties worden hoger al naar gelang de capaciteit van de servicelaag en rekenkracht.
* De uitvoering van scripts voor externe gegevensbronnen of externe tabellen vanuit SSMS of SSDT wordt nog niet ondersteund.
* Importeren/exporteren voor SQL Database biedt nog geen ondersteuning voor externe gegevensbronnen en externe tabellen. Als u importeren/exporteren moet gebruiken, verwijdert u deze objecten voordat u deze exporteert en maakt u ze opnieuw na het importeren.
* Een elastische query biedt momenteel alleen ondersteuning voor alleen-lezen toegang tot externe tabellen. U kunt echter de volledige T-SQL-functionaliteit gebruiken voor de database waarin de externe tabel is gedefinieerd. Dit kan handig zijn voor het blijven gebruiken van bijvoorbeeld tijdelijke resultaten, zoals SELECT <column_list> INTO <local_table>, of voor het definiëren van opgeslagen procedures voor de Elastische query-database die naar externe tabellen verwijzen.
* Met uitzondering van nvarchar(max) worden LOB-typen (met inbegrip van ruimtelijke typen) niet ondersteund in externe tabeldefinities. Als tijdelijke oplossing kunt u een weergave maken voor de externe database waarmee het LOB-type wordt omgezet in nvarchar(max), uw externe tabel definiëren via de weergave in plaats van de basistabel en deze vervolgens weer omzetten in het oorspronkelijke LOB-type in uw query's.
* Kolommen van het gegevenstype nvarchar(max) in de resultatenset schakelen geavanceerde batchverwerkingstechnieken uit die wordt gebruikt in de implementatie van de elastische query, en kunnen tot wel twee keer zoveel invloed hebben op de prestaties van de query in niet-canonieke gebruiksscenario's waarbij een grote hoeveelheid niet-geaggregeerde gegevens wordt overgezet als resultaat van de query.
* Kolomstatistieken over externe tabellen worden momenteel niet ondersteund. Tabelstatistieken worden ondersteund, maar moeten handmatig worden gemaakt.
* De elastische query werkt alleen met Azure SQL Database. U kunt deze niet gebruiken voor het uitvoeren van een query op een SQL Server-exemplaar.

## <a name="share-your-feedback"></a>Uw feedback delen

Deel hieronder uw feedback over uw ervaring met elastische query's met ons, op de MSDN-forums of op Stack Overflow. We zijn geïnteresseerd in alle soorten feedback over de service (mankementen, overbodige zaken, hiaten in de functionaliteit).

## <a name="next-steps"></a>Volgende stappen

* Zie [Aan de slag met query's op meerdere databases (verticale partitionering)](elastic-query-getting-started-vertical.md) voor een zelfstudie over verticale partitionering.
* Zie [Query's uitvoeren op verticaal gepartitioneerde gegevens](elastic-query-vertical-partitioning.md) voor de syntaxis van en voorbeeldquery's voor verticaal gepartitioneerde gegevens
* Zie [Aan de slag met elastische query's voor horizontale partitionering (sharding)](elastic-query-getting-started.md) voor een zelfstudie over horizontale partitionering (sharding).
* Zie [Query's uitvoeren op horizontaal gepartitioneerde gegevens](elastic-query-horizontal-partitioning.md) voor de syntaxis van en voorbeeldquery's voor horizontaal gepartitioneerde gegevens
* Zie [sp\_execute \_remote](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) voor een opgeslagen procedure waarmee een Transact-SQL-instructie wordt uitgevoerd op één externe Azure SQL-database of een aantal databases die als shards fungeren in een schema voor horizontale partitionering.

<!--Image references-->
[1]: ./media/elastic-query-overview/overview.png
[2]: ./media/elastic-query-overview/topology1.png
[3]: ./media/elastic-query-overview/vertpartrrefdata.png
[4]: ./media/elastic-query-overview/verticalpartitioning.png
[5]: ./media/elastic-query-overview/horizontalpartitioning.png

<!--anchors-->