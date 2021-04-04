---
title: Quickstart voor het maken van een Azure Recovery Services-kluis met behulp van een Azure Resource Manager-sjabloon.
description: In deze quickstart leert u hoe u met een sjabloon van Azure Resource Manager (ARM-sjabloon) een Azure Recovery Services-kluis maakt.
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 04/29/2020
ms.openlocfilehash: 62bfe71c397367282c696eecdf55ab9c900c69a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92745873"
---
# <a name="quickstart-create-a-recovery-services-vault-using-an-arm-template"></a>Quickstart: Een Recovery Services-kluis maken met behulp van een ARM-sjabloon

In deze quickstart wordt beschreven hoe u een Recovery Services-kluis instelt met behulp van een Azure Resource Manager-sjabloon (ARM-sjabloon). De service [Azure Site Recovery](site-recovery-overview.md) draagt bij aan uw strategie voor zakelijke continuïteit en noodherstel (BCDR) zodat uw zakelijke apps online blijven tijdens geplande en ongeplande uitval. Site Recovery beheert noodherstel van on-premises machines en virtuele Azure-machines (VM's), met inbegrip van replicatie, failover en herstel.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-recovery-services-vault-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

Als u nog geen actief abonnement op Azure hebt, kunt u een [gratis account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstart-sjablonen](https://azure.microsoft.com/resources/templates/101-recovery-services-vault-create/).

:::code language="json" source="~/quickstart-templates/101-recovery-services-vault-create/azuredeploy.json":::

Er worden twee Azure-resources gedefinieerd in de sjabloon:

- [Microsoft.Recovery Services-kluizen](/azure/templates/microsoft.recoveryservices/vaults): Hiermee wordt de kluis gemaakt.
- [Microsoft.Recovery Services/vaults/backupstorageconfig](/rest/api/backup/backupresourcestorageconfigs): Hiermee configureert u de instellingen voor redundantie van back-ups van de kluis.

De sjabloon bevat optionele parameters voor de back-upconfiguratie van de kluis. De instellingen voor opslagredundantie zijn lokaal redundante opslag (LRS) of geografisch redundante opslag (GRS). Zie [Opslagredundantie instellen](../backup/backup-create-rs-vault.md#set-storage-redundancy) voor meer informatie.

Zie [Azure Quickstart-sjablonen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Recoveryservices&pageNumber=1&sort=Popular)voor meer Azure Recovery Services-sjablonen.

## <a name="deploy-the-template"></a>De sjabloon implementeren

Als u de sjabloon wilt implementeren, zijn de **Abonnement**, **Resourcegroep** en **Kluisnaam** vereist.

1. Selecteer **Implementeren in Azure** om u aan te melden bij Azure en de sjabloon te openen.

   [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-recovery-services-vault-create%2Fazuredeploy.json)

1. Typ of selecteer de volgende waarden:

   :::image type="content" source="media/quickstart-create-vault-template/create-vault-template.png" alt-text="Sjabloon voor het maken van een Recovery Services-kluis.":::

   - **Abonnement**: selecteer uw Azure-abonnement.
   - **Resourcegroep**: Selecteer een bestaande groep of selecteer **Nieuwe maken** om een groep toe te voegen.
   - **Locatie**: wordt standaard ingesteld op de locatie van de resourcegroep en is niet meer beschikbaar nadat u een resourcegroep hebt geselecteerd.
   - **Kluisnaam**: Geef een naam op voor de kluis.
   - **Opslagtype wijzigen**: De standaardinstelling is **onwaar**. Selecteer **waar** alleen als u het opslagtype van de kluis moet wijzigen.
   - **Kluisopslagtype**: De standaardinstelling is **GloballyRedundant**. Als het opslagtype is ingesteld op **waar**, selecteert u **LocallyRedundant**.
   - **Locatie**: de functie `[resourceGroup().location]` is standaard ingesteld op de locatie van de resourcegroep. Als u de locatie wilt wijzigen, voert u een waarde in, zoals **westus**.
   - Schakel het selectievakje **Ik ga akkoord met de bovenstaande voorwaarden** in.

1. Als u de implementatie van de kluis wilt starten, selecteert u de knop **Kopen**. Na een geslaagde implementatie wordt er een melding weergegeven.

   :::image type="content" source="media/quickstart-create-vault-template/deployment-success.png" alt-text="De kluisimplementatie is voltooid.":::

## <a name="validate-the-deployment"></a>De implementatie valideren

Gebruik Azure CLI of Azure PowerShell om te controleren of de kluis is gemaakt.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
echo "Enter the vault name:" &&
read vaultName &&
az backup vault show --name $vaultName --resource-group $resourceGroupName &&
az backup vault backup-properties show --name $vaultName --resource-group $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resouceGroupName = Read-Host -Prompt "Enter the resource group name"
$vaultName = Read-Host -Prompt "Enter the vault name"
$vaultBackupConfig = Get-AzRecoveryServicesVault -Name $vaultName
Get-AzRecoveryServicesVault -ResourceGroupName $resouceGroupName -Name $vaultName
Get-AzRecoveryServicesBackupProperty -Vault $vaultBackupConfig
Write-Host "Press [ENTER] to continue..."
```

---

De volgende uitvoer is een fragment van de gegevens van de kluis:

# <a name="cli"></a>[CLI](#tab/CLI)

```Output
"id": "/subscriptions/<Subscription Id>/resourceGroups/myResourceGroup
         /providers/Microsoft.RecoveryServices/vaults/myVault"
"location": "eastus"
"name": "myVault"
"resourceGroup": "myResourceGroup"

"storageModelType": "GeoRedundant"
"storageType": "GeoRedundant"
"type": "Microsoft.RecoveryServices/vaults/backupstorageconfig"
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```Output
Name              : myVault
Type              : Microsoft.RecoveryServices/vaults
Location          : eastus
ResourceGroupName : myResourceGroup
SubscriptionId    : <Subscription Id>

BackupStorageRedundancy
-----------------------
GeoRedundant
```

---

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent om de nieuwe resources te gebruiken, hoeft u geen actie te ondernemen. Als dat niet het geval is, kunt u de resourcegroep en de kluis verwijderen die in deze quickstart is gemaakt. Gebruik Azure CLI of Azure PowerShell om de resourcegroep en de bijbehorende resources te verwijderen.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resouceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resouceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Recovery Services-kluis gemaakt. Ga naar het volgende quickstart-artikel voor meer informatie over herstel na noodgevallen.

> [!div class="nextstepaction"]
> [Herstel na noodgeval instellen](azure-to-azure-quickstart.md)
