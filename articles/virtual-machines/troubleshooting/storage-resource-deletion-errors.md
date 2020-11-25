---
title: Problemen met het verwijderen van opslag resources op Linux Vm's in azure oplossen | Microsoft Docs
description: Meer informatie over het oplossen van problemen met Linux-Vm's bij het verwijderen van opslag resources met gekoppelde Vhd's.
keywords: ''
services: virtual-machines
author: genlin
manager: dcscontentpm
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 8d727bc8bdc8f015504baa57f9596b3bacac9712
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022867"
---
# <a name="troubleshoot-storage-resource-deletion-errors"></a>Fouten bij het verwijderen van een opslagresource oplossen

In bepaalde scenario's kan een van de volgende fouten optreden tijdens het verwijderen van een Azure Storage-account,-container of-BLOB in een Azure Resource Manager-implementatie:

> **Kan het opslag account StorageAccountName niet verwijderen. Fout: het opslag account kan niet worden verwijderd omdat de artefacten in gebruik zijn.**
> 
> **Kan # van # container (s) niet verwijderen: <br> vhd's: er is momenteel een lease in de container en er is geen lease-id opgegeven in de aanvraag.**
> 
> **Kan # out van # blobs: <br> blobnaam. VHD niet verwijderen: er is momenteel een lease voor de BLOB en er is geen lease-id opgegeven in de aanvraag.**

De Vhd's die worden gebruikt in virtuele machines van Azure zijn. VHD-bestanden die zijn opgeslagen als pagina-blobs in een Standard-of Premium Storage-account in Azure. Zie onze [Inleiding tot Managed disks](../managed-disks-overview.md)voor meer informatie over Azure disks.

Azure voor komt dat een schijf die is gekoppeld aan een virtuele machine wordt verwijderd om beschadiging te voor komen. Er wordt ook voor komen dat containers en opslag accounts worden verwijderd die een pagina-BLOB hebben die is gekoppeld aan een virtuele machine. 

Het proces voor het verwijderen van een opslag account, container of BLOB bij het ontvangen van een van deze fouten is: 
1. Blobs identificeren die zijn gekoppeld aan een VM
2. [Vm's met gekoppelde **besturingssysteem schijf** verwijderen](#step-2-delete-vm-to-detach-os-disk)
3. [Alle **gegevens schijven** losmaken van de resterende VM ('s)](#step-3-detach-data-disk-from-the-vm)

Probeer het opslag account, de container of de BLOB opnieuw te verwijderen nadat deze stappen zijn voltooid.

## <a name="step-1-identify-blob-attached-to-a-vm"></a>Stap 1: BLOB identificeren die is gekoppeld aan een virtuele machine

### <a name="scenario-1-deleting-a-blob--identify-attached-vm"></a>Scenario 1: een BLOB verwijderen – gekoppelde VM identificeren
1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer in het Hub-menu **Alle resources**. Ga naar het opslag account, onder **BLOB-service** Selecteer **containers** en navigeer naar de blob die u wilt verwijderen.
3. Als de BLOB- **lease status** wordt **geleasd**, klikt u met de rechter muisknop en selecteert u **meta gegevens bewerken** om het deel venster BLOB-meta gegevens te openen. 

    ![Scherm opname van de portal, met de blobs van het opslag account en met de rechter muisknop op > "meta gegevens bewerken" gemarkeerd](./media/troubleshoot-vhds/utd-edit-metadata-sm.png)

4. Controleer en noteer de waarde voor **MicrosoftAzureCompute_VMName** in het deel venster BLOB-meta gegevens. Deze waarde is de naam van de virtuele machine waaraan de VHD is gekoppeld. (Zie **belang rijk** als dit veld niet bestaat)
5. Controleer en noteer de waarde van **MicrosoftAzureCompute_DiskType** in het deel venster BLOB-meta gegevens. Met deze waarde wordt aangegeven of de gekoppelde schijf een besturings systeem of gegevens schijf is (Zie **belang rijk** als dit veld niet bestaat). 

     ![Scherm opname van de portal, met het deel venster voor het opslaan van de meta gegevens van de BLOB](./media/troubleshoot-vhds/utd-blob-metadata-sm.png)

6. Als het type BLOB-schijf **OSDisk** is, volgt [u stap 2: virtuele machine verwijderen om de besturingssysteem schijf los te koppelen](#step-2-delete-vm-to-detach-os-disk). Als het type BLOB-schijf **DataDisk** is, volgt u de stappen in [stap 3: gegevens schijf loskoppelen van de virtuele machine](#step-3-detach-data-disk-from-the-vm). 

> [!IMPORTANT]
> Als **MicrosoftAzureCompute_VMName** en **MicrosoftAzureCompute_DiskType** niet worden weer gegeven in de BLOB-meta gegevens, geeft dit aan dat de BLOB expliciet is geleasd en niet is gekoppeld aan een virtuele machine. Geleasde blobs kunnen niet worden verwijderd zonder eerst de lease te verbreken. Als u de lease wilt verstoren, klikt u met de rechter muisknop op de BLOB en selecteert u **lease** afleiden. Geleasde blobs die niet zijn gekoppeld aan een virtuele machine, verhinderen het verwijderen van de blob, maar verhinderen het verwijderen van een container-of opslag account niet.

### <a name="scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms"></a>Scenario 2: een container verwijderen-alle blobs in een container identificeren die zijn gekoppeld aan Vm's
1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer in het Hub-menu **Alle resources**. Ga naar het opslag account, onder **BLOB** -service **containers** selecteren en zoek de container die moet worden verwijderd.
3. Klik om de container te openen en de lijst met blobs erin wordt weer gegeven. Alle blobs identificeren met Blob type = **pagina-BLOB** en lease status = **geleasd** uit deze lijst. Volg scenario 1 om de virtuele machine te identificeren die aan elk van deze blobs is gekoppeld.

    ![Scherm opname van de portal, met de blobs van het opslag account en de ' lease status ' en ' leased ' gemarkeerd](./media/troubleshoot-vhds/utd-disks-sm.png)

4. Volg [stap 2](#step-2-delete-vm-to-detach-os-disk) en [3](#step-3-detach-data-disk-from-the-vm) om de virtuele machine (s) met **OSDisk** te verwijderen en **DataDisk** los te koppelen. 

### <a name="scenario-3-deleting-storage-account---identify-all-blobs-within-storage-account-that-are-attached-to-vms"></a>Scenario 3: een opslag account verwijderen-een of meer blobs identificeren binnen het opslag account dat is gekoppeld aan Vm's
1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer in het Hub-menu **Alle resources**. Ga naar het opslag account en selecteer **blobs** onder **BLOB-service** .
3. In het deel venster **containers** identificeert u alle containers waarbij de **lease status** wordt **geleasd** en voert u [scenario 2](#scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms) uit voor elke **geleasde** container.
4. Volg [stap 2](#step-2-delete-vm-to-detach-os-disk) en [3](#step-3-detach-data-disk-from-the-vm) om de virtuele machine (s) met **OSDisk** te verwijderen en **DataDisk** los te koppelen. 

## <a name="step-2-delete-vm-to-detach-os-disk"></a>Stap 2: de virtuele machine verwijderen om de besturingssysteem schijf los te koppelen
Als de VHD een besturingssysteem schijf is, moet u de virtuele machine verwijderen voordat de gekoppelde VHD kan worden verwijderd. Er is geen aanvullende actie vereist voor gegevens schijven die zijn gekoppeld aan dezelfde VM wanneer deze stappen zijn voltooid:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer **virtual machines** in het menu hub.
3. Selecteer de virtuele machine waaraan de VHD is gekoppeld.
4. Zorg ervoor dat er niets actief gebruikmaakt van de virtuele machine en dat u de virtuele machine niet meer nodig hebt.
5. Selecteer boven in het detail venster van de **virtuele machine** de optie **verwijderen** en klik vervolgens op **Ja** om te bevestigen.
6. De virtuele machine moet worden verwijderd, maar de VHD kan worden bewaard. De VHD mag echter niet meer worden gekoppeld aan een virtuele machine of er moet een lease op worden aangesloten. Het kan enkele minuten duren voordat de lease is vrijgegeven. Als u wilt controleren of de lease is vrijgegeven, bladert u naar de BLOB-locatie en in het deel venster **BLOB-eigenschappen** , de **lease status** moet **beschikbaar** zijn.

## <a name="step-3-detach-data-disk-from-the-vm"></a>Stap 3: de gegevens schijf loskoppelen van de VM
Als de VHD een gegevens schijf is, koppelt u de VHD los van de virtuele machine om de lease te verwijderen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer **virtual machines** in het menu hub.
3. Selecteer de virtuele machine waaraan de VHD is gekoppeld.
4. Selecteer **schijven** in het detail venster van de **virtuele machine** .
5. Selecteer de gegevens schijf die moet worden verwijderd de VHD is gekoppeld aan. U kunt bepalen welke BLOB aan de schijf is gekoppeld door de URL van de VHD te controleren.
6. U kunt de BLOB-locatie controleren door te klikken op de schijf om het pad in de **VHD-URI** -veld te controleren.
7. Selecteer **bewerken** in het deel venster boven aan de **schijven** .
8. Klik op het **pictogram loskoppelen** van de gegevens schijf die u wilt verwijderen.

     ![Scherm afbeelding van de portal, met het deel venster voor het opslaan van de meta gegevens van de blob, markeert het ontkoppelings pictogram van de gegevens schijf die moet worden verwijderd.](./media/troubleshoot-vhds/utd-vm-disks-edit.png)

9. Selecteer **Opslaan**. De schijf wordt nu losgekoppeld van de virtuele machine en de VHD wordt niet meer geleasd. Het kan enkele minuten duren voordat de lease is vrijgegeven. Als u wilt controleren of de lease is vrijgegeven, bladert u naar de BLOB-locatie en in het deel venster **BLOB-eigenschappen** , de waarde voor de **lease status** moet worden **ontgrendeld** of **beschikbaar**.

[Storage deletion errors in Resource Manager deployment]: #storage-delete-errors-in-rm

