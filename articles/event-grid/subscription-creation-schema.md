---
title: Schema voor Azure Event Grid abonnement
description: In dit artikel worden de eigenschappen beschreven voor het abonneren op een gebeurtenis met Azure Event Grid. Schema van Event Grid abonnement.
ms.topic: reference
ms.date: 07/07/2020
ms.openlocfilehash: f6e33171cbba65cfeaca49ab6a8954be8bb89acb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102199948"
---
# <a name="event-grid-subscription-schema"></a>Schema voor Event Grid abonnement

Als u een Event Grid-abonnement wilt maken, stuurt u een aanvraag naar de bewerking gebeurtenis abonnement maken. Gebruik de volgende indeling:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Als u bijvoorbeeld een gebeurtenis abonnement wilt maken voor een opslag account met `examplestorage` de naam in een resource groep `examplegroup` met de naam, gebruikt u de volgende indeling:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

De naam van het gebeurtenis abonnement moet 3-64 tekens lang zijn en mag alleen a-z, A-Z, 0-9 en '-' bevatten. In het artikel worden de eigenschappen en het schema voor de hoofd tekst van de aanvraag beschreven.
 
## <a name="event-subscription-properties"></a>Eigenschappen van gebeurtenis abonnement

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| doel | object | Het object dat het eind punt definieert. |
| filter | object | Een optioneel veld voor het filteren van de typen gebeurtenissen. |

### <a name="destination-object"></a>doel object

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| endpointType | tekenreeks | Het type eind punt voor het abonnement (webhook/HTTP, Event hub of queue). | 
| endpointUrl | tekenreeks | De doel-URL voor gebeurtenissen in dit gebeurtenis abonnement. | 

### <a name="filter-object"></a>object filteren

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| includedEventTypes | matrix | Overeenkomen wanneer het gebeurtenis type in het gebeurtenis bericht exact overeenkomt met een van deze gebeurtenis type namen. Retourneert een fout wanneer de gebeurtenis naam niet overeenkomt met de geregistreerde gebeurtenis type namen voor de bron van de gebeurtenis. Standaard komt alle gebeurtenis typen overeen. |
| subjectBeginsWith | tekenreeks | Een voorvoegsel filter voor het veld onderwerp in het gebeurtenis bericht. De standaard waarde of de lege teken reeks komt overeen met alle. | 
| subjectEndsWith | tekenreeks | Een achtervoegsel-match filter voor het veld onderwerp in het gebeurtenis bericht. De standaard waarde of de lege teken reeks komt overeen met alle. |
| isSubjectCaseSensitive | tekenreeks | Hiermee wordt een hoofdletter gevoelige overeenkomst voor filters beheerd. |
| enableAdvancedFilteringOnArrays | booleaans | Maakt het gebruik van matrices voor sleutels in geavanceerde filters mogelijk. Zie [Advanced filtering](event-filtering.md#advanced-filtering)voor meer informatie. |


## <a name="example-subscription-schema"></a>Voorbeeld schema voor abonnementen

```json
{
  "properties": {
    "destination": {
      "endpointType": "webhook",
      "properties": {
          "endpointUrl": "https://example.azurewebsites.net/api/HttpTriggerCSharp1?code=VXbGWce53l48Mt8wuotr0GPmyJ/nDT4hgdFj9DpBiRt38qqnnm5OFg=="
      }
    },
    "filter": {
      "includedEventTypes": [ "Microsoft.Storage.BlobCreated", "Microsoft.Storage.BlobDeleted" ],
      "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
      "subjectEndsWith": ".jpg",
      "isSubjectCaseSensitive ": "true"
    }
  }
}
```

## <a name="next-steps"></a>Volgende stappen

* Zie [Wat is Event grid?](overview.md) voor een inleiding tot Event grid.
