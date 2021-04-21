---
title: Azure Disk Encryption-scenario's voor virtuele Linux-machines
description: Dit artikel bevat instructies voor het inschakelen Microsoft Azure schijfversleuteling voor Linux-VM's voor verschillende scenario's
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: f014c07a319cbb07497cba01699b93d092255b93
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771498"
---
# <a name="azure-disk-encryption-scenarios-on-linux-vms"></a>Azure Disk Encryption-scenario's voor virtuele Linux-machines

Azure Disk Encryption virtuele Linux-machines (VM's) maakt gebruik van de DM-Crypt-functie van Linux voor volledige schijfversleuteling van de besturingssysteemschijf en gegevensschijven. Daarnaast wordt de tijdelijke schijf versleuteld wanneer de functie EncryptFormatAll wordt gebruikt.

Azure Disk Encryption is [geïntegreerd met Azure Key Vault](disk-encryption-key-vault.md) u helpen bij het beheren en beheren van de schijfversleutelingssleutels en -geheimen. Zie voor een overzicht van de service Azure Disk Encryption [linux-VM's.](disk-encryption-overview.md)

U kunt alleen schijfversleuteling toepassen op virtuele machines met [ondersteunde VM-grootten en besturingssystemen.](disk-encryption-overview.md#supported-vms-and-operating-systems) U moet ook voldoen aan de volgende vereisten:

- [Aanvullende vereisten voor VM's](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Netwerkvereisten](disk-encryption-overview.md#networking-requirements)
- [Opslagvereisten voor versleutelingssleutels](disk-encryption-overview.md#encryption-key-storage-requirements)

In alle gevallen moet u een [momentopname maken en/of](snapshot-copy-managed-disk.md) een back-up maken voordat schijven worden versleuteld. Back-ups zorgen ervoor dat een hersteloptie mogelijk is als er een onverwachte fout optreedt tijdens de versleuteling. VM's met beheerde schijven vereisen een back-up voordat versleuteling plaatsvindt. Zodra er een back-up is gemaakt, kunt u de [cmdlet Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) gebruiken om beheerde schijven te versleutelen door de parameter -skipVmBackup op te geven. Zie het artikel over het maken van back-up en herstel van versleutelde VM's [Azure Backup](../../backup/backup-azure-vms-encryption.md) informatie. 

>[!WARNING]
> - Als u eerder een Azure Disk Encryption met Azure AD hebt gebruikt om een VM te versleutelen, moet u deze optie blijven gebruiken om uw VM te versleutelen. Zie [Azure Disk Encryption met Azure AD (vorige versie) voor](disk-encryption-overview-aad.md) meer informatie. 
>
> - Bij het versleutelen van Linux-besturingssysteemvolumes moet de VM als niet-beschikbaar worden beschouwd. We raden u ten zeerste aan om SSH-aanmeldingen te vermijden terwijl de versleuteling wordt uitgevoerd, om te voorkomen dat er problemen zijn met het blokkeren van geopende bestanden die tijdens het versleutelingsproces moeten worden geopend. Als u de voortgang wilt controleren, gebruikt u de PowerShell-cmdlet [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) of de CLI-opdracht [VM-versleuteling tonen.](/cli/azure/vm/encryption#az_vm_encryption_show) Dit proces duurt naar verwachting enkele uren voor een besturingssysteemvolume van 30 GB, plus extra tijd voor het versleutelen van gegevensvolumes. De versleutelingstijd van het gegevensvolume is evenredig met de grootte en hoeveelheid van de gegevensvolumes, tenzij de versleutelingsindeling alle opties worden gebruikt. 
> - Het uitschakelen van versleuteling op linux-VM's wordt alleen ondersteund voor gegevensvolumes. Het wordt niet ondersteund op gegevens- of besturingssysteemvolumes als het besturingssysteemvolume is versleuteld.  

## <a name="install-tools-and-connect-to-azure"></a>Hulpprogramma's installeren en verbinding maken met Azure

Azure Disk Encryption kunnen worden ingeschakeld en beheerd via [de Azure CLI](/cli/azure) en [Azure PowerShell](/powershell/azure/new-azureps-module-az). Als u dit wilt doen, moet u de hulpprogramma's lokaal installeren en verbinding maken met uw Azure-abonnement.

### <a name="azure-cli"></a>Azure CLI

De [Azure CLI 2.0](/cli/azure) is een opdrachtregelprogramma voor het beheren van Azure-resources. De CLI is ontworpen om flexibel query's uit te voeren op gegevens, langlopende bewerkingen als niet-blokkerende processen te ondersteunen en scripts eenvoudig te maken. U kunt deze lokaal installeren door de stappen te volgen in [Azure CLI installeren.](/cli/azure/install-azure-cli)

 

Gebruik de opdracht [az login](/cli/azure/reference-index#az_login) om u aan te melden bij uw [Azure-account](/cli/azure/authenticate-azure-cli)met de Azure CLI.

```azurecli
az login
```

Als u een tenant wilt selecteren om u aan te melden, gebruikt u:
    
```azurecli
az login --tenant <tenant>
```

Als u meerdere abonnementen hebt en een specifiek abonnement wilt opgeven, haal dan uw abonnementslijst op met [az account list](/cli/azure/account#az_account_list) en geef op met az account [set](/cli/azure/account#az_account_set).
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

Zie Aan de slag met [Azure CLI 2.0 voor meer informatie.](/cli/azure/get-started-with-azure-cli) 

### <a name="azure-powershell"></a>Azure PowerShell
De [Azure PowerShell az module](/powershell/azure/new-azureps-module-az) biedt een set cmdlets die gebruikmaakt van het [Azure Resource Manager-model](../../azure-resource-manager/management/overview.md) voor het beheren van uw Azure-resources. U kunt deze in uw browser gebruiken [met Azure Cloud Shell](../../cloud-shell/overview.md)of u kunt deze installeren op uw lokale computer met behulp van de instructies in De module [Azure PowerShell installeren.](/powershell/azure/install-az-ps) 

Als u de SDK al lokaal hebt geïnstalleerd, moet u ervoor zorgen dat u de nieuwste versie van Azure PowerShell SDK gebruikt om deze te Azure Disk Encryption. Download de nieuwste versie van [Azure PowerShell release](https://github.com/Azure/azure-powershell/releases).

Als [u zich wilt aanmelden bij uw Azure-account Azure PowerShell](/powershell/azure/authenticate-azureps), gebruikt u de cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount)

```powershell
Connect-AzAccount
```

Als u meerdere abonnementen hebt en er een wilt opgeven, gebruikt u de cmdlet [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) om deze weer te geven, gevolgd door de cmdlet [Set-AzContext:](/powershell/module/az.accounts/set-azcontext)

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

Als u de cmdlet [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) gebruikt, wordt gecontroleerd of het juiste abonnement is geselecteerd.

Gebruik de cmdlet [Get-command](/powershell/module/microsoft.powershell.core/get-command) om te controleren of Azure Disk Encryption cmdlets zijn geïnstalleerd:
     
```powershell
Get-command *diskencryption*
```
Zie Aan de slag met Azure PowerShell [voor meer informatie.](/powershell/azure/get-started-azureps) 

## <a name="enable-encryption-on-an-existing-or-running-linux-vm"></a>Versleuteling inschakelen op een bestaande of draaiende Linux-VM
In dit scenario kunt u versleuteling inschakelen met behulp van Resource Manager sjabloon, PowerShell-cmdlets of CLI-opdrachten. Als u schemagegevens nodig hebt voor de extensie van de virtuele machine, zie Azure Disk Encryption [voor Linux-extensie.](../extensions/azure-disk-enc-linux.md)

>[!IMPORTANT]
 >Het is verplicht om een momentopname en/of back-up te maken van een VM-exemplaar op basis van een beheerde schijf buiten en voordat u de Azure Disk Encryption. Een momentopname van de beheerde schijf kan worden gemaakt vanuit de portal of via [Azure Backup.](../../backup/backup-azure-vms-encryption.md) Back-ups zorgen ervoor dat een hersteloptie mogelijk is in het geval van onverwachte fouten tijdens de versleuteling. Zodra er een back-up is gemaakt, kan Set-AzVMDiskEncryptionExtension cmdlet worden gebruikt om beheerde schijven te versleutelen door de parameter -skipVmBackup op te geven. De Set-AzVMDiskEncryptionExtension opdracht mislukt voor VM's op basis van beheerde schijven totdat er een back-up is gemaakt en deze parameter is opgegeven. 
>
>Versleuteling of het uitschakelen van versleuteling kan ertoe leiden dat de VM opnieuw wordt opgestart. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-azure-cli"></a>Versleuteling inschakelen op een bestaande of linux-VM die wordt uitgevoerd met behulp van Azure CLI 

U kunt schijfversleuteling op uw versleutelde VHD inschakelen door het Opdrachtregelprogramma van [Azure CLI](/cli/azure/) te installeren en te gebruiken. U kunt PowerShell in uw browser gebruiken met [Azure Cloud Shell](../../cloud-shell/overview.md), of installeren op uw lokale computer en dan gebruiken in een PowerShell-sessie. Als u versleuteling wilt inschakelen op bestaande of virtuele Linux-VM's in Azure, gebruikt u de volgende CLI-opdrachten:

Gebruik de [opdracht az vm encryption enable om](/cli/azure/vm/encryption#az_vm_encryption_show) versleuteling in te stellen op een virtuele machine die wordt uitgevoerd in Azure.

- **Een VM versleutelen die wordt uitgevoerd:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Versleutel een VM die wordt uitgevoerd met behulp van KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > De syntaxis voor de waarde van de parameter disk-encryption-keyvault is de volledige id-tekenreeks: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br>
De syntaxis voor de waarde van de parameter key-encryption-key is de volledige URI voor de KEK, zoals in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Controleer of de schijven zijn versleuteld:** Als u de versleutelingsstatus van een VM wilt controleren, gebruikt [u de opdracht az vm encryption show.](/cli/azure/vm/encryption#az_vm_encryption_show) 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Versleuteling uitschakelen:** Als u versleuteling wilt uitschakelen, gebruikt [u de opdracht az vm encryption disable.](/cli/azure/vm/encryption#az_vm_encryption_disable) Het uitschakelen van versleuteling is alleen toegestaan op gegevensvolumes voor Linux-VM's.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "data"
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-powershell"></a>Versleuteling inschakelen op een bestaande of bestaande Linux-VM met behulp van PowerShell
Gebruik de cmdlet [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) om versleuteling in teschakelen op een virtuele machine die wordt uitgevoerd in Azure. Maak een [momentopname](snapshot-copy-managed-disk.md) en/of maak een back-up van de virtuele [Azure Backup](../../backup/backup-azure-vms-encryption.md) voordat schijven worden versleuteld. De parameter -skipVmBackup is al opgegeven in de PowerShell-scripts voor het versleutelen van een linux-VM die wordt uitgevoerd.

-  **Een VM versleutelen die wordt uitgevoerd:** Het onderstaande script initialiseert uw variabelen en voert de Set-AzVMDiskEncryptionExtension cmdlet uit. De resourcegroep, de VM en de sleutelkluis zijn gemaakt als vereisten. Vervang MyVirtualMachineResourceGroup, MySecureVM en MySecureVault door uw waarden. Wijzig de parameter -VolumeType om op te geven welke schijven u wilt versleutelen.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();  

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```
- **Een VM die wordt uitgevoerd versleutelen met behulp van KEK:** Mogelijk moet u de parameter -VolumeType toevoegen als u gegevensschijven versleutelt en niet de besturingssysteemschijf. 

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

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > De syntaxis voor de waarde van de parameter disk-encryption-keyvault is de volledige id-tekenreeks: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> De syntaxis voor de waarde van de parameter key-encryption-key is de volledige URI voor de KEK, zoals in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 
    
- **Controleer of de schijven zijn versleuteld:** Als u de versleutelingsstatus van een VM wilt controleren, gebruikt u de cmdlet [Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Schijfversleuteling uitschakelen:** Als u de versleuteling wilt uitschakelen, gebruikt u de cmdlet [Disable-AzVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) Het uitschakelen van versleuteling is alleen toegestaan op gegevensvolumes voor Linux-VM's.
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-with-a-template"></a>Versleuteling inschakelen op een bestaande of linux-VM met een sjabloon

U kunt schijfversleuteling inschakelen op een bestaande of linux-VM in Azure met behulp van de [Resource Manager sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad).

1. Klik **op Implementeren in Azure** in de Azure-quickstart-sjabloon.

2. Selecteer het abonnement, de resourcegroep, de locatie van de resourcegroep, de parameters, de juridische voorwaarden en de overeenkomst. Klik **op Maken om** versleuteling in te stellen op de bestaande of bestaande VM.

De volgende tabel bevat Resource Manager sjabloonparameters voor bestaande of bestaande VM's:

| Parameter | Beschrijving |
| --- | --- |
| vmName | Naam van de VM om de versleutelingsbewerking uit te voeren. |
| keyVaultName | Naam van de sleutelkluis waar de versleutelingssleutel naar moet worden geüpload. U kunt deze krijgen met behulp van de cmdlet `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` of de Azure CLI-opdracht `az keyvault list --resource-group "MyKeyVaultResourceGroupName"` .|
| keyVaultResourceGroup | Naam van de resourcegroep die de sleutelkluis bevat. |
|  keyEncryptionKeyURL | URL van de sleutelversleutelingssleutel die wordt gebruikt om de versleutelingssleutel te versleutelen. Deze parameter is optioneel als u **nokek selecteert** in de vervolgkeuzelijst UseExistingKek. Als u **kek selecteert** in de vervolgkeuzelijst UseExistingKek, moet u de _waarde keyEncryptionKeyURL_ invoeren. |
| volumeType | Type volume dat de versleutelingsbewerking wordt uitgevoerd. Geldige waarden zijn _OS,_ _Data_ en _All._ 
| forceUpdateTag | Geef elke keer dat de bewerking geforceerd moet worden uitgevoerd een unieke waarde door, zoals een GUID. |
| location | Locatie voor alle resources. |

Zie voor meer informatie over het configureren van de schijfversleutelingssjabloon voor linux-VM Azure Disk Encryption [voor Linux.](../extensions/azure-disk-enc-linux.md)

## <a name="use-encryptformatall-feature-for-data-disks-on-linux-vms"></a>De functie EncryptFormatAll gebruiken voor gegevensschijven op Linux-VM's

De **parameter EncryptFormatAll** vermindert de tijd voor het versleutelen van Linux-gegevensschijven. Partities die voldoen aan bepaalde criteria worden opgemaakt, samen met hun huidige bestandssystemen, en vervolgens opnieuw worden toegevoegd aan de locatie waar ze waren vóór de uitvoering van de opdracht. Als u een gegevensschijf wilt uitsluiten die voldoet aan de criteria, kunt u deze ontkoppelen voordat u de opdracht gaat uitvoeren.

 Nadat u deze opdracht hebt uitgevoerd, worden alle stations die eerder zijn bevestigd, geformatteerd en wordt de versleutelingslaag gestart boven op het nu lege station. Wanneer deze optie is geselecteerd, wordt de tijdelijke schijf die is gekoppeld aan de VM ook versleuteld. Als de tijdelijke schijf opnieuw wordt ingesteld, wordt deze bij de volgende kans opnieuw geformatteerd en opnieuw versleuteld voor de virtuele Azure Disk Encryption-oplossing. Zodra de resourceschijf is versleuteld, kan [de Microsoft Azure Linux-agent](../extensions/agent-linux.md) de resourceschijf niet meer beheren en het wisselbestand inschakelen, maar u kunt het wisselbestand handmatig configureren.

>[!WARNING]
> EncryptFormatAll mag niet worden gebruikt wanneer er gegevens nodig zijn op de gegevensvolumes van een VM. U kunt schijven uitsluiten van versleuteling door ze te ontkoppelen. Probeer eerst EncryptFormatAll eerst uit op een test-VM, begrijp de functieparameter en de implicatie ervan voordat u deze op de productie-VM probeert. Met de optie EncryptFormatAll wordt de gegevensschijf opgemaakt en gaan alle gegevens op de schijf verloren. Voordat u doorgaat, controleert u of de schijven die u wilt uitsluiten, juist zijn ontkoppeld. </br></br>
 >Als u deze parameter instel tijdens het bijwerken van de versleutelingsinstellingen, kan dit leiden tot opnieuw opstarten vóór de daadwerkelijke versleuteling. In dit geval wilt u ook de schijf verwijderen die u niet wilt formatteren uit het fstab-bestand. Op dezelfde manier moet u de partitie die u wilt versleutelen toevoegen aan het fstab-bestand voordat u de versleutelingsbewerking start. 

### <a name="encryptformatall-criteria"></a>EncryptFormatAlle criteria
De parameter gaat door alle partities en versleutelt ze zolang ze voldoen **aan alle** onderstaande criteria:
- Is geen root-/OS-/opstartpartitie
- Is nog niet versleuteld
- Is geen BEK-volume
- Is geen RAID-volume
- Is geen LVM-volume
- Is bevestigd

Versleutel de schijven die het RAID- of LVM-volume vormen in plaats van het RAID- of LVM-volume.

### <a name="use-the-encryptformatall-parameter-with-azure-cli"></a>De parameter EncryptFormatAll gebruiken met Azure CLI
Gebruik de [opdracht az vm encryption enable om](/cli/azure/vm/encryption#az_vm_encryption_enable) versleuteling in te stellen op een virtuele machine die wordt uitgevoerd in Azure.

-  **Versleutel een VM die wordt uitgevoerd met EncryptFormatAll:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "data" --encrypt-format-all
     ```

### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a>De parameter EncryptFormatAll gebruiken met een PowerShell-cmdlet
Gebruik de cmdlet [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) met de parameter EncryptFormatAll. 

**Versleutel een VM die wordt uitgevoerd met EncryptFormatAll:** Het onderstaande script initialiseert bijvoorbeeld uw variabelen en voert de cmdlet Set-AzVMDiskEncryptionExtension uit met de parameter EncryptFormatAll. De resourcegroep, de VM en de sleutelkluis zijn gemaakt als vereisten. Vervang MyVirtualMachineResourceGroup, MySecureVM en MySecureVault door uw waarden.
  
```azurepowershell
$KVRGname = 'MyKeyVaultResourceGroup';
$VMRGName = 'MyVirtualMachineResourceGroup';
$vmName = 'MySecureVM';
$KeyVaultName = 'MySecureVault';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "data" -EncryptFormatAll
```


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a>De parameter EncryptFormatAll gebruiken met Logical Volume Manager (LVM) 
We raden u aan om LVM-on-crypt in te stellen. Vervang voor alle volgende voorbeelden het apparaatpad en de bevestigingspunten door wat bij uw use-case past. Deze installatie kan als volgt worden uitgevoerd:

1.  Voeg de gegevensschijven toe waaruit de VM wordt opgeslagen.

1. Formatteer, bevestig en voeg deze schijven toe aan het fstab-bestand.

1. Kies een partitiestandaard, maak een partitie die het hele station omvat en formatteer vervolgens de partitie. We gebruiken hier symlinks die door Azure worden gegenereerd. Het gebruik van symlinks voorkomt problemen met betrekking tot het wijzigen van apparaatnamen. Zie het artikel Problemen met apparaatnamen [oplossen voor meer](/troubleshoot/azure/virtual-machines/troubleshoot-device-names-problems) informatie.
    
    ```bash
    parted /dev/disk/azure/scsi1/lun0 mklabel gpt
    parted -a opt /dev/disk/azure/scsi1/lun0 mkpart primary ext4 0% 100%
    
    mkfs -t ext4 /dev/disk/azure/scsi1/lun0-part1
    ```

1. De schijven monteren:

    ```bash
    mount /dev/disk/azure/scsi1/lun0-part1 /mnt/mountpoint
    ````
    
    Toevoegen aan fstab-bestand:

    ```bash
    echo "/dev/disk/azure/scsi1/lun0-part1 /mnt/mountpoint ext4 defaults,nofail 0 2" >> /etc/fstab
    ```
    
1. Voer de Azure PowerShell [cmdlet Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) uit met -EncryptFormatAll om deze schijven te versleutelen.

    ```azurepowershell-interactive
    $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
    
    Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data
    ```

    Als u een sleutelversleutelingssleutel (KEK) wilt gebruiken, geef dan respectievelijk de URI van uw KEK en de ResourceID van uw sleutelkluis door aan de parameters -KeyEncryptionKeyUrl en -KeyEncryptionKeyVaultId:

    ```azurepowershell-interactive
    $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
    $KEKKeyVault = Get-AzKeyVault -VaultName "MyKEKVault" -ResourceGroupName "MySecureGroup"
    $KEK = Get-AzKeyVaultKey -VaultName "myKEKVault" -KeyName "myKEKName"
    
    Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data -KeyEncryptionKeyUrl $$KEK.id -KeyEncryptionKeyVaultId $KEKKeyVault.ResourceId
    ```

1. Stel LVM in op deze nieuwe schijven. Houd er rekening mee dat de versleutelde stations worden ontgrendeld nadat de VM is opgestart. De LVM-bevestiging moet dus ook later worden vertraagd.

## <a name="new-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Nieuwe VM's die zijn gemaakt van door de klant versleutelde VHD en versleutelingssleutels
In dit scenario kunt u versleuteling inschakelen met behulp van PowerShell-cmdlets of CLI-opdrachten. 

Gebruik de instructies in Azure Disk Encryption voor het voorbereiden van vooraf versleutelde afbeeldingen die kunnen worden gebruikt in Azure. Nadat de afbeelding is gemaakt, kunt u de stappen in de volgende sectie gebruiken om een versleutelde Azure-VM te maken.

* [Een vooraf versleutelde Linux-VHD voorbereiden](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-linux-vhd)

>[!IMPORTANT]
 >Het is verplicht om een momentopname en/of back-up te maken van een VM-exemplaar op basis van een beheerde schijf buiten en voordat u de Azure Disk Encryption. Een momentopname van de beheerde schijf kan worden gemaakt vanuit de portal of [Azure Backup](../../backup/backup-azure-vms-encryption.md) kan worden gebruikt. Back-ups zorgen ervoor dat een hersteloptie mogelijk is in het geval van onverwachte fouten tijdens de versleuteling. Zodra er een back-up is gemaakt, kan Set-AzVMDiskEncryptionExtension cmdlet worden gebruikt om beheerde schijven te versleutelen door de parameter -skipVmBackup op te geven. De Set-AzVMDiskEncryptionExtension opdracht mislukt voor VM's op basis van beheerde schijven totdat er een back-up is gemaakt en deze parameter is opgegeven. 
>
> Versleuteling of het uitschakelen van versleuteling kan ertoe leiden dat de VM opnieuw wordt opgestart. 



### <a name="use-azure-powershell-to-encrypt-vms-with-pre-encrypted-vhds"></a>Gebruik Azure PowerShell om VM's te versleutelen met vooraf versleutelde VHD's 
U kunt schijfversleuteling inschakelen op uw versleutelde VHD met behulp van de PowerShell-cmdlet [Set-AzVMOSDisk.](/powershell/module/Az.Compute/Set-AzVMOSDisk#examples) In het onderstaande voorbeeld ziet u enkele algemene parameters. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Linux -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Versleuteling inschakelen op een nieuw toegevoegde gegevensschijf

U kunt een nieuwe gegevensschijf toevoegen met [az vm disk attach](add-disk.md)of via de [Azure Portal](attach-disk-portal.md). Voordat u kunt versleutelen, moet u eerst de zojuist gekoppelde gegevensschijf koppelen. U moet versleuteling van het gegevensstation aanvragen omdat het station onbruikbaar wordt terwijl de versleuteling wordt uitgevoerd. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Versleuteling inschakelen op een nieuw toegevoegde schijf met Azure CLI

 Als de VM eerder is versleuteld met 'Alle', moet de parameter --volume-type 'Alle' blijven. Alle omvatten zowel besturingssysteem- als gegevensschijven. Als de VM eerder is versleuteld met het volumetype 'OS', moet de parameter --volume-type worden gewijzigd in 'Alle' zodat zowel het besturingssysteem als de nieuwe gegevensschijf worden opgenomen. Als de VM is versleuteld met alleen het volumetype 'Gegevens', kan deze 'Gegevens' blijven, zoals hieronder wordt gedemonstreerd. Het toevoegen en koppelen van een nieuwe gegevensschijf aan een VM is niet voldoende voorbereiding voor versleuteling. De zojuist gekoppelde schijf moet ook worden geformatteerd en op de juiste wijze worden gekoppeld in de VM voordat versleuteling wordt inschakelen. In Linux moet de schijf worden bevestigd in /etc/fstab met een [permanente naam voor het blokapparaat.](../troubleshooting/troubleshoot-device-names-problems.md)  

In tegenstelling tot de PowerShell-syntaxis hoeft de GEBRUIKER geen unieke reeksversie op te geven bij het inschakelen van versleuteling. De CLI genereert en gebruikt automatisch een eigen unieke sequentieversiewaarde.

-  **Gegevensvolumes van een virtuele VM die wordt uitgevoerd, versleutelen:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Gegevensvolumes van een virtuele VM die wordt uitgevoerd versleutelen met behulp van KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Versleuteling inschakelen op een nieuw toegevoegde schijf met Azure PowerShell
 Wanneer u PowerShell gebruikt om een nieuwe schijf voor Linux te versleutelen, moet een nieuwe reeksversie worden opgegeven. De sequentieversie moet uniek zijn. Met het onderstaande script wordt een GUID voor de reeksversie gegenereerd. Maak een [momentopname](snapshot-copy-managed-disk.md) en/of maak een back-up van de virtuele [Azure Backup](../../backup/backup-azure-vms-encryption.md) voordat schijven worden versleuteld. De parameter -skipVmBackup is al opgegeven in de PowerShell-scripts voor het versleutelen van een nieuw toegevoegde gegevensschijf.
 

-  **Gegevensvolumes van een draaiende VM versleutelen:** Het onderstaande script initialiseert uw variabelen en voert de Set-AzVMDiskEncryptionExtension cmdlet uit. De resourcegroep, VM en sleutelkluis moeten al zijn gemaakt als vereisten. Vervang MyVirtualMachineResourceGroup, MySecureVM en MySecureVault door uw waarden. Acceptabele waarden voor de parameter -VolumeType zijn Alle, Besturingssysteem en Gegevens. Als de VM eerder is versleuteld met het volumetype 'BESTURINGSSYSTEEM' of 'Alle', moet de parameter -VolumeType worden gewijzigd in 'Alle', zodat zowel het besturingssysteem als de nieuwe gegevensschijf worden opgenomen.

      ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
      ```
- **Gegevensvolumes van een virtuele VM die wordt uitgevoerd versleutelen met behulp van KEK:** Acceptabele waarden voor de parameter -VolumeType zijn Alle, Besturingssysteem en Gegevens. Als de VM eerder is versleuteld met het volumetype 'BESTURINGSSYSTEEM' of 'Alle', moet de parameter -VolumeType worden gewijzigd in Alle zodat zowel het besturingssysteem als de nieuwe gegevensschijf worden opgenomen.

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

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > De syntaxis voor de waarde van de parameter disk-encryption-keyvault is de volledige id-tekenreeks: /subscriptions/[subscription-id-guid]/resourceGroups/[KSource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> De syntaxis voor de waarde van de parameter key-encryption-key is de volledige URI voor de KEK, zoals in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 


## <a name="disable-encryption-for-linux-vms"></a>Versleuteling voor Linux-VM's uitschakelen
[!INCLUDE [disk-encryption-disable-encryption-cli](../../../includes/disk-encryption-disable-cli.md)]

## <a name="unsupported-scenarios"></a>Niet-ondersteunde scenario's

Azure Disk Encryption werkt niet voor de volgende Linux-scenario's, -functies en -technologie:

- Versleuteling van virtuele VM's in de basic-laag of VM's die zijn gemaakt via de klassieke methode voor het maken van VM's.
- Het uitschakelen van versleuteling op een besturingssysteemstation of gegevensstation van een Linux-VM wanneer het besturingssysteemstation is versleuteld.
- Het besturingssysteemstation versleutelen voor virtuele-machineschaalsets in Linux.
- Aangepaste afbeeldingen versleutelen op Linux-VM's.
- Integratie met een on-premises sleutelbeheersysteem.
- Azure Files (gedeeld bestandssysteem).
- Network File System (NFS).
- Dynamische volumes.
- Kortstondige besturingssysteemschijven.
- Versleuteling van gedeelde/gedistribueerde bestandssystemen zoals (maar niet beperkt tot): DFS, GFS, DRDB en CephFS.
- Een versleutelde VM verplaatsen naar een ander abonnement of een andere regio.
- Een afbeelding of momentopname van een versleutelde VM maken en deze gebruiken om extra VM's te implementeren.
- Kernel-crashdump (kdump).
- Oracle ACFS (ASM-clusterbestandssysteem).
- De NVMe-schijven van VM's uit de Lsv2-serie (zie: [Lsv2-serie](../lsv2-series.md)).
- Een VM met 'geneste bevestigingspunten'; dat wil zeggen meerdere bevestigingspunten in één pad (zoals /1stmountpoint/data/2stmountpoint).
- Een VM met een gegevensstation die boven op een map van het besturingssysteem is bevestigd.
- Een VM waarop een logisch hoofdvolume (besturingssysteemschijf) is uitgebreid met behulp van een gegevensschijf.
- VM's uit de M-serie Write Accelerator schijven.
- ADE toepassen op een VM met schijven die zijn versleuteld met versleuteling aan [de serverzijde](../disk-encryption.md) met door de klant beheerde sleutels (SSE + CMK). Het toepassen van SSE + CMK op een gegevensschijf op een VM die is versleuteld met ADE is ook een niet-ondersteund scenario.
- Een VM migreren die is versleuteld met ADE of ooit is versleuteld met ADE, naar versleuteling aan de serverzijde met door de klant [beheerde sleutels](../disk-encryption.md). 
- VM's versleutelen in failoverclusters.
- Versleuteling [van Azure Ultra Disks](../disks-enable-ultra-ssd.md).

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure Disk Encryption](disk-encryption-overview.md)
- [Voorbeeldscripts voor Azure Disk Encryption](disk-encryption-sample-scripts.md)
- [Problemen met Azure Disk Encryption oplossen](disk-encryption-troubleshooting.md)