---
title: Azure Disk Encryption met Azure AD voor Windows-Vm's (vorige versie)
description: In dit artikel vindt u instructies voor het inschakelen van Microsoft Azure schijf versleuteling voor Windows IaaS-Vm's.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: how-to
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 3b7f6f63953ba09e57e4586c698e16b9abb8aa1c
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555275"
---
# <a name="azure-disk-encryption-with-azure-ad-for-windows-vms-previous-release"></a>Azure Disk Encryption met Azure AD voor Windows-Vm's (vorige versie)

**De nieuwe versie van Azure Disk Encryption elimineert de vereiste voor het opgeven van een Azure AD-toepassings parameter om VM-schijf versleuteling in te scha kelen. Met de nieuwe versie hoeft u geen Azure AD-referenties meer op te geven tijdens het inschakelen van de versleutelings stap. Alle nieuwe Vm's moeten worden versleuteld zonder de para meters van de Azure AD-toepassing met de nieuwe versie. Zie [Azure Disk Encryption voor Windows-vm's](disk-encryption-windows.md)om instructies te bekijken voor het inschakelen van VM-schijf versleuteling met de nieuwe versie. Vm's die al zijn versleuteld met Azure AD-toepassings parameters, worden nog steeds ondersteund en blijven behouden met de AAD-syntaxis.**


U kunt veel schijf versleutelings scenario's inschakelen en de stappen kunnen variëren afhankelijk van het scenario. In de volgende secties vindt u meer informatie over de scenario's voor Windows IaaS-Vm's. Voordat u schijf versleuteling kunt gebruiken, moeten de [Azure Disk Encryption vereisten](disk-encryption-overview-aad.md) zijn voltooid. 


>[!IMPORTANT]
> - U moet [een moment opname nemen](snapshot-copy-managed-disk.md) en/of een back-up maken voordat schijven worden versleuteld. Back-ups zorgen ervoor dat een herstel optie mogelijk is als er een onverwachte fout optreedt tijdens het versleutelen. Voor Vm's met Managed disks is een back-up vereist voordat versleuteling wordt uitgevoerd. Als er een back-up is gemaakt, kunt u de [cmdlet Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) gebruiken om beheerde schijven te versleutelen door de para meter-skipVmBackup op te geven. Zie [back-up en herstel van versleutelde virtuele Azure](../../backup/backup-azure-vms-encryption.md)-machines voor meer informatie over het maken van back-ups en het herstellen van versleutelde vm's. 
>
> - Het versleutelen of uitschakelen van versleuteling kan ertoe leiden dat een virtuele machine opnieuw wordt opgestart.

## <a name="enable-encryption-on-new-iaas-vms-created-from-the-marketplace"></a>Versleuteling inschakelen voor nieuwe IaaS-Vm's die zijn gemaakt op basis van Marketplace
Met een resource manager-sjabloon kunt u schijf versleuteling op nieuwe IaaS-Windows-VM'S inschakelen vanuit de Marketplace in Azure. De sjabloon maakt een nieuwe versleutelde Windows-VM met behulp van de installatie kopie van de Windows Server 2012-galerie.

1. Klik in de [Resource Manager-sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)op **implementeren naar Azure**.

2. Selecteer het abonnement, de resource groep, de locatie van de resource groep, de para meters, de juridische voor waarden en de overeenkomst. Klik op **kopen** om een nieuwe IaaS-VM te implementeren waarvoor versleuteling is ingeschakeld.

3. Nadat u de sjabloon hebt geïmplementeerd, controleert u de status van de VM-versleuteling met behulp van uw voorkeurs methode:
     - Controleer met de Azure CLI met behulp van de opdracht [AZ VM Encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) . 

         ```azurecli-interactive 
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
         ```

     - Controleer met Azure PowerShell met behulp van de cmdlet [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) . 

         ```azurepowershell-interactive
         Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
         ```

     -  Selecteer de virtuele machine en klik vervolgens op **schijven** onder de kop **instellingen** om de versleutelings status in de portal te controleren. In de grafiek onder **versleuteling** ziet u of deze is ingeschakeld. 
           ![Azure Portal-schijf versleuteling ingeschakeld](../media/disk-encryption/disk-encryption-fig2.png)

De volgende tabel bevat de para meters voor de Resource Manager-sjabloon voor nieuwe Vm's uit het Marketplace-scenario met behulp van de Azure AD-client-ID:

| Parameter | Beschrijving | 
| --- | --- |
| adminUserName | De gebruikers naam van de beheerder voor de virtuele machine. |
| adminPassword | Gebruikers wachtwoord beheerder voor de virtuele machine. |
| newStorageAccountName | De naam van het opslag account voor het opslaan van het besturings systeem en de gegevens-Vhd's. |
| vmSize | Grootte van de virtuele machine. Op dit moment worden alleen de standaard A-, D-en G-serie ondersteund. |
| virtualNetworkName | Naam van het VNet waarvan de VM-NIC deel moet uitmaken. |
| subnetName | Naam van het subnet in het VNet waarvan de VM-NIC deel moet uitmaken. |
| AADClientID | De client-ID van de Azure AD-toepassing met machtigingen voor het schrijven van geheimen naar uw sleutel kluis. |
| AADClientSecret | Client geheim van de Azure AD-toepassing met machtigingen voor het schrijven van geheimen naar uw sleutel kluis. |
| keyVaultURL | URL van de sleutel kluis waarnaar de BitLocker-sleutel moet worden geüpload. U kunt deze ophalen met behulp van de cmdlet `(Get-AzKeyVault -VaultName "MyKeyVault" -ResourceGroupName "MyKeyVaultResourceGroupName").VaultURI` of de Azure cli `az keyvault show --name "MySecureVault" --query properties.vaultUri` |
| keyEncryptionKeyURL | De URL voor de sleutel versleutelings sleutel die wordt gebruikt voor het versleutelen van de gegenereerde BitLocker-sleutel (optioneel). </br> </br>KeyEncryptionKeyURL is een optionele para meter. U kunt uw eigen KEK om de gegevens versleutelings sleutel (geheim wachtwoordzin) in uw sleutel kluis verder te beveiligen. |
| keyVaultResourceGroup | De resource groep van de sleutel kluis. |
| vmName | De naam van de virtuele machine waarop de versleutelings bewerking moet worden uitgevoerd. |


## <a name="enable-encryption-on-existing-or-running-iaas-windows-vms"></a><a name="bkmk_RunningWinVM"></a> Versleuteling inschakelen op bestaande of actieve Windows-Vm's met IaaS
In dit scenario kunt u versleuteling inschakelen met behulp van een sjabloon, Power shell-cmdlets of CLI-opdrachten. In de volgende secties vindt u meer informatie over het inschakelen van Azure Disk Encryption. 


### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a><a name="bkmk_RunningWinVMPSH"></a> Versleuteling inschakelen op bestaande of actieve Vm's met Azure PowerShell 
Gebruik de cmdlet [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) om versleuteling in te scha kelen op een actieve IaaS-virtuele machine in Azure. Voor informatie over het inschakelen van versleuteling met Azure Disk Encryption met behulp van Power shell-cmdlets raadpleegt u de blog berichten [verkennen Azure Disk Encryption met Azure PowerShell-deel 1](/archive/blogs/azuresecurity/explore-azure-disk-encryption-with-azure-powershell) en [bekijkt u Azure Disk Encryption met Azure PowerShell-deel 2](/archive/blogs/azuresecurity/explore-azure-disk-encryption-with-azure-powershell-part-2).

-  **Een actieve VM versleutelen met behulp van een client geheim:** Het onderstaande script initialiseert uw variabelen en voert de Set-AzVMDiskEncryptionExtension-cmdlet uit. De resource groep, de VM, de sleutel kluis, de AAD-app en het client geheim moeten al zijn gemaakt als vereisten. Vervang MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, my-AAD-client-ID en My-AAD-client-Secret door uw waarden.
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
- **Een actieve VM versleutelen met behulp van KEK om het client geheim te laten teruglopen:** Met Azure Disk Encryption kunt u een bestaande sleutel in de sleutel kluis opgeven om schijf versleutelings geheimen te verpakken die zijn gegenereerd tijdens het inschakelen van versleuteling. Wanneer er een KEK is opgegeven, gebruikt Azure Disk Encryption die sleutel om de versleutelingsgeheimen te verpakken voordat er naar Key Vault wordt geschreven. 

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
   > De syntaxis voor de waarde van de para meter voor schijf versleuteling-sleutel kluis is de volledige id-teken reeks:/Subscriptions/[abonnement-ID-GUID]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[sleutel kluis-naam]</br> De syntaxis voor de waarde van de para meter Key-Encryption-Key is de volledige URI naar de KEK zoals in: https://[sleutel kluis-name]. kluis. Azure. net/Keys/[kekname]/[Kek-unique-id] 

- **Controleer of de schijven zijn versleuteld:** Gebruik de cmdlet [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) om de versleutelings status van een IaaS-VM te controleren. 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Schijf versleuteling uitschakelen:** Als u de versleuteling wilt uitschakelen, gebruikt u de cmdlet [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-existing-or-running-vms-with--azure-cli"></a><a name="bkmk_RunningWinVMCLI"></a>Versleuteling inschakelen op bestaande of actieve Vm's met Azure CLI
Gebruik de opdracht [AZ VM Encryption Enable](/cli/azure/vm/encryption#az-vm-encryption-enable) om versleuteling in te scha kelen op een actieve IaaS virtuele machine in Azure.

- **Een actieve VM versleutelen met behulp van een client geheim:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **Een actieve VM versleutelen met behulp van KEK om het client geheim te laten teruglopen:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > De syntaxis voor de waarde van de para meter voor schijf versleuteling-sleutel kluis is de volledige id-teken reeks:/Subscriptions/[abonnement-ID-GUID]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[sleutel kluis-naam] </br> De syntaxis voor de waarde van de para meter Key-Encryption-Key is de volledige URI naar de KEK zoals in: https://[sleutel kluis-name]. kluis. Azure. net/Keys/[kekname]/[Kek-unique-id] 

- **Controleer of de schijven zijn versleuteld:** Als u de versleutelings status van een IaaS-VM wilt controleren, gebruikt u de opdracht [AZ VM Encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) . 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Versleuteling uitschakelen:** Als u versleuteling wilt uitschakelen, gebruikt u de opdracht [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```


### <a name="using-the-resource-manager-template"></a><a name="bkmk_RunningWinVMwRM"> </a>De Resource Manager-sjabloon gebruiken
U kunt schijf versleuteling inschakelen op bestaande of actieve IaaS Windows-Vm's in azure met behulp van de [Resource Manager-sjabloon om een actieve Windows-VM te versleutelen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm).


1. Klik in de quickstart-sjabloon van Azure op **Implementeren naar Azure**.

2. Selecteer het abonnement, de resource groep, de locatie van de resource groep, de para meters, de juridische voor waarden en de overeenkomst. Klik op **aanschaffen** om versleuteling in te scha kelen op de bestaande of actieve IaaS-VM.

De volgende tabel bevat de para meters van Resource Manager-sjablonen voor bestaande of actieve Vm's die gebruikmaken van een Azure AD-client-ID:

| Parameter | Beschrijving |
| --- | --- |
| AADClientID | De client-ID van de Azure AD-toepassing met machtigingen voor het schrijven van geheimen naar de sleutel kluis. |
| AADClientSecret | Client geheim van de Azure AD-toepassing met machtigingen voor het schrijven van geheimen naar de sleutel kluis. |
| keyVaultName | De naam van de sleutel kluis waarnaar de BitLocker-sleutel moet worden geüpload. U kunt deze ophalen met behulp van de cmdlet `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` of de Azure cli-opdracht `az keyvault list --resource-group "MySecureGroup"`|
|  keyEncryptionKeyURL | De URL van de sleutel coderings sleutel die wordt gebruikt om de gegenereerde BitLocker-sleutel te versleutelen. Deze para meter is optioneel als u **nokek** selecteert in de vervolg keuzelijst UseExistingKek. Als u **Kek** selecteert in de vervolg keuzelijst UseExistingKek, moet u de _keyEncryptionKeyURL_ -waarde invoeren. |
| volumeType | Type volume waarop de versleutelings bewerking wordt uitgevoerd. Geldige waarden zijn _besturings systeem_, _gegevens_ en _alle_. |
| sequenceVersion | Sequentie versie van de BitLocker-bewerking. Verhoog dit versie nummer elke keer dat er een schijf versleutelings bewerking wordt uitgevoerd op dezelfde VM. |
| vmName | De naam van de virtuele machine waarop de versleutelings bewerking moet worden uitgevoerd. |


## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a><a name="bkmk_VHDpre"> </a>Nieuwe IaaS-vm's gemaakt op basis van door de klant versleutelde VHD-en versleutelings sleutels
In dit scenario kunt u versleutelen inschakelen met behulp van de Resource Manager-sjabloon, Power shell-cmdlets of CLI-opdrachten. In de volgende secties vindt u meer informatie over de Resource Manager-sjabloon en CLI-opdrachten. 

Volg de instructies in de bijlage voor het voorbereiden van vooraf versleutelde installatie kopieën die kunnen worden gebruikt in Azure. Nadat de installatie kopie is gemaakt, kunt u de stappen in de volgende sectie gebruiken om een versleutelde Azure-VM te maken.

* [Een vooraf versleutelde Windows-VHD voorbereiden](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd)


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a><a name="bkmk_VHDprePSH"></a> Virtuele machines met vooraf versleutelde vhd's versleutelen met Azure PowerShell
U kunt schijf versleuteling op uw versleutelde VHD inschakelen met behulp van de Power shell [-cmdlet Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples). In het onderstaande voor beeld vindt u enkele algemene para meters. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Versleuteling inschakelen op een nieuw toegevoegde gegevens schijf
U kunt [een nieuwe schijf toevoegen aan een Windows-VM met behulp van Power shell](attach-disk-ps.md)of [via de Azure Portal](attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Versleuteling inschakelen op een nieuw toegevoegde schijf met Azure PowerShell
 Wanneer u Power shell gebruikt om een nieuwe schijf voor Windows-Vm's te versleutelen, moet u een nieuwe reeks versie opgeven. De reeks versie moet uniek zijn. In het onderstaande script wordt een GUID voor de reeks versie gegenereerd. In sommige gevallen kan een nieuw toegevoegde gegevens schijf automatisch worden versleuteld door de extensie Azure Disk Encryption. Automatische versleuteling vindt doorgaans plaats wanneer de virtuele machine opnieuw wordt opgestart nadat de nieuwe schijf online is. Dit wordt meestal veroorzaakt doordat ' all ' is opgegeven voor het volume type wanneer schijf versleuteling eerder is uitgevoerd op de virtuele machine. Als Automatische versleuteling op een nieuw toegevoegde gegevens schijf plaatsvindt, raden we u aan om de Set-AzVmDiskEncryptionExtension-cmdlet opnieuw uit te voeren met een nieuwe reeks versie. Als uw nieuwe gegevens schijf automatisch versleuteld is en u niet wilt versleutelen, moet u eerst alle stations ontsleutelen en vervolgens opnieuw versleutelen met een nieuwe reeks versie die het besturings systeem specificeert voor het volume type. 
 

-  **Een actieve VM versleutelen met behulp van een client geheim:** Het onderstaande script initialiseert uw variabelen en voert de Set-AzVMDiskEncryptionExtension-cmdlet uit. De resource groep, de VM, de sleutel kluis, de AAD-app en het client geheim moeten al zijn gemaakt als vereisten. Vervang MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, my-AAD-client-ID en My-AAD-client-Secret door uw waarden. In dit voor beeld wordt ' all ' gebruikt voor de para meter-VolumeType, waarin zowel het besturings systeem als de gegevens volumes zijn opgenomen. Als u alleen het volume van het besturings systeem wilt versleutelen, gebruikt u ' OS ' voor de para meter-VolumeType. 

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
- **Een actieve VM versleutelen met behulp van KEK om het client geheim te laten teruglopen:** Met Azure Disk Encryption kunt u een bestaande sleutel in de sleutel kluis opgeven om schijf versleutelings geheimen te verpakken die zijn gegenereerd tijdens het inschakelen van versleuteling. Wanneer er een KEK is opgegeven, gebruikt Azure Disk Encryption die sleutel om de versleutelingsgeheimen te verpakken voordat er naar Key Vault wordt geschreven. In dit voor beeld wordt ' all ' gebruikt voor de para meter-VolumeType, waarin zowel het besturings systeem als de gegevens volumes zijn opgenomen. Als u alleen het volume van het besturings systeem wilt versleutelen, gebruikt u ' OS ' voor de para meter-VolumeType. 

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
    > De syntaxis voor de waarde van de para meter voor schijf versleuteling-sleutel kluis is de volledige id-teken reeks:/Subscriptions/[abonnement-ID-GUID]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[sleutel kluis-naam]</br> De syntaxis voor de waarde van de para meter Key-Encryption-Key is de volledige URI naar de KEK zoals in: https://[sleutel kluis-name]. kluis. Azure. net/Keys/[kekname]/[Kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Versleuteling inschakelen op een nieuw toegevoegde schijf met Azure CLI
  De Azure CLI-opdracht geeft automatisch een nieuwe reeks versie voor u wanneer u de opdracht uitvoert om versleuteling in te scha kelen. Acceptabele waarden voor de para meter volume-yype zijn alle, het besturings systeem en de gegevens. U moet mogelijk de volume-type parameter wijzigen in besturings systeem of gegevens als u slechts één type schijf versleutelt voor de virtuele machine. De voor beelden gebruiken ' all ' voor de volume-type parameter. 

-  **Een actieve VM versleutelen met behulp van een client geheim:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Een actieve VM versleutelen met behulp van KEK om het client geheim te laten teruglopen:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "all"
     ```


## <a name="enable-encryption-using-azure-ad-client-certificate-based-authentication"></a>Schakel versleuteling in met behulp van verificatie op basis van Azure AD-client certificaten.
U kunt verificatie van client certificaten gebruiken met of zonder KEK. Voordat u de Power shell-scripts gebruikt, moet u het certificaat al hebben geüpload naar de sleutel kluis en op de virtuele machine zijn geïmplementeerd. Als u ook KEK gebruikt, moet de KEK al bestaan. Zie de sectie  [verificatie op basis van certificaten voor Azure AD](disk-encryption-key-vault-aad.md#certificate-based-authentication-optional) van het artikel vereisten voor meer informatie.


### <a name="enable-encryption-using-certificate-based-authentication-with-azure-powershell"></a>Versleuteling inschakelen met verificatie op basis van certificaten met Azure PowerShell

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
  
### <a name="enable-encryption-using-certificate-based-authentication-and-a-kek-with-azure-powershell"></a>Versleuteling inschakelen met verificatie op basis van certificaten en een KEK met Azure PowerShell

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
U kunt versleuteling uitschakelen met behulp van Azure PowerShell, de Azure CLI of met een resource manager-sjabloon. 

- **Schijf versleuteling uitschakelen met Azure PowerShell:** Als u de versleuteling wilt uitschakelen, gebruikt u de cmdlet [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

- **Versleuteling uitschakelen met Azure cli:** Als u versleuteling wilt uitschakelen, gebruikt u de opdracht [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Versleuteling uitschakelen met een resource manager-sjabloon:** 

    1. Klik op **implementeren naar Azure** van de sjabloon [voor het uitvoeren van schijf versleuteling op een Windows-VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm)  .
    2. Selecteer het abonnement, de resource groep, de locatie, de virtuele machine, de juridische voor waarden en de overeenkomst.
    3.  Klik op **aanschaffen** om schijf versleuteling uit te scha kelen op een actieve Windows-VM. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Overzicht van Azure Disk Encryption](disk-encryption-overview.md)
