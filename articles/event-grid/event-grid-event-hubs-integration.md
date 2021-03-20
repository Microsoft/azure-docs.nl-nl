---
title: 'Zelfstudie: Event Hubs-gegevens naar datawarehouse verzenden - Event Grid'
description: Hierin wordt beschreven hoe u in Event Hubs opgenomen gegevens opslaat in Azure Synapse Analytics via Azure Functions en Event Grid-triggers.
ms.topic: tutorial
ms.date: 12/07/2020
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 7b0e471e32650490e1896bb6ea171c8223b21378
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96854713"
---
# <a name="tutorial-stream-big-data-into-a-data-warehouse"></a>Zelfstudie: Big data streamen naar een datawarehouse
Azure [Event Grid](overview.md) is een intelligente service voor het routeren van gebeurtenissen waarmee u kunt reageren op meldingen of gebeurtenissen van apps en services. Het kan bijvoorbeeld een Azure-functie activeren voor het verwerken van Event Hubs-gegevens die zijn opgenomen in een blob-opslag of Data Lake Store. Dit [voorbeeld](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) laat zien hoe u Event Grid en Azure Functions gebruikt voor het migreren van vastgelegde Event Hubs-gegevens vanuit een blob-opslag naar Azure Synapse Analytics, waarbij het met name gaat om een toegewezen SQL-groep.

[!INCLUDE [event-grid-event-hubs-functions-synapse-analytics.md](../../includes/event-grid-event-hubs-functions-synapse-analytics.md)]

## <a name="next-steps"></a>Volgende stappen

* Zie voor meer informatie over de verschillen in de Azure-berichtenservices [Kiezen tussen Azure-services die berichten bezorgen](compare-messaging-services.md).
* Zie [Een inleiding tot Event Grid](overview.md) voor een inleiding tot Event Grid.
* Zie [Event Hubs Capture inschakelen met behulp van Azure Portal](../event-hubs/event-hubs-capture-enable-through-portal.md) voor een inleiding tot Event Hubs Capture.
* Zie het Engelstalige artikel [An overview, how Event Hubs Capture integrates with Event Grid](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) voor meer informatie over het instellen en uitvoeren van het voorbeeld.
