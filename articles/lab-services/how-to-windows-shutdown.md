---
title: Hand leiding voor het beheren van het afsluit gedrag van Windows in Azure Lab Services | Microsoft Docs
description: Stappen voor het automatisch afsluiten van een niet-actieve Windows-virtuele machine en het verwijderen van de Windows-opdracht shutdown.
ms.topic: article
ms.date: 09/29/2020
ms.openlocfilehash: 248bbeabaf704ba636e2f82c7a93d0ee90a09f22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94647695"
---
# <a name="guide-to-controlling-windows-shutdown-behavior"></a>Hand leiding voor het beheren van het afsluit gedrag van Windows

Azure Lab Services biedt verschillende kosten controles om ervoor te zorgen dat virtuele Windows-machines (Vm's) niet onverwacht worden uitgevoerd:
 - [Een planning instellen](./tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
 - [Quota instellen voor gebruikers](./how-to-configure-student-usage.md#set-quotas-for-users)
 - [De functie Automatisch afsluiten wanneer de verbinding is verbroken inschakelen](./how-to-enable-shutdown-disconnect.md)

Zelfs met deze kosten controles zijn er situaties waarin een Windows-VM onverwacht kan worden uitgevoerd. en als gevolg hiervan wordt het quotum van de student afgetrokken:

- **Het RDP-venster is geopend**
  
    Wanneer een student verbinding maakt met hun VM met behulp van RDP, kunnen ze per ongeluk het RDP-venster openen.  Zolang het RDP-venster geopend blijft, wordt de instelling **automatisch afsluiten bij verbinding verbreken nooit van** kracht, omdat deze alleen wordt geactiveerd nadat de verbinding met de RDP-sessie is verbroken.

- **Windows shutdown opdracht wordt gebruikt om de virtuele machine uit te scha kelen**
  
    Een student kan Windows shutdown opdracht of andere afsluit mechanismen van Windows gebruiken om de virtuele machine uit te scha kelen in plaats van [Azure Lab Services ' Stop knop '](./how-to-use-classroom-lab.md#start-or-stop-the-vm).  Als dit gebeurt, wordt de VM in het perspectief van Azure Lab Services nog steeds gebruikt.
    
Deze hand leiding bevat stappen voor het automatisch afsluiten van een niet-actieve Windows-VM en het verwijderen van de Windows-opdracht shutdown uit het **Start** menu om te voor komen dat deze situaties zich voordoen.  

> [!NOTE]
> Een virtuele machine kan ook onverwacht van het quotum aftrekken wanneer de student hun virtuele machine start, maar er geen verbinding mee maakt met behulp van RDP.  Deze hand leiding biedt momenteel *geen* ondersteuning voor dit scenario.  In plaats daarvan moeten studenten worden aangeraden om direct verbinding te maken met hun VM met behulp van RDP nadat ze deze hebben gestart. of ze moeten de virtuele machine stoppen.

## <a name="remove-windows-shutdown-command-from-start-menu"></a>De opdracht Windows afsluiten uit het start menu verwijderen

Met Windows **Local Groepsbeleid** -instellingen kunt u ook de afsluit opdracht verwijderen uit het **Start** menu.

Als u de afsluit opdracht wilt verwijderen, kunt u verbinding maken met de VM van de sjabloon en het onderstaande Power shell-script uitvoeren.

```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" -Name "HidePowerOptions" -Value 1 -Force
```

U kunt er ook voor kiezen om deze hand matige stappen te volgen met behulp van de sjabloon-VM:

1. Druk op de Windows-toets, typ **gpedit** en selecteer vervolgens **groeps beleid bewerken (configuratie scherm)**.

1. Ga naar **computer configuratie > Beheersjablonen > menu Start en de taak balk**.  

    ![Editor voor lokaal groepsbeleid](./media/how-to-windows-shutdown/group-policy-shutdown.png)

1. Klik met de rechter muisknop op **verwijderen en voorkom de toegang tot de opdrachten afsluiten, opnieuw opstarten, slaap stand en sluimer stand** en klik op **bewerken**.

1. Selecteer de **ingeschakelde** instelling en klik vervolgens op **OK**:
 
   ![Instelling voor afsluiten](./media/how-to-windows-shutdown/edit-shutdown.png)

1. U ziet dat de afsluit opdracht niet meer wordt weer gegeven onder het menu **Start** van Windows. alleen de opdracht **Disconnect** wordt weer gegeven.

    ![Opdracht shutdown](./media/how-to-windows-shutdown/start-menu.png)

## <a name="next-steps"></a>Volgende stappen
Zie het artikel over het voorbereiden van een Windows-sjabloon-VM: [hand leiding voor het instellen van een Windows-sjabloon computer in Azure Lab Services](how-to-prepare-windows-template.md)