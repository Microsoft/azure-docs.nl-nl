---
title: LVM en RAID op versleutelde apparaten configureren-Azure Disk Encryption
description: Dit artikel bevat instructies voor het configureren van LVM en RAID op versleutelde apparaten voor Linux-Vm's.
author: jofrance
ms.service: virtual-machines
ms.subservice: security
ms.topic: how-to
ms.author: jofrance
ms.date: 03/17/2020
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 46d2c039806e4e6a72e091458d44e7b21b3dfa70
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843516"
---
# <a name="configure-lvm-and-raid-on-encrypted-devices"></a>LVM en RAID op versleutelde apparaten configureren

Dit artikel is een stapsgewijze procedure voor het uitvoeren van LVM (Logical Volume Management) en RAID op versleutelde apparaten. Het proces is van toepassing op de volgende omgevingen:

- Linux-distributies
    - RHEL 7,6 +
    - Ubuntu 18.04 +
    - SUSE 12 +
- Azure Disk Encryption extensie voor één stap
- Azure Disk Encryption-extensie voor dubbel slagen


## <a name="scenarios"></a>Scenario's

De procedures in dit artikel bieden ondersteuning voor de volgende scenario's:  

- LVM boven op versleutelde apparaten configureren (LVM-on-cryptografie)
- RAID configureren boven op versleutelde apparaten (RAID-on-cryptografie)

Nadat de onderliggende apparaten zijn versleuteld, kunt u de LVM-of RAID-structuren boven op die versleutelde laag maken. 

De fysieke volumes (PVs) worden boven op de versleutelde laag gemaakt. De fysieke volumes worden gebruikt om de volume groep te maken. U maakt de volumes en voegt de vereiste vermeldingen toe aan/etc/fstab. 

![Diagram van de lagen van LVM-structuren](./media/disk-encryption/lvm-raid-on-crypt/000-lvm-raid-crypt-diagram.png)

Op soort gelijke wijze wordt het RAID-apparaat boven op de versleutelde laag op de schijven gemaakt. Een bestands systeem wordt boven op het RAID-apparaat gemaakt en als een normaal apparaat toegevoegd aan bestand/etc/fstab.

## <a name="considerations"></a>Overwegingen

U wordt aangeraden LVM-on-cryptografie te gebruiken. RAID is een optie wanneer LVM niet kan worden gebruikt vanwege specifieke beperkingen voor toepassingen of omgevingen.

U gebruikt de **EncryptFormatAll** -optie. Zie [de functie EncryptFormatAll gebruiken voor gegevens schijven op virtuele Linux-machines](./disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms)voor meer informatie over deze optie.

Hoewel u deze methode kunt gebruiken wanneer u ook het besturings systeem versleutelt, worden de gegevens stations hier gewoon versleuteld.

In de procedures wordt ervan uitgegaan dat u de vereisten in [Azure Disk Encryption scenario's voor Linux-vm's](./disk-encryption-linux.md) en in Quick start al hebt bekeken [: een virtuele Linux-machine maken en versleutelen met de Azure cli](./disk-encryption-cli-quickstart.md).

De Azure Disk Encryption Dual-Pass-versie bevindt zich op een afschaffing-pad en mag niet meer worden gebruikt voor nieuwe versleuteling.

## <a name="general-steps"></a>Algemene stappen

Wanneer u de configuraties ' op cryptografie ' gebruikt, gebruikt u het proces dat wordt beschreven in de volgende procedures.

>[!NOTE] 
>We gebruiken variabelen in het hele artikel. Vervang de waarden dienovereenkomstig.

### <a name="deploy-a-vm"></a>Een virtuele machine implementeren 
De volgende opdrachten zijn optioneel, maar we raden u aan deze toe te passen op een nieuwe geïmplementeerde virtuele machine (VM).

PowerShell:

```powershell
New-AzVm -ResourceGroupName ${RGNAME} `
-Name ${VMNAME} `
-Location ${LOCATION} `
-Size ${VMSIZE} `
-Image ${OSIMAGE} `
-Credential ${creds} `
-Verbose
```
Azure CLI:

```bash
az vm create \
-n ${VMNAME} \
-g ${RGNAME} \
--image ${OSIMAGE} \
--admin-username ${username} \
--admin-password ${password} \
-l ${LOCATION} \
--size ${VMSIZE} \
-o table
```
### <a name="attach-disks-to-the-vm"></a>Schijven koppelen aan de virtuele machine
Herhaal de volgende opdrachten voor het `$N` aantal nieuwe schijven dat u wilt koppelen aan de virtuele machine.

PowerShell:

```powershell
$storageType = 'Standard_LRS'
$dataDiskName = ${VMNAME} + '_datadisk0'
$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $LOCATION -CreateOption Empty -DiskSizeGB 5
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName ${RGNAME}
$vm = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME} 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 0
Update-AzVM -VM ${VM} -ResourceGroupName ${RGNAME}
```

Azure CLI:

```bash
az vm disk attach \
-g ${RGNAME} \
--vm-name ${VMNAME} \
--name ${VMNAME}datadisk1 \
--size-gb 5 \
--new \
-o table
```

### <a name="verify-that-the-disks-are-attached-to-the-vm"></a>Controleren of de schijven zijn gekoppeld aan de VM
PowerShell:
```powershell
$VM = Get-AzVM -ResourceGroupName ${RGNAME} -Name ${VMNAME}
$VM.StorageProfile.DataDisks | Select-Object Lun,Name,DiskSizeGB
```
![Lijst met gekoppelde schijven in Power shell](./media/disk-encryption/lvm-raid-on-crypt/001-lvm-raid-check-disks-powershell.png)

Azure CLI:

```bash
az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks -o table
```
![Lijst met gekoppelde schijven in de Azure CLI](./media/disk-encryption/lvm-raid-on-crypt/002-lvm-raid-check-disks-cli.png)

Portal:

![Lijst met gekoppelde schijven in de portal](./media/disk-encryption/lvm-raid-on-crypt/003-lvm-raid-check-disks-portal.png)

Besturingssysteem:

```bash
lsblk 
```
![Lijst met gekoppelde schijven in het besturings systeem](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="configure-the-disks-to-be-encrypted"></a>De schijven configureren die moeten worden versleuteld
Deze configuratie wordt uitgevoerd op het niveau van het besturings systeem. De bijbehorende schijven zijn geconfigureerd voor een traditionele versleuteling via Azure Disk Encryption:

- Bestands systemen worden boven op de schijven gemaakt.
- Tijdelijke koppel punten worden gemaakt om de bestands systemen te koppelen.
- Bestands systemen worden geconfigureerd op bestand/etc/fstab om te worden gekoppeld tijdens het opstarten.

Controleer de stationsletter die aan de nieuwe schijven is toegewezen. In dit voor beeld gebruiken we vier gegevens schijven.

```bash
lsblk 
```
![Gegevens schijven die zijn gekoppeld aan het besturings systeem](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="create-a-file-system-on-top-of-each-disk"></a>Een bestands systeem boven op elke schijf maken
Met deze opdracht wordt het maken van een ext4-bestands systeem herhaald op elke schijf die is gedefinieerd in het deel van de for-cyclus.

```bash
for disk in c d e f; do echo mkfs.ext4 -F /dev/sd${disk}; done |bash
```
![Maken van een ext4-bestands systeem](./media/disk-encryption/lvm-raid-on-crypt/005-lvm-raid-create-temp-fs.png)

Zoek de UUID (Universally Unique Identifier) van de bestands systemen die u onlangs hebt gemaakt, maak een tijdelijke map, voeg de bijbehorende vermeldingen toe aan bestand/etc/fstab en koppel alle bestands systemen.

Met deze opdracht wordt ook herhaald op elke schijf die is gedefinieerd in het deel van de cyclus ' voor ':

```bash
for disk in c d e f; do diskuuid="$(blkid -s UUID -o value /dev/sd${disk})"; \
mkdir /tempdata${disk}; \
echo "UUID=${diskuuid} /tempdata${disk} ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
``` 

### <a name="verify-that-the-disks-are-mounted-properly"></a>Controleren of de schijven goed zijn gekoppeld
```bash
lsblk
```
![Lijst met gekoppelde tijdelijke bestands systemen](./media/disk-encryption/lvm-raid-on-crypt/006-lvm-raid-verify-temp-fs.png)

Controleer ook of de schijven zijn geconfigureerd:

```bash
cat /etc/fstab
```
![Configuratie-informatie via fstab](./media/disk-encryption/lvm-raid-on-crypt/007-lvm-raid-verify-temp-fstab.png)

### <a name="encrypt-the-data-disks"></a>De gegevens schijven versleutelen
Power shell met een sleutel versleutelings sleutel (KEK):

```powershell
$sequenceVersion = [Guid]::NewGuid() 
Set-AzVMDiskEncryptionExtension -ResourceGroupName $RGNAME `
-VMName ${VMNAME} `
-DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
-DiskEncryptionKeyVaultId $KeyVaultResourceId `
-KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
-KeyEncryptionKeyVaultId $KeyVaultResourceId `
-VolumeType 'DATA' `
-EncryptFormatAll `
-SequenceVersion $sequenceVersion `
-skipVmBackup;
```

Azure CLI met behulp van een KEK:

```bash
az vm encryption enable \
--resource-group ${RGNAME} \
--name ${VMNAME} \
--disk-encryption-keyvault ${KEYVAULTNAME} \
--key-encryption-key ${KEYNAME} \
--key-encryption-keyvault ${KEYVAULTNAME} \
--volume-type "DATA" \
--encrypt-format-all \
-o table
```
### <a name="verify-the-encryption-status"></a>De versleutelings status controleren

Ga door naar de volgende stap alleen wanneer alle schijven zijn versleuteld.

PowerShell:

```powershell
Get-AzVmDiskEncryptionStatus -ResourceGroupName ${RGNAME} -VMName ${VMNAME}
```
![Versleutelings status in Power shell](./media/disk-encryption/lvm-raid-on-crypt/008-lvm-raid-verify-encryption-status-ps.png)

Azure CLI:

```bash
az vm encryption show -n ${VMNAME} -g ${RGNAME} -o table
```
![Versleutelings status in de Azure CLI](./media/disk-encryption/lvm-raid-on-crypt/009-lvm-raid-verify-encryption-status-cli.png)

Portal:

![Versleutelings status in de portal](./media/disk-encryption/lvm-raid-on-crypt/010-lvm-raid-verify-encryption-status-portal.png)

BESTURINGSSYSTEEM niveau:

```bash
lsblk
```
![Versleutelings status in het besturings systeem](./media/disk-encryption/lvm-raid-on-crypt/011-lvm-raid-verify-encryption-status-os.png)

De uitbrei ding voegt de bestands systemen toe aan/var/lib/azure_disk_encryption_config/azure_crypt_mount (een oude versleuteling) of/etc/crypttab (nieuwe versleuteling).

>[!NOTE] 
>Wijzig geen van deze bestanden.

Dit bestand zorgt ervoor dat deze schijven tijdens het opstart proces worden geactiveerd, zodat LVM of RAID ze later kunnen gebruiken. 

U hoeft zich geen zorgen te maken over de koppel punten van dit bestand. Azure Disk Encryption verliest de mogelijkheid om de schijven te verkrijgen die zijn gekoppeld als een normaal bestands systeem nadat u een fysiek volume of een RAID-apparaat hebt gemaakt boven op die versleutelde apparaten. (Hiermee wordt de bestandssysteem indeling verwijderd die we tijdens het voorbereidings proces hebben gebruikt.)

### <a name="remove-the-temporary-folders-and-temporary-fstab-entries"></a>Tijdelijke mappen en tijdelijke fstab-vermeldingen verwijderen
U ontkoppelt de bestands systemen op de schijven die worden gebruikt als onderdeel van LVM.

```bash
for disk in c d e f; do unmount /tempdata${disk}; done
```
En verwijder de bestand/etc/fstab-vermeldingen:

```bash
vi /etc/fstab
```
### <a name="verify-that-the-disks-are-not-mounted-and-that-the-entries-on-etcfstab-were-removed"></a>Controleer of de schijven niet zijn gekoppeld en of de vermeldingen op bestand/etc/fstab zijn verwijderd

```bash
lsblk
```
![Verificatie dat tijdelijke bestands systemen niet gekoppeld zijn](./media/disk-encryption/lvm-raid-on-crypt/012-lvm-raid-verify-disks-not-mounted.png)

Controleer of de schijven zijn geconfigureerd:
```bash
cat /etc/fstab
```
![Verificatie dat tijdelijke fstab-vermeldingen worden verwijderd](./media/disk-encryption/lvm-raid-on-crypt/013-lvm-raid-verify-fstab-temp-removed.png)

## <a name="steps-for-lvm-on-crypt"></a>Stappen voor LVM-on-cryptografie
Nu de onderliggende schijven zijn versleuteld, kunt u de LVM-structuren maken.

In plaats van de naam van het apparaat te gebruiken, gebruikt u de/dev/mapper-paden voor elk van de schijven om een fysiek volume te maken (op de Crypt-laag boven op de schijf, niet op de schijf zelf).

### <a name="configure-lvm-on-top-of-the-encrypted-layers"></a>LVM configureren boven op de versleutelde lagen
#### <a name="create-the-physical-volumes"></a>De fysieke volumes maken
Er wordt een waarschuwing weer gegeven waarin u wordt gevraagd of u de hand tekening van het bestands systeem OK wilt wissen. Ga door met het invoeren van **y** of gebruik **echo ' y '** zoals weer gegeven:

```bash
echo "y" | pvcreate /dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052
echo "y" | pvcreate /dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1
echo "y" | pvcreate /dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a
echo "y" | pvcreate /dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![Verificatie dat een fysiek volume is gemaakt](./media/disk-encryption/lvm-raid-on-crypt/014-lvm-raid-pvcreate.png)

>[!NOTE] 
>De/dev/mapper/device-namen moeten worden vervangen door de werkelijke waarden op basis van de uitvoer van **lsblk**.

#### <a name="verify-the-information-for-physical-volumes"></a>Controleer de gegevens voor fysieke volumes
```bash
pvs
```

![Informatie voor fysieke volumes](./media/disk-encryption/lvm-raid-on-crypt/015-lvm-raid-pvs.png)

#### <a name="create-the-volume-group"></a>De volume groep maken
Maak de volume groep met behulp van dezelfde apparaten die al zijn geïnitialiseerd:

```bash
vgcreate vgdata /dev/mapper/
```

### <a name="check-the-information-for-the-volume-group"></a>Controleer de informatie voor de volume groep

```bash
vgdisplay -v vgdata
```
```bash
pvs
```
![Informatie voor de volume groep](./media/disk-encryption/lvm-raid-on-crypt/016-lvm-raid-pvs-on-vg.png)

#### <a name="create-logical-volumes"></a>Logische volumes maken

```bash
lvcreate -L 10G -n lvdata1 vgdata
lvcreate -L 7G -n lvdata2 vgdata
``` 

#### <a name="check-the-created-logical-volumes"></a>Controleer de gemaakte logische volumes

```bash
lvdisplay
lvdisplay vgdata/lvdata1
lvdisplay vgdata/lvdata2
```
![Informatie voor logische volumes](./media/disk-encryption/lvm-raid-on-crypt/017-lvm-raid-lvs.png)

#### <a name="create-file-systems-on-top-of-the-structures-for-logical-volumes"></a>Bestands systemen boven op de structuren maken voor logische volumes

```bash
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata1
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata2
```

#### <a name="create-the-mount-points-for-the-new-file-systems"></a>De koppel punten maken voor de nieuwe bestands systemen

```bash
mkdir /data0
mkdir /data1
```

#### <a name="add-the-new-file-systems-to-etcfstab-and-mount-them"></a>Voeg de nieuwe bestands systemen toe aan bestand/etc/fstab en koppel ze

```bash
echo "/dev/mapper/vgdata-lvdata1 /data0 ext4 defaults,nofail 0 0" >>/etc/fstab
echo "/dev/mapper/vgdata-lvdata2 /data1 ext4 defaults,nofail 0 0" >>/etc/fstab
mount -a
```

#### <a name="verify-that-the-new-file-systems-are-mounted"></a>Controleren of de nieuwe bestands systemen zijn gekoppeld

```bash
lsblk -fs
df -h
```
![Scherm afbeelding toont een console venster met bestands systemen die zijn gekoppeld als data0 en bestand1.](./media/disk-encryption/lvm-raid-on-crypt/018-lvm-raid-lsblk-after-lvm.png)

Op deze variatie van **lsblk** worden de apparaten waarop de afhankelijkheden in omgekeerde volg orde worden weer gegeven. Met deze optie kunt u de apparaten die zijn gegroepeerd op het logische volume identificeren in plaats van de oorspronkelijke/dev/sd [disk]-apparaatnamen.

Het is belang rijk om ervoor te zorgen dat de **niet-werkende** optie wordt toegevoegd aan de koppel punt opties van de LVM-volumes die boven op een apparaat zijn versleuteld via Azure Disk Encryption. Hiermee wordt voor komen dat het besturings systeem vastloopt tijdens het opstart proces (of in de onderhouds modus).

Als **u de optie niet gebruiken** gebruikt:

- Het besturings systeem wordt nooit weer gegeven in het stadium waar Azure Disk Encryption wordt gestart en de gegevens schijven worden ontgrendeld en gekoppeld. 
- De versleutelde schijven worden ontgrendeld aan het einde van het opstart proces. De LVM-volumes en bestands systemen worden automatisch gekoppeld totdat Azure Disk Encryption ze ontgrendelt. 

U kunt de virtuele machine opnieuw opstarten testen en valideren dat de bestands systemen na het opstarten ook automatisch worden gekoppeld. Dit proces kan enkele minuten duren, afhankelijk van het aantal en de grootte van het bestands systeem.

#### <a name="reboot-the-vm-and-verify-after-reboot"></a>De virtuele machine opnieuw opstarten en controleren na opnieuw opstarten

```bash
shutdown -r now
```
```bash
lsblk
df -h
```
## <a name="steps-for-raid-on-crypt"></a>Stappen voor RAID-op-cryptografie
Nu de onderliggende schijven zijn versleuteld, kunt u door gaan met het maken van de RAID-structuren. Het proces is hetzelfde als het voor LVM, maar in plaats van de apparaatnaam te gebruiken, gebruikt u de/dev/mapper-paden voor elke schijf.

#### <a name="configure-raid-on-top-of-the-encrypted-layer-of-the-disks"></a>Configureer RAID boven op de versleutelde laag van de schijven
```bash
mdadm --create /dev/md10 \
--level 0 \
--raid-devices=4 \
/dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052 \
/dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1 \
/dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a \
/dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![Informatie voor geconfigureerde RAID via de mdadm-opdracht](./media/disk-encryption/lvm-raid-on-crypt/019-lvm-raid-md-creation.png)

>[!NOTE] 
>De/dev/mapper/device-namen moeten worden vervangen door de werkelijke waarden, op basis van de uitvoer van **lsblk**.

### <a name="checkmonitor-raid-creation"></a>RAID maken controleren/controleren
```bash
watch -n1 cat /proc/mdstat
mdadm --examine /dev/mapper/[]
mdadm --detail /dev/md10
```
![Status van RAID](./media/disk-encryption/lvm-raid-on-crypt/020-lvm-raid-md-details.png)

### <a name="create-a-file-system-on-top-of-the-new-raid-device"></a>Een bestands systeem maken boven op het nieuwe RAID-apparaat
```bash
mkfs.ext4 /dev/md10
```

Maak een nieuw koppel punt voor het bestands systeem, voeg het nieuwe bestands systeem toe aan bestand/etc/fstab en koppel het.

```bash
for device in md10; do diskuuid="$(blkid -s UUID -o value /dev/${device})"; \
mkdir /raiddata; \
echo "UUID=${diskuuid} /raiddata ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
```

Controleer of het nieuwe bestands systeem is gekoppeld:

```bash
lsblk -fs
df -h
```
![Scherm afbeelding toont een console venster met een bestands systeem dat is gekoppeld als raiddata.](./media/disk-encryption/lvm-raid-on-crypt/021-lvm-raid-lsblk-md-details.png)

Het is belang rijk om ervoor te zorgen dat de **niet-werkende** optie wordt toegevoegd aan de koppel punt opties van de RAID-volumes die boven op een apparaat zijn versleuteld via Azure Disk Encryption. Hiermee wordt voor komen dat het besturings systeem vastloopt tijdens het opstart proces (of in de onderhouds modus).

Als **u de optie niet gebruiken** gebruikt:

- Het besturings systeem wordt nooit weer gegeven in het stadium waar Azure Disk Encryption wordt gestart en de gegevens schijven worden ontgrendeld en gekoppeld.
- De versleutelde schijven worden ontgrendeld aan het einde van het opstart proces. De RAID-volumes en bestands systemen worden automatisch gekoppeld totdat Azure Disk Encryption ze ontgrendelt.

U kunt de virtuele machine opnieuw opstarten testen en valideren dat de bestands systemen na het opstarten ook automatisch worden gekoppeld. Dit proces kan enkele minuten duren, afhankelijk van het aantal en de grootte van het bestands systeem.

```bash
shutdown -r now
```

En wanneer u zich kunt aanmelden:

```bash
lsblk
df -h
```
## <a name="next-steps"></a>Volgende stappen

- [Grootte van beheer apparaten voor logische volumes versleutelen met Azure Disk Encryption](how-to-resize-encrypted-lvm.md)
- [Problemen met Azure Disk Encryption oplossen](disk-encryption-troubleshooting.md)
