---
title: Overzicht en implementatie van GPU-Vm's op uw Azure Stack Edge Pro-apparaat
description: Hierin wordt beschreven hoe u virtuele machines (Vm's) voor GPU maakt en beheert op een Azure Stack Edge Pro-apparaat met behulp van sjablonen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: ff805b758dce05a66764ab1ff08e53378c946362
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102438179"
---
# <a name="gpu-vms-for-your-azure-stack-edge-pro-device"></a>GPU-Vm's voor uw Azure Stack Edge Pro-apparaat

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Dit artikel bevat een overzicht van GPU virtual machines (Vm's) op uw Azure Stack Edge Pro-apparaat. In dit artikel wordt beschreven hoe u een GPU-VM maakt en vervolgens de extensie GPU-stuur programma installeert om de juiste NVIDIA-Stuur Programma's te installeren. Gebruik de Azure Resource Manager sjablonen om de GPU-VM te maken en de uitbrei ding van het GPU-stuur programma te installeren. 

Dit artikel is van toepassing op Azure Stack Edge Pro GPU-en Azure Stack Edge Pro R-apparaten.

## <a name="about-gpu-vms"></a>Over GPU Vm's

Uw Azure Stack Edge Pro-apparaten zijn uitgerust met 1 of 2 Nvidia Tesla T4 GPU. Als u GPU-versnelde VM-workloads op deze apparaten wilt implementeren, gebruikt u GPU geoptimaliseerde VM-grootten. Bijvoorbeeld, de NC T4 v3-serie moet worden gebruikt voor het implementeren van in-werk belastingen met T4-Gpu's. 

Zie voor meer informatie [virtuele machines in NC T4 v3-serie](../virtual-machines/nct4-v3-series.md).

## <a name="supported-os-and-gpu-drivers"></a>Ondersteunde Stuur Programma's voor besturings systemen en GPU 

Om te profiteren van de GPU-mogelijkheden van Vm's uit de Azure N-serie, moeten de NVIDIA GPU-Stuur Programma's zijn geïnstalleerd. 

Met de uitbrei ding NVIDIA GPU-stuur programma worden de juiste NVIDIA CUDA-of GRID-Stuur Programma's geïnstalleerd. U kunt de extensie installeren of beheren met behulp van de Azure Resource Manager sjablonen.

### <a name="supported-os-for-gpu-extension-for-windows"></a>Ondersteund besturings systeem voor GPU-extensie voor Windows

Deze extensie ondersteunt de volgende besturings systemen (OSs). Andere versies werken mogelijk wel, maar zijn niet intern getest op GPU-Vm's die worden uitgevoerd op Azure Stack Edge Pro-apparaten.

| Distributie | Versie |
|---|---|
| Windows Server 2019 | Kern |
| Windows Server 2016 | Kern |

### <a name="supported-os-for-gpu-extension-for-linux"></a>Ondersteund besturings systeem voor GPU-extensie voor Linux

Deze extensie ondersteunt de volgende OS distributies, afhankelijk van de ondersteuning van het stuur programma voor specifieke versie van het besturings systeem. Andere versies werken mogelijk wel, maar zijn niet intern getest op GPU-Vm's die worden uitgevoerd op Azure Stack Edge Pro-apparaten.


| Distributie | Versie |
|---|---|
| Ubuntu | 18,04 LTS |
| Red Hat Enterprise Linux | 7.4 |


## <a name="gpu-vms-and-kubernetes"></a>GPU-Vm's en Kubernetes

Voordat u GPU-Vm's op uw apparaat implementeert, moet u de volgende overwegingen nalezen als Kubernetes op het apparaat is geconfigureerd.

#### <a name="for-1-gpu-device"></a>Voor één GPU-apparaat: 

- **Een GPU-VM maken, gevolgd door de Kubernetes-configuratie op uw apparaat**: in dit scenario zijn de configuratie van de GPU VM maken en Kubernetes geslaagd. Kubernetes heeft in dit geval geen toegang tot de GPU.

- **Kubernetes op uw apparaat configureren, gevolgd door het maken van een GPU-VM**: in dit scenario zal de KUBERNETES de GPU op uw apparaat claimen en mislukt het maken van de virtuele machine omdat er geen GPU-bronnen beschikbaar zijn.

#### <a name="for-2-gpu-device"></a>Voor 2-GPU-apparaat

- **Een GPU-VM maken, gevolgd door de Kubernetes-configuratie op uw apparaat**: in dit scenario zal de GPU-VM die u maakt, een GPU op uw apparaat claimen en wordt de Kubernetes-configuratie ook wel geslaagd en de resterende één GPU claimen. 

- **Maak twee GPU-vm's, gevolgd door Kubernetes-configuratie op uw apparaat**: in dit scenario claimen de twee GPU-vm's de twee gpu's op het apparaat en de Kubernetes is geconfigureerd zonder gpu's. 

- **Kubernetes op uw apparaat configureren, gevolgd door het maken van een GPU-VM**: in dit scenario zal de Kubernetes de gpu's op uw apparaat claimen en mislukt het maken van de virtuele machine omdat er geen GPU-bronnen beschikbaar zijn.

Als er GPU-Vm's worden uitgevoerd op uw apparaat en Kubernetes ook is geconfigureerd, wordt de toewijzing van de virtuele machine ongedaan gemaakt (wanneer u een virtuele machine stopt of verwijdert met Stop-AzureRmVM of Remove-AzureRmVM), is er een risico dat het Kubernetes-cluster alle Gpu's op het apparaat aanbiedt. In een dergelijk geval kunt u de GPU-Vm's die op uw apparaat zijn geïmplementeerd, niet opnieuw starten of GPU-Vm's maken.


## <a name="create-gpu-vms"></a>GPU Vm's maken

Volg deze stappen wanneer u GPU-Vm's implementeert op uw apparaat:

1. Bepaal of op uw apparaat ook Kubernetes wordt uitgevoerd. Als Kubernetes wordt uitgevoerd op het apparaat, moet u eerst de GPU-VM maken en vervolgens Kubernetes configureren. Als Kubernetes eerst is geconfigureerd, worden alle beschik bare GPU-resources door het systeem claimt en mislukt het maken van de GPU-VM.

1. [Down load de VM-sjablonen en parameter bestanden](https://aka.ms/ase-vm-templates) naar uw client computer. Pak het bestand uit in een map die u wilt gebruiken als werkmap.

1. Als u GPU-Vm's wilt maken, volgt u alle stappen in de [implementatie-VM op uw Azure stack Edge Pro gebruiken met behulp van sjablonen](azure-stack-edge-gpu-deploy-virtual-machine-templates.md) , met uitzonde ring van de volgende verschillen: 

    1. Schakel tijdens het configureren van het Compute-netwerk de poort in die is verbonden met internet, voor reken processen. Zo kunt u de GPU-Stuur Programma's downloaden die vereist zijn voor GPU-uitbrei dingen voor uw GPU-Vm's.

        Hier volgt een voor beeld waarin poort 2 is verbonden met internet en dat is gebruikt om het berekenings netwerk in te scha kelen. Als u hebt vastgesteld dat Kubernetes in de vorige stap niet nodig is, kunt u de IP-toewijzing van het Kubernetes-knoop punt en de externe service overs Laan.

        ![Reken instellingen inschakelen op poort die is verbonden met Internet](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/enable-compute-network-1.png)

            
    1. Maak een virtuele machine met behulp van de sjablonen. Wanneer u de grootte van GPU-VM'S opgeeft, moet u ervoor zorgen dat u de NCasT4-v3-serie in de gebruikt, `CreateVM.parameters.json` zoals deze worden ondersteund voor GPU-vm's. Zie [ondersteunde VM-grootten voor GPU-vm's](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview)voor meer informatie.

        ```json
            "vmSize": {
          "value": "Standard_NC4as_T4_v3"
        },
        ```

    1. Zodra de GPU-VM is gemaakt, kunt u deze virtuele machine weer geven in de lijst met virtuele machines in uw Azure Stack Edge-resource in de Azure Portal.

        ![GPU-VM in de lijst met virtuele machines in Azure Portal](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/list-virtual-machine-1.png)

1. Selecteer de virtuele machine en zoom in op de details. Kopieer het IP-adres dat is toegewezen aan de virtuele machine.

    ![IP-adres toegewezen aan GPU-VM in Azure Portal](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/get-ip-gpu-virtual-machine-1.png)

1. Nadat de VM is gemaakt, implementeert u de GPU-extensie met behulp van de extensie sjabloon. Zie GPU-extensie voor [Linux](#gpu-extension-for-linux) en voor virtuele Windows-machines installeren voor virtuele Linux-machines. Zie [GPU-extensie voor Windows installeren](#gpu-extension-for-windows).

1. Als u wilt controleren of de GPU-extensie is geïnstalleerd, maakt u verbinding met de GPU-VM:
    1. Als u een virtuele Windows-machine gebruikt, volgt u de stappen in [verbinding maken met een virtuele Windows-machine](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-a-windows-vm). [Controleer de installatie](#verify-windows-driver-installation).
    1. Als u een virtuele Linux-machine gebruikt, volgt u de stappen in [verbinding maken met een virtuele Linux-machine](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-a-linux-vm). [Controleer de installatie](#verify-linux-driver-installation).

1. Als dat nodig is, kunt u het berekenings netwerk weer op de gewenste manier overschakelen. 


> [!NOTE]
> Wanneer u de software versie van uw apparaat bijwerkt van 2012 naar later, moet u de GPU-Vm's hand matig stoppen.


## <a name="install-gpu-extension"></a>GPU-extensie installeren

Afhankelijk van het besturings systeem voor uw virtuele machine, kunt u de GPU-extensie voor Windows of voor Linux installeren.

> [!NOTE]
> Voordat u de GPU-extensie installeert, moet u ervoor zorgen dat de poort ingeschakeld voor het berekenings netwerk op het apparaat is verbonden met internet en toegang heeft. De GPU-Stuur Programma's worden gedownload via internet toegang.

### <a name="gpu-extension-for-windows"></a>GPU-extensie voor Windows

Als u NVIDIA GPU-Stuur Programma's wilt implementeren voor een bestaande virtuele machine, bewerkt u het `addGPUExtWindowsVM.parameters.json` parameter bestand en implementeert u vervolgens de sjabloon `addGPUextensiontoVM.json` .

#### <a name="edit-parameters-file"></a>Parameter bestand bewerken

Het bestand `addGPUExtWindowsVM.parameters.json` heeft de volgende para meters:

```json
"parameters": { 
    "vmName": {
    "value": "<name of the VM>" 
    },
    "extensionName": {
    "value": "<name for the extension. Example: windowsGpu>" 
    },
    "publisher": {
    "value": "Microsoft.HpcCompute" 
    },
    "type": {
    "value": "NvidiaGpuDriverWindows" 
    },
    "typeHandlerVersion": {
    "value": "1.3" 
    },
    "settings": {
    "value": {
    "DriverURL" : "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
    "DriverCertificateUrl" : "https://go.microsoft.com/fwlink/?linkid=871664",
    "DriverType":"CUDA"
    }
    }
    }
```

Hier volgt een voor beeld van een parameter bestand dat in dit artikel is gebruikt:

```powershell
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\CreateVM\CreateVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\CreateVM\CreateVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment2"

DeploymentName          : deployment2
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/16/2020 12:02:56 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM2
                          adminUsername    String                     Administrator
                          password         String                     Password1
                          imageName        String                     myasewindowsimg
                          vmSize           String                     Standard_NC4as_T4_v3
                          vnetName         String                     ASEVNET
                          vnetRG           String                     aserg
                          subnetName       String                     ASEVNETsubNet
                          nicName          String                     nic6
                          ipConfigName     String                     ipconfig6
                          privateIPAddress  String

Outputs                 :
DeploymentDebugLogLevel :
PS C:\WINDOWS\system32>
```
#### <a name="deploy-template"></a>Sjabloon implementeren

Implementeer de sjabloon `addGPUextensiontoVM.json` . Met deze sjabloon implementeert u een extensie voor een bestaande virtuele machine. Voer de volgende opdracht uit:

```powershell
$templateFile = "<Path to addGPUextensiontoVM.json>" 
$templateParameterFile = "<Path to addGPUExtWindowsVM.parameters.json>" 
$RGName = "<Name of your resource group>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Name for your deployment>"
```
> [!NOTE]
> De implementatie van de extensie is een langlopende taak en duurt ongeveer 10 minuten om te volt ooien.

Hier volgt een voorbeeld van uitvoer:

```powershell
PS C:\WINDOWS\system32> "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addGPUExtWindowsVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment3"

DeploymentName          : deployment3
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/16/2020 12:18:50 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM2
                          extensionName    String                     windowsgpuext
                          publisher        String                     Microsoft.HpcCompute
                          type             String                     NvidiaGpuDriverWindows
                          typeHandlerVersion  String                     1.3
                          settings         Object                     {
                            "DriverURL": "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
                            "DriverCertificateUrl": "https://go.microsoft.com/fwlink/?linkid=871664",
                            "DriverType": "CUDA"
                          }

Outputs                 :
DeploymentDebugLogLevel :
PS C:\WINDOWS\system32>
```
#### <a name="track-deployment"></a>Implementatie bijhouden

Voer de volgende opdracht uit om de implementatie status van extensies voor een bepaalde virtuele machine te controleren: 

```powershell
Get-AzureRmVMExtension -ResourceGroupName <Name of resource group> -VMName <Name of VM> -Name <Name of the extension>
```
Hier volgt een voorbeeld van uitvoer:

```powershell
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM2 -Name windowsgpuext

ResourceGroupName       : myasegpuvm1
VMName                  : VM2
Name                    : windowsgpuext
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.HpcCompute
ExtensionType           : NvidiaGpuDriverWindows
TypeHandlerVersion      : 1.3
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM2/extensions/windowsgpuext
PublicSettings          : {
                            "DriverURL": "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
                            "DriverCertificateUrl": "https://go.microsoft.com/fwlink/?linkid=871664",
                            "DriverType": "CUDA"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

Uitvoer voor uitvoering van extensie wordt vastgelegd in het volgende bestand. Raadpleeg dit bestand `C:\Packages\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverWindows\1.3.0.0\Status` om de status van de installatie bij te houden. 


Een geslaagde installatie wordt aangeduid met een `message` as `Enable Extension` en `status` als `success` .

```powershell
"status":  {
                       "formattedMessage":  {
                                                "message":  "Enable Extension",
                                                "lang":  "en"
                                            },
                       "name":  "NvidiaGpuDriverWindows",
                       "status":  "success",
```

#### <a name="verify-windows-driver-installation"></a>Installatie van Windows-stuur programma controleren

Meld u aan bij de virtuele machine en voer het NVIDIA-SMI-opdracht regel programma uit dat met het stuur programma is geïnstalleerd. De `nvidia-smi.exe` bevindt zich op  `C:\Program Files\NVIDIA Corporation\NVSMI\nvidia-smi.exe` . Als u het bestand niet ziet, is het mogelijk dat de installatie van het stuur programma nog steeds op de achtergrond wordt uitgevoerd. Wacht tien minuten en probeer het opnieuw.

Als het stuur programma is geïnstalleerd, ziet u een uitvoer die lijkt op het volgende voor beeld: 

```powershell
PS C:\Users\Administrator> cd "C:\Program Files\NVIDIA Corporation\NVSMI"
PS C:\Program Files\NVIDIA Corporation\NVSMI> ls

    Directory: C:\Program Files\NVIDIA Corporation\NVSMI

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        2/26/2020  12:00 PM         849640 MCU.exe
-a----        2/26/2020  12:00 PM         443104 nvdebugdump.exe
-a----        2/25/2020   2:06 AM          81823 nvidia-smi.1.pdf
-a----        2/26/2020  12:01 PM         566880 nvidia-smi.exe
-a----        2/26/2020  12:01 PM         991344 nvml.dll

PS C:\Program Files\NVIDIA Corporation\NVSMI> .\nvidia-smi.exe
Wed Dec 16 00:35:51 2020
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 442.50       Driver Version: 442.50       CUDA Version: 10.2     |
|-------------------------------+----------------------+----------------------+
| GPU  Name            TCC/WDDM | Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  Tesla T4            TCC  | 0000503C:00:00.0 Off |                    0 |
| N/A   35C    P8    11W /  70W |      8MiB / 15205MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID   Type   Process name                             Usage      |
|=============================================================================|
|  No running processes found                                                 |
+-----------------------------------------------------------------------------+
PS C:\Program Files\NVIDIA Corporation\NVSMI>
```

Zie [NVIDIA GPU-stuur programma-extensie voor Windows](../virtual-machines/extensions/hpccompute-gpu-windows.md)voor meer informatie.

### <a name="gpu-extension-for-linux"></a>GPU-extensie voor Linux

Als u NVIDIA GPU-Stuur Programma's wilt implementeren voor een bestaande virtuele machine, bewerkt u het parameter bestand en implementeert u vervolgens de sjabloon `addGPUextensiontoVM.json` . Er zijn specifieke parameter bestanden voor Ubuntu en Red Hat Enterprise Linux (RHEL) zoals beschreven in de volgende secties.

#### <a name="edit-parameters-file"></a>Parameter bestand bewerken

Als Ubuntu wordt gebruikt, `addGPUExtLinuxVM.parameters.json` neemt het bestand de volgende para meters:

```powershell
"parameters": { 
    "vmName": {
    "value": "<name of the VM>" 
    },
    "extensionName": {
    "value": "<name for the extension. Example: linuxGpu>" 
    },
    "publisher": {
    "value": "Microsoft.HpcCompute" 
    },
    "type": {
    "value": "NvidiaGpuDriverLinux" 
    },
    "typeHandlerVersion": {
    "value": "1.3" 
    },
    "settings": {
    "value": {
    "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
    "PUBKEY_URL": "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
    "CUDA_ver": "10.0.130",
    "InstallCUDA": "true"
    }
    }
    }
```
Als Red Hat Enterprise Linux (RHEL) gebruikt, `addGPUExtensionRHELVM.parameters.json` heeft het bestand de volgende para meters:

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<name of the VM>" 
        },
        "extensionName": {
            "value": "<name for the extension. Example: linuxGpu>" 
        },
        "publisher": {
            "value": "Microsoft.HpcCompute" 
        },
        "type": {
            "value": "NvidiaGpuDriverLinux" 
        },
        "typeHandlerVersion": {
            "value": "1.3" 
        },
        "settings": {
            "value": {
                    "isCustomInstall":true,
                    "DRIVER_URL":"https://go.microsoft.com/fwlink/?linkid=874273",
                    "CUDA_ver":"10.0.130",
                    "PUBKEY_URL":"http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
                    "DKMS_URL":"https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm",
                    "LIS_URL":"https://aka.ms/lis",
                    "LIS_RHEL_ver":"3.10.0-1062.9.1.el7"
            }
        }
    }
}
```


Hier volgt een voor beeld van een Ubuntu-parameter bestand dat in dit artikel is gebruikt:

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "VM1" 
        },
        "extensionName": {
            "value": "gpuLinux" 
        },
        "publisher": {
            "value": "Microsoft.HpcCompute" 
        },
        "type": {
            "value": "NvidiaGpuDriverLinux" 
        },
        "typeHandlerVersion": {
            "value": "1.3" 
        },
        "settings": {
            "value": {
            "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
            "PUBKEY_URL": "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
            "CUDA_ver": "10.0.130",
            "InstallCUDA": "true"
            }
        }
    }
}
```


#### <a name="deploy-template"></a>Sjabloon implementeren

Implementeer de sjabloon `addGPUextensiontoVM.json` . Met deze sjabloon implementeert u een extensie voor een bestaande virtuele machine. Voer de volgende opdracht uit:

```powershell
$templateFile = "Path to addGPUextensiontoVM.json" 
$templateParameterFile = "Path to addGPUExtLinuxVM.parameters.json" 
$RGName = "<Name of your resource group>" 
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Name for your deployment>"
``` 

> [!NOTE]
> De implementatie van de extensie is een langlopende taak en duurt ongeveer 10 minuten om te volt ooien.

Hier volgt een voorbeeld van uitvoer:

```powershell
Copyright (C) Microsoft Corporation. All rights reserved.
Try the new cross-platform PowerShell https://aka.ms/pscore6

PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addGPUExtLinuxVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "rg2"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "delpoyment7"

DeploymentName          : delpoyment7
ResourceGroupName       : rg2
ProvisioningState       : Succeeded
Timestamp               : 12/10/2020 10:43:23 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM1
                          extensionName    String                     gpuLinux
                          publisher        String                     Microsoft.HpcCompute
                          type             String                     NvidiaGpuDriverLinux
                          typeHandlerVersion  String                     1.3
                          settings         Object                     {
                            "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
                            "PUBKEY_URL":
                          "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
                            "CUDA_ver": "10.0.130",
                            "InstallCUDA": "true"
                          }

Outputs                 :
DeploymentDebugLogLevel :
PS C:\WINDOWS\system32>
```

#### <a name="track-deployment-status"></a>Implementatie status bijhouden    
    
Sjabloonimlementatie is een langlopende taak. Als u de implementatie status van extensies voor een bepaalde virtuele machine wilt controleren, opent u een nieuwe Power shell-sessie (als administrator uitvoeren). Voer de volgende opdracht uit: 

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName <VM Name> -Name <Extension Name>
```
Hier volgt een voorbeeld van uitvoer: 

```powershell
Copyright (C) Microsoft Corporation. All rights reserved.
Try the new cross-platform PowerShell https://aka.ms/pscore6

PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName rg2 -VMName VM1 -Name gpulinux

ResourceGroupName       : rg2
VMName                  : VM1
Name                    : gpuLinux
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.HpcCompute
ExtensionType           : NvidiaGpuDriverLinux
TypeHandlerVersion      : 1.3
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/rg2/providers/Microsoft.Compute/virtualMachines/VM1/extensions/gpuLinux
PublicSettings          : {
                            "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
                            "PUBKEY_URL": "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
                            "CUDA_ver": "10.0.130",
                            "InstallCUDA": "true"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

> [!NOTE]
> Wanneer de implementatie is voltooid, `ProvisioningState` verandert deze in `Succeeded` .

De uitvoer van de uitbrei ding voor uitvoering wordt geregistreerd in het volgende bestand: `/var/log/azure/nvidia-vmext-status` .

#### <a name="verify-linux-driver-installation"></a>Installatie van Linux-stuur programma controleren

Voer de volgende stappen uit om de installatie van het stuur programma te controleren:

1. Verbinding maken met de GPU-VM. Volg de instructies in [verbinding maken met een virtuele Linux-machine](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-a-linux-vm). 

    Hier volgt een voorbeeld van uitvoer:

    ```powershell
    PS C:\WINDOWS\system32> ssh -l Administrator 10.57.50.60
    Administrator@10.57.50.60's password:
    Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 5.0.0-1031-azure x86_64)
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage
      System information as of Thu Dec 10 22:57:01 UTC 2020
    
      System load:  0.0                Processes:           133
      Usage of /:   24.8% of 28.90GB   Users logged in:     0
      Memory usage: 2%                 IP address for eth0: 10.57.50.60
      Swap usage:   0%
    
    249 packages can be updated.
    140 updates are security updates.
    
    Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 5.0.0-1031-azure x86_64)    
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage    
      System information as of Thu Dec 10 22:57:01 UTC 2020    
      System load:  0.0                Processes:           133
      Usage of /:   24.8% of 28.90GB   Users logged in:     0
      Memory usage: 2%                 IP address for eth0: 10.57.50.60
      Swap usage:   0%
        
    249 packages can be updated.
    140 updates are security updates.
    
    New release '20.04.1 LTS' available.
    Run 'do-release-upgrade' to upgrade to it.
        
    *** System restart required ***
    Last login: Thu Dec 10 21:49:29 2020 from 10.90.24.23
    To run a command as administrator (user "root"), use "sudo <command>".
    See "man sudo_root" for details.
    
    Administrator@VM1:~$
    ```
2. Voer het NVIDIA-SMI-opdracht regel programma uit dat met het stuur programma is geïnstalleerd. Als het stuur programma is geïnstalleerd, kunt u het hulp programma uitvoeren en de volgende uitvoer bekijken:

    ```powershell
    Administrator@VM1:~$ nvidia-smi
    Thu Dec 10 22:58:46 2020
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 455.45.01    Driver Version: 455.45.01    CUDA Version: 11.1     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            Off  | 0000941F:00:00.0 Off |                    0 |
    | N/A   48C    P0    27W /  70W |      0MiB / 15109MiB |      5%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    Administrator@VM1:~$
    ```

Zie [NVIDIA GPU-stuur programma-extensie voor Linux](../virtual-machines/extensions/hpccompute-gpu-linux.md)voor meer informatie.

## <a name="remove-gpu-extension"></a>GPU-extensie verwijderen

Als u de GPU-extensie wilt verwijderen, gebruikt u de volgende opdracht:

`Remove-AzureRmVMExtension -ResourceGroupName <Resource group name> -VMName <VM name> -Name <Extension name>`

Hier volgt een voorbeeld van uitvoer:

```powershell
PS C:\azure-stack-edge-deploy-vms> Remove-AzureRmVMExtension -ResourceGroupName rgl -VMName WindowsVM -Name windowsgpuext
Virtual machine extension removal operation
This cmdlet will remove the specified virtual machine extension. Do you want to continue? [Y] Yes [N] No [S] Suspend [?] Help (default is "Y"): y
Requestld IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------    
          True                OK         OK
```




## <a name="next-steps"></a>Volgende stappen

[Azure Resource Manager-cmdlets](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)