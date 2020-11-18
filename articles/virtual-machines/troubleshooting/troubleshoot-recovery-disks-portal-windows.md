---
title: Problemen oplossen met een Windows-VM in de Azure Portal | Microsoft Docs
description: Informatie over het oplossen van problemen met virtuele Windows-machines in azure door de besturingssysteem schijf te koppelen aan een herstel-VM via de Azure Portal.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/19/2018
ms.author: genli
ms.openlocfilehash: 04adf3903cd925f4507e94347251c762a576ff93
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94735225"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-through-the-azure-portal"></a>Problemen met een Windows-VM oplossen door de besturingssysteem schijf te koppelen aan een herstel-VM via de Azure Portal
Als op uw virtuele Windows-machine (VM) in azure een opstart-of schijf fout optreedt, moet u mogelijk stappen voor probleem oplossing uitvoeren op de virtuele harde schijf (VHD). Een veelvoorkomend voor beeld is een mislukte toepassings update die verhindert dat de virtuele machine wordt gestart. In dit artikel wordt beschreven hoe u met behulp van de Azure Portal de virtuele harde schijf verbindt met een andere Windows-VM om eventuele fouten op te lossen en de oorspronkelijke VM vervolgens opnieuw te maken. 

## <a name="recovery-process-overview"></a>Overzicht van het herstelproces
Het probleemoplossingsproces is als volgt:

1. Stop de betrokken VM.
1. Maak een moment opname voor de besturingssysteem schijf van de virtuele machine.
1. Maak een virtuele harde schijf van de moment opname.
1. Koppel en koppel de virtuele harde schijf aan een andere Windows-VM voor het oplossen van problemen.
1. Maak verbinding met de VM voor probleemoplossing. Bewerk bestanden of voer hulpprogram ma's uit om problemen op de oorspronkelijke virtuele harde schijf op te lossen.
1. Koppel de virtuele harde schijf van de VM voor probleemoplossing los.
1. Wissel de besturingssysteem schijf voor de virtuele machine uit.

> [!NOTE]
> Dit artikel is niet van toepassing op Vm's met niet-beheerde schijven.

## <a name="take-a-snapshot-of-the-os-disk"></a>Een moment opname maken van de besturingssysteem schijf
Een moment opname is een volledige, alleen-lezen kopie van een virtuele harde schijf. U wordt aangeraden de virtuele machine op een schone manier af te sluiten voordat u een moment opname maakt, zodat alle processen die worden uitgevoerd, worden gewist. Voer de volgende stappen uit om een moment opname te maken van een besturingssysteem schijf:

1. Ga naar de [Azure Portal](https://portal.azure.com). Selecteer **virtuele machines** in de zijbalk en selecteer vervolgens de VM waarop het probleem zich voordoet.
1. Selecteer **schijven** in het linkerdeel venster en selecteer vervolgens de naam van de besturingssysteem schijf.
    ![Scherm afbeelding met de naam van de besturingssysteem schijf in schijf instellingen.](./media/troubleshoot-recovery-disks-portal-windows/select-osdisk.png)
1. Op de pagina **overzicht** van de besturingssysteem schijf selecteert u **moment opname maken**.
1. Maak een moment opname op dezelfde locatie als de besturingssysteem schijf.

## <a name="create-a-disk-from-the-snapshot"></a>Een schijf maken op basis van de moment opname
Voer de volgende stappen uit om een schijf te maken op basis van de moment opname:

1. Selecteer **Cloud shell** in het Azure Portal.

    ![Scherm afbeelding met de knop voor het openen van Azure Cloud Shell.](./media/troubleshoot-recovery-disks-portal-windows/cloud-shell.png)
1. Voer de volgende Power shell-opdrachten uit om een beheerde schijf te maken op basis van de moment opname. Vervang de namen van de voor beelden door de juiste namen.

    ```powershell
    #Provide the name of your resource group.
    $resourceGroupName ='myResourceGroup'
    
    #Provide the name of the snapshot that will be used to create managed disks.
    $snapshotName = 'mySnapshot' 
    
    #Provide the name of the managed disk.
    $diskName = 'newOSDisk'
    
    #Provide the size of the disks in gigabytes. It should be greater than the VHD file size. In this example, the size of the snapshot is 127 GB. So we set the disk size to 128 GB.
    $diskSize = '128'
    
    #Provide the storage type for the managed disk: Premium_LRS or Standard_LRS.
    $storageType = 'Standard_LRS'
    
    #Provide the Azure region (for example, westus) where the managed disks will be located.
    #This location should be the same as the snapshot location.
    #Get all the Azure locations by using this command:
    #Get-AzLocation
    $location = 'westus'
    
    $snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
     
    $diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
     
    New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
    ```
3. Als de opdrachten correct worden uitgevoerd, ziet u de nieuwe schijf in de resource groep die u hebt ingevoerd.

## <a name="attach-the-disk-to-another-vm"></a>De schijf koppelen aan een andere virtuele machine
Voor de volgende stappen gebruikt u een andere virtuele machine voor het oplossen van problemen. Nadat u de schijf aan de virtuele machine voor probleem oplossing hebt gekoppeld, kunt u de inhoud van de schijf bekijken en bewerken. Met dit proces kunt u eventuele configuratie fouten corrigeren of aanvullende toepassings-of systeem logboek bestanden bekijken. Voer de volgende stappen uit om de schijf aan een andere virtuele machine te koppelen:

1. Selecteer uw resource groep in de portal en selecteer vervolgens de virtuele machine voor probleem oplossing. Selecteer **schijven**  >  **bewerken**  >  **gegevens schijf toevoegen**.

    ![Scherm opname van selecties voor het koppelen van een bestaande schijf in de portal.](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. Selecteer in de lijst **gegevens schijven** de besturingssysteem schijf van de virtuele machine die u hebt geïdentificeerd. Als u de besturingssysteem schijf niet ziet, moet u ervoor zorgen dat de virtuele machine voor probleem oplossing en de besturingssysteem schijf zich in dezelfde regio (locatie) bevinden. 
3. Selecteer **Opslaan** om de wijzigingen toe te passen.

## <a name="mount-the-attached-data-disk-to-the-vm"></a>De gekoppelde gegevens schijf aan de virtuele machine koppelen

1. Open een Extern bureaublad verbinding met de virtuele machine voor probleem oplossing. 
2. Open **Serverbeheer** op de virtuele machine voor probleem oplossing en selecteer vervolgens **Bestands-en opslag Services**. 

    ![Scherm opname van het selecteren van bestands-en opslag Services in Serverbeheer.](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

3. De gegevens schijf wordt automatisch gedetecteerd en gekoppeld. Selecteer **schijven** als u een lijst met de verbonden schijven wilt weer geven. U kunt uw gegevens schijf selecteren om volume gegevens weer te geven, inclusief de stationsletter. In het volgende voor beeld ziet u de gegevens schijf die is gekoppeld aan en gebruikt u station **F**.

    ![Scherm opname met een bijgevoegde schijf-en volume-informatie in Serverbeheer.](./media/troubleshoot-recovery-disks-portal-windows/server-manager-disk-attached.png)

## <a name="fix-problems-on-the-original-virtual-hard-disk"></a>Problemen op de oorspronkelijke virtuele harde schijf oplossen
Als de bestaande virtuele harde schijf is gekoppeld, kunt u nu eventuele onderhouds-en probleemoplossings stappen uitvoeren. Nadat u alle fouten hebt opgelost, gaat u verder met de volgende stappen.

## <a name="unmount-and-detach-the-original-virtual-hard-disk"></a>De oorspronkelijke virtuele harde schijf loskoppelen
Ontkoppel de bestaande virtuele harde schijf van uw virtuele machine voor probleem oplossing. U kunt de virtuele harde schijf niet met een andere virtuele machine gebruiken totdat de lease die de virtuele harde schijf koppelt aan de VM voor probleem oplossing is vrijgegeven.

1. Open vanuit de Extern bureaublad-sessie naar uw virtuele machine **Serverbeheer** en selecteer vervolgens **Bestands-en opslag Services**.

    ![Scherm opname van het selecteren van bestands-en opslag Services in Serverbeheer.](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

2. Selecteer **schijven**, klik met de rechter muisknop op uw gegevens schijf en selecteer vervolgens **offline halen**.

    ![Scherm opname van de weer gave van de gegevens schijf als offline in Serverbeheer.](./media/troubleshoot-recovery-disks-portal-windows/server-manager-set-disk-offline.png)

3. Ontkoppel de virtuele harde schijf van de VM. Selecteer uw virtuele machine in de Azure Portal en selecteer vervolgens **schijven**. 
4. Selecteer **bewerken**, selecteer de besturingssysteem schijf die u hebt toegevoegd en selecteer vervolgens **verwijderen**.

    ![Scherm opname van selecties voor het loskoppelen van een bestaande virtuele harde schijf.](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    Voordat u doorgaat, moet u wachten tot de gegevens schijf in de virtuele machine is verwijderd.

## <a name="swap-the-os-disk-for-the-vm"></a>De besturingssysteem schijf voor de virtuele machine wisselen

Azure Portal ondersteunt nu het wijzigen van de besturingssysteem schijf van de virtuele machine. Volg hiervoor de volgende stappen:

1. Ga naar de [Azure Portal](https://portal.azure.com). Selecteer **virtuele machines** in de zijbalk en selecteer vervolgens de VM waarop het probleem zich voordoet.
1. Selecteer **schijven** in het linkerdeel venster en selecteer vervolgens **besturingssysteem schijf wisselen**.
   
    ![Scherm afbeelding met de knop voor het wisselen van een besturingssysteem schijf in de Azure Portal.](./media/troubleshoot-recovery-disks-portal-windows/swap-os-ui.png)

1. Kies de nieuwe schijf die u hebt gerepareerd en voer de naam van de virtuele machine in om de wijziging te bevestigen. Als de schijf niet in de lijst wordt weer geven, wacht u 10 tot 15 minuten nadat u de schijf hebt losgekoppeld van de virtuele machine voor probleem oplossing. Zorg er ook voor dat de schijf zich op dezelfde locatie bevindt als de virtuele machine.
1. Selecteer **OK**.

## <a name="next-steps"></a>Volgende stappen
Zie [problemen met extern bureaublad verbindingen met een virtuele machine van Azure oplossen](troubleshoot-rdp-connection.md)als u problemen ondervindt bij het maken van verbinding met uw virtuele machine. Zie problemen met [toepassings connectiviteit oplossen op een Windows-VM](troubleshoot-app-connection.md)voor problemen met het openen van toepassingen die op uw virtuele machine worden uitgevoerd.

Zie [Azure Resource Manager Overview](../../azure-resource-manager/management/overview.md)voor meer informatie over het gebruik van Azure Resource Manager.


