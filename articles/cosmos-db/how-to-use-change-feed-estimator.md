---
title: De wijzigings feed gebruiken Estimator-Azure Cosmos DB
description: Meer informatie over het gebruik van de Change feed Estimator voor het analyseren van de voortgang van de processor voor wijzigings invoer
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 04/01/2021
ms.author: maquaran
ms.custom: devx-track-csharp
ms.openlocfilehash: 5d4e461b25a25ecdf0d4d89ee7f1c82b9d4a0737
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220161"
---
# <a name="use-the-change-feed-estimator"></a>De Estimator van de wijzigings feed gebruiken
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In dit artikel wordt beschreven hoe u de voortgang van uw [feeds voor wijzigings](./change-feed-processor.md) instanties kunt bewaken terwijl de wijzigings feed wordt gelezen.

## <a name="why-is-monitoring-progress-important"></a>Waarom is de bewakings voortgang belang rijk?

De wijzigings verwerkings processor fungeert als een pointer die door uw [wijzigings feed](./change-feed.md) doorloopt en de wijzigingen voor de implementatie van een gemachtigde bezorgt.

De implementatie van de wijzigings feed-processor kan wijzigingen verwerken met een bepaald percentage op basis van de beschik bare resources zoals CPU, geheugen, netwerk, enzovoort.

Als dit percentage langzamer is dan de snelheid waarmee uw wijzigingen worden aangebracht in de Azure Cosmos-container, begint de processor met de vertraging.

Door dit scenario te identificeren, kunt u beter begrijpen of we de implementatie van de wijzigings feed moeten schalen.

## <a name="implement-the-change-feed-estimator"></a>De Estimator voor de wijzigings feed implementeren

### <a name="as-a-push-model-for-automatic-notifications"></a>Als push model voor automatische meldingen

Net als de [Change feed-processor](./change-feed-processor.md)kan de wijzigings Estimator als een push model werken. In het Estimator wordt het verschil gemeten tussen het laatst verwerkte item (gedefinieerd door de status van de leases-container) en de laatste wijziging in de container, en deze waarde naar een gemachtigde pushen. Het interval waarmee de meting wordt uitgevoerd, kan ook worden aangepast met een standaard waarde van 5 seconden.

Als u bijvoorbeeld de processor voor de wijzigings feed als volgt definieert:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartProcessorEstimator)]

De juiste manier om een Estimator te initialiseren om te meten dat de processor `GetChangeFeedEstimatorBuilder` als zodanig wordt gebruikt:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartEstimator)]

Waarbij zowel de processor als de Estimator dezelfde `leaseContainer` en dezelfde naam hebben.

De andere twee para meters zijn de gemachtigde. er wordt dan een getal weer gegeven dat aangeeft hoeveel **wijzigingen in behandeling moeten worden gelezen** door de processor en het tijds interval waarop deze meting moet worden uitgevoerd.

Een voor beeld van een gemachtigde die de schatting ontvangt, is:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=EstimationDelegate)]

U kunt deze schatting verzenden naar uw bewakings oplossing en deze gebruiken om te begrijpen hoe de voortgang in de loop van de tijd gedraagt.

### <a name="as-an-on-demand-detailed-estimation"></a>Als een gedetailleerde schatting op aanvraag

In tegens telling tot het push model is er een alternatief waarmee u de schatting op aanvraag kunt verkrijgen. Dit model bevat ook meer gedetailleerde informatie:

* De geschatte vertraging per lease.
* Het exemplaar dat eigenaar is van elke lease en de verwerking ervan, zodat u kunt vaststellen of er een probleem is met een exemplaar.

Als de processor voor de wijzigings feed als volgt is gedefinieerd:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartProcessorEstimatorDetailed)]

U kunt de Estimator maken met dezelfde lease configuratie:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartEstimatorDetailed)]

En wanneer u dat wilt, met de frequentie die u nodig hebt, kunt u de gedetailleerde schatting verkrijgen:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=GetIteratorEstimatorDetailed)]

Elk `ChangeFeedProcessorState` bevat de informatie over de lease en de vertraging en ook van wie de huidige instantie eigenaar is. 

> [!NOTE]
> De Estimator van de wijzigings feed hoeft niet te worden geïmplementeerd als onderdeel van de processor voor wijzigings invoer en niet deel uitmaken van hetzelfde project. Het kan onafhankelijk zijn en worden uitgevoerd in een volledig ander exemplaar, dat wordt aanbevolen. U hoeft alleen dezelfde naam en lease configuratie te gebruiken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Voor beelden van gebruik op GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Aanvullende voor beelden op GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Volgende stappen

U kunt nu door gaan met meer informatie over het wijzigen van de feed-processor in de volgende artikelen:

* [Overzicht van de processor voor wijzigings invoer](change-feed-processor.md)
* [Starttijd van verwerker van wijzigingenfeed](./change-feed-processor.md#starting-time)