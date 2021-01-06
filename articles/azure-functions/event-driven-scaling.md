---
title: Op gebeurtenissen gebaseerd schalen in Azure Functions
description: Hierin wordt het schalen van het gedrag van verbruiks-en Premium-plan functies beschreven.
ms.date: 10/29/2020
ms.topic: conceptual
ms.service: azure-functions
ms.openlocfilehash: 8aca1ab6629f95ef9162e1247434bd3189d5a7d2
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937647"
---
# <a name="event-driven-scaling-in-azure-functions"></a>Op gebeurtenissen gebaseerd schalen in Azure Functions

In de verbruiks-en Premium-abonnementen, Azure Functions schaal van CPU-en geheugen bronnen door extra exemplaren van de functions-host toe te voegen. Het aantal exemplaren wordt bepaald aan de hand van het aantal gebeurtenissen waarmee een functie wordt geactiveerd. 

Elk exemplaar van de functions-host in het verbruiks abonnement is beperkt tot 1,5 GB aan geheugen en één CPU.  Een exemplaar van de host is de volledige functie-app, wat betekent dat alle functies binnen een functie-app resources delen binnen een exemplaar en op hetzelfde moment kunnen worden geschaald. Functie-apps die dezelfde schaal van het verbruiks plan delen, onafhankelijk van zichzelf.  In het Premium-abonnement bepaalt de grootte van het plan het beschik bare geheugen en de CPU voor alle apps in dat exemplaar.  

Functie code bestanden worden opgeslagen op Azure Files shares op het belangrijkste opslag account van de functie. Wanneer u het belangrijkste opslag account van de functie-app verwijdert, worden de functie code bestanden verwijderd en kunnen deze niet worden hersteld.

## <a name="runtime-scaling"></a>Runtime schalen

Azure Functions gebruikt een onderdeel dat de *schaal controller* wordt genoemd om de frequentie van gebeurtenissen te controleren en te bepalen of u wilt schalen of schalen. De schaal controller maakt gebruik van heuristiek voor elk trigger type. Wanneer u bijvoorbeeld een Azure Queue-opslag trigger gebruikt, wordt deze geschaald op basis van de lengte van de wachtrij en de leeftijd van het oudste wachtrij bericht.

De eenheid van de schaal voor Azure Functions is de functie-app. Wanneer de functie-app is geschaald, worden er extra resources toegewezen om meerdere exemplaren van de Azure Functions host uit te voeren. Als de berekenings aanvraag daarentegen wordt verkleind, worden de instanties van de functie-host door de schaal controller verwijderd. Het aantal exemplaren is uiteindelijk ' geschaald ' in nul wanneer er geen functies in een functie-app worden uitgevoerd.

![Controller bewakings gebeurtenissen schalen en instanties maken](./media/functions-scale/central-listener.png)

## <a name="cold-start"></a>Koude start

Nadat de functie-app gedurende een aantal minuten inactief is, kan het platform het aantal exemplaren waarop uw app wordt uitgevoerd, schalen op nul. De volgende aanvraag heeft de toegevoegde latentie van schalen van nul naar één. Deze latentie wordt een _koude start_ genoemd. Het aantal afhankelijkheden dat door uw functie-app wordt vereist, kan van invloed zijn op de koude start tijd. Koude start is meer een probleem voor synchrone bewerkingen, zoals HTTP-triggers die een antwoord moeten retour neren. Als koude start invloed heeft op uw functies, kunt u overwegen om in een Premium-abonnement of in een speciaal plan te worden uitgevoerd met de optie **altijd aan** ingeschakeld.   

## <a name="understanding-scaling-behaviors"></a>Meer informatie over het schalen van gedrag

Schalen kan variëren op basis van een aantal factoren en op verschillende manieren schalen, afhankelijk van de geselecteerde trigger en taal. Er zijn een aantal complexiteit waarmee u rekening moet houden:

* **Maximum aantal exemplaren:** Een app met één functie wordt alleen geschaald naar Maxi maal 200 exemplaren. Eén exemplaar kan echter meer dan één bericht of aanvraag tegelijk verwerken, dus er is geen limiet ingesteld voor het aantal gelijktijdige uitvoeringen.  U kunt zo nodig [een lagere maximum schaal opgeven](#limit-scale-out) .
* **Aantal nieuwe instanties:** Voor HTTP-triggers worden er Maxi maal één keer per seconde een nieuwe instantie toegewezen. Voor niet-HTTP-triggers worden nieuwe instanties Maxi maal één keer per 30 seconden toegewezen. Het schalen verloopt sneller bij het uitvoeren van een [Premium-abonnement](functions-premium-plan.md).
* **Schaal efficiëntie:** Gebruik voor Service Bus triggers _beheer_ rechten voor bronnen voor de meest efficiënte schaal aanpassing. Met _Luister_ rechten is schalen niet zo nauw keurig omdat de lengte van de wachtrij niet kan worden gebruikt om beslissingen over het schalen te melden. Zie [beleid voor gedeelde toegang](../service-bus-messaging/service-bus-sas.md#shared-access-authorization-policies)voor meer informatie over het instellen van rechten in service bus toegangs beleid. Zie de [richt lijnen voor schalen](functions-bindings-event-hubs-trigger.md#scaling) in het naslag artikel voor Event hub-triggers. 

## <a name="limit-scale-out"></a>Uitschalen beperken

U kunt het maximum aantal instanties beperken dat door een app kan worden uitgeschaald.  Dit komt het meest voor wanneer een stroomafwaarts onderdeel, zoals een Data Base, een beperkte door Voer heeft.  De functies van het verbruiks abonnement worden standaard uitgebreid naar Maxi maal 200 exemplaren en de functies van Premium-abonnementen worden uitgebreid tot Maxi maal 100 exemplaren.  U kunt een lager maximum opgeven voor een specifieke app door de waarde te wijzigen `functionAppScaleLimit` .  De `functionAppScaleLimit` kan worden ingesteld op `0` of `null` voor onbeperkt, of een geldige waarde tussen `1` en het maximum van de app.

```azurecli
az resource update --resource-type Microsoft.Web/sites -g <RESOURCE_GROUP> -n <FUNCTION_APP-NAME>/config/web --set properties.functionAppScaleLimit=<SCALE_LIMIT>
```

## <a name="best-practices-and-patterns-for-scalable-apps"></a>Aanbevolen procedures en patronen voor schaal bare apps

Er zijn veel aspecten van een functie-app die van invloed zijn op hoe deze wordt geschaald, inclusief hostconfiguratie, runtime footprint en resource-efficiëntie.  Zie de [sectie schaal baarheid in het artikel over prestatie overwegingen](functions-best-practices.md#scalability-best-practices)voor meer informatie. U moet ook weten hoe verbindingen zich gedragen als uw functie-app wordt geschaald. Raadpleeg [Verbindingen beheren in Azure Functions](manage-connections.md) voor meer informatie.

Voor meer informatie over het schalen van python en Node.js raadpleegt u de [Azure functions python-ontwikkelaars handleiding voor het schalen en gelijktijdigheid](functions-reference-python.md#scaling-and-performance) en de [Azure functions Node.js ontwikkelaars handleiding-schalen en gelijktijdigheid](functions-reference-node.md#scaling-and-concurrency).

## <a name="billing-model"></a>Factureringsmodel

De facturering voor de verschillende plannen wordt gedetailleerd beschreven op de [pagina met Azure functions prijzen](https://azure.microsoft.com/pricing/details/functions/). Het gebruik wordt geaggregeerd op het niveau van de functie-app en telt alleen de tijd waarop de functie code wordt uitgevoerd. Hier volgen enkele eenheden voor facturering:

* **Resource verbruik in Gigabyte-seconden (GB-s)**. Berekend als een combi natie van geheugen grootte en uitvoerings tijd voor alle functies in een functie-app. 
* **Uitvoeringen**. Geteld wanneer een functie wordt uitgevoerd in reactie op een gebeurtenis trigger.

Nuttige query's en informatie over het begrijpen van uw verbruiks factuur vindt u [in de veelgestelde vragen over facturering](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ).

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="next-steps"></a>Volgende stappen

+ [Opties voor Azure Functions hosten](functions-scale.md)

