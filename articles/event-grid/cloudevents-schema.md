---
title: Gebruik Azure Event Grid met gebeurtenissen in het CloudEvents-schema
description: Beschrijft hoe u het CloudEvents-schema gebruikt voor gebeurtenissen in Azure Event Grid. De service ondersteunt gebeurtenissen in de JSON-implementatie van CloudEvents.
ms.topic: conceptual
ms.date: 11/10/2020
ms.custom: devx-track-js, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 0ee816663a385601d4a31edbf87f8c787ea5aa91
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389499"
---
# <a name="use-cloudevents-v10-schema-with-event-grid"></a>CloudEvents v1.0-schema gebruiken met Event Grid
Naast het standaardgebeurtenisschema [ondersteunt](event-schema.md)Azure Event Grid gebeurtenissen in de JSON-implementatie van [CloudEvents v1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) en [HTTP-protocolbinding](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md). [CloudEvents](https://cloudevents.io/) is een [open specificatie voor het](https://github.com/cloudevents/spec/blob/v1.0/spec.md) beschrijven van gebeurtenisgegevens.

CloudEvents vereenvoudigt de interoperabiliteit door een algemeen gebeurtenisschema te bieden voor het publiceren en gebruiken van cloudgebeurtenissen. Dit schema maakt uniforme hulpprogramma's, standaardmanier voor het routeren en verwerken van gebeurtenissen mogelijk, en universele manieren om het buitenste gebeurtenisschema te deserialiseren. Met een gemeenschappelijk schema kunt u werk gemakkelijker integreren op verschillende platformen.

CloudEvents wordt gebouwd door verschillende [samenwerkers,](https://github.com/cloudevents/spec/blob/master/community/contributors.md)waaronder Microsoft, via de [Cloud Native Computing Foundation.](https://www.cncf.io/) Deze is momenteel beschikbaar als versie 1.0.

In dit artikel wordt beschreven hoe u het CloudEvents-schema gebruikt met Event Grid.

## <a name="cloudevent-schema"></a>CloudEvent-schema

Hier is een voorbeeld van een Azure Blob Storage gebeurtenis in CloudEvents-indeling:

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

Zie [CloudEvents v1.0](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes)voor een gedetailleerde beschrijving van de beschikbare velden, hun typen en definities.

De headerswaarden voor gebeurtenissen die worden geleverd in het CloudEvents-schema en het Event Grid schema zijn hetzelfde, met uitzondering van `content-type` . Voor het CloudEvents-schema is die headerwaarde `"content-type":"application/cloudevents+json; charset=utf-8"` . Voor het Event Grid schema is die headerwaarde `"content-type":"application/json; charset=utf-8"` .

## <a name="configure-event-grid-for-cloudevents"></a>Een Event Grid voor CloudEvents configureren

U kunt Event Grid voor zowel invoer als uitvoer van gebeurtenissen in het CloudEvents-schema. In de volgende tabel worden de mogelijke transformaties beschreven:

 Event Grid resource | Invoerschema       | Leveringsschema
|---------------------|-------------------|---------------------
| Systeemonderwerpen       | Event Grid-schema | Event Grid of CloudEvents-schema
| Aangepaste onderwerpen/domeinen | Event Grid-schema | Event Grid of CloudEvents-schema
| Aangepaste onderwerpen/domeinen | CloudEvents-schema | CloudEvents-schema
| Aangepaste onderwerpen/domeinen | Aangepast schema     | Aangepast schema, Event Grid schema of CloudEvents-schema
| Partneronderwerpen       | CloudEvents-schema | CloudEvents-schema

Voor alle gebeurtenisschema's Event Grid validatie vereist wanneer u publiceert naar een Event Grid onderwerp en wanneer u een gebeurtenisabonnement maakt.

Zie beveiliging en verificatie Event Grid [meer informatie.](security-authentication.md)

### <a name="input-schema"></a>Invoerschema

U stelt het invoerschema voor een aangepast onderwerp in wanneer u het aangepaste onderwerp maakt.

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

### <a name="output-schema"></a>Uitvoerschema

U stelt het uitvoerschema in wanneer u het gebeurtenisabonnement maakt.

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

 Op dit moment kunt u geen trigger Event Grid voor een Azure Functions-app wanneer de gebeurtenis wordt geleverd in het CloudEvents-schema. Gebruik een HTTP-trigger. Zie Using [CloudEvents with Azure Functions (CloudEvents](#azure-functions)gebruiken met Azure Functions) voor voorbeelden van het implementeren van een HTTP-trigger die gebeurtenissen in het CloudEvents-schema ontvangt.

## <a name="endpoint-validation-with-cloudevents-v10"></a>Eindpuntvalidatie met CloudEvents v1.0

Als u al bekend bent met Event Grid, bent u mogelijk op de hoogte van de eindpuntvalidatiehandhake voor het voorkomen van misbruik. CloudEvents v1.0 implementeert een eigen semantiek voor beveiliging tegen misbruik met [behulp](webhook-event-delivery.md) van de HTTP OPTIONS-methode. Zie HTTP [1.1 Web Hooks for event delivery - Version 1.0 (HTTP 1.1-web hooks](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection)voor levering van gebeurtenissen - versie 1.0) voor meer informatie. Wanneer u het CloudEvents-schema voor uitvoer gebruikt, gebruikt Event Grid de cloudgebeurtenisbeveiliging CloudEvents v1.0 in plaats van het mechanisme voor Event Grid validatiegebeurtenis.

<a name="azure-functions"></a>

## <a name="use-with-azure-functions"></a>Gebruiken met Azure Functions

De [Azure Functions Event Grid biedt](../azure-functions/functions-bindings-event-grid.md) geen systeemeigen ondersteuning voor CloudEvents, dus http-geactiveerde functies worden gebruikt om CloudEvents-berichten te lezen. Wanneer u een HTTP-trigger gebruikt om CloudEvents te lezen, moet u code schrijven voor wat de trigger Event Grid automatisch doet:

* Verzendt een validatiereactie op een [abonnementsvalidatieaanvraag](../event-grid/webhook-event-delivery.md)
* Roept de functie eenmaal aan per element van de gebeurtenis array in de aanvraag body

Zie de referentiedocumentatie over [HTTP-triggerbinding](../azure-functions/functions-bindings-http-webhook.md)voor informatie over de URL die moet worden gebruikt voor het lokaal aanroepen van de functie of wanneer deze wordt uitgevoerd in Azure.

De volgende C#-voorbeeldcode voor een HTTP-trigger simuleert Event Grid triggergedrag. Gebruik dit voorbeeld voor gebeurtenissen die worden geleverd in het CloudEvents-schema.

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

De volgende JavaScript-voorbeeldcode voor een HTTP-trigger simuleert Event Grid triggergedrag. Gebruik dit voorbeeld voor gebeurtenissen die worden geleverd in het CloudEvents-schema.

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

* Zie Bewaking van de levering van berichten Event Grid informatie over het bewaken [van leveringen van gebeurtenissen.](monitor-event-delivery.md)
* We raden u aan om [cloudevents](https://github.com/cloudevents/spec/blob/master/community/CONTRIBUTING.md)te testen, er commentaar van te maken en bij te dragen.
* Zie abonnementschema voor Azure Event Grid meer informatie [Event Grid het maken van een abonnement.](subscription-creation-schema.md)
