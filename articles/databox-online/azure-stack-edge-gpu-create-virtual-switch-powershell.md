---
title: Een nieuwe virtuele switch maken in Azure Stack rand via Power shell
description: Hierin wordt beschreven hoe u een virtuele switch op een Azure Stack edge-apparaat maakt met behulp van Power shell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 04/06/2021
ms.author: alkohli
ms.openlocfilehash: 1ad86695510a8fe93bbeeab27db53f5afbef92fd
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556176"
---
# <a name="create-a-new-virtual-switch-in-azure-stack-edge-pro-gpu-via-powershell"></a>Een nieuwe virtuele switch maken in Azure Stack Edge Pro GPU via Power shell

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In dit artikel wordt beschreven hoe u een nieuwe virtuele switch maakt op uw Azure Stack Edge Pro GPU-apparaat. U kunt bijvoorbeeld een nieuwe virtuele switch maken als u wilt dat uw virtuele machines verbinding maken via een andere fysieke netwerk poort.

## <a name="vm-deployment-workflow"></a>VM-implementatiewerkstroom

1. Maak verbinding met de Power shell-interface op het apparaat.
2. Beschik bare fysieke netwerk interfaces opvragen.
3. Maak een virtuele switch.
4. Controleer het virtuele netwerk en het subnet dat automatisch wordt gemaakt.

## <a name="prerequisites"></a>Vereisten

Zorg voordat u begint voor het volgende:

- U hebt toegang tot een client computer die toegang heeft tot de Power shell-interface van uw apparaat. Zie [verbinding maken met de Power shell-interface](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface). 

    Op de client computer moet een [ondersteund besturings systeem](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)worden uitgevoerd.

- Gebruik de lokale gebruikers interface om Compute in te scha kelen op een van de fysieke netwerk interfaces op uw apparaat volgens de instructies in het [berekenings netwerk inschakelen](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network) op het apparaat. 


## <a name="connect-to-the-powershell-interface"></a>Verbinding maken met de PowerShell-interface

[Verbinding maken met de Power shell-interface van uw apparaat](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

## <a name="query-available-network-interfaces"></a>Beschik bare netwerk interfaces opvragen

1. Gebruik de volgende opdracht om een lijst met fysieke netwerk interfaces weer te geven waarop u een nieuwe virtuele switch kunt maken. U selecteert een van deze netwerk interfaces.

    ```powershell
    Get-NetAdapter -Physical
    ```
    Hier volgt een voorbeeld van uitvoer:
    
    ```powershell
        [10.100.10.10]: PS>Get-NetAdapter -Physical
        
        Name                      InterfaceDescription                    ifIndex Status       MacAddress       LinkSpeed
        ----                      --------------------                    ------- ------       ----------        -----
        Port2                     QLogic 2x1GE+2x25GE QL41234HMCU NIC ...      12 Up           34-80-0D-05-26-EA ...ps
        Ethernet                  Remote NDIS Compatible Device                11 Up           F4-02-70-CD-41-39 ...ps
        Port1                     QLogic 2x1GE+2x25GE QL41234HMCU NI...#3       9 Up           34-80-0D-05-26-EB ...ps
        Port5                     Mellanox ConnectX-4 Lx Ethernet Ad...#2       8 Up           0C-42-A1-C0-E3-99 ...ps
        Port3                     QLogic 2x1GE+2x25GE QL41234HMCU NI...#4       7 Up           34-80-0D-05-26-E9 ...ps
        Port6                     Mellanox ConnectX-4 Lx Ethernet Adapter       6 Up           0C-42-A1-C0-E3-98 ...ps
        Port4                     QLogic 2x1GE+2x25GE QL41234HMCU NI...#2       4 Up           34-80-0D-05-26-E8 ...ps
        
        [10.100.10.10]: PS>
    ```
2. Kies een netwerk interface die:

    - In de status **up** . 
    - Wordt niet gebruikt door bestaande virtuele switches. Op dit moment kan slechts één vswitch per netwerk interface worden geconfigureerd. 
    
    Voer de opdracht uit om de bestaande virtuele switch en de netwerk interface koppeling te controleren `Get-HcsExternalVirtualSwitch` .
 
    Hier volgt een voor beeld van een uitvoer.

    ```powershell
    [10.100.10.10]: PS>Get-HcsExternalVirtualSwitch

    Name                          : vSwitch1
    InterfaceAlias                : {Port2}
    EnableIov                     : True
    MacAddressPools               :
    IPAddressPools                : {}
    ConfigurationSource           : Dsc
    EnabledForCompute             : True
    SupportsAcceleratedNetworking : False
    DbeDhcpHostVnicName           : f4a92de8-26ed-4597-a141-cb233c2ba0aa
    Type                          : External
    
    [10.100.10.10]: PS>
    ```
    In dit exemplaar is poort 2 gekoppeld aan een bestaande virtuele switch en mag niet worden gebruikt.

## <a name="create-a-virtual-switch"></a>Een virtuele switch maken

Gebruik de volgende cmdlet om een nieuwe virtuele switch te maken op de opgegeven netwerk interface. Nadat deze bewerking is voltooid, kunnen de reken instanties het nieuwe virtuele netwerk gebruiken.

```powershell
Add-HcsExternalVirtualSwitch -InterfaceAlias <Network interface name> -WaitForSwitchCreation $true
```

Gebruik de `Get-HcsExternalVirtualSwitch` opdracht om de zojuist gemaakte switch te identificeren. De nieuwe switch die wordt gemaakt, heeft de naam `vswitch-<InterfaceAlias>` . 

Hier volgt een voorbeeld van uitvoer:

```powershell
[10.100.10.10]: P> Add-HcsExternalVirtualSwitch -InterfaceAlias Port5 -WaitForSwitchCreation $true
[10.100.10.10]: PS>Get-HcsExternalVirtualSwitch

Name                          : vSwitch1
InterfaceAlias                : {Port2}
EnableIov                     : True
MacAddressPools               :
IPAddressPools                : {}
ConfigurationSource           : Dsc
EnabledForCompute             : True
SupportsAcceleratedNetworking : False
DbeDhcpHostVnicName           : f4a92de8-26ed-4597-a141-cb233c2ba0aa
Type                          : External

Name                          : vswitch-Port5
InterfaceAlias                : {Port5}
EnableIov                     : True
MacAddressPools               :
IPAddressPools                :
ConfigurationSource           : Dsc
EnabledForCompute             : False
SupportsAcceleratedNetworking : False
DbeDhcpHostVnicName           : 9b301c40-3daa-49bf-a20b-9f7889820129
Type                          : External

[10.100.10.10]: PS>
```

## <a name="verify-network-subnet"></a>Netwerk, subnet controleren 

Nadat u de nieuwe virtuele switch hebt gemaakt, maakt Azure Stack Edge Pro GPU automatisch een virtueel netwerk en een subnet dat hieraan overeenkomt. U kunt dit virtuele netwerk gebruiken bij het maken van Vm's.

<!--To identify the virtual network and subnet associated with the new switch that you created, use the `Get-HcsVirtualNetwork` command. This cmdlet will be released in April some time. -->

## <a name="next-steps"></a>Volgende stappen

- [Implementeer Vm's op uw Azure Stack Edge Pro GPU-apparaat via Azure PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)

- [Implementeer Vm's op uw Azure Stack Edge Pro GPU-apparaat via de Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
