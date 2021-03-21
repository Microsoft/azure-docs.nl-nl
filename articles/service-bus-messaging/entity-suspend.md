---
title: Azure Service Bus-Messa ging-entiteiten onderbreken
description: In dit artikel wordt uitgelegd hoe u Azure Service Bus bericht entiteiten (wacht rijen, onderwerpen en abonnementen) tijdelijk kunt onderbreken en opnieuw activeren.
ms.topic: article
ms.date: 09/29/2020
ms.openlocfilehash: ea1acab3d0a86b0064f8b3eef7bfd1496bd17041
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94543048"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>Messa ging-entiteiten onderbreken en opnieuw activeren (uitschakelen)

Wacht rijen, onderwerpen en abonnementen kunnen tijdelijk worden onderbroken. Met opschorting wordt de entiteit ingesteld op een uitgeschakelde status waarin alle berichten in de opslag worden bewaard. Berichten kunnen echter niet worden verwijderd of toegevoegd, en de bijbehorende protocol bewerkingen veroorzaken fouten.

Mogelijk wilt u een entiteit schorsen voor dringende administratieve redenen. Een defecte ontvanger neemt bijvoorbeeld berichten uit de wachtrij, mislukt de verwerking en de berichten zijn nog niet correct voltooid en verwijderd. In dit geval kunt u de wachtrij voor ontvangen uitschakelen totdat u de code corrigeert en implementeert. 

Een opschorting of heractivering kan worden uitgevoerd door de gebruiker of door het systeem. Het systeem schorst alleen entiteiten vanwege een afdoende administratieve redenen, zoals het aantal keren dat de bestedings limiet van het abonnement is bereikt. Entiteiten waarvoor een systeem is uitgeschakeld, kunnen niet opnieuw worden geactiveerd door de gebruiker, maar worden hersteld wanneer de oorzaak van de onderbreking is opgelost.

## <a name="queue-status"></a>Wachtrij status 
De statussen die kunnen worden ingesteld voor een **wachtrij** zijn:

-   **Actief**: de wachtrij is actief. U kunt berichten verzenden naar en berichten ontvangen van de wachtrij. 
-   **Uitgeschakeld**: de wachtrij is onderbroken. Het is gelijk aan het instellen van zowel **SendDisabled** als **ReceiveDisabled**. 
-   **SendDisabled**: u kunt geen berichten verzenden naar de wachtrij, maar u ontvangt wel berichten. U krijgt een uitzonde ring als u probeert berichten te verzenden naar de wachtrij. 
-   **ReceiveDisabled**: u kunt berichten verzenden naar de wachtrij, maar kunt u er geen berichten van ontvangen. U ontvangt een uitzonde ring als u berichten in de wachtrij probeert te ontvangen.


### <a name="change-the-queue-status-in-the-azure-portal"></a>Wijzig de status van de wachtrij in de Azure Portal: 

1. Ga in Azure Portal naar uw Service Bus-naamruimte. 
1. Selecteer de wachtrij waarvan u de status wilt wijzigen. U ziet wacht rijen in het onderste deel venster in het midden. 
1. Op de pagina **Service Bus wachtrij** ziet u de huidige status van de wachtrij als een Hyper link. Als het **overzicht** niet is geselecteerd in het linkermenu, selecteert u dit om de status van de wachtrij weer te geven. Selecteer de huidige status van de wachtrij om deze te wijzigen. 

    :::image type="content" source="./media/entity-suspend/select-state.png" alt-text="Status van de wachtrij selecteren":::
4. Selecteer de nieuwe status voor de wachtrij en selecteer **OK**. 

    :::image type="content" source="./media/entity-suspend/entity-state-change.png" alt-text="Status van de wachtrij instellen":::
    
U kunt de verzend-en ontvangst bewerkingen ook uitschakelen met behulp van de Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) -api's in de .NET SDK of door middel van een Azure Resource Manager-sjabloon via Azure CLI of Azure PowerShell.

### <a name="change-the-queue-status-using-azure-powershell"></a>De wachtrij status wijzigen met behulp van Azure PowerShell
De Power shell-opdracht voor het uitschakelen van een wachtrij wordt weer gegeven in het volgende voor beeld. De opdracht voor opnieuw activeren is gelijk aan de instelling `Status` **actief**.

```powershell
$q = Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="topic-status"></a>Onderwerps status
U kunt de status van het onderwerp in de Azure Portal wijzigen. Selecteer de huidige status van het onderwerp om de volgende pagina te bekijken, waarmee u de status kunt wijzigen. 

:::image type="content" source="./media/entity-suspend/topic-state-change.png" alt-text="Onderwerps status wijzigen":::

De statussen die kunnen worden ingesteld voor een **onderwerp** zijn:
- **Actief**: het onderwerp is actief. U kunt berichten verzenden naar het onderwerp. 
- **Uitgeschakeld**: het onderwerp is onderbroken. U kunt geen berichten verzenden naar het onderwerp. 
- **SendDisabled**: hetzelfde effect als **uitgeschakeld**. U kunt geen berichten verzenden naar het onderwerp. U krijgt een uitzonde ring als u probeert berichten te verzenden naar het onderwerp. 

## <a name="subscription-status"></a>Abonnements status
U kunt de abonnements status wijzigen in de Azure Portal. Selecteer de huidige status van het abonnement om de volgende pagina weer te geven, waarmee u de status kunt wijzigen. 

:::image type="content" source="./media/entity-suspend/subscription-state-change.png" alt-text="Abonnements status wijzigen":::

De statussen die kunnen worden ingesteld voor een **abonnement** zijn:
- **Actief**: het abonnement is actief. U kunt berichten ontvangen frm het abonnement.
- **Uitgeschakeld**: het abonnement is onderbroken. U kunt geen berichten van het abonnement ontvangen. 
- **ReceiveDisabled**: hetzelfde effect als **uitgeschakeld**. U kunt geen berichten van het abonnement ontvangen. U krijgt een uitzonde ring als u berichten probeert te ontvangen voor het abonnement.

| Onderwerps status | Abonnements status | Gedrag | 
| ------------ | ------------------- | -------- | 
| Actief | Actief | U kunt berichten verzenden naar het onderwerp en berichten ontvangen van het abonnement. | 
| Actief | Uitgeschakeld of ontvangen is uitgeschakeld | U kunt berichten verzenden naar het onderwerp, maar u kunt geen berichten ontvangen van het abonnement | 
| Uitgeschakeld of verzenden is uitgeschakeld | Actief | U kunt geen berichten verzenden naar het onderwerp, maar u ontvangt wel berichten die al bij het abonnement zijn. | 
| Uitgeschakeld of verzenden is uitgeschakeld | Uitgeschakeld of ontvangen is uitgeschakeld | U kunt geen berichten verzenden naar het onderwerp en u kunt niet ontvangen van het abonnement. | 

## <a name="other-statuses"></a>Andere statussen
De opsomming [EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) definieert ook een set overgangs statussen die alleen door het systeem kan worden ingesteld. 


## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen voor meer informatie over Service Bus Messa ging:

* [Service Bus-wachtrijen, -onderwerpen en -abonnementen](service-bus-queues-topics-subscriptions.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus-onderwerpen en -abonnementen gebruiken](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/entity-state-change.png

