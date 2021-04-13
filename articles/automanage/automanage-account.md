---
title: Azure Automanage Account
description: Meer informatie over hoe een Automanage-account werkt en hoe u er een maakt.
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 04/07/2021
ms.author: alsin
ms.openlocfilehash: b79e061ae00c42ed2ec2ac39f5653a868f09a15f
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368635"
---
# <a name="automanage-accounts"></a>Accounts automatisch beheren

Het Automanage-account is de identiteit die door de Automanage-service wordt gebruikt om de geautomatiseerde bewerkingen uit te voeren.

Wanneer u Azure Portal in de Azure Portal inschakelen op uw VM's, is er een vervolgkeuzeblade Geavanceerd op de blade **Azure VM best practice** inschakelen waarmee u het Automanage-account kunt toewijzen of handmatig kunt maken.

Aan het Automanage-account  worden zowel de rollen Inzender als Inzender voor **resourcebeleid** toegekend aan de abonnementen met de machines die u onboardt voor Automanage. U kunt hetzelfde Automanage-account gebruiken op computers in meerdere abonnementen,  waarmee de machtigingen Accountbijdrager en Inzender voor **resourcebeleid** voor alle abonnementen worden verleend.

Als uw VM is verbonden met een Log Analytics-werkruimte in een  ander abonnement, wordt aan het Account voor automatisch beheer zowel inzender als inzender voor **resourcebeleid** in dat andere abonnement verleend.

Als u Automanage inschakelen met een nieuw Automanage-account, hebt u  de volgende machtigingen voor uw abonnement **nodig:** Eigenaarrol of Inzender, samen met de rollen Gebruikerstoegangbeheerder. 

Als u Automanage inschakelen met een bestaand Automanage-account, moet u de rol Inzender hebben voor de resourcegroep die uw VM's bevat. 

> [!NOTE]
> Wanneer u Automanage Best Practices uit schakelen, blijven de machtigingen van het account voor alle gekoppelde abonnementen. Verwijder de machtigingen handmatig door naar de IAM-pagina van het abonnement te gaan of het Automanage-account te verwijderen. Het Automanage-account kan niet worden verwijderd als het nog steeds computers beheert.

## <a name="create-an-automanage-account"></a>Een automanage-account maken
U kunt een Automanage-account maken via de portal of met behulp van een ARM-sjabloon.

### <a name="portal"></a>Portal
1. Navigeer naar de blade **Automanage** in de portal
1. Klik **op Inschakelen op bestaande machine**
1. Klik **onder Geavanceerd** op Een nieuw account maken
1. Vul de vereiste velden in en klik op **Maken**

### <a name="arm-template"></a>ARM-sjabloon
Voor het maken van een account voor automatisch gebruik met behulp van een ARM-sjabloon zijn twee stappen vereist:
1. Het Automanage-account maken
1. Geef het account voldoende machtigingen om bewerkingen voor u uit te voeren
    1. U hebt de object-id nodig van het account dat u voor deze stap hebt gemaakt.
        1. Stappen voor het vinden van details van de service-principal van uw account (inclusief de object-id) zijn hier [beschikbaar.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-view-managed-identity-service-principal-portal#view-the-service-principal)
    1. Zodra u de service-principal hebt gevonden, kopieert u de **object-id**. Sla deze op omdat u deze nodig hebt om de onderstaande machtigingen te delegeren.

#### <a name="1-create-automanage-account-does-not-grant-permissions-to-it"></a>1. Automanage Account maken (verleent er geen machtigingen voor)
Als u een Account automatisch wilt maken, moet u de volgende ARM-sjabloon opslaan als `azuredeploy.json` en de onderstaande Azure CLI-opdracht uitvoeren. Als u klaar bent, gaat u verder met de tweede sjabloon hieronder om voldoende machtigingen voor het account te delegeren.

```azurecli-interactive
az deployment group create --resource-group <resource group name> --template-file azuredeploy.json
```

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "automanageAccountName": {
            "type": "String"
        },
        "location": {
            "type": "String"
        }
    },
    "resources": [
        {
            "apiVersion": "2020-06-30-preview",
            "type": "Microsoft.Automanage/accounts",
            "name": "[parameters('automanageAccountName')]",
            "location": "[parameters('location')]",
            "identity": {
                "type": "SystemAssigned"
            }
        }
    ]
}
```
#### <a name="2-grant-permissions-to-the-automanage-account"></a>2. Machtigingen verlenen aan het Automanage-account
Als u voldoende machtigingen wilt verlenen aan het Automanage-account, moet u het volgende doen:
1. Sla de volgende ARM-sjabloon op als `azuredeploy2.json` en voer de onderstaande Azure CLI-opdracht uit.
1. Wanneer u hier om wordt gevraagd, voert u de object-id in van het Automanage-account dat u hebt gemaakt en opgeslagen.

```azurecli-interactive
az deployment group create --resource-group <resource group name> --template-file azuredeploy.json
```
```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
        "contributorRoleDefinitionID": "/providers/Microsoft.Authorization/roledefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
        "resourcePolicyContributorRoleDefinitionID": "/providers/Microsoft.Authorization/roledefinitions/36243c78-bf99-498c-9df9-86d9f8d28608"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2020-04-01-preview",
            "name": "[guid(variables('contributorRoleDefinitionID'))]",
            "properties": {
                "roleDefinitionId": "[variables('contributorRoleDefinitionID')]",
                "principalId": "[parameters('principalId')]"
            }
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2020-04-01-preview",
            "name": "[guid(variables('resourcePolicyContributorRoleDefinitionID'))]",
            "properties": {
                "roleDefinitionId": "[variables('resourcePolicyContributorRoleDefinitionID')]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over Automanage-services voor [Linux](./automanage-linux.md) en [Windows](./automanage-windows-server.md)