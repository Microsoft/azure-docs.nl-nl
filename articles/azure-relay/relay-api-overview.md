---
title: Overzicht van Azure Relay-API | Microsoft Docs
description: In dit artikel vindt u een overzicht van de beschik bare Azure Relay-Api's (.NET Standard, .NET Framework, Node.js, enz.)
ms.topic: article
ms.custom: devx-track-dotnet
ms.date: 06/23/2020
ms.openlocfilehash: 98bbb1ecc7e870ff9b7687284e7087d44375d275
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88935718"
---
# <a name="available-relay-apis"></a>Beschik bare relay-Api's

## <a name="runtime-apis"></a>Runtime-Api's

De volgende tabel bevat alle momenteel beschik bare relay runtime-clients.

De sectie [aanvullende informatie](#additional-information) bevat meer informatie over de status van elke runtime-bibliotheek.

| Taal/platform | Beschik bare functie | Clientpakket | Opslagplaats |
| --- | --- | --- | --- |
| .NET Standard | Hybride verbindingen | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [GitHub](https://github.com/azure/azure-relay-dotnet) |
| .NET Framework | WCF-relay | [WindowsAzure. ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | N.v.t. |
| Knooppunt | Hybride verbindingen | [WebSockets `hyco-ws`](https://www.npmjs.com/package/hyco-ws)<br/>[WebSockets `hyco-websocket`](https://www.npmjs.com/package/hyco-websocket)<br/>[HTTP-aanvragen: `hyco-https`](https://www.npmjs.com/package/hyco-https) | [GitHub](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>Aanvullende informatie

#### <a name="net"></a>.NET

Het .NET-ecosysteem heeft meerdere runtimes, dus er zijn meerdere .NET-bibliotheken voor de relay. De .NET Standard-bibliotheek kan worden uitgevoerd met behulp van .NET core of de .NET Framework, terwijl de .NET Framework bibliotheek alleen kan worden uitgevoerd in een .NET Framework omgeving. Zie [Framework-versies](/dotnet/articles/standard/frameworks)(Engelstalig) voor meer informatie over .net frameworks.

De .NET Framework-bibliotheek ondersteunt alleen het WCF-programmeer model en maakt gebruik van een eigen binair protocol op basis van het WCF- `net.tcp` Trans Port. Dit protocol en deze bibliotheek worden onderhouden voor achterwaartse compatibiliteit met bestaande toepassingen.

De .NET Standard-bibliotheek is gebaseerd op de open protocol definitie voor de Hybride verbindingen relay die gebaseerd is op HTTP-en websockets. De bibliotheek ondersteunt een stroom abstractie over websockets en een eenvoudige API-penbeweging voor aanvragen en antwoorden voor het beantwoorden van HTTP-aanvragen. De [Web-API](https://github.com/Azure/azure-relay-dotnet) -voor beeld laat zien hoe u hybride verbindingen integreert met ASP.net core voor webservices.

#### <a name="nodejs"></a>Node.js

De Hybride verbindingen-modules die in de bovenstaande tabel worden weer gegeven, vervangen of wijzigen bestaande Node.js modules met alternatieve implementaties die Luis teren op de Azure Relay-service in plaats van de lokale netwerk stack.

De `hyco-https` module wijzigt en gedeeltelijk overschrijft de kern Node.js modules `http` en `https` biedt een HTTPS-listener-implementatie die compatibel is met veel bestaande Node.js modules en toepassingen die afhankelijk zijn van deze kern modules.

Met `hyco-ws` de `hyco-websocket` modules en worden de populaire `ws` en `websocket` modules voor Node.js gewijzigd, waarbij alternatieve listener-implementaties worden geboden waarmee modules en toepassingen die afhankelijk zijn van een van beide modules, kunnen werken achter de hybride verbindingen relay.

Meer informatie over deze modules vindt u in de [Azure-relay-node github-](https://github.com/Azure/azure-relay-node) opslag plaats.

## <a name="next-steps"></a>Volgende stappen

Ga voor meer informatie over Azure Relay naar deze koppelingen:
* [Wat is Azure Relay?](relay-what-is-it.md)
* [Veelgestelde vragen over Relay](relay-faq.md)
