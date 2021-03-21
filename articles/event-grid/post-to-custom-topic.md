---
title: Het onderwerp gebeurtenis op aangepast Azure Event Grid plaatsen
description: In dit artikel wordt beschreven hoe u een gebeurtenis op een aangepast onderwerp plaatst. Hier ziet u de indeling van de post-en gebeurtenis gegevens.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: ed126487938e524264c94544903460854ffc4d41
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98681597"
---
# <a name="post-to-custom-topic-for-azure-event-grid"></a>Bericht plaatsen op aangepast onderwerp voor Azure Event Grid

In dit artikel wordt beschreven hoe u een gebeurtenis op een aangepast onderwerp plaatst. Hier ziet u de indeling van de post-en gebeurtenis gegevens. De [Service Level Agreement (Sla)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/) is alleen van toepassing op Posts die overeenkomen met de verwachte indeling.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="endpoint"></a>Eindpunt

Gebruik de URI-indeling als u het HTTP-bericht naar een aangepast onderwerp verzendt: `https://<topic-endpoint>?api-version=2018-01-01` .

Een geldige URI is bijvoorbeeld: `https://exampletopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01` .

Als u het eind punt voor een aangepast onderwerp met Azure CLI wilt ophalen, gebruikt u:

```azurecli-interactive
az eventgrid topic show --name <topic-name> -g <topic-resource-group> --query "endpoint"
```

Als u het eind punt voor een aangepast onderwerp met Azure PowerShell wilt ophalen, gebruikt u:

```powershell
(Get-AzEventGridTopic -ResourceGroupName <topic-resource-group> -Name <topic-name>).Endpoint
```

## <a name="header"></a>Header

Neem in de aanvraag een header waarde op met de naam `aeg-sas-key` die een sleutel voor verificatie bevat.

Een geldige header-waarde is bijvoorbeeld `aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==` .

Als u de sleutel voor een aangepast onderwerp met Azure CLI wilt ophalen, gebruikt u:

```azurecli
az eventgrid topic key list --name <topic-name> -g <topic-resource-group> --query "key1"
```

Als u de sleutel voor een aangepast onderwerp wilt ophalen met Power shell, gebruikt u:

```powershell
(Get-AzEventGridTopicKey -ResourceGroupName <topic-resource-group> -Name <topic-name>).Key1
```

## <a name="event-data"></a>Gebeurtenisgegevens

Voor aangepaste onderwerpen bevatten de gegevens op het hoogste niveau dezelfde velden als standaard, door de resource gedefinieerde gebeurtenissen. Een van deze eigenschappen is een gegevens eigenschap met eigenschappen die uniek zijn voor het aangepaste onderwerp. Als uitgever van de gebeurtenis bepaalt u de eigenschappen voor dat gegevens object. Gebruik het volgende schema:

```json
[
  {
    "id": string,    
    "eventType": string,
    "subject": string,
    "eventTime": string-in-date-time-format,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string
  }
]
```

Zie [Azure Event grid-gebeurtenis schema](event-schema.md)voor een beschrijving van deze eigenschappen. Bij het posten van gebeurtenissen naar een event grid-onderwerp kan de matrix een totale grootte hebben van Maxi maal 1 MB. De Maxi maal toegestane grootte voor een gebeurtenis is ook 1 MB. Gebeurtenissen van meer dan 64 KB worden in rekening gebracht in stappen van 64-KB. 

Een geldig schema voor gebeurtenis gegevens is bijvoorbeeld:

```json
[{
  "id": "1807",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2017-08-10T21:03:07+00:00",
  "data": {
    "make": "Ducati",
    "model": "Monster"
  },
  "dataVersion": "1.0"
}]
```

## <a name="response"></a>Antwoord

Nadat u naar het onderwerp-eind punt hebt gepost, ontvangt u een antwoord. Het antwoord is een standaard-HTTP-antwoord code. Enkele veelvoorkomende reacties zijn:

|Resultaat  |Antwoord  |
|---------|---------|
|Geslaagd  | 200 OK  |
|De gebeurtenis gegevens hebben een onjuiste indeling | 400 Ongeldige aanvraag |
|Ongeldige toegangs sleutel | 401 Onbevoegd |
|Onjuist eind punt | 404 Niet gevonden |
|Matrix of gebeurtenis overschrijdt grootte limieten | 413 Payload is te groot |

Voor fouten heeft de bericht tekst de volgende indeling:

```json
{
    "error": {
        "code": "<HTTP status code>",
        "message": "<description>",
        "details": [{
            "code": "<HTTP status code>",
            "message": "<description>"
    }]
  }
}
```

## <a name="next-steps"></a>Volgende stappen

* Zie [Event grid bericht bezorging bewaken](monitor-event-delivery.md)voor meer informatie over het bewaken van gebeurtenis leveringen.
* Zie [Event grid beveiliging en verificatie](security-authentication.md)voor meer informatie over de verificatie sleutel.
* Zie [Event grid Subscription schema](subscription-creation-schema.md)voor meer informatie over het maken van een Azure Event grid-abonnement.
