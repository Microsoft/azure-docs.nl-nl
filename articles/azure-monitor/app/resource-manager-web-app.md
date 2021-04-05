---
title: Voorbeelden van Resource Manager-sjablonen voor Azure App Service en Application Insights-resources
description: Voorbeelden van Azure Resource Manager-sjablonen voor het implementeren van een Azure App Service met een Application Insights-resource
ms.subservice: application-insights
ms.topic: sample
ms.custom: devx-track-dotnet
author: lgayhardt
ms.author: lagayhar
ms.date: 08/06/2020
ms.openlocfilehash: 17602083b5712e5e693cf2b1f09664efd92bfe09
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100627376"
---
# <a name="resource-manager-template-samples-for-creating-azure-app-services-web-apps-with-application-insights-monitoring"></a>Voorbeelden van Resource Manager-sjablonen voor het maken van Azure App Service-web-apps en Application Insights-bewaking

In dit artikel vindt u voorbeelden van [Azure Resource Manager-sjablonen](../../azure-resource-manager/templates/template-syntax.md) voor het implementeren en configureren van [klassieke Application Insights-resources](../app/create-new-resource.md) in combinatie met een Azure App Services-web-app. Elk voorbeeld bevat een sjabloonbestand en een parameterbestand met voorbeeldwaarden voor het sjabloon.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]

## <a name="net-core-runtime"></a>.NET Core-runtime

In het volgende voorbeeld wordt een standaard Azure App Service-web-app gemaakt met de .NET Core-runtime en een [klassieke Application Insights-resource](../app/create-new-resource.md) met ingeschakelde bewaking. 

### <a name="template-file"></a>Sjabloonbestand

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "subscriptionId": {
            "type": "string"
        },
        "name": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "hostingPlanName": {
            "type": "string"
        },
        "serverFarmResourceGroup": {
            "type": "string"
        },
        "alwaysOn": {
            "type": "bool"
        },
        "currentStack": {
            "type": "string"
        },
        "phpVersion": {
            "type": "string"
        },
        "errorLink": {
            "type": "string"
        }
    },
    "resources": [
        {
            "apiVersion": "2018-11-01",
            "name": "[parameters('name')]",
            "type": "Microsoft.Web/sites",
            "location": "[parameters('location')]",
            "tags": {},
            "dependsOn": [
                "microsoft.insights/components/web-app-name-01"
            ],
            "properties": {
                "name": "[parameters('name')]",
                "siteConfig": {
                    "appSettings": [
                        {
                            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                            "value": "[reference('microsoft.insights/components/web-app-name-01', '2015-05-01').InstrumentationKey]"
                        },
                        {
                            "name": "APPLICATIONINSIGHTS_CONNECTION_STRING",
                            "value": "[reference('microsoft.insights/components/web-app-name-01', '2015-05-01').ConnectionString]"
                        },
                        {
                            "name": "ApplicationInsightsAgent_EXTENSION_VERSION",
                            "value": "~2"
                        },
                        {
                            "name": "XDT_MicrosoftApplicationInsights_Mode",
                            "value": "default"
                        },
                        {
                            "name": "ANCM_ADDITIONAL_ERROR_PAGE_LINK",
                            "value": "[parameters('errorLink')]"
                        }
                    ],
                    "metadata": [
                        {
                            "name": "CURRENT_STACK",
                            "value": "[parameters('currentStack')]"
                        }
                    ],
                    "phpVersion": "[parameters('phpVersion')]",
                    "alwaysOn": "[parameters('alwaysOn')]"
                },
                "serverFarmId": "[concat('/subscriptions/', parameters('subscriptionId'),'/resourcegroups/', parameters('serverFarmResourceGroup'), '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "clientAffinityEnabled": true
            }
        },
        {
            "apiVersion": "2015-05-01",
            "name": "web-app-name-01",
            "type": "microsoft.insights/components",
            "location": "centralus",
            "tags": {},
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Request_Source": "IbizaWebAppExtensionCreate"
            }
        }
    ]
}
```

### <a name="parameter-file"></a>Parameterbestand

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "subscriptionId": {
      "value": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    },
    "name": {
      "value": "web-app-name-01"
    },
    "location": {
      "value": "Central US"
    },
    "hostingPlanName": {
      "value": "WebApplication2720191003010920Plan"
    },
    "serverFarmResourceGroup": {
      "value": "Testwebapp01"
    },
    "alwaysOn": {
      "value": true
    },
    "currentStack": {
      "value": "dotnetcore"
    },
    "phpVersion": {
      "value": "OFF"
    },
    "errorLink": {
      "value": "https://web-app-name-01.scm.azurewebsites.net/detectors?type=tools&name=eventviewer"
    }
  }
}

```

## <a name="aspnet-runtime"></a>ASP.NET-runtime

In het volgende voorbeeld wordt een standaard Azure App Service-web-app gemaakt met de ASP.NET Core-runtime en een [klassieke Application Insights-resource](../app/create-new-resource.md) met ingeschakelde bewaking. 

### <a name="template-file"></a>Sjabloonbestand

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "subscriptionId": {
            "type": "string"
        },
        "name": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "hostingPlanName": {
            "type": "string"
        },
        "serverFarmResourceGroup": {
            "type": "string"
        },
        "alwaysOn": {
            "type": "bool"
        },
        "currentStack": {
            "type": "string"
        },
        "phpVersion": {
            "type": "string"
        },
        "netFrameworkVersion": {
            "type": "string"
        }
    },
    "resources": [
        {
            "apiVersion": "2018-11-01",
            "name": "[parameters('name')]",
            "type": "Microsoft.Web/sites",
            "location": "[parameters('location')]",
            "tags": {},
            "dependsOn": [
                "microsoft.insights/components/web-app-name-01"
            ],
            "properties": {
                "name": "[parameters('name')]",
                "siteConfig": {
                    "appSettings": [
                        {
                            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                            "value": "[reference('microsoft.insights/components/web-app-name-01', '2015-05-01').InstrumentationKey]"
                        },
                        {
                            "name": "APPLICATIONINSIGHTS_CONNECTION_STRING",
                            "value": "[reference('microsoft.insights/components/web-app-name-01', '2015-05-01').ConnectionString]"
                        },
                        {
                            "name": "ApplicationInsightsAgent_EXTENSION_VERSION",
                            "value": "~2"
                        },
                        {
                            "name": "XDT_MicrosoftApplicationInsights_Mode",
                            "value": "default"
                        }
                    ],
                    "metadata": [
                        {
                            "name": "CURRENT_STACK",
                            "value": "[parameters('currentStack')]"
                        }
                    ],
                    "phpVersion": "[parameters('phpVersion')]",
                    "netFrameworkVersion": "[parameters('netFrameworkVersion')]",
                    "alwaysOn": "[parameters('alwaysOn')]"
                },
                "serverFarmId": "[concat('/subscriptions/', parameters('subscriptionId'),'/resourcegroups/', parameters('serverFarmResourceGroup'), '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "clientAffinityEnabled": true
            }
        },
        {
            "apiVersion": "2015-05-01",
            "name": "web-app-name-01",
            "type": "microsoft.insights/components",
            "location": "centralus",
            "tags": {},
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Request_Source": "IbizaWebAppExtensionCreate"
            }
        }
    ]
}
```

### <a name="parameters-file"></a>Parameterbestand

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "subscriptionId": {
      "value": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    },
    "name": {
      "value": "web-app-name-01"
    },
    "location": {
      "value": "Central US"
    },
    "hostingPlanName": {
      "value": "WebApplication2720191003010920Plan"
    },
    "serverFarmResourceGroup": {
      "value": "Testwebapp01"
    },
    "alwaysOn": {
      "value": true
    },
    "currentStack": {
      "value": "dotnet"
    },
    "phpVersion": {
      "value": "OFF"
    },
    "netFrameworkVersion": {
      "value": "v4.0"
    }
  }
}
```

## <a name="nodejs-runtime-linux"></a>Node.js-runtime (Linux)

In het volgende voorbeeld wordt een standaard Azure App Service-web-app in Linux gemaakt met de node.js-runtime en een [klassieke Application Insights-resource](../app/create-new-resource.md) met ingeschakelde bewaking. 

### <a name="template-file"></a>Sjabloonbestand

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "subscriptionId": {
            "type": "string"
        },
        "name": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "hostingPlanName": {
            "type": "string"
        },
        "serverFarmResourceGroup": {
            "type": "string"
        },
        "alwaysOn": {
            "type": "bool"
        },
        "linuxFxVersion": {
            "type": "string"
        }
    },
    "resources": [
        {
            "apiVersion": "2018-11-01",
            "name": "[parameters('name')]",
            "type": "Microsoft.Web/sites",
            "location": "[parameters('location')]",
            "tags": {},
            "dependsOn": [
                "microsoft.insights/components/web-app-name-01"
            ],
            "properties": {
                "name": "[parameters('name')]",
                "siteConfig": {
                    "appSettings": [
                        {
                            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                            "value": "[reference('microsoft.insights/components/web-app-name-01', '2015-05-01').InstrumentationKey]"
                        },
                        {
                            "name": "APPLICATIONINSIGHTS_CONNECTION_STRING",
                            "value": "[reference('microsoft.insights/components/web-app-name-01', '2015-05-01').ConnectionString]"
                        },
                        {
                            "name": "ApplicationInsightsAgent_EXTENSION_VERSION",
                            "value": "~2"
                        },
                        {
                            "name": "XDT_MicrosoftApplicationInsights_Mode",
                            "value": "default"
                        }
                    ],
                    "linuxFxVersion": "[parameters('linuxFxVersion')]",
                    "alwaysOn": "[parameters('alwaysOn')]"
                },
                "serverFarmId": "[concat('/subscriptions/', parameters('subscriptionId'),'/resourcegroups/', parameters('serverFarmResourceGroup'), '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "clientAffinityEnabled": false
            }
        },
        {
            "apiVersion": "2015-05-01",
            "name": "web-app-name-01",
            "type": "microsoft.insights/components",
            "location": "centralus",
            "tags": null,
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Request_Source": "IbizaWebAppExtensionCreate"
            }
        }
    ]
}
```

### <a name="parameter-file"></a>Parameterbestand

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "subscriptionId": {
      "value": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    },
    "name": {
      "value": "web-app-name-01"
    },
    "location": {
      "value": "Central US"
    },
    "hostingPlanName": {
      "value": "App-planTest01-916e"
    },
    "serverFarmResourceGroup": {
      "value": "app_resource_group_custom"
    },
    "alwaysOn": {
      "value": true
    },
    "linuxFxVersion": {
      "value": "NODE|12-lts"
    }
  }
}
```

## <a name="next-steps"></a>Volgende stappen

* [Meer voorbeeldsjablonen voor Azure Monitor](../resource-manager-samples.md).
* [Meer informatie over klassieke van Application Insights-resources](../app/create-new-resource.md).
