---
title: Verbinding maken met de console van de virtuele machine op Azure Stack Edge Pro GPU-apparaat
description: Hierin wordt beschreven hoe u verbinding maakt met de console van de virtuele machine op een VM die wordt uitgevoerd op Azure Stack Edge Pro GPU-apparaat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/22/2021
ms.author: alkohli
ms.openlocfilehash: 68cf157a512e9b1f6caee4734869c5bb4b836e2f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104962568"
---
# <a name="connect-to-a-virtual-machine-console-on-an-azure-stack-edge-pro-gpu-device"></a>Verbinding maken met een virtuele-machine console op een Azure Stack Edge Pro GPU-apparaat

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Met Azure Stack Edge Pro GPU-oplossing worden niet-container werkbelastingen uitgevoerd via de virtuele machines. In dit artikel wordt beschreven hoe u verbinding maakt met de console van een virtuele machine die op het apparaat is geïmplementeerd. 

Met de console van de virtuele machine kunt u toegang krijgen tot uw Vm's met toetsen bord-, muis-en scherm functies met behulp van de algemeen beschik bare hulpprogram ma's voor extern bureau blad. U hebt toegang tot de-console en kunt problemen oplossen die zijn opgetreden bij het implementeren van een virtuele machine op het apparaat. U kunt verbinding maken met de console van de virtuele machine, zelfs als uw VM niet is ingericht.


## <a name="workflow"></a>Werkstroom

De werk stroom op hoog niveau heeft de volgende stappen:

1. Maak verbinding met de Power shell-interface op het apparaat.
1. Schakel console toegang tot de virtuele machine in.
1. Maak verbinding met de virtuele machine met behulp van Remote Desktop Protocol (RDP).
1. Console toegang tot de virtuele machine intrekken.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u ervoor zorgen dat u de volgende vereisten hebt voltooid:

#### <a name="for-your-device"></a>Voor uw apparaat

U moet toegang hebben tot een Azure Stack Edge Pro GPU-apparaat dat is geactiveerd. Op het apparaat moeten een of meer virtuele machines zijn geïmplementeerd. U kunt Vm's implementeren via Azure PowerShell, via de sjablonen of via de Azure Portal.

#### <a name="for-client-accessing-the-device"></a>Voor client toegang tot het apparaat

Zorg ervoor dat u toegang hebt tot een client systeem dat:

- Kan toegang krijgen tot de Power shell-interface van het apparaat. Op de client wordt een [besturings systeem](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)uitgevoerd dat wordt ondersteund.
- Power shell 7,0 of hoger wordt uitgevoerd op de client. Deze versie van Power shell werkt voor Windows-, Mac-en Linux-clients. Zie de instructies voor het [installeren van Power shell 7,0](/powershell/scripting/whats-new/what-s-new-in-powershell-70?view=powershell-7.1&preserve-view=true).
- Heeft mogelijkheden voor extern bureau blad. Afhankelijk van of u Windows, macOS of Linux gebruikt, moet u een van deze [extern bureau blad-clients](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)installeren. Dit artikel bevat instructies voor [Windows Extern bureaublad](/windows-server/remote/remote-desktop-services/clients/windowsdesktop#install-the-client) en [FreeRDP](https://www.freerdp.com/). <!--Which version of FreeRDP to use?-->


## <a name="connect-to-vm-console"></a>Verbinding maken met VM-console

Volg deze stappen om verbinding te maken met de console van de virtuele machine op het apparaat.

### <a name="connect-to-the-powershell-interface-on-your-device"></a>Verbinding maken met de Power shell-interface op het apparaat

De eerste stap is om [verbinding te maken met de Power shell-interface](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) van uw apparaat. 

### <a name="enable-console-access-to-the-vm"></a>Console toegang tot de virtuele machine inschakelen

1.  Voer in de Power shell-interface de volgende opdracht uit om toegang tot de VM-console in te scha kelen.

    ```powershell
    Grant-HcsVMConnectAccess -ResourceGroupName <VM resource group> -VirtualMachineName <VM name>
    ```
2. In de voorbeeld uitvoer noteert u de ID van de virtuele machine. Dit hebt u nodig voor een latere stap.

    ```powershell
    [10.100.10.10]: PS>Grant-HcsVMConnectAccess -ResourceGroupName mywindowsvm1rg -VirtualMachineName mywindowsvm1
        
    VirtualMachineId       : 81462e0a-decb-4cd4-96e9-057094040063
    VirtualMachineHostName : 3V78B03
    ResourceGroupName      : mywindowsvm1rg
    VirtualMachineName     : mywindowsvm1
    Id                     : 81462e0a-decb-4cd4-96e9-057094040063
    [10.100.10.10]: PS>
    ```

### <a name="connect-to-the-vm"></a>Verbinding maken met de virtuele machine

U kunt nu een Extern bureaublad-client gebruiken om verbinding te maken met de console van de virtuele machine.

#### <a name="use-windows-remote-desktop"></a>Windows Extern bureaublad gebruiken

1. Maak een nieuw tekst bestand en voer de volgende tekst in.

    ```
    pcb:s:<VM ID from PowerShell>;EnhancedMode=0
    full address:s:<IP address of the device>   
    server port:i:2179
    username:s:EdgeARMUser
    negotiate security layer:i:0
    ```
1. Sla het bestand op als **. RDP* op het client systeem. U gebruikt dit profiel om verbinding te maken met de virtuele machine.
1. Dubbel klik op het profiel om verbinding te maken met de virtuele machine. Geef de volgende referenties op:

    - **Gebruikers naam**: Meld u aan als EdgeARMUser.
    - **Wacht woord**: Geef het lokale Azure Resource Manager wacht woord voor uw apparaat op. Als u het wacht woord bent verg eten, moet u [Azure Resource Manager wacht woord opnieuw instellen via de Azure Portal](azure-stack-edge-gpu-set-azure-resource-manager-password.md#reset-password-via-the-azure-portal). 

#### <a name="use-freerdp"></a>FreeRDP gebruiken

Als u FreeRDP op uw Linux-client gebruikt, voert u de volgende opdracht uit: 

```powershell
./wfreerdp /u:EdgeARMUser /vmconnect:<VM ID from PowerShell> /v:<IP address of the device>
```

## <a name="revoke-vm-console-access"></a>VM-console toegang intrekken

Als u de toegang tot de VM-console wilt intrekken, gaat u terug naar de Power shell-interface van uw apparaat. Voer de volgende opdracht uit:

```
Revoke-HcsVMConnectAccess -ResourceGroupName <VM resource group> -VirtualMachineName <VM name>
```
Hier volgt een voorbeeld van uitvoer:

```powershell
[10.100.10.10]: PS>Revoke-HcsVMConnectAccess -ResourceGroupName mywindowsvm1rg -VirtualMachineName mywindowsvm1

VirtualMachineId       : 81462e0a-decb-4cd4-96e9-057094040063
VirtualMachineHostName : 3V78B03
ResourceGroupName      : mywindowsvm1rg
VirtualMachineName     : mywindowsvm1
Id                     : 81462e0a-decb-4cd4-96e9-057094040063

[10.100.10.10]: PS>
```
> [!NOTE] 
> Wanneer u klaar bent met het gebruik van de VM-console, trekt u de toegang in of sluit u het Power shell-venster om de sessie af te sluiten. 

## <a name="next-steps"></a>Volgende stappen

- Problemen met [Azure stack Edge Pro](azure-stack-edge-gpu-troubleshoot.md) in azure Portal oplossen.