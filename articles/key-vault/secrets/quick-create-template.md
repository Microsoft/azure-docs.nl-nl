---
title: 'Azure-quickstart: een Azure-sleutelkluis en een geheim maken met behulp van een Azure Resource Manager-sjabloon | Microsoft Docs'
description: In deze quickstart ontdekt u hoe u Azure-sleutelkluizen maakt en hoe u hieraan geheimen toevoegt aan de hand van een Azure Resource Manager-sjabloon.
services: key-vault
author: mumian
manager: dougeby
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc,subject-armqs
ms.date: 02/27/2020
ms.author: jgao
ms.openlocfilehash: 214c86eb7272c87e067b1d5f6df0b09ce9e7095c
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814132"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-an-arm-template"></a>Quickstart: Vanuit Azure Key Vault een geheim instellen en ophalen met behulp van een ARM-sjabloon

[Azure Key Vault](../general/overview.md) is een cloudservice die een veilig archief biedt voor geheimen, zoals sleutels, wachtwoorden, certificaten en andere geheimen. Deze quickstart is gericht op het implementeren van een ARM-sjabloon (Azure Resource Manager-sjabloon) voor het maken van een sleutelkluis en een geheim.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van dit artikel, is het volgende vereist:

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

* Uw Azure AD-gebruikersobject-id is in de sjabloon nodig om machtigingen te kunnen configureren. Met de volgende procedure wordt de object-id (GUID) opgehaald.

    1. Voer de volgende Azure PowerShell- of de Azure CLI-opdracht uit door **Proberen** te selecteren en het script vervolgens in het shell-deelvenster te plakken. Plak het script door met de rechtermuisknop op de shell te klikken en **Plakken** te selecteren.

        # <a name="cli"></a>[CLI](#tab/CLI)
        ```azurecli-interactive
        echo "Enter your email address that is used to sign in to Azure:" &&
        read upn &&
        az ad user show --id $upn --query "objectId" &&
        echo "Press [ENTER] to continue ..."
        ```

        # <a name="powershell"></a>[PowerShell](#tab/PowerShell)
        ```azurepowershell-interactive
        $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
        (Get-AzADUser -UserPrincipalName $upn).Id
        Write-Host "Press [ENTER] to continue..."
        ```

        ---

    2. Noteer de object-id. U hebt deze nodig in de volgende sectie van deze quickstart.

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstart-sjablonen](https://azure.microsoft.com/resources/templates/101-key-vault-create/).

:::code language="json" source="~/quickstart-templates/101-key-vault-create/azuredeploy.json":::

Er worden twee Azure-resources gedefinieerd in de sjabloon:

* [**Microsoft.KeyVault/vaults**](/azure/templates/microsoft.keyvault/vaults): een Azure-sleutelkluis maken.
* [**Microsoft.KeyVault/vaults/secrets**](/azure/templates/microsoft.keyvault/vaults/secrets): een sleutelkluisgeheim maken.

In [Azure-quickstart-sjablonen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault&pageNumber=1&sort=Popular) vindt u meer voorbeelden van Azure Key Vault-sjablonen.

## <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer de volgende afbeelding om u aan te melden bij Azure en een sjabloon te openen. Met de sjabloon worden een sleutelkluis en een geheim gemaakt.

    [![Implementeren in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json)

2. Typ of selecteer de volgende waarden.

    ![ARM-sjabloon, Key Vault-integratie, portalimplementatie](../media/quick-create-template/create-key-vault-using-template-portal.png)

    Gebruik de standaardwaarde om de sleutelkluis en een geheim te maken, tenzij er iets anders is aangegeven.

    * **Abonnement**: selecteer een Azure-abonnement.
    * **Resourcegroep**: selecteer **Nieuwe maken**, geef een unieke naam op voor de resourcegroep en klik op **OK**.
    * **Locatie**: selecteer een locatie. Bijvoorbeeld **VS - centraal**.
    * **Key Vault-naam**: voer een naam in voor de sleutelkluis; deze moet wereldwijd uniek zijn binnen de naamruimte .vault.azure.net. U hebt de naam in de volgende sectie nodig wanneer u de implementatie valideert.
    * **Tenant-id**: met de sjabloonfunctie wordt uw tenant-id automatisch opgehaald. Wijzig de standaardwaarde niet.
    * **AD-gebruikers-id**: voer de gebruikersobject-id voor Azure AD in die u hebt opgehaald bij [Vereisten](#prerequisites).
    * **Geheime naam**: voer een naam in voor het geheim dat u opslaat in de sleutelkluis. Bijvoorbeeld **adminpassword**.
    * **Geheime waarde**: voer de geheime waarde in. Als u een wachtwoord opslaat, wordt u aangeraden het gegenereerde wachtwoord te gebruiken dat u hebt gemaakt in Vereisten.
    * **Ik ga akkoord met de bovenstaande voorwaarden**: Selecteren.
3. Selecteer **Aankoop**. Nadat de sleutelkluis is geïmplementeerd, ontvangt u een melding:

    ![ARM-sjabloon, Key Vault-integratie, melding over portalimplementatie](../media/quick-create-template/resource-manager-template-portal-deployment-notification.png)

Voor het implementeren van de sjabloon wordt de Azure-portal gebruikt. Naast Azure Portal kunt u ook de Azure PowerShell, Azure CLI en REST API gebruiken. Zie [Sjablonen implementeren](../../azure-resource-manager/templates/deploy-powershell.md) voor meer informatie over andere implementatiemethoden.

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

U kunt Azure Portal gebruiken om de sleutelkluis en het geheim te controleren of het volgende Azure CLI- of Azure PowerShell-script gebruiken om het gemaakte geheim weer te geven.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter your key vault name:" &&
read keyVaultName &&
az keyvault secret list --vault-name $keyVaultName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$keyVaultName = Read-Host -Prompt "Enter your key vault name"
Get-AzKeyVaultSecret -vaultName $keyVaultName
Write-Host "Press [ENTER] to continue..."
```

---

De uitvoer ziet er ongeveer als volgt uit:

# <a name="cli"></a>[CLI](#tab/CLI)

![Schermopname van de validatie-uitvoer van de portal implementeren in CLI.](../media/quick-create-template/resource-manager-template-portal-deployment-cli-output.png)

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

![ARM-sjabloon, Key Vault-integratie, validatie-uitvoer van de portal implementeren](../media/quick-create-template/resource-manager-template-portal-deployment-powershell-output.png)

---

## <a name="clean-up-resources"></a>Resources opschonen

Andere Key Vault-snelstarts en zelfstudies bouwen voort op deze snelstart. Als u van plan bent om verder te gaan met volgende snelstarts en zelfstudies, kunt u deze resources intact laten.
Als u die niet meer nodig hebt, verwijdert u de resourcegroep. Hierdoor worden ook de sleutelkluis en de gerelateerde resources verwijderd. De resourcegroep verwijderen met behulp van Azure CLI of Azure PowerShell:

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een sleutelkluis en een geheim gemaakt met behulp van een ARM-sjabloon en hebt u de implementatie gevalideerd. Als u meer wilt weten over Key Vault en Azure Resource Manager, vindt u meer informatie in de onderstaande artikelen.

- Lees een [Overzicht van Azure Key Vault](../general/overview.md)
- Meer informatie over [Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- Raadpleeg het [Overzicht voor Key Vault-beveiliging](../general/security-features.md)
