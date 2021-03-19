---
title: Overdracht van gebeurtenis van webhook
description: In dit artikel worden webhook-gebeurtenis bezorgings-en eindpunt validatie beschreven bij gebruik van webhooks.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: e9a52d0cb3e4e880d91e1b748d97ef3041298930
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87461235"
---
# <a name="webhook-event-delivery"></a>Overdracht van gebeurtenis van webhook
Webhooks zijn een van de vele manieren om gebeurtenissen van Azure Event Grid te ontvangen. Wanneer een nieuwe gebeurtenis gereed is, boekt Event Grid-Service een HTTP-aanvraag naar het geconfigureerde eind punt met de gebeurtenis in de hoofd tekst van de aanvraag.

Net als bij veel andere services die webhooks ondersteunen, moet Event Grid het eigendom van het webhook-eind punt bewijzen voordat ze gebeurtenissen aan dat eind punt kunnen leveren. Deze vereiste voor komt dat een kwaadwillende gebruiker uw eind punt kan overlopen met gebeurtenissen. Wanneer u een van de drie onderstaande Azure-Services gebruikt, wordt deze validatie automatisch door de Azure-infra structuur afgehandeld:

- Azure Logic Apps met [Event grid-connector](/connectors/azureeventgrid/)
- Azure Automation via [webhook](../event-grid/ensure-tags-exists-on-new-virtual-machines.md)
- Azure Functions met [Event grid trigger](../azure-functions/functions-bindings-event-grid.md)

## <a name="endpoint-validation-with-event-grid-events"></a>Eindpunt validatie met Event Grid gebeurtenissen
Als u een ander type eind punt gebruikt, zoals een op een HTTP-trigger gebaseerde Azure-functie, moet uw eindpunt code deel nemen aan een validatie-Handshake met Event Grid. Event Grid ondersteunt twee manieren om het abonnement te valideren.

1. **Synchrone Handshake**: op het moment van het maken van het gebeurtenis abonnement wordt Event grid een validatie gebeurtenis voor het abonnement naar uw eind punt verzonden. Het schema van deze gebeurtenis is vergelijkbaar met een andere Event Grid gebeurtenis. Het gegevens gedeelte van deze gebeurtenis bevat een `validationCode` eigenschap. Uw toepassing controleert of de validatie aanvraag voor een verwacht gebeurtenis abonnement is en retourneert de validatie code in het antwoord synchroon. Dit handshake-mechanisme wordt in alle Event Grid versies ondersteund.

2. **Asynchrone Handshake**: in bepaalde gevallen kunt u de ValidationCode in antwoord niet synchroon retour neren. Als u bijvoorbeeld gebruikmaakt van een service van derden (zoals [`Zapier`](https://zapier.com) of [IFTTT](https://ifttt.com/)), kunt u niet programmatisch met de validatie code reageren.

   Vanaf versie 2018-05-01-preview ondersteunt Event Grid een hand matige validatie-handshake. Als u een gebeurtenis abonnement met een SDK of een hulp programma maakt die gebruikmaakt van API versie 2018-05-01-preview of hoger, wordt Event Grid een `validationUrl` eigenschap verzonden in het gedeelte gegevens van de validatie gebeurtenis voor het abonnement. Als u de handshake wilt volt ooien, gaat u naar die URL in de gebeurtenis gegevens en voert u een GET-aanvraag naar deze. U kunt een REST-client of uw webbrowser gebruiken.

   De beschik bare URL is **5 minuten** geldig. Gedurende die tijd is de inrichtings status van het gebeurtenis abonnement `AwaitingManualAction` . Als u de hand matige validatie niet binnen vijf minuten voltooit, wordt de inrichtings status ingesteld op `Failed` . U moet het gebeurtenis abonnement opnieuw maken voordat u de hand matige validatie start.

   Voor dit verificatie mechanisme moet ook het webhook-eind punt een HTTP-status code van 200 retour neren, zodat u weet dat het bericht voor de validatie gebeurtenis is geaccepteerd voordat het in de hand matige validatie modus kan worden geplaatst. Met andere woorden, als het eind punt 200 retourneert, maar niet synchroon een validatie antwoord terugkeert, wordt de modus overgezet naar de hand matige validatie modus. Als er binnen vijf minuten een GET op de validatie-URL is, wordt de validatie-Handshake als geslaagd beschouwd.

> [!NOTE]
> Het gebruik van zelfondertekende certificaten voor validatie wordt niet ondersteund. Gebruik in plaats daarvan een ondertekend certificaat van een commerciële certificerings instantie (CA).

### <a name="validation-details"></a>Validatie Details

- Op het moment dat het maken of bijwerken van het gebeurtenis abonnement wordt gemaakt, Event Grid een validatie gebeurtenis voor het abonnement op het eind punt van het doel.
- De gebeurtenis bevat een header waarde "AEG-Event-type: SubscriptionValidation".
- De hoofd tekst van de gebeurtenis heeft hetzelfde schema als andere Event Grid-gebeurtenissen.
- De eigenschap Event type van de gebeurtenis is `Microsoft.EventGrid.SubscriptionValidationEvent` .
- De eigenschap data van de gebeurtenis bevat een `validationCode` eigenschap met een wille keurig gegenereerde teken reeks. Bijvoorbeeld "validationCode: acb13...".
- De gebeurtenis gegevens bevatten ook een `validationUrl` eigenschap met een URL voor het hand matig valideren van het abonnement.
- De matrix bevat alleen de validatie gebeurtenis. Andere gebeurtenissen worden verzonden in een afzonderlijke aanvraag nadat u de validatie code hebt teruggestuurd.
- De EventGrid DataPlane Sdk's hebben klassen die overeenkomen met de validatie van de gebeurtenis gegevens van het abonnement en het antwoord op het abonnement.

In het volgende voor beeld wordt een voor beeld van SubscriptionValidationEvent weer gegeven:

```json
[
  {
    "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
    "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "subject": "",
    "data": {
      "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
      "validationUrl": "https://rp-eastus2.eventgrid.azure.net:553/eventsubscriptions/estest/validate?id=512d38b6-c7b8-40c8-89fe-f46f9e9622b6&t=2018-04-26T20:30:54.4538837Z&apiVersion=2018-05-01-preview&token=1A1A1A1A"
    },
    "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
    "eventTime": "2018-01-25T22:12:19.4556811Z",
    "metadataVersion": "1",
    "dataVersion": "1"
  }
]
```

Als u het eigendom van het eind punt wilt bewijzen, keert u terug naar de validatie code in de eigenschap validationResponse, zoals wordt weer gegeven in het volgende voor beeld:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

U moet een HTTP 200 OK-antwoord status code retour neren. HTTP 202-geaccepteerd wordt niet herkend als een geldig Event Grid antwoord op een abonnements validatie. De HTTP-aanvraag moet binnen 30 seconden worden voltooid. Als de bewerking niet binnen 30 seconden wordt voltooid, wordt de bewerking geannuleerd en wordt deze na vijf seconden opnieuw geprobeerd. Als alle pogingen mislukken, wordt deze behandeld als validatie-Handshake-fout.

Of u kunt het abonnement hand matig valideren door een GET-aanvraag naar de validatie-URL te verzenden. Het gebeurtenis abonnement blijft in de status in behandeling totdat het is gevalideerd. De validatie-URL maakt gebruik van poort 553. Als uw firewall regels poort 553 blok keren, moeten de regels mogelijk worden bijgewerkt voor een geslaagde hand matige handshake.

Zie voor een voor beeld van het verwerken van de handshake voor het valideren van een abonnement een C#-voor [beeld](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs).

## <a name="endpoint-validation-with-cloudevents-v10"></a>Eindpunt validatie met CloudEvents v 1.0
Als u al bekend bent met Event Grid, is het mogelijk dat u op de hoogte bent van de verificatie-handshake van het Event Grid om misbruik te voor komen. CloudEvents v 1.0 implementeert zijn eigen [beveiligings semantiek](webhook-event-delivery.md) met behulp van de http-opties methode. Meer informatie hierover vindt u [hier](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). Wanneer u het CloudEvents-schema voor uitvoer gebruikt, wordt Event Grid gebruikt met de CloudEvents v 1.0-beschermings beveiliging in plaats van het Event Grid validatie gebeurtenis mechanisme.

## <a name="next-steps"></a>Volgende stappen
Raadpleeg het volgende artikel voor meer informatie over het oplossen van validaties van gebeurtenis abonnementen: 

[Problemen met validatie van gebeurtenis abonnementen oplossen](troubleshoot-subscription-validation.md)
