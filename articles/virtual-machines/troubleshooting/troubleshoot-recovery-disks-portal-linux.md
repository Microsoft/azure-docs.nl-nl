---
title: Gebruik een Linux-probleemoplossings-VM in de Azure Portal | Microsoft Docs
description: Meer informatie over het oplossen van problemen met virtuele Linux-machines door de besturingssysteem schijf te koppelen aan een herstel-VM met behulp van de Azure Portal
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/19/2019
ms.author: genli
ms.openlocfilehash: affdee6871649102ef7881fb0f540eba6ab450ca
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98200954"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Problemen met een virtuele Linux-machine oplossen door de besturingssysteem schijf te koppelen aan een herstel-VM met behulp van de Azure Portal
Als op de virtuele Linux-machine (VM) een opstart-of schijf fout optreedt, moet u mogelijk de stappen voor probleem oplossing uitvoeren op de virtuele harde schijf zelf. Een voor beeld hiervan is een ongeldige vermelding in `/etc/fstab` die verhindert dat de virtuele machine kan worden opgestart. In dit artikel wordt beschreven hoe u de Azure Portal kunt gebruiken om de virtuele harde schijf te verbinden met een andere Linux-VM om eventuele fouten op te lossen en vervolgens de oorspronkelijke VM opnieuw te maken.

## <a name="recovery-process-overview"></a>Overzicht van het herstelproces
Het probleemoplossingsproces is als volgt:

1. Stop de betrokken VM.
1. Maak een moment opname van de besturingssysteem schijf van de virtuele machine.
1. Maak een virtuele harde schijf van de moment opname.
1. Koppel en koppel de virtuele harde schijf aan een andere Linux-VM voor het oplossen van problemen.
1. Maak verbinding met de VM voor probleemoplossing. Bewerk bestanden of voer hulpprogram ma's uit om problemen op de oorspronkelijke virtuele harde schijf op te lossen.
1. Koppel de virtuele harde schijf van de VM voor probleemoplossing los.
1. Wissel de besturingssysteem schijf voor de virtuele machine uit.

> [!NOTE]
> Dit artikel is niet van toepassing op de virtuele machine met een niet-beheerde schijf.

## <a name="determine-boot-issues"></a>Opstart problemen vaststellen
Bekijk de scherm opname van diagnostische gegevens over opstarten en VM om te bepalen waarom de virtuele machine niet correct kan worden opgestart. Een veelvoorkomend voor beeld is een ongeldige vermelding in `/etc/fstab` of een onderliggende virtuele harde schijf die wordt verwijderd of verplaatst.

Selecteer uw virtuele machine in de portal en schuif omlaag naar de sectie **ondersteuning en probleem oplossing** . Klik op **Diagnostische gegevens over opstarten** om de console berichten weer te geven die zijn gestreamd vanaf uw VM. Bekijk de console Logboeken om te zien of u kunt bepalen waarom de virtuele machine een probleem ondervindt. In het volgende voor beeld ziet u een VM die is vastgelopen in de onderhouds modus waarvoor hand matige interactie is vereist:

![De console logboeken voor diagnostische gegevens over opstarten van VM weer geven](./media/troubleshoot-recovery-disks-portal-linux/boot-diagnostics-error.png)

U kunt ook op de **scherm opname** boven aan het logboek voor diagnostische gegevens over opstarten klikken om een opname van de VM-scherm afbeelding te downloaden.

## <a name="take-a-snapshot-of-the-os-disk"></a>Een moment opname maken van de besturingssysteem schijf
Een moment opname is een volledige, alleen-lezen kopie van een virtuele harde schijf (VHD). U wordt aangeraden de virtuele machine op een schone manier af te sluiten voordat u een moment opname maakt, zodat alle processen die worden uitgevoerd, worden gewist. Voer de volgende stappen uit om een moment opname te maken van een besturingssysteem schijf:

1. Ga naar [Azure Portal](https://portal.azure.com). Selecteer **virtuele machines** in de zijbalk en selecteer vervolgens de VM met een probleem.
1. Selecteer **schijven** in het linkerdeel venster en selecteer vervolgens de naam van de besturingssysteem schijf.
    ![Afbeelding over de naam van de besturingssysteem schijf](./media/troubleshoot-recovery-disks-portal-windows/select-osdisk.png)
1. Op de pagina **overzicht** van de besturingssysteem schijf en selecteert u **moment opname maken**.
1. Maak een moment opname op dezelfde locatie als de besturingssysteem schijf.

## <a name="create-a-disk-from-the-snapshot"></a>Een schijf maken op basis van de moment opname
Voer de volgende stappen uit om een schijf te maken op basis van de moment opname:

1. Selecteer **Cloud shell** in het Azure Portal.

    ![Afbeelding over open Cloud Shell](./media/troubleshoot-recovery-disks-portal-windows/cloud-shell.png)
1. Voer de volgende Power shell-opdrachten uit om een beheerde schijf te maken op basis van de moment opname. Vervang deze voorbeeld namen door de juiste namen.

    ```powershell
    #Provide the name of your resource group
    $resourceGroupName ='myResourceGroup'
    
    #Provide the name of the snapshot that will be used to create Managed Disks
    $snapshotName = 'mySnapshot' 
    
    #Provide the name of theManaged Disk
    $diskName = 'newOSDisk'
    
    #Provide the size of the disks in GB. It should be greater than the VHD file size. In this sample, the size of the snapshot is 127 GB. So we set the disk size to 128 GB.
    $diskSize = '128'
    
    #Provide the storage type for Managed Disk. Premium_LRS or Standard_LRS.
    $storageType = 'Standard_LRS'
    
    #Provide the Azure region (e.g. westus) where Managed Disks will be located.
    #This location should be same as the snapshot location
    #Get all the Azure location using command below:
    #Get-AzLocation
    $location = 'westus'
    
    $snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
     
    $diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
     
    New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
    ```
3. Als de opdrachten correct worden uitgevoerd, ziet u de nieuwe schijf in de resource groep die u hebt ingevoerd.

## <a name="attach-disk-to-another-vm"></a>Schijf koppelen aan een andere virtuele machine
Voor de volgende stappen gebruikt u een andere virtuele machine voor het oplossen van problemen. Nadat u de schijf aan de virtuele machine voor probleem oplossing hebt gekoppeld, kunt u de inhoud van de schijf bekijken en bewerken. Met dit proces kunt u eventuele configuratie fouten corrigeren of aanvullende toepassings-of systeem logboek bestanden bekijken. Voer de volgende stappen uit om de schijf aan een andere virtuele machine te koppelen:

1. Selecteer uw resource groep in de portal en selecteer vervolgens de virtuele machine voor probleem oplossing. Selecteer **schijven**, selecteer **bewerken** en klik vervolgens op **gegevens schijf toevoegen**:

    ![Een bestaande schijf koppelen in de portal](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. Selecteer in de lijst **gegevens schijven** de besturingssysteem schijf van de virtuele machine die u hebt geïdentificeerd. Als u de besturingssysteem schijf niet ziet, zorg er dan voor dat u problemen met de virtuele machine oplost en de besturingssysteem schijf zich in dezelfde regio (locatie) bevindt. 
3. Selecteer **Opslaan** om de wijzigingen toe te passen.

## <a name="mount-the-attached-data-disk"></a>De gekoppelde gegevens schijf koppelen

> [!NOTE]
> In de volgende voor beelden worden de stappen beschreven die nodig zijn voor een Ubuntu-VM. Als u een andere Linux-distributie gebruikt, zoals Red Hat Enterprise Linux of SUSE, zijn de locaties en opdrachten van het logboek bestand `mount` mogelijk iets anders. Raadpleeg de documentatie voor uw specifieke distributie voor de desbetreffende wijzigingen in de opdrachten.

1. SSH naar uw virtuele machine voor probleem oplossing met de juiste referenties. Als deze schijf de eerste gegevens schijf is die aan uw virtuele machine voor probleem oplossing is gekoppeld, is deze waarschijnlijk verbonden met `/dev/sdc` . Gebruiken `dmseg` om gekoppelde schijven weer te geven:

    ```bash
    dmesg | grep SCSI
    ```
    De uitvoer lijkt op die in het volgende voorbeeld:

    ```bash
    [    0.294784] SCSI subsystem initialized
    [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
    [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
    [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
    [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
    ```

    In het vorige voor beeld bevindt de besturingssysteem schijf zich op `/dev/sda` en de tijdelijke schijf die voor elke VM is ingesteld, bevindt zich op `/dev/sdb` . Als u meerdere gegevens schijven had, moeten ze zich op `/dev/sdd` , `/dev/sde` ,,,, enzovoort.

2. Maak een directory voor het koppelen van uw bestaande virtuele harde schijf. In het volgende voor beeld wordt een map gemaakt met de naam `troubleshootingdisk` :

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. Als u meerdere partities op de bestaande virtuele harde schijf hebt, koppelt u de vereiste partitie. In het volgende voor beeld wordt de eerste primaire partitie gekoppeld aan `/dev/sdc1` :

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > Best Practice is het koppelen van gegevens schijven op virtuele machines in azure met behulp van de Universally Unique Identifier (UUID) van de virtuele harde schijf. Voor dit korte probleemoplossings scenario is het niet nodig om de virtuele harde schijf te koppelen met behulp van de UUID. `/etc/fstab`Het bewerken van virtuele harde schijven met de apparaatnaam in plaats van de UUID kan er echter toe leiden dat de VM niet kan worden opgestart onder normaal gebruik.


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Problemen op de oorspronkelijke virtuele harde schijf oplossen
Als de bestaande virtuele harde schijf is gekoppeld, kunt u nu eventuele onderhouds-en probleemoplossings stappen uitvoeren. Zodra u de problemen hebt opgelost, kunt u doorgaan met de volgende stappen.

## <a name="unmount-and-detach-original-virtual-hard-disk"></a>De oorspronkelijke virtuele harde schijf ontkoppelen en loskoppelen
Wanneer de fouten zijn opgelost, koppelt u de bestaande virtuele harde schijf los van uw probleemoplossings-VM. U kunt de virtuele harde schijf niet met andere virtuele machines gebruiken totdat de lease die de virtuele harde schijf koppelt aan de VM voor probleem oplossing is vrijgegeven.

1. Ontkoppel de bestaande virtuele harde schijf van de SSH-sessie naar uw VM voor probleem oplossing. Wijzig eerst de bovenliggende map voor het koppel punt:

    ```bash
    cd /
    ```

    Ontkoppel nu de bestaande virtuele harde schijf. In het volgende voor beeld wordt het apparaat ontkoppeld op `/dev/sdc1` :

    ```bash
    sudo umount /dev/sdc1
    ```

2. Ontkoppel nu de virtuele harde schijf van de VM. Selecteer uw virtuele machine in de portal en klik op **schijven**. Selecteer uw bestaande virtuele harde schijf en klik vervolgens op **ontkoppelen**:

    ![Bestaande virtuele harde schijf ontkoppelen](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    Wacht tot de virtuele machine de gegevens schijf heeft losgekoppeld voordat u doorgaat.

## <a name="swap-the-os-disk-for-the-vm"></a>De besturingssysteem schijf voor de virtuele machine wisselen

Azure Portal ondersteunt nu het wijzigen van de besturingssysteem schijf van de virtuele machine. Voer hiervoor de volgende stappen uit:

1. Ga naar [Azure Portal](https://portal.azure.com). Selecteer **virtuele machines** in de zijbalk en selecteer vervolgens de VM met een probleem.
1. Selecteer **schijven** in het linkerdeel venster en selecteer vervolgens **besturingssysteem schijf wisselen**.
        ![De afbeelding over het wisselen van de besturingssysteem schijf in Azure Portal](./media/troubleshoot-recovery-disks-portal-windows/swap-os-ui.png)

1. Kies de nieuwe schijf die u hebt gerepareerd en typ de naam van de virtuele machine om de wijziging te bevestigen. Als de schijf niet in de lijst wordt weer geven, wacht u 10 ~ 15 minuten nadat u de schijf hebt losgekoppeld van de virtuele machine voor probleem oplossing. Zorg er ook voor dat de schijf zich op dezelfde locatie bevindt als de virtuele machine.
1. Selecteer OK.

## <a name="next-steps"></a>Volgende stappen
Zie [problemen met ssh-verbindingen met een Azure VM oplossen](troubleshoot-ssh-connection.md)als u problemen ondervindt bij het maken van verbinding met uw virtuele machine. Zie problemen met [toepassings connectiviteit oplossen op een Linux-VM](./troubleshoot-app-connection.md?toc=/azure/virtual-machines/linux/toc.json)voor problemen met het openen van toepassingen die op uw virtuele machine worden uitgevoerd.

Zie [Azure Resource Manager Overview](../../azure-resource-manager/management/overview.md)voor meer informatie over het gebruik van Resource Manager.
