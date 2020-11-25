---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 3fe622d2ff4f6f8aff546452db0f475cfd44eb1b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015368"
---
## <a name="create-a-resource-group"></a>Een resourcegroep maken

*Als u al een resourcegroep hebt, kunt u verdergaan naar [Een sleutelkluis maken](#create-a-key-vault).*

Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. 

Maak een resourcegroep met behulp van de Azure CLI-opdracht [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create), de Azure PowerShell-opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) of vanuit de [Azure-portal](https://portal.azure.com).

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az group create --name "myResourceGroup" --location eastus
```
### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-key-vault"></a>Een sleutelkluis maken

*Als u al een sleutelkluis hebt, kunt u verdergaan naar [Set key vault advanced access policies](#set-key-vault-advanced-access-policies) (Geavanceerde toegangsbeleidsregels voor sleutelkluizen instellen).*

Maak een sleutelkluis met behulp van de Azure CLI-opdracht [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create), de Azure Powershell-opdracht [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault), de [Azure-portal](https://portal.azure.com) of een [Resource Manager-sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

>[!WARNING]
> Uw sleutelkluis en VM's moeten deel uitmaken van hetzelfde abonnement. Om ervoor te zorgen dat versleutelingsgeheimen geen regionale grenzen overschrijden, vereist Azure Disk Encryption dat de sleutelkluis en de virtuele machines in dezelfde regio staan. Maak en gebruik een sleutelkluis die zich in hetzelfde abonnement en dezelfde regio bevindt als de virtuele machines die moeten worden versleuteld. 

Elke sleutelkluis moet een unieke naam hebben. Vervang <your-unique-keyvault-name> door de naam van uw sleutelkluis in de volgende voorbeelden.

### <a name="azure-cli"></a>Azure CLI

Wanneer u een sleutelkluis maakt met behulp van Azure CLI, voegt u de vlag --enabled-for-disk-encryption toe.

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

### <a name="azure-powershell"></a>Azure PowerShell

Wanneer u een sleutelkluis maakt met behulp van Azure PowerShell, voegt u de vlag -EnabledForDiskEncryption toe.

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "eastus" -EnabledForDiskEncryption
```
### <a name="resource-manager-template"></a>Resource Manager-sjabloon

U kunt ook een sleutelkluis maken met behulp van een [Resource Manager-sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. Klik in de quickstart-sjabloon van Azure op **Implementeren naar Azure**.
2. Selecteer het abonnement, de resourcegroep, de locatie van de resourcegroep, de naam van de sleutelkluis, de object-id, de juridische voorwaarden en de overeenkomst en klik vervolgens op **Aankoop**. 


##  <a name="set-key-vault-advanced-access-policies"></a>Geavanceerd toegangsbeleid voor de sleutelkluis instellen

Het Azure-platform heeft toegang nodig tot de versleutelingssleutels of geheimen in uw sleutelkluis om ze beschikbaar te maken voor de VM voor het opstarten en ontsleutelen van de volumes. 

Als u de sleutelkluis voor schijfversleuteling, implementatie of sjabloonimplementatie niet hebt ingeschakeld tijdens het maken (zoals in de vorige stap is uitgelegd), moet u het bijbehorende geavanceerde toegangsbeleid bijwerken.  

### <a name="azure-cli"></a>Azure CLI

Gebruik [az keyvault update](/cli/azure/keyvault#az-keyvault-update) om schijfversleuteling in te schakelen voor de sleutelkluis. 

 - **Key Vault inschakelen voor schijfversleuteling:** Enabled-for-disk-encryption is vereist. 

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Key Vault inschakelen voor implementatie, indien nodig:** Hiermee kan de resourceprovider Microsoft.Compute geheimen ophalen van deze sleutelkluis wanneer er naar deze sleutelkluis wordt verwezen bij het maken van een resource, bijvoorbeeld een virtuele machine.

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Key Vault inschakelen voor sjabloonimplementatie, indien nodig:** Hiermee kan Resource Manager geheimen uit de kluis ophalen.
     ```azurecli-interactive  
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-template-deployment "true"
     ```

###  <a name="azure-powershell"></a>Azure PowerShell
 Gebruik de PowerShell-cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) voor sleutelkluizen om schijfversleuteling in te schakelen voor de sleutelkluis.

  - **Key Vault inschakelen voor schijfversleuteling:** EnabledForDiskEncryption is vereist voor Azure Disk Encryption.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDiskEncryption
     ```

  - **Key Vault inschakelen voor implementatie, indien nodig:** Hiermee kan de resourceprovider Microsoft.Compute geheimen ophalen van deze sleutelkluis wanneer er naar deze sleutelkluis wordt verwezen bij het maken van een resource, bijvoorbeeld een virtuele machine.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDeployment
     ```

  - **Key Vault inschakelen voor sjabloonimplementatie, indien nodig:** Hiermee kan Azure Resource Manager geheimen van deze sleutelkluis ophalen wanneer er naar deze sleutelkluis wordt verwezen bij een sjabloonimplementatie.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForTemplateDeployment
     ```

### <a name="azure-portal"></a>Azure Portal

1. Selecteer uw sleutelkluis, ga naar **Toegangsbeleid** en **Klik hierop om geavanceerde beleidsregels weer te geven**.
2. Schakel het selectievakje **Toegang tot Azure Disk Encryption voor volumeversleuteling inschakelen** in.
3. Selecteer indien nodig **Toegang tot Azure Virtual Machines inschakelen voor implementatie** en/of **Toegang tot Azure Resource Manager inschakelen voor sjabloonimplementatie**. 
4. Klik op **Opslaan**.

    ![Geavanceerd toegangsbeleid voor Azure Key Vault](../articles/virtual-machines/media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-kek"></a>Een Key Encryption Key (KEK) instellen

Als u een Key Encryption Key (KEK) wilt gebruiken als een extra beveiligingslaag voor versleutelingssleutels, voegt u een KEK toe aan uw sleutelkluis. Wanneer er een KEK is opgegeven, gebruikt Azure Disk Encryption die sleutel om de versleutelingsgeheimen te verpakken voordat er naar Key Vault wordt geschreven.

U kunt een nieuwe KEK genereren met behulp van de Azure CLI-opdracht [az keyvault key create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create), de Azure PowerShell-cmdlet [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) of de [Azure-portal](https://portal.azure.com/). U moet een RSA-sleuteltype genereren. Azure Disk Encryption biedt nog geen ondersteuning voor het gebruik van Elliptic Curve-sleutels.

U kunt in plaats daarvan een KEK importeren uit uw HSM voor on-premises sleutelbeheer. Zie onze [Key Vault-documentatie](../articles/key-vault/keys/hsm-protected-keys.md) voor meer informatie.

De URL's voor uw sleutelkluis-KEK moeten versiebeheer hebben. Azure dwingt deze beperking van versiebeheer af. Zie de volgende voorbeelden voor geldige geheim- en KEK-URL's:

* Voorbeeld van een geldige geheim-URL: *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* Voorbeeld van een geldige KEK-URL: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

Azure Disk Encryption biedt geen ondersteuning voor het opgeven van poortnummers als onderdeel van sleutelkluisgeheimen en KEK-URL's. Voorbeelden van niet-ondersteunde en ondersteunde sleutelkluis-URL's:

  * Acceptabele sleutelkluis-URL: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Onacceptabele sleutelkluis-URL: *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="azure-cli"></a>Azure CLI

Gebruik de Azure CLI-opdracht [eyvault key create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) om een nieuwe KEK te genereren en op te slaan in uw sleutelkluis.

```azurecli-interactive
az keyvault key create --name "myKEK" --vault-name "<your-unique-keyvault-name>" --kty RSA
```

U kunt in plaats daarvan een persoonlijke sleutel importeren met behulp van de Azure CLI-opdracht [az keyvault key import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import):

In beide gevallen geeft u de naam van uw KEK op aan de Azure CLI [az vm encryption enable](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-enable)-parameter --key-encryption-key. 

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>" --key-encryption-key "myKEK"
```

###  <a name="azure-powershell"></a>Azure PowerShell 

Gebruik de Azure PowerShell-cmdlet [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey?view=azps-2.5.0) om een nieuwe KEK te genereren en op te slaan in uw sleutelkluis.

 ```powershell-interactive
Add-AzKeyVaultKey -Name "myKEK" -VaultName "<your-unique-keyvault-name>" -Destination "HSM"
```

U kunt in plaats daarvan een persoonlijke sleutel importeren met behulp van de Azure PowerShell-opdracht [az keyvault key import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import).

In beide gevallen geeft u de id van uw KEK-sleutelkluis en de URL van uw KEK op aan de Azure PowerShell [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.5.0)-parameters -KeyEncryptionKeyVaultId en -KeyEncryptionKeyUrl. Houd er rekening mee dat in dit voorbeeld wordt aangenomen dat u dezelfde sleutelkluis gebruikt voor zowel de schijfversleutelingssleutel als de KEK.

 ```powershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup"
$KEK = Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "myKEK"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyUrl $KEK.Id -SkipVmBackup -VolumeType All
 ```