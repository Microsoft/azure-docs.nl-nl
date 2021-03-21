---
title: Referentiemateriaal voor aangepaste resource-cache
description: Aangepaste Naslag informatie voor de resource-cache voor aangepaste Azure-resource providers. In dit artikel worden de vereisten beschreven voor eind punten die aangepaste cache-resources implementeren.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: e1b8c44f020d18066423eed236018308fe88b607
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "75650381"
---
# <a name="custom-resource-cache-reference"></a>Verwijzing naar aangepaste resource-cache

In dit artikel worden de vereisten beschreven voor eind punten die aangepaste cache-resources implementeren. Als u niet bekend bent met aangepaste Azure-resource providers, raadpleegt u [het overzicht van aangepaste resource providers](overview.md).

## <a name="how-to-define-a-cache-resource-endpoint"></a>Het definiëren van een cache resource-eind punt

U kunt een proxy bron maken door de **routingType** aan te geven als ' proxy, cache '.

Voor beeld van aangepaste resource provider:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy, Cache",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
  "name": "{resourceProviderName}"
}
```

## <a name="building-proxy-resource-endpoint"></a>Resource-eind punt van de proxy bouwen

Een **eind punt** dat het resource- **eind punt** proxy, cache implementeert, moet de aanvraag en het antwoord voor de nieuwe API in azure afhandelen. In dit geval genereert het **resource type** een nieuwe Azure-resource-API voor `PUT` , `GET` , en `DELETE` voor het uitvoeren van ruw op één resource, en `GET` voor het ophalen van alle bestaande resources:

> [!NOTE]
> De Azure API genereert de aanvraag methoden `PUT` , `GET` en `DELETE` , maar het cache- **eind punt** hoeft alleen te worden verwerkt `PUT` en `DELETE` .
> Het is raadzaam dat het **eind punt** ook implementeert `GET` .

### <a name="create-a-custom-resource"></a>Een aangepaste resource maken

Inkomende Azure API-aanvraag:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Deze aanvraag wordt vervolgens doorgestuurd naar het **eind punt** in de vorm:

``` HTTP
PUT https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Op dezelfde manier wordt de reactie van het **eind punt** doorgestuurd naar de klant. Het antwoord van het eind punt moet het volgende retour neren:

- Een geldig JSON-object document. Alle matrices en teken reeksen moeten worden genest onder een top-object.
- De `Content-Type` header moet worden ingesteld op application/json; charset = UTF-8.
- De aangepaste resource provider overschrijft de `name` `type` velden,, en `id` voor de aanvraag.
- De aangepaste resource provider retourneert alleen velden onder het `properties` object voor een cache-eind punt.

**Eind punt** Beantwoord

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

De `name` `id` velden, en en `type` worden automatisch gegenereerd voor de aangepaste resource door de aangepaste resource provider.

Reactie van de aangepaste Azure-resource provider:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

### <a name="remove-a-custom-resource"></a>Een aangepaste resource verwijderen

Inkomende Azure API-aanvraag:

``` HTTP
Delete https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Deze aanvraag wordt vervolgens doorgestuurd naar het **eind punt** in de vorm:

``` HTTP
Delete https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

Op dezelfde manier wordt de reactie van het **eind punt** doorgestuurd naar de klant. Het antwoord van het eind punt moet het volgende retour neren:

- Een geldig JSON-object document. Alle matrices en teken reeksen moeten worden genest onder een top-object.
- De `Content-Type` header moet worden ingesteld op application/json; charset = UTF-8.
- De resource provider van Azure wordt alleen het item uit de cache verwijderd als een antwoord op 200-niveau wordt geretourneerd. Zelfs als de resource niet bestaat, moet het **eind punt** 204 retour neren.

**Eind punt** Beantwoord

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

Reactie van de aangepaste Azure-resource provider:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

### <a name="retrieve-a-custom-resource"></a>Een aangepaste resource ophalen

Inkomende Azure API-aanvraag:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

De aanvraag wordt **niet** doorgestuurd naar het **eind punt**.

Reactie van de aangepaste Azure-resource provider:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

### <a name="enumerate-all-custom-resources"></a>Alle aangepaste resources opsommen

Inkomende Azure API-aanvraag:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Deze aanvraag wordt **niet** doorgestuurd naar het **eind punt**.

Reactie van de aangepaste Azure-resource provider:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "value" : [
        {
            "name": "{myCustomResourceName}",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3" : "myPropertyValue3"
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van aangepaste Azure-resource providers](overview.md)
- [Snelstartgids: een aangepaste Azure-resource provider maken en aangepaste resources implementeren](./create-custom-provider.md)
- [Zelf studie: aangepaste acties en resources maken in azure](./tutorial-get-started-with-custom-providers.md)
- [Procedure: aangepaste acties toevoegen aan Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Naslag informatie: Naslag informatie over aangepaste resource proxy](proxy-resource-endpoint-reference.md)
