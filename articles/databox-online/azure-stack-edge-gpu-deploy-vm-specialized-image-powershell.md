---
title: VM-installatie kopieën maken van een gespecialiseerde afbeelding van Windows VHD voor uw Azure Stack Edge Pro GPU-apparaat
description: Hierin wordt beschreven hoe u VM-installatie kopieën maakt van gespecialiseerde installatie kopieën vanaf een Windows-VHD of een VHDX. Gebruik deze speciale afbeelding om VM-installatie kopieën te maken voor gebruik met Vm's die op uw Azure Stack Edge Pro GPU-apparaat zijn geïmplementeerd.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/30/2021
ms.author: alkohli
ms.openlocfilehash: d03aeb9759fb321b580fa65e06dc09ccde4a44a0
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556094"
---
# <a name="deploy-a-vm-from-a-specialized-image-on-your-azure-stack-edge-pro-device-via-azure-powershell"></a>Implementeer een virtuele machine op basis van een gespecialiseerde installatie kopie op uw Azure Stack Edge Pro-apparaat via Azure PowerShell 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In dit artikel worden de stappen beschreven die nodig zijn voor het implementeren van een virtuele machine (VM) op uw Azure Stack Edge Pro-apparaat vanuit een gespecialiseerde installatie kopie. 

## <a name="about-specialized-images"></a>Over gespecialiseerde installatie kopieën

Een VHD of VHDX van Windows kan worden gebruikt om een *gespecialiseerde* installatie kopie of een *gegeneraliseerde* installatie kopie te maken. De volgende tabel bevat een overzicht van de belangrijkste verschillen tussen de *gespecialiseerde* en *gegeneraliseerde* installatie kopieën.


|Type installatiekopie  |Gegeneraliseerd  |Gespecialiseerd  |
|---------|---------|---------|
|Doel     |Geïmplementeerd op elk systeem         | Gericht op een specifiek systeem        |
|Setup na opstarten     | Setup is vereist om de virtuele machine eerst op te starten.          | Setup is niet nodig. <br> Het platform schakelt de virtuele machine in.        |
|Configuratie     |Hostnaam, admin-gebruiker en andere VM-specifieke instellingen vereist.         |Vooraf geconfigureerd.         |
|Gebruikt om     |Maak meerdere nieuwe Vm's op basis van dezelfde installatie kopie.         |Migreer een specifieke machine of herstel een VM op basis van een eerdere back-up.         |


In dit artikel worden de stappen besproken die nodig zijn om te implementeren vanuit een gespecialiseerde installatie kopie. Zie voor het implementeren van een gegeneraliseerde installatie kopie [gegeneraliseerde Windows VHD gebruiken](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md) voor uw apparaat.


## <a name="vm-image-workflow"></a>Werkstroom voor VM-installatiekopie

De werk stroom op hoog niveau voor het implementeren van een virtuele machine uit een gespecialiseerde installatie kopie is:

1. Kopieer de VHD naar een lokaal opslag account op uw Azure Stack Edge Pro GPU-apparaat.
1. Een nieuwe beheerde schijf maken op basis van de VHD.
1. Maak een nieuwe virtuele machine op basis van de beheerde schijf en koppel de beheerde schijf.


## <a name="prerequisites"></a>Vereisten

Voordat u een virtuele machine op uw apparaat kunt implementeren via Power shell, moet u het volgende doen:

- U hebt toegang tot een client die u gebruikt om verbinding te maken met uw apparaat.
    - De client voert een [ondersteund besturings systeem](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)uit.
    - Uw client is geconfigureerd om verbinding te maken met de lokale Azure Resource Manager van uw apparaat volgens de instructies in [verbinding maken met Azure Resource Manager voor uw apparaat](azure-stack-edge-gpu-connect-resource-manager.md).

## <a name="verify-the-local-azure-resource-manager-connection"></a>De lokale Azure Resource Manager verbinding controleren

Controleer of de client verbinding kan maken met de lokale Azure Resource Manager. 

1. Api's van het lokale apparaat aanroepen om te verifiëren:

    ```powershell
    Login-AzureRMAccount -EnvironmentName <Environment Name>
    ```

2. Geef de gebruikers naam `EdgeArmUser` en het wacht woord op om verbinding te maken via Azure Resource Manager. Als u het wacht woord niet intrekt, [stelt u het wacht woord voor Azure Resource Manager opnieuw](azure-stack-edge-gpu-set-azure-resource-manager-password.md) in en gebruikt u dit wacht woord om u aan te melden.
 

## <a name="deploy-vm-from-specialized-image"></a>VM implementeren vanuit gespecialiseerde installatie kopie

De volgende secties bevatten stapsgewijze instructies voor het implementeren van een VM op basis van een gespecialiseerde installatie kopie.

## <a name="copy-vhd-to-local-storage-account-on-device"></a>VHD kopiëren naar een lokaal opslag account op het apparaat

Volg deze stappen om VHD naar een lokaal opslag account te kopiëren:

1. Kopieer de bron-VHD naar een lokaal Blob Storage-account op uw Azure Stack-rand. 

1. Noteer de resulterende URI. U gebruikt deze URI in een latere stap.
    
    Als u een lokaal opslag account wilt maken en openen, raadpleegt u de secties [een opslag account maken](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#create-a-storage-account) met [een VHD uploaden](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#upload-a-vhd) in het artikel: [vm's op uw Azure stack Edge-apparaat implementeren via Azure PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md). 

## <a name="create-a-managed-disk-from-vhd"></a>Een beheerde schijf maken op basis van VHD

Volg deze stappen om een beheerde schijf te maken op basis van een VHD die u eerder hebt geüpload naar het opslag account:

1. Stel een aantal para meters in.

    ```powershell
    $VhdURI = <URI of VHD in local storage account>
    $DiskRG = <managed disk resource group>
    $DiskName = <managed disk name>    
    ```
    Hier volgt een voor beeld van een uitvoer.

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

    Hier volgt een voor beeld van een uitvoer. De locatie hier wordt ingesteld op de locatie van het lokale opslag account en is altijd `DBELocal` voor alle lokale opslag accounts op uw Azure stack Edge Pro GPU-apparaat. 

    ```powershell
    PS C:\WINDOWS\system32> $DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri $VHDURI
    PS C:\WINDOWS\system32> $disk = New-AzureRMDisk -ResourceGroupName $DiskRG -DiskName $DiskName -Disk $DiskConfig
    PS C:\WINDOWS\system32>    
    ```
## <a name="create-a-vm-from-managed-disk"></a>Een virtuele machine maken op basis van een beheerde schijf

Volg deze stappen om een virtuele machine te maken op basis van een beheerde schijf:

1. Stel een aantal para meters in.

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
    > De `PrivateIP` para meter is optioneel. Gebruik deze para meter om een statisch IP-adres toe te wijzen. de standaard waarde is een dynamisch IP-adres met behulp van DHCP.

    Hier volgt een voor beeld van een uitvoer. In dit voor beeld is dezelfde resource groep opgegeven voor alle VM-resources, maar u kunt indien nodig afzonderlijke resource groepen maken en opgeven voor de resources.

    ```powershell
    PS C:\WINDOWS\system32> $NicRG = "myasevm1rg"
    PS C:\WINDOWS\system32> $NicName = "myasevmnic1"
    PS C:\WINDOWS\system32> $IPConfigName = "myaseipconfig1" 

    PS C:\WINDOWS\system32> $VMRG = "myasevm1rg"
    PS C:\WINDOWS\system32> $VMName = "myasetestvm1"
    PS C:\WINDOWS\system32> $VMSize = "Standard_D1_v2"   
    ```

1. De gegevens van het virtuele netwerk ophalen en een nieuwe netwerk interface maken.

    In dit voor beeld wordt ervan uitgegaan dat u één netwerk interface maakt op het virtuele standaard netwerk `ASEVNET` dat is gekoppeld aan de standaard resource groep `ASERG` . Indien nodig kunt u een alternatief virtueel netwerk opgeven of meerdere netwerk interfaces maken. Zie [een netwerk interface toevoegen aan een virtuele machine via de Azure Portal](azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal.md)voor meer informatie.

    ```powershell
    $armVN = Get-AzureRMVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG
    $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName -SubnetId $armVN.Subnets[0].Id [-PrivateIpAddress $PrivateIP]
    $nic = New-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $NicRG -Location DBELocal -IpConfiguration $ipConfig
    ```

    Hier volgt een voor beeld van een uitvoer.

    ```powershell
    PS C:\WINDOWS\system32> $armVN = Get-AzureRMVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG
    PS C:\WINDOWS\system32> $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName -SubnetId $armVN.Subnets[0].Id
    PS C:\WINDOWS\system32> $nic = New-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $NicRG -Location DBELocal -IpConfiguration $ipConfig
    WARNING: The output object type of this cmdlet will be modified in a future release.
    PS C:\WINDOWS\system32>    
    ```
1. Maak een nieuw VM-configuratie object.

    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
    ```

    
1. Voeg de netwerk interface toe aan de virtuele machine.

    ```powershell
    $vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
    ```

1. Stel de eigenschappen van de besturingssysteem schijf in op de VM.

    ```powershell
    $vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $disk.Id -StorageAccountType StandardLRS -CreateOption Attach –[Windows/Linux]
    ```
    De laatste vlag in deze opdracht is ofwel `-Windows` of `-Linux` afhankelijk van het besturings systeem dat u voor uw virtuele machine gebruikt.

1. Maak de VM.

    ```powershell
    New-AzureRmVM -ResourceGroupName $VMRG -Location DBELocal -VM $vm 
    ```

    Hier volgt een voor beeld van een uitvoer. 

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

In dit artikel wordt slechts één resource groep gebruikt voor het maken van de VM-resource. Als u deze resource groep verwijdert, worden de virtuele machine en alle bijbehorende resources verwijderd. 

1. Bekijk eerst alle resources die zijn gemaakt in de resource groep.

    ```powershell
    Get-AzureRmResource -ResourceGroupName <Resource group name>
    ```
    Hier volgt een voor beeld van een uitvoer.
    
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

1. Verwijder de resource groep en alle bijbehorende resources.

    ```powershell
    Remove-AzureRmResourceGroup -ResourceGroupName <Resource group name>
    ```
    Hier volgt een voor beeld van een uitvoer.
    
    ```powershell
    PS C:\WINDOWS\system32> Remove-AzureRmResourceGroup -ResourceGroupName myasevm1rg
    
    Confirm
    Are you sure you want to remove resource group 'myasevm1rg'
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
    True
    PS C:\WINDOWS\system32>
    ```

1. Controleer of de resource groep is verwijderd. Alle resource groepen ophalen die op het apparaat bestaan. 

    ```powershell
    Get-AzureRmResourceGroup
    ```
    Hier volgt een voor beeld van een uitvoer.

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

Afhankelijk van de aard van de implementatie, kunt u een van de volgende procedures kiezen.

- [Een virtuele machine implementeren vanuit een gegeneraliseerde installatie kopie via Azure PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)  
- [Een virtuele machine implementeren via Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
