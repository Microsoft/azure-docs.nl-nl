---
title: Processor voor wijzigingenfeed in Azure Cosmos DB
description: Meer informatie over het gebruik van de Azure Cosmos DB Change feed-processor om de wijzigings feed, de onderdelen van de wijzigings feed-processor te lezen
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 409b51682700a8b13b2840f171642bdcbee6f6d2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93340223"
---
# <a name="change-feed-processor-in-azure-cosmos-db"></a>Processor voor wijzigingenfeed in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

De Change feed-processor maakt deel uit van de [Azure Cosmos DB SDK v3](https://github.com/Azure/azure-cosmos-dotnet-v3). Het vereenvoudigt het proces van het lezen van de wijzigings feed en het distribueren van de gebeurtenis verwerking over meerdere gebruikers effectief.

Het belangrijkste voor deel van de processor bibliotheek voor wijzigings invoer is het probleem tolerant gedrag dat ervoor zorgt dat alle gebeurtenissen in de feed ten minste één keer worden bezorgd.

## <a name="components-of-the-change-feed-processor"></a>Onderdelen van de processor voor wijzigings invoer

Er zijn vier belangrijke onderdelen bij de implementatie van de wijzigingenfeedverwerker:

1. **De bewaakte container:** De bewaakte container bevat de gegevens waaruit de wijzigingenfeed wordt gegenereerd. Eventuele toevoegingen en updates van de bewaakte container worden weergegeven in de wijzigingenfeed van de container.

1. **De leasecontainer:** De leasecontainer fungeert als een statusopslag en coördineert de wijzigingenfeed voor meerdere werkrollen. De leasecontainer kan worden opgeslagen in hetzelfde account als de bewaakte container of in een afzonderlijk account.

1. **De host:** Een host is een toepassingsexemplaar dat de wijzigingenfeedverwerker gebruikt om naar wijzigingen te luisteren. Er kunnen meerdere exemplaren met dezelfde leaseconfiguratie parallel worden uitgevoerd, maar elk exemplaar moet een andere **naam hebben**.

1. **De gemachtigde:** De gemachtigde is de code die definieert wat u, de ontwikkelaar, wilt doen met elke batch met wijzigingen die door de wijzigingenfeedverwerker wordt verwerkt. 

We kijken naar een voor beeld in het volgende diagram om meer inzicht te krijgen in de manier waarop deze vier elementen van de feed-processor samen werken. In de bewaakte container worden documenten opgeslagen en wordt ' City ' gebruikt als partitie sleutel. We zien dat de partitie sleutel waarden worden gedistribueerd in bereiken die items bevatten. Er zijn twee exemplaren van hosts en de wijzigings processor wordt toegewezen aan verschillende bereiken van partitie sleutel waarden voor elke instantie om de reken distributie te maximaliseren. Elk bereik wordt parallel gelezen en de voortgang wordt afzonderlijk van andere bereiken in de lease-container bewaard.

:::image type="content" source="./media/change-feed-processor/changefeedprocessor.png" alt-text="Voor beeld van een feed-processor wijzigen" border="false":::

## <a name="implementing-the-change-feed-processor"></a>De processor voor de wijzigings feed implementeren

Het gegeven punt is altijd de bewaakte container, van een `Container` instantie die u aanroept `GetChangeFeedProcessorBuilder` :

[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=DefineProcessor)]

Waarbij de eerste para meter een unieke naam is die het doel van deze processor beschrijft, en de tweede naam is de gemachtigde-implementatie die wijzigingen verwerkt. 

Een voor beeld van een gemachtigde is:


[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=Delegate)]

Ten slotte definieert u een naam voor dit processor exemplaar met `WithInstanceName` en dit is de container waarin de lease status moet worden onderhouden `WithLeaseContainer` .

Met aanroepen krijgt `Build` u het processor exemplaar dat u kunt starten door aan te roepen `StartAsync` .

## <a name="processing-life-cycle"></a>Levens cyclus verwerken

De normale levenscyclus van een host-exemplaar is:

1. Lees de wijzigings feed.
1. Als er geen wijzigingen zijn, schakelt u de slaap stand in voor een vooraf gedefinieerde hoeveelheid tijd (aanpasbaar met `WithPollInterval` in de opbouw functie) en gaat u naar #1.
1. Als er wijzigingen zijn, stuurt u deze naar de **gemachtigde**.
1. Wanneer de gemachtigde de verwerking van de wijzigingen **heeft** voltooid, werkt u de lease Store bij met het meest recente verwerkte punt in de tijd en gaat u naar #1.

## <a name="error-handling"></a>Foutafhandeling

De Change feed-processor is robuust voor gebruikers code fouten. Dit betekent dat als de implementatie van de gemachtigde een niet-verwerkte uitzonde ring heeft (stap #4), de thread verwerking die door de desbetreffende batch wijzigingen wordt uitgevoerd, wordt gestopt en er een nieuwe thread wordt gemaakt. In de nieuwe thread wordt gecontroleerd welk punt in de tijd dat de lease-opslag het meest recent is voor dat bereik van partitie sleutel waarden en opnieuw wordt opgestart, waardoor dezelfde batch met wijzigingen in de gemachtigde effectief wordt verzonden. Dit gedrag wordt voortgezet totdat de gemachtigde de wijzigingen correct verwerkt en de reden is dat de wijzigings processor ten minste eenmaal is gegarandeerd, omdat als de code van de gemachtigde een uitzonde ring genereert, wordt de batch opnieuw geprobeerd.

Als u wilt voor komen dat de processor voor wijzigings invoer voortdurend opnieuw probeert dezelfde batch met wijzigingen aan te brengen, moet u logica toevoegen aan de code van de gemachtigde om documenten te schrijven, na uitzonde ring, naar een wachtrij met onbestelbare berichten. Met dit ontwerp zorgt u ervoor dat u niet-verwerkte wijzigingen kunt bijhouden, terwijl u toekomstige wijzigingen nog steeds kunt blijven verwerken. De wachtrij met onbestelbare berichten kan een andere Cosmos-container zijn. Het exacte gegevens archief is niet van belang, alleen dat de niet-verwerkte wijzigingen behouden blijven.

Daarnaast kunt u de [wijzigings feed-Estimator](how-to-use-change-feed-estimator.md) gebruiken om de voortgang van de wijzigingen in de feeder-processor te controleren wanneer ze de wijzigings feed lezen. U kunt deze schatting gebruiken om te begrijpen of uw Change feed-processor ' vastgelopen ' is of achtergebleven als gevolg van de beschik bare bronnen zoals CPU, geheugen en netwerk bandbreedte.

## <a name="deployment-unit"></a>Implementatie-eenheid

Een enkele wijzigings eenheid voor het wijzigen van een feed bestaat uit een of meer exemplaren met dezelfde `processorName` en een lease-container configuratie. U kunt veel implementatie-eenheden hebben waarbij elk een andere bedrijfs stroom heeft voor de wijzigingen en elke implementatie-eenheid die bestaat uit een of meer instanties. 

U kunt bijvoorbeeld één implementatie-eenheid hebben die een externe API activeert, op het moment dat er een wijziging in uw container optreedt. Een andere implementatie-eenheid kan gegevens in realtime verplaatsen, telkens wanneer er een wijziging is. Wanneer er een wijziging in de bewaakte container optreedt, krijgen alle implementatie-eenheden een melding.

## <a name="dynamic-scaling"></a>Dynamische schaalbaarheid

Zoals eerder vermeld, binnen een implementatie-eenheid, kunt u een of meer exemplaren hebben. Om te profiteren van de reken distributie binnen de implementatie-eenheid, zijn de enige vereisten voor de sleutel:

1. Alle instanties moeten dezelfde configuratie voor de leasecontainer hebben.
1. Alle exemplaren moeten hetzelfde hebben `processorName` .
1. Elk exemplaar moet een andere naam hebben (`WithInstanceName`).

Als deze drie voor waarden van toepassing zijn, wordt met behulp van een even distributie algoritme alle leases in de lease container gedistribueerd op alle actieve instanties van die implementatie-eenheid en parallelliseren compute. Een lease kan alleen eigendom zijn van één exemplaar op een bepaald moment, waardoor het maximum aantal exemplaren gelijk is aan het aantal leases.

Het aantal exemplaren kan worden verg root of verkleind, en de wijzigings processor past de belasting dynamisch aan door dienovereenkomstig te distribueren.

Daarnaast kan de wijzigings processor van de feed dynamisch worden aangepast aan de schaal van containers als gevolg van de door Voer of opslag toeneemt. Wanneer uw container groeit, verwerkt de wijzigings verwerkings processor deze scenario's transparant door de leases dynamisch te verhogen en de nieuwe leases te verdelen over bestaande instanties.

## <a name="change-feed-and-provisioned-throughput"></a>Feed en ingerichte door Voer wijzigen

De Lees bewerkingen voor feeds in de bewaakte container gebruiken RUs. 

Bewerkingen op de lease container gebruiken RUs. Hoe hoger het aantal exemplaren is dat dezelfde lease container gebruikt, des te hoger het potentiële RU-verbruik zal zijn. Vergeet niet om uw RU-verbruik op de leases-container te bewaken als u besluit om het aantal exemplaren te schalen en uit te breiden.

## <a name="starting-time"></a>Begin tijd

Wanneer een wijzigings verwerkings processor de eerste keer wordt gestart, wordt de-container voor leases standaard geïnitialiseerd en wordt de [levens cyclus](#processing-life-cycle)van de verwerking gestart. Eventuele wijzigingen die zich hebben voorgedaan in de bewaakte container voordat de Change feed-processor voor de eerste keer werd geïnitialiseerd, worden niet gedetecteerd.

### <a name="reading-from-a-previous-date-and-time"></a>Lezen van een vorige datum en tijd

Het is mogelijk om de Change feed-processor te initialiseren om wijzigingen te lezen die beginnen op een **specifieke datum en tijd**, door een exemplaar van een `DateTime` te geven aan de `WithStartTime` uitbrei ding van de opbouw functie:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=TimeInitialization)]

De Change feed-processor wordt geïnitialiseerd voor die specifieke datum en tijd en begint met het lezen van de wijzigingen die zijn opgetreden.

### <a name="reading-from-the-beginning"></a>Lezen vanaf het begin

In andere scenario's, zoals gegevens migraties of het analyseren van de volledige geschiedenis van een container, moeten we de wijzigings feed lezen vanaf **het begin van de levens duur van die container**. Hiervoor kunnen we gebruiken `WithStartTime` op de uitbrei ding van de opbouw functie, maar door geven `DateTime.MinValue.ToUniversalTime()` , waardoor de UTC-weer gave van de minimum `DateTime` waarde zou worden gegenereerd, zoals:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartFromBeginningInitialization)]

De Change feed-processor wordt geïnitialiseerd en begint met het lezen van wijzigingen aan het begin van de levens duur van de container.

> [!NOTE]
> Deze aanpassings opties werken alleen voor het instellen van het begin punt in de tijd van de processor voor wijzigings invoer. Als de container leases voor de eerste keer wordt geïnitialiseerd, heeft het wijzigen van de containers geen effect.

## <a name="where-to-host-the-change-feed-processor"></a>De locatie van de feed-processor hosten

De Change feed-processor kan worden gehost in elk platform dat ondersteuning biedt voor langlopende processen of taken:

* Een continue uitvoering van [Azure-Webtaak](/learn/modules/run-web-app-background-task-with-webjobs/).
* Een proces op een [virtuele machine van Azure](/azure/architecture/best-practices/background-jobs#azure-virtual-machines).
* Een achtergrond taak in de [Azure Kubernetes-service](/azure/architecture/best-practices/background-jobs#azure-kubernetes-service).
* Een [ASP.net-gehoste service](/aspnet/core/fundamentals/host/hosted-services).

Hoewel de feed-processor in korte Bewaar omgevingen kan worden uitgevoerd, omdat de lease container de status behoudt, zal de opstart cyclus van deze omgevingen vertraging oplopen om de meldingen te ontvangen (als gevolg van de overhead van het starten van de processor telkens wanneer de omgeving wordt gestart).

## <a name="additional-resources"></a>Aanvullende bronnen

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [De voorbeeld toepassing op GitHub volt ooien](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)
* [Aanvullende gebruiks voorbeelden op GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Cosmos DB workshop Labs voor Change feed processor](https://azurecosmosdb.github.io/labs/dotnet/labs/08-change_feed_with_azure_functions.html#consume-cosmos-db-change-feed-via-the-change-feed-processor)

## <a name="next-steps"></a>Volgende stappen

U kunt nu door gaan met meer informatie over het wijzigen van de feed-processor in de volgende artikelen:

* [Overzicht van wijzigings feed](change-feed.md)
* [Pull-model voor feed wijzigen](change-feed-pull-model.md)
* [Migreren vanuit de bibliotheek van de wijzigings feed-processor](how-to-migrate-from-change-feed-library.md)
* [De wijzigingenfeedschatting gebruiken](how-to-use-change-feed-estimator.md)
* [Starttijd van verwerker van wijzigingenfeed](#starting-time)