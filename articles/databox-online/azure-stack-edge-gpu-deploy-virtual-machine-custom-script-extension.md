---
title: Gebruik van aangepaste script extensies voor Vm's op uw Azure Stack Edge Pro-apparaat
description: Hierin wordt beschreven hoe u aangepaste script extensies installeert op virtuele machines (Vm's) die worden uitgevoerd op een Azure Stack Edge Pro-apparaat met behulp van sjablonen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/05/2021
ms.author: alkohli
ms.openlocfilehash: d601c6191da9d555e54c1d58c122420510d288fc
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97955549"
---
# <a name="deploy-custom-script-extension-on-vms-running-on-your-azure-stack-edge-pro-device"></a>Aangepaste script extensie implementeren op Vm's die worden uitgevoerd op uw Azure Stack Edge Pro-apparaat

De aangepaste script extensie downloadt en voert scripts of opdrachten uit op virtuele machines die worden uitgevoerd op uw Azure Stack Edge Pro-apparaten. In dit artikel wordt beschreven hoe u de aangepaste script extensie installeert en uitvoert met behulp van een Azure Resource Manager sjabloon. 

Dit artikel is van toepassing op Azure Stack Edge Pro GPU, Azure Stack Edge Pro R en Azure Stack Edge mini-R-apparaten.

## <a name="about-custom-script-extension"></a>Aangepaste script extensie

De aangepaste script extensie is handig voor configuratie na de implementatie, software-installatie of een andere configuratie/beheer taak. U kunt scripts downloaden van Azure Storage of een andere toegankelijke Internet locatie, of u kunt scripts of opdrachten bieden voor de runtime van de uitbrei ding.

De aangepaste script extensie kan worden geïntegreerd met Azure Resource Manager sjablonen. U kunt dit ook uitvoeren met behulp van Azure CLI, Power shell of de Azure Virtual Machines REST API.

## <a name="os-for-custom-script-extension"></a>Besturings systeem voor aangepaste script extensie

#### <a name="supported-os-for-custom-script-extension-on-windows"></a>Ondersteund besturings systeem voor aangepaste script extensie in Windows

De aangepaste script extensie voor Windows wordt uitgevoerd op de volgende OSs. Andere versies werken mogelijk wel, maar zijn niet intern getest op Vm's die worden uitgevoerd op Azure Stack Edge Pro-apparaten.

| Distributie | Versie |
|---|---|
| Windows Server 2019 | Kern |
| Windows Server 2016 | Kern |

#### <a name="supported-os-for-custom-script-extension-on-linux"></a>Ondersteund besturings systeem voor aangepaste script extensie in Linux

De aangepaste script extensie voor Linux wordt uitgevoerd op de volgende OSs. Andere versies werken mogelijk wel, maar zijn niet intern getest op Vm's die worden uitgevoerd op Azure Stack Edge Pro-apparaten.

| Distributie | Versie |
|---|---|
| Linux: Ubuntu | 18,04 LTS |
| Linux: Red Hat Enterprise Linux | 7,4, 7,5, 7,7 |

<!--### Script location

Instead of the scripts, in this article, we pass a command to execute via the Custom Script Extension. 

### Internet Connectivity

To download a script externally such as from GitHub or Azure Storage, make sure that the port on which you enable compute network, is connected to the internet. 

If your script is on a local server, then you may still need additional firewall and Network Security Group ports need to be opened.

> [!NOTE]
> Before you install the Custom Script extension, make sure that the port enabled for compute network on your device is connected to Internet and has access. -->

## <a name="prerequisites"></a>Vereisten

1. [Down load de VM-sjablonen en parameter bestanden](https://aka.ms/ase-vm-templates) naar uw client computer. Pak het bestand uit in een map die u wilt gebruiken als werkmap.

1. U moet een virtuele machine maken en implementeren op het apparaat. Als u Vm's wilt maken, volgt u alle stappen in de [virtuele machine implementeren op uw Azure stack Edge Pro met behulp van sjablonen](azure-stack-edge-gpu-deploy-virtual-machine-templates.md).

    Als u een script extern moet downloaden, zoals van GitHub of Azure Storage, terwijl u het Compute-netwerk configureert, schakelt u de poort die is verbonden met internet in voor reken kracht. Hiermee kunt u het script downloaden.

    Hier volgt een voor beeld waarin poort 2 is verbonden met internet en dat is gebruikt om het berekenings netwerk in te scha kelen. Als u hebt vastgesteld dat Kubernetes in de vorige stap niet nodig is, kunt u de IP-toewijzing van het Kubernetes-knoop punt en de externe service overs Laan.    

    ![Reken instellingen inschakelen op poort die is verbonden met Internet](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/enable-compute-network-1.png)

## <a name="install-custom-script-extension"></a>Aangepaste script extensie installeren

Afhankelijk van het besturings systeem voor uw virtuele machine, kunt u een aangepaste script extensie voor Windows of voor Linux installeren.
 

### <a name="custom-script-extension-for-windows"></a>Aangepaste scriptextensie voor Windows

Als u een aangepaste script extensie voor Windows wilt implementeren voor een VM die wordt uitgevoerd op uw apparaat, bewerkt u het `addCSExtWindowsVM.parameters.json` parameter bestand en implementeert u vervolgens de sjabloon `addCSextensiontoVM.json` .

#### <a name="edit-parameters-file"></a>Parameter bestand bewerken

Het bestand `addCSExtWindowsVM.parameters.json` heeft de volgende para meters:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<Name of VM>" 
        },
        "extensionName": {
            "value": "<Name of extension>" 
        },
        "publisher": {
            "value": "Microsoft.Compute" 
        },
        "type": {
            "value": "CustomScriptExtension" 
        },
        "typeHandlerVersion": {
            "value": "1.10" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "<Command to execute>"
            }
        }
    }
}
```
Geef de naam van de virtuele machine, de naam van de uitbrei ding en de opdracht die u wilt uitvoeren.

Hier volgt een voor beeld van een parameter bestand dat in dit artikel is gebruikt. 

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "VM5" 
        },
        "extensionName": {
            "value": "CustomScriptExtension" 
        },
        "publisher": {
            "value": "Microsoft.Compute" 
        },
        "type": {
            "value": "CustomScriptExtension" 
        },
        "typeHandlerVersion": {
            "value": "1.10" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "md C:\\Users\\Public\\Documents\\test"
            }
        }
    }
}
```
#### <a name="deploy-template"></a>Sjabloon implementeren

Implementeer de sjabloon `addCSextensiontoVM.json` . Met deze sjabloon implementeert u een extensie voor een bestaande virtuele machine. Voer de volgende opdracht uit:

```powershell
$templateFile = "<Path to addCSExtensiontoVM.json file>"
$templateParameterFile = "<Path to addCSExtWindowsVM.parameters.json file>"
$RGName = "<Resource group name>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Deployment name>"
```
> [!NOTE]
> De implementatie van de extensie is een langlopende taak en duurt ongeveer 10 minuten om te volt ooien.

Hier volgt een voorbeeld van uitvoer:

```powershell
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addCSExtensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addCSExtWindowsVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment7"

DeploymentName          : deployment7
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/17/2020 10:07:44 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM5
                          extensionName    String                     CustomScriptExtension
                          publisher        String                     Microsoft.Compute
                          type             String                     CustomScriptExtension
                          typeHandlerVersion  String                     1.10
                          settings         Object                     {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
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
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM5 -Name CustomScriptExtension

ResourceGroupName       : myasegpuvm1
VMName                  : VM5
Name                    : CustomScriptExtension
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.Compute
ExtensionType           : CustomScriptExtension
TypeHandlerVersion      : 1.10
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM5/extensions/CustomScriptExtension
PublicSettings          : {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
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

Uitvoer van de extensie wordt vastgelegd in bestanden die zijn gevonden in de volgende map op de virtuele doel machine. 

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

De opgegeven bestanden worden gedownload naar de volgende map op de virtuele doel machine.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```
waar <n> is een decimaal geheel getal. Dit kan veranderen tussen de uitvoeringen van de uitbrei ding. De waarde 1. * komt overeen met de werkelijke, huidige `typeHandlerVersion` waarde van de uitbrei ding. Bijvoorbeeld, de daad werkelijke map in dit geval was `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.10.9\Downloads\0` . 


In dit geval is de opdracht voor het uitvoeren van de aangepaste extensie: `md C:\\Users\\Public\\Documents\\test` . Wanneer de uitbrei ding is geïnstalleerd, kunt u controleren of de map in de virtuele machine is gemaakt in het opgegeven pad in de opdracht. 


### <a name="custom-script-extension-for-linux"></a>Aangepaste script extensie voor Linux

Als u een aangepaste script extensie voor Windows wilt implementeren voor een VM die wordt uitgevoerd op uw apparaat, bewerkt u het `addCSExtLinuxVM.parameters.json` parameter bestand en implementeert u vervolgens de sjabloon `addCSExtensiontoVM.json` .

#### <a name="edit-parameters-file"></a>Parameter bestand bewerken

Het bestand `addCSExtLinuxVM.parameters.json` heeft de volgende para meters:

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<Name of your VM>" 
        },
        "extensionName": {
            "value": "<Name of your extension>" 
        },
        "publisher": {
            "value": "Microsoft.Azure.Extensions" 
        },
        "type": {
            "value": "CustomScript" 
        },
        "typeHandlerVersion": {
            "value": "2.0" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "<Command to execute>"
            }
        }
    }
}
```
Geef de naam van de virtuele machine, de naam van de uitbrei ding en de opdracht die u wilt uitvoeren.

Hier volgt een voor beeld van een parameter bestand dat in dit artikel is gebruikt:

```powershell
$templateFile = "<Path to addCSExtensionToVM.json file>"
$templateParameterFile = "<Path to addCSExtLinuxVM.parameters.json file>"
$RGName = "<Resource group name>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Deployment name>"
``` 

> [!NOTE]
> De implementatie van de extensie is een langlopende taak en duurt ongeveer 10 minuten om te volt ooien.

Hier volgt een voorbeeld van uitvoer:

```powershell
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addCSExtensionToVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addCSExtLinuxVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment99"

DeploymentName          : deployment99
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/18/2020 1:55:23 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM6
                          extensionName    String                     LinuxCustomScriptExtension
                          publisher        String                     Microsoft.Azure.Extensions
                          type             String                     CustomScript
                          typeHandlerVersion  String                     2.0
                          settings         Object                     {
                            "commandToExecute": "sudo echo 'some text' >> /home/Administrator/file2.txt"
                          }

Outputs                 :
DeploymentDebugLogLevel :

PS C:\WINDOWS\system32>
```

De `commandToExecute` is ingesteld op het maken van een bestand `file2.txt` in de `/home/Administrator` map en de inhoud van het bestand `some text` . In dit geval kunt u controleren of het bestand is gemaakt in het opgegeven pad.

```powershell
Administrator@VM6:~$ dir
file2.txt
Administrator@VM6:~$ cat file2.txt
some text
Administrator@VM6:
```

#### <a name="track-deployment-status"></a>Implementatie status bijhouden    
    
Sjabloonimlementatie is een langlopende taak. Als u de implementatie status van extensies voor een bepaalde virtuele machine wilt controleren, opent u een nieuwe Power shell-sessie (als administrator uitvoeren). Voer de volgende opdracht uit: 

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName <VM Name> -Name <Extension Name>
```
Hier volgt een voorbeeld van uitvoer: 

```powershell
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM5 -Name CustomScriptExtension

ResourceGroupName       : myasegpuvm1
VMName                  : VM5
Name                    : CustomScriptExtension
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.Compute
ExtensionType           : CustomScriptExtension
TypeHandlerVersion      : 1.10
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM5/extensions/CustomScriptExtension
PublicSettings          : {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
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

De uitvoer van de uitbrei ding voor uitvoering wordt geregistreerd in het volgende bestand: `/var/lib/waagent/custom-script/download/0/` .


## <a name="remove-custom-script-extension"></a>Aangepaste script extensie verwijderen

Als u de aangepaste script extensie wilt verwijderen, gebruikt u de volgende opdracht:

`Remove-AzureRmVMExtension -ResourceGroupName <Resource group name> -VMName <VM name> -Name <Extension name>`

Hier volgt een voorbeeld van uitvoer:

```powershell
PS C:\WINDOWS\system32> Remove-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM6 -Name LinuxCustomScriptExtension
Virtual machine extension removal operation
This cmdlet will remove the specified virtual machine extension. Do you want to continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Yes
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```


## <a name="next-steps"></a>Volgende stappen

[Azure Resource Manager-cmdlets](/powershell/module/azurerm.resources/?view=azurermps-6.13.0)
