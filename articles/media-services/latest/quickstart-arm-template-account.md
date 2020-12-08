---
title: ARM-sjabloon van Azure Media Services-account
titleSuffix: Azure Media Services
description: In dit artikel wordt uitgelegd hoe u een Media Services-account kunt maken met een ARM-sjabloon.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: quickstart
ms.date: 11/24/2020
ms.author: inhenkel
ms.custom: subject-armqs
ms.openlocfilehash: 6a23c3a20e79fe6fff7de8faccf4e4ef78f02585
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185030"
---
# <a name="quickstart-media-services-account-arm-template"></a>Quickstart: ARM-sjabloon van Azure Media Services-account

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In dit artikel wordt uitgelegd hoe u een Media Services-account kunt maken met een Azure Resource Manager-sjabloon.

## <a name="introduction"></a>Inleiding

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Lezers die ervaring hebben met ARM-sjablonen kunnen doorgaan met de [sectie over implementatie](#deploy-the-template).

<!-- this section will be added when the template is merged. If your environment meets the prerequisites and you're familiar with using ARM templates, select the **Deploy to Azure** button. The template will open in the Azure portal.

[![Deploy to Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/<template's URI>)
-->

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

Als u nog nooit een ARM-sjabloon hebt geïmplementeerd, is het handig om meer te weten te komen over [Azure ARM-sjablonen](https://docs.microsoft.com/azure/azure-resource-manager/templates/) en de [zelfstudie](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-tutorial-create-first-template?tabs=azure-powershell) te doen.

## <a name="review-the-template"></a>De sjabloon controleren

<!-- this will be added when the template is merged. The template used in this quickstart is from [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/101-media-services-account-create/).

The syntax for the JSON code fence is:

:::code language="json" source="~/quickstart-templates/101-media-services-account-create/azuredeploy.json"::: -->

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "mediaServiceName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Media Services account. A Media Services account name is unique in a given region, all lowercase letters or numbers with no spaces."
      }
    }
  },
  "variables": {
    "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "name": "[parameters('mediaServiceName')]",
      "type": "Microsoft.Media/mediaServices",
      "apiVersion": "2018-07-01",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageName'))]"
      ],
      "properties": {
        "storageAccounts": [
          {
            "id": "[resourceId('microsoft.storage/storageaccounts/', variables('storageName'))]",
            "type": "Primary"
          }
        ]
      }
    },
    {
      "name": "[variables('storageName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "StorageV2",
      "apiVersion": "2017-10-01",
      "location": "[resourceGroup().location]",
      "tags": {},
      "scale": null,
      "properties": {
          "encryption": {
              "services": {
                  "file": {
                      "enabled": true
                  },
                  "blob": {
                      "enabled": true
                  }
              },
              "keySource": "Microsoft.Storage"
          },
          "accessTier": "Hot"
      }
    }
  ]
}

```

Er worden drie soorten Azure-resources gedefinieerd in de sjabloon:

- [Microsoft.Media/mediaservices](https://docs.microsoft.com/azure/templates/microsoft.media/mediaservices): een Media Services-account maken
- [Microsoft.Storage/storageAccounts](https://docs.microsoft.com/azure/templates/microsoft.storage/storageaccounts): een opslagaccount maken

## <a name="set-the-account"></a>Het account instellen

```azurecli-interactive

az account set --subscription {your subscription name or id}

```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

```azurecli-interactive

az group create --name {the name you want to give your resource group} --location "{pick a location}"

```

## <a name="assign-a-variable-to-your-deployment-file"></a>Wijs een variabele toe aan het implementatiebestand

Voor het gemak maakt u een variabele waarmee het pad naar het sjabloonbestand wordt opgeslagen. Deze variabele maakt het voor u gemakkelijker om de implementatieopdrachten uit te voeren omdat u het pad niet telkens opnieuw hoeft te typen.

```azurecli-interactive

templateFile="{provide the path to the template file}"

```

## <a name="deploy-the-template"></a>De sjabloon implementeren

U wordt gevraagd om de naam van het Media Services-account in te voeren.

```azurecli-interactive

az deployment group create --name {the name you want to give to your deployment} --resource-group {the name of resource group you created} --template-file $templateFile

```

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

U ziet een JSON-antwoord van deze strekking:

```json
{
  "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Resources/deployments/amsarmquickstartdeploy",
  "location": null,
  "name": "amsarmquickstartdeploy",
  "properties": {
    "correlationId": "{correlationid}",
    "debugSetting": null,
    "dependencies": [
      {
        "dependsOn": [
          {
            "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Storage/storageAccounts/storagey44cfdmliwatk",
            "resourceGroup": "amsarmquickstartrg",
            "resourceName": "storagey44cfdmliwatk",
            "resourceType": "Microsoft.Storage/storageAccounts"
          }
        ],
        "id": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/amsarmquickstartrg/providers/Microsoft.Media/mediaServices/{accountname}",
        "resourceGroup": "amsarmquickstartrg",
        "resourceName": "{accountname}",
        "resourceType": "Microsoft.Media/mediaServices"
      }
    ],
    "duration": "PT1M10.8615001S",
    "error": null,
    "mode": "Incremental",
    "onErrorDeployment": null,
    "outputResources": [
      {
        "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Media/mediaServices/{accountname}",
        "resourceGroup": "amsarmquickstartrg"
      },
      {
        "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Storage/storageAccounts/storagey44cfdmliwatk",
        "resourceGroup": "amsarmquickstartrg"
      }
    ],
    "outputs": null,
    "parameters": {
      "mediaServiceName": {
        "type": "String",
        "value": "{accountname}"
      }
    },
    "parametersLink": null,
    "providers": [
      {
        "id": null,
        "namespace": "Microsoft.Media",
        "registrationPolicy": null,
        "registrationState": null,
        "resourceTypes": [
          {
            "aliases": null,
            "apiVersions": null,
            "capabilities": null,
            "locations": [
              "eastus"
            ],
            "properties": null,
            "resourceType": "mediaServices"
          }
        ]
      },
      {
        "id": null,
        "namespace": "Microsoft.Storage",
        "registrationPolicy": null,
        "registrationState": null,
        "resourceTypes": [
          {
            "aliases": null,
            "apiVersions": null,
            "capabilities": null,
            "locations": [
              "eastus"
            ],
            "properties": null,
            "resourceType": "storageAccounts"
          }
        ]
      }
    ],
    "provisioningState": "Succeeded",
    "templateHash": "{templatehash}",
    "templateLink": null,
    "timestamp": "2020-11-24T23:25:52.598184+00:00",
    "validatedResources": null
  },
  "resourceGroup": "amsarmquickstartrg",
  "tags": null,
  "type": "Microsoft.Resources/deployments"
}

```

Bevestig in Azure Portal dat uw resources zijn gemaakt.

![quickstartresources gemaakt](./media/quickstart-arm-template-account/quickstart-arm-template-resources.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u niet van plan bent om de resources te gebruiken die u zojuist hebt gemaakt, kunt u de resourcegroep verwijderen.

```azurecli-interactive

az group delete --name {name of the resource group}

```

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over het gebruik van een ARM-sjabloon door het proces te volgen voor het maken van een sjabloon met parameters, variabelen en meer, kunt u de zelfstudie raadplegen

> [!div class="nextstepaction"]
> [Zelfstudie: uw eerste ARM-sjabloon maken en implementeren](/azure/azure-resource-manager/templates/template-tutorial-create-first-template)