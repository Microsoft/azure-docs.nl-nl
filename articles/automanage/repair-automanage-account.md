---
title: Een gebroken Azure automanage-account herstellen
description: Als u onlangs een abonnement hebt verplaatst dat een automanage-account bevat naar een nieuwe Tenant, moet u de app opnieuw configureren. In dit artikel leert u hoe.
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: alsin
ms.openlocfilehash: 4694fa679c7bbff309a0452219ff39bacf2488c4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96183699"
---
# <a name="repair-an-automanage-account"></a>Een account voor automanage herstellen
Uw [Azure automanage-account](./automanage-virtual-machines.md#automanage-account) is de beveiligings context of identiteit waaronder de geautomatiseerde bewerkingen plaatsvinden. Als u onlangs een abonnement hebt verplaatst dat een automanage-account bevat naar een nieuwe Tenant, moet u het account opnieuw configureren. Als u het opnieuw wilt configureren, moet u het identiteits type opnieuw instellen en de juiste rollen voor het account toewijzen.

## <a name="step-1-reset-the-automanage-account-identity-type"></a>Stap 1: het identiteits type van het account voor automanage opnieuw instellen
Stel het identiteits type van het automanage-account opnieuw in met behulp van de volgende Azure Resource Manager (ARM)-sjabloon. Sla het bestand lokaal op als armdeploy.jsop of een vergelijk bare naam. Noteer uw account naam en-locatie voor het automanage, omdat de vereiste para meters in de ARM-sjabloon vereist zijn.

1. Maak een Resource Manager-implementatie met behulp van de volgende sjabloon. Gebruik `identityType = None`.
    * U kunt de implementatie in de Azure CLI maken met behulp van `az deployment sub create` . Zie [AZ Deployment sub](/cli/azure/deployment/sub)(Engelstalig) voor meer informatie.
    * U kunt de implementatie in Power shell maken met behulp van de `New-AzDeployment` module. Zie [New-AzDeployment](/powershell/module/az.resources/new-azdeployment)voor meer informatie.

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

## <a name="step-2-assign-appropriate-roles-for-the-automanage-account"></a>Stap 2: de juiste rollen toewijzen voor het account voor automatisch beheren
Voor het account voor de functie voor automanaged moet de Inzender-en het resource beleid zijn ingesteld op het abonnement met de Vm's die door automanage worden beheerd. U kunt deze rollen toewijzen met behulp van de Azure Portal, ARM-sjablonen of de Azure CLI.

Als u een ARM-sjabloon of Azure CLI gebruikt, hebt u de principal-ID (ook wel de object-ID genoemd) van uw automanage-account nodig. (U hebt de ID niet nodig als u de Azure Portal gebruikt.) U kunt deze ID vinden met behulp van de volgende methoden:

- [Azure cli](/cli/azure/ad/sp): gebruik de opdracht `az ad sp list --display-name <name of your Automanage Account>` .

- Azure Portal: Ga naar **Azure Active Directory** en zoek uw account voor automanage op naam. Selecteer onder **bedrijfs toepassingen** de naam van het automanage-account wanneer het wordt weer gegeven.

### <a name="azure-portal"></a>Azure Portal
1. Onder **abonnementen** gaat u naar het abonnement met uw automanaged vm's.
1. Ga naar **toegangs beheer (IAM)**.
1. Selecteer **roltoewijzingen toevoegen**.
1. Selecteer de rol **Inzender** en voer de naam van uw account voor automanage in.
1. Selecteer **Opslaan**.
1. Herhaal stap 3 t/m 5, deze keer met de rol **Inzender voor resource beleid** .

### <a name="arm-template"></a>ARM-sjabloon
Voer de volgende ARM-sjabloon uit. U hebt de principal-ID van uw account voor automanage nodig. De stappen om deze op te halen, zijn aan het begin van deze sectie. Voer de ID in wanneer u hierom wordt gevraagd.

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
[Meer informatie over Azure automanage](./automanage-virtual-machines.md)