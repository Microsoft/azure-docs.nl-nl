---
title: Het bewaken van uw Azure Kubernetes service-cluster stoppen | Microsoft Docs
description: In dit artikel wordt beschreven hoe u de bewaking van uw Azure AKS-cluster kunt stoppen met Azure Monitor voor containers.
ms.topic: conceptual
ms.date: 08/19/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 476934d84d8ffc63336ec620432db3507b2cbb34
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943837"
---
# <a name="how-to-stop-monitoring-your-azure-kubernetes-service-aks-with-azure-monitor-for-containers"></a>Stoppen met controleren van Azure Kubernetes Service (AKS) met Azure Monitor voor containers

Nadat u de bewaking van uw AKS-cluster hebt ingeschakeld, kunt u stoppen met het bewaken van het cluster als u besluit dat u het niet meer wilt bewaken. In dit artikel wordt beschreven hoe u dit kunt doen met behulp van de Azure CLI of met de meegeleverde Azure Resource Manager sjablonen.  


## <a name="azure-cli"></a>Azure CLI

Gebruik de opdracht [AZ AKS Disable-addons](/cli/azure/aks#az-aks-disable-addons) om Azure monitor voor containers uit te scha kelen. Met de opdracht wordt de agent uit de cluster knooppunten verwijderd, wordt de oplossing niet verwijderd of worden de gegevens die al zijn verzameld en opgeslagen in uw Azure Monitor-resource.  

```azurecli
az aks disable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG
```

Zie [bewaking inschakelen met Azure cli](container-insights-enable-new-cluster.md#enable-using-azure-cli)om de bewaking van uw cluster weer in te scha kelen.

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sjabloon

Gegeven, zijn twee Azure Resource Manager sjabloon voor het consistent verwijderen van de oplossings resources en herhaaldelijk in uw resource groep. Een JSON-sjabloon waarmee de configuratie wordt opgegeven voor het stoppen van de bewaking en de andere bevat parameter waarden die u configureert om de AKS-cluster resource-ID en de resource groep op te geven waarin het cluster is geïmplementeerd.

Als u niet bekend bent met het concept van het implementeren van resources met behulp van een sjabloon, raadpleegt u:
* [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)
* [Resources implementeren met Resource Manager-sjablonen en Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

>[!NOTE]
>De sjabloon moet worden geïmplementeerd in dezelfde resource groep van het cluster. Als u andere eigenschappen of invoeg toepassingen weglaat wanneer u deze sjabloon gebruikt, kan dit leiden tot verwijdering van het cluster. Bijvoorbeeld *enableRBAC* for Kubernetes RBAC-beleid dat is geïmplementeerd in uw cluster of *aksResourceTagValues* als er labels zijn opgegeven voor het AKS-cluster.  
>

Als u ervoor kiest om de Azure CLI te gebruiken, moet u de CLI eerst lokaal installeren en gebruiken. U moet de Azure CLI-versie 2.0.27 of hoger uitvoeren. Voer uit om uw versie te identificeren `az --version` . Als u de Azure CLI wilt installeren of upgraden, raadpleegt u [de Azure cli installeren](/cli/azure/install-azure-cli).

### <a name="create-template"></a>Sjabloon maken

1. Kopieer en plak de volgende JSON-syntaxis in het bestand:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
           "type": "string",
           "metadata": {
             "description": "AKS Cluster Resource ID"
           }
       },
      "aksResourceLocation": {
        "type": "string",
        "metadata": {
           "description": "Location of the AKS resource e.g. \"East US\""
         }
       },
    "aksResourceTagValues": {
      "type": "object",
      "metadata": {
        "description": "Existing all tags on AKS Cluster Resource"
        }
      }
     },
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
        "tags": "[parameters('aksResourceTagValues')]",
        "apiVersion": "2018-03-31",
        "properties": {
          "mode": "Incremental",
          "id": "[parameters('aksResourceId')]",
          "addonProfiles": {
            "omsagent": {
              "enabled": false,
              "config": null
            }
           }
         }
       }
      ]
    }
    ```

2. Sla dit bestand als **OptOutTemplate.jsop in** een lokale map.

3. Plak de volgende JSON-syntaxis in het bestand:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
          "value": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
        },
        "aksResourceLocation": {
          "value": "<aksClusterRegion>"
        },
        "aksResourceTagValues": {
          "value": {
            "<existing-tag-name1>": "<existing-tag-value1>",
            "<existing-tag-name2>": "<existing-tag-value2>",
            "<existing-tag-nameN>": "<existing-tag-valueN>"
          }
        }
      }
    }
    ```

4. Bewerk de waarden voor **aksResourceId** en **aksResourceLocation** met behulp van de waarden van het AKS-cluster, dat u kunt vinden op de pagina **Eigenschappen** voor het geselecteerde cluster.

    ![Pagina container eigenschappen](media/container-insights-optout/container-properties-page.png)

    Terwijl u zich op de pagina **Eigenschappen** bevindt, kopieert u ook de **resource-id van de werk ruimte**. Deze waarde is vereist als u besluit om de Log Analytics-werk ruimte later te verwijderen. Het verwijderen van de Log Analytics-werk ruimte wordt niet uitgevoerd als onderdeel van dit proces.

    Bewerk de waarden voor **aksResourceTagValues** zodat deze overeenkomen met de bestaande label waarden die zijn opgegeven voor het AKS-cluster.

5. Sla dit bestand als **OptOutParam.jsop in** een lokale map.

6. U kunt deze sjabloon nu implementeren.

### <a name="remove-the-solution-using-azure-cli"></a>De oplossing verwijderen met Azure CLI

Voer de volgende opdracht uit met Azure CLI in Linux om de oplossing te verwijderen en de configuratie op uw AKS-cluster op te schonen.

```azurecli
az login   
az account set --subscription "Subscription Name"
az deployment group create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

Het kan enkele minuten duren voordat de configuratie is gewijzigd. Wanneer de service is voltooid, wordt er een bericht weer gegeven dat vergelijkbaar is met het volgende:

```output
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>De oplossing verwijderen met Power shell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Voer de volgende Power shell-opdrachten uit in de map met de sjabloon om de oplossing te verwijderen en de configuratie op te schonen van uw AKS-cluster.    

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <yourSubscriptionName>
New-AzResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

Het kan enkele minuten duren voordat de configuratie is gewijzigd. Wanneer de service is voltooid, wordt er een bericht weer gegeven dat vergelijkbaar is met het volgende:

```output
ProvisioningState       : Succeeded
```


## <a name="next-steps"></a>Volgende stappen

Als de werk ruimte alleen is gemaakt ter ondersteuning van het bewaken van het cluster en u deze niet meer nodig hebt, moet u deze hand matig verwijderen. Als u niet bekend bent met het verwijderen van een werk ruimte, raadpleegt u [een Azure log Analytics-werk ruimte verwijderen met de Azure Portal](../platform/delete-workspace.md). Vergeet niet om de **resource-id van de werk ruimte** die u eerder in stap 4 hebt gekopieerd, te zien.
