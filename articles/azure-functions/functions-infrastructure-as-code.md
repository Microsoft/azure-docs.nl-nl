---
title: Functie implementatie van app-resources automatiseren naar Azure
description: Meer informatie over het bouwen van een Azure Resource Manager sjabloon waarmee uw functie-app wordt geïmplementeerd.
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.topic: conceptual
ms.date: 04/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 708478f50078276245a7dfab7d185dd50d597407
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97589794"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>De implementatie van resources voor uw functie-app in Azure Functions automatiseren

U kunt een Azure Resource Manager sjabloon gebruiken om een functie-app te implementeren. In dit artikel vindt u een overzicht van de vereiste resources en para meters. Mogelijk moet u aanvullende bronnen implementeren, afhankelijk van de [Triggers en bindingen](functions-triggers-bindings.md) in uw functie-app.

Zie [Azure Resource Manager-sjablonen samenstellen](../azure-resource-manager/templates/template-syntax.md) voor meer informatie over het maken van sjablonen.

Zie voor voorbeeld sjablonen:
- [Functie-app voor verbruiks abonnement]
- [Functie-app voor Azure App Service plan]

## <a name="required-resources"></a>Vereiste bronnen

Een Azure Functions-implementatie bestaat meestal uit de volgende bronnen:

| Resource                                                                           | Vereiste | Naslag informatie en eigenschappen                                                         |
|------------------------------------------------------------------------------------|-------------|-----------------------------------------------------------------------------------------|
| Een functie-app                                                                     | Vereist    | [Micro soft. web/sites](/azure/templates/microsoft.web/sites)                             |
| Een [Azure Storage](../storage/index.yml) -account                                   | Vereist    | [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| Een [Application Insights](../azure-monitor/app/app-insights-overview.md) onderdeel | Optioneel    | [Micro soft. Insights/onderdelen](/azure/templates/microsoft.insights/components)         |
| Een [hosting abonnement](./functions-scale.md)                                             | Optioneel<sup>1</sup>    | [Micro soft. web/server farms](/azure/templates/microsoft.web/serverfarms)                 |

<sup>1</sup> Een hosting plan is alleen vereist wanneer u uw functie-app wilt uitvoeren in een [Premium-abonnement](./functions-premium-plan.md) of op een [app service plan](../app-service/overview-hosting-plans.md).

> [!TIP]
> Hoewel dit niet vereist is, wordt het ten zeerste aanbevolen om Application Insights voor uw app te configureren.

<a name="storage"></a>
### <a name="storage-account"></a>Storage-account

Een Azure-opslag account is vereist voor een functie-app. U hebt een algemeen doel account nodig dat blobs, tabellen, wacht rijen en bestanden ondersteunt. Zie [Azure functions vereisten voor opslag accounts](storage-considerations.md#storage-account-requirements)voor meer informatie.

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2019-06-01",
    "location": "[resourceGroup().location]",
    "kind": "StorageV2",
    "sku": {
        "name": "[parameters('storageAccountType')]"
    }
}
```

Daarnaast moet de eigenschap `AzureWebJobsStorage` worden opgegeven als een app-instelling in de site configuratie. Als de functie-app geen gebruik maakt van Application Insights voor bewaking, moet deze ook worden opgegeven `AzureWebJobsDashboard` als een app-instelling.

De Azure Functions runtime gebruikt de `AzureWebJobsStorage` Connection String om interne wacht rijen te maken.  Als Application Insights niet is ingeschakeld, gebruikt de runtime de `AzureWebJobsDashboard` Connection String om u aan te melden bij Azure Table Storage en het tabblad **monitor** in de portal uit te scha kelen.

Deze eigenschappen zijn opgegeven in de `appSettings` verzameling in het `siteConfig` object:

```json
"appSettings": [
    {
        "name": "AzureWebJobsStorage",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
    },
    {
        "name": "AzureWebJobsDashboard",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
    }
]
```

### <a name="application-insights"></a>Application Insights

Application Insights wordt aanbevolen voor het bewaken van uw functie-apps. De Application Insights resource is gedefinieerd met het type **micro soft. Insights/onderdelen** en het soort **Web**:

```json
        {
            "apiVersion": "2015-05-01",
            "name": "[variables('appInsightsName')]",
            "type": "Microsoft.Insights/components",
            "kind": "web",
            "location": "[resourceGroup().location]",
            "tags": {
                "[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/sites/', variables('functionAppName'))]": "Resource"
            },
            "properties": {
                "Application_Type": "web",
                "ApplicationId": "[variables('appInsightsName')]"
            }
        },
```

Daarnaast moet de instrumentatie sleutel aan de functie-app worden door gegeven met de `APPINSIGHTS_INSTRUMENTATIONKEY` toepassings instelling. Deze eigenschap is opgegeven in de `appSettings` verzameling in het `siteConfig` object:

```json
"appSettings": [
    {
        "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
    }
]
```

### <a name="hosting-plan"></a>Hosting plan

De definitie van het hosting plan varieert, en kan een van de volgende zijn:
* [Verbruiks abonnement](#consumption) (standaard)
* [Premium-abonnement](#premium)
* [App Service-plan](#app-service-plan)

### <a name="function-app"></a>Functie-app

De functie-app resource wordt gedefinieerd met behulp van een resource van het type **micro soft. web/sites** en de soort **functionapp**:

```json
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
    ]
}
```

> [!IMPORTANT]
> Als u een hosting plan expliciet definieert, zou u een extra item nodig hebben in de dependsOn-matrix: `"[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"`

Een functie-app moet deze toepassings instellingen bevatten:

| Naam van de instelling                 | Beschrijving                                                                               | Voorbeeldwaarden                        |
|------------------------------|-------------------------------------------------------------------------------------------|---------------------------------------|
| AzureWebJobsStorage          | Een connection string naar een opslag account dat door de functions-runtime wordt gebruikt voor interne wachtrij gebruik | Zie [Storage-account](#storage)       |
| FUNCTIONS_EXTENSION_VERSION  | De versie van de Azure Functions runtime                                                | `~2`                                  |
| FUNCTIONS_WORKER_RUNTIME     | De taal stack die moet worden gebruikt voor functies in deze app                                   | `dotnet`,,, `node` `java` `python` of `powershell` |
| WEBSITE_NODE_DEFAULT_VERSION | Dit is alleen nodig als `node` u de taal stack gebruikt. Hiermee geeft u de versie op die moet worden gebruikt              | `10.14.1`                             |

Deze eigenschappen zijn opgegeven in de `appSettings` verzameling in de `siteConfig` eigenschap:

```json
"properties": {
    "siteConfig": {
        "appSettings": [
            {
                "name": "AzureWebJobsStorage",
                "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
            },
            {
                "name": "FUNCTIONS_WORKER_RUNTIME",
                "value": "node"
            },
            {
                "name": "WEBSITE_NODE_DEFAULT_VERSION",
                "value": "10.14.1"
            },
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~2"
            }
        ]
    }
}
```

<a name="consumption"></a>

## <a name="deploy-on-consumption-plan"></a>Implementeren in verbruiks abonnement

Het verbruiks plan wijst automatisch reken kracht toe wanneer uw code wordt uitgevoerd, wordt zo nodig geschaald om de belasting te verwerken en vervolgens wordt geschaald wanneer code niet wordt uitgevoerd. U hoeft niet te betalen voor niet-actieve Vm's en u hoeft de capaciteit niet vooraf te reserveren. Zie [Azure functions schalen en hosten](functions-scale.md#consumption-plan)voor meer informatie.

Zie [functie-app voor verbruiks abonnement]voor een voor beeld-Azure Resource Manager sjabloon.

### <a name="create-a-consumption-plan"></a>Een verbruiks plan maken

Een verbruiks plan hoeft niet te worden gedefinieerd. Wanneer u de resource van de functie-app zelf maakt, wordt er automatisch een per regio gemaakt of geselecteerd.

Het verbruiks plan is een speciaal type resource ' server farm '. Voor Windows kunt u deze opgeven met behulp van de `Dynamic` waarde voor `computeMode` de `sku` Eigenschappen en:

```json
{  
   "type":"Microsoft.Web/serverfarms",
   "apiVersion":"2016-09-01",
   "name":"[variables('hostingPlanName')]",
   "location":"[resourceGroup().location]",
   "properties":{  
      "name":"[variables('hostingPlanName')]",
      "computeMode":"Dynamic"
   },
   "sku":{  
      "name":"Y1",
      "tier":"Dynamic",
      "size":"Y1",
      "family":"Y",
      "capacity":0
   }
}
```

> [!NOTE]
> Het verbruiks plan kan niet expliciet worden gedefinieerd voor Linux. Het wordt automatisch gemaakt.

Als u het verbruiks abonnement expliciet definieert, moet u de `serverFarmId` eigenschap op de app instellen zodat deze verwijst naar de resource-id van het plan. Zorg ervoor dat de functie-app ook een `dependsOn` instelling voor het plan heeft.

### <a name="create-a-function-app"></a>Een functie-app maken

#### <a name="windows"></a>Windows

In Windows zijn voor een verbruiks abonnement twee extra instellingen vereist in de site configuratie: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` en `WEBSITE_CONTENTSHARE` . Met deze eigenschappen configureert u het opslag account en het bestandspad waar de code en configuratie van de functie-app worden opgeslagen.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "WEBSITE_CONTENTSHARE",
                    "value": "[toLower(variables('functionAppName'))]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```

#### <a name="linux"></a>Linux

Op Linux moet de functie-app zijn `kind` ingesteld op `functionapp,linux` en moet de eigenschap zijn `reserved` ingesteld op `true` :

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp,linux",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountName'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        },
        "reserved": true
    }
}
```

<a name="premium"></a>

## <a name="deploy-on-premium-plan"></a>Implementeren in Premium-abonnement

Het Premium-abonnement biedt dezelfde schaal als het verbruiks abonnement, maar bevat speciale resources en aanvullende mogelijkheden. Zie [Azure functions Premium-abonnement](./functions-premium-plan.md)voor meer informatie.

### <a name="create-a-premium-plan"></a>Een Premium-abonnement maken

Een Premium-abonnement is een speciaal type resource ' server farm '. U kunt dit opgeven met behulp van ofwel `EP1` `EP2` of `EP3` voor de `Name` waarde van de eigenschap in het `sku` [object beschrijving](/azure/templates/microsoft.web/2018-02-01/serverfarms#skudescription-object).

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[parameters('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[parameters('hostingPlanName')]",
        "workerSize": "[parameters('workerSize')]",
        "workerSizeId": "[parameters('workerSizeId')]",
        "numberOfWorkers": "[parameters('numberOfWorkers')]",
        "hostingEnvironment": "[parameters('hostingEnvironment')]",
        "maximumElasticWorkerCount": "20"
    },
    "sku": {
        "Tier": "ElasticPremium",
        "Name": "EP1"
    }
}
```

### <a name="create-a-function-app"></a>Een functie-app maken

Voor een functie-app voor een Premium-abonnement moet de `serverFarmId` eigenschap zijn ingesteld op de resource-id van het abonnement dat u eerder hebt gemaakt. Daarnaast is voor een Premium-abonnement twee extra instellingen vereist in de site configuratie: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` en `WEBSITE_CONTENTSHARE` . Met deze eigenschappen configureert u het opslag account en het bestandspad waar de code en configuratie van de functie-app worden opgeslagen.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",            
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "WEBSITE_CONTENTSHARE",
                    "value": "[toLower(variables('functionAppName'))]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```

<a name="app-service-plan"></a>

## <a name="deploy-on-app-service-plan"></a>Implementeren op App Service plan

In het App Service plan wordt uw functie-app uitgevoerd op specifieke Vm's op basis-, standaard-en Premium-Sku's, vergelijkbaar met web apps. Zie het [overzicht van Azure app service plannen](../app-service/overview-hosting-plans.md)voor meer informatie over de werking van het app service plan.

Zie voor een voorbeeld Azure Resource Manager sjabloon [functie-app op Azure app service plan].

### <a name="create-an-app-service-plan"></a>Een App Service-plan maken

Een App Service-abonnement wordt gedefinieerd door een resource ' server farm '.

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

Als u uw app op Linux wilt uitvoeren, moet u ook `kind` het `Linux` volgende instellen:

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "kind": "Linux",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

### <a name="create-a-function-app"></a>Een functie-app maken

Voor een functie-app voor een App Service plan moet de `serverFarmId` eigenschap zijn ingesteld op de resource-id van het abonnement dat u eerder hebt gemaakt.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```

Linux-apps moeten ook een `linuxFxVersion` eigenschap bevatten onder `siteConfig` . Als u alleen code implementeert, wordt de waarde voor dit bepaald door de gewenste runtime stack:

| Stack            | Voorbeeldwaarde                                         |
|------------------|-------------------------------------------------------|
| Python           | `DOCKER|microsoft/azure-functions-python3.6:2.0`      |
| Javascript       | `DOCKER|microsoft/azure-functions-node8:2.0`          |
| .NET             | `DOCKER|microsoft/azure-functions-dotnet-core2.0:2.0` |

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ],
            "linuxFxVersion": "DOCKER|microsoft/azure-functions-node8:2.0"
        }
    }
}
```

Als u [een aangepaste container installatie kopie implementeert](./functions-create-function-linux-custom-image.md), moet u deze opgeven met `linuxFxVersion` en configuratie toevoegen waarmee de installatie kopie kan worden opgehaald, zoals in [Web App for containers](../app-service/index.yml). Stel ook `WEBSITES_ENABLE_APP_SERVICE_STORAGE` in op `false` , omdat de inhoud van uw app wordt opgegeven in de container zelf:

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_URL",
                    "value": "[parameters('dockerRegistryUrl')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
                    "value": "[parameters('dockerRegistryUsername')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
                    "value": "[parameters('dockerRegistryPassword')]"
                },
                {
                    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
                    "value": "false"
                }
            ],
            "linuxFxVersion": "DOCKER|myacr.azurecr.io/myimage:mytag"
        }
    }
}
```

## <a name="customizing-a-deployment"></a>Een implementatie aanpassen

Een functie-app heeft veel onderliggende resources die u kunt gebruiken in uw implementatie, waaronder app-instellingen en opties voor bron beheer. U kunt er ook voor kiezen om de **sourcecontrols** onderliggende resource te verwijderen en in plaats daarvan een andere [implementatie optie](functions-continuous-deployment.md) te gebruiken.

> [!IMPORTANT]
> Als u uw toepassing wilt implementeren met behulp van Azure Resource Manager, is het belang rijk om te begrijpen hoe resources in Azure worden geïmplementeerd. In het volgende voor beeld worden configuraties op het hoogste niveau toegepast met behulp van **siteConfig**. Het is belang rijk deze configuraties op het hoogste niveau in te stellen, omdat ze informatie overbrengen naar de runtime-en implementatie-engine van functions. Informatie op het hoogste niveau is vereist voordat de onderliggende **sourcecontrols/** WebResource wordt toegepast. Hoewel het mogelijk is om deze instellingen te configureren in de resource van het onderliggende **configuratie/appSettings** , in sommige gevallen moet uw functie-app worden geïmplementeerd *voordat* **config/appSettings** wordt toegepast. Als u bijvoorbeeld functies gebruikt met [Logic apps](../logic-apps/index.yml), zijn uw functies een afhankelijkheid van een andere resource.

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('appName')]",
  "type": "Microsoft.Web/sites",
  "kind": "functionapp",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Web/serverfarms', parameters('appName'))]"
  ],
  "properties": {
     "serverFarmId": "[variables('appServicePlanName')]",
     "siteConfig": {
        "alwaysOn": true,
        "appSettings": [
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~2"
            },
            {
                "name": "Project",
                "value": "src"
            }
        ]
     }
  },
  "resources": [
     {
        "apiVersion": "2015-08-01",
        "name": "appsettings",
        "type": "config",
        "dependsOn": [
          "[resourceId('Microsoft.Web/Sites', parameters('appName'))]",
          "[resourceId('Microsoft.Web/Sites/sourcecontrols', parameters('appName'), 'web')]",
          "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
        ],
        "properties": {
          "AzureWebJobsStorage": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]",
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]",
          "FUNCTIONS_EXTENSION_VERSION": "~2",
          "FUNCTIONS_WORKER_RUNTIME": "dotnet",
          "Project": "src"
        }
     },
     {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites/', parameters('appName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('sourceCodeRepositoryURL')]",
            "branch": "[parameters('sourceCodeBranch')]",
            "IsManualIntegration": "[parameters('sourceCodeManualIntegration')]"
          }
     }
  ]
}
```
> [!TIP]
> Deze sjabloon maakt gebruik van de waarde van de [project](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) app-instellingen, waarmee de basis directory wordt ingesteld waarin de functions Deployment Engine (kudu) zoekt naar de code die kan worden geïmplementeerd. In onze opslag plaats bevinden onze functies zich in een submap van de map **src** . In het vorige voor beeld stellen we de waarde van de app-instellingen in op `src` . Als uw functies zich in de hoofdmap van uw opslag plaats bevinden of als u niet vanuit broncode beheer implementeert, kunt u deze waarde voor app-instellingen verwijderen.

## <a name="deploy-your-template"></a>De sjabloon implementeren

U kunt een van de volgende manieren gebruiken om uw sjabloon te implementeren:

* [PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
* [Azure-CLI](../azure-resource-manager/templates/deploy-cli.md)
* [Azure-portal](../azure-resource-manager/templates/deploy-portal.md)
* [REST API](../azure-resource-manager/templates/deploy-rest.md)

### <a name="deploy-to-azure-button"></a>De knop Implementeren in Azure

Vervang door ```<url-encoded-path-to-azuredeploy-json>``` een [URL-gecodeerde](https://www.bing.com/search?q=url+encode) versie van het onbewerkte pad van het `azuredeploy.json` bestand in github.

Hier volgt een voor beeld waarin prijs verlaging wordt gebruikt:

```markdown
[![Deploy to Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

Hier volgt een voor beeld waarin gebruik wordt gemaakt van HTML:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"></a>
```

### <a name="deploy-using-powershell"></a>Implementeren met PowerShell

Met de volgende Power shell-opdrachten maakt u een resource groep en implementeert u een sjabloon waarmee een functie-app met de vereiste resources wordt gemaakt. Als u lokaal wilt uitvoeren, moet [Azure PowerShell](/powershell/azure/install-az-ps) zijn geïnstalleerd. Voer uit [`Connect-AzAccount`](/powershell/module/az.accounts/connect-azaccount) om aan te melden.

```powershell
# Register Resource Providers if they're not already registered
Register-AzResourceProvider -ProviderNamespace "microsoft.web"
Register-AzResourceProvider -ProviderNamespace "microsoft.storage"

# Create a resource group for the function app
New-AzResourceGroup -Name "MyResourceGroup" -Location 'West Europe'

# Create the parameters for the file, which for this template is the function app name.
$TemplateParams = @{"appName" = "<function-app-name>"}

# Deploy the template
New-AzResourceGroupDeployment -ResourceGroupName "MyResourceGroup" -TemplateFile template.json -TemplateParameterObject $TemplateParams -Verbose
```

Als u deze implementatie wilt testen, kunt u een [sjabloon als deze](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json) gebruiken die een functie-app in Windows maakt in een verbruiks abonnement. Vervang door `<function-app-name>` een unieke naam voor uw functie-app.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het ontwikkelen en configureren van Azure Functions.

* [Naslaginformatie over Azure Functions voor ontwikkelaars](functions-reference.md)
* [Instellingen voor Azure function-apps configureren](functions-how-to-use-azure-function-app-settings.md)
* [Uw eerste Azure-functie maken](functions-create-first-azure-function.md)

<!-- LINKS -->

[Functie-app voor verbruiks abonnement]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[Functie-app voor Azure App Service plan]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json