---
title: Overzicht van Azure Functions
description: Ontdek hoe Azure Functions u kan helpen schaalbare serverloze apps te bouwen.
author: craigshoemaker
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.topic: overview
ms.date: 11/20/2020
ms.author: cshoe
ms.custom: contperfq2
ms.openlocfilehash: 6713c0d45a8b5363122c726d1d31e5c479ba8fff
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95504638"
---
# <a name="introduction-to-azure-functions"></a>Een inleiding tot Azure Functions

We bouwen vaak systemen om te reageren op een reeks kritieke gebeurtenissen. Of u nu een web-API bouwt, reageert op wijzigingen in de database, IoT-gegevensstromen verwerkt of berichtenwachtrijen beheert, elke toepassing heeft een methode nodig om code uit te voeren wanneer deze gebeurtenissen optreden.

Azure Functions biedt twee belangrijke methoden voor 'berekeningen op aanvraag' om aan deze behoefte te voldoen.

Ten eerste kunt u met Azure Functions de logica van uw systeem implementeren in direct beschikbare codeblokken. Deze codeblokken worden 'functies' genoemd. Verschillende functies kunnen op elk gewenst moment worden uitgevoerd om te reageren op kritieke gebeurtenissen.

Ten tweede kunt u, als het aantal aanvragen toeneemt, met Azure Functions aan de vraag voldoen met zo veel resources en functie-instanties als nodig, maar alleen zolang dat nodig is. Wanneer het aantal aanvragen afneemt, worden alle extra resources en toepassingsexemplaren automatisch weer verwijderd.

Waar komen alle rekenresources vandaan? Azure Functions [biedt zo veel of zo weinig rekenresources](./functions-scale.md) als nodig is om te voldoen aan de vraag van uw toepassing.

Het leveren van rekenresources op aanvraag is de essentie van [serverloze computing](https://azure.microsoft.com/solutions/serverless/) in Azure Functions.

## <a name="scenarios"></a>Scenario's

In veel gevallen kan een functie worden [geïntegreerd met een matrix van cloudservices](./functions-triggers-bindings.md) om implementaties met uitgebreide functies te bieden.

Hier volgt een veelvoorkomende, _maar geen volledige_ set scenario's voor Azure Functions.

| Als u dit wilt... | moet u... |
| --- | --- |
| **Een web-API bouwen** | Een eindpunt voor uw webtoepassingen implementeren met de [HTTP-trigger](./functions-bindings-http-webhook.md) |
| **Uploads van bestanden verwerken** | Code uitvoeren wanneer een bestand wordt geüpload of gewijzigd in [blob-opslag](./functions-bindings-storage-blob.md) |
| **Een serverloze werkstroom maken** | Een reeks functies koppelen met behulp van [duurzame functies](./durable-functions-overview.md) |
| **Reageren op wijzigingen in de database** | Aangepaste logica uitvoeren wanneer een document wordt gemaakt of bijgewerkt in [Cosmos DB](./functions-bindings-cosmosdb-v2.md) |
| **Geplande taken uitvoeren** | Code uitvoeren op [vaste tijdstippen](./functions-bindings-timer.md) |
| **Betrouwbare berichtenwachtrijsystemen maken** | Berichtenwachtrijen verwerken met behulp van [Queue Storage](./functions-bindings-storage-queue.md), [Service Bus](./functions-bindings-service-bus.md) of [Event Hubs](./functions-bindings-event-hubs.md) |
| **IoT-gegevensstromen analyseren** | [Gegevens van IoT-apparaten](./functions-bindings-event-iot.md) verzamelen en verwerken |
| **Gegevens in realtime verwerken** | [Functions en Signal R](./functions-bindings-signalr-service.md) gebruiken om in realtime op gegevens te reageren |

Voor het bouwen van functies zijn de volgende opties en resources beschikbaar:

- **Uw voorkeurstaal gebruiken**: Schrijf functies in [C#, Java, JavaScript, PowerShell of Python](./supported-languages.md) of gebruik een [aangepaste handler](./functions-custom-handlers.md) om vrijwel elke andere taal te kunnen gebruiken.

- **Implementatie automatiseren**: Bij een op hulpprogramma's gebaseerde benadering voor het gebruik van externe pijplijnen zijn er [talloze implementatieopties](./functions-deployment-technologies.md) beschikbaar.

- **Problemen met een functie oplossen**: Gebruik [controlehulpprogramma's](./functions-monitoring.md) en [teststrategieën](./functions-test-a-function.md) om inzicht te krijgen in uw apps.

- **Flexibele prijsopties**: Bij het [Verbruiksabonnement](./pricing.md) betaalt u alleen voor uw functies wanneer deze worden uitgevoerd. De [Premium](./pricing.md)- en [App Service](./pricing.md)-abonnementen bieden functies voor speciale behoeften.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aan de slag met lessen, voorbeelden en interactieve zelfstudies](./functions-get-started.md)
