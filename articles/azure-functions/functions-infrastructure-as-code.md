---
title: Implementatie van resource voor functie-apps automatiseren in Azure
description: Meer informatie over het bouwen van Azure Resource Manager sjabloon waarmee uw functie-app wordt geïmplementeerd.
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.topic: conceptual
ms.date: 04/03/2019
ms.custom: fasttrack-edit, devx-track-azurepowershell
ms.openlocfilehash: 4bbd3491c45b15d43ae0e94b37b916cd8091e655
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834206"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Resource-implementatie automatiseren voor uw functie-app in Azure Functions

U kunt een Azure Resource Manager gebruiken om een functie-app te implementeren. Dit artikel bevat een overzicht van de vereiste resources en parameters om dit te doen. Mogelijk moet u aanvullende resources implementeren, afhankelijk van de [triggers en bindingen](functions-triggers-bindings.md) in uw functie-app.

Zie [Azure Resource Manager-sjablonen samenstellen](../azure-resource-manager/templates/template-syntax.md) voor meer informatie over het maken van sjablonen.

Zie voor voorbeeldsjablonen:
- [Functie-app in verbruiksplan]
- [Functie-app in Azure App Service abonnement]

## <a name="required-resources"></a>Vereiste resources

Een Azure Functions-implementatie bestaat doorgaans uit deze resources:

| Resource                                                                           | Vereiste | Naslag voor syntaxis en eigenschappen                                                         |
|------------------------------------------------------------------------------------|-------------|-----------------------------------------------------------------------------------------|
| Een functie-app                                                                     | Vereist    | [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)                             |
| Een [Azure Storage account](../storage/index.yml)                                   | Vereist    | [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| Een [Application Insights-onderdeel](../azure-monitor/app/app-insights-overview.md) | Optioneel    | [Microsoft.Insights/components](/azure/templates/microsoft.insights/components)         |
| Een [hostingplan](./functions-scale.md)                                             | Optioneel<sup>1</sup>    | [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)                 |

<sup>1</sup> Een hostingplan is alleen vereist wanneer u ervoor kiest om uw functie-app uit te voeren in een [Premium-abonnement](./functions-premium-plan.md) of op basis [van App Service abonnement](../app-service/overview-hosting-plans.md).

> [!TIP]
> Hoewel dit niet vereist is, wordt u ten zeerste aangeraden om een Application Insights voor uw app te configureren.

<a name="storage"></a>
### <a name="storage-account"></a>Storage-account

Een Azure-opslagaccount is vereist voor een functie-app. U hebt een algemeen account nodig dat ondersteuning biedt voor blobs, tabellen, wachtrijen en bestanden. Zie vereisten voor [opslagaccounts Azure Functions meer informatie.](storage-considerations.md#storage-account-requirements)

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

Bovendien moet de eigenschap `AzureWebJobsStorage` worden opgegeven als een app-instelling in de siteconfiguratie. Als de functie-app geen gebruik Application Insights voor bewaking, moet deze ook worden opgegeven `AzureWebJobsDashboard` als een app-instelling.

De Azure Functions runtime gebruikt de connection string `AzureWebJobsStorage` om interne wachtrijen te maken.  Wanneer Application Insights niet is ingeschakeld, gebruikt de runtime de connection string om u aan te melden bij Azure Table Storage en het tabblad Monitor in de `AzureWebJobsDashboard` portal aan te zetten. 

Deze eigenschappen worden opgegeven in de `appSettings` verzameling in het `siteConfig` -object:

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

Application Insights wordt aanbevolen voor het bewaken van uw functie-apps. De Application Insights resource wordt gedefinieerd met het type **Microsoft.Insights/components** en het type **web**:

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

Daarnaast moet de instrumentatiesleutel worden opgegeven voor de functie-app met behulp van de `APPINSIGHTS_INSTRUMENTATIONKEY` toepassingsinstelling. Deze eigenschap is opgegeven in de `appSettings` verzameling in het `siteConfig` -object:

```json
"appSettings": [
    {
        "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
    }
]
```

### <a name="hosting-plan"></a>Hostingplan

De definitie van het hostingplan varieert en kan een van de volgende zijn:
* [Verbruiksplan](#consumption) (standaard)
* [Premium-abonnement](#premium)
* [App Service-plan](#app-service-plan)

### <a name="function-app"></a>Functie-app

De functie-app-resource wordt gedefinieerd met behulp van een resource van het type **Microsoft.Web/sites** en kind **functionapp:**

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
> Als u expliciet een hostingplan definieert, is er een extra item nodig in de matrix dependsOn: `"[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"`

Een functie-app moet deze toepassingsinstellingen bevatten:

| Naam van de instelling                 | Description                                                                               | Voorbeeldwaarden                        |
|------------------------------|-------------------------------------------------------------------------------------------|---------------------------------------|
| AzureWebJobsStorage          | Een connection string naar een opslagaccount dat door de Functions-runtime wordt gebruikt voor interne wachtrijen | Zie [Opslagaccount](#storage)       |
| FUNCTIONS_EXTENSION_VERSION  | De versie van de Azure Functions runtime                                                | `~3`                                  |
| FUNCTIONS_WORKER_RUNTIME     | De taalstack die moet worden gebruikt voor functies in deze app                                   | `dotnet`, `node` , `java` , `python` of `powershell` |
| WEBSITE_NODE_DEFAULT_VERSION | Alleen nodig als u de `node` taalstack gebruikt, geeft de te gebruiken versie op              | `10.14.1`                             |

Deze eigenschappen worden opgegeven in de `appSettings` verzameling in de eigenschap `siteConfig` :

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
                "value": "~3"
            }
        ]
    }
}
```

<a name="consumption"></a>

## <a name="deploy-on-consumption-plan"></a>Implementeren op verbruiksplan

Het verbruiksplan wijst automatisch rekenkracht toe wanneer uw code wordt uitgevoerd, schaalt zo nodig uit om de belasting te verwerken en schaalt vervolgens in wanneer de code niet wordt uitgevoerd. U hoeft niet te betalen voor niet-actieve VM's en u hoeft van tevoren geen capaciteit te reserveren. Zie Schalen en [hosten Azure Functions voor meer informatie.](consumption-plan.md)

Zie Functie-app Azure Resource Manager verbruiksplan voor een voorbeeld [van een Azure Resource Manager sjabloon.]

### <a name="create-a-consumption-plan"></a>Een verbruiksplan maken

Er hoeft geen verbruiksplan te worden gedefinieerd. Er wordt automatisch een resource per regio gemaakt of geselecteerd wanneer u de functie-app-resource zelf maakt.

Het verbruiksplan is een speciaal type serverfarmresource. Voor Windows kunt u deze opgeven met behulp van `Dynamic` de waarde voor de eigenschappen en `computeMode` `sku` :

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
> Het verbruiksplan kan niet expliciet worden gedefinieerd voor Linux. Deze wordt automatisch gemaakt.

Als u uw Verbruiksplan expliciet definieert, moet u de eigenschap voor de app zo instellen dat deze naar de `serverFarmId` resource-id van het plan wijst. Zorg ervoor dat de functie-app ook `dependsOn` een instelling voor het plan heeft.

### <a name="create-a-function-app"></a>Een functie-app maken

De instellingen die vereist zijn voor een functie-app die wordt uitgevoerd in het Verbruiksplan, worden uitgesteld tussen Windows en Linux. 

#### <a name="windows"></a>Windows

In Windows vereist een verbruiksplan een extra instelling in de siteconfiguratie: [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](functions-app-settings.md#website_contentazurefileconnectionstring) . Met deze eigenschap configureert u het opslagaccount waarin de code en configuratie van de functie-app worden opgeslagen.

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
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                }
            ]
        }
    }
}
```

> [!IMPORTANT]
> Stel de instelling niet in omdat deze voor u wordt gegenereerd wanneer de site voor het [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare) eerst wordt gemaakt.  

#### <a name="linux"></a>Linux

In Linux moet de functie-app zijn ingesteld op en moet de `kind` eigenschap zijn ingesteld op `functionapp,linux` `reserved` `true` . 

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
                    "value": "~3"
                }
            ]
        },
        "reserved": true
    }
}
```

De [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](functions-app-settings.md#website_contentazurefileconnectionstring) instellingen en worden niet ondersteund in [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare) Linux.

<a name="premium"></a>
## <a name="deploy-on-premium-plan"></a>Implementeren in Premium-abonnement

Het Premium-abonnement biedt dezelfde schaal als het verbruiksplan, maar bevat toegewezen resources en aanvullende mogelijkheden. Zie Premium-abonnement [Azure Functions voor meer informatie.](./functions-premium-plan.md)

### <a name="create-a-premium-plan"></a>Een Premium-abonnement maken

Een Premium-abonnement is een speciaal type serverfarmresource. U kunt deze opgeven met behulp van `EP1` , of voor de `EP2` `EP3` `Name` eigenschapswaarde in het `sku` [beschrijvingsobject](/azure/templates/microsoft.web/2018-02-01/serverfarms#skudescription-object).

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

Voor een functie-app in een Premium-abonnement moet `serverFarmId` de eigenschap zijn ingesteld op de resource-id van het plan dat u eerder hebt gemaakt. Bovendien vereist een Premium-abonnement een extra instelling in de siteconfiguratie: [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](functions-app-settings.md#website_contentazurefileconnectionstring) . Met deze eigenschap configureert u het opslagaccount waarin de code en configuratie van de functie-app worden opgeslagen.

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
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                }
            ]
        }
    }
}
```
> [!IMPORTANT]
> Stel de instelling niet in omdat deze voor u wordt gegenereerd wanneer de site voor het [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare) eerst wordt gemaakt.  

<a name="app-service-plan"></a>

## <a name="deploy-on-app-service-plan"></a>Implementeren in App Service abonnement

In het App Service wordt uw functie-app uitgevoerd op toegewezen VM's op Basic-, Standard- en Premium-SKU's, vergelijkbaar met web-apps. Zie het overzicht App Service plan voor meer informatie over hoe [Azure App Service werkt.](../app-service/overview-hosting-plans.md)

Zie Functie-app op Azure Resource Manager-abonnement voor een voorbeeld [van Azure App Service sjabloon.]

### <a name="create-an-app-service-plan"></a>Een App Service-plan maken

Een App Service wordt gedefinieerd door een serverfarmresource.

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

Als u uw app wilt uitvoeren in Linux, moet u ook de `kind` instellen op `Linux` :

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

Een functie-app op een App Service-abonnement moet de eigenschap hebben `serverFarmId` ingesteld op de resource-id van het plan dat u eerder hebt gemaakt.

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
                    "value": "~3"
                }
            ]
        }
    }
}
```

Linux-apps moeten ook een eigenschap `linuxFxVersion` onder `siteConfig` bevatten. Als u alleen code implementeert, wordt de waarde hiervoor bepaald door de gewenste runtimestack in de indeling ```runtime|runtimeVersion``` :

| Stack            | Voorbeeldwaarde                                         |
|------------------|-------------------------------------------------------|
| Python           | `python|3.7`      |
| Javascript       | `node|12`          |
| .NET             | `dotnet|3.1` |

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
                    "value": "~3"
                }
            ],
            "linuxFxVersion": "node|12"
        }
    }
}
```

Als u een [aangepaste containerafbeelding](./functions-create-function-linux-custom-image.md)implementeert, moet u deze opgeven met en configuratie opnemen waarmee uw installatiebestand kan worden binnengehaald, zoals `linuxFxVersion` in [Web App for Containers](../app-service/index.yml). Stel ook in `WEBSITES_ENABLE_APP_SERVICE_STORAGE` op , omdat uw `false` app-inhoud wordt opgegeven in de container zelf:

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
                    "value": "~3"
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

Een functie-app heeft veel onderliggende resources die u kunt gebruiken in uw implementatie, waaronder app-instellingen en opties voor broncodebeheer. U kunt er ook voor kiezen om de onderliggende resource **sourcecontrols** te verwijderen en in plaats daarvan een andere [implementatieoptie te](functions-continuous-deployment.md) gebruiken.

> [!IMPORTANT]
> Als u uw toepassing wilt implementeren met behulp van Azure Resource Manager, is het belangrijk om te begrijpen hoe resources worden geïmplementeerd in Azure. In het volgende voorbeeld worden configuraties op het hoogste niveau toegepast met **behulp van siteConfig.** Het is belangrijk om deze configuraties op het hoogste niveau in te stellen, omdat ze informatie overbrengen naar de Functions-runtime en implementatie-engine. Informatie op het hoogste niveau is vereist voordat de onderliggende **bronbeheer/wesource** wordt toegepast. Hoewel het mogelijk is om deze instellingen te configureren in de **resource config/appSettings**  op onderliggend niveau, moet uw functie-app in sommige gevallen worden geïmplementeerd voordat **config/appSettings** wordt toegepast. Wanneer u bijvoorbeeld functies gebruikt met [Logic Apps,](../logic-apps/index.yml)zijn uw functies een afhankelijkheid van een andere resource.

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
                "value": "~3"
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
          "FUNCTIONS_EXTENSION_VERSION": "~3",
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
> Deze sjabloon maakt gebruik van de waarde project-app-instellingen, waarmee de basismap wordt ingesteld waarin de Functions-implementatie-engine (Kudu) zoekt naar implementeerbare code. [](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) In onze opslagplaats staan onze functies in een submap van de **map src.** Daarom stellen we in het vorige voorbeeld de waarde voor app-instellingen in op `src` . Als uw functies zich in de hoofdmap van uw opslagplaats of als u niet implementeert vanuit broncodebeheer, kunt u deze waarde voor app-instellingen verwijderen.

## <a name="deploy-your-template"></a>De sjabloon implementeren

U kunt een van de volgende manieren gebruiken om uw sjabloon te implementeren:

* [PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
* [Azure-CLI](../azure-resource-manager/templates/deploy-cli.md)
* [Azure-portal](../azure-resource-manager/templates/deploy-portal.md)
* [REST API](../azure-resource-manager/templates/deploy-rest.md)

### <a name="deploy-to-azure-button"></a>De knop Implementeren in Azure

Vervang ```<url-encoded-path-to-azuredeploy-json>``` door een met URL [gecodeerde versie](https://www.bing.com/search?q=url+encode) van het onbewerkte pad van uw bestand in `azuredeploy.json` GitHub.

Hier is een voorbeeld dat gebruikmaakt van markdown:

```markdown
[![Deploy to Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

Hier is een voorbeeld dat gebruikmaakt van HTML:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"></a>
```

### <a name="deploy-using-powershell"></a>Implementeren met PowerShell

Met de volgende PowerShell-opdrachten maakt u een resourcegroep en implementeert u een sjabloon waarmee een functie-app met de vereiste resources wordt gemaakt. Als u lokaal wilt uitvoeren, moet [u Azure PowerShell](/powershell/azure/install-az-ps) geïnstalleerd. Voer uit [`Connect-AzAccount`](/powershell/module/az.accounts/connect-azaccount) om u aan te melden.

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

Als u deze implementatie wilt testen, kunt u een sjabloon zoals deze [gebruiken om](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json) een functie-app in Windows te maken in een verbruiksplan. Vervang `<function-app-name>` door een unieke naam voor uw functie-app.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het ontwikkelen en configureren van Azure Functions.

* [Naslaginformatie over Azure Functions voor ontwikkelaars](functions-reference.md)
* [Instellingen voor Azure-functie-apps configureren](functions-how-to-use-azure-function-app-settings.md)
* [Uw eerste Azure-functie maken](./functions-get-started.md)

<!-- LINKS -->

[Functie-app in verbruiksplan]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[Functie-app in Azure App Service abonnement]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json
