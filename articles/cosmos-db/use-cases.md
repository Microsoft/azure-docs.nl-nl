---
title: Veelvoorkomende use cases en scenario's voor Azure Cosmos DB
description: 'Meer informatie over de belangrijkste vijf use cases voor Azure Cosmos DB: door de gebruiker gegenereerde inhoud, logboek registratie, catalogus gegevens, gebruikers voorkeuren gegevens en Internet of Things (IoT).'
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: 4aed956fc7052f844080b830aa4cc3b2a07235a1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104799391"
---
# <a name="common-azure-cosmos-db-use-cases"></a>Gebruikelijke Azure Cosmos DB use cases
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

In dit artikel vindt u een overzicht van een aantal algemene gebruiks voorbeelden voor Azure Cosmos DB.  De aanbevelingen in dit artikel dienen als uitgangs punt bij het ontwikkelen van uw toepassing met Cosmos DB.

Na het lezen van dit artikel kunt u de volgende vragen beantwoorden: 

* Wat zijn de algemene gebruiks cases voor Azure Cosmos DB?
* Wat zijn de voor delen van het gebruik van Azure Cosmos DB voor retail toepassingen?
* Wat zijn de voor delen van het gebruik van Azure Cosmos DB als een gegevens Archief voor Internet of Things-systemen (IoT)?
* Wat zijn de voor delen van het gebruik van Azure Cosmos DB voor web-en mobiele toepassingen?

## <a name="introduction"></a>Introductie

[Azure Cosmos DB](../cosmos-db/introduction.md) is de snelle NoSQL-data base van micro soft met open-api's voor elke schaal. De service is zo ontworpen dat klanten hun door Voer en opslag voor een wille keurig aantal geografische regio's elastisch (en onafhankelijk) kunnen schalen. Azure Cosmos DB is momenteel de eerste wereld wijd gedistribueerde database service in de markt om uitgebreide [service overeenkomsten](https://azure.microsoft.com/support/legal/sla/cosmos-db/) te bieden die de door Voer, latentie, Beschik baarheid en consistentie omvatten.

Azure Cosmos DB is een wereld wijd gedistribueerde, multi-model database die wordt gebruikt in een breed scala aan toepassingen en use cases. Het is een goede keuze voor elke [serverloze](https://azure.com/serverless) toepassing die een lage reactie tijd van milliseconden nodig heeft en snel en wereld wijd moet schalen. Het ondersteunt meerdere gegevens modellen (sleutel-waarde, documenten, grafieken en kolommen) en veel Api's voor gegevens toegang, waaronder [de API van Azure Cosmos DB voor MongoDb](mongodb-introduction.md), [SQL API](./introduction.md), [Gremlin API](graph-introduction.md)en [Table API](table-introduction.md) native en op een uitbreid bare manier. 

Hier volgen enkele kenmerken van Azure Cosmos DB die het geschikt maken voor toepassingen met hoge prestaties en globale visie.

* Azure Cosmos DB systeem eigen gegevens partitioneert voor hoge Beschik baarheid en schaal baarheid. Azure Cosmos DB biedt een garantie van 99,99% voor Beschik baarheid, door Voer, lage latentie en consistentie op alle accounts met één regio en alle accounts met meerdere regio's met beperkte consistentie en 99,999% Lees Beschik baarheid voor alle database accounts voor meerdere regio's.
* Azure Cosmos DB heeft een opslag met SSD-back-ups met een reactie tijd van minder latentie.
* De ondersteuning van Azure Cosmos DB voor consistentie niveaus, zoals het uiteindelijke, het consistente voor voegsel, de sessie en de gebonden veroudering, biedt volledige flexibiliteit en lage kosten-naar-prestatie verhouding. Geen enkele database service biedt zoveel flexibiliteit als Azure Cosmos DB in niveaus consistentie. 
* Azure Cosmos DB heeft een flexibel prijsstellings model waarmee opslag en door Voer onafhankelijk van de gegevens worden geretourneerd.
* Met het gereserveerde doorvoer model van Azure Cosmos DB kunt u zien wat het aantal lees-en schrijf bewerkingen is in plaats van CPU/geheugen/IOPs van de onderliggende hardware.
* Met het ontwerp van Azure Cosmos DB kunt u schalen naar enorme aanvraag volumes in de volg orde van triljoen aanvragen per dag.

Deze kenmerken zijn nuttig in web-, mobiele, gaming-en IoT-toepassingen die weinig reactie tijden nodig hebben en een enorme hoeveelheid Lees-en schrijf bewerkingen moeten afhandelen.

## <a name="iot-and-telematics"></a>IoT en telematica

IoT use cases delen meestal enkele patronen in de manier waarop ze gegevens opnemen, verwerken en opslaan.  Ten eerste moeten deze systemen bursts van gegevens opnemen uit Sens oren van verschillende land instellingen. Vervolgens verwerken en analyseren deze systemen streaminggegevens om real-time inzichten te verkrijgen. De gegevens worden vervolgens gearchiveerd naar koude opslag voor batch analyse. Microsoft Azure biedt uitgebreide services die kunnen worden toegepast voor IoT-use cases, waaronder Azure Cosmos DB, Azure Event Hubs, Azure Stream Analytics, Azure notification hub, Azure Machine Learning, Azure HDInsight en Power BI. 

:::image type="content" source="./media/use-cases/iot.png" alt-text="IoT-referentie architectuur Azure Cosmos DB" border="false":::

De bursts van gegevens kunnen door Azure Event Hubs worden opgenomen, omdat de gegevens opname met een hoge doorvoer snelheid met lage latentie wordt verzonden. De gegevens die moeten worden verwerkt voor realtime inzicht kunnen worden afgebeeld in Azure Stream Analytics voor realtime analyses. Gegevens kunnen in Azure Cosmos DB worden geladen voor ad hoc query's. Zodra de gegevens in Azure Cosmos DB zijn geladen, kunnen de gegevens worden opgevraagd. Daarnaast kunnen nieuwe gegevens en wijzigingen aan bestaande gegevens worden gelezen voor wijzigings invoer. Wijzigings invoer is een permanent, alleen-toevoegen logboek waarmee wijzigingen in Cosmos-containers in sequentiële volg orde worden opgeslagen. Vervolgens kunnen alle gegevens of alleen wijzigingen in gegevens in Azure Cosmos DB als referentie gegevens worden gebruikt als onderdeel van de analyse van real-time. Bovendien kunnen gegevens verder worden verfijnd en verwerkt door Azure Cosmos DB gegevens te koppelen aan HDInsight voor Pig, Hive of het toewijzen/verminderen van taken.  Verfijnde gegevens worden vervolgens teruggeladen naar Azure Cosmos DB voor rapportage.   

Voor een voor beeld van een IoT-oplossing met Azure Cosmos DB, Event hubs en Storm raadpleegt u de [opslag plaats hdinsight-Storm-voor beelden op github](https://github.com/hdinsight/hdinsight-storm-examples/).

Zie [het Internet van uw dingen maken](https://www.microsoft.com/en-us/internet-of-things)voor meer informatie over Azure-aanbiedingen voor IOT. 

## <a name="retail-and-marketing"></a>Detail handel en marketing
Azure Cosmos DB wordt uitgebreid gebruikt op de e-commerce-platforms van micro soft, waarop de Windows Store en XBox Live worden uitgevoerd. Het wordt ook gebruikt in de detail handel voor het opslaan van catalogus gegevens en gebeurtenis bronnen in pijp lijnen voor order verwerking.

Scenario's voor het gebruik van catalogus gegevens omvatten het opslaan en opvragen van een set kenmerken voor entiteiten zoals personen, plaatsen en producten. Enkele voor beelden van catalogus gegevens zijn gebruikers accounts, product catalogi, IoT Device-registers en stuk lijst systemen. Kenmerken voor deze gegevens kunnen variëren en kunnen in de loop van de tijd worden gewijzigd zodat ze aan de toepassings vereisten voldoen.

Bekijk een voor beeld van een product catalogus voor een leverancier van auto onderdelen. Elk onderdeel kan zijn eigen kenmerken hebben naast de algemene kenmerken die alle onderdelen delen. Bovendien kunnen kenmerken voor een specifiek deel het volgende jaar wijzigen wanneer een nieuw model wordt vrijgegeven. Azure Cosmos DB biedt ondersteuning voor flexibele schema's en hiërarchische gegevens, zodat deze geschikt zijn voor het opslaan van product catalogus gegevens.

:::image type="content" source="./media/use-cases/product-catalog.png" alt-text="Referentie architectuur van de retail Catalog Azure Cosmos DB" border="false":::

Azure Cosmos DB wordt vaak gebruikt voor gebeurtenis bronnen voor gestuurde architecturen op basis van de [wijzigings](change-feed.md) functie van de feed. De wijzigings feed biedt downstream micro Services de mogelijkheid om invoegingen en updates (bijvoorbeeld bestel gebeurtenissen) op betrouw bare en incrementele wijze te lezen die zijn aangebracht in een Azure Cosmos DB. Deze functionaliteit kan worden gebruikt om een persistent gebeurtenis archief als een Message Broker te bieden voor status wijzigings gebeurtenissen en werk stroom voor de verwerking van de verwerkings volgorde tussen veel micro Services (die kunnen worden geïmplementeerd als [serverloze Azure functions](https://azure.com/serverless)).

:::image type="content" source="./media/use-cases/event-sourcing.png" alt-text="Pijplijn referentie architectuur Azure Cosmos DB best Ellen" border="false":::

Bovendien kunnen gegevens die zijn opgeslagen in Azure Cosmos DB worden geïntegreerd met HDInsight voor big data Analytics via Apache Spark-taken. Zie [een Spark-taak uitvoeren met Cosmos DB en HDInsight](spark-connector.md)voor meer informatie over de Spark-Connector voor Azure Cosmos db.

## <a name="gaming"></a>Gaming
De databaselaag is een cruciaal onderdeel van gamingtoepassingen. Moderne games voeren grafische verwerking uit op mobiele/consoleclients, maar zijn afhankelijk van de cloud om aangepaste en gepersonaliseerde inhoud te leveren, zoals in-gamestatistieken, integratie van sociale media en klassementen voor hoogste scores. Spellen vereisen vaak enkele milliseconde wacht tijden voor lees-en schrijf bewerkingen om een gebruiks vriendelijke ervaring te bieden. Een gamedatabase moet snel zijn en in staat zijn om grote pieken in het aantal aanvragen te verwerken tijdens het starten van een nieuwe game en functie-updates.

Azure Cosmos DB wordt gebruikt door games als de doorlopende [dood: er is geen man](https://azure.microsoft.com/blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/) met de [volgende games](https://www.nextgames.com/)en [Halo 5: Guardians](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). Azure Cosmos DB biedt de volgende voor delen voor spel ontwikkelaars:

* Met Azure Cosmos DB kunnen prestaties op flexibele wijze worden uitgebreid of omlaag worden geschaald. Op deze manier kunnen games het bijwerken van profielen en statistieken van tien tallen tot miljoenen gelijktijdige gamers afhandelen door één API-aanroep uit te voeren.
* Azure Cosmos DB ondersteunt milliseconden voor lezen en schrijven om lags te voor komen tijdens het spelen van het spel.
* Met de automatische indexering van Azure Cosmos DB kunt u in realtime filteren op meerdere verschillende eigenschappen, bijvoorbeeld door spelers te zoeken op hun interne speler-Id's of hun GameCenter, Facebook, Google-Id's of query op basis van lidmaatschap van een speler in een Guild. Dit is mogelijk zonder complexe indexering of sharding-infra structuur te bouwen.
* Sociale functies met inbegrip van game-chat berichten, Guild-lidmaatschappen van spelers, uitdagingen die zijn voltooid, kwalitatief hoogwaardige klassementen en sociale grafieken zijn eenvoudiger te implementeren met een flexibel schema.
* Azure Cosmos DB als een beheerde PaaS (platform-as-a-Service) vereist mini maal installatie-en beheer taken om snel te kunnen iteratieen en de tijd op de markt te verminderen.

:::image type="content" source="./media/use-cases/gaming.png" alt-text="Azure Cosmos DB-naslag architectuur voor games" border="false":::

## <a name="web-and-mobile-applications"></a>Webtoepassingen en mobiele toepassingen
Azure Cosmos DB wordt doorgaans gebruikt in web- en mobiele toepassingen en is zeer geschikt voor het modelleren van sociale interacties, integratie met services van derden en voor het bouwen van geavanceerde persoonlijke ervaringen. De Cosmos DB Sdk's kunnen worden gebruikt om geavanceerde iOS-en Android-toepassingen te bouwen met behulp van het populaire [Xamarin-Framework](mobile-apps-with-xamarin.md).  

### <a name="social-applications"></a>Sociale toepassingen
Een veelvoorkomend gebruik van Azure Cosmos DB is het opslaan en opvragen van door de gebruiker gegenereerde inhoud (UGC) voor web-, mobiele en Social-media toepassingen. Enkele voor beelden van UGC zijn chat sessies, tweets, blog berichten, beoordelingen en opmerkingen. Vaak is het UGC van toepassingen in sociale media een combi natie van vrije tekst, eigenschappen, tags en relaties die niet worden begrensd door stijve structuur. Inhoud zoals chats, opmerkingen en Posts kunnen worden opgeslagen in Cosmos DB zonder trans formaties of complex object naar relationele toewijzings lagen te hoeven.  Gegevens eigenschappen kunnen eenvoudig worden toegevoegd of gewijzigd om te voldoen aan de vereisten als ontwikkel aars de code van de toepassing herhalen, waardoor de snelle ontwikkeling kan worden bevorderd.  

Toepassingen die met sociale netwerken van derden kunnen worden geïntegreerd, moeten reageren op veranderende schema's van deze netwerken. Wanneer gegevens automatisch worden geïndexeerd in Cosmos DB, kunnen gegevens op elk gewenst moment worden opgevraagd. Deze toepassingen hebben daarom de flexibiliteit om projecties op te halen conform hun eigen behoeften.

Veel van de sociale toepassingen worden uitgevoerd op wereld wijde schaal en kunnen onvoorspelbare gebruiks patronen vertonen. De flexibiliteit bij het schalen van het gegevens archief is essentieel voor de schaal van de toepassingslaag zodat deze overeenkomt met de gebruiks vraag.  U kunt uitschalen door extra gegevens partities toe te voegen onder een Cosmos DB-account.  Daarnaast kunt u ook extra Cosmos DB-accounts maken in meerdere regio's. Zie [Azure-regio's](https://azure.microsoft.com/regions/#services)voor Cosmos DB Beschik baarheid van de service regio.

:::image type="content" source="./media/use-cases/apps-with-global-reach.png" alt-text="Diagram waarin de referentie architectuur van Azure Cosmos DB web-app wordt weer gegeven." border="false":::

### <a name="personalization"></a>Persoonlijke instellingen
Tegenwoordig, moderne toepassingen worden geleverd met complexe weer gaven en ervaringen. Dit zijn doorgaans dynamische, Cate ring voor gebruikers voorkeuren of stemmingen en huismerk vereisten. Daarom moeten toepassingen aangepaste instellingen effectief kunnen ophalen om gebruikers interface-elementen en-ervaringen snel weer te geven. 

JSON, een indeling die wordt ondersteund door Cosmos DB, is een effectief formaat om de indelings gegevens van de gebruikers interface weer te geven, aangezien deze niet alleen lichter zijn, maar ook gemakkelijk kunnen worden geïnterpreteerd door Java script. Cosmos DB biedt instel bare-consistentie niveaus die snelle lees bewerkingen met lage latentie mogelijk maken. Daarom is het opslaan van gegevens over de indeling van de gebruikers interface, inclusief persoonlijke instellingen als JSON-documenten in Cosmos DB een doel matige manier om deze gegevens over de kabel te halen.

:::image type="content" source="./media/use-cases/personalization.png" alt-text="Referentie architectuur voor Azure Cosmos DB web-app" border="false":::

## <a name="next-steps"></a>Volgende stappen

* Volg onze [Snelstartgids](create-sql-api-dotnet.md)om aan de slag te gaan met Azure Cosmos DB, waarmee u een account maakt en aan de slag gaat met Cosmos db.

* Als u meer wilt weten over klanten met behulp van Azure Cosmos DB, gaat u naar de pagina [casestudy's van klanten](https://azure.microsoft.com/case-studies/?service=cosmos-db) .
