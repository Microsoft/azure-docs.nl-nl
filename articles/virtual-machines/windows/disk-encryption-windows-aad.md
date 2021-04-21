---
title: Azure Disk Encryption met Azure AD voor Virtuele Windows-VM's (vorige versie)
description: Dit artikel bevat instructies voor het inschakelen Microsoft Azure schijfversleuteling voor Windows IaaS-VM's.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: how-to
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 081ead434dbdc3b9c348e3fa35068a638bab6e62
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776857"
---
# <a name="azure-disk-encryption-with-azure-ad-for-windows-vms-previous-release"></a>Azure Disk Encryption met Azure AD voor Virtuele Windows-VM's (vorige versie)

**Met de nieuwe versie van Azure Disk Encryption hoeft u geen Azure AD-toepassingsparameter op te geven om VM-schijfversleuteling in te kunnenschakelen. Met de nieuwe release hoeft u geen Azure AD-referenties meer op te geven tijdens de stap Versleuteling inschakelen. Alle nieuwe VM's moeten worden versleuteld zonder de Azure AD-toepassingsparameters met behulp van de nieuwe release. Als u instructies wilt bekijken voor het inschakelen van VM-schijfversleuteling met behulp van de nieuwe versie, Azure Disk Encryption [voor Windows-VMS.](disk-encryption-windows.md) VM's die al zijn versleuteld met Azure AD-toepassingsparameters worden nog steeds ondersteund en moeten worden onderhouden met de AAD-syntaxis.**


U kunt veel schijfversleutelingsscenario's inschakelen en de stappen kunnen variëren afhankelijk van het scenario. In de volgende secties worden de scenario's voor Windows IaaS-VM's uitgebreider behandeld. Voordat u schijfversleuteling kunt gebruiken, [moeten Azure Disk Encryption](disk-encryption-overview-aad.md) zijn voltooid. 


>[!IMPORTANT]
> - U moet [een momentopname maken](snapshot-copy-managed-disk.md) en/of een back-up maken voordat schijven worden versleuteld. Back-ups zorgen ervoor dat een hersteloptie mogelijk is als er een onverwachte fout optreedt tijdens de versleuteling. VM's met beheerde schijven vereisen een back-up voordat versleuteling plaatsvindt. Zodra er een back-up is gemaakt, kunt u de [cmdlet Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) gebruiken om beheerde schijven te versleutelen door de parameter -skipVmBackup op te geven. Zie Back-up en herstel van versleutelde Azure-VM voor meer informatie over het maken van back-up en herstel [van versleutelde VM's.](../../backup/backup-azure-vms-encryption.md) 
>
> - Versleuteling of het uitschakelen van versleuteling kan ertoe leiden dat een VM opnieuw wordt opgestart.

## <a name="enable-encryption-on-new-iaas-vms-created-from-the-marketplace"></a>Versleuteling inschakelen op nieuwe IaaS-VM's die zijn gemaakt op basis van Marketplace
U kunt schijfversleuteling inschakelen op nieuwe IaaS Windows-VM vanuit Marketplace in Azure met behulp van een Resource Manager sjabloon. Met de sjabloon maakt u een nieuwe versleutelde virtuele Windows-VM met behulp van de windows Server 2012-galerie-afbeelding.

1. Klik in [Resource Manager sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)op Implementeren **in Azure.**

2. Selecteer het abonnement, de resourcegroep, de locatie van de resourcegroep, de parameters, de juridische voorwaarden en de overeenkomst. Klik **op Kopen** om een nieuwe IaaS-VM te implementeren waarop versleuteling is ingeschakeld.

3. Nadat u de sjabloon hebt geïmplementeerd, controleert u de versleutelingsstatus van de VM met behulp van de gewenste methode:
     - Controleer dit met de Azure CLI met behulp van [de opdracht az vm encryption show.](/cli/azure/vm/encryption#az_vm_encryption_show) 

         ```azurecli-interactive 
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
         ```

     - Controleer met Azure PowerShell met behulp van de cmdlet [Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 

         ```azurepowershell-interactive
         Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
         ```

     -  Selecteer de VM en klik vervolgens op Schijven onder de kop **Instellingen** om de versleutelingsstatus in de portal te controleren.  In de grafiek **onder Versleuteling** ziet u of deze is ingeschakeld. 
           ![Azure Portal: schijfversleuteling is ingeschakeld](../media/disk-encryption/disk-encryption-fig2.png)

De volgende tabel bevat de Resource Manager sjabloonparameters voor nieuwe VM's uit het Marketplace-scenario met behulp van de Azure AD-client-id:

| Parameter | Beschrijving | 
| --- | --- |
| adminUserName | Gebruikersnaam van beheerder voor de virtuele machine. |
| adminPassword | Beheerderswachtwoord voor de virtuele machine. |
| newStorageAccountName | Naam van het opslagaccount voor het opslaan van besturingssysteem- en gegevens-VHD's. |
| vmSize | Grootte van de VM. Momenteel worden alleen de Standard A-, D- en G-serie ondersteund. |
| virtualNetworkName | Naam van het VNet waar de VM-NIC bij hoort. |
| subnetName | Naam van het subnet in het VNet waar de VM-NIC bij hoort. |
| AADClientID | Client-id van de Azure AD-toepassing die machtigingen heeft om geheimen naar uw sleutelkluis te schrijven. |
| AADClientSecret | Clientgeheim van de Azure AD-toepassing die machtigingen heeft om geheimen naar uw sleutelkluis te schrijven. |
| keyVaultURL | URL van de sleutelkluis waar de BitLocker-sleutel naar moet worden geüpload. U kunt deze krijgen met behulp van de cmdlet `(Get-AzKeyVault -VaultName "MyKeyVault&quot; -ResourceGroupName &quot;MyKeyVaultResourceGroupName").VaultURI` of de Azure CLI `az keyvault show --name "MySecureVault" --query properties.vaultUri` |
| keyEncryptionKeyURL | URL van de sleutelversleutelingssleutel die wordt gebruikt om de gegenereerde BitLocker-sleutel te versleutelen (optioneel). </br> </br>KeyEncryptionKeyURL is een optionele parameter. U kunt uw eigen KEK gebruiken om de gegevensversleutelingssleutel (wachtwoordzingeheim) in uw sleutelkluis verder te beveiligen. |
| keyVaultResourceGroup | Resourcegroep van de sleutelkluis. |
| vmName | Naam van de VM die de versleutelingsbewerking moet uitvoeren. |


## <a name="enable-encryption-on-existing-or-running-iaas-windows-vms"></a><a name="bkmk_RunningWinVM"></a> Versleuteling inschakelen op bestaande of bestaande IaaS Windows-VM's
In dit scenario kunt u versleuteling inschakelen met behulp van een sjabloon, PowerShell-cmdlets of CLI-opdrachten. In de volgende secties wordt gedetailleerder uitgelegd hoe u een Azure Disk Encryption. 


### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a><a name="bkmk_RunningWinVMPSH"></a> Versleuteling inschakelen op bestaande of bestaande VM's met Azure PowerShell 
Gebruik de cmdlet [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) om versleuteling in te stellen op een virtuele IaaS-machine die wordt uitgevoerd in Azure. Zie de blogberichten Explore Azure Disk Encryption with Azure PowerShell - Part 1 (Azure Disk Encryption verkennen met [Azure PowerShell - Deel 1)](/archive/blogs/azuresecurity/explore-azure-disk-encryption-with-azure-powershell) en Explore Azure Disk Encryption with Azure PowerShell - Part 2 (Azure Disk Encryption verkennen met Azure PowerShell - [deel 2)](/archive/blogs/azuresecurity/explore-azure-disk-encryption-with-azure-powershell-part-2)voor informatie over het inschakelen van versleuteling met Azure Disk Encryption.

-  **Een VM die wordt uitgevoerd versleutelen met behulp van een clientgeheim:** Het onderstaande script initialiseert uw variabelen en voert de Set-AzVMDiskEncryptionExtension cmdlet uit. De resourcegroep, VM, sleutelkluis, AAD-app en clientgeheim moeten al zijn gemaakt als vereisten. Vervang MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID en My-AAD-client-secret door uw waarden.
     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **Versleutel een VM die wordt uitgevoerd met behulp van KEK om het clientgeheim in te pakken:** Azure Disk Encryption kunt u een bestaande sleutel in uw sleutelkluis opgeven voor het verpakken van schijfversleutelingsgeheimen die zijn gegenereerd tijdens het inschakelen van versleuteling. Wanneer er een KEK is opgegeven, gebruikt Azure Disk Encryption die sleutel om de versleutelingsgeheimen te verpakken voordat er naar Key Vault wordt geschreven. 

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = ‘MyExtraSecureVM’;
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > De syntaxis voor de waarde van de parameter disk-encryption-keyvault is de volledige id-tekenreeks: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> De syntaxis voor de waarde van de parameter key-encryption-key is de volledige URI voor de KEK, zoals in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Controleer of de schijven zijn versleuteld:** Als u de versleutelingsstatus van een IaaS-VM wilt controleren, gebruikt u de cmdlet [Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Schijfversleuteling uitschakelen:** Als u de versleuteling wilt uitschakelen, gebruikt u de cmdlet [Disable-AzureRmVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-existing-or-running-vms-with--azure-cli"></a><a name="bkmk_RunningWinVMCLI"></a>Versleuteling inschakelen op bestaande of bestaande VM's met Azure CLI
Gebruik de [opdracht az vm encryption enable](/cli/azure/vm/encryption#az_vm_encryption_enable) om versleuteling in teschakelen op een virtuele IaaS-machine die wordt uitgevoerd in Azure.

- **Een VM die wordt uitgevoerd versleutelen met behulp van een clientgeheim:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **Versleutel een VM die wordt uitgevoerd met behulp van KEK om het clientgeheim in te pakken:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > De syntaxis voor de waarde van de parameter disk-encryption-keyvault is de volledige id-tekenreeks: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> De syntaxis voor de waarde van de parameter key-encryption-key is de volledige URI voor de KEK, zoals in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Controleer of de schijven zijn versleuteld:** Als u de versleutelingsstatus van een IaaS-VM wilt controleren, gebruikt u [de opdracht az vm encryption show.](/cli/azure/vm/encryption#az_vm_encryption_show) 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Versleuteling uitschakelen:** Als u versleuteling wilt uitschakelen, gebruikt [u de opdracht az vm encryption disable.](/cli/azure/vm/encryption#az_vm_encryption_disable) 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```


### <a name="using-the-resource-manager-template"></a><a name="bkmk_RunningWinVMwRM"> </a>De sjabloon Resource Manager gebruiken
U kunt schijfversleuteling inschakelen op bestaande of met IaaS Windows-VM's in Azure met behulp van de Resource Manager-sjabloon om een [windows-VM te versleutelen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)die wordt uitgevoerd.


1. Klik in de quickstart-sjabloon van Azure op **Implementeren naar Azure**.

2. Selecteer het abonnement, de resourcegroep, de locatie van de resourcegroep, de parameters, de juridische voorwaarden en de overeenkomst. Klik **op Kopen** om versleuteling in te stellen op de bestaande of bestaande IaaS-VM.

De volgende tabel bevat de Resource Manager sjabloonparameters voor bestaande of bestaande VM's die gebruikmaken van een Azure AD-client-id:

| Parameter | Beschrijving |
| --- | --- |
| AADClientID | Client-id van de Azure AD-toepassing die machtigingen heeft om geheimen naar de sleutelkluis te schrijven. |
| AADClientSecret | Clientgeheim van de Azure AD-toepassing die machtigingen heeft om geheimen naar de sleutelkluis te schrijven. |
| keyVaultName | Naam van de sleutelkluis waar de BitLocker-sleutel naar moet worden geüpload. U kunt deze krijgen met behulp van de cmdlet `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` of de Azure CLI-opdracht `az keyvault list --resource-group "MySecureGroup"`|
|  keyEncryptionKeyURL | URL van de sleutelversleutelingssleutel die wordt gebruikt om de gegenereerde BitLocker-sleutel te versleutelen. Deze parameter is optioneel als u **nokek selecteert** in de vervolgkeuzelijst UseExistingKek. Als u **kek selecteert** in de vervolgkeuzelijst UseExistingKek, moet u de _waarde keyEncryptionKeyURL_ invoeren. |
| volumeType | Type volume dat de versleutelingsbewerking wordt uitgevoerd. Geldige waarden zijn _OS,_ _Data_ en _All._ |
| sequenceVersion | Sequentieversie van de BitLocker-bewerking. Versleuteling van dit versienummer telkens als een schijfversleutelingsbewerking wordt uitgevoerd op dezelfde VM. |
| vmName | Naam van de VM die de versleutelingsbewerking moet uitvoeren. |


## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a><a name="bkmk_VHDpre"> </a>Nieuwe IaaS-VM's die zijn gemaakt van door de klant versleutelde VHD en versleutelingssleutels
In dit scenario kunt u versleuteling inschakelen met behulp van Resource Manager sjabloon, PowerShell-cmdlets of CLI-opdrachten. In de volgende secties worden de Resource Manager en CLI-opdrachten uitgebreider uitgelegd. 

Gebruik de instructies in de bijlage voor het voorbereiden van vooraf versleutelde afbeeldingen die kunnen worden gebruikt in Azure. Nadat de afbeelding is gemaakt, kunt u de stappen in de volgende sectie gebruiken om een versleutelde Azure-VM te maken.

* [Een vooraf versleutelde Windows-VHD voorbereiden](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd)


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a><a name="bkmk_VHDprePSH"></a> VM's met vooraf versleutelde VHD's versleutelen met Azure PowerShell
U kunt schijfversleuteling inschakelen op uw versleutelde VHD met behulp van de PowerShell-cmdlet [Set-AzVMOSDisk.](/powershell/module/az.compute/set-azvmosdisk#examples) In het onderstaande voorbeeld ziet u enkele algemene parameters. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Versleuteling inschakelen op een nieuw toegevoegde gegevensschijf
U kunt [een nieuwe schijf toevoegen aan een Windows-VM met behulp](attach-disk-ps.md)van PowerShell of via de [Azure Portal.](attach-managed-disk-portal.md) 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Versleuteling inschakelen op een nieuw toegevoegde schijf met Azure PowerShell
 Wanneer u PowerShell gebruikt voor het versleutelen van een nieuwe schijf voor Virtuele Windows-VM's, moet een nieuwe reeksversie worden opgegeven. De volgordeversie moet uniek zijn. Met het onderstaande script wordt een GUID voor de reeksversie gegenereerd. In sommige gevallen kan een nieuw toegevoegde gegevensschijf automatisch worden versleuteld door de Azure Disk Encryption extensie. Automatische versleuteling treedt meestal op wanneer de VM opnieuw wordt opgestart nadat de nieuwe schijf online is. Dit wordt meestal veroorzaakt doordat 'Alle' is opgegeven voor het volumetype toen schijfversleuteling eerder op de VM werd gebruikt. Als automatische versleuteling plaatsvindt op een nieuw toegevoegde gegevensschijf, raden we u aan om de cmdlet Set-AzVmDiskEncryptionExtension nieuwe reeksversie opnieuw uit te proberen. Als uw nieuwe gegevensschijf automatisch is versleuteld en u niet wilt worden versleuteld, ontsleutelt u eerst alle stations en versleutelt u vervolgens opnieuw met een nieuwe reeksversie waarin het besturingssysteem voor het volumetype wordt opgegeven. 
 

-  **Versleutel een VM die wordt uitgevoerd met behulp van een clientgeheim:** Het onderstaande script initialiseert uw variabelen en voert de Set-AzVMDiskEncryptionExtension cmdlet uit. De resourcegroep, VM, sleutelkluis, AAD-app en clientgeheim moeten al zijn gemaakt als vereisten. Vervang MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID en My-AAD-client-secret door uw waarden. In dit voorbeeld wordt 'All' gebruikt voor de parameter -VolumeType, die zowel besturingssysteem- als gegevensvolumes bevat. Als u alleen het besturingssysteemvolume wilt versleutelen, gebruikt u 'OS' voor de parameter -VolumeType. 

     ```azurepowershell
      $sequenceVersion = [Guid]::NewGuid();
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'all' –SequenceVersion $sequenceVersion;
    ```
- **Versleutel een VM die wordt uitgevoerd met KEK om het clientgeheim in te pakken:** Azure Disk Encryption kunt u een bestaande sleutel in uw sleutelkluis opgeven om schijfversleutelingsgeheimen te verpakken die zijn gegenereerd tijdens het inschakelen van versleuteling. Wanneer er een KEK is opgegeven, gebruikt Azure Disk Encryption die sleutel om de versleutelingsgeheimen te verpakken voordat er naar Key Vault wordt geschreven. In dit voorbeeld wordt 'All' gebruikt voor de parameter -VolumeType, die zowel besturingssysteem- als gegevensvolumes bevat. Als u alleen het besturingssysteemvolume wilt versleutelen, gebruikt u 'OS' voor de parameter -VolumeType. 

     ```azurepowershell
     $sequenceVersion = [Guid]::NewGuid();
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = 'MyExtraSecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'all' –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > De syntaxis voor de waarde van de parameter disk-encryption-keyvault is de volledige id-tekenreeks: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> De syntaxis voor de waarde van de parameter key-encryption-key is de volledige URI voor de KEK, zoals in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Versleuteling inschakelen op een nieuw toegevoegde schijf met Azure CLI
  De Azure CLI-opdracht biedt automatisch een nieuwe sequentieversie voor u wanneer u de opdracht voor het inschakelen van versleuteling gebruikt. Acceptabele waarden voor de parameter volume-yype zijn Alle, Besturingssysteem en Gegevens. Mogelijk moet u de volumetypeparameter wijzigen in Besturingssysteem of Gegevens als u slechts één type schijf voor de VM versleutelt. In de voorbeelden wordt 'Alle' gebruikt voor de parameter volumetype. 

-  **Een VM die wordt uitgevoerd versleutelen met behulp van een clientgeheim:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Versleutel een VM die wordt uitgevoerd met behulp van KEK om het clientgeheim in te pakken:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "all"
     ```


## <a name="enable-encryption-using-azure-ad-client-certificate-based-authentication"></a>Versleuteling inschakelen met verificatie op basis van azure AD-clientcertificaten.
U kunt clientcertificaatverificatie gebruiken met of zonder KEK. Voordat u de PowerShell-scripts gebruikt, moet u het certificaat al hebben geüpload naar de sleutelkluis en geïmplementeerd op de VM. Als u KEK ook gebruikt, moet de KEK al bestaan. Zie de sectie Verificatie op basis van certificaten voor  [Azure AD](disk-encryption-key-vault-aad.md#certificate-based-authentication-optional) van het artikel vereisten voor meer informatie.


### <a name="enable-encryption-using-certificate-based-authentication-with-azure-powershell"></a>Versleuteling inschakelen met behulp van verificatie op basis van certificaten met Azure PowerShell

```powershell
## Fill in 'MyVirtualMachineResourceGroup', 'MyKeyVaultResourceGroup', 'My-AAD-client-ID', 'MySecureVault, and ‘MySecureVM’.

$VMRGName = 'MyVirtualMachineResourceGroup'
$KVRGname = 'MyKeyVaultResourceGroup';
$AADClientID ='My-AAD-client-ID';
$KeyVaultName = 'MySecureVault';
$VMName = 'MySecureVM';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

# Fill in the certificate path and the password so the thumbprint can be set as a variable. 

$certPath = '$CertPath = "C:\certificates\mycert.pfx';
$CertPassword ='Password'
$Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
$aadClientCertThumbprint = $cert.Thumbprint;

# Enable disk encryption using the client certificate thumbprint

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
```
  
### <a name="enable-encryption-using-certificate-based-authentication-and-a-kek-with-azure-powershell"></a>Versleuteling inschakelen met behulp van verificatie op basis van certificaten en een KEK met Azure PowerShell

```powershell
# Fill in 'MyVirtualMachineResourceGroup', 'MyKeyVaultResourceGroup', 'My-AAD-client-ID', 'MySecureVault,, 'MySecureVM’, and "KEKName.

$VMRGName = 'MyVirtualMachineResourceGroup';
$KVRGname = 'MyKeyVaultResourceGroup';
$AADClientID ='My-AAD-client-ID';
$KeyVaultName = 'MySecureVault';
$VMName = 'MySecureVM';
$keyEncryptionKeyName ='KEKName';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

## Fill in the certificate path and the password so the thumbprint can be read and set as a variable.

$certPath = '$CertPath = "C:\certificates\mycert.pfx';
$CertPassword ='Password'
$Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
$aadClientCertThumbprint = $cert.Thumbprint;

# Enable disk encryption using the client certificate thumbprint and a KEK

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

## <a name="disable-encryption"></a>Versleuteling uitschakelen
U kunt versleuteling uitschakelen Azure PowerShell, de Azure CLI of met een Resource Manager sjabloon. 

- **Schijfversleuteling uitschakelen met Azure PowerShell:** Als u de versleuteling wilt uitschakelen, gebruikt u de cmdlet [Disable-AzureRmVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

- **Versleuteling uitschakelen met de Azure CLI:** Als u versleuteling wilt uitschakelen, gebruikt [u de opdracht az vm encryption disable.](/cli/azure/vm/encryption#az_vm_encryption_disable) 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Versleuteling uitschakelen met een Resource Manager sjabloon:** 

    1. Klik **op Implementeren in Azure in** de sjabloon [Schijfversleuteling uitschakelen bij het uitvoeren van een Windows-VM.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm)
    2. Selecteer het abonnement, de resourcegroep, de locatie, de VM, de juridische voorwaarden en de overeenkomst.
    3.  Klik **op Kopen om** schijfversleuteling uit te schakelen op een windows-VM die wordt uitgevoerd. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Overzicht van Azure Disk Encryption](disk-encryption-overview.md)
