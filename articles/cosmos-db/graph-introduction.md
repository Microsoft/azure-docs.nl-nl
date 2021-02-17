---
title: Inleiding tot de Gremlin-API van Azure Cosmos DB
description: Leer hoe u Azure Cosmos DB kunt gebruiken om zeer grote grafieken met een lage latentie op te slaan, te doorzoeken en erin te bladeren, met behulp van de querytaal voor Gremlin-grafieken van Apache TinkerPop.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 11/25/2020
ms.author: chrande
ms.openlocfilehash: 806904943f4e4a20734d595f835dc9450afdded5
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100547273"
---
# <a name="introduction-to-gremlin-api-in-azure-cosmos-db"></a>Inleiding tot de Gremlin-API in Azure Cosmos DB
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

[Azure Cosmos DB](introduction.md)  is de wereldwijd gedistribueerde multimodel-databaseservice van Microsoft voor essentiële toepassingen. Dit is een database met meerdere modellen die modellen voor document-, sleutelwaarde-, grafiek- en kolomgegevens ondersteunt. Azure Cosmos DB biedt een Graph-database service via de Gremlin-API op een volledig beheerde database service die is ontworpen voor elke schaal.  

:::image type="content" source="./media/graph-introduction/cosmosdb-graph-architecture.png" alt-text="Azure Cosmos DB-grafiekarchitectuur" border="false":::

Dit artikel biedt een overzicht van de Azure Cosmos DB Gremlin API en legt uit hoe u ze kunt gebruiken om zeer grote grafieken met miljarden hoekpunten en randen op te slaan. U kunt query's uitvoeren voor de grafieken met een latentie van milliseconden en eenvoudig de grafiekstructuur ontwikkelen. De Gremlin-API van Azure Cosmos DB is ontworpen op basis van de [Apache TinkerPop](https://tinkerpop.apache.org), een rekenframework voor grafieken. De Gremlin-API in Azure Cosmos DB maakt gebruik van de Gremlin-querytaal.

De Gremlin-API van Azure Cosmos DB combineert de kracht van graafdatabase-algoritmen met een uiterst schaalbare, beheerde infrastructuur om een unieke, flexibele oplossing te bieden voor de meeste algemene gegevensproblemen die het gevolg zijn van gebrek aan flexibiliteit en relationele methoden.

> [!NOTE]
> De [modus met serverloze capaciteit](serverless.md) is nu beschikbaar in de Gremlin-API van Azure Cosmos DB.

## <a name="features-of-azure-cosmos-dbs-gremlin-api"></a>Functies van de Gremlin-API van Azure Cosmos DB
 
Azure Cosmos DB is een volledig beheerde grafiekdatabase met wereldwijde distributie, elastisch schalen van opslag en doorvoer, automatisch indexeren en doorzoeken, instelbare consistentieniveaus, en ondersteuning voor de standaardversie van TinkerPop.

De volgende functies zijn de gedifferentieerde functies die beschikbaar zijn met de Gremlin-API van Azure Cosmos DB:

* **Elastisch schaalbare doorvoer en opslag**

  Grafieken moeten in de praktijk hoger worden geschaald dan de capaciteit van een enkele server. Azure Cosmos DB biedt ondersteuning voor horizontaal schaalbare graafdatabases die, wat opslag en ingerichte doorvoer betreft, een vrijwel onbeperkte grootte kunnen hebben. Naarmate de schaal van de graafdatabase wordt uitgebreid, worden de gegevens automatisch gedistribueerd met behulp van [grafiekpartitionering](./graph-partitioning.md).

* **Replicatie naar meerdere regio's**

  Met Azure Cosmos DB kunnen uw graafgegevens automatisch worden gerepliceerd naar elke Azure-regio wereldwijd. Met behulp van wereldwijde replicatie wordt de ontwikkeling van toepassingen waarvoor wereldwijde toegang tot gegevens vereist is, vereenvoudigd. Niet alleen wordt de lees- en schrijflatentie over de hele wereld geminimaliseerd, Azure Cosmos DB biedt bovendien een mechanisme voor automatische regionale failover die de continuïteit van uw toepassing garandeert in het zeldzame geval van een storing van de service in een bepaalde regio.

* **Snelle query's en traversals met de meest gebruikte grafiekquerystandaard**

  Sla heterogene hoekpunten en randen op en doorzoek deze met behulp van de vertrouwde Gremlin-syntaxis. Gremlin is een belangrijke, functionele querytaal die een uitgebreide interface beidt om algemene grafiekalgoritmen te implementeren.
  
  Azure Cosmos DB stelt u in staat om query's en traversals in realtime uit te voeren zonder dat u schemahints, secundaire indexen of weergaven hoeft op te geven. Meer informatie vindt u in [Querygrafieken met behulp van Gremlin](gremlin-support.md).

* **Volledig beheerde graafdatabase**

  Dankzij Azure Cosmos DB is het niet meer nodig om database- en machineresources te beheren. De meeste bestaande graafdatabaseplatformen zijn afhankelijk van de beperkingen van hun infrastructuur en vergen vaak een hoge mate van onderhoud om de werking ervan te kunnen blijven garanderen. 
  
  Omdat Cosmos DB een volledig beheerde service is, hoeven virtuele machines niet meer te worden beheerd, hoeft de runtimesoftware niet te worden bijgewerkt, hoeft sharding of replicatie niet te worden beheerd en hoeven er geen ingewikkelde upgrades van de gegevenslagen te worden uitgevoerd. Er wordt automatisch een back-up van elke grafiek gemaakt en de databases worden automatisch beveiligd tegen regionale fouten. Hierdoor kunnen ontwikkelaars zich volledig richten op het leveren van waardevolle toepassingen in plaats van het uitvoeren en beheren van hun graafdatabases. 

* **Automatisch indexeren**

  In Azure Cosmos DB worden standaard alle eigenschappen in de knooppunten (ook hoekpunten genoemd) en randen van de grafiek geïndexeerd. Er wordt geen schema verwacht of vereist en er hoeven geen secundaire indexen te worden gemaakt. Meer informatie over [indexeren in Azure Cosmos DB](./index-overview.md).

* **Compatibiliteit met Apache TinkerPop**

  Azure Cosmos DB biedt ondersteuning voor de [opensource Apache TinkerPop-standaard](https://tinkerpop.apache.org/). De Tinkerpop-standaard beschikt over een uitgebreid ecosysteem van toepassingen en bibliotheken die eenvoudig kunnen worden geïntegreerd met de Gremlin-API van Azure Cosmos DB.

* **Instelbare consistentieniveaus**

  Azure Cosmos DB biedt vijf goed gedefinieerde consistentieniveaus om de juiste balans te vinden tussen consistentie en prestaties voor uw toepassing. Voor query's en leesbewerkingen biedt Azure Cosmos DB vijf verschillende consistentieniveaus: sterk, gebonden-verouderd, sessie, consistent voorvoegsel en mogelijk. Op basis van deze gedetailleerde, goed gedefinieerde consistentieniveaus kunt u een goede balans vinden tussen de consistentie, beschikbaarheid en latentie. Meer informatie over [Instelbare gegevensconsistentieniveaus in Azure Cosmos DB](consistency-levels.md).

## <a name="scenarios-that-use-gremlin-api"></a>Scenario's waarin de Gremlin-API wordt gebruikt

Hier zijn enkele scenario's waarin ondersteuning voor grafieken in Azure Cosmos DB nuttig kan zijn:

* **Sociale netwerken/Customer 365**

  Door gegevens over uw klanten en hun interacties met andere personen te combineren kunt u een persoonlijke ervaring ontwikkelen, het gedrag van klanten voorspellen of personen in contact brengen met anderen die gelijksoortige interesses hebben. Azure Cosmos DB kan worden gebruikt om sociale netwerken te beheren en klantvoorkeuren en -gegevens bij te houden.

* **Aanbevelingsengines**

  Dit scenario wordt doorgaans gebruikt in de detailhandel. Door informatie over producten, gebruikers en interactie tussen gebruikers, zoals het kopen, zoeken of beoordelen van een item, te combineren, kunt u aangepaste aanbevelingen genereren. De lage latentie, elastische schaal en systeemeigen ondersteuning voor grafieken van Azure Cosmos DB is ideaal voor deze scenario's.

* **Georuimtelijk**

  In veel toepassingen voor telecommunicatie, logistiek en reisplanning moet vaak worden gezocht naar belangrijke locaties binnen een gebied, of moet de kortste/snelste route tussen twee locaties worden berekend. Azure Cosmos DB is zeer geschikt voor dit soort taken.

* **Internet of Things**

  Omdat het netwerk en de verbindingen tussen IoT-apparaten zijn gemodelleerd als een grafiek krijgt u een beter beeld van de status van uw apparaten en activa. U krijgt ook inzicht in hoe wijzigingen in één deel van het netwerk van invloed kunnen zijn op een ander deel.

## <a name="introduction-to-graph-databases"></a>Inleiding tot graafdatabases

Gegevens zoals deze in werkelijkheid voorkomen, zijn op natuurlijke wijze verbonden. Traditionele gegevensmodellering is gericht op het definiëren van afzonderlijke entiteiten en het berekenen van hun relaties in runtime. Hoewel dit model weliswaar enkele voordelen biedt, kan het beheer van uiterst verbonden gegevens een ware uitdaging vormen door alle beperkingen.  

De graafdatabasemethode is in plaats daarvan gebaseerd op permanente relaties in de opslaglaag, wat tot uiterst efficiënte ophaalbewerkingen van grafieken leidt. De Gremlin-API van Azure Cosmos DB biedt ondersteuning voor het [eigenschappengrafiekmodel](https://tinkerpop.apache.org/docs/current/reference/#intro).

### <a name="property-graph-objects"></a>Objecten van de eigenschappengrafiek

Een [eigenschappengrafiek](http://mathworld.wolfram.com/Graph.html) is een structuur die is samengesteld uit [hoekpunten](http://mathworld.wolfram.com/GraphVertex.html) en [randen](http://mathworld.wolfram.com/GraphEdge.html). Voor beide objecten kan een willekeurig aantal sleutel-waardeparen als eigenschappen worden gebruikt. 

* **Hoekpunten/knooppunten**: hoekpunten duiden afzonderlijke entiteiten aan, zoals een persoon, plaats of gebeurtenis.

* **Kanten/relaties**: Kanten duiden relaties tussen hoekpunten aan. Bijvoorbeeld, een persoon kent mogelijk een andere persoon, is betrokken bij een gebeurtenis, en is onlangs op een bepaalde locatie geweest.

* **Eigenschappen**: Eigenschappen geven informatie over de hoekpunten en kanten. Er kan een willekeurig aantal eigenschappen worden gebruikt in hoekpunten of randen en ze kunnen worden gebruikt voor het beschrijven en filteren van de objecten in een query. Voorbeeldeigenschappen omvatten een hoekpunt met een naam en een leeftijd, of een rand, die een tijdstempel en/of gewicht kan hebben.

* **Label**: een label is een naam of de id van een hoekpunt of een rand. Labels kunnen meerdere hoekpunten of randen samenbrengen, zodat alle hoekpunten/randen in een groep een bepaald label hebben. Een grafiek kan bijvoorbeeld meerdere hoekpunten met het label 'persoon' hebben.

Graafdatabases worden vaak opgenomen in de NoSQL- of niet-relationele databasecategorie, omdat deze niet afhankelijk zijn van een schema of beperkt gegevensmodel. Door dit ontbreken van een schema kunnen verbonden structuren natuurlijk en efficiënt worden gemodelleerd en opgeslagen.

### <a name="graph-database-by-example"></a>Grafiekendatabase op voorbeeld

Laten we een voorbeeldgrafiek gebruiken om te zien hoe query’s kunnen worden uitgedrukt in Gremlin. In de volgende afbeelding ziet u een zakelijke toepassing waarmee gegevens over gebruikers, interesses en apparaten worden beheerd, in de vorm van een grafiek.  

:::image type="content" source="./media/gremlin-support/sample-graph.png" alt-text="Voorbeelddatabase met personen, apparaten en interesses" border="false"::: 

Deze grafiek heeft de volgende types van *hoekpunten* (in Gremlin ook wel 'labels' genoemd):

* **Personen**: In de grafiek komen drie personen voor: Robin, Thomas en Ben
* **Interesses**: In dit voorbeeld is hun interesse voetbal
* **Apparaten**: De apparaten die de personen gebruiken
* **Besturingssystemen**: De besturingssystemen op de apparaten
* **Plaats**: De locaties van waaruit de apparaten worden geopend

We laten de relaties tussen deze entiteiten zien via de volgende *rand*-types:

* **Kent**: Bijvoorbeeld 'Thomas kent Robin'
* **Is geïnteresseerd in**: Geeft de interesses weer van de personen in de grafiek, bijvoorbeeld 'Ben is geïnteresseerd in voetbal'
* **RunsOS**: Op de laptop wordt het Windows-besturingssysteem uitgevoerd
* **Gebruikt**: Geeft aan welk apparaat een persoon gebruikt. Robin gebruikt bijvoorbeeld een Motorola-telefoon met serienummer 77
* **In**: Om de locatie van waaruit de apparaten worden geopend weer te geven

De Gremlin-console is een interactieve terminal die wordt aangeboden door de Apache TinkerPop. Via deze terminal kunt u werken met de grafiekgegevens. Meer informatie vindt u in het quickstartdocument over [hoe u de Gremlin-console kunt gebruiken](create-graph-gremlin-console.md). U kunt deze bewerkingen ook uitvoeren met behulp van Gremlin-stuurprogramma’s op een platform naar keuze (Java, Node.js, Python of NET). In de volgende voorbeelden ziet u hoe u query's kunt uitvoeren op deze grafiekgegevens met behulp van de Gremlin-console.

Eerst kijken we naar CRUD. Met de volgende Gremlin-instructie wordt het hoekpunt Thomas ingevoegd in de grafiek:

```java
:> g.addV('person').property('id', 'thomas.1').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44)
```

Vervolgens wordt met de volgende Gremlin-instructie de rand Kent ingevoegd tussen Thomas en Robin.

```java
:> g.V('thomas.1').addE('knows').to(g.V('robin.1'))
```

Met de volgende query worden de hoekpunten voor Persoon in aflopende volgorde van de voornamen geretourneerd:

```java
:> g.V().hasLabel('person').order().by('firstName', decr)
```

Waar grafieken in uitblinken is in het beantwoorden van vragen zoals: Welke besturingssystemen gebruiken de vrienden van Thomas? U kunt deze Gremlin-traversal uitvoeren om deze gegevens op te halen uit de grafiek:

```java
:> g.V('thomas.1').out('knows').out('uses').out('runsos').group().by('name').by(count())
```

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over ondersteuning voor grafieken in Azure Cosmos DB raadpleegt u:

* Aan de slag met de [zelfstudie voor grafieken in Azure Cosmos DB](create-graph-dotnet.md).
* Meer informatie over het [doorzoeken van grafieken in Azure Cosmos DB met behulp van Gremlin](gremlin-support.md).
