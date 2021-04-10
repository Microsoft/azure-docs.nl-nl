---
title: Status van asynchrone bewerkingen
description: Hierin wordt beschreven hoe u asynchrone bewerkingen in azure kunt volgen. Hierin worden de waarden weer gegeven die u gebruikt om de status van een langlopende bewerking op te halen.
ms.topic: conceptual
ms.date: 08/21/2020
ms.custom: seodec18
ms.openlocfilehash: f5823bc8f6c0d5b334a638d4ad350eb2f9ef2b28
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932740"
---
# <a name="track-asynchronous-azure-operations"></a>Asynchrone Azure-bewerkingen bijhouden

Sommige Azure REST-bewerkingen worden asynchroon uitgevoerd, omdat de bewerking niet snel kan worden voltooid. In dit artikel wordt beschreven hoe u de status van asynchrone bewerkingen kunt volgen via waarden die in het antwoord worden geretourneerd.  

## <a name="status-codes-for-asynchronous-operations"></a>Status codes voor asynchrone bewerkingen

Met een asynchrone bewerking wordt in eerste instantie een HTTP-status code geretourneerd van ofwel:

* 201 (gemaakt)
* 202 (geaccepteerd)

Wanneer de bewerking is voltooid, wordt de volgende waarde geretourneerd:

* 200 (OK)
* 204 (geen inhoud)

Raadpleeg de [documentatie van rest API](/rest/api/azure/) voor een overzicht van de antwoorden voor de bewerking die u wilt uitvoeren.

Nadat u de respons code 201 of 202 hebt opgehaald, kunt u de status van de bewerking controleren.

## <a name="url-to-monitor-status"></a>URL om de status te controleren

Er zijn twee verschillende manieren om de status van de asynchrone bewerking te controleren. U kunt de juiste aanpak bepalen door de header waarden te controleren die worden geretourneerd door de oorspronkelijke aanvraag. Zoek eerst naar:

* `Azure-AsyncOperation` -URL voor het controleren van de doorlopende status van de bewerking. Als uw bewerking deze waarde retourneert, kunt u deze gebruiken om de status van de bewerking bij te houden.
* `Retry-After` -Het aantal seconden dat moet worden gewacht voordat de status van de asynchrone bewerking wordt gecontroleerd.

Als `Azure-AsyncOperation` dit niet het geval is, zoekt u naar:

* `Location` -URL om te bepalen wanneer een bewerking is voltooid. Gebruik deze waarde alleen als Azure-AsyncOperation niet wordt geretourneerd.
* `Retry-After` -Het aantal seconden dat moet worden gewacht voordat de status van de asynchrone bewerking wordt gecontroleerd.

## <a name="azure-asyncoperation-request-and-response"></a>Azure-AsyncOperation aanvraag en-antwoord

Als u een URL van de `Azure-AsyncOperation` header waarde hebt, verzendt u een GET-aanvraag naar die URL. Gebruik de waarde van `Retry-After` om te plannen hoe vaak de status moet worden gecontroleerd. U ontvangt een reactie object dat de status van de bewerking aangeeft. Er wordt een andere reactie geretourneerd bij het controleren van de status van de bewerking met de `Location` URL. Zie voor meer informatie over de reactie van een locatie-URL [opslag account maken (202 met locatie en opnieuw proberen na)](#create-storage-account-202-with-location-and-retry-after).

De antwoord eigenschappen kunnen variëren, maar bevatten altijd de status van de asynchrone bewerking.

```json
{
    "status": "{status-value}"
}
```

In het volgende voor beeld ziet u andere waarden die kunnen worden geretourneerd door de bewerking:

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

Het fout object wordt geretourneerd wanneer de status mislukt of geannuleerd is. Alle andere waarden zijn optioneel. Het antwoord dat u ontvangt, kan er anders uitzien dan het voor beeld.

## <a name="provisioningstate-values"></a>provisioningState waarden

Bewerkingen die een resource maken, bijwerken of verwijderen (PUT, PATCH, DELETE), retour neren doorgaans een `provisioningState` waarde. Wanneer een bewerking is voltooid, wordt een van de volgende drie waarden geretourneerd:

* Geslaagd
* Mislukt
* Geannuleerd

Alle andere waarden geven aan dat de bewerking nog steeds wordt uitgevoerd. De resource provider kan een aangepaste waarde Retour neren die de status aangeeft. U kunt bijvoorbeeld **geaccepteerd** ontvangen wanneer de aanvraag is ontvangen en wordt uitgevoerd.

## <a name="example-requests-and-responses"></a>Voorbeeld aanvragen en-antwoorden

### <a name="start-virtual-machine-202-with-azure-asyncoperation"></a>Virtuele machine starten (202 met Azure-AsyncOperation)

In dit voor beeld ziet u hoe u de status van de [Start bewerking voor virtuele machines](/rest/api/compute/virtualmachines/start)bepaalt. De eerste aanvraag heeft de volgende indeling:

```HTTP
POST 
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Compute/virtualMachines/{vm-name}/start?api-version=2019-12-01
```

Hiermee wordt de status code 202 geretourneerd. Onder de waarden van de header ziet u het volgende:

```HTTP
Azure-AsyncOperation : https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2019-12-01
```

Als u de status van de asynchrone bewerking wilt controleren, verzendt u een nieuwe aanvraag naar die URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2019-12-01
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

In dit voor beeld ziet u hoe u de status van [implementaties](/rest/api/resources/resources/deployments/createorupdate) kunt bepalen voor het implementeren van resources in Azure. De eerste aanvraag heeft de volgende indeling:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/microsoft.resources/deployments/{deployment-name}?api-version=2020-06-01
```

Hiermee wordt de status code 201 geretourneerd. De hoofd tekst van het antwoord omvat:

```json
"provisioningState":"Accepted",
```

Onder de waarden van de header ziet u het volgende:

```HTTP
Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2020-06-01
```

Als u de status van de asynchrone bewerking wilt controleren, verzendt u een nieuwe aanvraag naar die URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2020-06-01
```

De antwoord tekst bevat de status van de bewerking:

```json
{
    "status": "Running"
}
```

Wanneer de implementatie is voltooid, bevat het antwoord:

```json
{
    "status": "Succeeded"
}
```

### <a name="create-storage-account-202-with-location-and-retry-after"></a>Opslag account maken (202 met locatie en opnieuw proberen na)

In dit voor beeld ziet u hoe u de status van de [Maak bewerking voor opslag accounts](/rest/api/storagerp/storageaccounts/create)kunt vaststellen. De eerste aanvraag heeft de volgende indeling:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2019-06-01
```

De aanvraag tekst bevat eigenschappen voor het opslag account:

```json
{
    "location": "South Central US",
    "properties": {},
    "sku": {
        "name": "Standard_LRS"
    },
    "kind": "Storage"
}
```

Hiermee wordt de status code 202 geretourneerd. Onder de header waarden ziet u de volgende twee waarden:

```HTTP
Location: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2019-06-01
Retry-After: 17
```

Na het wachten op het aantal seconden dat is opgegeven in opnieuw proberen na, controleert u de status van de asynchrone bewerking door een andere aanvraag naar die URL te verzenden.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2019-06-01
```

Als de aanvraag nog steeds wordt uitgevoerd, ontvangt u de status code 202. Als de aanvraag is voltooid, ontvangt u de status code 200 en de hoofd tekst van het antwoord bevat de eigenschappen van het opslag account dat is gemaakt.

## <a name="next-steps"></a>Volgende stappen

* Zie [rest API-documentatie](/rest/api/azure/)voor documentatie over elke rest-bewerking.
* Zie [resources implementeren met Resource Manager-sjablonen en resource rest API Manager](../templates/deploy-rest.md)voor informatie over het implementeren van sjablonen via de rest API van Resource Manager.