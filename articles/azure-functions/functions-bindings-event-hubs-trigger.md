---
title: Azure Event Hubs trigger voor Azure Functions
description: Meer informatie over het gebruik van Azure Event Hubs activeren in Azure Functions.
author: craigshoemaker
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: c2b8302e64f7dcc657fd20ed5d918ed6816d750d
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102608905"
---
# <a name="azure-event-hubs-trigger-for-azure-functions"></a>Azure Event Hubs trigger voor Azure Functions

In dit artikel wordt uitgelegd hoe u kunt werken met de [Azure Event hubs](../event-hubs/event-hubs-about.md) -trigger voor Azure functions. Azure Functions ondersteunt trigger-en [uitvoer bindingen](functions-bindings-event-hubs-output.md) voor Event hubs.

Zie het [overzicht](functions-bindings-event-hubs.md)voor meer informatie over de installatie-en configuratie details.

[!INCLUDE [functions-bindings-event-hubs-trigger](../../includes/functions-bindings-event-hubs-trigger.md)]

## <a name="hostjson-settings"></a>host.jsop instellingen

De [host.jsin](functions-host-json.md#eventhub) het bestand bevat instellingen die het trigger gedrag van de Event hub regelen. Zie de sectie [host.jsop instellingen](functions-bindings-event-hubs.md#hostjson-settings) voor meer informatie over de beschik bare instellingen.

## <a name="next-steps"></a>Volgende stappen

- [Gebeurtenissen schrijven naar een gebeurtenis stroom (uitvoer binding)](./functions-bindings-event-hubs-output.md)
