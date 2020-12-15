---
title: 'Quickstart: Een Azure Purview-account maken met behulp van Azure PowerShell/Azure CLI (preview)'
description: Deze quickstart laat zien hoe u een Azure Purview-account maakt met Azure PowerShell/Azure CLI.
author: hophan
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: quickstart
ms.date: 11/23/2020
ms.openlocfilehash: d03e343e9158f237ee786ff1b1d06436bdd2d6e7
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96555611"
---
# <a name="quickstart-create-an-azure-purview-account-using-azure-powershellazure-cli"></a>Quickstart: Een Azure Purview-account maken met behulp van Azure PowerShell/Azure CLI

> [!IMPORTANT]
> Azure Purview is momenteel in preview. De [Aanvullende voorwaarden voor gebruik van Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) omvatten aanvullende juridische voorwaarden die van toepassing zijn op Azure-functies die in bèta of preview zijn of die anders nog niet algemeen beschikbaar zijn.

Deze quickstart laat zien hoe u een Azure Purview-account maakt met Azure PowerShell/Azure CLI.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

* Het gebruikersaccount waarmee u zich bij Azure aanmeldt, moet lid zijn van de rol Inzender of Eigenaar, of moet een beheerder van het Azure-abonnement zijn.

* Uw eigen [Azure Active Directory-tenant](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant).

* Installeer Azure PowerShell of Azure CLI op uw clientcomputer om de sjabloon te implementeren: [Implementatie via de opdrachtregel](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-tutorial-create-first-template?tabs=azure-cli#command-line-deployment)

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u met uw Azure-account aan bij [Azure Portal](https://portal.azure.com).

## <a name="configure-your-subscription"></a>Uw abonnement configureren

Voer zo nodig de volgende stappen uit om uw abonnement te configureren zodat Azure Purview kan worden uitgevoerd in uw abonnement:

   1. Zoek en selecteer **Abonnementen** in Azure Portal.

   1. Selecteer in de lijst met abonnementen het abonnement dat u wilt gebruiken. Voor het abonnement zijn beheerdersmachtigingen vereist.

      :::image type="content" source="./media/create-catalog-portal/select-subscription.png" alt-text="Schermopname van het selecteren van een abonnement in Azure Portal.":::

   1. Selecteer **Resourceproviders** voor uw abonnement. Zoek in het deelvenster **Resourceproviders** alle drie de resourceproviders en registreer deze: 
       1. **Microsoft.Purview**
       1. **Microsoft.Storage**
       1. **Microsoft.EventHub** 
      
      Als ze niet zijn geregistreerd, registreert u ze alsnog door **Registreren** te selecteren.

      :::image type="content" source="./media/create-catalog-portal/register-purview-resource-provider.png" alt-text="Schermopname van het registreren van de Microsoft Azure Purview-resourceprovider in Azure Portal.":::

## <a name="create-an-azure-purview-account-instance"></a>Een Azure Purview-account maken

1. Aanmelden met uw Azure-referenties

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    Connect-AzAccount
    ```
    
    # <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
    
    ```azurecli
    az login
    ```
    
    ---

1. Als u meerdere Azure-abonnementen hebt, selecteert u het abonnement dat u wilt gebruiken:

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    Set-AzContext [SubscriptionID/SubscriptionName]
    ```
    
    # <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
    
    ```azurecli
    az account set --subscription [SubscriptionID/SubscriptionName]
    ```
    
    ---

1. Maak een resourcegroep voor uw Purview-account. Als u er al een hebt, kunt u deze stap overslaan:

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzResourceGroup `
      -Name myResourceGroup `
      -Location "East US"
    ```
    
    # <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
    
    ```azurecli
    az group create \
      --name myResourceGroup \
      --location "East US"
    ```
    
    ---

1. Maak een Purview-sjabloonbestand, zoals `purviewtemplate.json`. U kunt `name`, `location` en `capacity` (`4` of `16`) bijwerken:

    ```json
    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [
        {
          "name": "<yourPurviewAccountName>",
          "type": "Microsoft.Purview/accounts",
          "apiVersion": "2020-12-01-preview",
          "location": "EastUs",
          "identity": {
            "type": "SystemAssigned"
          },
          "properties": {
            "networkAcls": {
              "defaultAction": "Allow"
            }
          },
          "dependsOn": [],
          "sku": {
            "name": "Standard",
            "capacity": "4"
          },
          "tags": {}
        }
      ],
      "outputs": {}
    }
    ```

1. Purview-sjabloon implementeren

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzResourceGroupDeployment -ResourceGroupName "<myResourceGroup>" -TemplateFile "<PATH TO purviewtemplate.json>"
    ```
    
    # <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
    
    Als u deze implementatieopdracht wilt uitvoeren, moet u beschikken over de [nieuwste versie](/cli/azure/install-azure-cli) van Azure CLI.
    
    ```azurecli
    az deployment group create --resource-group "<myResourceGroup>" --template-file "<PATH TO purviewtemplate.json>"
    ```
    
    ---

1. De implementatieopdracht retourneert resultaten. Zoek naar `ProvisioningState` om te controleren of de implementatie is geslaagd.
    
## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een Azure Purview-account maakt.

Ga naar het volgende artikel voor informatie over hoe u gebruikers toegang kunt geven tot uw Azure Purview-account. 

> [!div class="nextstepaction"]
> [Gebruikers toevoegen aan uw Azure Purview-account](catalog-permissions.md)