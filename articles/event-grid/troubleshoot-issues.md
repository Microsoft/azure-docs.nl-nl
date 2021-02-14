---
title: Problemen met Event Grid oplossen
description: In dit artikel worden verschillende manieren beschreven om Azure Event Grid problemen op te lossen
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 9c52ba8561c10dd94ec6ef51c78b8534c6c58e96
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417237"
---
# <a name="troubleshoot-azure-event-grid-issues"></a>Problemen met Azure Event Grid oplossen
Dit artikel bevat informatie die u helpt bij het oplossen van problemen met Azure Event Grid. 

## <a name="diagnostic-logs"></a>Diagnostische logboeken
Schakel de diagnostische instellingen voor Event Grid onderwerpen of domeinen in om publicatie-en leverings fout logboeken vast te leggen en weer te geven. Zie [Diagnostische logboeken](enable-diagnostic-logs-topic.md)voor meer informatie.

## <a name="metrics"></a>Metrische gegevens
U kunt metrische gegevens weer geven voor Event Grid-onderwerpen en-abonnementen en er waarschuwingen op maken. Zie [Event grid metrische](monitor-event-delivery.md)gegevens voor meer informatie.

## <a name="alerts"></a>Waarschuwingen
Waarschuwingen maken voor Azure Event Grid metrische gegevens en activiteiten logboek bewerkingen. Zie [waarschuwingen op Event grid metrische gegevens en activiteiten logboeken](set-alerts.md)voor meer informatie.

## <a name="subscription-validation-issues"></a>Problemen met abonnements validatie
Tijdens het maken van het gebeurtenis abonnement wordt mogelijk een fout bericht weer gegeven met de melding dat het gegeven eind punt niet kan worden gevalideerd. Zie [problemen met het event grid-abonnement oplossen](troubleshoot-subscription-validation.md)voor meer informatie over het oplossen van problemen met abonnements validatie. 

## <a name="network-connectivity-issues"></a>Problemen met de netwerk verbinding
Er zijn verschillende redenen waarom client toepassingen geen verbinding kunnen maken met een Event Grid onderwerp/domein. De verbindings problemen die u ondervindt, kunnen permanent of tijdelijk zijn. Zie [verbindings problemen oplossen](troubleshoot-network-connectivity.md)voor meer informatie over het oplossen van deze problemen.

## <a name="error-codes"></a>Foutcodes
Als u fout berichten ontvangt met fout codes zoals 400, 409 en 403, raadpleegt u [Event grid-fouten oplossen](troubleshoot-errors.md). 

## <a name="distributed-tracing-net"></a>Gedistribueerde tracering (.NET)
De Event Grid .NET-bibliotheek ondersteunt het distribueren van tracering. Om te voldoen aan de [richt lijnen](https://github.com/cloudevents/spec/blob/master/extensions/distributed-tracing.md) van de CloudEvents-specificatie voor het distribueren van tracering, stelt de tape wisselaar de `traceparent` en `tracestate` op de [ExtensionAttributes](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventgrid/Azure.Messaging.EventGrid/src/Customization/CloudEvent.cs#L126) van een `CloudEvent` Wanneer gedistribueerde tracering is ingeschakeld. Voor meer informatie over het inschakelen van gedistribueerde tracering in uw toepassing raadpleegt u de documentatie van Azure SDK [Distributed tracing](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Azure.Core/samples/Diagnostics.md#Distributed-tracing).

### <a name="sample"></a>Voorbeeld
Zie het voor [beeld van regel tellers](/samples/azure/azure-sdk-for-net/line-counter/). Deze voor beeld-app illustreert het gebruik van opslag-, Event Hubs-en Event Grid-clients samen met ASP.NET Core integratie, gedistribueerde tracering en gehoste services. Hiermee kunnen gebruikers een bestand uploaden naar een blob, waarmee een Event Hubs gebeurtenis met de bestands naam wordt geactiveerd. De Event Hubs processor ontvangt de gebeurtenis, waarna de app de BLOB downloadt en het aantal regels in het bestand telt. In de app wordt een koppeling naar een pagina met het aantal regels weer gegeven. Als er op de koppeling wordt geklikt, wordt er een CloudEvent met de naam van het bestand gepubliceerd met behulp van Event Grid.

## <a name="next-steps"></a>Volgende stappen
Als u meer hulp nodig hebt, kunt u uw probleem in het [stack overflow forum](https://stackoverflow.com/questions/tagged/azure-eventgrid) plaatsen of een [ondersteunings ticket](https://azure.microsoft.com/support/options/)openen. 
