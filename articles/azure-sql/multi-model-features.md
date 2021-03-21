---
title: Mogelijkheden voor meerdere modellen
description: Met Microsoft Azure SQL kunt u werken met meerdere gegevens modellen in dezelfde data base.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 12/17/2018
ms.openlocfilehash: b16a2fc9f107a8420fb7d05667807a869fa3e00a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102172754"
---
# <a name="multi-model-capabilities-of-azure-sql-database--sql-managed-instance"></a>Mogelijkheden van meerdere modellen van Azure SQL Database & SQL Managed instance
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Data bases met meerdere modellen bieden de mogelijkheid om gegevens op te slaan en te gebruiken die worden weer gegeven in meerdere gegevens indelingen, zoals relationele gegevens, grafieken, JSON/XML-documenten, sleutel-waardeparen, enzovoort.

## <a name="when-to-use-multi-model-capabilities"></a>Wanneer u multi-model mogelijkheden gebruikt

De [Azure SQL-product familie](azure-sql-iaas-vs-paas-what-is-overview.md) is ontworpen om te werken met het relationele model dat de beste prestaties biedt in de meeste gevallen voor diverse toepassingen voor algemeen gebruik. De Azure SQL-product familie van producten is echter niet beperkt tot relationele gegevens. Met de Azure SQL-familie van producten kunt u gebruikmaken van een groot aantal niet-relationele indelingen die nauw geïntegreerd zijn in het relationele model.
In de volgende gevallen kunt u overwegen om gebruik te maken van mogelijkheden voor meerdere modellen van de Azure SQL-producten:

- U hebt een aantal informatie of structuren die beter passen voor NoSQL-modellen en u geen afzonderlijke NoSQL-Data Base wilt gebruiken.
- Een meerderheid van uw gegevens is geschikt voor relationele modellen en u moet een deel van uw gegevens in NoSQL-stijl model leren.
- U wilt gebruikmaken van de uitgebreide Transact-SQL-taal voor het opvragen en analyseren van relationele en NoSQL-gegevens en deze te integreren met een aantal hulpprogram ma's en toepassingen die SQL-taal kunnen gebruiken.
- U wilt database functies, zoals [in-Memory technologieën](in-memory-oltp-overview.md) , Toep assen om de prestaties van uw analyse of verwerking van uw NoSQL-gegevens structuren te verbeteren, gebruik [transactionele replicatie](managed-instance/replication-transactional-overview.md) of [Lees bare replica's](database/read-scale-out.md) om een kopie van uw gegevens te maken op de andere locatie en een aantal analytische workloads van de primaire data base te offloaden.

## <a name="overview"></a>Overzicht

De producten uit de Azure SQL-familie bieden de volgende mogelijkheden voor meerdere modellen:

- Met [Graph-functies](#graph-features) kunt u uw gegevens weer geven als set van knoop punten en randen en kunt u standaard Transact-SQL-query's gebruiken met de grafiek `MATCH` operator voor het opvragen van de grafiek gegevens.
- Met de [JSON-functies](#json-features) kunt u JSON-documenten in tabellen plaatsen, relationele gegevens TRANSFORMEREN naar JSON-documenten en vice versa. U kunt de standaard Transact-SQL-taal uitgebreid met JSON-functies voor het parseren van documenten en niet-geclusterde indexen, Column Store-indexen of tabellen die zijn geoptimaliseerd voor geheugen gebruiken om uw query's te optimaliseren.
- Met [ruimtelijke functies](#spatial-features) kunt u geografische en geometrische gegevens opslaan, indexeren met behulp van ruimtelijke indexen en de gegevens ophalen met behulp van ruimtelijke query's.
- Met [XML-functies](#xml-features) kunt u XML-gegevens in uw data base opslaan en indexeren en systeem eigen XQuery/XPath-bewerkingen gebruiken om met XML-gegevens te werken. De Azure SQL-product familie beschikt over een gespecialiseerde ingebouwde XML-query-engine waarmee XML-gegevens worden verwerkt.
- [Sleutel-](#key-value-pairs) waardeparen worden niet expliciet ondersteund als speciale functies omdat sleutel-waardeparen kunnen worden gemodelleerd als tabellen met twee kolommen.

  > [!Note]
  > U kunt JSON Path-expressie, XQuery/XPath-expressies, ruimtelijke functies en Graph-query-expressies in dezelfde Transact-SQL-query gebruiken om toegang te krijgen tot gegevens die u in de Data Base hebt opgeslagen. Daarnaast kan elk hulp programma of elke programmeer taal waarmee Transact-SQL-query's kunnen worden uitgevoerd, deze query-interface ook gebruiken om toegang te krijgen tot gegevens van meerdere modellen. Dit is het belangrijkste verschil ten opzichte van de data bases met meerdere modellen, zoals [Azure Cosmos DB](../cosmos-db/index.yml) die speciale API bieden voor verschillende gegevens modellen.

In de volgende secties vindt u meer informatie over de belangrijkste mogelijkheden van meerdere modellen van de Azure SQL-producten.

## <a name="graph-features"></a>Graph-functies

De Azure SQL-producten bieden grafiek database mogelijkheden voor het model leren van veel-op-veel-relaties in de data base. Een grafiek is een verzameling knoop punten (of hoek punten) en randen (of relaties). Een knoop punt vertegenwoordigt een entiteit (bijvoorbeeld een persoon of een organisatie) en een rand vertegenwoordigt een relatie tussen de twee knoop punten die er verbinding mee maakt (bijvoorbeeld leuk of vrienden). Hier volgen enkele functies die een grafiek database uniek maken:

- Randen of relaties zijn de eerste klasse-entiteiten in een grafiek database en er kunnen kenmerken of eigenschappen aan zijn gekoppeld.
- Eén rand kan flexibel meerdere knoop punten in een grafiek database verbinden.
- U kunt snel patroon overeenkomsten en navigatie query's met meerdere hops eenvoudig uitdrukken.
- U kunt transitieve sluitingen en polymorphing-query's eenvoudig uitdrukken.

De [mogelijkheden van Graph-relaties en Graph-query's](/sql/relational-databases/graphs/sql-graph-overview) zijn geïntegreerd in Transact-SQL en ontvangen de voor delen van het gebruik van de SQL server data base-engine als basis Database Management System.

### <a name="when-to-use-a-graph-capability"></a>Wanneer u een grafiek mogelijkheid wilt gebruiken

Er is niets wat een grafiek database kan bereiken. Dit kan niet worden bereikt met een relationele data base. Met een Graph-Data Base kunt u echter gemakkelijker bepaalde query's uitdrukken. Uw beslissing om een van de andere te kiezen, kan worden gebaseerd op de volgende factoren:

- Modeleer hiërarchische gegevens waarbij één knoop punt meerdere bovenliggende items kan hebben, zodat HierarchyId niet kan worden gebruikt
- Model heeft uw toepassing complexe veel-op-veel-relaties. Naarmate de toepassing wordt ontwikkeld, worden nieuwe relaties toegevoegd.
- U moet onderling verbonden gegevens en relaties analyseren.

## <a name="json-features"></a>JSON-functies

Met de Azure SQL-familie van producten kunt u gegevens parseren en opvragen die worden weer gegeven in de indeling van JavaScript Object Notation [(JSON)](https://www.json.org/) en uw relationele gegevens exporteren als JSON-tekst.

JSON is een populaire gegevens indeling die wordt gebruikt voor het uitwisselen van gegevens in moderne web-en mobiele toepassingen. JSON wordt ook gebruikt voor het opslaan van semi-gestructureerde gegevens in logboek bestanden of in NoSQL-data bases zoals [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). Veel REST-webservices retour neren resultaten die zijn opgemaakt als JSON-tekst of accepteren gegevens die zijn opgemaakt als JSON. De meeste Azure-Services, zoals [Azure Cognitive Search](https://azure.microsoft.com/services/search/), [Azure Storage](https://azure.microsoft.com/services/storage/)en [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) hebben rest-eind punten die JSON retour neren of gebruiken.


Met de Azure SQL-familie van producten kunt u eenvoudig met JSON-gegevens werken en uw data base integreren met moderne services. Daarnaast biedt de volgende functies voor het werken met JSON-gegevens:

![JSON-functies](./media/multi-model-features/image_1.png)

Als u JSON-tekst hebt, kunt u gegevens uit JSON extra heren of controleren of de JSON juist is ingedeeld met behulp van de ingebouwde functies [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql), [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql)en [ISJSON](/sql/t-sql/functions/isjson-transact-sql). Met de functie [JSON_MODIFY](/sql/t-sql/functions/json-modify-transact-sql) kunt u de waarde in JSON-tekst bijwerken. Voor meer geavanceerde query's en analyse kunnen met de functie [openjson](/sql/t-sql/functions/openjson-transact-sql) een matrix van JSON-objecten worden omgezet in een set rijen. Een SQL-query kan worden uitgevoerd op de geretourneerde resultatenset. Ten slotte is er een [voor json](/sql/relational-databases/json/format-query-results-as-json-with-for-json-sql-server) -component waarmee u gegevens kunt opmaken die in uw relationele tabellen zijn opgeslagen als JSON-tekst.

Zie [werken met JSON-gegevens](database/json-features.md)voor meer informatie.
[JSON](/sql/relational-databases/json/json-data-sql-server) is een kern functie van SQL server data base-engine.

### <a name="when-to-use-a-json-capability"></a>Wanneer gebruikt u een JSON-mogelijkheid?

Document modellen kunnen worden gebruikt in plaats van de relationele modellen in een aantal specifieke scenario's:

- High-normalisatie van het schema brengt geen aanzienlijke voor delen omdat u alle velden van de objecten tegelijk opent, of u kunt de genormaliseerde delen van de objecten nooit bijwerken. Het genormaliseerde model verhoogt echter de complexiteit van uw query's vanwege het grote aantal tabellen dat u moet koppelen om de gegevens op te halen.
- U werkt met de toepassingen die systeem eigen gebruik maken van JSON-documenten zijn communicatie-of gegevens modellen en u wilt geen extra lagen introduceren waarmee relationele gegevens naar JSON worden getransformeerd en omgekeerd.
- U moet uw gegevens model vereenvoudigen door onderliggende tabellen of entiteit-object-waarde-patronen te ontsleutelen.
- U moet gegevens die zijn opgeslagen in JSON-indeling laden of exporteren zonder een extra hulp programma waarmee de gegevens worden geparseerd.

## <a name="spatial-features"></a>Ruimtelijke functies

Ruimtelijke gegevens vertegenwoordigen informatie over de fysieke locatie en vorm van geometrische objecten. Deze objecten kunnen punt locaties of complexere objecten zijn, zoals landen/regio's, wegen of meren.

 De twee ondersteunde ruimtelijke gegevens typen: 

- Het type geometrie vertegenwoordigt gegevens in een Euclidean (plat)-coördinaten systeem.
- Het geografie type staat voor gegevens in een coördinaten systeem met round-Earth.

Er zijn een aantal ruimtelijke objecten die kunnen worden gebruikt in de Azure SQL-familie van producten, waarmee u gegevens kunt parseren en opvragen die worden weer gegeven in de indeling van JavaScript Object Notation [(JSON)](https://www.json.org/) en uw relationele gegevens exporteren als JSON-tekst.
zoals [Point](/sql/relational-databases/spatial/point), [Lines Tring](/sql/relational-databases/spatial/linestring), [veelhoek](/sql/relational-databases/spatial/polygon), enzovoort.

De Azure SQL-product familie biedt ook gespecialiseerde [ruimtelijke indexen](/sql/relational-databases/spatial/spatial-indexes-overview) die kunnen worden gebruikt om de prestaties van uw ruimtelijke query's te verbeteren.

[Ruimtelijke ondersteuning](/sql/relational-databases/spatial/spatial-data-sql-server) is een kern functie van SQL server data base-engine.

## <a name="xml-features"></a>XML-functies

De SQL Server data base-engine biedt een krachtig platform voor het ontwikkelen van geavanceerde toepassingen voor semi-gestructureerde gegevens beheer. Ondersteuning voor XML is geïntegreerd in alle onderdelen van de data base-engine en bevat het volgende:

- Het XML-gegevens type. XML-waarden kunnen worden opgeslagen in een kolom met XML-gegevens typen die kunnen worden getypt op basis van een verzameling van XML-schema's of niet-getypte links. U kunt de XML-kolom indexeren.
- De mogelijkheid om een XQuery-query op te geven voor XML-gegevens die zijn opgeslagen in kolommen en variabelen van het XML-type. XQuery-functionaliteiten kunnen worden gebruikt in elke Transact-SQL-query die toegang heeft tot elk gegevens model dat u in uw data base gebruikt.
- Alle elementen in XML-documenten automatisch indexeren met de [primaire XML-index](/sql/relational-databases/xml/xml-indexes-sql-server#primary-xml-index) of de exacte paden opgeven die moeten worden geïndexeerd met de [secundaire XML-index](/sql/relational-databases/xml/xml-indexes-sql-server#secondary-xml-indexes).
- OPENROWSET waarmee XML-gegevens bulksgewijs kunnen worden geladen.
- Transformeer relationele gegevens in XML-indeling.

[XML](/sql/relational-databases/xml/xml-data-sql-server) is een kern functie van SQL server data base-engine.

### <a name="when-to-use-an-xml-capability"></a>Wanneer gebruikt u een XML-mogelijkheid?

Document modellen kunnen worden gebruikt in plaats van de relationele modellen in een aantal specifieke scenario's:

- High-normalisatie van het schema brengt geen aanzienlijke voor delen omdat u alle velden van de objecten tegelijk opent, of u kunt de genormaliseerde delen van de objecten nooit bijwerken. Het genormaliseerde model verhoogt echter de complexiteit van uw query's vanwege het grote aantal tabellen dat u moet koppelen om de gegevens op te halen.
- U werkt met de toepassingen die systeem eigen XML-documenten gebruiken, zijn communicatie-of gegevens modellen en u wilt geen extra lagen maken waarmee relationele gegevens worden omgezet naar XML en omgekeerd.
- U moet uw gegevens model vereenvoudigen door onderliggende tabellen of entiteit-object-waarde-patronen te ontsleutelen.
- U moet gegevens die zijn opgeslagen in XML-indeling laden of exporteren zonder een extra hulp programma waarmee de gegevens worden geparseerd.

## <a name="key-value-pairs"></a>Sleutel-waardeparen

De Azure SQL-product familie heeft geen gespecialiseerde typen of structuren die sleutel-waardeparen ondersteunen, omdat de sleutel-waardeparen kunnen worden aangeduid als standaard relationele tabellen:

```sql
CREATE TABLE Collection (
  Id int identity primary key,
  Data nvarchar(max)
)
```

U kunt deze structuur van de sleutel waarde aanpassen aan uw behoeften zonder beperkingen. Een voor beeld: de waarde kan een XML-document zijn in plaats van `nvarchar(max)` het type, als de waarde een JSON-document is, kunt u `CHECK` een beperking plaatsen die de geldigheid van JSON-inhoud verifieert. U kunt een wille keurig aantal waarden in verband met één sleutel in de extra kolommen plaatsen, berekende kolommen en indexen toevoegen om gegevens toegang te vereenvoudigen en te optimaliseren, de tabel definiëren als tabel met alleen geheugen/geoptimaliseerd schema voor betere prestaties, enzovoort.

Bekijk [hoe BWin gebruikmaakt van In-Memory OLTP om ongekende prestaties te bereiken en te schalen](/archive/blogs/sqlcat/how-bwin-is-using-sql-server-2016-in-memory-oltp-to-achieve-unprecedented-performance-and-scale) voor de ASP.NET-cache oplossing die 1.200.000 batches per seconden heeft behaald, als voor beeld hoe relationeel model effectief kan worden gebruikt als een oplossing voor sleutel waarde-paren in de praktijk.

## <a name="next-steps"></a>Volgende stappen

Mogelijkheden van meerdere modellen in de Azure SQL-product familie zijn ook de kern functies van SQL Server data base-engine die worden gedeeld met de Azure SQL-producten. Ga voor meer informatie over deze functies naar de documentatie pagina's van SQL relationele data base:

- [Grafiek verwerking](/sql/relational-databases/graphs/sql-graph-overview)
- [JSON-gegevens](/sql/relational-databases/json/json-data-sql-server)
- [Ruimtelijke ondersteuning](/sql/relational-databases/spatial/spatial-data-sql-server)
- [XML-gegevens](/sql/relational-databases/xml/xml-data-sql-server)
