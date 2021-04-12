---
title: Voorbeeldscripts voor Azure Disk Encryption
description: Dit artikel is de bijlage voor Microsoft Azure schijf versleuteling voor Linux-Vm's.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: how-to
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: f11677d9ebc31f1c1f7cc6332b07b69f8e35ad52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102561174"
---
# <a name="azure-disk-encryption-sample-scripts-for-linux-vms"></a>Voorbeeld scripts voor virtuele Linux-machines Azure Disk Encryption

Dit artikel bevat voorbeeld scripts voor het voorbereiden van vooraf versleutelde Vhd's en andere taken.  

> [!NOTE]
> Alle scripts verwijzen naar de nieuwste, niet-AAD-versie van ADE, tenzij anders vermeld.

## <a name="sample-powershell-scripts-for-azure-disk-encryption"></a>Power shell-voorbeeld scripts voor Azure Disk Encryption 

- **Alle versleutelde virtuele machines in uw abonnement weer geven**
  
  Met [Dit Power shell-script](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/Find_1passAdeVersion_VM.ps1)vindt u alle met ade versleutelde vm's en de extensie versie, in alle resource groepen die aanwezig zijn in een abonnement.

  Daarnaast worden met deze cmdlets alle met ADE versleutelde Vm's weer gegeven (maar niet de extensie versie):

   ```azurepowershell-interactive
   $osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
   $dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
   Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
   ```

- **Alle versleutelde VMSS-instanties in uw abonnement weer geven**
    
    Met [Dit Power shell-script](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/Find_1passAdeVersion_VMSS.ps1)vindt u alle met ade versleutelde VMSS-instanties en de extensie versie, in alle resource groepen die aanwezig zijn in een abonnement.

- **Alle schijf versleutelings geheimen weer geven die worden gebruikt voor het versleutelen van Vm's in een sleutel kluis** 

   ```azurepowershell-interactive
   Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
   ```

### <a name="using-the-azure-disk-encryption-prerequisites-powershell-script"></a>Het Power shell-script voor de Azure Disk Encryption vereisten gebruiken
Als u al bekend bent met de vereisten voor Azure Disk Encryption, kunt u het [Power shell-script Azure Disk Encryption vereisten](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 )gebruiken. Zie [Quick Start a VM versleutelen](disk-encryption-powershell-quickstart.md)voor een voor beeld van het gebruik van dit Power shell-script. U kunt de opmerkingen uit een sectie van het script verwijderen, beginnend bij regel 211, om alle schijven te versleutelen voor bestaande virtuele machines in een bestaande resource groep. 

In de volgende tabel ziet u welke para meters kunnen worden gebruikt in het Power shell-script: 


|Parameter|Beschrijving|Ingevuld?|
|------|------|------|
|$resourceGroupName| De naam van de resource groep waartoe de sleutel kluis behoort.  Er wordt een nieuwe resource groep met deze naam gemaakt als er nog geen bestaat.| Waar|
|$keyVaultName|De naam van de kluis waarin de versleutelings sleutels moeten worden geplaatst. Er wordt een nieuwe kluis met deze naam gemaakt als er nog geen bestaat.| Waar|
|$location|Locatie van de sleutel kluis. Zorg ervoor dat de sleutel kluis en de virtuele machines die moeten worden gecodeerd, zich op dezelfde locatie bevinden. Haal een locatielijst op met `Get-AzLocation`.|Waar|
|$subscriptionId|De id van het Azure-abonnement dat moet worden gebruikt.  U kunt uw abonnements-ID ophalen met `Get-AzSubscription`.|Waar|
|$aadAppName|De naam van de Azure AD-toepassing die wordt gebruikt om geheimen te schrijven naar de sleutel kluis. Als er nog geen toepassing met deze naam bestaat, wordt deze aangemaakt. Als deze app al bestaat, geeft u de para meter aadClientSecret door aan het script.|Niet waar|
|$aadClientSecret|Client geheim van de Azure AD-toepassing die eerder is gemaakt.|Niet waar|
|$keyEncryptionKeyName|Naam van optionele coderings sleutel in de sleutel kluis. Er wordt een nieuwe sleutel met deze naam gemaakt als deze nog niet bestaat.|Niet waar|

### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Vm's versleutelen of ontsleutelen zonder Azure AD-app

- [Schijf versleuteling inschakelen op een bestaande of actieve virtuele Linux-machine](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)  
- [Versleuteling uitschakelen op een actieve Linux-VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) 
    - Versleuteling uitschakelen is alleen toegestaan op gegevens volumes voor Linux-Vm's.  

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Vm's versleutelen of ontsleutelen met een Azure AD-app (vorige versie)
 
- [Schijf versleuteling inschakelen op een bestaande of actieve virtuele Linux-machine](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)    


-  [Versleuteling uitschakelen op een actieve Linux-VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm) 
    - Versleuteling uitschakelen is alleen toegestaan op gegevens volumes voor Linux-Vm's. 


- [Een nieuwe versleutelde beheerde schijf maken op basis van een vooraf versleutelde VHD/opslag-BLOB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Hiermee maakt u een nieuwe versleutelde beheerde schijf met een vooraf versleutelde VHD en de bijbehorende versleutelings instellingen

## <a name="encrypting-an-os-drive-on-a-running-linux-vm"></a>Een OS-station versleutelen op een actieve Linux-VM

### <a name="prerequisites-for-os-disk-encryption"></a>Vereisten voor de versleuteling van de besturingssysteem schijf

* De virtuele machine moet gebruikmaken van een distributie die compatibel is met de besturingssysteem schijf versleuteling, zoals vermeld in de [Azure Disk Encryption ondersteunde besturings systemen](disk-encryption-overview.md#supported-vms) 
* De virtuele machine moet worden gemaakt op basis van de Marketplace-installatie kopie in Azure Resource Manager.
* Azure VM met ten minste 4 GB RAM (aanbevolen grootte is 7 GB).
* (Voor RHEL en CentOS) Schakel SELinux uit. Zie ' 4.4.2 ' om SELinux uit te scha kelen. SELinux uitschakelen in de [Beheerders handleiding](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) van de SELinux-gebruiker op de VM.
* Nadat u SELinux hebt uitgeschakeld, start u de VM ten minste één keer opnieuw op.

### <a name="steps"></a>Stappen
1. Maak een virtuele machine met behulp van een van de eerder opgegeven distributies.

   Voor CentOS 7,2 wordt versleuteling van de besturingssysteem schijf ondersteund via een speciale installatie kopie. Als u deze installatie kopie wilt gebruiken, geeft u "7,2 n" op als de SKU wanneer u de virtuele machine maakt:

   ```powershell
    Set-AzVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
   ```
2. Configureer de virtuele machine op basis van uw behoeften. Als u alle stations van het (OS + data) wilt versleutelen, moeten de gegevens stations worden opgegeven en gekoppeld vanuit/etc/fstab.

   > [!NOTE]
   > UUID gebruiken =... gegevens stations opgeven in bestand/etc/fstab in plaats van de naam van het blok apparaat op te geven (bijvoorbeeld/dev/sdb1). Tijdens de versleuteling wordt de volg orde van de stations gewijzigd op de VM. Als uw VM afhankelijk is van een specifieke volg orde van blok apparaten, kan deze na de versleuteling niet worden gekoppeld.

3. Meld u af bij de SSH-sessies.

4. Als u het besturings systeem wilt versleutelen, geeft u volumeType op als **alle** of als **besturings systeem** wanneer u versleuteling inschakelt.

   > [!NOTE]
   > Alle gebruikers ruimte processen die niet worden uitgevoerd als `systemd` Services, moeten worden afgebroken met een `SIGKILL` . Start de VM opnieuw op. Wanneer u schijf versleuteling van het besturings systeem op een actieve virtuele machine inschakelt, moet u uitval tijd van de VM plannen.

5. Controleer regel matig de voortgang van de versleuteling met behulp van de instructies in de [volgende sectie](#monitoring-os-encryption-progress).

6. Nadat Get-AzVmDiskEncryptionStatus ' VMRestartPending ' hebt weer gegeven, start u de VM opnieuw op door u aan te melden of door de portal, Power shell of CLI te gebruiken.
    ```powershell
    C:\> Get-AzVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
   Voordat u de computer opnieuw opstart, wordt u aangeraden de [Diagnostische gegevens](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) over het opstarten van de virtuele machine op te slaan.

## <a name="monitoring-os-encryption-progress"></a>Voortgang van bewaking van besturingssysteem versleuteling
U kunt de voortgang van de besturingssysteem versleuteling op drie manieren controleren:

* Gebruik de `Get-AzVmDiskEncryptionStatus` cmdlet en Inspecteer het veld ProgressMessage:
    ```powershell
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
  Nadat de VM de ' versleuteling van het besturings systeem heeft gestart ' bereikt, duurt het ongeveer 40 tot 50 minuten op een back-upvm met Premium-opslag.

  Vanwege een [probleem #388](https://github.com/Azure/WALinuxAgent/issues/388) in WALinuxAgent `OsVolumeEncrypted` en wordt `DataVolumesEncrypted` weer gegeven `Unknown` in een aantal distributies. Met WALinuxAgent versie 2.1.5 en hoger wordt dit probleem automatisch opgelost. Als u `Unknown` in de uitvoer ziet, kunt u de status van de schijf versleuteling controleren met behulp van de Azure resource Explorer.

  Ga naar [Azure resource Explorer](https://resources.azure.com/)en vouw vervolgens deze hiërarchie uit in het deel venster selectie aan de linkerkant:

  ```
  |-- subscriptions
     |-- [Your subscription]
          |-- resourceGroups
               |-- [Your resource group]
                    |-- providers
                         |-- Microsoft.Compute
                              |-- virtualMachines
                                   |-- [Your virtual machine]
                                        |-- InstanceView
  ```                

  Schuif in de InstanceView omlaag om de versleutelings status van uw stations weer te geven.

  ![Weer gave van VM-instantie](./media/disk-encryption/vm-instanceview.png)

* Bekijk de [Diagnostische gegevens over opstarten](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/). Berichten van de ADE-extensie moeten worden voorafgegaan door `[AzureDiskEncryption]` .

* Meld u aan bij de virtuele machine via SSH en haal het extensie logboek op uit:

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

  U wordt aangeraden zich niet aan te melden bij de virtuele machine wanneer de besturingssysteem versleuteling wordt uitgevoerd. Kopieer de Logboeken alleen als de andere twee methoden zijn mislukt.

## <a name="prepare-a-pre-encrypted-linux-vhd"></a>Een vooraf versleutelde Linux-VHD voorbereiden
De voor bereiding van vooraf versleutelde Vhd's kan variëren, afhankelijk van de distributie. Voor beelden van het voorbereiden van Ubuntu 16, openSUSE 13,2 en CentOS 7 zijn beschikbaar. 

### <a name="ubuntu-16"></a>Ubuntu 16
Configureer de versleuteling tijdens de distributie-installatie door de volgende stappen uit te voeren:

1. Selecteer **versleutelde volumes configureren** wanneer u de schijven partitioneert.

   ![Ubuntu 16,04-installatie-versleutelde volumes configureren](./media/disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. Maak een afzonderlijk opstart station dat niet moet worden versleuteld. Versleutel uw basis station.

   ![Ubuntu 16,04-installatie: Selecteer apparaten die u wilt versleutelen](./media/disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. Geef een wachtwoordzin op. Dit is de wachtwoordzin die u hebt geüpload naar de sleutel kluis.

   ![Ubuntu 16,04-installatie: wachtwoordzin opgeven](./media/disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. Partitioneren volt ooien.

   ![Ubuntu 16,04 instellen-partitioneren volt ooien](./media/disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. Wanneer u de virtuele machine opstart en om een wachtwoordzin wordt gevraagd, gebruikt u de wachtwoordzin die u in stap 3 hebt gegeven.

   ![Ubuntu 16,04-installatie: Geef de wachtwoordzin op tijdens het opstarten](./media/disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. Bereid de virtuele machine voor op het uploaden naar Azure met behulp van [deze instructies](./create-upload-ubuntu.md?toc=/azure/virtual-machines/linux/toc.json). Voer de laatste stap nog niet uit (de inrichting van de virtuele machine wordt ongedaan gemaakt).

Configureer versleuteling om met Azure te werken door de volgende stappen uit te voeren:

1. Maak een bestand onder/usr/local/sbin/azure_crypt_key. sh met de inhoud van het volgende script. Let op de naam van het bestand, omdat het de wachtwoordzin is die door Azure wordt gebruikt.

    ```bash
    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    modprobe ntfs >/dev/null 2>&1
    sleep 2
    OPENED=0
    cd /sys/block
    for DEV in sd*; do

        echo "> Trying device: $DEV ..." >&2
        mount -t vfat -r /dev/${DEV}1 $MountPoint >/dev/null||
        mount -t ntfs -r /dev/${DEV}1 $MountPoint >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi
   ```

2. Wijzig de cryptografie configuratie in */etc/crypttab*. Dit ziet er als volgt uit:
   ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

4. Uitvoer bare machtigingen toevoegen aan het script:
   ```
    chmod +x /usr/local/sbin/azure_crypt_key.sh
   ```
5. */Etc/initramfs-tools/modules* bewerken door regels toe te voegen:
   ```
    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1
   ```
6. Voer uit `update-initramfs -u -k all` om de initramfs bij te werken, zodat de actie kan worden doorgevoerd `keyscript` .

7. Nu kunt u de inrichting van de virtuele machine ongedaan maken.

   ![Ubuntu 16,04-installatie-update-initramfs](./media/disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. Ga door naar de volgende stap en upload uw VHD naar Azure.

### <a name="opensuse-132"></a>openSUSE 13,2
Voer de volgende stappen uit om versleuteling te configureren tijdens de distributie-installatie:
1. Wanneer u de schijven partitioneert, selecteert u **volume groep versleutelen** en voert u vervolgens een wacht woord in. Dit is het wacht woord dat u naar uw sleutel kluis uploadt.

   ![openSUSE 13,2 Setup-volume groep versleutelen](./media/disk-encryption/opensuse-encrypt-fig1.png)

2. Start de virtuele machine op met uw wacht woord.

   ![openSUSE 13,2-installatie: Geef de wachtwoordzin op tijdens het opstarten](./media/disk-encryption/opensuse-encrypt-fig2.png)

3. Bereid de virtuele machine voor op het uploaden naar Azure door de instructies in [een SLES-of openSUSE-virtuele machine voorbereiden voor Azure](./suse-create-upload-vhd.md?toc=/azure/virtual-machines/linux/toc.json#prepare-opensuse-131)te volgen. Voer de laatste stap nog niet uit (de inrichting van de virtuele machine wordt ongedaan gemaakt).

Als u versleuteling wilt configureren voor gebruik met Azure, voert u de volgende stappen uit:
1. Bewerk de/etc/Dracut.conf en voeg de volgende regel toe:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. Commentaar op deze regels aan het einde van het bestand/usr/lib/Dracut/modules.d/90crypt/module-Setup.sh:
   ```bash
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
   ```

3. Voeg de volgende regel toe aan het begin van het bestand/usr/lib/Dracut/modules.d/90crypt/parse-crypt.sh:
   ```bash
    DRACUT_SYSTEMD=0
   ```
   En wijzig alle instanties van:
   ```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
   ```
   in:
   ```bash
    if [ 1 ]; then
   ```
4. Bewerk/usr/lib/Dracut/modules.d/90crypt/cryptroot-Ask.sh en voeg deze toe aan het # open LUKS-apparaat:

    ```bash
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```
5. Voer uit `/usr/sbin/dracut -f -v` om de initrd bij te werken.

6. U kunt nu de inrichting van de virtuele machine ongedaan maken en uw VHD uploaden naar Azure.

### <a name="centos-7-and-rhel-7"></a>CentOS 7 en RHEL 7

Voer de volgende stappen uit om versleuteling te configureren tijdens de distributie-installatie:
1. Selecteer **mijn gegevens versleutelen** bij het partitioneren van schijven.

   ![CentOS 7 Setup-installatie bestemming](./media/disk-encryption/centos-encrypt-fig1.png)

2. Zorg ervoor dat **versleutelen** is geselecteerd voor de hoofd partitie.

   ![CentOS 7 Setup: Selecteer versleutelen voor basis partitie](./media/disk-encryption/centos-encrypt-fig2.png)

3. Geef een wachtwoordzin op. Dit is de wachtwoordzin die u naar uw sleutel kluis uploadt.

   ![CentOS 7 instellen: wachtwoordzin opgeven](./media/disk-encryption/centos-encrypt-fig3.png)

4. Wanneer u de virtuele machine opstart en om een wachtwoordzin wordt gevraagd, gebruikt u de wachtwoordzin die u in stap 3 hebt gegeven.

   ![CentOS 7 Setup: wachtwoordzin invoeren op opstart chassis](./media/disk-encryption/centos-encrypt-fig4.png)

5. Bereid de virtuele machine voor op het uploaden naar Azure met behulp van de instructies voor ' CentOS 7.0 + ' in [voor bereiding a op CentOS gebaseerde virtuele machines voor Azure](./create-upload-centos.md?toc=/azure/virtual-machines/linux/toc.json#centos-70). Voer de laatste stap nog niet uit (de inrichting van de virtuele machine wordt ongedaan gemaakt).

6. U kunt nu de inrichting van de virtuele machine ongedaan maken en uw VHD uploaden naar Azure.

Als u versleuteling wilt configureren voor gebruik met Azure, voert u de volgende stappen uit:

1. Bewerk de/etc/Dracut.conf en voeg de volgende regel toe:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. Commentaar op deze regels aan het einde van het bestand/usr/lib/Dracut/modules.d/90crypt/module-Setup.sh:
   ```bash
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
   ```

3. Voeg de volgende regel toe aan het begin van het bestand/usr/lib/Dracut/modules.d/90crypt/parse-crypt.sh:
   ```bash
    DRACUT_SYSTEMD=0
   ```
   En wijzig alle instanties van:
   ```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
   ```
   tot
   ```bash
    if [ 1 ]; then
   ```
4. Bewerk/usr/lib/Dracut/modules.d/90crypt/cryptroot-Ask.sh en voeg het volgende toe na het ' # open LUKS-apparaat ':
    ```bash
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```    
5. Voer '/usr/sbin/Dracut-f-v ' uit om de initrd bij te werken.

    ![CentOS 7 Setup-run/usr/sbin/Dracut-f-v](./media/disk-encryption/centos-encrypt-fig5.png)

## <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Versleutelde VHD uploaden naar een Azure Storage-account
Nadat DM-Crypt versleuteling is ingeschakeld, moet de lokale versleutelde VHD worden geüpload naar uw opslag account.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```
## <a name="upload-the-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Upload het geheim voor de vooraf versleutelde virtuele machine naar uw sleutel kluis
Bij het versleutelen met een Azure AD-app (vorige versie) moet het geheim voor schijf versleuteling dat u eerder hebt verkregen, worden geüpload als een geheim in uw sleutel kluis. Voor de sleutel kluis moet schijf versleuteling en machtigingen zijn ingeschakeld voor uw Azure AD-client.

```powershell 
 $AadClientId = "My-AAD-Client-Id"
 $AadClientSecret = "My-AAD-Client-Secret"

 $key vault = New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption
``` 

### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Het geheim voor schijf versleuteling is niet versleuteld met een KEK
Als u het geheim in uw sleutel kluis wilt instellen, gebruikt u [set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret). De wachtwoordzin wordt gecodeerd als een base64-teken reeks en vervolgens geüpload naar de sleutel kluis. Bovendien moet u ervoor zorgen dat de volgende tags worden ingesteld wanneer u het geheim maakt in de sleutel kluis.

```powershell

 # This is the passphrase that was provided for encryption during the distribution installation
 $passphrase = "contoso-password"

 $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
 $secretName = [guid]::NewGuid().ToString()
 $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
 $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

 $secret = Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
 $secretUrl = $secret.Id
```


Gebruik de `$secretUrl` in de volgende stap voor [het koppelen van de besturingssysteem schijf zonder gebruik te maken van KEK](#without-using-a-kek).

### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Het geheim voor schijf versleuteling is versleuteld met een KEK
Voordat u het geheim uploadt naar de sleutel kluis, kunt u dit eventueel versleutelen met behulp van een sleutel versleutelings sleutel. Gebruik de omloop- [API](/rest/api/keyvault/wrapkey) om het geheim eerst te versleutelen met behulp van de coderings sleutel sleutel. De uitvoer van deze terugloop bewerking is een met base64 gecodeerde URL-teken reeks, die u vervolgens als geheim kunt uploaden met behulp van de- [`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret) cmdlet.

```powershell
    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id
```

Gebruik `$KeyEncryptionKey` en `$secretUrl` in de volgende stap voor [het koppelen van de besturingssysteem schijf met behulp van KEK](#using-a-kek).

##  <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Geef een geheime URL op wanneer u een besturingssysteem schijf koppelt

###  <a name="without-using-a-kek"></a>Zonder gebruik te maken van een KEK
Wanneer u de besturingssysteem schijf koppelt, moet u door gaan `$secretUrl` . De URL is gegenereerd in het gedeelte ' schijf versleutelings geheim is niet versleuteld met een KEK '.
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl
```
### <a name="using-a-kek"></a>Een KEK gebruiken
Wanneer u de besturingssysteem schijf koppelt, geeft u het door `$KeyEncryptionKey` en `$secretUrl` . De URL is gegenereerd in het gedeelte ' schijf versleutelings geheim versleuteld met een KEK-onderdeel.
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id
```
