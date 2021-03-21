---
title: Azure IoT Hub trigger voor Azure Functions
description: Meer informatie over het reageren op gebeurtenissen die worden verzonden naar een IoT hub-gebeurtenis stroom in Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 5c9309834b407ee56d29e38afd965ac947fc8a4f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102612283"
---
# <a name="azure-iot-hub-trigger-for-azure-functions"></a>Azure IoT Hub trigger voor Azure Functions

In dit artikel wordt uitgelegd hoe u kunt werken met Azure Functions bindingen voor IoT Hub. De IoT Hub ondersteuning is gebaseerd op de [Azure Event hubs-binding](functions-bindings-event-hubs.md).

Zie het [overzicht](functions-bindings-event-iot.md)voor meer informatie over de installatie-en configuratie details.

> [!IMPORTANT]
> Hoewel de volgende code voorbeelden de Event hub API gebruiken, is de opgegeven syntaxis van toepassing op IoT Hub-functies.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-trigger.md)]

## <a name="hostjson-properties"></a>host.json-eigenschappen

De [host.jsin](functions-host-json.md#eventhub) het bestand bevat instellingen die het trigger gedrag van de Event hub regelen. Zie de sectie [host.jsop instellingen](functions-bindings-event-iot.md#hostjson-settings) voor meer informatie over de beschik bare instellingen.

## <a name="next-steps"></a>Volgende stappen

- [Gebeurtenissen schrijven naar een gebeurtenis stroom (uitvoer binding)](./functions-bindings-event-iot-output.md)
