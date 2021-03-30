---
title: Een web-app inrichten met Azure cache voor redis
description: Gebruik Azure Resource Manager sjabloon voor het implementeren van een web-app met Azure cache voor redis.
services: app-service
author: yegu-ms
ms.service: app-service
ms.topic: conceptual
ms.date: 01/06/2017
ms.author: yegu
ms.openlocfilehash: ec8d4f5611425734974d07ae6ee7008b10b9b406
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85833771"
---
# <a name="create-a-web-app-plus-azure-cache-for-redis-using-a-template"></a>Een web-app plus Azure cache maken voor redis met behulp van een sjabloon

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In dit onderwerp leert u hoe u een Azure Resource Manager sjabloon maakt waarmee een Azure-web-app met Azure cache voor redis wordt geïmplementeerd. U leert hoe u kunt definiëren welke resources worden geïmplementeerd en hoe u para meters definieert die worden opgegeven wanneer de implementatie wordt uitgevoerd. U kunt deze sjabloon gebruiken voor uw eigen implementaties of de sjabloon aanpassen aan uw eisen.

Zie [Azure Resource Manager sjablonen ontwerpen](../azure-resource-manager/templates/template-syntax.md)voor meer informatie over het maken van sjablonen. Zie [resource typen van micro soft. cache](/azure/templates/microsoft.cache/allversions)voor meer informatie over de JSON-syntaxis en eigenschappen voor cache resource typen.

Zie voor de volledige sjabloon [Web-app met Azure cache voor redis-sjabloon](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-with-redis-cache/azuredeploy.json).

## <a name="what-you-will-deploy"></a>Wat u gaat implementeren
In deze sjabloon voert u de volgende implementatie uit:

* Azure Web App
* Azure Cache voor Redis

Klik op de volgende knop om de implementatie automatisch uit te voeren:

[![Implementeren in Azure](./media/cache-web-app-arm-with-redis-cache-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-with-redis-cache%2Fazuredeploy.json)

## <a name="parameters-to-specify"></a>Para meters om op te geven
[!INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]

[!INCLUDE [cache-deploy-parameters](../../includes/cache-deploy-parameters.md)]

## <a name="variables-for-names"></a>Variabelen voor namen
Deze sjabloon maakt gebruik van variabelen om namen voor de resources te maken. De functie [Unique string](../azure-resource-manager/templates/template-functions-string.md#uniquestring) wordt gebruikt om een waarde te maken op basis van de resource groep-ID.

```json
"variables": {
  "hostingPlanName": "[concat('hostingplan', uniqueString(resourceGroup().id))]",
  "webSiteName": "[concat('webSite', uniqueString(resourceGroup().id))]",
  "cacheName": "[concat('cache', uniqueString(resourceGroup().id))]"
},
```


## <a name="resources-to-deploy"></a>Resources om te implementeren
[!INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]

### <a name="azure-cache-for-redis"></a>Azure Cache voor Redis
Hiermee maakt u de Azure-cache voor redis die wordt gebruikt met de web-app. De naam van de cache is opgegeven in de variabele **cachemap** .

De sjabloon maakt de cache op dezelfde locatie als de resource groep.

```json
{
  "name": "[variables('cacheName')]",
  "type": "Microsoft.Cache/Redis",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-08-01",
  "dependsOn": [ ],
  "tags": {
    "displayName": "cache"
  },
  "properties": {
    "sku": {
      "name": "[parameters('cacheSKUName')]",
      "family": "[parameters('cacheSKUFamily')]",
      "capacity": "[parameters('cacheSKUCapacity')]"
    }
  }
}
```


### <a name="web-app"></a>Web-app
Hiermee maakt u de web-app met de naam die is opgegeven in de variabele voor de **Websitenaam** .

U ziet dat de web-app is geconfigureerd met app-instellings eigenschappen, zodat deze kan worden gebruikt met de Azure-cache voor redis. Deze app-instellingen worden dynamisch gemaakt op basis van de waarden die tijdens de implementatie worden gegeven.

```json
{
  "apiVersion": "2015-08-01",
  "name": "[variables('webSiteName')]",
  "type": "Microsoft.Web/sites",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Web/serverFarms/', variables('hostingPlanName'))]",
    "[concat('Microsoft.Cache/Redis/', variables('cacheName'))]"
  ],
  "tags": {
    "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', variables('hostingPlanName'))]": "empty",
    "displayName": "Website"
  },
  "properties": {
    "name": "[variables('webSiteName')]",
    "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
  },
  "resources": [
    {
      "apiVersion": "2015-08-01",
      "type": "config",
      "name": "appsettings",
      "dependsOn": [
        "[concat('Microsoft.Web/Sites/', variables('webSiteName'))]",
        "[concat('Microsoft.Cache/Redis/', variables('cacheName'))]"
      ],
      "properties": {
       "CacheConnection": "[concat(variables('cacheName'),'.redis.cache.windows.net,abortConnect=false,ssl=true,password=', listKeys(resourceId('Microsoft.Cache/Redis', variables('cacheName')), '2015-08-01').primaryKey)]"
      }
    }
  ]
}
```

## <a name="commands-to-run-deployment"></a>Opdrachten om implementatie uit te voeren
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```azurepowershell
New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-with-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-with-redis-cache/azuredeploy.json -g ExampleDeployGroup
```
