---
title: Gedistribueerde tracering in Azure-toepassing Insights | Microsoft Docs
description: Bevat informatie over de ondersteuning van micro soft voor gedistribueerde tracering via onze samen werking in het project opentelling
ms.topic: conceptual
ms.custom: devx-track-dotnet
author: nikmd23
ms.author: nimolnar
ms.date: 09/17/2018
ms.reviewer: mbullwin
ms.openlocfilehash: a47b41a8b7f4e18be58c32c97cf279b9229f26da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100579505"
---
# <a name="what-is-distributed-tracing"></a>Wat is gedistribueerde tracering?

De komst van moderne Cloud-en micro [Services](https://azure.com/microservices) -architecturen heeft tot eenvoudige, onafhankelijk Implementeer bare Services geleid die de kosten kunnen helpen verlagen en de beschik baarheid en door Voer verhogen. Maar hoewel deze bewegingen afzonderlijke services in een geheel eenvoudiger te begrijpen hebben gemaakt, hebben ze het algemene systeem moeilijker gemaakt om redenen te krijgen en fouten op te sporen.

In monolithische architecturen zijn we gebruikt voor het opsporen van fouten met Call stacks. Aanroep Stacks zijn schitterende hulp middelen voor het weer geven van de uitvoering van de stroom (methode A aangeroepen methode B, die methode C wordt genoemd), samen met details en para meters voor elk van deze aanroepen. Dit is ideaal voor Monoliths of services die worden uitgevoerd op één proces, maar hoe kunnen we fouten opsporen wanneer de aanroep over een proces grens komt, niet alleen een verwijzing naar de lokale stack? 

Dat is waar gedistribueerde tracering beschikbaar is in.  

Gedistribueerde tracering is het equivalent van aanroep stacks voor moderne Cloud-en micro Services-architecturen, met toevoeging van een vereenvoudigde-prestatie Profiler dat is opgetreden in. In Azure Monitor bieden we twee ervaringen voor het gebruiken van gedistribueerde tracerings gegevens. De eerste is de weer gave van de [Diagnostische gegevens over trans acties](./transaction-diagnostics.md) , die als een aanroep stack met een tijd dimensie is toegevoegd in. De weer gave voor de diagnostische gegevens van de trans actie biedt inzicht in één trans actie/aanvraag en is handig voor het vinden van de hoofd oorzaak van betrouwbaarheids problemen en prestatie knelpunten op basis van een aanvraag.

Azure Monitor biedt ook een weer gave van een [toepassings overzicht](./app-map.md) waarmee veel trans acties worden weer gegeven met een topologische weer gave van de manier waarop de systemen communiceren, en wat de gemiddelde prestatie-en fout tarieven zijn. 

## <a name="how-to-enable-distributed-tracing"></a>Gedistribueerde tracering inschakelen

Het inschakelen van gedistribueerde tracering over de services in een toepassing is net zo eenvoudig als het toevoegen van de juiste agent, SDK of bibliotheek aan elke service, op basis van de taal waarin de service is geïmplementeerd.

## <a name="enabling-via-application-insights-through-auto-instrumentation-or-sdks"></a>Inschakelen via Application Insights via automatische instrumentatie of Sdk's

De Application Insights agents en/of Sdk's voor .NET, .NET core, Java, Node.js en Java script bieden alle ondersteuning voor gedistribueerde tracering. Instructies voor het installeren en configureren van elke Application Insights SDK zijn hieronder beschikbaar:

* [.NET](asp-net.md)
* [.NET Core](asp-net-core.md)
* [Java](./java-in-process-agent.md)
* [Node.js](../app/nodejs-quick-start.md)
* [JavaScript](./javascript.md)
* [Python](opencensus-python.md)

Met de juiste Application Insights SDK geïnstalleerd en geconfigureerd, wordt tracerings informatie automatisch verzameld voor populaire frameworks, Bibliotheken en technologieën door de auto-Collector van de SDK-afhankelijkheden. De volledige lijst met ondersteunde technologieën is beschikbaar in [de documentatie voor het automatisch verzamelen van afhankelijkheden](./auto-collect-dependencies.md).

 Daarnaast kan elke technologie hand matig worden gevolgd door een aanroep van [TrackDependency](./api-custom-events-metrics.md) op de [TelemetryClient](./api-custom-events-metrics.md).

## <a name="enable-via-opencensus"></a>Inschakelen via opentellingen

Naast de Application Insights Sdk's ondersteunt Application Insights ook gedistribueerde tracering via [Opentellingen](https://opencensus.io/). Opentelling is een open source, leverancier-neutraal, één distributie van bibliotheken voor het verzamelen van metrische gegevens en gedistribueerde tracering voor services. Daarnaast kan de open source-community gedistribueerde tracering inschakelen met populaire technologieën zoals redis, memcached of MongoDB. [Micro soft werkt samen met Opentellingen met verschillende andere bewakings-en Cloud partners](https://open.microsoft.com/2018/06/13/microsoft-joins-the-opencensus-project/).

[Python](opencensus-python.md) 

De opentellings website houdt API-referentie documentatie voor [python](https://opencensus.io/api/python/trace/usage.html) en [Go](https://godoc.org/go.opencensus.io), evenals verschillende hand leidingen voor het gebruik van opentellingen. 

## <a name="next-steps"></a>Volgende stappen

* [Gebruiks overzicht van opentellingen python](https://opencensus.io/api/python/trace/usage.html)
* [Toepassings overzicht](./app-map.md)
* [End-to-end prestatie bewaking](../app/tutorial-performance.md)

