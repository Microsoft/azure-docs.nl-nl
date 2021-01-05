---
title: Azure Event Grid gebruiken met gebeurtenissen in het CloudEvents-schema
description: Hierin wordt beschreven hoe u het CloudEvents-schema gebruikt voor gebeurtenissen in Azure Event Grid. De service ondersteunt gebeurtenissen in de JSON-implementatie van CloudEvents.
ms.topic: conceptual
ms.date: 11/10/2020
ms.custom: devx-track-js, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 93e514e0eac40cfaa51d410a446608deca3cbd6d
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901399"
---
# <a name="use-cloudevents-v10-schema-with-event-grid"></a>CloudEvents v 1.0-schema gebruiken met Event Grid
Naast het [standaard schema](event-schema.md)van de gebeurtenis, ondersteunt Azure Event grid systeem eigen gebeurtenissen in de [JSON-implementatie van CloudEvents v 1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) en [http-protocol binding](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md). [CloudEvents](https://cloudevents.io/) is een [open specificatie](https://github.com/cloudevents/spec/blob/v1.0/spec.md) voor het beschrijven van gebeurtenis gegevens.

CloudEvents vereenvoudigt interoperabiliteit door een gemeen schappelijk gebeurtenis schema te bieden voor het publiceren en gebruiken van Cloud gebeurtenissen. Dit schema biedt uniforme hulp middelen, standaard manieren van route ring en afhandeling van gebeurtenissen en universele manieren om het buitenste gebeurtenis schema te deserialiseren. Met een gemeen schappelijk schema kunt u gemakkelijker werk op verschillende platforms integreren.

CloudEvents wordt gebouwd door verschillende deel [nemers](https://github.com/cloudevents/spec/blob/master/community/contributors.md), waaronder micro soft, via de [systeem eigen Cloud Computing Foundation](https://www.cncf.io/). Het is momenteel beschikbaar als versie 1,0.

In dit artikel wordt beschreven hoe u het CloudEvents-schema gebruikt met Event Grid.

## <a name="cloudevent-schema"></a>CloudEvent-schema

Hier volgt een voor beeld van een Azure Blob Storage-gebeurtenis in de indeling CloudEvents:

``` JSON
{
    "specversion": "1.0",
    "type": "Microsoft.Storage.BlobCreated",  
    "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}",
    "id": "9aeb0fdf-c01e-0131-0922-9eb54906e209",
    "time": "2019-11-18T15:13:39.4589254Z",
    "subject": "blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "dataschema": "#",
    "data": {
        "api": "PutBlockList",
        "clientRequestId": "4c5dd7fb-2c48-4a27-bb30-5361b5de920a",
        "requestId": "9aeb0fdf-c01e-0131-0922-9eb549000000",
        "eTag": "0x8D76C39E4407333",
        "contentType": "image/png",
        "contentLength": 30699,
        "blobType": "BlockBlob",
        "url": "https://gridtesting.blob.core.windows.net/testcontainer/{new-file}",
        "sequencer": "000000000000000000000000000099240000000000c41c18",
        "storageDiagnostics": {
            "batchId": "681fe319-3006-00a8-0022-9e7cde000000"
        }
    }
}
```

Zie [CloudEvents v 1.0](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes)voor een gedetailleerde beschrijving van de beschik bare velden, hun typen en definities.

De waarden van headers voor gebeurtenissen die in het CloudEvents-schema en het Event Grid schema worden bezorgd, zijn hetzelfde, behalve voor `content-type` . Voor het CloudEvents-schema is die header waarde `"content-type":"application/cloudevents+json; charset=utf-8"` . Voor het Event Grid schema is die header waarde `"content-type":"application/json; charset=utf-8"` .

## <a name="configure-event-grid-for-cloudevents"></a>Event Grid configureren voor CloudEvents

U kunt Event Grid gebruiken voor zowel de invoer als uitvoer van gebeurtenissen in het CloudEvents-schema. In de volgende tabel worden de mogelijke trans formaties beschreven:

 Event Grid resource | Invoer schema       | Leverings schema
|---------------------|-------------------|---------------------
| Systeem onderwerpen       | Event Grid-schema | Event Grid schema of CloudEvent-schema
| Gebruikers onderwerpen/domeinen | Event Grid-schema | Event Grid-schema
| Gebruikers onderwerpen/domeinen | CloudEvent-schema | CloudEvent-schema
| Gebruikers onderwerpen/domeinen | Aangepast schema     | Aangepast schema, Event Grid schema of CloudEvent-schema
| PartnerTopics       | CloudEvent-schema | CloudEvent-schema

Voor alle gebeurtenis schema's moet Event Grid worden gevalideerd wanneer u naar een Event Grid onderwerp publiceert en wanneer u een gebeurtenis abonnement maakt.

Zie [Event grid beveiliging en verificatie](security-authentication.md)voor meer informatie.

### <a name="input-schema"></a>Invoer schema

Wanneer u het aangepaste onderwerp maakt, stelt u het invoer schema voor een aangepast onderwerp in.

Gebruik voor de Azure CLI:

```azurecli-interactive
az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventschemav1_0
```

Gebruik voor PowerShell:

```azurepowershell-interactive
New-AzEventGridTopic `
  -ResourceGroupName gridResourceGroup `
  -Location westcentralus `
  -Name <topic_name> `
  -InputSchema CloudEventSchemaV1_0
```

### <a name="output-schema"></a>Uitvoer schema

U stelt het uitvoer schema in wanneer u het gebeurtenis abonnement maakt.

Gebruik voor de Azure CLI:

```azurecli-interactive
topicID=$(az eventgrid topic show --name <topic-name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --name <event_subscription_name> \
  --source-resource-id $topicID \
  --endpoint <endpoint_URL> \
  --event-delivery-schema cloudeventschemav1_0
```

Gebruik voor PowerShell:
```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeliverySchema CloudEventSchemaV1_0
```

 Op dit moment kunt u geen Event Grid trigger gebruiken voor een Azure Functions-app wanneer de gebeurtenis wordt bezorgd in het CloudEvents-schema. Gebruik een HTTP-trigger. Zie [using CloudEvents with Azure functions](#azure-functions)(Engelstalig) voor voor beelden van het implementeren van een http-trigger die gebeurtenissen ontvangt in het CloudEvents-schema.

## <a name="endpoint-validation-with-cloudevents-v10"></a>Eindpunt validatie met CloudEvents v 1.0

Als u al bekend bent met Event Grid, is het mogelijk dat u op de hoogte bent van de validatie-Handshake van het eind punt om misbruik te voor komen. CloudEvents v 1.0 implementeert zijn eigen [beveiligings semantiek voor misbruik](webhook-event-delivery.md) door gebruik te maken van de http-opties methode. Zie [HTTP 1,1-Webhooks voor gebeurtenis levering-versie 1,0 voor](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection)meer informatie hierover. Wanneer u het CloudEvents-schema voor uitvoer gebruikt, maakt Event Grid gebruik van de CloudEvents v 1.0-misbruik beveiliging in plaats van het Event Grid validatie gebeurtenis mechanisme.

<a name="azure-functions"></a>

## <a name="use-with-azure-functions"></a>Gebruiken met Azure Functions

De [Azure Functions Event grid-binding](../azure-functions/functions-bindings-event-grid.md) biedt geen systeem eigen ondersteuning voor CloudEvents, dus er worden door http geactiveerde functies gebruikt voor het lezen van CloudEvents-berichten. Wanneer u een HTTP-trigger gebruikt om CloudEvents te lezen, moet u code schrijven voor wat de Event Grid trigger automatisch doet:

* Hiermee wordt een validatie reactie verzonden naar een aanvraag voor een [abonnements validatie](../event-grid/webhook-event-delivery.md)
* Hiermee wordt de functie aangeroepen per element van de gebeurtenis matrix die is opgenomen in de hoofd tekst van de aanvraag

Voor informatie over de URL die moet worden gebruikt om de functie lokaal aan te roepen of wanneer deze wordt uitgevoerd in azure, raadpleegt u de [referentie documentatie voor de http-trigger binding](../azure-functions/functions-bindings-http-webhook.md).

Met de volgende C#-voorbeeld code voor een HTTP-trigger wordt Event Grid trigger gedrag gesimuleerd. Gebruik dit voor beeld voor gebeurtenissen die in het CloudEvents-schema worden bezorgd.

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run([HttpTrigger(AuthorizationLevel.Anonymous, "post", "options", Route = null)]HttpRequestMessage req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");
    if (req.Method == HttpMethod.Options)
    {
        // If the request is for subscription validation, send back the validation code
        
        var response = req.CreateResponse(HttpStatusCode.OK);
        response.Headers.Add("Webhook-Allowed-Origin", "eventgrid.azure.net");

        return response;
    }

    var requestmessage = await req.Content.ReadAsStringAsync();
    var message = JToken.Parse(requestmessage);

    // The request isn't for subscription validation, so it's for an event.
    // CloudEvents schema delivers one event at a time.
    log.LogInformation($"Source: {message["source"]}");
    log.LogInformation($"Time: {message["eventTime"]}");
    log.LogInformation($"Event data: {message["data"].ToString()}");

    return req.CreateResponse(HttpStatusCode.OK);
}
```

Met de volgende Java script-voorbeeld code voor een HTTP-trigger wordt Event Grid trigger gedrag gesimuleerd. Gebruik dit voor beeld voor gebeurtenissen die in het CloudEvents-schema worden bezorgd.

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    
    if (req.method == "OPTIONS") {
        // If the request is for subscription validation, send back the validation code
        
        context.log('Validate request received');
        context.res = {
            status: 200,
            headers: {
                'Webhook-Allowed-Origin': 'eventgrid.azure.net',
            },
         };
    }
    else
    {
        var message = req.body;
        
        // The request isn't for subscription validation, so it's for an event.
        // CloudEvents schema delivers one event at a time.
        var event = JSON.parse(message);
        context.log('Source: ' + event.source);
        context.log('Time: ' + event.eventTime);
        context.log('Data: ' + JSON.stringify(event.data));
    }
 
    context.done();
};
```

## <a name="next-steps"></a>Volgende stappen

* Zie [Event grid bericht bezorging bewaken](monitor-event-delivery.md)voor meer informatie over het bewaken van gebeurtenis leveringen.
* We raden u aan om CloudEvents te testen, opmerkingen [te leveren en bij te dragen](https://github.com/cloudevents/spec/blob/master/community/CONTRIBUTING.md).
* Zie [Event grid Subscription schema](subscription-creation-schema.md)voor meer informatie over het maken van een Azure Event grid-abonnement.
