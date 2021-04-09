---
title: Reageren op gebeurtenissen van de Azure signalerings service
description: Gebruik Azure Event Grid om u te abonneren op gebeurtenissen van de Azure signalerings service. Andere downstream-Services kunnen worden geactiveerd door deze gebeurtenissen.
services: azure-signalr,event-grid
author: chenyl
ms.author: chenyl
ms.reviewer: zhshang
ms.date: 11/13/2019
ms.topic: conceptual
ms.service: signalr
ms.openlocfilehash: 77c8887ac19c6ce4c7d83734bdd2b44d9213914d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92151104"
---
# <a name="reacting-to-azure-signalr-service-events"></a>Reageren op Azure SignalR Service-gebeurtenissen

Met de service gebeurtenissen van Azure signalering kunnen toepassingen reageren op client verbindingen die zijn verbonden of losgekoppeld met moderne serverloze architecturen. Dit gebeurt zonder dat er complexe code of dure en inefficiënte polling services nodig zijn.  In plaats daarvan worden gebeurtenissen gepusht via [Azure Event grid](https://azure.microsoft.com/services/event-grid/) naar abonnees zoals [Azure functions](https://azure.microsoft.com/services/functions/), [Azure Logic apps](https://azure.microsoft.com/services/logic-apps/)of zelfs naar uw eigen aangepaste HTTP-listener. Met Azure-Signa lering betaalt u alleen voor wat u verbruikt.

Gebeurtenissen van de Azure signalerings service worden betrouwbaar verzonden naar de Event Grid-Service. Dit biedt betrouw bare leverings Services voor uw toepassingen via een uitgebreid beleid voor opnieuw proberen en levering van onbestelbare berichten. Zie [Event grid aflevering van berichten en probeer het opnieuw](../event-grid/delivery-and-retry.md).

![Event Grid model](/azure/event-grid/media/overview/functional-model.png)

## <a name="serverless-state"></a>Serverloze status
Gebeurtenissen van de Azure signalerings service zijn alleen actief wanneer client verbindingen een serverloze status hebben. Als een client niet wordt doorgestuurd naar een hub-server, wordt de status zonder server gebruikt. De klassieke modus werkt alleen wanneer de hub waarmee client verbindingen verbinding maken, geen hub-server heeft. Serverloze modus wordt aanbevolen als best practice. Zie [Service modus kiezen](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose)voor meer informatie over de service modus.

## <a name="available-azure-signalr-service-events"></a>Beschik bare gebeurtenissen van de Azure signalerings service
Gebeurtenisraster maakt gebruik van [gebeurtenisabonnementen](../event-grid/concepts.md#event-subscriptions) om gebeurtenisberichten te routen naar abonnees. Gebeurtenis abonnementen van de Azure signalerings service ondersteunen twee typen gebeurtenissen:  

|Gebeurtenisnaam|Description|
|----------|-----------|
|`Microsoft.SignalRService.ClientConnectionConnected`|Deze gebeurtenis treedt op wanneer een verbinding met een client tot stand is gebracht.|
|`Microsoft.SignalRService.ClientConnectionDisconnected`|Deze gebeurtenis treedt op wanneer de verbinding van een client verbinding wordt verbroken.|

## <a name="event-schema"></a>Gebeurtenisschema
De gebeurtenissen van de Azure signalerings service bevatten alle informatie die u nodig hebt om te reageren op de wijzigingen in uw gegevens. U kunt een Azure signalerings service-gebeurtenis identificeren met de eigenschap Event type begint met ' micro soft. SignalRService '. Meer informatie over het gebruik van Event Grid gebeurtenis eigenschappen wordt beschreven in [Event grid-gebeurtenis schema](../event-grid/event-schema.md).  

Hier volgt een voor beeld van een gebeurtenis die verbonden is met de client verbinding:
```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionConnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Zie voor meer informatie [schema voor seingevings service-gebeurtenissen](../event-grid/event-schema-azure-signalr.md).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Event Grid en het geven van Azure signalerings service-gebeurtenissen een try:

> [!div class="nextstepaction"]
> [Een voor beeld-Event grid integratie met de Azure signalerings service uitproberen](./signalr-howto-event-grid-integration.md) 
>  [Over Event grid](../event-grid/overview.md)