---
title: Status van Event Grid asynchrone bewerkingen
description: Hierin wordt beschreven hoe u Event Grid asynchrone bewerkingen in azure bijhoudt. Hierin worden de waarden weer gegeven die u gebruikt om de status van een langlopende bewerking op te halen.
ms.topic: conceptual
ms.date: 07/07/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: baae7b097a0b696d405c0e7ea3d3bdeb326f23b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89011681"
---
# <a name="track-event-grid-asynchronous-azure-operations"></a>Event Grid asynchrone bewerkingen van Azure bijhouden
Sommige Azure REST-bewerkingen worden asynchroon uitgevoerd, omdat de bewerking niet snel kan worden voltooid. In dit artikel wordt beschreven hoe u de status van asynchrone bewerkingen kunt volgen via waarden die in het antwoord worden geretourneerd.  

## <a name="status-codes-for-asynchronous-operations"></a>Status codes voor asynchrone bewerkingen
Met een asynchrone bewerking wordt in eerste instantie een HTTP-status code geretourneerd van ofwel:

* 201 (gemaakt)
* 202 (geaccepteerd) 

Wanneer de bewerking is voltooid, wordt de volgende waarde geretourneerd:

* 200 (OK)
* 204 (geen inhoud) 

Raadpleeg de [documentatie van rest API](/rest/api/) voor een overzicht van de antwoorden voor de bewerking die u wilt uitvoeren.

## <a name="monitor-status-of-operation"></a>Bewaak de status van de bewerking
De asynchrone REST-bewerkingen retour neren koptekst waarden, die u gebruikt om de status van de bewerking te bepalen. Er zijn mogelijk drie header-waarden om te onderzoeken:

* `Azure-AsyncOperation` -URL voor het controleren van de doorlopende status van de bewerking. Als uw bewerking deze waarde retourneert, moet u deze altijd gebruiken (in plaats van locatie) om de status van de bewerking bij te houden.
* `Location` -URL om te bepalen wanneer een bewerking is voltooid. Gebruik deze waarde alleen wanneer Azure-AsyncOperation niet wordt geretourneerd.
* `Retry-After` -Het aantal seconden dat moet worden gewacht voordat de status van de asynchrone bewerking wordt gecontroleerd.

Niet elke asynchrone bewerking retourneert echter al deze waarden. Het is bijvoorbeeld mogelijk dat u de waarde voor de Azure-AsyncOperation-header voor één bewerking moet evalueren en de waarde van de locatie-header voor een andere bewerking. 

U haalt de waarden van de header op zoals u een header waarde voor een aanvraag zou ophalen. In C# haalt u bijvoorbeeld de waarde header op van een `HttpWebResponse` object `response` met de naam met de volgende code:

```cs
response.Headers.GetValues("Azure-AsyncOperation").GetValue(0)
```

## <a name="azure-asyncoperation-request-and-response"></a>Azure-AsyncOperation aanvraag en-antwoord

Als u de status van de asynchrone bewerking wilt ophalen, verzendt u een GET-aanvraag naar de URL in Azure-AsyncOperation header-waarde.

De hoofd tekst van het antwoord van deze bewerking bevat informatie over de bewerking. In het volgende voor beeld ziet u de mogelijke waarden die zijn geretourneerd door de bewerking:

```json
{
    "id": "{resource path from GET operation}",
    "name": "{operation-id}", 
    "status" : "Succeeded | Failed | Canceled | {resource provider values}", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
    "percentComplete": {double between 0 and 100 },
    "properties": {
        /* Specific resource provider values for successful operations */
    },
    "error" : { 
        "code": "{error code}",  
        "message": "{error description}" 
    }
}
```

Alleen `status` voor alle antwoorden wordt geretourneerd. Het fout object wordt geretourneerd wanneer de status mislukt of geannuleerd is. Alle andere waarden zijn optioneel. Daarom kan het antwoord dat u ontvangt, er anders uitzien dan het voor beeld.

## <a name="provisioningstate-values"></a>provisioningState waarden

Bewerkingen die een resource maken, bijwerken of verwijderen (PUT, PATCH, DELETE), retour neren doorgaans een `provisioningState` waarde. Wanneer een bewerking is voltooid, wordt een van de volgende drie waarden geretourneerd: 

* Geslaagd
* Mislukt
* Geannuleerd

Alle andere waarden geven aan dat de bewerking nog steeds wordt uitgevoerd. De resource provider kan een aangepaste waarde Retour neren die de status aangeeft. U kunt bijvoorbeeld **geaccepteerd** ontvangen wanneer de aanvraag is ontvangen en wordt uitgevoerd.


## <a name="example-requests-and-responses"></a>Voorbeeld aanvragen en-antwoorden

### <a name="start-virtual-machine-202-with-azure-asyncoperation"></a>Virtuele machine starten (202 met Azure-AsyncOperation)
In dit voor beeld ziet u hoe u de status van de **Start** bewerking voor virtuele machines bepaalt. De eerste aanvraag heeft de volgende indeling:

```HTTP
POST 
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Compute/virtualMachines/{vm-name}/start?api-version=2016-03-30
```

Hiermee wordt de status code 202 geretourneerd. Onder de waarden van de header ziet u het volgende:

```HTTP
Azure-AsyncOperation : https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

Als u de status van de asynchrone bewerking wilt controleren, verzendt u een nieuwe aanvraag naar die URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

De antwoord tekst bevat de status van de bewerking:

```json
{
  "startTime": "2017-01-06T18:58:24.7596323+00:00",
  "status": "InProgress",
  "name": "9a062a88-e463-4697-bef2-fe039df73a02"
}
```

### <a name="deploy-resources-201-with-azure-asyncoperation"></a>Resources implementeren (201 met Azure-AsyncOperation)

In dit voor beeld ziet u hoe u de status van **implementaties** kunt bepalen voor het implementeren van resources in Azure. De eerste aanvraag heeft de volgende indeling:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/microsoft.resources/deployments/{deployment-name}?api-version=2016-09-01
```

Hiermee wordt de status code 201 geretourneerd. De hoofd tekst van het antwoord omvat:

```json
"provisioningState":"Accepted",
```

Onder de waarden van de header ziet u het volgende:

```HTTP
Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

Als u de status van de asynchrone bewerking wilt controleren, verzendt u een nieuwe aanvraag naar die URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

De antwoord tekst bevat de status van de bewerking:

```json
{"status":"Running"}
```

Wanneer de implementatie is voltooid, bevat het antwoord:

```json
{"status":"Succeeded"}
```

### <a name="create-storage-account-202-with-location-and-retry-after"></a>Opslag account maken (202 met locatie en opnieuw proberen na)

In dit voor beeld ziet u hoe u de status van de **Maak** bewerking voor opslag accounts kunt vaststellen. De eerste aanvraag heeft de volgende indeling:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2016-01-01
```

De aanvraag tekst bevat eigenschappen voor het opslag account:

```json
{ "location": "South Central US", "properties": {}, "sku": { "name": "Standard_LRS" }, "kind": "Storage" }
```

Hiermee wordt de status code 202 geretourneerd. Onder de header waarden ziet u de volgende twee waarden:

```HTTP
Location: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
Retry-After: 17
```

Na het wachten op het aantal seconden dat is opgegeven in opnieuw proberen na, controleert u de status van de asynchrone bewerking door een andere aanvraag naar die URL te verzenden.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
```

Als de aanvraag nog steeds wordt uitgevoerd, ontvangt u de status code 202. Als de aanvraag is voltooid, ontvangt u de status code 200 en de hoofd tekst van het antwoord bevat de eigenschappen van het opslag account dat is gemaakt.

## <a name="next-steps"></a>Volgende stappen

* Zie [rest API-documentatie](/rest/api/)voor documentatie over elke rest-bewerking.
* Zie [resources implementeren met Resource Manager-sjablonen en resource rest API Manager](../azure-resource-manager/templates/deploy-rest.md)voor informatie over het implementeren van sjablonen via de rest API van Resource Manager.