---
title: Implementatiegeschiedenis
description: Beschrijft hoe u Azure Resource Manager implementaties kunt weergeven met de portal, PowerShell, Azure CLI en REST API.
tags: top-support-issue
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: e7ed2096a696efdc9a2654a8fd0c294c82cbd4f7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781861"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Implementatiegeschiedenis weergeven met Azure Resource Manager

Azure Resource Manager kunt u uw implementatiegeschiedenis bekijken. U kunt specifieke bewerkingen in eerdere implementaties onderzoeken en zien welke resources zijn geïmplementeerd. Deze geschiedenis bevat informatie over eventuele fouten.

De implementatiegeschiedenis voor een resourcegroep is beperkt tot 800 implementaties. Wanneer u de limiet nadert, worden implementaties automatisch uit de geschiedenis verwijderd. Raadpleeg [Automatic deletions from deployment history](deployment-history-deletions.md) (Automatische verwijderingen uit de implementatiegeschiedenis) voor meer informatie.

Zie Veelvoorkomende fouten bij het implementeren van resources in Azure oplossen met Azure Resource Manager voor hulp bij [het oplossen Azure Resource Manager.](common-deployment-errors.md)

## <a name="get-deployments-and-correlation-id"></a>Implementaties en correlatie-id's op halen

U kunt details over een implementatie bekijken via Azure Portal, PowerShell, Azure CLI of REST API. Elke implementatie heeft een correlatie-id, die wordt gebruikt om gerelateerde gebeurtenissen bij te houden. Als u [een aanvraag ondersteuning voor Azure,](../../azure-portal/supportability/how-to-create-azure-support-request.md)kan ondersteuning u vragen om de correlatie-id. Ondersteuning gebruikt de correlatie-id om de bewerkingen voor de mislukte implementatie te identificeren.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Selecteer de resourcegroep die u wilt onderzoeken.

1. Selecteer de koppeling onder **Implementaties.**

   ![Implementatiegeschiedenis selecteren](./media/deployment-history/select-deployment-history.png)

1. Selecteer een van de implementaties uit de implementatiegeschiedenis.

   ![Selecteer de implementatie](./media/deployment-history/select-details.png)

1. Er wordt een samenvatting van de implementatie weergegeven, met inbegrip van de correlatie-id.

    ![Implementatieoverzicht](./media/deployment-history/show-correlation-id.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Gebruik de opdracht [Get-AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment) om alle implementaties voor een resourcegroep weer te geven.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

Voeg de parameter DeploymentName toe om een specifieke implementatie van een **resourcegroep op te** halen.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment
```

Gebruik het volgende om de correlatie-id op te halen:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment).CorrelationId
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik az deployment group list om de implementatie voor een resourcegroep [weer te maken.](/cli/azure/group/deployment#az_deployment_group_list)

```azurecli-interactive
az deployment group list --resource-group ExampleGroup
```

Gebruik de az deployment group show om een specifieke implementatie [op te halen.](/cli/azure/group/deployment#az_deployment_group_show)

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment
```

Gebruik het volgende om de correlatie-id op te halen:

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment --query properties.correlationId
```

# <a name="http"></a>[HTTP](#tab/http)

Gebruik de volgende bewerking om de implementaties voor een resourcegroep weer te maken. Zie  [Deployments - List By Resource Group (Implementaties -](/rest/api/resources/deployments/listbyresourcegroup)Lijst op resourcegroep) voor het meest recente API-versienummer dat u in de aanvraag kunt gebruiken.

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/?api-version={api-version}
```

Om een specifieke implementatie te krijgen. gebruik de volgende bewerking. Zie Implementaties - Get voor het meest recente API-versienummer dat u in de aanvraag [kunt gebruiken.](/rest/api/resources/deployments/get)

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

Het antwoord bevat de correlatie-id.

```json
{
 ...
 "properties": {
   "mode": "Incremental",
   "provisioningState": "Failed",
   "timestamp": "2019-11-26T14:18:36.4518358Z",
   "duration": "PT26.2091817S",
   "correlationId": "47ff4228-bf2e-4ee5-a008-0b07da681230",
   ...
 }
}
```

---

## <a name="get-deployment-operations-and-error-message"></a>Implementatiebewerkingen en foutbericht ontvangen

Elke implementatie kan meerdere bewerkingen bevatten. Bekijk de implementatiebewerkingen voor meer informatie over een implementatie. Wanneer een implementatie mislukt, bevatten de implementatiebewerkingen een foutbericht.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Selecteer bewerkingsdetails in de samenvatting **voor een implementatie.**

    ![Bewerkingsdetails selecteren](./media/deployment-history/get-operation-details.png)

1. U ziet de details voor die stap van de implementatie. Wanneer er een fout optreedt, bevatten de details het foutbericht.

    ![Bewerkingsdetails tonen](./media/deployment-history/see-operation-details.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u de implementatiebewerkingen voor implementatie naar een resourcegroep wilt weergeven, gebruikt u de opdracht [Get-AzResourceGroupDeploymentOperation.](/powershell/module/az.resources/get-azdeploymentoperation)

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy
```

Als u mislukte bewerkingen wilt weergeven, filtert u bewerkingen met **de status** Mislukt.

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy).Properties | Where-Object ProvisioningState -eq Failed
```

Gebruik de volgende opdracht om het statusbericht van mislukte bewerkingen op te halen:

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy ).Properties | Where-Object ProvisioningState -eq Failed).StatusMessage.error
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u de implementatiebewerkingen voor implementatie naar een resourcegroep wilt weergeven, gebruikt u [de opdracht az deployment operation group list.](/cli/azure/deployment/operation/group#az_deployment-operation-group-list) U moet Azure CLI 2.6.0 of hoger hebben.

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeployment
```

Als u mislukte bewerkingen wilt weergeven, filtert u bewerkingen met **de status** Mislukt.

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed']"
```

Gebruik de volgende opdracht om het statusbericht van mislukte bewerkingen op te halen:

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed'].properties.statusMessage.error"
```

# <a name="http"></a>[HTTP](#tab/http)

Gebruik de volgende bewerking om implementatiebewerkingen op te halen. Zie Deployment Operations - List (Implementatiebewerkingen - Lijst) voor het meest recente API-versienummer dat u in de aanvraag [kunt gebruiken.](/rest/api/resources/deploymentoperations/list)

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```

Het antwoord bevat een foutbericht.

```json
{
  "value": [
    {
      "id": "/subscriptions/xxxx/resourceGroups/examplegroup/providers/Microsoft.Resources/deployments/exampledeploy/operations/13EFD9907103D640",
      "operationId": "13EFD9907103D640",
      "properties": {
        "provisioningOperation": "Create",
        "provisioningState": "Failed",
        "timestamp": "2019-11-26T14:18:36.3177613Z",
        "duration": "PT21.0580179S",
        "trackingId": "9d3cdac4-54f8-486c-94bd-10c20867b8bc",
        "serviceRequestId": "01a9d0fe-896b-4c94-a30f-60b70a8f1ad9",
        "statusCode": "BadRequest",
        "statusMessage": {
          "error": {
            "code": "InvalidAccountType",
            "message": "The AccountType Standard_LRS1 is invalid. For more information, see - https://aka.ms/storageaccountskus"
          }
        },
        "targetResource": {
          "id": "/subscriptions/xxxx/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/storageq2czadzfgizc2",
          "resourceType": "Microsoft.Storage/storageAccounts",
          "resourceName": "storageq2czadzfgizc2"
        }
      }
    },
    ...
  ]
}
```

---

## <a name="next-steps"></a>Volgende stappen

* Zie Veelvoorkomende fouten bij het implementeren van resources in Azure oplossen met Azure Resource Manager voor hulp bij [het oplossen Azure Resource Manager.](common-deployment-errors.md)
* Zie Automatische verwijderingen uit de implementatiegeschiedenis voor meer informatie over hoe implementaties worden beheerd in [de geschiedenis.](deployment-history-deletions.md)
* Zie Deploy a resource group with Azure Resource Manager template (Een resourcegroep implementeren met een Azure Resource Manager [implementatie).](deploy-powershell.md)
