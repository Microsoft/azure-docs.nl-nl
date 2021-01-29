---
title: Overzicht van Azure cache voor redis-Event Grid
description: Gebruik Azure Event Grid voor het publiceren van Azure cache voor redis-gebeurtenissen.
author: curib
ms.author: cauribeg
ms.date: 12/21/2020
ms.topic: conceptual
ms.service: cache
ms.openlocfilehash: 0a0809076367356739dfeadcf8dd63f88866a987
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99056075"
---
# <a name="azure-cache-for-redis-event-grid-overview"></a>Overzicht van Azure cache voor redis-Event Grid 

Azure cache voor redis-gebeurtenissen, zoals patches, schalen, import/export (RDB)-gebeurtenissen worden gepusht met [Azure Event grid](https://azure.microsoft.com/services/event-grid/) naar abonnees zoals Azure Functions, Azure Logic apps of zelfs naar uw eigen HTTP-listener. Event Grid biedt betrouw bare levering van gebeurtenissen aan uw toepassingen via een uitgebreid beleid voor opnieuw proberen en onbestelbare berichten.

Zie het artikel [Azure cache for redis Events schema](../event-grid/event-schema-azure-cache.md) voor het weer geven van de volledige lijst met gebeurtenissen die Azure cache voor redis ondersteunt.

Als u Azure cache wilt proberen voor redis-gebeurtenissen, raadpleegt u een van de volgende Quick starts:

|Als u dit hulp programma wilt gebruiken:    |Bekijk deze Snelstartgids: |
|--|-|
|Azure Portal    |[Quick Start: Azure cache naar redis-gebeurtenissen naar een webeindpunt door sturen met de Azure Portal](cache-event-grid-quickstart-portal.md)|
|PowerShell    |[Snelstartgids: Azure cache voor redis-gebeurtenissen naar een webeindpunt door sturen met Power shell](cache-event-grid-quickstart-powershell.md)|
|Azure CLI    |[Snelstartgids: Azure-cache voor redis-gebeurtenissen naar een webeindpunt door sturen met Azure CLI](cache-event-grid-quickstart-cli.md)|

## <a name="the-event-model"></a>Het gebeurtenis model

Event Grid gebruikt [gebeurtenis abonnementen](../event-grid/concepts.md#event-subscriptions) om gebeurtenis berichten te routeren naar abonnees. In deze afbeelding ziet u de relatie tussen gebeurtenis uitgevers, gebeurtenis abonnementen en gebeurtenis-handlers.

:::image type="content" source="media/cache-event-grid/event-grid-model.png" alt-text="Event grid-model.":::

Abonneer u eerst op een eind punt voor een gebeurtenis. Wanneer een gebeurtenis vervolgens wordt geactiveerd, verzendt de Event Grid-Service gegevens over die gebeurtenis naar het eind punt.

Zie het artikel [Azure cache for redis Events schema](../event-grid/event-schema-azure-cache.md) voor het weer geven van:

> [!div class="checklist"]
> * Een volledige lijst met Azure-cache voor redis-gebeurtenissen en hoe elke gebeurtenis wordt geactiveerd.
> * Een voor beeld van de gegevens die het Event Grid verzendt voor elk van deze gebeurtenissen.
> * Het doel van elk sleutel waarde-paar dat wordt weer gegeven in de gegevens.


## <a name="best-practices-for-consuming-events"></a>Aanbevolen procedures voor het gebruiken van gebeurtenissen

Toepassingen die Azure cache voor redis-gebeurtenissen verwerken, moeten een aantal aanbevolen procedures volgen:
> [!div class="checklist"]
> * Omdat meerdere abonnementen kunnen worden geconfigureerd voor het routeren van gebeurtenissen naar dezelfde gebeurtenis-handler, is het belang rijk om te voor komen dat gebeurtenissen afkomstig zijn uit een bepaalde bron, maar om het onderwerp van het bericht te controleren om ervoor te zorgen dat het afkomstig is van de Azure-cache voor het redis-exemplaar dat u verwacht.
> * Controleer ook of de Event type is ingesteld als een voor bereiding op het proces en ga er niet van uit dat alle gebeurtenissen die u ontvangt, de verwachte typen zijn.
> * Azure cache voor redis-gebeurtenissen garandeert een levering van ten minste één keer naar abonnees. Dit zorgt ervoor dat alle berichten worden gegenereerd. Als gevolg van nieuwe pogingen of Beschik baarheid van abonnementen, kunnen er af en toe dubbele berichten optreden. Zie [Event grid aflevering van berichten en probeer](../event-grid/delivery-and-retry.md)het opnieuw.


## <a name="next-steps"></a>Volgende stappen

Meer informatie over Event Grid en Azure cache voor redis-gebeurtenissen geven een try:

- [Over Event Grid](../event-grid/overview.md)
- [Azure cache voor redis-gebeurtenissen schema](../event-grid/event-schema-azure-cache.md)
- [Azure-cache naar redis-gebeurtenissen door sturen naar een webeindpunt met Azure CLI](cache-event-grid-quickstart-cli.md)
- [Azure-cache voor redis-gebeurtenissen naar een webeindpunt door sturen met de Azure Portal](cache-event-grid-quickstart-portal.md)
- [Azure-cache naar redis-gebeurtenissen door sturen naar een webeindpunt met Power shell](cache-event-grid-quickstart-powershell.md)
