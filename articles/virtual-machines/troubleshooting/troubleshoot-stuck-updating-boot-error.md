---
title: Het opstarten van de Azure-VM is vastgelopen op Windows Update | Microsoft Docs
description: Informatie over het oplossen van het probleem wanneer een Azure-VM wordt gestart op Windows Update.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/09/2018
ms.author: genli
ms.openlocfilehash: 3090b7b889d914fc0cdb598b8bf29a73c81f50cb
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632000"
---
# <a name="azure-vm-startup-is-stuck-at-windows-update"></a>Opstarten van Azure VM is vastgelopen op Windows Update

Dit artikel helpt u bij het oplossen van het probleem wanneer uw virtuele machine (VM) tijdens het opstarten vastloopt in de Windows Update fase. 


## <a name="symptom"></a>Symptoom

 Een Windows-VM start niet. Wanneer u de scherm opnamen in het venster [Diagnostische gegevens over opstarten](../troubleshooting/boot-diagnostics.md) inschakelt, ziet u dat het opstarten is vastgelopen in het update proces. Hier volgen enkele voor beelden van berichten die kunnen worden weer gegeven:

- Windows # #% wordt niet meer op uw PC geïnstalleerd. Dit duurt even voordat de PC meerdere keren opnieuw wordt opgestart
- Blijf op de hoogte van uw PC totdat dit is voltooid. Update # van #... installeren... 
- De wijzigingen die worden aangebracht, kunnen niet worden voltooid de computer niet uitschakelen
- Fout bij het configureren van wijzigingen in Windows-updates de computer niet uitschakelen
- Fout < fout code > het Toep assen van update bewerkingen # # # # van # # # # # (\Regist...)
- Onherstelbare fout < fout code > het Toep assen van update bewerkingen # # # # # # # # # # ($ $...)


## <a name="solution"></a>Oplossing
> [!TIP]
> Als u een recente back-up van de virtuele machine hebt, kunt u proberen [de virtuele machine terug te zetten vanaf de back-up](../../backup/backup-azure-arm-restore-vms.md) om het opstart probleem op te lossen.

Het update proces kan enige tijd in beslag nemen, afhankelijk van het aantal updates dat wordt geïnstalleerd of teruggedraaid. De VM in deze status gedurende 8 uur verlaten. Als de VM na die periode nog steeds in deze status is, start u de VM opnieuw op via de Azure Portal en controleert u of deze normaal kan worden gestart. Als deze stap niet werkt, voert u de volgende oplossing uit.

### <a name="remove-the-update-that-causes-the-problem"></a>De update die het probleem veroorzaakt, verwijderen

1. Maak een moment opname van de besturingssysteem schijf van de betrokken VM als back-up. Zie [snap shot a disk](../windows/snapshot-copy-managed-disk.md)(Engelstalig) voor meer informatie. 
2. [Koppel de besturingssysteem schijf aan een herstel-VM](troubleshoot-recovery-disks-portal-windows.md).
3. Zodra de besturingssysteem schijf is gekoppeld op de herstel-VM, voert u **diskmgmt. msc** uit om schijf beheer te openen en ervoor te zorgen dat de gekoppelde schijf **online** is. Noteer de stationsletter die is toegewezen aan de gekoppelde besturingssysteem schijf die de map \Windows heeft. Als de schijf is versleuteld, ontsleutelt u de schijf voordat u verdergaat met de volgende stappen in dit document.

4. Open een opdracht prompt exemplaar met verhoogde bevoegdheid (als administrator uitvoeren). Voer de volgende opdracht uit om de lijst op te halen van de update pakketten die zich op de gekoppelde besturingssysteem schijf bevinden:

    ```console
    dism /image:<Attached OS disk>:\ /get-packages > c:\temp\Patch_level.txt
    ```

    Als de gekoppelde besturingssysteem schijf bijvoorbeeld station F is, voert u de volgende opdracht uit:

    ```console
    dism /image:F:\ /get-packages > c:\temp\Patch_level.txt
    ```

5. Open het C:\temp\Patch_level.txt-bestand en lees het van de onderkant. Zoek de update die in **behandeling** is of verwijder de status in **behandeling** .  Hier volgt een voor beeld van de update status:

    ```
    Package Identity : Package_for_RollupFix~31bf3856ad364e35~amd64~~17134.345.1.5
    State : Install Pending
    Release Type : Security Update
    Install Time :
    ```
6. Verwijder de update die het probleem heeft veroorzaakt:
    
    ```
    dism /Image:<Attached OS disk>:\ /Remove-Package /PackageName:<PACKAGE NAME TO DELETE>
    ```
    Voorbeeld: 

    ```
    dism /Image:F:\ /Remove-Package /PackageName:Package_for_RollupFix~31bf3856ad364e35~amd64~~17134.345.1.5
    ```

    > [!NOTE] 
    > Afhankelijk van de grootte van het pakket, neemt het hulp programma DISM even de tijd om de verwijdering te verwerken. Normaal gesp roken wordt het proces binnen 16 minuten voltooid.

7. [Ontkoppel de besturingssysteem schijf en maak de virtuele machine opnieuw](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-the-original-virtual-hard-disk). Controleer vervolgens of het probleem is opgelost.
