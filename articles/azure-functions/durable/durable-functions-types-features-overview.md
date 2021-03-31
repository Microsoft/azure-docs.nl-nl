---
title: Functie typen in azure Durable Functions
description: Meer informatie over de typen functies en rollen die ondersteuning bieden voor functie-to-function-communicatie in een Durable Functions indeling in Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: azfuncdf
ms.openlocfilehash: 35ef9d8731e169e890f5985ce01215fec5d6e3de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84697704"
---
# <a name="durable-functions-types-and-features"></a>Durable Functions typen en-functies

Durable Functions is een uitbreiding op [Azure Functions](../functions-overview.md). U kunt Durable Functions gebruiken om de functie-uitvoering in de stateful te organiseren. Een duurzame functie-app is een oplossing die bestaat uit verschillende Azure-functies. Functies kunnen verschillende rollen afspelen in een duurzame functie-indeling. 

Er zijn momenteel vier duurzame functie typen in Azure Functions: Activity, orchestrator, entity en client. In de rest van deze sectie vindt u meer informatie over de typen functies die deel uitmaken van een indeling.

## <a name="orchestrator-functions"></a>Orchestrator-functies

Orchestrator-functies beschrijven hoe acties worden uitgevoerd en de volg orde waarin acties worden uitgevoerd. Met Orchestrator-functies wordt de indeling in code (C# of Java script) beschreven, zoals weer gegeven in [Durable functions toepassings patronen](durable-functions-overview.md#application-patterns). Een indeling kan een groot aantal verschillende typen acties hebben, waaronder [activiteiten functies](#activity-functions), [Sub-](durable-functions-orchestrations.md#sub-orchestrations)indelingen, [wacht op externe gebeurtenissen](durable-functions-orchestrations.md#external-events), [http](durable-functions-http-features.md)en [timers](durable-functions-orchestrations.md#durable-timers). Orchestrator-functies kunnen ook communiceren met [entiteits functies](#entity-functions).

> [!NOTE]
> Orchestrator-functies worden geschreven met behulp van normale code, maar er zijn strikte vereisten voor het schrijven van de code. Met name de functie code van Orchestrator moet *deterministisch* zijn. Het volgen van deze determinism-vereisten kan ertoe leiden dat Orchestrator-functies niet goed worden uitgevoerd. Gedetailleerde informatie over deze vereisten en hoe u deze kunt omzeilen, vindt u in het onderwerp [code beperkingen](durable-functions-code-constraints.md) .

Zie het artikel over [duurzame Orchestrations](durable-functions-orchestrations.md) voor meer gedetailleerde informatie over Orchestrator-functies en de bijbehorende functies.

## <a name="activity-functions"></a>Activiteit functies

Activiteiten functies zijn de basis eenheid van het werk in een duurzame functie indeling. Activiteit functies zijn de functies en taken die in het proces zijn georganiseerd. U kunt bijvoorbeeld een Orchestrator-functie maken om een bestelling te verwerken. De taken omvatten het controleren van de inventarisatie, het opladen van de klant en het maken van een verzen ding. Elke taak zou een afzonderlijke activiteit functie zijn. Deze activiteiten functies kunnen opeenvolgend, parallel of een combi natie van beide worden uitgevoerd.

In tegens telling tot Orchestrator-functies zijn de activiteit functies niet beperkt in het type werk dat u kunt uitvoeren. Activiteit functies worden vaak gebruikt om netwerk aanroepen te maken of CPU-intensieve bewerkingen uit te voeren. Een functie van een activiteit kan ook gegevens retour neren naar de Orchestrator-functie. Het duurzame taak raamwerk garandeert dat elke aangeroepen activiteit functie *ten minste eenmaal* wordt uitgevoerd tijdens de uitvoering van een Orchestration.

> [!NOTE]
> Omdat activiteit functies slechts *ten minste eenmaal* moeten worden uitgevoerd, raden we u aan om uw activiteit functie Logic *idempotent* waar mogelijk te maken.

Gebruik een [activiteit trigger](durable-functions-bindings.md#activity-trigger) om een activiteit functie te definiëren. .NET-functies ontvangen een `DurableActivityContext` als-para meter. U kunt de trigger ook binden aan een ander JSON-serializeable-object om invoer door te geven aan de functie. In Java script kunt u toegang krijgen tot een invoer via de `<activity trigger binding name>` eigenschap van het [ `context.bindings` object](../functions-reference-node.md#bindings). Voor activiteit functies kan slechts één waarde worden door gegeven. Als u meerdere waarden wilt door geven, moet u Tuples, matrices of complexe typen gebruiken.

> [!NOTE]
> U kunt een activiteit functie alleen activeren vanuit een Orchestrator-functie.

## <a name="entity-functions"></a>Entiteitsfuncties

Met entiteits functies worden bewerkingen gedefinieerd voor het lezen en bijwerken van kleine stukjes status. Vaak verwijzen we naar deze stateful-entiteiten als *duurzame entiteiten*. Net als Orchestrator functions zijn entiteits functies functies met een speciaal trigger type, *entiteits trigger*. Ze kunnen ook worden aangeroepen vanuit client functies of vanuit Orchestrator-functies. In tegens telling tot Orchestrator-functies hebben entiteits functies geen specifieke code beperkingen. Met entiteits functies wordt ook de status expliciet beheerd in plaats van impliciet de status via de controle stroom te vertegenwoordigen.

> [!NOTE]
> Entiteits functies en gerelateerde functionaliteit zijn alleen beschikbaar in Durable Functions 2,0 en hoger.

Zie het artikel [duurzame entities](durable-functions-entities.md) voor meer informatie over entiteits functies.

## <a name="client-functions"></a>Client functies

Orchestrator-functies worden geactiveerd door een binding van een [Orchestration-trigger](durable-functions-bindings.md#orchestration-trigger) en entiteits functies worden geactiveerd door een binding van een [entiteits trigger](durable-functions-bindings.md#entity-trigger). Beide triggers werken door te reageren op berichten die in een [taak hub](durable-functions-task-hubs.md)in de wachtrij worden geplaatst. De belangrijkste manier om deze berichten te leveren is door gebruik te maken van een [Orchestrator-client binding](durable-functions-bindings.md#orchestration-client) of een [entiteit-client binding](durable-functions-bindings.md#entity-client) vanuit een *client functie*. Een niet-Orchestrator-functie kan een *client functie* zijn. U kunt bijvoorbeeld de Orchestrator activeren vanuit een door HTTP geactiveerde functie, een door Azure Event hub geactiveerde functie, enzovoort. Wat een functie vormt een *client functie* is het gebruik van de duurzame client-uitvoer binding.

> [!NOTE]
> In tegens telling tot andere functie typen kunnen Orchestrator-en entiteits functies niet rechtstreeks worden geactiveerd met behulp van de knoppen in azure Portal. Als u een functie van Orchestrator of entiteit wilt testen in azure Portal, moet u in plaats daarvan een *client functie* uitvoeren waarmee een Orchestrator-of entiteit functie wordt gestart als onderdeel van de implementatie. Voor de eenvoudigste test ervaring wordt een *hand matige activerings* functie aanbevolen.

Naast het activeren van Orchestrator-of entiteits functies, kan de *duurzame client* binding worden gebruikt om te communiceren met het uitvoeren van Orchestrations en entiteiten. Zo kunnen indelingen worden opgevraagd, beëindigd en kunnen er gebeurtenissen op worden gegenereerd. Zie het artikel [instance Management](durable-functions-instance-management.md) voor meer informatie over het beheren van Orchestrations en entiteiten.

## <a name="next-steps"></a>Volgende stappen

Om aan de slag te gaan, maakt u uw eerste duurzame functie in [C#](durable-functions-create-first-csharp.md) of [Java script](quickstart-js-vscode.md).

> [!div class="nextstepaction"]
> [Meer informatie over Durable Functions Orchestrations](durable-functions-orchestrations.md)