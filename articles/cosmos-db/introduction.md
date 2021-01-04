---
title: Inleiding tot Azure Cosmos DB
description: Lees hier alles over Azure Cosmos DB. Deze wereldwijd gedistribueerde database met meerdere modellen is gebouwd voor lage latentie, elastische schaalbaarheid, hoge beschikbaarheid en voor het bieden van systeemeigen ondersteuning voor NoSQL-gegevens.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: overview
ms.date: 10/23/2020
ms.openlocfilehash: 2dd60d1962734f7a4264587fd1b5b0d2a03c9ff0
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359741"
---
# <a name="welcome-to-azure-cosmos-db"></a>Welkom bij Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Van toepassingen wordt tegenwoordig vereist dat ze zeer responsief en altijd online zijn. Voor het bereiken van lage latentie en hoge beschikbaarheid moeten de instanties van deze toepassingen worden geïmplementeerd in datacenters die zich dicht bij de gebruikers ervan bevinden. Toepassingen moeten in realtime reageren op grote fluctuaties in het gebruik tijdens piekuren, moeten steeds toenemende gegevensvolumes kunnen opslaan en deze gegevens in milliseconden beschikbaar kunnen maken voor gebruikers.

Azure Cosmos DB is een volledig beheerde NoSQL-database voor de ontwikkeling van moderne apps. Reactietijden van slechts enkele milliseconden, en automatische en directe schaalbaarheid, garanderen snelheid op elke schaal. De bedrijfscontinuïteit is gewaarborgd met [door SLA ondersteunde](https://azure.microsoft.com/support/legal/sla/cosmos-db) beschikbaarheid en beveiliging op bedrijfsniveau. App-ontwikkeling is sneller en productiever dankzij kant-en-klare gegevensdistributie voor meerdere regio's, opensource-API's en SDK's voor populaire talen. Als volledig beheerde service neemt Azure Cosmos DB u het databasebeheer volledig uit handen met automatisch beheer, updates en patches. Daarnaast wordt capaciteitsbeheer afgehandeld met rendabele opties voor serverloze en automatische schaling die inspelen op de behoeften van de toepassing om de capaciteit af te stemmen op de vraag.

U kunt [Azure Cosmos DB gratis proberen](https://azure.microsoft.com/try/cosmosdb/) zonder een Azure-abonnement en geheel kosteloos en zonder verplichtingen, of de [gratis Azure Cosmos DB-laag](optimize-dev-test.md#azure-cosmos-db-free-tier) gebruiken om een account te maken waarbij de eerste 400 RU/s en 5 GB aan opslagruimte gratis zijn.

> [!div class="nextstepaction"]
> [Azure Cosmos DB gratis uitproberen](https://azure.microsoft.com/try/cosmosdb/)

:::image type="content" source="./media/introduction/azure-cosmos-db.png" alt-text="Azure Cosmos DB is een volledig beheerde NoSQL-database voor de ontwikkeling van moderne apps." border="false":::

## <a name="key-benefits"></a>Belangrijkste voordelen

### <a name="guaranteed-speed-at-any-scale"></a>Gegarandeerde snelheid op elke schaal

Profiteer van ongeëvenaarde, [door SLA ondersteunde](https://azure.microsoft.com/support/legal/sla/cosmos-db) snelheid en doorvoer, snelle wereldwijde toegang en directe elasticiteit.

- Realtime-toegang met wereldwijde snelle lees- en schrijflatenties, en doorvoer en consistentie, allemaal ondersteund door [SLA's](https://azure.microsoft.com/support/legal/sla/cosmos-db)
- Schrijfbewerkingen en gegevensdistributie in elke Azure-regio met één klik op de knop.
- Opslag en doorvoer in elke Azure-regio onafhankelijk en elastisch schalen, zelfs tijdens onvoorspelbare verkeerspieken, voor een onbeperkte wereldwijde schaalbaarheid.

### <a name="simplified-application-development"></a>Vereenvoudigde toepassingsontwikkeling

Snel bouwen met open source-API's, meerdere SDK's, schemaloze gegevens en analyses zonder ETL van operationele gegevens.

- Diep geïntegreerd met de belangrijkste Azure-services die worden gebruikt in moderne (cloudeigen) toepassingsontwikkeling, waaronder Azure Functions, IoT Hub, AKS (Azure Kubernetes Service), App Service en meer.
- U kunt kiezen uit meerdere database-API's, waaronder de systeemeigen Core (SQL)-API, API voor MongoDB, Cassandra-API, Gremlin-API en Table-API.
- Apps bouwen op Core (SQL)-API met behulp van de talen van uw keuze met SDK's voor .NET, Java, Node.js en Python. Of uw keuze van stuurprogramma's voor een van de andere database-API's.
- Analyses zonder ETL uitvoeren via de bijna realtime operationele gegevens die zijn opgeslagen in Azure Cosmos DB met Azure Synapse Analytics.
- Met wijzigingenfeed kunt u eenvoudig wijzigingen in databasecontainers bijhouden en beheren en geactiveerde gebeurtenissen maken met Azure Functions.
- Met de schemaloze service van Azure Cosmos DB worden automatisch al uw gegevens geïndexeerd, ongeacht het gegevensmodel, om razendsnelle query's te leveren.

### <a name="mission-critical-ready"></a>Essentieel gereed

Gegarandeerde bedrijfscontinuïteit, 99,999% beschikbaarheid en beveiliging op bedrijfsniveau voor elke toepassing.

- Azure Cosmos DB biedt een uitgebreid pakket [SLA's](https://azure.microsoft.com/support/legal/sla/cosmos-db), inclusief toonaangevende beschikbaarheid wereldwijd.
- Eenvoudig gegevens naar elke Azure-regio distribueren met automatische gegevensreplicatie. Profiteer van geen downtime met schrijfbewerkingen in meerdere regio's of RPO 0 bij het gebruik van Sterke consistentie.
- Profiteer van versleuteling in rust op bedrijfsniveau met zelfbeheerde sleutels.
- Op rollen gebaseerd toegangsbeheer in Azure houdt uw gegevens veilig en biedt nauwkeurig afgestemd beheer.

### <a name="fully-managed-and-cost-effective"></a>Volledig beheerd en rendabel

End-to-end databasebeheer, met serverloze en automatische schaling die overeenkomt met uw toepassingen en TCO-behoeften

- Volledig beheerde databaseservice. Automatisch onderhoud, patches en updates, zonder iets te hoeven aanraken, bespaart ontwikkelaars tijd en geld.
- Rendabele opties voor onvoorspelbare of sporadische werkbelastingen van elke grootte of schaal, waardoor ontwikkelaars gemakkelijk aan de slag kunnen, zonder dat ze capaciteit hoeven te plannen of beheren.
- Het serverloze model biedt een automatische en responsieve service voor werkbelastingen met pieken voor het beheren van verkeerspieken op aanvraag.
- Ingerichte doorvoer met automatische schaalaanpassing schaalt automatisch en direct de capaciteit voor onvoorspelbare werkbelastingen, terwijl [SLA’s](https://azure.microsoft.com/support/legal/sla/cosmos-db) worden behouden.

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Oplossingen die profiteren van Azure Cosmos DB

Alle [toepassingen voor web, mobiel, games en IoT](use-cases.md) die op [wereldwijde](distribute-data-globally.md) schaal en met zeer snelle reactietijden zeer grote hoeveelheden gegevens en lees- en schrijfbewerkingen moeten verwerken voor een verscheidenheid aan gegevens, profiteren van de [gegarandeerde](https://azure.microsoft.com/support/legal/sla/cosmos-db/) hoge beschikbaarheid, hoge doorvoer, lage latentie en instelbare consistentie van Cosmos DB. Kom meer te weten over hoe Cosmos DB kan worden gebruikt om toepassingen voor [IoT en telematica](use-cases.md#iot-and-telematics), [detailhandel en marketing](use-cases.md#retail-and-marketing), [gaming](use-cases.md#gaming) en [web en mobiel](use-cases.md#web-and-mobile-applications) te maken.

## <a name="next-steps"></a>Volgende stappen

Lees onze snelstartgidsen om snel aan de slag te gaan met Azure Cosmos DB:

- [Aan de slag met de SQL-API van Azure Cosmos DB](create-sql-api-dotnet.md)
- [Aan de slag met de API van Azure Cosmos DB voor MongoDB](create-mongodb-nodejs.md)
- [Aan de slag met de Cassandra-API van Azure Cosmos DB](create-cassandra-dotnet.md)
- [Aan de slag met de Gremlin-API van Azure Cosmos DB](create-graph-dotnet.md)
- [Aan de slag met de Table-API van Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Azure Cosmos DB gratis uitproberen](https://azure.microsoft.com/try/cosmosdb/)