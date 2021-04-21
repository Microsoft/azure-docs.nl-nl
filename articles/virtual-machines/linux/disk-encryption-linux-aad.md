---
title: Azure Disk Encryption met Azure AD-app Linux IaaS-VM's (vorige versie)
description: Dit artikel bevat instructies voor het inschakelen van Microsoft Azure Disk Encryption voor Linux IaaS-VM's.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 94440b71eee1ff9dcc4a86733582e3e5f57f6a00
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764617"
---
# <a name="enable-azure-disk-encryption-with-azure-ad-on-linux-vms-previous-release"></a>Een Azure Disk Encryption azure ad inschakelen op Linux-VM's (vorige versie)

De nieuwe versie van Azure Disk Encryption elimineert de vereiste voor het opgeven van een Azure Active Directory-toepassingsparameter (Azure AD) voor het inschakelen van VM-schijfversleuteling. Met de nieuwe release hoeft u geen Azure AD-referenties meer op te geven tijdens de stap Versleuteling inschakelen. Alle nieuwe VM's moeten worden versleuteld zonder de azure AD-toepassingsparameters met behulp van de nieuwe release. Zie voor instructies over het inschakelen van VM-schijfversleuteling met behulp van de nieuwe versie Azure Disk Encryption [voor Linux-VMS.](disk-encryption-linux.md) VM's die al zijn versleuteld met Azure AD-toepassingsparameters worden nog steeds ondersteund en moeten worden onderhouden met de AAD-syntaxis.

U kunt veel scenario's voor schijfversleuteling inschakelen en de stappen kunnen per scenario verschillen. In de volgende secties worden de scenario's voor virtuele IaaS-VM's (Infrastructure as a Service) in Linux uitgebreider behandeld. U kunt alleen schijfversleuteling toepassen op virtuele machines met [ondersteunde VM-grootten en besturingssystemen.](disk-encryption-overview.md#supported-vms-and-operating-systems) U moet ook voldoen aan de volgende vereisten:

- [Aanvullende vereisten voor VM's](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Netwerken en groepsbeleid](disk-encryption-overview-aad.md#networking-and-group-policy)
- [Opslagvereisten voor versleutelingssleutels](disk-encryption-overview-aad.md#encryption-key-storage-requirements)

Maak een [momentopname,](snapshot-copy-managed-disk.md)maak een back-up of beide voordat u de schijven versleutelt. Back-ups zorgen ervoor dat een hersteloptie mogelijk is als er een onverwachte fout optreedt tijdens de versleuteling. VM's met beheerde schijven vereisen een back-up voordat versleuteling plaatsvindt. Nadat er een back-up is gemaakt, kunt u de cmdlet Set-AzVMDiskEncryptionExtension gebruiken om beheerde schijven te versleutelen door de parameter -skipVmBackup op te geven. Zie voor meer informatie over het maken van back-up en herstel van versleutelde [VM'Azure Backup.](../../backup/backup-azure-vms-encryption.md) 

>[!WARNING]
 > - Als u eerder een [Azure Disk Encryption de Azure AD-app](disk-encryption-overview-aad.md) hebt gebruikt om deze VM te versleutelen, moet u deze optie blijven gebruiken om uw VM te versleutelen. U kunt Azure Disk Encryption [](disk-encryption-overview.md) niet gebruiken op deze versleutelde VM, omdat dit geen ondersteund scenario is. Dit betekent dat het uitschakelen van de Azure AD-toepassing voor deze versleutelde VM nog niet wordt ondersteund.
 > - Om ervoor te zorgen dat de versleutelingsgeheimen geen regionale grenzen overschrijden, moet Azure Disk Encryption de sleutelkluis en de VM's zich op dezelfde locatie in dezelfde regio bevinden. Maak en gebruik een sleutelkluis in dezelfde regio als de VM die moet worden versleuteld.
 > - Wanneer u Linux-besturingssysteemvolumes versleutelt, kan het proces enkele uren duren. Het is normaal dat het langer duurt om Linux-besturingssysteemvolumes te versleutelen dan gegevensvolumes.
> - Wanneer u Linux-besturingssysteemvolumes versleutelt, moet de VM als niet-beschikbaar worden beschouwd. We raden u ten zeerste aan SSH-aanmeldingen te vermijden terwijl de versleuteling wordt uitgevoerd, om te voorkomen dat geopende bestanden worden geblokkeerd die tijdens het versleutelingsproces moeten worden geopend. Als u de voortgang wilt controleren, gebruikt [u de opdrachten Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) of [vm encryption show.](/cli/azure/vm/encryption#az_vm_encryption_show) U kunt verwachten dat dit proces enkele uren duurt voor een besturingssysteemvolume van 30 GB, plus extra tijd voor het versleutelen van gegevensvolumes. De versleutelingstijd van het gegevensvolume is evenredig met de grootte en hoeveelheid van de gegevensvolumes, tenzij de **versleutelingsindeling** alle optie wordt gebruikt. 
 > - Het uitschakelen van versleuteling op linux-VM's wordt alleen ondersteund voor gegevensvolumes. Het wordt niet ondersteund op gegevens- of besturingssysteemvolumes als het besturingssysteemvolume is versleuteld. 

 

## <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm"></a><a name="bkmk_RunningLinux"></a> Versleuteling inschakelen op een bestaande of bestaande IaaS Linux-VM

In dit scenario kunt u versleuteling inschakelen met behulp van Azure Resource Manager sjabloon, PowerShell-cmdlets of Azure CLI-opdrachten. 

>[!IMPORTANT]
 >Het is verplicht om een momentopname te maken of een back-up te maken van een VM-exemplaar op basis van een beheerde schijf buiten en voordat u de Azure Disk Encryption. U kunt een momentopname van de beheerde schijf maken van de Azure Portal of u kunt [Azure Backup.](../../backup/backup-azure-vms-encryption.md) Back-ups zorgen ervoor dat een hersteloptie mogelijk is in het geval van onverwachte fouten tijdens de versleuteling. Nadat een back-up is gemaakt, gebruikt u de cmdlet Set-AzVMDiskEncryptionExtension beheerde schijven te versleutelen door de parameter -skipVmBackup op te geven. De Set-AzVMDiskEncryptionExtension mislukt voor VM's op basis van beheerde schijven totdat er een back-up wordt gemaakt en deze parameter wordt opgegeven. 
>
>Versleuteling of het uitschakelen van versleuteling kan ertoe leiden dat de VM opnieuw wordt opgestart. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-the-azure-cli"></a><a name="bkmk_RunningLinuxCLI"> </a>Versleuteling inschakelen op een bestaande of linux-VM met behulp van de Azure CLI 
U kunt schijfversleuteling inschakelen op uw versleutelde VHD door het [Azure CLI 2.0-opdrachtregelprogramma](/cli/azure) te installeren en te gebruiken. U kunt PowerShell in uw browser gebruiken met [Azure Cloud Shell](../../cloud-shell/overview.md), of installeren op uw lokale computer en dan gebruiken in een PowerShell-sessie. Gebruik de volgende CLI-opdrachten om versleuteling in te stellen voor bestaande of bestaande virtuele IaaS Linux-VM's in Azure:

Gebruik de [opdracht az vm encryption enable om](/cli/azure/vm/encryption#az_vm_encryption_enable) versleuteling in te stellen op een virtuele IaaS-machine die wordt uitgevoerd in Azure.

-  **Versleutel een VM die wordt uitgevoerd met behulp van een clientgeheim:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Versleutel een VM die wordt uitgevoerd met behulp van KEK om het clientgeheim in te pakken:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

   >[!NOTE]
   > De syntaxis voor de waarde van de parameter disk-encryption-keyvault is de volledige id-tekenreeks: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name].</br> </br> De syntaxis voor de waarde van de parameter key-encryption-key is de volledige URI voor de KEK, zoals in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id].

- **Controleer of de schijven zijn versleuteld:** Gebruik de opdracht [az vm encryption show](/cli/azure/vm/encryption#az_vm_encryption_show) om de versleutelingsstatus van een IaaS-VM te controleren. 

     ```azurecli-interactive
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Versleuteling uitschakelen:** Als u versleuteling wilt uitschakelen, gebruikt [u de opdracht az vm encryption disable.](/cli/azure/vm/encryption#az_vm_encryption_disable) Het uitschakelen van versleuteling is alleen toegestaan op gegevensvolumes voor Linux-VM's.
    
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-powershell"></a><a name="bkmk_RunningLinuxPSH"></a> Versleuteling inschakelen op een bestaande of linux-VM met behulp van PowerShell
Gebruik de cmdlet [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) om versleuteling in te stellen op een virtuele IaaS-machine die wordt uitgevoerd in Azure. Maak een [momentopname](snapshot-copy-managed-disk.md) of maak een back-up van de virtuele [Azure Backup](../../backup/backup-azure-vms-encryption.md) voordat de schijven worden versleuteld. De parameter -skipVmBackup is al opgegeven in de PowerShell-scripts voor het versleutelen van een linux-VM die wordt uitgevoerd.

- **Versleutel een VM die wordt uitgevoerd met behulp van een clientgeheim:** Het volgende script initialiseert uw variabelen en voert de Set-AzVMDiskEncryptionExtension cmdlet uit. De resourcegroep, VM, sleutelkluis, Azure AD-app en clientgeheim moeten al zijn gemaakt als vereisten. Vervang MyVirtualMachineResourceGroup, MyKeyVaultResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID en My-AAD-client-secret door uw waarden. Wijzig de parameter -VolumeType om op te geven welke schijven u wilt versleutelen.

     ```azurepowershell
         $VMRGName = 'MyVirtualMachineResourceGroup';
         $KVRGname = 'MyKeyVaultResourceGroup';
         $vmName = 'MySecureVM';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```
- **Versleutel een VM die wordt uitgevoerd met behulp van KEK om het clientgeheim in te pakken:** Azure Disk Encryption kunt u een bestaande sleutel in uw sleutelkluis opgeven voor het verpakken van schijfversleutelingsgeheimen die zijn gegenereerd tijdens het inschakelen van versleuteling. Wanneer een sleutelversleutelingssleutel is opgegeven, gebruikt Azure Disk Encryption sleutel om de versleutelingsgeheimen te verpakken voordat ze naar de sleutelkluis worden geschreven. Wijzig de parameter -VolumeType om op te geven welke schijven u wilt versleutelen. 

     ```azurepowershell
         $KVRGname = 'MyKeyVaultResourceGroup';
         $VMRGName = 'MyVirtualMachineResourceGroup';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $keyEncryptionKeyName = 'MyKeyEncryptionKey';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```

  >[!NOTE]
  > De syntaxis voor de waarde van de parameter disk-encryption-keyvault is de volledige id-tekenreeks: /subscriptions/[subscription-id-guid]/resourceGroups/[K Vaultsource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name].</br> </br>
  De syntaxis voor de waarde van de parameter key-encryption-key is de volledige URI voor de KEK, zoals in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]. 
    
- **Controleer of de schijven zijn versleuteld:** Als u de versleutelingsstatus van een IaaS-VM wilt controleren, gebruikt u de cmdlet [Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 
    
     ```azurepowershell-interactive 
         Get-AzVmDiskEncryptionStatus -ResourceGroupName MyVirtualMachineResourceGroup -VMName MySecureVM
     ```
    
- **Schijfversleuteling uitschakelen:** Als u de versleuteling wilt uitschakelen, gebruikt u de cmdlet [Disable-AzureRmVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) Het uitschakelen van versleuteling is alleen toegestaan op gegevensvolumes voor Linux-VM's.
     
     ```azurepowershell-interactive 
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```


### <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm-with-a-template"></a><a name="bkmk_RunningLinux"></a> Versleuteling inschakelen op een bestaande of bestaande IaaS Linux-VM met een sjabloon

U kunt schijfversleuteling inschakelen op een bestaande of met IaaS Linux-VM in Azure met behulp van [de Resource Manager sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

1. Selecteer **Implementeren in Azure in** de Azure-quickstart-sjabloon.

2. Selecteer het abonnement, de resourcegroep, de locatie van de resourcegroep, de parameters, de juridische voorwaarden en de overeenkomst. Selecteer **Maken om** versleuteling in te stellen op de bestaande of bestaande IaaS-VM.

De volgende tabel bevat Resource Manager sjabloonparameters voor bestaande of bestaande VM's die gebruikmaken van een Azure AD-client-id:

| Parameter | Beschrijving |
| --- | --- |
| AADClientID | Client-id van de Azure AD-toepassing die machtigingen heeft om geheimen naar de sleutelkluis te schrijven. |
| AADClientSecret | Clientgeheim van de Azure AD-toepassing die machtigingen heeft om geheimen naar uw sleutelkluis te schrijven. |
| keyVaultName | Naam van de sleutelkluis waar de sleutel naar moet worden geüpload. U kunt deze krijgen met behulp van de Azure CLI-opdracht `az keyvault show --name "MySecureVault" --query KVresourceGroup` . |
|  keyEncryptionKeyURL | URL van de sleutelversleutelingssleutel die wordt gebruikt om de gegenereerde sleutel te versleutelen. Deze parameter is optioneel als u **nokek selecteert** in de **vervolgkeuzelijst UseExistingKek.** Als u **kek selecteert** in **de vervolgkeuzelijst UseExistingKek,** moet u de waarde _keyEncryptionKeyURL_ invoeren. |
| volumeType | Type volume dat de versleutelingsbewerking wordt uitgevoerd. Geldige ondersteunde waarden zijn _OS_ of _All._ (Zie ondersteunde Linux-distributies en hun versies voor besturingssysteem- en gegevensschijven in de sectie vereisten eerder.) |
| sequenceVersion | Sequentieversie van de BitLocker-bewerking. Versleuteling van dit versienummer telkens als een schijfversleutelingsbewerking wordt uitgevoerd op dezelfde VM. |
| vmName | Naam van de VM die de versleutelingsbewerking moet uitvoeren. |
| wachtwoordzin | Typ een sterke wachtwoordzin als de gegevensversleutelingssleutel. |



## <a name="use-the-encryptformatall-feature-for-data-disks-on-linux-iaas-vms"></a><a name="bkmk_EFA"> </a>De functie EncryptFormatAll gebruiken voor gegevensschijven op Linux IaaS-VM's
De parameter EncryptFormatAll vermindert de tijd die nodig is om Linux-gegevensschijven te versleutelen. Partities die aan bepaalde criteria voldoen, worden geformatteerd (met hun huidige bestandssysteem). Vervolgens worden ze opnieuw aan de plek waar ze waren vóór de uitvoering van de opdracht. Als u een gegevensschijf wilt uitsluiten die voldoet aan de criteria, kunt u deze ontkoppelen voordat u de opdracht uit te voeren.

 Nadat u deze opdracht hebt uitgevoerd, worden alle stations die eerder zijn bevestigd, geformatteerd. Vervolgens wordt de versleutelingslaag boven op het nu lege station gestart. Wanneer deze optie is geselecteerd, wordt de tijdelijke schijf die is gekoppeld aan de VM ook versleuteld. Als het kortstondige station opnieuw wordt ingesteld, wordt het opnieuw geformatteerd en opnieuw versleuteld voor de VM door de Azure Disk Encryption bij de volgende kans.

>[!WARNING]
> EncryptFormatAll mag niet worden gebruikt wanneer er gegevens nodig zijn op de gegevensvolumes van een VM. U kunt schijven uitsluiten van versleuteling door ze te ontkoppelen. Probeer eerst de parameter EncryptFormatAll uit op een test-VM om de functieparameter en de implicatie ervan te begrijpen voordat u deze op de productie-VM probeert. Met de optie EncryptFormatAll wordt de gegevensschijf opgemaakt, zodat alle gegevens op de schijf verloren gaan. Controleer voordat u verdergaat of alle schijven die u wilt uitsluiten, juist zijn ontkoppeld. </br></br>
 >Als u deze parameter in stelt tijdens het bijwerken van de versleutelingsinstellingen, kan dit leiden tot opnieuw opstarten vóór de daadwerkelijke versleuteling. In dit geval wilt u ook de schijf verwijderen die u niet wilt formatteerd uit het fstab-bestand. Op dezelfde manier moet u de partitie die u wilt versleutelen toevoegen aan het fstab-bestand voordat u de versleutelingsbewerking start. 

### <a name="encryptformatall-criteria"></a><a name="bkmk_EFACriteria"></a> EncryptFormatAlle criteria
De parameter door ondergaat alle partities en versleutelt ze zolang ze voldoen *aan alle* volgende criteria: 
- Is geen root-/OS-/opstartpartitie
- Is nog niet versleuteld
- Is geen BEK-volume
- Is geen RAID-volume
- Is geen LVM-volume
- Is bevestigd

Versleutel de schijven die het RAID- of LVM-volume vormen in plaats van het RAID- of LVM-volume.

### <a name="use-the-encryptformatall-parameter-with-a-template"></a><a name="bkmk_EFATemplate"></a> De parameter EncryptFormatAll gebruiken met een sjabloon
Als u de optie EncryptFormatAll wilt gebruiken, gebruikt u een bestaande Azure Resource Manager-sjabloon die een Linux-VM versleutelt en het veld **EncryptionOperation** voor de AzureDiskEncryption-resource wijzigt.

1. Gebruik bijvoorbeeld de sjabloon Resource Manager om een virtuele [Linux IaaS-VM te versleutelen.](https://github.com/vermashi/azure-quickstart-templates/tree/encrypt-format-running-linux-vm/201-encrypt-running-linux-vm) 
2. Selecteer **Implementeren in Azure in** de Azure-quickstart-sjabloon.
3. Wijzig het **veld EncryptionOperation van** **EnableEncryption** in **EnableEncryptionFormatAl.**
4. Selecteer het abonnement, de resourcegroep, de locatie van de resourcegroep, andere parameters, de juridische voorwaarden en de overeenkomst. Selecteer **Maken om** versleuteling in te stellen op de bestaande of bestaande IaaS-VM.


### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a><a name="bkmk_EFAPSH"></a> De parameter EncryptFormatAll gebruiken met een PowerShell-cmdlet
Gebruik de cmdlet [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) met de parameter EncryptFormatAll.

**Versleutel een VM die wordt uitgevoerd met behulp van een clientgeheim en EncryptFormatAll:** Het volgende script initialiseert bijvoorbeeld uw variabelen en voert de cmdlet Set-AzVMDiskEncryptionExtension uit met de parameter EncryptFormatAll. De resourcegroep, VM, sleutelkluis, Azure AD-app en clientgeheim moeten al zijn gemaakt als vereisten. Vervang MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID en My-AAD-client-secret door uw waarden.
  
   ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup'; 
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
      
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
   ```


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a><a name="bkmk_EFALVM"></a> De parameter EncryptFormatAll gebruiken met Logical Volume Manager (LVM) 
We raden u aan om LVM-on-crypt in te stellen. Vervang voor alle volgende voorbeelden het apparaatpad en de bevestigingspunten door wat het beste past bij uw use-case. Deze installatie kan als volgt worden uitgevoerd:

- Voeg de gegevensschijven toe waaruit de VM wordt opgeslagen.
- Formatteer, bevestig en voeg deze schijven toe aan het fstab-bestand.

    1. Maak de zojuist toegevoegde schijf op. We gebruiken hier symlinks die door Azure worden gegenereerd. Het gebruik van symlinks voorkomt problemen met betrekking tot het wijzigen van apparaatnamen. Zie Problemen met apparaatnamen [oplossen voor meer informatie.](/troubleshoot/azure/virtual-machines/troubleshoot-device-names-problems)
    
        ```console
        mkfs -t ext4 /dev/disk/azure/scsi1/lun0
        ```

    2. De schijven te monteren.

        ```console
        mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint
        ```

    3. Voeg toe aan fstab.

        ```console
        echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab
        ```

    4. Voer de Set-AzVMDiskEncryptionExtension PowerShell-cmdlet uit met -EncryptFormatAll om deze schijven te versleutelen.

       ```azurepowershell-interactive
        Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
        ```

    5. Stel LVM in boven op deze nieuwe schijven. Houd er rekening mee dat de versleutelde stations worden ontgrendeld nadat de VM is opgestart. De LVM-bevestiging moet dus ook later worden vertraagd.




## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a><a name="bkmk_VHDpre"></a> Nieuwe IaaS-VM's die zijn gemaakt van door de klant versleutelde VHD en versleutelingssleutels
In dit scenario kunt u versleuteling inschakelen met behulp van Resource Manager sjabloon, PowerShell-cmdlets of CLI-opdrachten. In de volgende secties worden de sjabloon Resource Manager CLI-opdrachten uitgebreider uitgelegd. 

Gebruik de instructies in de bijlage voor het voorbereiden van vooraf versleutelde afbeeldingen die kunnen worden gebruikt in Azure. Nadat de afbeelding is gemaakt, kunt u de stappen in de volgende sectie gebruiken om een versleutelde Azure-VM te maken.

* [Een vooraf versleutelde Linux-VHD voorbereiden](disk-encryption-sample-scripts.md)

>[!IMPORTANT]
 >Het is verplicht om een momentopname te maken of een back-up te maken van een VM-exemplaar op basis van een beheerde schijf buiten en voordat u een Azure Disk Encryption. U kunt een momentopname van de beheerde schijf maken vanuit de portal of u kunt [Azure Backup](../../backup/backup-azure-vms-encryption.md). Back-ups zorgen ervoor dat een hersteloptie mogelijk is in het geval van onverwachte fouten tijdens de versleuteling. Nadat een back-up is gemaakt, gebruikt u de cmdlet Set-AzVMDiskEncryptionExtension beheerde schijven te versleutelen door de parameter -skipVmBackup op te geven. De Set-AzVMDiskEncryptionExtension mislukt voor VM's op basis van beheerde schijven totdat er een back-up wordt gemaakt en deze parameter wordt opgegeven. 
>
>Versleuteling of het uitschakelen van versleuteling kan ertoe leiden dat de VM opnieuw wordt opgestart.



### <a name="use-azure-powershell-to-encrypt-iaas-vms-with-pre-encrypted-vhds"></a><a name="bkmk_VHDprePSH"></a> Gebruik Azure PowerShell om IaaS-VM's te versleutelen met vooraf versleutelde VHD's 
U kunt schijfversleuteling inschakelen op uw versleutelde VHD met behulp van de PowerShell-cmdlet [Set-AzVMOSDisk.](/powershell/module/az.compute/set-azvmosdisk#examples) In het volgende voorbeeld ziet u enkele algemene parameters. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Versleuteling inschakelen op een nieuw toegevoegde gegevensschijf
U kunt een nieuwe gegevensschijf toevoegen met [az vm disk attach](add-disk.md) of via de [Azure Portal](attach-disk-portal.md). Voordat u kunt versleutelen, moet u eerst de zojuist gekoppelde gegevensschijf koppelen. U moet versleuteling van het gegevensstation aanvragen omdat het station onbruikbaar wordt terwijl de versleuteling wordt uitgevoerd. 

### <a name="enable-encryption-on-a-newly-added-disk-with-the-azure-cli"></a>Versleuteling inschakelen op een nieuw toegevoegde schijf met de Azure CLI
 Als de VM eerder is versleuteld met 'Alle', blijft de parameter --volume-type Alle. Alle omvatten zowel besturingssysteem- als gegevensschijven. Als de VM eerder is versleuteld met het volumetype 'Besturingssysteem', moet de parameter --volume-type worden gewijzigd in Alle, zodat zowel het besturingssysteem als de nieuwe gegevensschijf worden opgenomen. Als de VM is versleuteld met alleen het volumetype 'Gegevens', kan deze gegevens blijven, zoals hier wordt gedemonstreerd. Het toevoegen en koppelen van een nieuwe gegevensschijf aan een VM is niet voldoende voorbereiding voor versleuteling. De zojuist gekoppelde schijf moet ook worden geformatteerd en op de juiste wijze worden gekoppeld in de VM voordat u versleuteling inschakelen. In Linux moet de schijf worden bevestigd in /etc/fstab met de [permanente naam van het blokapparaat.](/troubleshoot/azure/virtual-machines/troubleshoot-device-names-problems) 

In tegenstelling tot de PowerShell-syntaxis hoeft u voor de CLI geen unieke sequentieversie op te geven wanneer u versleuteling inschakelen. De CLI genereert en gebruikt automatisch een eigen unieke sequentieversiewaarde.

-  **Versleutel een VM die wordt uitgevoerd met behulp van een clientgeheim:** 
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Versleutel een VM die wordt uitgevoerd met behulp van KEK om het clientgeheim in te pakken:**

     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Versleuteling inschakelen op een nieuw toegevoegde schijf met Azure PowerShell
 Wanneer u PowerShell gebruikt om een nieuwe schijf voor Linux te versleutelen, moet een nieuwe reeksversie worden opgegeven. De sequentieversie moet uniek zijn. Met het volgende script wordt een GUID voor de sequentieversie gegenereerd. 
 

-  **Versleutel een VM die wordt uitgevoerd met behulp van een clientgeheim:** Het volgende script initialiseert uw variabelen en voert de Set-AzVMDiskEncryptionExtension cmdlet uit. De resourcegroep, de VM, de sleutelkluis, de Azure AD-app en het clientgeheim moeten al zijn gemaakt als vereisten. Vervang MyVirtualMachineResourceGroup, MyKeyVaultResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID en My-AAD-client-secret door uw waarden. De parameter -VolumeType is ingesteld op gegevensschijven en niet op de besturingssysteemschijf. Als de VM eerder is versleuteld met het volumetype 'BESTURINGSSYSTEEM' of 'Alle', moet de parameter -VolumeType worden gewijzigd in Alle, zodat zowel het besturingssysteem als de nieuwe gegevensschijf worden opgenomen.

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
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
     ```
- **Versleutel een VM die wordt uitgevoerd met behulp van KEK om het clientgeheim in te pakken:** Azure Disk Encryption kunt u een bestaande sleutel in uw sleutelkluis opgeven om schijfversleutelingsgeheimen te verpakken die zijn gegenereerd tijdens het inschakelen van versleuteling. Wanneer een sleutelversleutelingssleutel is opgegeven, gebruikt Azure Disk Encryption sleutel om de versleutelingsgeheimen te verpakken voordat ze naar de sleutelkluis worden geschreven. De parameter -VolumeType is ingesteld op gegevensschijven en niet op de besturingssysteemschijf. Als de VM eerder is versleuteld met het volumetype 'BESTURINGSSYSTEEM' of 'Alle', moet de parameter -VolumeType worden gewijzigd in Alle, zodat zowel het besturingssysteem als de nieuwe gegevensschijf worden opgenomen.

     ```azurepowershell
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
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
     ```


>[!NOTE]
> De syntaxis voor de waarde van de parameter disk-encryption-keyvault is de volledige id-tekenreeks: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]. </br> </br>
De syntaxis voor de waarde van de parameter key-encryption-key is de volledige URI voor de KEK, zoals in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id].

## <a name="disable-encryption-for-linux-vms"></a>Versleuteling voor Linux-VM's uitschakelen
U kunt versleuteling uitschakelen met behulp Azure PowerShell, de Azure CLI of een Resource Manager sjabloon. 

>[!IMPORTANT]
>Het uitschakelen van versleuteling Azure Disk Encryption linux-VM's wordt alleen ondersteund voor gegevensvolumes. Het wordt niet ondersteund op gegevens- of besturingssysteemvolumes als het besturingssysteemvolume is versleuteld. 

- **Schijfversleuteling uitschakelen met Azure PowerShell:** Als u versleuteling wilt uitschakelen, gebruikt u de cmdlet [Disable-AzureRmVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **Versleuteling uitschakelen met de Azure CLI:** Als u versleuteling wilt uitschakelen, gebruikt [u de opdracht az vm encryption disable.](/cli/azure/vm/encryption#az_vm_encryption_disable) 
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Versleuteling uitschakelen met een Resource Manager sjabloon:** Als u versleuteling wilt uitschakelen, gebruikt [u de sjabloon Versleuteling uitschakelen op een linux-VM-sjabloon](https://aka.ms/decrypt-linuxvm) die wordt uitgevoerd.
     1. Selecteer **Implementeren in Azure.**
     2. Selecteer het abonnement, de resourcegroep, de locatie, de VM, de juridische voorwaarden en de overeenkomst.
     3. Selecteer **Kopen om** schijfversleuteling uit te schakelen op een windows-VM die wordt uitgevoerd. 


## <a name="next-steps"></a>Volgende stappen

- [Azure Disk Encryption voor Linux](disk-encryption-overview-aad.md)
- [Een sleutelkluis maken en configureren voor Azure Disk Encryption met Azure AD (vorige versie)](disk-encryption-key-vault-aad.md)