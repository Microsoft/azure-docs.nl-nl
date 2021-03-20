---
title: Taken en toepassingen voor gebeurtenis replicatie-Azure Event Hubs | Microsoft Docs
description: In dit artikel vindt u een overzicht van het bouwen van gebeurtenis replicatie taken en-toepassingen met Azure Functions
ms.topic: article
ms.date: 12/01/2020
ms.openlocfilehash: a65815c7da400af8b5b6d46358e6bca6edbd7543
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97663601"
---
# <a name="event-replication-tasks-and-applications-with-azure-functions"></a>Gebeurtenis replicatie taken en-toepassingen met Azure Functions

> [!TIP]
> Gebruik [Azure stream Analytics](../stream-analytics/stream-analytics-introduction.md) in plaats van Azure functions voor alle stateful replicatie taken waarbij u rekening moet houden met de nettoladingen van uw gebeurtenissen en om deze te transformeren, te combi neren, te verrijken of te verminderen.

Zoals uitgelegd in het [gebeurtenis replicatie en het federatieve artikel over meerdere regio's](event-hubs-federation-overview.md) , stateless replicatie van gebeurtenis stromen tussen de paar Event hubs en tussen Event hubs en andere bronnen voor gebeurtenis stroom en de doelen lean op Azure functions.

[Azure functions](../azure-functions/functions-overview.md) is een schaal bare en betrouw bare uitvoerings omgeving voor het configureren en uitvoeren van serverloze toepassingen, inclusief gebeurtenis replicatie en Federatie taken.

In dit overzicht vindt u meer informatie over de ingebouwde mogelijkheden van Azure Functions voor dergelijke toepassingen, over code blokken die u kunt aanpassen en wijzigen voor transformatie taken, en over het configureren van een Azure Functions toepassing, zodat deze in het ideale geval wordt geïntegreerd met Event Hubs en andere Azure Messa ging-Services. Voor veel details verwijst dit artikel naar de Azure Functions documentatie.

[!INCLUDE [messaging-replicator-functions](../../includes/messaging-replicator-functions.md)]









