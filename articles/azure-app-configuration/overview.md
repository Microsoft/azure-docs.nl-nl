---
title: Wat is Azure App Configuration?
description: Lees een overzicht van de service Azure App Configuration. Begrijp waarom u App Configuration wilt gebruiken en leer hoe u dit kunt gebruiken.
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: overview
ms.date: 02/19/2020
ms.openlocfilehash: 9786ff20c1d3f467e217d5fe34518fcc9494863e
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96930106"
---
# <a name="what-is-azure-app-configuration"></a>Wat is Azure App Configuration?

Azure App Configuration biedt een service voor het centraal beheren van toepassingsinstellingen en functiemarkeringen. Moderne programma's, met name programma’s die in een cloud worden uitgevoerd, hebben doorgaans veel onderdelen die zijn gedistribueerd. Het verspreiden van configuratie-instellingen over deze onderdelen kan leiden tot moeilijk oplosbare fouten tijdens de implementatie van een toepassing. Gebruik App Configuration om alle instellingen voor uw toepassing op te slaan en de toegang ertoe beveiligen op één plek.

## <a name="why-use-app-configuration"></a>Waarom App Configuration gebruiken?

Cloudgebaseerde toepassingen worden vaak uitgevoerd op meerdere virtuele machines of containers in meerdere regio's en gebruiken meerdere externe services. Het is een flinke uitdaging om een robuuste en schaalbare toepassing te maken in een gedistribueerde omgeving.

Er zijn verschillende programmeermethoden om ontwikkelaars te helpen omgaan met de toenemende complexiteit van het bouwen van toepassingen. De [Twelve-Factor-app](https://12factor.net/) beschrijft bijvoorbeeld veel goed geteste architectonische patronen en best practices voor cloudtoepassingen. Een belangrijke aanbeveling in deze handleiding is om de configuratie van de code te scheiden. De configuratie-instellingen van een toepassing moeten extern van het bijbehorende uitvoerbare bestand worden gehouden en worden opgehaald uit de bijbehorende runtime-omgeving of een externe bron.

Hoewel elke toepassing gebruik kan maken van App Configuration, profiteren vooral de volgende soorten toepassingen ervan:

* Microservices op basis van Azure Kubernetes Service, Service Fabric of andere apps in containers die worden geïmplementeerd in een of meer regio's
* Serverloze apps, met inbegrip van Azure Functions of andere gebeurtenisgestuurde, staatloze compute-apps
* Pijplijn voor doorlopende implementatie

App Configuration biedt de volgende voordelen:

* Een volledig beheerde service die in enkele minuten kan worden ingesteld
* Flexibele belangrijke weergaven en toewijzingen
* Taggen met labels
* Point-in-time-herhaling van instellingen
* Exclusieve gebruikersinterface voor het beheer van functievlaggen
* Vergelijking van twee sets met configuraties voor speciaal gedefinieerde dimensies
* Verbeterde beveiliging met door Azure beheerde identiteiten
* Versleuteling van gevoelige informatie in rust en tijdens overdrachten
* Systeemeigen integratie met populaire frameworks

App Configuration vormt een aanvulling op [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), dat wordt gebruikt om toepassingsgeheimen op te slaan. Met App Configuration kunt u de volgende scenario's eenvoudiger implementeren:

* Centralisatie van het beheer en de distributie van hiërarchische configuratiegegevens voor verschillende omgevingen en regio’s
* Dynamische wijziging van toepassingsinstellingen zonder dat een toepassing opnieuw hoeft te worden geïmplementeerd of gestart
* Beschikbaarheid van besturingsfuncties in realtime

## <a name="use-app-configuration"></a>App Configuration gebruiken

De eenvoudigste manier om een App Configuration-archief aan uw toepassing toe te voegen, is via een door Microsoft aangeboden clientbibliotheek. De volgende methoden zijn beschikbaar om verbinding te maken met uw toepassing, afhankelijk van uw gekozen taal en framework

| Computertaal en framework | Verbinding maken |
|---|---|
| .NET Core en ASP.NET Core | App Configuration-provider voor .NET Core |
| .NET Framework en ASP.NET | App Configuration-ontwikkelaar voor .NET |
| Java Spring | App Configuration-client voor Spring Cloud |
| Andere | App Configuration-REST API |

## <a name="next-steps"></a>Volgende stappen

* [Snelstartgids voor ASP.NET Core](./quickstart-aspnet-core-app.md)
* [Snelstartgids voor .NET Core](./quickstart-dotnet-core-app.md)
* [Snelstartgids voor .NET Framework](./quickstart-dotnet-app.md)
* [Snelstartgids voor Azure Functions](./quickstart-azure-functions-csharp.md)
* [Snelstartgids voor Java Spring](./quickstart-java-spring-app.md)
* [Snelstartgids voor de ASP.NET Core-functievlag](./quickstart-feature-flag-aspnet-core.md)
* [Snelstartgids voor de Spring Boot-functievlag](./quickstart-feature-flag-spring-boot.md)
