---
title: Bewaking van uw Azure Kubernetes Service cluster stoppen | Microsoft Docs
description: In dit artikel wordt beschreven hoe u de bewaking van uw Azure AKS-cluster met Container Insights kunt stoppen.
ms.topic: conceptual
ms.date: 08/19/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 619b6fc4cce860e5869fd0b31e303b4a474f8428
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774013"
---
# <a name="how-to-stop-monitoring-your-azure-kubernetes-service-aks-with-container-insights"></a>Stoppen met het bewaken van uw Azure Kubernetes Service (AKS) met Container Insights

Nadat u de bewaking van uw AKS-cluster hebt ingeschakeld, kunt u stoppen met het bewaken van het cluster als u besluit dat u het cluster niet meer wilt bewaken. In dit artikel wordt beschreven hoe u dit kunt doen met behulp van de Azure CLI of met de opgegeven Azure Resource Manager sjablonen.  


## <a name="azure-cli"></a>Azure CLI

Gebruik de [opdracht az aks disable-addons](/cli/azure/aks#az_aks_disable_addons) om Container Insights uit te schakelen. Met de opdracht wordt de agent uit de clusterknooppunten verwijderd. Hiermee wordt de oplossing of de gegevens die al zijn verzameld en opgeslagen in uw Azure Monitor verwijderd.  

```azurecli
az aks disable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG
```

Zie Bewaking inschakelen met behulp van Azure CLI als u bewaking voor uw cluster opnieuw [wilt inschakelen.](container-insights-enable-new-cluster.md#enable-using-azure-cli)

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sjabloon

Opgegeven zijn twee Azure Resource Manager ter ondersteuning van het consistent en herhaaldelijk verwijderen van de oplossingsresources in uw resourcegroep. Een is een JSON-sjabloon die de configuratie opgeeft om de bewaking te stoppen en de andere bevat parameterwaarden die u configureert om de resource-id en resourcegroep van het AKS-cluster op te geven waarin het cluster is geïmplementeerd.

Als u niet bekend bent met het concept van het implementeren van resources met behulp van een sjabloon, gaat u naar:
* [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)
* [Resources implementeren met Resource Manager-sjablonen en de Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

>[!NOTE]
>De sjabloon moet worden geïmplementeerd in dezelfde resourcegroep van het cluster. Als u andere eigenschappen of invoegtoepassingen weglaten wanneer u deze sjabloon gebruikt, kan dit ertoe leiden dat ze uit het cluster worden verwijderd. Schakel *bijvoorbeeldRBAC* in voor Kubernetes RBAC-beleid dat in uw cluster is geïmplementeerd, of *aksResourceTagValues* als er tags zijn opgegeven voor het AKS-cluster.  
>

Als u ervoor kiest om de Azure CLI te gebruiken, moet u eerst de CLI lokaal installeren en gebruiken. U moet azure CLI versie 2.0.27 of hoger uitvoeren. Voer uit om uw versie te `az --version` identificeren. Zie Azure CLI installeren als u de Azure CLI wilt installeren of [upgraden.](/cli/azure/install-azure-cli)

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

2. Sla dit bestand op **OptOutTemplate.jsin** een lokale map.

3. Plak de volgende JSON-syntaxis in uw bestand:

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

4. Bewerk de waarden **voor aksResourceId** en **aksResourceLocation** met behulp van de  waarden van het AKS-cluster. Deze waarden vindt u op de pagina Eigenschappen voor het geselecteerde cluster.

    ![Pagina Containereigenschappen](media/container-insights-optout/container-properties-page.png)

    Terwijl u op de pagina **Eigenschappen bent,** kopieert u ook de **resource-id van de werkruimte.** Deze waarde is vereist als u besluit de Log Analytics-werkruimte later te verwijderen. Het verwijderen van de Log Analytics-werkruimte wordt niet uitgevoerd als onderdeel van dit proces.

    Bewerk de waarden voor **aksResourceTagValues** zo dat deze overeenkomen met de bestaande tagwaarden die zijn opgegeven voor het AKS-cluster.

5. Sla dit bestand op **OptOutParam.jsin** een lokale map.

6. U kunt deze sjabloon nu implementeren.

### <a name="remove-the-solution-using-azure-cli"></a>De oplossing verwijderen met behulp van Azure CLI

Voer de volgende opdracht uit met Azure CLI in Linux om de oplossing te verwijderen en de configuratie op uw AKS-cluster op te schonen.

```azurecli
az login   
az account set --subscription "Subscription Name"
az deployment group create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

Het kan enkele minuten duren voordat de configuratiewijziging is voltooid. Wanneer dit is voltooid, wordt een bericht geretourneerd dat vergelijkbaar is met het volgende resultaat:

```output
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>De oplossing verwijderen met behulp van PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Voer de volgende PowerShell-opdrachten uit in de map met de sjabloon om de oplossing te verwijderen en de configuratie op te schonen uit uw AKS-cluster.    

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <yourSubscriptionName>
New-AzResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

Het kan enkele minuten duren voordat de configuratiewijziging is voltooid. Wanneer dit is voltooid, wordt een bericht geretourneerd dat vergelijkbaar is met het volgende resultaat:

```output
ProvisioningState       : Succeeded
```


## <a name="next-steps"></a>Volgende stappen

Als de werkruimte alleen is gemaakt ter ondersteuning van het bewaken van het cluster en deze niet meer nodig is, moet u deze handmatig verwijderen. Als u niet bekend bent met het verwijderen van een werkruimte, zie [Een Azure Log Analytics-werkruimte verwijderen met de Azure Portal](../logs/delete-workspace.md). Vergeet niet de resource-id van de **werkruimte** die u eerder in stap 4 hebt gekopieerd. U hebt deze nodig.