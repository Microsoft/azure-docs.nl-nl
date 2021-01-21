---
title: Windows-lus voor opnieuw opstarten op een Azure-VM | Microsoft Docs
description: Meer informatie over het oplossen van problemen met het opnieuw opstarten van Windows | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/15/2018
ms.author: genli
ms.openlocfilehash: ad0ed7e9619f0b789bf8949fe398aa27bc36b9e0
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629637"
---
# <a name="windows-reboot-loop-on-an-azure-vm"></a>Windows-lus voor opnieuw opstarten op een virtuele machine van Azure
In dit artikel wordt de herstart-lus beschreven die kan optreden op een virtuele Windows-machine (VM) in Microsoft Azure.

## <a name="symptom"></a>Symptoom

Wanneer u [Diagnostische gegevens over opstarten](./boot-diagnostics.md) gebruikt om de scherm opnamen van een virtuele machine op te halen, vindt u de virtuele machines opstarten, maar wordt het opstart proces onderbroken en wordt het proces opnieuw gestart.

![Start scherm 1](./media/troubleshoot-reboot-loop/start-screen-1.png)

## <a name="cause"></a>Oorzaak

De herstart-lus treedt op als gevolg van de volgende oorzaken:

### <a name="cause-1"></a>Oorzaak 1

Er is een service van derden die als kritiek is gemarkeerd en niet kan worden gestart. Dit zorgt ervoor dat het besturings systeem opnieuw wordt opgestart.

### <a name="cause-2"></a>Oorzaak 2

Er zijn enkele wijzigingen aangebracht in het besturings systeem. Deze zijn doorgaans gerelateerd aan een update-installatie, een installatie van een toepassing of een nieuw beleid. Mogelijk moet u de volgende logboeken controleren voor aanvullende informatie:

- Gebeurtenislogboeken
- CBS. logWindows
- Update. log

### <a name="cause-3"></a>Oorzaak 3

Beschadiging van het bestands systeem kan dit tot gevolg hebben. Het is echter moeilijk om een diagnose uit te stellen en de wijziging te identificeren die de beschadiging van het besturings systeem veroorzaakt.

## <a name="solution"></a>Oplossing

> [!TIP]
> Als u een recente back-up van de virtuele machine hebt, kunt u proberen [de virtuele machine terug te zetten vanaf de back-up](../../backup/backup-azure-arm-restore-vms.md) om het opstart probleem op te lossen.

Om dit probleem op te lossen, maakt u een [back-up van de besturingssysteem schijf](../windows/snapshot-copy-managed-disk.md)en [koppelt u de besturingssysteem schijf aan een virtuele machine voor herstel](./troubleshoot-recovery-disks-portal-windows.md)en volgt u de oplossings opties dienovereenkomstig of probeert u de oplossingen één voor één.

### <a name="solution-for-cause-1"></a>Oplossing voor oorzaak 1

1. Zodra de besturingssysteem schijf is gekoppeld aan een werkende VM, moet u ervoor zorgen dat de schijf is gemarkeerd als **online** in de schijf beheer-console en noteer de stationsletter van de partitie die de map **\Windows** bevat.

2. Als de schijf is ingesteld op **offline**, stelt u deze in op **online**.

3. Maak een kopie van de map **\Windows\System32\Config** als u de wijzigingen ongedaan wilt maken.

4. Open de Windows REGI ster-editor (regedit) op de virtuele machine voor herstel.

5. Selecteer de sleutel **HKEY_LOCAL_MACHINE** en selecteer vervolgens   >  **component bestand laden** in het menu.

6. Blader naar het systeem bestand in de map **\Windows\System32\Config** .

7. Selecteer **openen**, typ **BROKENSYSTEM** voor de naam, vouw de **HKEY_LOCAL_MACHINE** sleutel uit en raadpleeg vervolgens een extra sleutel met de naam **BROKENSYSTEM**.

8. Controleer op welke Beheerset de computer wordt opgestart. Het sleutel nummer wordt weer geven in de volgende register sleutel.

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\Select\Current`

9. Controleer wat de ernst is van de VM-Agent service via de volgende register sleutel.

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\RDAgent\ErrorControl`

10. Als de waarde van de register sleutel niet is ingesteld op **2**, gaat u naar de volgende oplossing.

11. Als de waarde van de register sleutel is ingesteld op **2**, wijzigt u de waarde van **2** in **1**.

12. Als een van de volgende sleutels bestaat en de waarde **2** of **3** is, en wijzig deze waarden in **1** dienovereenkomstig:

    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupCoordinatorSvc\ErrorControl`
    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupInquirySvc\ErrorControl`
    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupPluginSvc\ErrorControl`

13. Selecteer de sleutel **BROKENSYSTEM** en selecteer vervolgens   >  **component bestand verwijderen** in het menu.

14. Ontkoppel de besturingssysteem schijf van de virtuele machine voor probleem oplossing.

15. Verwijder de schijf uit de virtuele machine voor probleem oplossing en wacht ongeveer 2 minuten tot Azure deze schijf heeft vrijgegeven.

16. [Maak een nieuwe virtuele machine op basis van de besturingssysteem schijf](../windows/create-vm-specialized.md).

17. Als het probleem is opgelost, moet u de [RDAgent](/archive/blogs/mast/install-the-vm-agent-on-an-existing-azure-vm) (WaAppAgent.exe) mogelijk opnieuw installeren.

### <a name="solution-for-cause-2"></a>Oplossing voor oorzaak 2

Herstel de virtuele machine naar de laatste bekende juiste configuratie. Volg de stappen in [Azure Windows VM starten met de laatste bekende juiste configuratie](https://support.microsoft.com/help/4016731/).

### <a name="solution-for-cause-3"></a>Oplossing voor oorzaak 3
>[!NOTE]
>De volgende procedure mag alleen worden gebruikt als laatste resource. Tijdens het herstellen van regback kunnen de toegang tot de machine worden hersteld, wordt het besturings systeem niet als stabiel beschouwd, omdat er gegevens verloren zijn gegaan in het REGI ster tussen de tijds tempel van de Hive en de huidige dag. U moet een nieuwe VM bouwen en plannen maken om gegevens te migreren.

1. Zodra de schijf is gekoppeld aan een virtuele machine voor probleem oplossing, controleert u of de schijf is gemarkeerd als **online** in de schijf beheer-console.

2. Maak een kopie van de map **\Windows\System32\Config** als u de wijzigingen ongedaan wilt maken.

3. Kopieer de bestanden in de map **\Windows\System32\config\regback** en vervang de bestanden in de map **\Windows\System32\Config** .

4. Verwijder de schijf uit de virtuele machine voor probleem oplossing en wacht ongeveer 2 minuten tot Azure deze schijf heeft vrijgegeven.

5. [Maak een nieuwe virtuele machine op basis van de besturingssysteem schijf](../windows/create-vm-specialized.md).
