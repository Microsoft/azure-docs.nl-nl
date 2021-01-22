---
title: Versleutelings status controleren voor Linux-Azure Disk Encryption
description: In dit artikel vindt u instructies voor het controleren van de versleutelings status van het platform-en besturingssysteem niveau.
author: kailashmsft
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: how-to
ms.author: kaib
ms.date: 03/11/2020
ms.custom: seodec18, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 42b1aed2f6c66dbfc0f04759b232855f3b7f0a2a
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98676815"
---
# <a name="verify-encryption-status-for-linux"></a>Versleutelings status voor Linux controleren 

Het bereik van dit artikel is het valideren van de versleutelings status van een virtuele machine met behulp van verschillende methoden: de Azure Portal, Power shell, de Azure CLI of het besturings systeem van de virtuele machine (VM). 

U kunt de versleutelings status tijdens of na de versleuteling valideren door ofwel:

- Controleren welke schijven zijn gekoppeld aan een bepaalde virtuele machine. 
- Een query uitvoeren op de versleutelings instellingen op elke schijf, of de schijf is gekoppeld of niet is gekoppeld.

Dit scenario is van toepassing op Azure Disk Encryptione uitbrei dingen met dubbele en eenmalige passes. Linux-distributies zijn de enige omgeving voor dit scenario.

>[!NOTE] 
>We gebruiken variabelen in het hele artikel. Vervang de waarden dienovereenkomstig.

## <a name="portal"></a>Portal

Selecteer in de Azure Portal, in de sectie **extensies** , de Azure Disk Encryption extensie in de lijst. De informatie voor het **status bericht** geeft de huidige versleutelings status aan:

![Portal controle met status, versie en status bericht gemarkeerd](./media/disk-encryption/verify-encryption-linux/portal-check-001.png)

In de lijst met extensies ziet u de bijbehorende versie van de Azure Disk Encryption-extensie. Versie 0. x komt overeen met Azure Disk Encryption Dual Pass en versie 1. x komt overeen met Azure Disk Encryption single pass.

U kunt meer informatie krijgen door de uitbrei ding te selecteren en vervolgens **gedetailleerde status weer geven** te selecteren. De gedetailleerde status van het versleutelings proces wordt weer gegeven in JSON-indeling.

![De controle van de portal met de koppeling gedetailleerde status weer geven gemarkeerd](./media/disk-encryption/verify-encryption-linux/portal-check-002.png)

![Gedetailleerde status in JSON-indeling](./media/disk-encryption/verify-encryption-linux/portal-check-003.png)

Een andere manier om de versleutelings status te valideren, is door te kijken naar het gedeelte **schijf instellingen** .

![Versleutelings status voor besturingssysteem schijf en gegevens schijven](./media/disk-encryption/verify-encryption-linux/portal-check-004.png)

>[!NOTE] 
> Deze status betekent dat de schijven versleutelings instellingen hebben stempel, niet dat ze daad werkelijk zijn versleuteld op het niveau van het besturings systeem.
>
> Op basis van het ontwerp worden de schijven eerst vastgelegd en later versleuteld. Als het versleutelings proces mislukt, kunnen de schijven een stempel hebben, maar niet versleuteld. 
>
> Als u wilt controleren of de schijven echt zijn versleuteld, kunt u de versleuteling van elke schijf op het niveau van het besturings systeem controleren.

## <a name="powershell"></a>PowerShell

U kunt de *algemene* versleutelings status van een versleutelde virtuele machine valideren met behulp van de volgende Power shell-opdrachten:

```azurepowershell
   $VMNAME="VMNAME"
   $RGNAME="RGNAME"
   Get-AzVmDiskEncryptionStatus -ResourceGroupName  ${RGNAME} -VMName ${VMNAME}
```
![Algemene versleutelings status in Power shell](./media/disk-encryption/verify-encryption-linux/verify-status-ps-01.png)

U kunt de versleutelings instellingen van elke schijf vastleggen met behulp van de volgende Power shell-opdrachten.

### <a name="single-pass"></a>Eenmalige Pass
In één keer worden de versleutelings instellingen op elk van de schijven (besturings systeem en gegevens) geplaatst. U kunt de versleutelings instellingen voor een besturingssysteem schijf in één keer vastleggen als volgt:

```powershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$VM = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME}  
 $Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $VM.StorageProfile.OsDisk.Name
 Write-Host "============================================================================================================================================================="
 Write-Host "Encryption Settings:"
 Write-Host "============================================================================================================================================================="
 Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
 Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
 Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
 Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
 Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
 Write-Host "============================================================================================================================================================="
```
![Versleutelings instellingen voor een besturingssysteem schijf](./media/disk-encryption/verify-encryption-linux/verify-os-single-ps-001.png)

Als de schijf geen versleutelings instellingen heeft, is de uitvoer leeg:

![Lege uitvoer](./media/disk-encryption/verify-encryption-linux/os-encryption-settings-2.png)

Gebruik de volgende opdrachten om versleutelings instellingen voor gegevens schijven vast te leggen:

```azurepowershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$VM = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME}
 clear
 foreach ($i in $VM.StorageProfile.DataDisks|ForEach-Object{$_.Name})
 {
 Write-Host "============================================================================================================================================================="
 Write-Host "Encryption Settings:"
 Write-Host "============================================================================================================================================================="
 Write-Host "Checking Disk:" $i
 $Disk=(Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $i)
 Write-Host "Encryption Enable: " $Sourcedisk.EncryptionSettingsCollection.Enabled
 Write-Host "Encryption KeyEncryptionKey: " $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl;
 Write-Host "Encryption DiskEncryptionKey: " $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl;
 Write-Host "============================================================================================================================================================="
 }
```
![Versleutelings instellingen voor gegevens schijven](./media/disk-encryption/verify-encryption-linux/verify-data-single-ps-001.png)

### <a name="dual-pass"></a>Dubbele Pass
In een dubbele fase worden de versleutelings instellingen stempel in het VM-model en niet op elke afzonderlijke schijf.

Gebruik de volgende opdrachten om te controleren of de versleutelings instellingen zijn voorzien van een dubbele fase:

```azurepowershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$vm = Get-AzVm -ResourceGroupName ${RGNAME} -Name ${VMNAME};
$Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $VM.StorageProfile.OsDisk.Name
clear
Write-Host "============================================================================================================================================================="
Write-Host "Encryption Settings:"
Write-Host "============================================================================================================================================================="
Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
Write-Host "============================================================================================================================================================="
```
![Versleutelings instellingen in een dubbele fase](./media/disk-encryption/verify-encryption-linux/verify-dual-ps-001.png)

### <a name="unattached-disks"></a>Niet-gekoppelde schijven

Controleer de versleutelings instellingen voor schijven die niet zijn gekoppeld aan een virtuele machine.

### <a name="managed-disks"></a>Managed Disks
```powershell
$Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName ${TARGETDISKNAME}
Write-Host "============================================================================================================================================================="
Write-Host "Encryption Settings:"
Write-Host "============================================================================================================================================================="
Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
Write-Host "============================================================================================================================================================="
```
## <a name="azure-cli"></a>Azure CLI

U kunt de *algemene* versleutelings status van een versleutelde virtuele machine valideren met behulp van de volgende Azure cli-opdrachten:

```azurecli
VMNAME="VMNAME"
RGNAME="RGNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"
```
![Algemene versleutelings status van de Azure CLI ](./media/disk-encryption/verify-encryption-linux/verify-gen-cli.png)

### <a name="single-pass"></a>Eenmalige Pass
U kunt de versleutelings instellingen voor elke schijf valideren met behulp van de volgende Azure CLI-opdrachten:

```azurecli
az vm encryption show -g ${RGNAME} -n ${VMNAME} --query "disks[*].[name, statuses[*].displayStatus]"  -o table
```

![Instellingen voor gegevens versleuteling](./media/disk-encryption/verify-encryption-linux/data-encryption-settings-2.png)

>[!IMPORTANT]
> Als de schijf geen versleutelings instellingen heeft, wordt de tekst **schijf niet versleuteld** weer geven.

Gebruik de volgende opdrachten om gedetailleerde status-en versleutelings instellingen op te halen.

Besturingssysteemschijf:

```bash
RGNAME="RGNAME"
VMNAME="VNAME"

disk=`az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.osDisk.name -o tsv`
for disk in $disk; do \
echo "============================================================================================================================================================="
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![Gedetailleerde status-en versleutelings instellingen voor de besturingssysteem schijf](./media/disk-encryption/verify-encryption-linux/os-single-cli.png)

Gegevens schijven:

```azurecli
RGNAME="RGNAME"
VMNAME="VMNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"

for disk in `az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks[].name -o tsv`; do \
echo "============================================================================================================================================================="; \
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![Gedetailleerde status-en versleutelings instellingen voor de gegevens schijven](./media/disk-encryption/verify-encryption-linux/data-single-cli.png)

### <a name="dual-pass"></a>Dubbele Pass

```azurecli
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} -o table
```

![Algemene versleutelings instellingen voor Dual Pass via de Azure CLI](./media/disk-encryption/verify-encryption-linux/verify-gen-dual-cli.png)

U kunt ook de versleutelings instellingen in het VM-model opslag profiel van de besturingssysteem schijf controleren:

```bash
disk=`az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.osDisk.name -o tsv`
for disk in $disk; do \
echo "============================================================================================================================================================="; \
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![VM-profiel voor dubbele fase via de Azure CLI](./media/disk-encryption/verify-encryption-linux/verify-vm-profile-dual-cli.png)

### <a name="unattached-disks"></a>Niet-gekoppelde schijven

Controleer de versleutelings instellingen voor schijven die niet zijn gekoppeld aan een virtuele machine.

### <a name="managed-disks"></a>Managed Disks

```bash
RGNAME="RGNAME"
TARGETDISKNAME="DISKNAME"
echo "============================================================================================================================================================="
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
```
### <a name="unmanaged-disks"></a>Onbeheerde schijven

Onbeheerde schijven zijn VHD-bestanden die zijn opgeslagen als pagina-blobs in azure-opslag accounts.

Als u de Details voor een specifieke schijf wilt weer geven, moet u het volgende opgeven:

- De ID van het opslag account dat de schijf bevat.
- Een connection string voor dat specifieke opslag account.
- De naam van de container waarin de schijf wordt opgeslagen.
- De naam van de schijf.

Met deze opdracht worden alle Id's voor al uw opslag accounts weer gegeven:

```azurecli
az storage account list --query [].[id] -o tsv
```
De Id's van het opslag account worden in de volgende vorm weer gegeven:

/Subscriptions/ \<subscription id> /ResourceGroups/ \<resource group name> /providers/Microsoft.Storage/storageAccounts/\<storage account name>

Selecteer de juiste ID en sla deze op in een variabele:
```bash
id="/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name>"
```

Met deze opdracht wordt de connection string voor een bepaald opslag account opgehaald en opgeslagen in een variabele:

```bash
ConnectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
```

Met de volgende opdracht worden alle containers onder een opslag account weer gegeven:
```azurecli
az storage container list --connection-string $ConnectionString --query [].[name] -o tsv
```
De container die wordt gebruikt voor schijven heet meestal vhd's.

Sla de container naam op in een variabele: 
```bash
ContainerName="name of the container"
```

Gebruik deze opdracht om alle blobs in een bepaalde container weer te geven:
```azurecli 
az storage blob list -c ${ContainerName} --connection-string $ConnectionString --query [].[name] -o tsv
```
Kies de schijf die u wilt doorzoeken en sla de naam op in een variabele:
```bash
DiskName="diskname.vhd"
```
Query uitvoeren op de instellingen voor schijf versleuteling:
```azurecli
az storage blob show -c ${ContainerName} --connection-string ${ConnectionString} -n ${DiskName} --query metadata.DiskEncryptionSettings
```

## <a name="operating-system"></a>Besturingssysteem
Controleer of de partities met de gegevens schijf zijn versleuteld (en de besturingssysteem schijf niet).

Wanneer een partitie of schijf wordt versleuteld, wordt deze weer gegeven als een type **crypt** . Wanneer het niet is versleuteld, wordt het weer gegeven als **onderdeel/schijf** type.

```bash
lsblk
```

![De cryptografie laag van het besturings systeem voor een partitie](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer.png)

U kunt meer informatie krijgen met behulp van de volgende **lsblk** -variant. 

U ziet een **crypt** -type-laag die wordt gekoppeld door de extensie. In het volgende voor beeld ziet u logische volumes en normale schijven met **crypto \_ luks FSTYPE**.

```bash
lsblk -o NAME,TYPE,FSTYPE,LABEL,SIZE,RO,MOUNTPOINT
```
![De cryptografie laag van het besturings systeem voor logische volumes en normale schijven](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer-2.png)

Als extra stap kunt u controleren of er sleutels zijn geladen op de gegevens schijf:

```bash
cryptsetup luksDump /dev/VGNAME/LVNAME
```

```bash
cryptsetup luksDump /dev/sdd1
```

En u kunt controleren welke **DM** -apparaten als **crypt** worden weer gegeven:

```bash
dmsetup ls --target crypt
```

## <a name="next-steps"></a>Volgende stappen

- [Problemen met Azure Disk Encryption oplossen](disk-encryption-troubleshooting.md)
