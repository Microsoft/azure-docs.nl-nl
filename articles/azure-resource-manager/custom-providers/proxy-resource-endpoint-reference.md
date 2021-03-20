---
title: Referentiemateriaal voor aangepaste resource-proxy
description: Aangepaste resource proxy referentie voor aangepaste Azure-resource providers. In dit artikel worden de vereisten beschreven voor eind punten die aangepaste proxy resources implementeren.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 46b38686b39836f3d4bfb80686d514f932a79bf3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "75650459"
---
# <a name="custom-resource-proxy-reference"></a>Naslag informatie voor de aangepaste resource proxy

In dit artikel worden de vereisten beschreven voor eind punten die aangepaste proxy resources implementeren. Als u niet bekend bent met aangepaste Azure-resource providers, raadpleegt u [het overzicht van aangepaste resource providers](overview.md).

## <a name="how-to-define-a-proxy-resource-endpoint"></a>Een proxy-resource-eind punt definiëren

U kunt een proxy bron maken door de **routingType** op ' proxy ' op te geven.

Voor beeld van aangepaste resource provider:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
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

Een **eind punt** dat een ' proxy ' resource- **eind punt** implementeert, moet de aanvraag en het antwoord voor de nieuwe API in azure afhandelen. In dit geval genereert het **resource type** een nieuwe Azure-resource-API voor `PUT` , `GET` , en `DELETE` voor het uitvoeren van ruw op één resource, en `GET` voor het ophalen van alle bestaande resources.

> [!NOTE]
> De `id` `name` velden, en zijn `type` niet vereist, maar zijn nodig voor de integratie van de aangepaste resource met het bestaande Azure-ecosysteem.

Voorbeeld resource:

``` JSON
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

Parameter verwijzing:

Eigenschap | Voorbeeld | Beschrijving
---|---|---
naam | '{myCustomResourceName}' | De naam van de aangepaste resource.
type | Micro soft. CustomProviders/resourceProviders/{resourceTypeName} | De naam ruimte van het resource type.
id | '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{myCustomResourceName}' | De resource-ID.

### <a name="create-a-custom-resource"></a>Een aangepaste resource maken

Inkomende Azure API-aanvraag:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resource-provider-name}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
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

**Eind punt** Beantwoord

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

- Geldig document van het JSON-object. Alle matrices en teken reeksen moeten worden genest onder een top-object.
- De `Content-Type` header moet worden ingesteld op application/json; charset = UTF-8.

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

Deze aanvraag wordt vervolgens doorgestuurd naar het **eind punt** in de vorm:

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

Op dezelfde manier wordt de reactie van het **eind punt** doorgestuurd naar de klant. Het antwoord van het eind punt moet het volgende retour neren:

- Een geldig JSON-object document. Alle matrices en teken reeksen moeten worden genest onder een top-object.
- De `Content-Type` header moet worden ingesteld op application/json; charset = UTF-8.

**Eind punt** Beantwoord

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

Deze aanvraag wordt vervolgens doorgestuurd naar het **eind punt** in de vorm:

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources
```

Op dezelfde manier wordt de reactie van het **eind punt** doorgestuurd naar de klant. Het antwoord van het eind punt moet het volgende retour neren:

- Een geldig JSON-object document. Alle matrices en teken reeksen moeten worden genest onder een top-object.
- De `Content-Type` header moet worden ingesteld op application/json; charset = UTF-8.
- De lijst met resources moet worden geplaatst onder de eigenschap op het hoogste niveau `value` .

**Eind punt** Beantwoord

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
- [Verwijzing: verwijzing naar aangepaste resource-cache](proxy-cache-resource-endpoint-reference.md)
