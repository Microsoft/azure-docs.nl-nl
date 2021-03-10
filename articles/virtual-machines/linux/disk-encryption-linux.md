---
title: Azure Disk Encryption-scenario's voor virtuele Linux-machines
description: In dit artikel vindt u instructies voor het inschakelen van Microsoft Azure schijf versleuteling voor Linux-Vm's voor verschillende scenario's
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: bf5da120072f49421b6f2194626178048b044f6a
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102564693"
---
# <a name="azure-disk-encryption-scenarios-on-linux-vms"></a>Azure Disk Encryption-scenario's voor virtuele Linux-machines

Azure Disk Encryption voor virtuele Linux-machines (Vm's) gebruikt de DM-Crypt-functie van Linux voor het versleutelen van de volledige schijf versleuteling van de besturingssysteem schijf en gegevens schijven. Daarnaast biedt het een versleuteling van de tijdelijke schijf wanneer de functie EncryptFormatAll wordt gebruikt.

Azure Disk Encryption is [geïntegreerd met Azure Key Vault](disk-encryption-key-vault.md) , zodat u de versleutelings sleutels en geheimen van de schijf kunt beheren. Zie [Azure Disk Encryption voor Linux-vm's](disk-encryption-overview.md)voor een overzicht van de service.

U kunt alleen schijf versleuteling Toep assen op virtuele machines met [ondersteunde VM-grootten en-besturings systemen](disk-encryption-overview.md#supported-vms-and-operating-systems). U moet ook aan de volgende vereisten voldoen:

- [Aanvullende vereisten voor Vm's](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Netwerkvereisten](disk-encryption-overview.md#networking-requirements)
- [Opslag vereisten voor de versleutelings sleutel](disk-encryption-overview.md#encryption-key-storage-requirements)

In alle gevallen moet u [een moment opname nemen](snapshot-copy-managed-disk.md) en/of een back-up maken voordat schijven worden versleuteld. Back-ups zorgen ervoor dat een herstel optie mogelijk is als er een onverwachte fout optreedt tijdens het versleutelen. Voor Vm's met Managed disks is een back-up vereist voordat versleuteling wordt uitgevoerd. Als er een back-up is gemaakt, kunt u de [cmdlet Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) gebruiken om beheerde schijven te versleutelen door de para meter-skipVmBackup op te geven. Zie het [Azure backup](../../backup/backup-azure-vms-encryption.md) -artikel voor meer informatie over het maken van back-ups en het herstellen van versleutelde vm's. 

>[!WARNING]
> - Als u eerder Azure Disk Encryption met Azure AD hebt gebruikt om een virtuele machine te versleutelen, moet u deze optie blijven gebruiken om uw virtuele machine te versleutelen. Zie [Azure Disk Encryption met Azure AD (vorige versie)](disk-encryption-overview-aad.md) voor meer informatie. 
>
> - Bij het versleutelen van Linux-besturingssysteem volumes moet de virtuele machine worden beschouwd als niet-beschikbaar. We raden u ten zeerste aan om SSH-aanmeldingen te voor komen terwijl de versleuteling wordt uitgevoerd om te voor komen dat er geopende bestanden worden geblokkeerd die moeten worden geopend tijdens het versleutelings proces. Als u de voortgang wilt controleren, gebruikt u de Power shell-cmdlet [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) of de [VM-versleutelings opdracht show](/cli/azure/vm/encryption#az-vm-encryption-show) cli. Dit proces kan naar verwachting enkele uren duren voor een besturingssysteem volume van 30 GB, plus extra tijd voor het versleutelen van gegevens volumes. De versleutelings tijd voor gegevens volumes is evenredig met de grootte en hoeveelheid van de gegevens volumes, tenzij de optie alle versleutelings indeling wordt gebruikt. 
> - Het uitschakelen van versleuteling op Linux Vm's wordt alleen ondersteund voor gegevens volumes. Het wordt niet ondersteund op gegevens of besturingssysteem volumes als het volume van het besturings systeem is versleuteld.  

## <a name="install-tools-and-connect-to-azure"></a>Hulpprogramma's installeren en verbinding maken met Azure

Azure Disk Encryption kunnen worden ingeschakeld en beheerd via de [Azure cli](/cli/azure) en [Azure PowerShell](/powershell/azure/new-azureps-module-az). Hiervoor moet u de hulpprogram ma's lokaal installeren en verbinding maken met uw Azure-abonnement.

### <a name="azure-cli"></a>Azure CLI

De [Azure CLI 2,0](/cli/azure) is een opdracht regel programma voor het beheer van Azure-resources. De CLI is ontworpen voor flexibelere query gegevens, ondersteunt langlopende bewerkingen als niet-blokkerende processen en maakt het uitvoeren van scripts eenvoudig. U kunt deze lokaal installeren door de stappen in [de Azure cli installeren](/cli/azure/install-azure-cli)te volgen.

 

Als u [zich wilt aanmelden bij uw Azure-account met de Azure cli](/cli/azure/authenticate-azure-cli), gebruikt u de opdracht [AZ login](/cli/azure/reference-index#az_login) .

```azurecli
az login
```

Als u een Tenant wilt selecteren om u aan te melden, gebruikt u:
    
```azurecli
az login --tenant <tenant>
```

Als u meerdere abonnementen hebt en u een specifiek abonnement wilt opgeven, kunt u uw lijst met abonnementen ophalen met [AZ account list](/cli/azure/account#az-account-list) en opgeven met [AZ account set](/cli/azure/account#az-account-set).
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

Zie [aan de slag met Azure CLI 2,0](/cli/azure/get-started-with-azure-cli)voor meer informatie. 

### <a name="azure-powershell"></a>Azure PowerShell
De [Azure PowerShell AZ-module](/powershell/azure/new-azureps-module-az) bevat een set cmdlets die gebruikmaken van het [Azure Resource Manager](../../azure-resource-manager/management/overview.md) model voor het beheren van uw Azure-resources. U kunt dit in uw browser gebruiken met [Azure Cloud shell](../../cloud-shell/overview.md), of u kunt het installeren op uw lokale computer met behulp van de instructies in [de Azure PowerShell-module installeren](/powershell/azure/install-az-ps). 

Als u dit al hebt geïnstalleerd, moet u ervoor zorgen dat u de nieuwste versie van Azure PowerShell SDK-versie gebruikt om Azure Disk Encryption te configureren. Down load de nieuwste versie van [Azure PowerShell release](https://github.com/Azure/azure-powershell/releases).

Gebruik de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) om u aan [te melden bij uw Azure-account met Azure PowerShell](/powershell/azure/authenticate-azureps).

```powershell
Connect-AzAccount
```

Als u meerdere abonnementen hebt en er een wilt opgeven, gebruikt u de cmdlet [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) om ze weer te geven, gevolgd door de cmdlet [set-AzContext](/powershell/module/az.accounts/set-azcontext) :

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

Als u de cmdlet [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) uitvoert, wordt gecontroleerd of het juiste abonnement is geselecteerd.

Als u wilt controleren of de Azure Disk Encryption-cmdlets zijn geïnstalleerd, gebruikt u de cmdlet [Get-opdracht](/powershell/module/microsoft.powershell.core/get-command) :
     
```powershell
Get-command *diskencryption*
```
Zie [aan de slag met Azure PowerShell](/powershell/azure/get-started-azureps)voor meer informatie. 

## <a name="enable-encryption-on-an-existing-or-running-linux-vm"></a>Versleuteling inschakelen op een bestaande of actieve virtuele Linux-machine
In dit scenario kunt u versleuteling inschakelen met behulp van de Resource Manager-sjabloon, Power shell-cmdlets of CLI-opdrachten. Als u schema-informatie voor de extensie van de virtuele machine nodig hebt, raadpleegt u het artikel [Azure Disk Encryption voor Linux-extensie](../extensions/azure-disk-enc-linux.md) .

>[!IMPORTANT]
 >Het is verplicht een moment opname en/of back-up te maken van een beheerde schijf op basis van een virtuele machine buiten en voordat u Azure Disk Encryption inschakelt. Een moment opname van de beheerde schijf kan worden gemaakt vanuit de portal of via [Azure backup](../../backup/backup-azure-vms-encryption.md). Back-ups zorgen ervoor dat een herstel optie mogelijk is in het geval van een onverwachte fout tijdens het versleutelen. Als er een back-up is gemaakt, kan de cmdlet Set-AzVMDiskEncryptionExtension worden gebruikt voor het versleutelen van beheerde schijven door de para meter-skipVmBackup op te geven. De Set-AzVMDiskEncryptionExtension opdracht mislukt voor virtuele machines op basis van beheerde schijven tot er een back-up is gemaakt en deze para meter is opgegeven. 
>
>Het versleutelen of uitschakelen van versleuteling kan ertoe leiden dat de virtuele machine opnieuw wordt opgestart. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-azure-cli"></a>Versleuteling inschakelen op een bestaande of actieve virtuele Linux-machine met behulp van Azure CLI 

U kunt schijf versleuteling inschakelen op uw versleutelde VHD door het opdracht regel programma van [Azure cli](/cli/azure/) te installeren en te gebruiken. U kunt PowerShell in uw browser gebruiken met [Azure Cloud Shell](../../cloud-shell/overview.md), of installeren op uw lokale computer en dan gebruiken in een PowerShell-sessie. Gebruik de volgende CLI-opdrachten om versleuteling in te scha kelen voor bestaande of actieve virtuele Linux-machines in Azure:

Gebruik de opdracht [AZ VM Encryption Enable](/cli/azure/vm/encryption#az_vm_encryption_show) om versleuteling in te scha kelen op een actieve virtuele machine in Azure.

- **Een actieve VM versleutelen:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Een actieve VM versleutelen met behulp van KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > De syntaxis voor de waarde van de para meter voor schijf versleuteling-sleutel kluis is de volledige id-teken reeks:/Subscriptions/[abonnement-ID-GUID]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[sleutel kluis-naam]</br>
De syntaxis voor de waarde van de para meter Key-Encryption-Key is de volledige URI naar de KEK zoals in: https://[sleutel kluis-name]. kluis. Azure. net/Keys/[kekname]/[Kek-unique-id] 

- **Controleer of de schijven zijn versleuteld:** Als u de versleutelings status van een virtuele machine wilt controleren, gebruikt u de opdracht [AZ VM Encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) . 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Versleuteling uitschakelen:** Als u versleuteling wilt uitschakelen, gebruikt u de opdracht [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . Versleuteling uitschakelen is alleen toegestaan op gegevens volumes voor Linux-Vm's.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "data"
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-powershell"></a>Versleuteling inschakelen op een bestaande of actieve virtuele Linux-machine met behulp van Power shell
Gebruik de cmdlet [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) om versleuteling in te scha kelen op een actieve virtuele machine in Azure. Maak een [moment opname](snapshot-copy-managed-disk.md) en/of maak een back-up van de virtuele machine met [Azure backup](../../backup/backup-azure-vms-encryption.md) voordat schijven worden versleuteld. De para meter-skipVmBackup is al opgegeven in de Power shell-scripts voor het versleutelen van een actieve Linux-VM.

-  **Een actieve VM versleutelen:** Het onderstaande script initialiseert uw variabelen en voert de Set-AzVMDiskEncryptionExtension-cmdlet uit. De resource groep, de VM en de sleutel kluis zijn gemaakt als vereisten. Vervang MyVirtualMachineResourceGroup, MySecureVM en MySecureVault door uw waarden. Wijzig de para meter-VolumeType om op te geven welke schijven u wilt versleutelen.

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
- **Een actieve VM versleutelen met behulp van KEK:** Mogelijk moet u de para meter-VolumeType toevoegen als u gegevens schijven versleutelt en niet de besturingssysteem schijf. 

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
    > De syntaxis voor de waarde van de para meter voor schijf versleuteling-sleutel kluis is de volledige id-teken reeks:/Subscriptions/[abonnement-ID-GUID]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[sleutel kluis-naam]</br> De syntaxis voor de waarde van de para meter Key-Encryption-Key is de volledige URI naar de KEK zoals in: https://[sleutel kluis-name]. kluis. Azure. net/Keys/[kekname]/[Kek-unique-id] 
    
- **Controleer of de schijven zijn versleuteld:** Gebruik de cmdlet [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) om de versleutelings status van een virtuele machine te controleren. 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Schijf versleuteling uitschakelen:** Als u de versleuteling wilt uitschakelen, gebruikt u de cmdlet [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . Versleuteling uitschakelen is alleen toegestaan op gegevens volumes voor Linux-Vm's.
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-with-a-template"></a>Versleuteling inschakelen op een bestaande of uitgevoerde Linux-VM met een sjabloon

U kunt schijf versleuteling inschakelen op een bestaande of uitgevoerde Linux-VM in azure met behulp van de [Resource Manager-sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad).

1. Klik op **implementeren naar Azure** op de Azure Quick Start-sjabloon.

2. Selecteer het abonnement, de resource groep, de locatie van de resource groep, de para meters, de juridische voor waarden en de overeenkomst. Klik op **maken** om versleuteling in te scha kelen op de bestaande of actieve virtuele machine.

De volgende tabel bevat de para meters van Resource Manager-sjablonen voor bestaande of actieve Vm's:

| Parameter | Beschrijving |
| --- | --- |
| vmName | De naam van de virtuele machine om de versleutelings bewerking uit te voeren. |
| keyVaultName | De naam van de sleutel kluis waarnaar de versleutelings sleutel moet worden geüpload. U kunt deze ophalen met behulp van de cmdlet `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` of de Azure cli-opdracht `az keyvault list --resource-group "MyKeyVaultResourceGroupName"` .|
| keyVaultResourceGroup | De naam van de resource groep die de sleutel kluis bevat. |
|  keyEncryptionKeyURL | De URL van de sleutel versleutelings sleutel die wordt gebruikt voor het versleutelen van de versleutelings sleutel. Deze para meter is optioneel als u **nokek** selecteert in de vervolg keuzelijst UseExistingKek. Als u **Kek** selecteert in de vervolg keuzelijst UseExistingKek, moet u de _keyEncryptionKeyURL_ -waarde invoeren. |
| volumeType | Type volume waarop de versleutelings bewerking wordt uitgevoerd. Geldige waarden zijn _besturings systeem_, _gegevens_ en _alle_. 
| Updatetag | Geef een unieke waarde op als een GUID elke keer dat de bewerking geforceerd moet worden uitgevoerd. |
| location | Locatie voor alle resources. |

Zie [Azure Disk Encryption voor Linux](../extensions/azure-disk-enc-linux.md)voor meer informatie over het configureren van de sjabloon voor Linux VM-schijf versleuteling.

## <a name="use-encryptformatall-feature-for-data-disks-on-linux-vms"></a>De functie EncryptFormatAll gebruiken voor gegevens schijven op virtuele Linux-machines

De para meter **EncryptFormatAll** vermindert de tijd voor het versleutelen van Linux-gegevens schijven. Partities die voldoen aan bepaalde criteria worden opgemaakt, samen met de huidige bestands systemen en vervolgens opnieuw gekoppeld aan waar ze waren voordat ze werden uitgevoerd. Als u een gegevens schijf wilt uitsluiten die aan de criteria voldoet, kunt u deze ontkoppelen voordat u de opdracht uitvoert.

 Nadat u deze opdracht hebt uitgevoerd, worden alle schijven die eerder zijn gekoppeld, geformatteerd en wordt de versleutelings laag boven op het lege station gestart. Als deze optie is geselecteerd, wordt de tijdelijke schijf die aan de VM is gekoppeld, ook versleuteld. Als de tijdelijke schijf opnieuw wordt ingesteld, wordt deze opnieuw ingedeeld en opnieuw versleuteld voor de virtuele machine door de Azure Disk Encryption oplossing bij de volgende mogelijkheid. Zodra de bron schijf is versleuteld, kan de [Microsoft Azure Linux-agent](../extensions/agent-linux.md) de bron schijf niet beheren en het wissel bestand niet inschakelen, maar u kunt het wissel bestand ook hand matig configureren.

>[!WARNING]
> EncryptFormatAll mag niet worden gebruikt wanneer er gegevens op de gegevens volumes van de virtuele machine nodig zijn. U kunt schijven uitsluiten van versleuteling door deze te ontkoppelen. U moet eerst de EncryptFormatAll eerst op een test-VM uitproberen, inzicht krijgen in de para meter van de functie en de implicatie hiervan voordat u deze op de productie-VM probeert. De EncryptFormatAll-optie formatteert de gegevens schijf en alle gegevens erop gaan verloren. Controleer voordat u doorgaat of de schijven die u wilt uitsluiten, goed zijn ontkoppeld. </br></br>
 >Als u deze para meter instelt tijdens het bijwerken van de versleutelings instellingen, kan dit ertoe leiden dat de computer opnieuw wordt opgestart vóór de daad werkelijke versleuteling. In dit geval wilt u ook de schijf die u niet wilt Format teren uit het fstab-bestand verwijderen. U moet ook de partitie die u wilt versleutelen, toevoegen aan het fstab-bestand voordat de versleutelings bewerking wordt gestart. 

### <a name="encryptformatall-criteria"></a>EncryptFormatAll criteria
De para meter zorgt ervoor dat alle partities worden verplaatst en versleuteld zolang ze voldoen aan **alle** onderstaande criteria:
- Is geen hoofdmap/OS/opstart partitie
- Is nog niet versleuteld
- Is geen BEK-volume
- Is geen RAID-volume
- Is geen LVM-volume
- Is gekoppeld

Versleutel de schijven die het RAID-of LVM-volume vormen, in plaats van het RAID-of LVM-volume.

### <a name="use-the-encryptformatall-parameter-with-azure-cli"></a>De para meter EncryptFormatAll gebruiken met Azure CLI
Gebruik de opdracht [AZ VM Encryption Enable](/cli/azure/vm/encryption#az-vm-encryption-enable) om versleuteling in te scha kelen op een actieve virtuele machine in Azure.

-  **Een actieve VM versleutelen met behulp van EncryptFormatAll:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "data" --encrypt-format-all
     ```

### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a>De para meter EncryptFormatAll gebruiken met een Power shell-cmdlet
Gebruik de cmdlet [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) met de para meter EncryptFormatAll. 

**Een actieve VM versleutelen met behulp van EncryptFormatAll:** Als voor beeld wordt met het onderstaande script de variabelen geïnitialiseerd en wordt de Set-AzVMDiskEncryptionExtension cmdlet uitgevoerd met de para meter EncryptFormatAll. De resource groep, de virtuele machine en de sleutel kluis zijn gemaakt als vereisten. Vervang MyVirtualMachineResourceGroup, MySecureVM en MySecureVault door uw waarden.
  
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


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a>Gebruik de para meter EncryptFormatAll met Logical Volume Manager (LVM) 
We raden u aan om een LVM-on-cryptografie installatie uit te voeren. Voor alle volgende voor beelden vervangt u het pad naar het apparaat en de mountpoints door een wille keurige wens. Deze installatie kan als volgt worden uitgevoerd:

1.  Voeg de gegevens schijven toe waarmee de virtuele machine wordt samengesteld.

1. Format teer, koppel en voeg deze schijven toe aan het fstab-bestand.

1. Kies een partitie standaard, maak een partitie die het hele station omvat en Format teer vervolgens de partitie. We gebruiken hier symlinks die door Azure worden gegenereerd. Het gebruik van symlinks voor komt problemen met betrekking tot het wijzigen van de apparaatnaam. Zie het artikel problemen [met apparaatnamen oplossen](../troubleshooting/troubleshoot-device-names-problems.md) voor meer informatie.
    
    ```bash
    parted /dev/disk/azure/scsi1/lun0 mklabel gpt
    parted -a opt /dev/disk/azure/scsi1/lun0 mkpart primary ext4 0% 100%
    
    mkfs -t ext4 /dev/disk/azure/scsi1/lun0-part1
    ```

1. De schijven koppelen:

    ```bash
    mount /dev/disk/azure/scsi1/lun0-part1 /mnt/mountpoint
    ````
    
    Toevoegen aan fstab-bestand:

    ```bash
    echo "/dev/disk/azure/scsi1/lun0-part1 /mnt/mountpoint ext4 defaults,nofail 0 2" >> /etc/fstab
    ```
    
1. Voer de Azure PowerShell cmdlet [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) met-EncryptFormatAll uit om deze schijven te versleutelen.

    ```azurepowershell-interactive
    $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
    
    Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data
    ```

    Als u een sleutel versleutelings sleutel (KEK) wilt gebruiken, geeft u de URI van uw KEK en de ResourceID van uw sleutel kluis door respectievelijk de para meter-KeyEncryptionKeyUrl en-KeyEncryptionKeyVaultId:

    ```azurepowershell-interactive
    $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
    $KEKKeyVault = Get-AzKeyVault -VaultName "MyKEKVault" -ResourceGroupName "MySecureGroup"
    $KEK = Get-AzKeyVaultKey -VaultName "myKEKVault" -KeyName "myKEKName"
    
    Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data -KeyEncryptionKeyUrl $$KEK.id -KeyEncryptionKeyVaultId $KEKKeyVault.ResourceId
    ```

1. Stel LVM in op deze nieuwe schijven. Opmerking de versleutelde stations worden ontgrendeld nadat de virtuele machine is opgestart. Daarom moet de LVM-koppeling ook worden vertraagd.

## <a name="new-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Nieuwe Vm's gemaakt op basis van door de klant versleutelde VHD-en versleutelings sleutels
In dit scenario kunt u versleutelen inschakelen met behulp van Power shell-cmdlets of CLI-opdrachten. 

Gebruik de instructies in de Azure Disk Encryption zelfde scripts voor het voorbereiden van vooraf versleutelde installatie kopieën die kunnen worden gebruikt in Azure. Nadat de installatie kopie is gemaakt, kunt u de stappen in de volgende sectie gebruiken om een versleutelde Azure-VM te maken.

* [Een vooraf versleutelde Linux-VHD voorbereiden](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-linux-vhd)

>[!IMPORTANT]
 >Het is verplicht een moment opname en/of back-up te maken van een beheerde schijf op basis van een virtuele machine buiten en voordat u Azure Disk Encryption inschakelt. Een moment opname van de beheerde schijf kan worden opgehaald uit de portal of [Azure backup](../../backup/backup-azure-vms-encryption.md) kunnen worden gebruikt. Back-ups zorgen ervoor dat een herstel optie mogelijk is in het geval van een onverwachte fout tijdens het versleutelen. Als er een back-up is gemaakt, kan de cmdlet Set-AzVMDiskEncryptionExtension worden gebruikt voor het versleutelen van beheerde schijven door de para meter-skipVmBackup op te geven. De Set-AzVMDiskEncryptionExtension opdracht mislukt voor virtuele machines op basis van beheerde schijven tot er een back-up is gemaakt en deze para meter is opgegeven. 
>
> Het versleutelen of uitschakelen van versleuteling kan ertoe leiden dat de virtuele machine opnieuw wordt opgestart. 



### <a name="use-azure-powershell-to-encrypt-vms-with-pre-encrypted-vhds"></a>Gebruik Azure PowerShell om Vm's te versleutelen met vooraf versleutelde Vhd's 
U kunt schijf versleuteling op uw versleutelde VHD inschakelen met behulp van de Power shell [-cmdlet Set-AzVMOSDisk](/powershell/module/Az.Compute/Set-AzVMOSDisk#examples). In het onderstaande voor beeld vindt u enkele algemene para meters. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Linux -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Versleuteling inschakelen op een nieuw toegevoegde gegevens schijf

U kunt een nieuwe gegevens schijf toevoegen met [AZ VM Disk attach](add-disk.md)of [via de Azure Portal](attach-disk-portal.md). Voordat u kunt versleutelen, moet u eerst de zojuist gekoppelde gegevens schijf koppelen. U moet versleuteling van het gegevens station aanvragen omdat het station niet kan worden gebruikt wanneer versleuteling wordt uitgevoerd. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Versleuteling inschakelen op een nieuw toegevoegde schijf met Azure CLI

 Als de virtuele machine eerder is versleuteld met ' all ', moet de para meter--type ' all ' blijven. Alle bevatten zowel het besturings systeem als de gegevens schijven. Als de virtuele machine eerder is versleuteld met het volume ' OS ', moet de para meter--volume-type worden gewijzigd in ' alle ', zodat zowel het besturings systeem als de nieuwe gegevens schijf worden opgenomen. Als de virtuele machine is versleuteld met alleen het volume type ' data ', kunnen de gegevens worden gewijzigd, zoals hieronder wordt weer gegeven. Het toevoegen en koppelen van een nieuwe gegevens schijf aan een VM is niet voldoende voor bereidingen voor versleuteling. De nieuw gekoppelde schijf moet ook worden geformatteerd en op de juiste wijze in de VM zijn gekoppeld voordat versleuteling kan worden ingeschakeld. Op Linux moet de schijf worden gekoppeld in bestand/etc/fstab met een [permanente blok apparaatnaam](../troubleshooting/troubleshoot-device-names-problems.md).  

In tegens telling tot de Power shell-syntaxis vereist de CLI niet dat de gebruiker een unieke volgorde versie moet opgeven bij het inschakelen van versleuteling. De CLI genereert en gebruikt automatisch een eigen unieke sequentie versie waarde.

-  **Gegevens volumes van een actieve VM versleutelen:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Versleutelen van gegevens volumes van een actieve VM met behulp van KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Versleuteling inschakelen op een nieuw toegevoegde schijf met Azure PowerShell
 Wanneer u Power shell gebruikt om een nieuwe schijf voor Linux te versleutelen, moet u een nieuwe reeks versie opgeven. De reeks versie moet uniek zijn. In het onderstaande script wordt een GUID voor de reeks versie gegenereerd. Maak een [moment opname](snapshot-copy-managed-disk.md) en/of maak een back-up van de virtuele machine met [Azure backup](../../backup/backup-azure-vms-encryption.md) voordat schijven worden versleuteld. De para meter-skipVmBackup is al opgegeven in de Power shell-scripts voor het versleutelen van een nieuw toegevoegde gegevens schijf.
 

-  **Gegevens volumes van een actieve VM versleutelen:** Het onderstaande script initialiseert uw variabelen en voert de Set-AzVMDiskEncryptionExtension-cmdlet uit. De resource groep, de virtuele machine en de sleutel kluis moeten al zijn gemaakt als vereisten. Vervang MyVirtualMachineResourceGroup, MySecureVM en MySecureVault door uw waarden. Acceptabele waarden voor de para meter-VolumeType zijn alle, het besturings systeem en de gegevens. Als de virtuele machine eerder is versleuteld met het volume ' OS ' of ' all ', moet de para meter-VolumeType worden gewijzigd in ' all ', zodat zowel het besturings systeem als de nieuwe gegevens schijf worden opgenomen.

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
- **Versleutelen van gegevens volumes van een actieve VM met behulp van KEK:** Acceptabele waarden voor de para meter-VolumeType zijn alle, het besturings systeem en de gegevens. Als de virtuele machine eerder is versleuteld met het volume ' OS ' of ' all ', moet de para meter-VolumeType worden gewijzigd in alle, zodat zowel het besturings systeem als de nieuwe gegevens schijf worden opgenomen.

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
    > De syntaxis voor de waarde van de para meter voor schijf versleuteling-sleutel kluis is de volledige id-teken reeks:/Subscriptions/[abonnement-ID-GUID]/resourceGroups/[KVresource-group-name]/providers/Microsoft.KeyVault/vaults/[sleutel kluis-name]</br> De syntaxis voor de waarde van de para meter Key-Encryption-Key is de volledige URI naar de KEK zoals in: https://[sleutel kluis-name]. kluis. Azure. net/Keys/[kekname]/[Kek-unique-id] 


## <a name="disable-encryption-for-linux-vms"></a>Versleuteling uitschakelen voor virtuele Linux-machines
[!INCLUDE [disk-encryption-disable-encryption-cli](../../../includes/disk-encryption-disable-cli.md)]

## <a name="unsupported-scenarios"></a>Niet-ondersteunde scenario's

Azure Disk Encryption werkt niet voor de volgende Linux-scenario's,-functies en-technologie:

- Het versleutelen van de virtuele machine van de Basic-laag of de virtuele machines die zijn gemaakt met de methode klassieke VM
- Versleuteling uitschakelen op een OS-station of gegevens station van een virtuele Linux-machine wanneer het station van het besturings systeem is versleuteld.
- Het station van het besturings systeem voor virtuele Linux-machine schaal sets versleutelen.
- Aangepaste installatie kopieën op Linux-Vm's versleutelen.
- Integratie met een on-premises sleutel beheersysteem.
- Azure Files (gedeeld bestands systeem).
- NFS (Network File System).
- Dynamische volumes.
- Tijdelijke OS-schijven.
- Versleuteling van gedeelde/gedistribueerde bestands systemen zoals (maar niet beperkt tot): DFS, GFS, DRDB en CephFS.
- Een versleutelde VM verplaatsen naar een ander abonnement of nieuwe regio.
- Een installatie kopie of moment opname van een versleutelde virtuele machine maken en deze gebruiken voor het implementeren van extra Vm's.
- Kernel-crash dump (kdump).
- Oracle-ACFS (ASM-cluster bestands systeem).
- De NVMe-schijven van virtuele machines uit de Lsv2-serie (zie: [Lsv2-serie](../lsv2-series.md)).
- Een virtuele machine met ' geneste koppel punten '; dat wil zeggen, meerdere koppel punten in één pad (zoals "/1stmountpoint/data/2stmountpoint").
- Een virtuele machine met een gegevens station die boven op een map van het besturings systeem is geplaatst.
- Een virtuele machine waarop een logische volume (besturingssysteem schijf) is uitgebreid met een gegevens schijf.
- Vm's uit de M-serie met Write Accelerator-schijven.
- ADE Toep assen op een virtuele machine met schijven die zijn versleuteld met versleuteling aan de [server zijde met door de klant beheerde sleutels](../disk-encryption.md) (SSE + CMK). Het Toep assen van SSE en CMK op een gegevens schijf op een virtuele machine die is versleuteld met ADE, is ook een niet-ondersteund scenario.
- Migratie van een VM die is versleuteld met ADE of **ooit** is versleuteld met ADE, voor versleuteling aan de [server zijde met door de klant beheerde sleutels](../disk-encryption.md).
- Vm's in failoverclusters versleutelen.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure Disk Encryption](disk-encryption-overview.md)
- [Voorbeeldscripts voor Azure Disk Encryption](disk-encryption-sample-scripts.md)
- [Problemen met Azure Disk Encryption oplossen](disk-encryption-troubleshooting.md)
