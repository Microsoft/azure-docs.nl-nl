---
title: Web-app inrichten met Azure Cache voor Redis
description: Gebruik Azure Resource Manager om een web-app te implementeren met Azure Cache voor Redis.
services: app-service
author: yegu-ms
ms.service: app-service
ms.topic: conceptual
ms.date: 01/06/2017
ms.author: yegu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 089d7d7990f0f94135f629a5cd7a461700aa3433
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830768"
---
# <a name="create-a-web-app-plus-azure-cache-for-redis-using-a-template"></a>Een web-app maken plus Azure Cache voor Redis met behulp van een sjabloon

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In dit onderwerp leert u hoe u een Azure Resource Manager maakt waarmee een Azure-web-app wordt geïmplementeerd met Azure Cache voor Redis. U leert hoe u definieert welke resources worden geïmplementeerd en hoe u parameters definieert die worden opgegeven wanneer de implementatie wordt uitgevoerd. U kunt deze sjabloon gebruiken voor uw eigen implementaties of de sjabloon aanpassen aan uw eisen.

Zie [Authoring Azure Resource Manager Templates (Sjablonen maken) voor Azure Resource Manager informatie over het maken van sjablonen.](../azure-resource-manager/templates/template-syntax.md) Zie [Microsoft.Cache resource types (Microsoft.Cache-resourcetypen)](/azure/templates/microsoft.cache/allversions)voor meer informatie over de JSON-syntaxis en eigenschappen voor cacheresourcetypen.

Zie Web App with Azure Cache voor Redis [template (Web-app met Azure Cache voor Redis sjabloon) voor de volledige sjabloon.](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-with-redis-cache/azuredeploy.json)

## <a name="what-you-will-deploy"></a>Wat u gaat implementeren
In deze sjabloon implementeert u:

* Azure Web App
* Azure Cache voor Redis

Klik op de volgende knop om de implementatie automatisch uit te voeren:

[![Implementeren in Azure](./media/cache-web-app-arm-with-redis-cache-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-with-redis-cache%2Fazuredeploy.json)

## <a name="parameters-to-specify"></a>Parameters om op te geven
[!INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]

[!INCLUDE [cache-deploy-parameters](../../includes/cache-deploy-parameters.md)]

## <a name="variables-for-names"></a>Variabelen voor namen
Deze sjabloon maakt gebruik van variabelen om namen voor de resources te maken. De functie [uniqueString wordt](../azure-resource-manager/templates/template-functions-string.md#uniquestring) gebruikt om een waarde te maken op basis van de resourcegroep-id.

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
Hiermee maakt u Azure Cache voor Redis die wordt gebruikt met de web-app. De naam van de cache wordt opgegeven in de **variabele cacheName.**

De sjabloon maakt de cache op dezelfde locatie als de resourcegroep.

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
Hiermee maakt u de web-app met de naam die is opgegeven in de **variabele webSiteName.**

U ziet dat de web-app is geconfigureerd met app-instellingseigenschappen waarmee deze kan werken met de Azure Cache voor Redis. Deze app-instellingen worden dynamisch gemaakt op basis van waarden die zijn opgegeven tijdens de implementatie.

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
