---
title: Een aangepaste provider maken en gebruiken
description: Deze zelfstudie laat zien hoe u een aangepaste Azure-provider maakt en gebruikt. U gebruikt aangepaste providers om werkstromen in Azure te wijzigen.
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4f425af7681b666b42fbcc70ac0e4c31d9df6d49
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87503749"
---
# <a name="create-and-use-a-custom-provider"></a>Een aangepaste provider maken en gebruiken

Een aangepaste provider is een contract tussen Azure en een eindpunt. Met aangepaste providers kunt u werkstromen in Azure wijzigen. Deze zelfstudie laat het proces voor het maken van een aangepaste provider zien. Als u niet bekend bent met aangepaste Azure-providers, raadpleegt u het [overzicht van aangepaste Azure-resourceproviders](overview.md).

## <a name="create-a-custom-provider"></a>Een aangepaste provider maken

> [!NOTE]
> In deze zelfstudie wordt niet beschreven hoe u een eindpunt kunt maken. Als u geen RESTFUL-eindpunt hebt, volgt u de [zelfstudie over het maken van RESTful-eindpunten](./tutorial-custom-providers-function-authoring.md). Dit is de basis voor de huidige zelfstudie.

Nadat u een eindpunt hebt gemaakt, kunt u een aangepaste provider maken om een contract tussen de provider en het eindpunt te genereren. Met een aangepaste provider kunt u een lijst met eindpuntdefinities opgeven:

```JSON
{
  "name": "myEndpointDefinition",
  "routingType": "Proxy",
  "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
}
```

Eigenschap | Vereist | Beschrijving
---|---|---
**name** | Ja | De naam van de eindpuntdefinitie. Deze naam wordt door Azure weergegeven via de API onder /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders<br>/resourceProviders/{resourceProviderName}/{endpointDefinitionName}
**routingType** | Nee | Het type contract voor het eindpunt. Als de waarde niet is opgegeven, wordt standaard 'Proxy' gebruikt.
**endpoint** | Ja | Het eindpunt waarnaar de aanvragen worden gerouteerd. Dit eindpunt verwerkt het antwoord en eventuele neveneffecten van de aanvraag.

De waarde van **endpoint** is de trigger-URL van de Azure-functie-app. De tijdelijke aanduidingen `<yourapp>`, `<funcname>` en `<functionkey>` moeten worden vervangen door waarden voor uw gemaakte functie-app.

## <a name="define-custom-actions-and-resources"></a>Aangepaste acties en resources definiëren

De aangepaste provider bevat een lijst met eindpuntdefinities die zijn gemodelleerd onder de eigenschappen **actions** en **resourceTypes**. De eigenschap **actions** wordt toegewezen aan de aangepaste acties die worden weergegeven door de aangepaste provider, en de eigenschap **resourceTypes** is de aangepaste resources. In deze zelfstudie heeft de aangepaste provider een eigenschap **actions** met de naam `myCustomAction` en een eigenschap **resourceTypes** met de naam `myCustomResources`.

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
      }
    ],
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
      }
    ]
  },
  "location": "eastus"
}
```

## <a name="deploy-the-custom-provider"></a>De aangepaste provider implementeren

> [!NOTE]
> U moet de **endpoint**-waarden vervangen door de trigger-URL uit de functie-app die u in de vorige zelfstudie hebt gemaakt.

U kunt de vorige aangepaste provider implementeren met behulp van een Azure Resource Manager sjabloon:

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders",
            "name": "myCustomProvider",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "actions": [
                    {
                        "name": "myCustomAction",
                        "routingType": "Proxy",
                        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
                    }
                ],
                "resourceTypes": [
                    {
                        "name": "myCustomResources",
                        "routingType": "Proxy",
                        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
                    }
                ]
            }
        }
    ]
}
```

## <a name="use-custom-actions-and-resources"></a>Aangepaste acties en resources gebruiken

Nadat u een aangepaste provider hebt gemaakt, kunt u de nieuwe Azure-API's gebruiken. In de volgende tabbladen wordt uitgelegd hoe u een aangepaste provider aanroept en gebruikt.

### <a name="custom-actions"></a>Aangepaste acties

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

> [!NOTE]
> U moet de tijdelijke aanduidingen `{subscriptionId}` en `{resourceGroupName}` vervangen door het abonnement en de resourcegroep waarvoor u de aangepaste provider hebt geïmplementeerd.

```azurecli-interactive
az resource invoke-action --action myCustomAction \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider \
                          --request-body
                            '{
                                "hello": "world"
                            }'
```

Parameter | Vereist | Beschrijving
---|---|---
*action* | Ja | De naam van de actie die is gedefinieerd in de aangepaste provider
*ids* | Ja | De resource-id van de aangepaste provider
*request-body* | Nee | De aanvraagbody die naar het eindpunt wordt verzonden

# <a name="template"></a>[Sjabloon](#tab/template)

Geen.

---

### <a name="custom-resources"></a>Aangepaste resources

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

> [!NOTE]
> U moet de tijdelijke aanduidingen `{subscriptionId}` en `{resourceGroupName}` vervangen door het abonnement en de resourcegroep waarvoor u de aangepaste provider hebt geïmplementeerd.

#### <a name="create-a-custom-resource"></a>Een aangepaste resource maken

```azurecli-interactive
az resource create --is-full-object \
                   --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1 \
                   --properties
                    '{
                        "location": "eastus",
                        "properties": {
                            "hello" : "world"
                        }
                    }'
```

Parameter | Vereist | Beschrijving
---|---|---
*is-full-object* | Ja | Hiermee wordt aangegeven of het properties-object andere opties bevat, zoals locatie, tags, SKU of plan.
*id* | Ja | De resource-id van de aangepaste resource. Deze id is een uitbreiding van de resource-id van de aangepaste provider.
*properties* | Ja | De aanvraagbody die naar het eindpunt wordt verzonden.

#### <a name="delete-a-custom-resource"></a>Een aangepaste resource verwijderen

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Parameter | Vereist | Beschrijving
---|---|---
*id* | Ja | De resource-id van de aangepaste resource. Deze id is een uitbreiding van de resource-id van de aangepaste provider.

#### <a name="retrieve-a-custom-resource"></a>Een aangepaste resource ophalen

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Parameter | Vereist | Beschrijving
---|---|---
*id* | Ja | De resource-id van de aangepaste resource. Deze id is een uitbreiding van de resource-id van de aangepaste provider.

# <a name="template"></a>[Sjabloon](#tab/template)

Een voorbeeld van een Resource Manager-sjabloon:

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "name": "myCustomProvider/myTestResourceName1",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "hello": "world"
            }
        }
    ]
}
```

Parameter | Vereist | Beschrijving
---|---|---
*resourceTypeName* | Ja | De `name`-waarde van de eigenschap **resourceTypes** die is gedefinieerd in de aangepaste provider.
*resourceProviderName* | Ja | De naam van het exemplaar van de aangepaste provider.
*customResourceName* | Ja | De naam van de aangepaste resource.

---

> [!NOTE]
> Vergeet niet om alle gemaakte resources, inclusief de Azure-functie-app, op te schonen wanneer u klaar bent met het implementeren en gebruiken van de aangepaste provider.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over aangepaste providers. Zie voor meer informatie:

- [Procedure: Aangepaste acties toevoegen aan Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Procedure: Aangepaste resources toevoegen aan Azure REST API](./custom-providers-resources-endpoint-how-to.md)
