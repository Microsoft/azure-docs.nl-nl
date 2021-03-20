---
title: 'Zelfstudie: Gebeurtenisgegevens naar Azure Synapse Analytics migreren - Azure Event Hubs'
description: In dit artikel wordt beschreven hoe u met Azure Event Grid en Azure Functions in Event Hubs opgenomen gegevens kunt migreren naar Azure Synapse Analytics.
services: event-hubs
ms.date: 12/07/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 77a2b35dea57c71e9e6f07056bd106df2ac109b8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96854141"
---
# <a name="tutorial-migrate-captured-event-hubs-data-to-azure-synapse-analytics-using-event-grid-and-azure-functions"></a>Zelfstudie: Vastgelegde Event Hub-gegevens migreren naar Azure Synapse Analytics met behulp van Event Grid en Azure Functions
Met Azure Event Hubs [Capture](./event-hubs-capture-overview.md) kunt u de gegevensstroom in Event Hubs automatisch opnemen in een Azure Blob-opslag of Azure Data Lake Store. In deze zelfstudie leest u hoe in Event Hub opgenomen gegevens migreert vanuit Storage naar Azure Synapse Analytics met behulp van een Azure-functie die door een [gebeurtenissenraster](../event-grid/overview.md) wordt geactiveerd.

[!INCLUDE [event-grid-event-hubs-functions-synapse-analytics.md](../../includes/event-grid-event-hubs-functions-synapse-analytics.md)]

## <a name="next-steps"></a>Volgende stappen 
U kunt krachtige hulpmiddelen voor gegevensvisualisatie gebruiken met uw datawarehouse om bruikbare inzichten te verkrijgen.

In dit artikel leest u hoe u [Power BI met Azure Synapse Analytics](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect) gebruikt