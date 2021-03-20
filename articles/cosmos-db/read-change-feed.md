---
title: Wijzigingenfeed in Azure Cosmos DB lezen
description: In dit artikel worden verschillende opties beschreven die beschikbaar zijn voor lees-en toegangs wijzigings invoer in Azure Cosmos DB.
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/27/2020
ms.reviewer: sngun
ms.openlocfilehash: 7021367e1230573343ddf57ccd399d998ad5280e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93339271"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Wijzigingenfeed in Azure Cosmos DB lezen
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

U kunt met de Azure Cosmos DB wijzigings feed werken met een push model of een pull-model. Met een push-model werkt de wijzigings processor pushes naar een client met bedrijfs logica voor de verwerking van dit werk. De complexiteit van het controleren op werk en het opslaan van de status voor het laatst verwerkte werk wordt echter verwerkt in de processor voor wijzigings invoer.

Met een pull-model moet de client het werk ophalen van de-server. De client heeft in dit geval niet alleen bedrijfs logica voor de verwerking van werk, maar ook de status voor het laatst verwerkte werk, verwerking van de taak verdeling over meerdere verwerkte clients parallel en het afhandelen van fouten.

Bij het lezen van de Azure Cosmos DB wijzigings feed raden wij u aan een push model te gebruiken omdat u zich geen zorgen hoeft te maken over:

- De wijzigings feed wordt gecontroleerd op toekomstige wijzigingen.
- Opslag status voor de laatst verwerkte wijziging. Bij het lezen van de wijzigings feed wordt dit automatisch opgeslagen in een [lease-container](change-feed-processor.md#components-of-the-change-feed-processor).
- Taak verdeling over meerdere clients waarbij wijzigingen worden verbruikt. Als een client bijvoorbeeld geen wijzigingen kan aanbrengen in de verwerking, en een andere een beschik bare capaciteit heeft.
- Het [afhandelen van fouten](change-feed-processor.md#error-handling). Bijvoorbeeld: automatisch opnieuw proberen van mislukte wijzigingen die niet juist zijn verwerkt na een niet-verwerkte uitzonde ring in code of een tijdelijk netwerk probleem.

De meeste scenario's die gebruikmaken van de Azure Cosmos DB Change feed, gebruiken een van de opties voor het push model. Er zijn echter enkele scenario's waarin u de extra controle op laag niveau van het pull-model mogelijk wilt maken. Deze omvatten:

- Wijzigingen van een bepaalde partitie sleutel lezen
- Het tempo bepalen waarmee uw client wijzigingen voor verwerking ontvangt
- Een eenmalige het lezen van de bestaande gegevens in de feed (bijvoorbeeld om een gegevens migratie uit te voeren)

## <a name="reading-change-feed-with-a-push-model"></a>Wijzigings feed lezen met een push model

Het gebruik van een push model is de eenvoudigste manier om de wijzigings feed te lezen. Er zijn twee manieren om te lezen van de wijzigings feed met een push model: [Azure Functions Cosmos DB triggers](change-feed-functions.md) en de [bibliotheek met wijzigings doorvoer processor](change-feed-processor.md). Azure Functions maakt gebruik van de processor voor wijzigings invoer achter de schermen. Dit zijn beide op dezelfde manier om de wijzigings feed te lezen. U kunt Azure Functions beschouwen als een hosting platform voor de Change feed-processor, niet op een volledig andere manier om de wijzigings feed te lezen.

### <a name="azure-functions"></a>Azure Functions

Azure Functions is de eenvoudigste optie als u zojuist aan de slag gaat met het wijzigen van de feed. Als gevolg van de eenvoud is het ook de aanbevolen optie voor het gebruik van de meeste cases voor wijzigings invoer. Wanneer u een Azure Functions trigger voor Azure Cosmos DB maakt, selecteert u de container om verbinding te maken en wordt de functie Azure geactiveerd wanneer er een wijziging in de container optreedt. Omdat Azure Functions de Change feed-processor achter de schermen gebruikt, wordt parallelizes automatisch gewijzigd in de [partities](partitioning-overview.md)van de container.

Ontwikkelen met Azure Functions is een eenvoudige ervaring en kan sneller zijn dan de implementatie van de Change feed-processor zelf. Triggers kunnen worden gemaakt met behulp van de Azure Functions portal of programmatisch met behulp van Sdk's. Visual Studio en VS code bieden ondersteuning voor het schrijven van Azure Functions, en u kunt zelfs de Azure Functions CLI gebruiken voor het ontwikkelen van meerdere platforms. U kunt de code op uw bureau blad schrijven en fouten opsporen en de functie vervolgens met één klik implementeren. Zie [serverloze database Computing met Azure functions](serverless-computing-database.md) en [gebruik change feed met Azure functions](change-feed-functions.md) artikelen voor meer informatie.

### <a name="change-feed-processor-library"></a>De processor bibliotheek voor feeds wijzigen

De Change feed-processor geeft u meer controle over de wijzigings feed en verbergt de meeste complexiteit. De processor bibliotheek voor wijzigings invoer volgt het waarnemers patroon, waarbij uw verwerkings functie wordt aangeroepen door de bibliotheek. De processor bibliotheek van de wijzigings feed controleert automatisch op wijzigingen en, als er wijzigingen worden gevonden, wordt deze naar de client gepusht. Als u een wijzigings feed voor hoge door Voer hebt, kunt u meerdere clients instantiëren om de wijzigings feed te lezen. De processor bibliotheek van de wijzigings feed onderverdelen automatisch de belasting tussen de verschillende clients. U hoeft geen logica te implementeren voor taak verdeling over meerdere clients of enige logica voor het onderhouden van de lease status.

De processor bibliotheek voor wijzigings invoer garandeert een ' mini maal één keer ' van alle wijzigingen. Met andere woorden, als u de bibliotheek van de Change feed-processor gebruikt, wordt de verwerkings functie voor elk item in de wijzigings feed met succes aangeroepen. Als er sprake is van een onverwerkte uitzonde ring in de bedrijfs logica in uw verwerkings functie, worden de mislukte wijzigingen opnieuw geprobeerd totdat ze met succes zijn verwerkt. Als u wilt voor komen dat de processor voor wijzigings invoer voortdurend opnieuw wordt uitgevoerd, voegt u logica toe aan de verwerkings functie om documenten te schrijven, na uitzonde ring, naar een wachtrij met onbestelbare berichten. Meer informatie over het [afhandelen van fouten](change-feed-processor.md#error-handling).

In Azure Functions is de aanbeveling voor het afhandelen van fouten hetzelfde. U moet nog steeds logica in uw code van de gemachtigde toevoegen om documenten te schrijven, bij uitzonde ring naar een wachtrij met onbestelbare berichten. Als er echter een onverwerkte uitzonde ring in uw Azure-functie is, wordt de wijziging die de uitzonde ring heeft gegenereerd, niet automatisch opnieuw geprobeerd. Als er sprake is van een onverwerkte uitzonde ring in de bedrijfs logica, wordt de Azure-functie verplaatst om de volgende wijziging te verwerken. De Azure-functie voert niet dezelfde mislukte wijziging opnieuw uit.

Net als bij Azure Functions is het eenvoudig om te ontwikkelen met de processor bibliotheek voor wijzigings invoer. U bent echter zelf verantwoordelijk voor het implementeren van een of meer hosts voor de Change feed-processor. Een host is een toepassings exemplaar dat de feed-processor van de wijziging gebruikt om te Luis teren naar wijzigingen. Hoewel Azure Functions mogelijkheden heeft voor automatisch schalen, bent u verantwoordelijk voor het schalen van uw hosts. Zie [de Change feed-processor gebruiken](change-feed-processor.md#dynamic-scaling)voor meer informatie. De processor bibliotheek voor wijzigings invoer maakt deel uit van de [Azure Cosmos DB SDK v3](https://github.com/Azure/azure-cosmos-dotnet-v3).

## <a name="reading-change-feed-with-a-pull-model"></a>Wijzigings feed lezen met een pull-model

Met het [pull-model wijzigings invoer](change-feed-pull-model.md) kunt u de wijzigings feed in uw eigen tempo gebruiken. Wijzigingen moeten door de client worden aangevraagd en er is geen automatische polling voor wijzigingen. Als u de laatste verwerkte wijziging (vergelijkbaar met de lease-container van het push model) permanent wilt ' blad ', moet u [een vervolg token opslaan](change-feed-pull-model.md#saving-continuation-tokens).

Met het pull-model wijzigings invoer krijgt u meer controle over het beheer op laag niveau van de wijzigings feed. Bij het lezen van de wijzigings feed met het pull-model hebt u drie opties:

- Wijzigingen voor een volledige container lezen
- Wijzigingen voor een specifieke [FeedRange](change-feed-pull-model.md#using-feedrange-for-parallelization) lezen
- Wijzigingen voor een specifieke partitie sleutel waarde lezen

U kunt de verwerking van wijzigingen op meerdere clients parallelliseren, net zoals u dat kunt doen met de Change feed-processor. Het pull-model verwerkt echter niet automatisch taak verdeling over clients. Wanneer u het pull-model gebruikt om de parallelliseren verwerking van de wijzigings feed te verwisselen, krijgt u eerst een lijst met FeedRanges. Een FeedRange omvat een bereik van partitie sleutel waarden. U moet een Orchestrator-proces hebben dat FeedRanges verkrijgt en distribueert naar uw machines. U kunt deze FeedRanges vervolgens gebruiken om meerdere machines de wijzigings feed parallel te laten lezen.

Er is geen ingebouwde leverings garantie van ' mini maal één keer ' met het pull-model. Het pull-model biedt u de controle op laag niveau om te bepalen hoe u fouten wilt afhandelen.

> [!NOTE]
> Het pull-model voor de wijzigings feed is momenteel in [de preview-versie van de Azure Cosmos db .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.15.0-preview) . De preview-versie is nog niet beschikbaar voor andere SDK-versies.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Feed wijzigen in Api's voor Cassandra en MongoDB

De functionaliteit voor het wijzigen van de feed wordt geoppereerd als een wijzigings stroom in de MongoDB-API en query met een predikaat in Cassandra-API. Zie voor meer informatie over de implementatie Details voor MongoDB API de [wijzigings stromen in de Azure Cosmos DB-API voor MongoDb](mongodb-change-streams.md).

Systeem eigen Apache Cassandra biedt change data capture (CDC), een mechanisme voor het markeren van specifieke tabellen voor archivering en het afwijzen van schrijf bewerkingen naar die tabellen wanneer een Configureer bare grootte-op-schijf voor het CDC-logboek wordt bereikt. De functie voor het wijzigen van de feed in Azure Cosmos DB-API voor Cassandra verbetert de mogelijkheid om query's uit te voeren op de wijzigingen met een predikaat via CQL. Zie [Change feed in de Azure Cosmos DB-API voor Cassandra voor](cassandra-change-feed.md)meer informatie over de implementatie details.

## <a name="next-steps"></a>Volgende stappen

U kunt nu door gaan met meer informatie over wijzigings invoer in de volgende artikelen:

* [Overzicht van wijzigings feed](change-feed.md)
* [Change feed gebruiken met Azure Functions](change-feed-functions.md)
* [De processor bibliotheek voor Change feed gebruiken](change-feed-processor.md)
