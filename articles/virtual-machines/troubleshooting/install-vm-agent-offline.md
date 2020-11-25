---
title: De Azure VM-agent installeren in de offline modus | Microsoft Docs
description: Meer informatie over het installeren van de Azure VM-agent in de offline modus.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 07/06/2020
ms.author: genli
ms.openlocfilehash: 456aa225fa8eed47ca794c54e61b77a30c93fa9a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002609"
---
# <a name="install-the-azure-virtual-machine-agent-in-offline-mode"></a>De Azure virtual machine-agent installeren in de offline modus 

De Azure virtual machine agent (VM-agent) biedt nuttige functies, zoals het opnieuw instellen van het wacht woord voor de lokale beheerder en het pushen van scripts. In dit artikel wordt beschreven hoe u de VM-agent voor een offline virtuele Windows-machine (VM) installeert. 

## <a name="when-to-use-the-vm-agent-in-offline-mode"></a>Wanneer de VM-agent in de offline modus moet worden gebruikt

Installeer de VM-agent in de offline modus in de volgende scenario's:

- De VM-agent is niet geïnstalleerd op de geïmplementeerde virtuele machine van Azure of de agent werkt niet.
- U hebt het beheerders wachtwoord voor de virtuele machine verg eten of u hebt geen toegang tot de virtuele machine.

## <a name="how-to-install-the-vm-agent-in-offline-mode"></a>De VM-agent installeren in de offline modus

Gebruik de volgende stappen om de VM-agent in de offline modus te installeren.

### <a name="step-1-attach-the-os-disk-of-the-vm-to-another-vm-as-a-data-disk"></a>Stap 1: de besturingssysteem schijf van de virtuele machine als een gegevens schijf aan een andere virtuele machine koppelen

1. Maak een moment opname van de besturingssysteem schijf van de betreffende virtuele machine, maakt een schijf van de moment opname en koppel de schijf vervolgens aan een virtuele machine voor probleem oplossing. Zie [problemen met een Windows-VM oplossen door de besturingssysteem schijf te koppelen aan een herstel-VM met behulp van de Azure Portal](troubleshoot-recovery-disks-portal-windows.md)voor meer informatie. Voor de klassieke virtuele machine verwijdert u de virtuele machine en bewaart u de besturingssysteem schijf en koppelt u de besturingssysteem schijf aan de virtuele machine voor probleem oplossing.

2.  Maak verbinding met de virtuele machine voor probleem oplossing. Open **Computer management**  >  **schijf beheer** van computer beheer. Controleer of de besturingssysteem schijf online is en of de stationsletters zijn toegewezen aan de schijf partities.

### <a name="step-2-modify-the-os-disk-to-install-the-azure-vm-agent"></a>Stap 2: Wijzig de besturingssysteem schijf om de Azure VM-agent te installeren

1.  Een verbinding met een extern bureau blad maken met de virtuele machine voor probleem oplossing.

2.  Blader in de virtuele machine van de probleem oplossing naar de besturingssysteem schijf die u hebt toegevoegd, open de map \Windows\System32\Config. Kopieer alle bestanden in deze map als back-up als een terugdraai actie vereist is.

3.  Start de **REGI ster-editor** (regedit.exe).

4.  Selecteer de **HKEY_LOCAL_MACHINE** sleutel. Selecteer in het menu **bestand**  >  **laden component**:

    ![De Hive laden](./media/install-vm-agent-offline/load-hive.png)

5.  Blader naar de map \windows\system32\config\SYSTEM op de besturingssysteem schijf die u hebt toegevoegd. Voer **BROKENSYSTEM** in voor de naam van de component. De nieuwe register component wordt weer gegeven onder de sleutel **HKEY_LOCAL_MACHINE** .

6.  Blader naar de map \windows\system32\config\SOFTWARE op de besturingssysteem schijf die u hebt toegevoegd. Voer **BROKENSOFTWARE** in als de naam van de Hive-software.

7. Als op de gekoppelde besturingssysteem schijf de VM-agent is geïnstalleerd, voert u een back-up uit van de huidige configuratie. Als er geen VM-agent is geïnstalleerd, gaat u verder met de volgende stap.
      
    1. Wijzig de naam van de map \windowsazure in \windowsazure.old.

    2. De volgende registers exporteren:
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\RdAgent

8.  Gebruik de bestaande bestanden op de virtuele machine voor probleem oplossing als opslag plaats voor de installatie van de VM-agent. Voer de volgende stappen uit:

    1. Exporteer de volgende subsleutels in de register indeling (. reg) van de virtuele machine voor probleem oplossing: 
        - HKEY_LOCAL_MACHINE \SYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE \SYSTEM\ControlSet001\Services\RdAgent

          ![De subsleutels van het REGI ster exporteren](./media/install-vm-agent-offline/backup-reg.png)

    2. Bewerk de register bestanden. Wijzig in elk bestand het vermeldings waarde **systeem** in **BROKENSYSTEM** (zoals weer gegeven in de volgende installatie kopieën) en sla het bestand op. Onthoud het **ImagePath** van de huidige VM-agent. U moet de bijbehorende map kopiëren naar de gekoppelde besturingssysteem schijf. 

        ![De waarden van de registersubsleutel wijzigen](./media/install-vm-agent-offline/change-reg.png)

    3. Importeer de register bestanden in de opslag plaats door te dubbel klikken op elk register bestand.

    4. Controleer of de volgende twee subsleutels zijn geïmporteerd in de **BROKENSYSTEM** -component:
        - WindowsAzureGuestAgent
        - RdAgent

    5. Kopieer de installatiemap van de huidige VM-agent naar de gekoppelde besturingssysteem schijf: 

        1.  Maak op de besturingssysteem schijf die u hebt gekoppeld, een map met de naam WindowsAzure in het hoofdpad.

        2.  Ga naar C:\WindowsAzure op de virtuele machine voor probleem oplossing, zoek naar een map met de naam C:\WindowsAzure\ GuestAgent_X. X. XXXX. XXX. Kopieer de werken-map met het meest recente versie nummer van C:\WindowsAzure naar de map WindowsAzure in de gekoppelde besturingssysteem schijf. Als u niet zeker weet welke map moet worden gekopieerd, kopieert u alle werken-mappen. In de volgende afbeelding ziet u een voor beeld van de map werken die wordt gekopieerd naar de gekoppelde besturingssysteem schijf.

             ![Map werken kopiëren](./media/install-vm-agent-offline/copy-files.png)

9.  Selecteer **BROKENSYSTEM**. Selecteer in het menu het **File**  >  **onderdeel bestand verwijderen**.

10.  Selecteer **BROKENSOFTWARE**. Selecteer in het menu het **File**  >  **onderdeel bestand verwijderen**.

11.  Ontkoppel de besturingssysteem schijf en wijzig vervolgens [de besturingssysteem schijf voor de betrokken VM](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm). Maak voor de klassieke virtuele machine een nieuwe virtuele machine met behulp van de gerepareerde besturingssysteem schijf.

12.  Toegang tot de virtuele machine. U ziet dat de RdAgent wordt uitgevoerd en de logboeken worden gegenereerd.

Als u de virtuele machine hebt gemaakt met behulp van het Resource Manager-implementatie model, bent u klaar.

### <a name="use-the-provisionguestagent-property-for-classic-vms"></a>Gebruik de eigenschap ProvisionGuestAgent voor klassieke Vm's

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Als u de virtuele machine hebt gemaakt met behulp van het klassieke model, gebruikt u de module Azure PowerShell om de eigenschap **ProvisionGuestAgent** bij te werken. De eigenschap informeert Azure dat de VM-agent op de VM is geïnstalleerd.

Als u de eigenschap **ProvisionGuestAgent** wilt instellen, voert u de volgende opdrachten uit in azure PowerShell:

   ```powershell
   $vm = Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   $vm.VM.ProvisionGuestAgent = $true
   Update-AzureVM –Name <VM name> –VM $vm.VM –ServiceName <cloud service name>
   ```

Voer vervolgens de `Get-AzureVM` opdracht uit. U ziet dat de eigenschap **GuestAgentStatus** nu is gevuld met gegevens:

   ```powershell
   Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   GuestAgentStatus:Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVMModel.GuestAgentStatus
   ```

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van de agent voor virtuele Azure-machines](../extensions/agent-windows.md)
- [Extensies en functies van virtuele machines voor Windows](../extensions/features-windows.md)
