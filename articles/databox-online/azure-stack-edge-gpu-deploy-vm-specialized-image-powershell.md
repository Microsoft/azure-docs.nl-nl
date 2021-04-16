---
title: VM-afbeeldingen maken van een gespecialiseerde windows-VHD-Azure Stack Edge Pro GPU-apparaat
description: Beschrijft hoe u VM-afbeeldingen maakt op basis van gespecialiseerde afbeeldingen vanaf een Windows-VHD of een VHDX. Gebruik deze gespecialiseerde afbeelding om VM-afbeeldingen te maken voor gebruik met VM's die zijn geïmplementeerd op Azure Stack Edge Pro GPU-apparaat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 04/15/2021
ms.author: alkohli
ms.openlocfilehash: 6bfa42e99f295b429eba40a27eb59becb8aa80a1
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575940"
---
# <a name="deploy-a-vm-from-a-specialized-image-on-your-azure-stack-edge-pro-gpu-device-via-azure-powershell"></a>Implementeer een VM vanuit een gespecialiseerde afbeelding op uw Azure Stack Edge Pro GPU-apparaat via Azure PowerShell 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In dit artikel worden de stappen beschreven die nodig zijn voor het implementeren van een virtuele machine (VM) op uw Azure Stack Edge Pro GPU-apparaat vanuit een gespecialiseerde afbeelding. 

Zie Prepare [generalized image from Windows VHD](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md) (Ge generaliseerde afbeelding voorbereiden vanuit Windows VHD) of [Prepare generalized image from an ISO](azure-stack-edge-gpu-prepare-windows-generalized-image-iso.md)(Ge generaliseerde afbeelding voorbereiden vanuit een ISO) als u een gealeraliseerde afbeelding wilt voorbereiden voor het implementeren van VM's in Azure Stack Edge Pro GPU.

## <a name="about-vm-images"></a>Over VM-afbeeldingen

Een Windows-VHD of VHDX kan worden gebruikt voor het maken van een *gespecialiseerde* of ge *generaliseerde afbeelding.* De volgende tabel bevat een overzicht van de belangrijkste verschillen tussen de *gespecialiseerde* en *de ge generaliseerde* afbeeldingen.

[!INCLUDE [about-vm-images-for-azure-stack-edge](../../includes/azure-stack-edge-about-vm-images.md)]

## <a name="workflow"></a>Werkstroom

De werkstroom op hoog niveau voor het implementeren van een VM vanuit een gespecialiseerde afbeelding is:

1. Kopieer de VHD naar een lokaal opslagaccount op uw Azure Stack Edge Pro GPU-apparaat.
1. Maak een nieuwe beheerde schijf van de VHD.
1. Maak een nieuwe virtuele machine van de beheerde schijf en koppel de beheerde schijf.

## <a name="prerequisites"></a>Vereisten

Voordat u een VM op uw apparaat kunt implementeren via PowerShell, moet u ervoor zorgen dat:

- U hebt toegang tot een client die u gebruikt om verbinding te maken met uw apparaat.
    - Op uw client wordt een [ondersteund besturingssysteem uitgevoerd.](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)
    - Uw client is geconfigureerd om verbinding te maken met de lokale Azure Resource Manager van uw apparaat volgens de instructies in Verbinding maken met Azure Resource Manager [voor uw apparaat.](azure-stack-edge-gpu-connect-resource-manager.md)

## <a name="verify-the-local-azure-resource-manager-connection"></a>De lokale verbinding Azure Resource Manager controleren

Controleer of uw client verbinding kan maken met de lokale Azure Resource Manager. 

1. Roep API's van lokale apparaten aan om te verifiëren:

    ```powershell
    Login-AzureRMAccount -EnvironmentName <Environment Name>
    ```

2. Geef de gebruikersnaam `EdgeArmUser` en het wachtwoord op om verbinding te maken via Azure Resource Manager. Als u het wachtwoord niet meer weet, [stelt u het](azure-stack-edge-gpu-set-azure-resource-manager-password.md) wachtwoord voor Azure Resource Manager en gebruikt u dit wachtwoord om u aan te melden.

## <a name="deploy-vm-from-specialized-image"></a>VM implementeren vanuit gespecialiseerde afbeelding

De volgende secties bevatten stapsgewijs instructies voor het implementeren van een VM vanuit een gespecialiseerde afbeelding.

## <a name="copy-vhd-to-local-storage-account-on-device"></a>VHD kopiëren naar lokaal opslagaccount op apparaat

Volg deze stappen om de VHD te kopiëren naar het lokale opslagaccount:

1. Kopieer de bron-VHD naar een lokaal blobopslagaccount op uw Azure Stack Edge.

1. Noteer de resulterende URI. U gebruikt deze URI in een latere stap.

    Zie de [secties](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#create-a-storage-account) Een opslagaccount maken via Een [VHD](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#upload-a-vhd) uploaden in het artikel VM's implementeren op uw Azure Stack Edge-apparaat via Azure PowerShell om een lokaal opslagaccount te maken en er toegang [toe te krijgen.](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md) 

## <a name="create-a-managed-disk-from-vhd"></a>Een beheerde schijf maken van VHD

Volg deze stappen om een beheerde schijf te maken van een VHD die u eerder naar het opslagaccount hebt geüpload:

1. Stel enkele parameters in.

    ```powershell
    $VhdURI = <URI of VHD in local storage account>
    $DiskRG = <managed disk resource group>
    $DiskName = <managed disk name>    
    ```
    Hier is een voorbeeld van uitvoer.

    ```powershell
    PS C:\WINDOWS\system32> $VHDURI = "https://myasevmsa.blob.myasegpudev.wdshcsso.com/vhds/WindowsServer2016Datacenter.vhd"
    PS C:\WINDOWS\system32> $DiskRG = "myasevm1rg"
    PS C:\WINDOWS\system32> $DiskName = "myasemd1"
    ```
1. Maak een nieuwe beheerde schijf.

    ```powershell
    $DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri $VhdURI
    $disk = New-AzureRMDisk -ResourceGroupName $DiskRG -DiskName $DiskName -Disk $DiskConfig
    ```

    Hier is een voorbeeld van uitvoer. De locatie hier is ingesteld op de locatie van het lokale opslagaccount en is altijd voor alle lokale opslagaccounts op uw `DBELocal` Azure Stack Edge Pro GPU-apparaat. 

    ```powershell
    PS C:\WINDOWS\system32> $DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri $VHDURI
    PS C:\WINDOWS\system32> $disk = New-AzureRMDisk -ResourceGroupName $DiskRG -DiskName $DiskName -Disk $DiskConfig
    PS C:\WINDOWS\system32>    
    ```
## <a name="create-a-vm-from-managed-disk"></a>Een VM maken van een beheerde schijf

Volg deze stappen om een VM te maken van een beheerde schijf:

1. Stel enkele parameters in.

    ```powershell
    $NicRG = <NIC resource group>
    $NicName = <NIC name>
    $IPConfigName = <IP config name>
    $PrivateIP = <IP address> #Optional
    
    $VMRG = <VM resource group>
    $VMName = <VM name>
    $VMSize = <VM size> 
    ```

    >[!NOTE]
    > De `PrivateIP` parameter is optioneel. Gebruik deze parameter om een statisch IP-adres toe te wijzen, anders is de standaardwaarde een dynamisch IP-adres met DHCP.

    Hier is een voorbeeld van uitvoer. In dit voorbeeld wordt dezelfde resourcegroep opgegeven voor alle VM-resources, maar u kunt indien nodig afzonderlijke resourcegroepen voor de resources maken en opgeven.

    ```powershell
    PS C:\WINDOWS\system32> $NicRG = "myasevm1rg"
    PS C:\WINDOWS\system32> $NicName = "myasevmnic1"
    PS C:\WINDOWS\system32> $IPConfigName = "myaseipconfig1" 

    PS C:\WINDOWS\system32> $VMRG = "myasevm1rg"
    PS C:\WINDOWS\system32> $VMName = "myasetestvm1"
    PS C:\WINDOWS\system32> $VMSize = "Standard_D1_v2"   
    ```

1. Haal de gegevens van het virtuele netwerk op en maak een nieuwe netwerkinterface.

    In dit voorbeeld wordt ervan uit gegaan dat u één netwerkinterface maakt in het standaard virtuele netwerk `ASEVNET` dat is gekoppeld aan de standaardresourcegroep `ASERG` . Indien nodig kunt u een alternatief virtueel netwerk opgeven of meerdere netwerkinterfaces maken. Zie Een netwerkinterface toevoegen aan een VM via de Azure Portal [voor meer Azure Portal.](azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal.md)

    ```powershell
    $armVN = Get-AzureRMVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG
    $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName -SubnetId $armVN.Subnets[0].Id [-PrivateIpAddress $PrivateIP]
    $nic = New-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $NicRG -Location DBELocal -IpConfiguration $ipConfig
    ```

    Hier is een voorbeeld van uitvoer.

    ```powershell
    PS C:\WINDOWS\system32> $armVN = Get-AzureRMVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG
    PS C:\WINDOWS\system32> $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName -SubnetId $armVN.Subnets[0].Id
    PS C:\WINDOWS\system32> $nic = New-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $NicRG -Location DBELocal -IpConfiguration $ipConfig
    WARNING: The output object type of this cmdlet will be modified in a future release.
    PS C:\WINDOWS\system32>    
    ```
1. Maak een nieuw VM-configuratieobject.

    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
    ```

    
1. Voeg de netwerkinterface toe aan de VM.

    ```powershell
    $vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
    ```

1. Stel de eigenschappen van de besturingssysteemschijf in op de VM.

    ```powershell
    $vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $disk.Id -StorageAccountType StandardLRS -CreateOption Attach –[Windows/Linux]
    ```
    De laatste vlag in deze opdracht is of afhankelijk van het `-Windows` `-Linux` besturingssysteem dat u gebruikt voor uw VM.

1. Maak de VM.

    ```powershell
    New-AzureRmVM -ResourceGroupName $VMRG -Location DBELocal -VM $vm 
    ```

    Hier is een voorbeeld van uitvoer. 

    ```powershell
    PS C:\WINDOWS\system32> $vmConfig = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
    PS C:\WINDOWS\system32> $vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
    PS C:\WINDOWS\system32> $vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $disk.Id -StorageAccountType StandardLRS -CreateOption Attach -Windows
    PS C:\WINDOWS\system32> New-AzureRmVM -ResourceGroupName $VMRG -Location DBELocal -VM $vm
    WARNING: Since the VM is created using premium storage or managed disk, existing standard storage account, myasevmsa, is used for
    boot diagnostics.    
    RequestId IsSuccessStatusCode StatusCode ReasonPhrase
    --------- ------------------- ---------- ------------
                             True         OK OK        
    PS C:\WINDOWS\system32>
    ```

## <a name="delete-vm-and-resources"></a>VM en resources verwijderen

In dit artikel is slechts één resourcegroep gebruikt om alle VM-resources te maken. Als u die resourcegroep verwijdert, worden de VM en alle bijbehorende resources verwijderd. 

1. Bekijk eerst alle resources die zijn gemaakt onder de resourcegroep.

    ```powershell
    Get-AzureRmResource -ResourceGroupName <Resource group name>
    ```
    Hier is een voorbeeld van uitvoer.
    
    ```powershell
    PS C:\WINDOWS\system32> Get-AzureRmResource -ResourceGroupName myasevm1rg
    
    
    Name              : myasemd1
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Compute/disks
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Compute/disk
                        s/myasemd1
    
    Name              : myasetestvm1
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Compute/virtualMachines
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Compute/virt
                        ualMachines/myasetestvm1
    
    Name              : myasevmnic1
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Network/networkInterfaces
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Network/netw
                        orkInterfaces/myasevmnic1
    
    Name              : myasevmsa
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Storage/storageaccounts
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Storage/stor
                        ageaccounts/myasevmsa
    
    PS C:\WINDOWS\system32>
    ```

1. Verwijder de resourcegroep en alle bijbehorende resources.

    ```powershell
    Remove-AzureRmResourceGroup -ResourceGroupName <Resource group name>
    ```
    Hier is een voorbeeld van uitvoer.
    
    ```powershell
    PS C:\WINDOWS\system32> Remove-AzureRmResourceGroup -ResourceGroupName myasevm1rg
    
    Confirm
    Are you sure you want to remove resource group 'myasevm1rg'
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
    True
    PS C:\WINDOWS\system32>
    ```

1. Controleer of de resourcegroep is verwijderd. Haal alle resourcegroepen op die op het apparaat bestaan. 

    ```powershell
    Get-AzureRmResourceGroup
    ```
    Hier is een voorbeeld van uitvoer.

    ```powershell
    PS C:\WINDOWS\system32> Get-AzureRmResourceGroup

    ResourceGroupName : ase-image-resourcegroup
    Location          : dbelocal
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/ase-image-resourcegroup
    
    ResourceGroupName : ASERG
    Location          : dbelocal
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/ASERG
    
    ResourceGroupName : myaserg
    Location          : dbelocal
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg
        
    PS C:\WINDOWS\system32>
    ```

## <a name="next-steps"></a>Volgende stappen

- [Een ge generaliseerde afbeelding van een Windows-VHD voorbereiden om VM's te implementeren op Azure Stack Edge Pro GPU](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md)
- [Een ge generaliseerde afbeelding van een ISO voorbereiden om VM's te implementeren op Azure Stack Edge Pro GPU](azure-stack-edge-gpu-prepare-windows-generalized-image-iso.md) d