---
title: Azure Event Grid-probleemoplossings validatie van abonnementen
description: In dit artikel wordt beschreven hoe u de validatie van abonnementen kunt oplossen.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 857760182675d5673a3b09495c2faaf7372a4164
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94592937"
---
# <a name="troubleshoot-azure-event-grid-subscription-validations"></a>Problemen met de validatie van Azure Event Grid-abonnementen oplossen
Bij het maken van een gebeurtenis abonnement, als er een fout bericht `The attempt to validate the provided endpoint https://your-endpoint-here failed. For more details, visit https://aka.ms/esvalidation` wordt weer gegeven, geeft dit aan dat er een fout is opgetreden in de validatie-handshake. U kunt deze fout oplossen door de volgende aspecten te controleren:

- Voer een HTTP POST naar uw webhook-URL uit met een voor beeld van een [SubscriptionValidationEvent](webhook-event-delivery.md#validation-details) -aanvraag tekst met behulp van Postman of krul of een soortgelijk hulp programma.
- Als uw webhook het handshake-mechanisme voor synchrone validatie implementeert, controleert u of de ValidationCode als onderdeel van het antwoord wordt geretourneerd.
- Als uw webhook een asynchroon validatie-handshake-mechanisme implementeert, controleert u of het HTTP-bericht 200 OK is.
- Als uw webhook `403 (Forbidden)` in het antwoord wordt geretourneerd, controleert u of uw webhook zich achter een Azure-toepassing gateway of Web Application firewall bevindt. Als dat het geval is, moet u deze firewall regels uitschakelen en opnieuw een HTTP-POST doen:
    - 920300 (aanvraag mist een Accept-header)
    - 942430 (beperkte SQL-teken anomalie detectie (argumenten): aantal speciale tekens is overschreden (12))
    - 920230 (meerdere URL-code ring gedetecteerd)
    - 942130 (SQL-injectie aanval: SQL-tautology gedetecteerd.)
    - 931130 (mogelijke RFI-aanval (Remote File Include) = off-domein verwijzing/koppeling)

> [!IMPORTANT]
> Zie [webhook Event Delivery](webhook-event-delivery.md)(Engelstalig) voor gedetailleerde informatie over eindpunt validatie voor webhooks.

In de volgende secties ziet u hoe u een gebeurtenis abonnementen kunt valideren met behulp van Postman en krul.  

## <a name="validate-event-grid-event-subscription-using-postman"></a>Event Grid gebeurtenis abonnement valideren met behulp van postman
Hier volgt een voor beeld van het gebruik van Postman voor het valideren van een webhook-abonnement van een Event Grid gebeurtenis: 

![Validatie van gebeurtenis abonnementen Event grid met behulp van postman](./media/troubleshoot-subscription-validation/event-subscription-validation-postman.png)

Hier volgt een voor beeld van een JSON- **SubscriptionValidationEvent** :

```json
[
  {
    "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
    "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "subject": "",
    "data": {
      "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
    },
    "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
    "eventTime": "2018-01-25T22:12:19.4556811Z",
    "metadataVersion": "1",
    "dataVersion": "1"
  }
]
```

Dit is het voor beeld van een geslaagde reactie:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Zie [endpoint validator with Event grid Events](webhook-event-delivery.md#endpoint-validation-with-event-grid-events)(Engelstalig) voor meer informatie over Event grid gebeurtenis validatie voor webhooks.


## <a name="validate-event-grid-event-subscription-using-curl"></a>Event Grid gebeurtenis abonnement met behulp van krul valideren 
Hier volgt een voor beeld van een krul opdracht voor het valideren van een webhook-abonnement van een Event Grid gebeurtenis: 

```bash
curl -X POST -d '[{"id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66","topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx","subject": "","data": {"validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"},"eventType": "Microsoft.EventGrid.SubscriptionValidationEvent","eventTime": "2018-01-25T22:12:19.4556811Z", "metadataVersion": "1","dataVersion": "1"}]' -H 'Content-Type: application/json' https://{your-webhook-url.com}
```

## <a name="validate-cloud-event-subscription-using-postman"></a>Cloud gebeurtenis abonnement valideren met behulp van postman
Hier volgt een voor beeld van het gebruik van Postman voor het valideren van een webhook-abonnement van een Cloud gebeurtenis: 

![Validatie van abonnement op Cloud gebeurtenis met behulp van postman](./media/troubleshoot-subscription-validation/cloud-event-subscription-validation-postman.png)

Gebruik de **http-opties** methode voor validatie met Cloud gebeurtenissen. Zie [endpoint validator with Cloud Events](webhook-event-delivery.md#endpoint-validation-with-event-grid-events)(Engelstalig) voor meer informatie over de validatie van de Cloud gebeurtenis voor webhooks.

## <a name="troubleshoot-event-subscription-validation"></a>Problemen met validatie van gebeurtenis abonnementen oplossen

## <a name="next-steps"></a>Volgende stappen
Als u meer hulp nodig hebt, kunt u uw probleem in het [stack overflow forum](https://stackoverflow.com/questions/tagged/azure-eventgrid) plaatsen of een [ondersteunings ticket](https://azure.microsoft.com/support/options/)openen. 
