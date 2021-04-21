---
title: Een defect account Azure Automanage herstellen
description: Als u onlangs een abonnement met een Automanage-account naar een nieuwe tenant hebt verplaatst, moet u het opnieuw configureren. In dit artikel leert u hoe u dit kunt doen.
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: alsin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e6bf5404a33e0b4e57c2ff8d82d8791eda3d0f06
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834188"
---
# <a name="repair-an-automanage-account"></a>Een automanage-account herstellen
Uw [Azure Automanage account](./automanage-virtual-machines.md#automanage-account) is de beveiligingscontext of identiteit waaronder de geautomatiseerde bewerkingen plaatsvinden. Als u onlangs een abonnement met een Automanage-account naar een nieuwe tenant hebt verplaatst, moet u het account opnieuw configureren. Als u het opnieuw wilt configureren, moet u het identiteitstype opnieuw instellen en de juiste rollen voor het account toewijzen.

## <a name="step-1-reset-the-automanage-account-identity-type"></a>Stap 1: het identiteitstype Account automatisch opnieuw instellen
Stel het identiteitstype Account automatischmanage opnieuw in met behulp van de Azure Resource Manager arm-sjabloon (Arm). Sla het bestand lokaal op armdeploy.jsnaam of een vergelijkbare naam. Noteer de automanage accountnaam en -locatie, omdat dit vereiste parameters zijn in de ARM-sjabloon.

1. Maak een Resource Manager implementatie met behulp van de volgende sjabloon. Gebruik `identityType = None`.
    * U kunt de implementatie in de Azure CLI maken met behulp van `az deployment sub create` . Zie az [deployment sub voor meer informatie.](/cli/azure/deployment/sub)
    * U kunt de implementatie in PowerShell maken met behulp van de `New-AzDeployment` module . Zie [New-AzDeployment](/powershell/module/az.resources/new-azdeployment)voor meer informatie.

1. Voer dezelfde ARM-sjabloon opnieuw uit met `identityType = SystemAssigned` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "identityType": {
            "type": "string",
            "allowedValues": [ "None", "SystemAssigned" ]
        }
    },
    "resources": [
        {
            "apiVersion": "2020-06-30-preview",
            "name": "[parameters('accountName')]",
            "location": "[parameters('location')]",
            "type": "Microsoft.Automanage/accounts",
            "identity": {
                "type": "[parameters('identityType')]"
            }
        }
    ]
}

```

## <a name="step-2-assign-appropriate-roles-for-the-automanage-account"></a>Stap 2: wijs de juiste rollen toe voor het account voor automatischmanage
Voor het Automanage-account zijn de rollen Inzender en Inzender voor resourcebeleid vereist voor het abonnement dat de VM's bevat die door Automanage worden beheert. U kunt deze rollen toewijzen met behulp van de Azure Portal, ARM-sjablonen of de Azure CLI.

Als u een ARM-sjabloon of de Azure CLI gebruikt, hebt u de principal-id (ook wel de object-id genoemd) van uw Automanage-account nodig. (U hebt de id niet nodig als u de Azure Portal.) U kunt deze id vinden met behulp van de volgende methoden:

- [Azure CLI:](/cli/azure/ad/sp)gebruik de opdracht `az ad sp list --display-name <name of your Automanage Account>` .

- Azure Portal: ga naar **Azure Active Directory** en zoek uw Automanage-account op naam. Onder **Bedrijfstoepassingen** selecteert u de naam van het account automatisch als deze wordt weergegeven.

### <a name="azure-portal"></a>Azure Portal
1. Ga **onder Abonnementen** naar het abonnement dat uw automatischmanagede VM's bevat.
1. Ga naar **Toegangsbeheer (IAM)**.
1. Selecteer **Roltoewijzingen toevoegen.**
1. Selecteer de **rol Inzender** en voer de naam van uw Automanage-account in.
1. Selecteer **Opslaan**.
1. Herhaal stap 3 tot en met 5, deze keer met de rol **Inzender voor resourcebeleid.**

### <a name="arm-template"></a>ARM-sjabloon
Voer de volgende ARM-sjabloon uit. U hebt de principal-id van uw Automanage-account nodig. De stappen voor het op te halen staan aan het begin van deze sectie. Voer de id in wanneer u hier om wordt gevraagd.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        }
    },
    "variables": {
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Resource Policy Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '36243c78-bf99-498c-9df9-86d9f8d28608')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(uniqueString(variables('Contributor')))]",
            "properties": {
                "roleDefinitionId": "[variables('Contributor')]",
                "principalId": "[parameters('principalId')]"
            }
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(uniqueString(variables('Resource Policy Contributor')))]",
            "properties": {
                "roleDefinitionId": "[variables('Resource Policy Contributor')]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

### <a name="azure-cli"></a>Azure CLI
Voer deze opdrachten uit:

```azurecli
az role assignment create --assignee-object-id <your Automanage Account Object ID> --role "Contributor" --scope /subscriptions/<your subscription ID>

az role assignment create --assignee-object-id <your Automanage Account Object ID> --role "Resource Policy Contributor" --scope /subscriptions/<your subscription ID>
```

## <a name="next-steps"></a>Volgende stappen
[Meer informatie over Azure Automanage](./automanage-virtual-machines.md)
