---
title: Azure Disk Encryption-scenario's voor Windows-VM's
description: Dit artikel bevat instructies voor het inschakelen Microsoft Azure schijfversleuteling voor Windows-VM's voor verschillende scenario's
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: how-to
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 2d8173e8b79e8696c2a3e4a7ea722b2947b1aee6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776782"
---
# <a name="azure-disk-encryption-scenarios-on-windows-vms"></a>Azure Disk Encryption-scenario's voor Windows-VM's

Azure Disk Encryption virtuele Windows-machines (VM's) gebruikt de BitLocker-functie van Windows om volledige schijfversleuteling van de besturingssysteemschijf en gegevensschijf te bieden. Daarnaast biedt deze versleuteling van de tijdelijke schijf wanneer de parameter VolumeType Alle is.

Azure Disk Encryption is [geïntegreerd met Azure Key Vault](disk-encryption-key-vault.md) u helpen bij het beheren en beheren van de schijfversleutelingssleutels en -geheimen. Zie Azure Disk Encryption voor [Windows-VM's voor een](disk-encryption-overview.md)overzicht van de service.

U kunt alleen schijfversleuteling toepassen op virtuele machines met [ondersteunde VM-grootten en besturingssystemen.](disk-encryption-overview.md#supported-vms-and-operating-systems) U moet ook voldoen aan de volgende vereisten:

- [Netwerkvereisten](disk-encryption-overview.md#networking-requirements)
- [groepsbeleid vereisten](disk-encryption-overview.md#group-policy-requirements)
- [Opslagvereisten voor versleutelingssleutels](disk-encryption-overview.md#encryption-key-storage-requirements)

>[!IMPORTANT]
> - Als u eerder een Azure Disk Encryption met Azure AD hebt gebruikt om een VM te versleutelen, moet u deze optie blijven gebruiken om uw VM te versleutelen. Zie [Azure Disk Encryption met Azure AD (vorige versie) voor](disk-encryption-overview-aad.md) meer informatie. 
>
> - U moet [een momentopname maken](snapshot-copy-managed-disk.md) en/of een back-up maken voordat schijven worden versleuteld. Back-ups zorgen ervoor dat een hersteloptie mogelijk is als er een onverwachte fout optreedt tijdens de versleuteling. VM's met beheerde schijven vereisen een back-up voordat versleuteling plaatsvindt. Zodra er een back-up is gemaakt, kunt u de [cmdlet Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) gebruiken om beheerde schijven te versleutelen door de parameter -skipVmBackup op te geven. Zie Back-up en herstel van versleutelde Azure-VM voor meer informatie over het maken van back-up en herstel [van versleutelde VM's.](../../backup/backup-azure-vms-encryption.md) 
>
> - Versleuteling of het uitschakelen van versleuteling kan ertoe leiden dat een VM opnieuw wordt opgestart.

## <a name="install-tools-and-connect-to-azure"></a>Hulpprogramma's installeren en verbinding maken met Azure

[!INCLUDE [disk-encryption-install-cli-powershell](../../../includes/disk-encryption-install-cli-powershell.md)]

## <a name="enable-encryption-on-an-existing-or-running-windows-vm"></a>Versleuteling inschakelen op een bestaande of bestaande Windows-VM
In dit scenario kunt u versleuteling inschakelen met behulp van Resource Manager sjabloon, PowerShell-cmdlets of CLI-opdrachten. Als u schemagegevens voor de extensie van de virtuele machine nodig hebt, zie Azure Disk Encryption [voor Windows-extensies.](../extensions/azure-disk-enc-windows.md)

### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a>Versleuteling inschakelen op bestaande of bestaande VM's met Azure PowerShell 
Gebruik de cmdlet [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) om versleuteling in teschakelen op een virtuele IaaS-machine die wordt uitgevoerd in Azure. 

-  **Een VM versleutelen die wordt uitgevoerd:** Het onderstaande script initialiseert uw variabelen en voert de Set-AzVMDiskEncryptionExtension cmdlet uit. De resourcegroep, VM en sleutelkluis moeten al zijn gemaakt als vereisten. Vervang MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM en MySecureVault door uw waarden.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **Een VM die wordt uitgevoerd versleutelen met behulp van KEK:** 

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > De syntaxis voor de waarde van de parameter disk-encryption-keyvault is de volledige id-tekenreeks: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> De syntaxis voor de waarde van de parameter key-encryption-key is de volledige URI voor de KEK, zoals in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Controleer of de schijven zijn versleuteld:** Als u de versleutelingsstatus van een IaaS-VM wilt controleren, gebruikt u de cmdlet [Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Schijfversleuteling uitschakelen:** Als u de versleuteling wilt uitschakelen, gebruikt u de cmdlet [Disable-AzVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) Het uitschakelen van gegevensschijfversleuteling voor de Windows-VM wanneer zowel de besturingssysteem als gegevensschijven zijn versleuteld, werkt niet zoals verwacht. Schakel in plaats daarvan versleuteling op alle schijven uit.

     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-existing-or-running-vms-with-the-azure-cli"></a>Versleuteling inschakelen voor bestaande of bestaande VM's met de Azure CLI
Gebruik de [opdracht az vm encryption enable](/cli/azure/vm/encryption#az_vm_encryption_enable) om versleuteling in teschakelen op een virtuele IaaS-machine die wordt uitgevoerd in Azure.

- **Een VM versleutelen die wordt uitgevoerd:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **Een VM die wordt uitgevoerd versleutelen met behulp van KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > De syntaxis voor de waarde van de parameter disk-encryption-keyvault is de volledige id-tekenreeks: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> De syntaxis voor de waarde van de parameter key-encryption-key is de volledige URI voor de KEK, zoals in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Controleer of de schijven zijn versleuteld:** Als u de versleutelingsstatus van een IaaS-VM wilt controleren, gebruikt u [de opdracht az vm encryption show.](/cli/azure/vm/encryption#az_vm_encryption_show) 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Versleuteling uitschakelen:** Als u versleuteling wilt uitschakelen, gebruikt [u de opdracht az vm encryption disable.](/cli/azure/vm/encryption#az_vm_encryption_disable) Het uitschakelen van gegevensschijfversleuteling voor de Windows-VM wanneer zowel de besturingssysteem als gegevensschijven zijn versleuteld, werkt niet zoals verwacht. Schakel in plaats daarvan versleuteling op alle schijven uit.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```

### <a name="using-the-resource-manager-template"></a>De sjabloon Resource Manager gebruiken

U kunt schijfversleuteling inschakelen op bestaande of bestaande IaaS Windows-VM's in Azure met behulp van de Resource Manager-sjabloon om een virtuele [Windows-computer te versleutelen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)die wordt uitgevoerd.


1. Klik in de quickstart-sjabloon van Azure op **Implementeren naar Azure**.

2. Selecteer het abonnement, de resourcegroep, de locatie, de instellingen, de juridische voorwaarden en de overeenkomst. Klik **op Kopen** om versleuteling in te stellen op de bestaande of draaiende IaaS-VM.

De volgende tabel bevat de Resource Manager sjabloonparameters voor bestaande of bestaande VM's:

| Parameter | Beschrijving |
| --- | --- |
| vmName | Naam van de VM om de versleutelingsbewerking uit te voeren. |
| keyVaultName | Naam van de sleutelkluis waar de BitLocker-sleutel naar moet worden geüpload. U kunt deze krijgen met behulp van de cmdlet `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` of de Azure CLI-opdracht `az keyvault list --resource-group "MyKeyVaultResourceGroup"`|
| keyVaultResourceGroup | Naam van de resourcegroep die de sleutelkluis bevat|
|  keyEncryptionKeyURL | De URL van de sleutelversleutelingssleutel, in de indeling https:// &lt; keyvault-name &gt; .vault.azure.net/key/ &lt; key-name &gt; . Als u geen KEK wilt gebruiken, laat u dit veld leeg. |
| volumeType | Type volume dat de versleutelingsbewerking wordt uitgevoerd. Geldige waarden zijn _OS_, _Data_ en _All._ 
| forceUpdateTag | Geef elke keer dat de bewerking geforceerd moet worden uitgevoerd een unieke waarde door, zoals een GUID. |
| resizeOSDisk | Moet de grootte van de besturingssysteempartitie worden geseed om de volledige VHD van het besturingssysteem in beslag te nemen voordat u het systeemvolume splitst. |
| location | Locatie voor alle resources. |

## <a name="enable-encryption-on-nvme-disks-for-lsv2-vms"></a>Versleuteling op NVMe-schijven inschakelen voor Lsv2-VM's

In dit scenario wordt beschreven hoe Azure Disk Encryption inschakelen op NVMe-schijven voor VM's uit de Lsv2-serie.  De Lsv2-serie bevat lokale NVMe-opslag. Lokale NVMe-schijven zijn tijdelijk en er gaan gegevens verloren op deze schijven als u de VM stopt of de toewijzing van de VM in de [Lsv2-serie opeenlaat.](../lsv2-series.md)

Versleuteling op NVMe-schijven inschakelen:

1. Initialiseer de NVMe-schijven en maak NTFS-volumes.
1. Schakel versleuteling op de VM in met de parameter VolumeType ingesteld op Alle. Hiermee wordt versleuteling ingeschakeld voor alle besturingssysteem- en gegevensschijven, inclusief volumes die worden gebacked door NVMe-schijven. Zie Versleuteling [inschakelen op een bestaande of met Windows-VM voor meer informatie.](#enable-encryption-on-an-existing-or-running-windows-vm)

Versleuteling blijft in de volgende scenario's op de NVMe-schijven bestaan:
- VM opnieuw opstarten
- Nieuweimage voor virtuele-machineschaalset
- Besturingssysteem wisselen

NVMe-schijven worden in de volgende scenario's niet-ge uninitialiseerd:

- VM starten na de toewijzing
- Serviceherstel
- Backup

In deze scenario's moeten de NVMe-schijven worden initialiseren nadat de VM is gestart. Als u versleuteling op de NVMe-schijven wilt inschakelen, moet u de opdracht uitvoeren om de Azure Disk Encryption weer in te stellen nadat de NVMe-schijven zijn initialiseerd.

Naast de scenario's die [](#unsupported-scenarios) worden vermeld in de sectie Niet-ondersteunde scenario's, wordt versleuteling van NVMe-schijven niet ondersteund voor:

- VM's die zijn versleuteld Azure Disk Encryption met AAD (vorige versie)
- NVMe-schijven met opslagruimten
- Azure Site Recovery van SKU's met NVMe-schijven (zie Ondersteuningsmatrix voor herstel na noodherstel van [Azure-VM's](../../site-recovery/azure-to-azure-support-matrix.md#replicated-machines---storage)tussen Azure-regio's: Gerepliceerde machines - opslag).

## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Nieuwe IaaS-VM's die zijn gemaakt van door de klant versleutelde VHD en versleutelingssleutels

In dit scenario kunt u een nieuwe VM maken op basis van een vooraf versleutelde VHD en de bijbehorende versleutelingssleutels met behulp van PowerShell-cmdlets of CLI-opdrachten. 

Gebruik de instructies in [Een vooraf versleutelde Windows-VHD voorbereiden.](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd) Nadat de afbeelding is gemaakt, kunt u de stappen in de volgende sectie gebruiken om een versleutelde Azure-VM te maken.


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a>VM's met vooraf versleutelde VHD's versleutelen met Azure PowerShell
U kunt schijfversleuteling inschakelen op uw versleutelde VHD met behulp van de PowerShell-cmdlet [Set-AzVMOSDisk.](/powershell/module/az.compute/set-azvmosdisk#examples) In het onderstaande voorbeeld ziet u enkele algemene parameters. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Versleuteling inschakelen op een nieuw toegevoegde gegevensschijf
U kunt [een nieuwe schijf toevoegen aan een Windows-VM met behulp](attach-disk-ps.md)van PowerShell of via de [Azure Portal.](attach-managed-disk-portal.md) 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Versleuteling inschakelen op een nieuw toegevoegde schijf met Azure PowerShell
 Wanneer u PowerShell gebruikt voor het versleutelen van een nieuwe schijf voor Windows-VM's, moet een nieuwe reeksversie worden opgegeven. De sequentieversie moet uniek zijn. Met het onderstaande script wordt een GUID voor de reeksversie gegenereerd. In sommige gevallen kan een nieuw toegevoegde gegevensschijf automatisch worden versleuteld door de Azure Disk Encryption extensie. Automatische versleuteling treedt meestal op wanneer de VM opnieuw wordt opgestart nadat de nieuwe schijf online is. Dit wordt meestal veroorzaakt doordat 'Alles' is opgegeven voor het volumetype toen schijfversleuteling eerder op de virtueleM werd gebruikt. Als automatische versleuteling plaatsvindt op een nieuw toegevoegde gegevensschijf, raden we u aan de cmdlet Set-AzVmDiskEncryptionExtension met een nieuwe reeksversie opnieuw uit te werken. Als uw nieuwe gegevensschijf automatisch is versleuteld en u niet wilt worden versleuteld, ontsleutelt u eerst alle stations en versleutelt u vervolgens opnieuw met een nieuwe reeksversie waarin het besturingssysteem voor het volumetype wordt opgegeven. 
  
 

-  **Een VM versleutelen die wordt uitgevoerd:** Het onderstaande script initialiseert uw variabelen en voert de Set-AzVMDiskEncryptionExtension cmdlet uit. De resourcegroep, VM en sleutelkluis moeten al zijn gemaakt als vereisten. Vervang MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM en MySecureVault door uw waarden. In dit voorbeeld wordt 'Alle' gebruikt voor de parameter -VolumeType, die zowel besturingssysteem- als gegevensvolumes bevat. Als u alleen het besturingssysteemvolume wilt versleutelen, gebruikt u 'OS' voor de parameter -VolumeType. 

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;
    ```
- **Een VM die wordt uitgevoerd versleutelen met behulp van KEK:** In dit voorbeeld wordt 'Alle' gebruikt voor de parameter -VolumeType, die zowel besturingssysteem- als gegevensvolumes bevat. Als u alleen het besturingssysteemvolume wilt versleutelen, gebruikt u 'OS' voor de parameter -VolumeType.

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     $sequenceVersion = [Guid]::NewGuid();

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > De syntaxis voor de waarde van de parameter disk-encryption-keyvault is de volledige id-tekenreeks: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> De syntaxis voor de waarde van de parameter key-encryption-key is de volledige URI voor de KEK, zoals in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Versleuteling inschakelen op een nieuw toegevoegde schijf met Azure CLI
 De Azure CLI-opdracht biedt automatisch een nieuwe sequentieversie voor u wanneer u de opdracht voor het inschakelen van versleuteling gebruikt. In het voorbeeld wordt 'Alle' gebruikt voor de parameter volumetype. Mogelijk moet u de parameter volumetype wijzigen in besturingssysteem als u alleen de besturingssysteemschijf versleutelt. In tegenstelling tot de PowerShell-syntaxis hoeft de GEBRUIKER geen unieke reeksversie op te geven bij het inschakelen van versleuteling. De CLI genereert en gebruikt automatisch een eigen unieke reeksversiewaarde.   

-  **Een VM versleutelen die wordt uitgevoerd:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Een VM die wordt uitgevoerd versleutelen met behulp van KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "All"
     ```


## <a name="disable-encryption"></a>Versleuteling uitschakelen
[!INCLUDE [disk-encryption-disable-encryption-powershell](../../../includes/disk-encryption-disable-powershell.md)]

## <a name="unsupported-scenarios"></a>Niet-ondersteunde scenario's

Azure Disk Encryption werkt niet voor de volgende scenario's, functies en technologie:

- Versleuteling van VM's in de basic-laag of VM's die zijn gemaakt via de klassieke methode voor het maken van VM's.
- VM's versleutelen die zijn geconfigureerd met RAID-systemen op basis van software.
- VM's versleutelen die zijn geconfigureerd met Opslagruimten Direct (S2D) of Windows Server-versies vóór 2016 geconfigureerd met Windows Storage Spaces.
- Integratie met een on-premises sleutelbeheersysteem.
- Azure Files (gedeeld bestandssysteem).
- Network File System (NFS).
- Dynamische volumes.
- Windows Server-containers, die dynamische volumes voor elke container maken.
- Kortstondige besturingssysteemschijven.
- Versleuteling van gedeelde/gedistribueerde bestandssystemen zoals (maar niet beperkt tot) DFS, GFS, DRDB en CephFS.
- Een versleutelde VM verplaatsen naar een ander abonnement of een andere regio.
- Een afbeelding of momentopname van een versleutelde VM maken en deze gebruiken om extra VM's te implementeren.
- VM's uit de M-serie Write Accelerator schijven.
- ADE toepassen op een VM met schijven die zijn versleuteld met versleuteling aan [de serverzijde](../disk-encryption.md) met door de klant beheerde sleutels (SSE + CMK). Het toepassen van SSE + CMK op een gegevensschijf op een VM die is versleuteld met ADE is ook een niet-ondersteund scenario.
- Het migreren van een VM die is versleuteld  met ADE of die ooit is versleuteld met ADE, naar versleuteling aan de serverzijde met door de klant [beheerde sleutels](../disk-encryption.md).
- VM's versleutelen in failoverclusters.
- Versleuteling [van Azure Ultra Disks.](../disks-enable-ultra-ssd.md)

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure Disk Encryption](disk-encryption-overview.md)
- [Voorbeeldscripts voor Azure Disk Encryption](disk-encryption-sample-scripts.md)
- [Problemen met Azure Disk Encryption oplossen](disk-encryption-troubleshooting.md)