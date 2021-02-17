---
title: Stoppen met het bewaken van uw Azure Red Hat open Shift v3-cluster | Microsoft Docs
description: In dit artikel wordt beschreven hoe u de bewaking van uw Azure Red Hat open Shift-cluster kunt stoppen met Azure Monitor voor containers.
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 7e6ab46940ed29a98b3988c00c92d6c691d6e0f0
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100613032"
---
# <a name="how-to-stop-monitoring-your-azure-red-hat-openshift-v3-cluster"></a>Het bewaken van uw Azure Red Hat open Shift v3-cluster stoppen

>[!IMPORTANT]
> Azure Red Hat open Shift 3,11 wordt in juni 2022.
>
> Vanaf oktober 2020 kunt u geen nieuwe 3,11-clusters meer maken.
> Bestaande 3,11-clusters blijven actief tot juni 2022, maar worden na die datum niet meer ondersteund.
>
> Volg deze hand leiding voor het [maken van een Azure Red Hat open Shift 4-cluster](../../openshift/tutorial-create-cluster.md).
> Als u specifieke vragen hebt, [kunt u contact met ons](mailto:aro-feedback@microsoft.com)opnemen.

Nadat u de bewaking van uw Azure Red Hat open Shift versie 3. x-cluster hebt ingeschakeld, kunt u stoppen met het bewaken van het cluster met Azure Monitor voor containers als u besluit dat u het niet meer wilt controleren. In dit artikel wordt beschreven hoe u dit kunt doen met behulp van de Azure Resource Manager sjabloon.  

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sjabloon

Gegeven, zijn twee Azure Resource Manager sjabloon voor het consistent verwijderen van de oplossings resources en herhaaldelijk in uw resource groep. Een JSON-sjabloon geeft u de configuratie voor het stoppen van de controle en de andere bevat parameter waarden die u configureert om de open Shift-cluster resource-ID en Azure-regio op te geven waarin het cluster is geïmplementeerd.

Als u niet bekend bent met het concept van het implementeren van resources met behulp van een sjabloon, raadpleegt u:
* [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)
* [Resources implementeren met Resource Manager-sjablonen en Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Als u ervoor kiest om de Azure CLI te gebruiken, moet u de CLI eerst lokaal installeren en gebruiken. U moet de Azure CLI-versie 2.0.65 of hoger uitvoeren. Voer uit om uw versie te identificeren `az --version` . Als u de Azure CLI wilt installeren of upgraden, raadpleegt u [de Azure cli installeren](/cli/azure/install-azure-cli).

### <a name="create-template"></a>Sjabloon maken

1. Kopieer en plak de volgende JSON-syntaxis in het bestand:

    ```json
    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aroResourceId": {
           "type": "string",
           "metadata": {
             "description": "ARO Cluster Resource ID"
          }
        },
        "aroResourceLocation": {
          "type": "string",
          "metadata": {
            "description": "Location of the aro cluster resource e.g. westcentralus"
          }
        }
      },
      "resources": [
        {
           "name": "[split(parameters('aroResourceId'),'/')[8]]",
           "type": "Microsoft.ContainerService/openShiftManagedClusters",
           "location": "[parameters('aroResourceLocation')]",
           "apiVersion": "2019-09-30-preview",
           "properties": {
             "mode": "Incremental",
             "id": "[parameters('aroResourceId')]",
             "monitorProfile": {
               "workspaceResourceID": null,
               "enabled": false
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
        "aroResourceId": {
          "value": "/subscriptions/<subscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.ContainerService/openShiftManagedClusters/<clusterName>"
        },
        "aroResourceLocation": {
          "value": "<azure region of the cluster e.g. westcentralus>"
        }
      }
    }
    ```

4. Bewerk de waarden voor **aroResourceId** en **aroResourceLocation** met behulp van de waarden van het open Shift-cluster, dat u kunt vinden op de pagina **Eigenschappen** voor het geselecteerde cluster.

    ![Pagina container eigenschappen](media/container-insights-optout-openshift/cluster-properties-page.png)

5. Sla dit bestand als **OptOutParam.jsop in** een lokale map.

6. U kunt deze sjabloon nu implementeren.

### <a name="remove-the-solution-using-azure-cli"></a>De oplossing verwijderen met Azure CLI

Voer de volgende opdracht uit met Azure CLI in Linux om de oplossing te verwijderen en de configuratie op uw cluster op te schonen.

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

Voer de volgende Power shell-opdrachten uit in de map met de sjabloon om de oplossing te verwijderen en de configuratie op te schonen van uw cluster.    

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

Als de werk ruimte alleen is gemaakt ter ondersteuning van het bewaken van het cluster en u deze niet meer nodig hebt, moet u deze hand matig verwijderen. Zie [een Azure log Analytics-werk ruimte verwijderen](../platform/delete-workspace.md)als u niet bekend bent met het verwijderen van een werk ruimte.
