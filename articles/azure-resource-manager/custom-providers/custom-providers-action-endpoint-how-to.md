---
title: Aangepaste acties toevoegen aan Azure REST API
description: Meer informatie over het toevoegen van aangepaste acties aan de Azure REST API. In dit artikel worden de vereisten en aanbevolen procedures besproken voor eind punten die aangepaste acties willen implementeren.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 6110a7952b7c29609d2b98e135b61032aec3fa52
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "75650394"
---
# <a name="adding-custom-actions-to-azure-rest-api"></a>Aangepaste acties toevoegen aan Azure REST API

In dit artikel worden de vereisten en aanbevolen procedures beschreven voor het maken van eind punten van aangepaste Azure-resource providers waarmee aangepaste acties worden geïmplementeerd. Als u niet bekend bent met aangepaste Azure-resource providers, raadpleegt u [het overzicht van aangepaste resource providers](overview.md).

## <a name="how-to-define-an-action-endpoint"></a>Een actie-eind punt definiëren

Een **eind punt** is een URL die verwijst naar een service, waarmee het onderliggende contract tussen IT en Azure wordt geïmplementeerd. Het eind punt wordt gedefinieerd in de aangepaste resource provider en kan een openbaar toegankelijke URL zijn. In het onderstaande voor beeld is de **actie** `myCustomAction` geïmplementeerd door `endpointURL` .

Voor beeld van **resource provider**:

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
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

## <a name="building-an-action-endpoint"></a>Een actie-eind punt bouwen

Een **eind punt** dat een **actie** implementeert, moet de aanvraag en het antwoord voor de nieuwe API in azure afhandelen. Wanneer er een aangepaste resource provider met een **actie** wordt gemaakt, wordt er een nieuwe set Api's in azure gegenereerd. In dit geval genereert de actie een nieuwe Azure Action API voor `POST` aanroepen:

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction
```

Inkomende Azure API-aanvraag:

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Deze aanvraag wordt vervolgens doorgestuurd naar het **eind punt** in de vorm:

``` HTTP
POST https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Op dezelfde manier wordt de reactie van het **eind punt** doorgestuurd naar de klant. Het antwoord van het eind punt moet het volgende retour neren:

- Een geldig JSON-object document. Alle matrices en teken reeksen moeten worden genest onder een top-object.
- De `Content-Type` header moet worden ingesteld op application/json; charset = UTF-8.

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Reactie van de aangepaste Azure-resource provider:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

## <a name="calling-a-custom-action"></a>Een aangepaste actie aanroepen

Er zijn twee hoofd manieren voor het aanroepen van een aangepaste actie voor een aangepaste resource provider:

- Azure CLI
- Azure Resource Manager-sjablonen

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az resource invoke-action --action {actionName} \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName} \
                          --request-body \
                            '{
                                "myProperty1": "myPropertyValue1",
                                "myProperty2": {
                                    "myProperty3": "myPropertyValue3"
                                }
                            }'
```

Parameter | Vereist | Beschrijving
---|---|---
actie | *klikt* | De naam van de actie die is gedefinieerd in de **resource provider**.
ids | *klikt* | De resource-ID van de **resource provider**.
request-body | *geen* | De hoofd tekst van de aanvraag die naar het **eind punt** wordt verzonden.

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sjabloon

> [!NOTE]
> Acties hebben beperkte ondersteuning in Azure Resource Manager sjablonen. De actie kan alleen worden aangeroepen in een sjabloon als deze het [`list`](../templates/template-functions-resource.md#list) voor voegsel in de naam bevat.

Voor beeld van **resource provider** met lijst actie:

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "listMyCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

Voor beeld van Azure Resource Manager sjabloon:

``` JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "resourceIdentifier": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
        "apiVersion": "2018-09-01-preview",
        "functionValues": {
            "myProperty1": "myPropertyValue1",
            "myProperty2": {
                "myProperty3": "myPropertyValue3"
            }
        }
    },
    "resources": [],
    "outputs": {
        "myCustomActionOutput": {
            "type": "object",
            "value": "[listMyCustomAction(variables('resourceIdentifier'), variables('apiVersion'), variables('functionValues'))]"
        }
    }
}
```

Parameter | Vereist | Beschrijving
---|---|---
resourceIdentifier | *klikt* | De resource-ID van de **resource provider**.
apiVersion | *klikt* | De API-versie van de resource-runtime. Dit moet altijd ' 2018-09-01-preview ' zijn.
functionValues | *geen* | De hoofd tekst van de aanvraag die naar het **eind punt** wordt verzonden.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van aangepaste Azure-resource providers](overview.md)
- [Snelstartgids: een aangepaste Azure-resource provider maken en aangepaste resources implementeren](./create-custom-provider.md)
- [Zelf studie: aangepaste acties en resources maken in azure](./tutorial-get-started-with-custom-providers.md)
- [Procedure: aangepaste resources toevoegen aan Azure REST API](./custom-providers-resources-endpoint-how-to.md)
