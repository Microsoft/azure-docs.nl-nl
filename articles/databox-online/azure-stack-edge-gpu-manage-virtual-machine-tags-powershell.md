---
title: VM-Tags beheren op Azure Stack Edge Pro GPU-apparaat via Azure PowerShell
description: Hierin wordt beschreven hoe u virtuele-machine tags maakt en beheert voor virtuele machines die in Azure Stack Edge worden uitgevoerd met behulp van Azure PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 04/06/2021
ms.author: alkohli
ms.openlocfilehash: be4348359e6b53c3e7454e9ab7c1af8ce8a7020a
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305549"
---
# <a name="manage-vm-tags-on-azure-stack-edge-via-azure-powershell"></a>VM-labels op Azure Stack rand beheren via Azure PowerShell

In dit artikel wordt beschreven hoe u virtuele machines (Vm's) die worden uitgevoerd op uw Azure Stack Edge Pro GPU-apparaten kunt labelen met behulp van Azure PowerShell.

## <a name="about-tags"></a>Info over Tags

Tags zijn door de gebruiker gedefinieerde sleutel-waardeparen die kunnen worden toegewezen aan een resource of resource groep. U kunt Tags Toep assen op Vm's die op uw apparaat worden uitgevoerd om ze logisch in een taxonomie te organiseren. U kunt tags op een resource plaatsen op het moment dat deze wordt gemaakt of aan een bestaande resource worden toegevoegd. U kunt bijvoorbeeld de naam `Organization` en de waarde Toep assen `Engineering` op alle virtuele machines die worden gebruikt door de technische afdeling in uw organisatie.

Zie [Tags beheren via AzureRM Power shell](/powershell/module/azurerm.tags/?view=azurermps-6.13.0&preserve-view=true)voor meer informatie over tags.

## <a name="prerequisites"></a>Vereisten

Voordat u een virtuele machine op uw apparaat kunt implementeren via Power shell, moet u het volgende doen:

- U hebt toegang tot een client die u gebruikt om verbinding te maken met uw apparaat.
    - De client voert een [ondersteund besturings systeem](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)uit.
    - Uw client is geconfigureerd om verbinding te maken met de lokale Azure Resource Manager van uw apparaat volgens de instructies in [verbinding maken met Azure Resource Manager voor uw apparaat](azure-stack-edge-gpu-connect-resource-manager.md).


## <a name="verify-connection-to-local-azure-resource-manager"></a>Verbinding met lokale Azure Resource Manager controleren

[!INCLUDE [azure-stack-edge-gateway-verify-azure-resource-manager-connection](../../includes/azure-stack-edge-gateway-verify-azure-resource-manager-connection.md)]


## <a name="add-a-tag-to-a-vm"></a>Een tag toevoegen aan een VM

1. Stel een aantal para meters in.

    ```powershell
    $VMName = <VM Name>
    $VMRG = <VM Resource Group>
    $TagName = <Tag Name>
    $TagValue = <Tag Value>   
    ```

    Hier volgt een voorbeeld van uitvoer:

    ```powershell
    PS C:\WINDOWS\system32> $VMName = "myasetestvm1"
    PS C:\WINDOWS\system32> $VMRG = "myaserg2"
    PS C:\WINDOWS\system32> $TagName = "Organization"
    PS C:\WINDOWS\system32> $TagValue = "Sales"
    ```

2. Haal het VM-object en de bijbehorende tags op.

   ```powershell
   $VirtualMachine = Get-AzureRmVM -ResourceGroupName $VMRG -Name $VMName
   $tags = $VirtualMachine.Tags
   ```

3. Voeg de tag toe en werk de virtuele machine bij. Het bijwerken van de virtuele machine kan enkele minuten duren.

    U kunt de vlag optionele **-Force** gebruiken om de opdracht zonder gebruikers bevestiging uit te voeren.

    ```powershell
    $tags.Add($TagName, $TagValue)
    Set-AzureRmResource -ResourceId $VirtualMachine.Id -Tag $tags [-Force]
    ```

    Hier volgt een voorbeeld van uitvoer:

    ```powershell
    PS C:\WINDOWS\system32> $VirtualMachine = Get-AzureRMVM -ResourceGroupName $VMRG -Name $VMName
    PS C:\WINDOWS\system32> $tags = $VirtualMachine.Tags
    PS C:\WINDOWS\system32> $tags.Add($TagName, $TagValue)
    PS C:\WINDOWS\system32> Set-AzureRmResource -ResourceID $VirtualMachine.ID -Tag $tags -Force
    
    Name              : myasetestvm1
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg2/providers/Microsoft.Compute/virtua
                        lMachines/myasetestvm1
    ResourceName      : myasetestvm1
    ResourceType      : Microsoft.Compute/virtualMachines
    ResourceGroupName : myaserg2
    Location          : dbelocal
    SubscriptionId    : 992601bc-b03d-4d72-598e-d24eac232122
    Tags              : {Organization}
    Properties        : @{vmId=958c0baa-e143-4d8a-82bd-9c6b1ba45e86; hardwareProfile=; storageProfile=; osProfile=; networkProfile=;
                        provisioningState=Succeeded}
    
    PS C:\WINDOWS\system32>
    ```

Zie [add-AzureRMTag](/powershell/module/azurerm.tags/remove-azurermtag?view=azurermps-6.13.0&preserve-view=true)voor meer informatie.

## <a name="view-tags-of-a-vm"></a>Tags van een virtuele machine weer geven

U kunt de labels weer geven die zijn toegepast op een specifieke virtuele machine die op uw apparaat wordt uitgevoerd. 

1. Definieer de para meters die zijn gekoppeld aan de virtuele machine waarvan u de labels wilt weer geven.

   ```powershell
   $VMName = <VM Name>
   $VMRG = <VM Resource Group>
   ```
    Hier volgt een voorbeeld van uitvoer:

    ```powershell
    PS C:\WINDOWS\system32> $VMName = "myasetestvm1"
    PS C:\WINDOWS\system32> $VMRG = "myaserg2"
    PS C:\WINDOWS\system32> $TagName = "Organization"
    PS C:\WINDOWS\system32> $TagValue = "Sales"
    ```
1. Haal het VM-object op en Bekijk de labels ervan.

   ```powershell
   $VirtualMachine = Get-AzureRmVM -ResourceGroupName $VMRG -Name $VMName
   $VirtualMachine.Tags
   ```
    Hier volgt een voorbeeld van uitvoer:

    ```powershell
    PS C:\WINDOWS\system32> $VirtualMachine = Get-AzureRMVM -ResourceGroupName $VMRG -Name $VMName
    PS C:\WINDOWS\system32> $VirtualMachine

    ResourceGroupName : myaserg2
    Id                : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg2/providers/Microsoft.Compute/virtua
    lMachines/myasetestvm1
    VmId              : 958c0baa-e143-4d8a-82bd-9c6b1ba45e86
    Name              : myasetestvm1
    Type              : Microsoft.Compute/virtualMachines
    Location          : dbelocal
    Tags              : {"Organization":"Sales"}
    HardwareProfile   : {VmSize}
    NetworkProfile    : {NetworkInterfaces}
    OSProfile         : {ComputerName, AdminUsername, LinuxConfiguration, Secrets}
    ProvisioningState : Succeeded
    StorageProfile    : {ImageReference, OsDisk, DataDisks}
    
    PS C:\WINDOWS\system32>
    ```
## <a name="view-tags-for-all-resources"></a>Labels weer geven voor alle resources

Gebruik de opdracht om de huidige lijst met tags weer te geven voor alle resources in het lokale Azure Resource Manager abonnement (die verschillen van uw Azure-abonnement) van uw apparaat `Get-AzureRMTag` .


Hier volgt een voor beeld van een uitvoer wanneer er meerdere Vm's op het apparaat worden uitgevoerd en u alle labels op alle Vm's wilt weer geven.

```powershell
PS C:\WINDOWS\system32> Get-AzureRMTag

Name         Count
----         -----
Organization 3

PS C:\WINDOWS\system32>
```

De voor gaande uitvoer geeft aan dat er drie `Organization` Tags zijn op de virtuele machines die op het apparaat worden uitgevoerd.

Als u meer details wilt weer geven, gebruikt u de `-Detailed` para meter.

```powershell
PS C:\WINDOWS\system32> Get-AzureRMTag -Detailed |fl

Name        : Organization
ValuesTable :
              Name         Count
              ===========  =====
              Engineering  2
              Sales        1

Count       : 3
Values      : {Engineering, Sales}

PS C:\WINDOWS\system32>
```

In de voor gaande uitvoer wordt aangegeven dat er uit de drie tags bestaan, 2 Vm's worden gelabeld alsof het `Engineering` een label heeft die hoort bij `Sales` .

## <a name="remove-a-tag-from-a-vm"></a>Een tag verwijderen uit een VM

1. Stel een aantal para meters in. 

    ```powershell
    $VMName = <VM Name>
    $VMRG = <VM Resource Group>
    $TagName = <Tag Name>
   ``` 
    Hier volgt een voorbeeld van uitvoer:

    ```powershell
    PS C:\WINDOWS\system32> $VMName = "myaselinuxvm1"
    PS C:\WINDOWS\system32> $VMRG = "myaserg1"
    PS C:\WINDOWS\system32> $TagName = "Organization" 
    ```
2. Haal het VM-object op.

    ```powershell
    $VirtualMachine = Get-AzureRmVM -ResourceGroupName $VMRG -Name $VMName
    $VirtualMachine   
    ```   

    Hier volgt een voorbeeld van uitvoer:

    ```powershell
    PS C:\WINDOWS\system32> $VirtualMachine = Get-AzureRMVM -ResourceGroupName $VMRG -Name $VMName
    ResourceGroupName : myaserg1
    Id                : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg1/providers/Microsoft.Compute/virtualMachines/myaselinuxvm1
    VmId              : 290b3fdd-0c99-4905-9ea1-cf93cd6f25ee
    Name              : myaselinuxvm1
    Type              : Microsoft.Compute/virtualMachines
    Location          : dbelocal
    Tags              : {"Organization":"Engineering"}
    HardwareProfile   : {VmSize}
    NetworkProfile    : {NetworkInterfaces}
    OSProfile         : {ComputerName, AdminUsername, LinuxConfiguration, Secrets}
    ProvisioningState : Succeeded
    StorageProfile    : {ImageReference, OsDisk, DataDisks}
                                                                              PS C:\WINDOWS\system32> 
    ```
3. Verwijder het label en werk de virtuele machine bij. Gebruik de optionele `-Force` vlag om de opdracht zonder gebruikers bevestiging uit te voeren.

    ```powershell
    $tags = $VirtualMachine.Tags
    $tags.Remove($TagName)
    Set-AzureRmResource -ResourceId $VirtualMachine.Id -Tag $tags [-Force]
    ```
    Hier volgt een voorbeeld van uitvoer:

    ```powershell
    PS C:\WINDOWS\system32> $tags = $Virtualmachine.Tags
    PS C:\WINDOWS\system32> $tags
    Key          Value
    ---          -----
    Organization Engineering
    PS C:\WINDOWS\system32> $tags.Remove($TagName)
    True
    PS C:\WINDOWS\system32> Set-AzureRMResource -ResourceID $VirtualMachine.ID -Tag $tags -Force
    Name              : myaselinuxvm1
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGrou
                        ps/myaserg1/providers/Microsoft.Compute/virtualMachines/myaselin
                        uxvm1
    ResourceName      : myaselinuxvm1
    ResourceType      : Microsoft.Compute/virtualMachines
    ResourceGroupName : myaserg1
    Location          : dbelocal
    SubscriptionId    : 992601bc-b03d-4d72-598e-d24eac232122
    Tags              : {}
    Properties        : @{vmId=290b3fdd-0c99-4905-9ea1-cf93cd6f25ee; hardwareProfile=;
                        storageProfile=; osProfile=; networkProfile=;
                        provisioningState=Succeeded}
    PS C:\WINDOWS\system32>
    ```


## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [beheren van Tags via AzureRM Power shell](/powershell/module/azurerm.tags/?view=azurermps-6.13.0&preserve-view=true).
