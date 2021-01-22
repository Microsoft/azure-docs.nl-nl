---
title: Kortstondige besturingssysteemschijven
description: Meer informatie over tijdelijke besturingssysteem schijven voor virtuele Azure-machines.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/23/2020
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 62f89106538ab7f57047e211fc8715878f889af1
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98684556"
---
# <a name="ephemeral-os-disks-for-azure-vms"></a>Tijdelijke besturingssysteem schijven voor virtuele Azure-machines

Tijdelijke besturingssysteem schijven worden gemaakt op de lokale virtuele machine (VM) en worden niet opgeslagen op de externe Azure Storage. Tijdelijke besturingssysteem schijven werken goed voor stateless workloads, waarbij toepassingen tolerant zijn voor afzonderlijke VM-fouten, maar die meer worden beïnvloed door de implementatie tijd van de VM of het opnieuw maken van de afzonderlijke VM-exemplaren. Met een kortstondige besturingssysteem schijf krijgt u een lagere latentie voor lezen/schrijven naar de besturingssysteem schijf en een snellere installatie kopie van de virtuele machine. 
 
De belangrijkste functies van tijdelijke schijven zijn: 
- Ideaal voor stateless toepassingen.
- Ze kunnen worden gebruikt met zowel Marketplace als aangepaste installatie kopieën.
- De mogelijkheid om Vm's en installatie kopieën van virtuele machines en schaal sets snel opnieuw in te stellen of te herstellen naar de oorspronkelijke opstart status.  
- Lagere latentie, vergelijkbaar met een tijdelijke schijf. 
- Tijdelijke besturingssysteem schijven zijn gratis, er zijn geen opslag kosten voor de besturingssysteem schijf.
- Deze zijn beschikbaar in alle Azure-regio's. 
- Tijdelijke besturingssysteem schijf wordt ondersteund door de [Galerie met gedeelde afbeeldingen](./shared-image-galleries.md). 
 

 
Belangrijkste verschillen tussen permanente en tijdelijke besturingssysteem schijven:

|                             | Permanente besturingssysteem schijf                          | Kortstondige besturingssysteemschijf                              |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| **Maximale grootte voor de besturingssysteem schijf**      | 2 TiB                                                                                        | De cache grootte voor de VM-grootte of 2TiB, afhankelijk van wat kleiner is. Zie [DS](sizes-general.md), [es](sizes-memory.md), [M](sizes-memory.md), [FS](sizes-compute.md)en [GS](sizes-previous-gen.md#gs-series) voor de **cache grootte in GiB**              |
| **VM-grootten worden ondersteund**          | Alles                                                                                          | VM-grootten die ondersteuning bieden voor Premium-opslag, zoals DSv1, DSv2, DSv3, Esv3, FS, FsV2, GS, M                                               |
| **Ondersteuning voor schijf type**           | Beheerde en onbeheerde besturingssysteem schijf                                                                | Alleen beheerde besturingssysteem schijf                                                               |
| **Ondersteuning voor regio**              | Alle regio's                                                                                  | Alle regio's                              |
| **Gegevenspersistentie**            | De schijf gegevens van het besturings systeem die naar de besturingssysteem schijf zijn geschreven, worden opgeslagen in Azure Storage                                  | Gegevens die naar de besturingssysteem schijf zijn geschreven, worden opgeslagen in de lokale VM-opslag en worden niet bewaard om Azure Storage. |
| **Status voor stoppen-toewijzing ongedaan gemaakt**      | Vm's en schaal sets instanties kunnen stoppen en opnieuw worden toegewezen en opnieuw worden gestart vanaf de status voor het ongedaan maken van de toewijzing | Vm's en instanties van schaal sets kunnen niet stoppen-toewijzing opheffen                                  |
| **Ondersteuning van gespecialiseerde besturingssysteem schijf** | Ja                                                                                          | Nee                                                                                 |
| **Grootte van besturingssysteem schijf**              | Ondersteund tijdens het maken van de virtuele machine en nadat de virtuele machine is onttoewijzing gestopt                                | Alleen ondersteund tijdens het maken van VM'S                                                  |
| **Formaat wijzigen in een nieuwe VM-grootte**   | De schijf gegevens van het besturings systeem blijven behouden                                                                    | Gegevens op de besturingssysteem schijf worden verwijderd, het besturings systeem wordt opnieuw ingericht       
| **Plaatsing van wissel bestand**   | Voor Windows wordt het wissel bestand op de bron schijf opgeslagen                                              | Voor Windows wordt het wissel bestand op de besturingssysteem schijf opgeslagen   |

## <a name="size-requirements"></a>Grootte vereisten

U kunt installatie kopieën van VM'S en instanties implementeren tot de grootte van de VM-cache. Standaard installatie kopieën van Windows Server van de Marketplace hebben bijvoorbeeld ongeveer 127 GiB. Dit betekent dat u een VM-grootte nodig hebt die een cache heeft die groter is dan 127 GiB. In dit geval heeft de [Standard_DS2_v2](dv2-dsv2-series.md) een cache grootte van 86 GiB, die niet groot genoeg is. Het Standard_DS3_v2 heeft een cache grootte van 172 GiB, die groot genoeg is. In dit geval is het Standard_DS3_v2 de kleinste grootte in de DSv2-serie die u kunt gebruiken met deze afbeelding. Basis-Linux-installatie kopieën in de Marketplace en Windows Server-installatie kopieën die worden aangeduid met `[smallsize]` ongeveer 30 GiB en kunnen de meeste van de beschik bare VM-grootten gebruiken.

Voor tijdelijke schijven is het ook vereist dat de VM-grootte Premium-opslag ondersteunt. De grootten zijn meestal (maar niet altijd) `s` met de naam, zoals DSv2 en EsV3. Zie [Azure VM-grootten](sizes.md) voor meer informatie over welke grootten Premium-opslag worden ondersteund.

## <a name="preview---ephemeral-os-disks-can-now-be-stored-on-temp-disks"></a>Voor beeld-tijdelijke besturingssysteem schijven kunnen nu worden opgeslagen op tijdelijke schijven
Tijdelijke besturingssysteem schijven kunnen nu naast de VM-cache worden opgeslagen op een tijdelijke virtuele machine/bron schijf. U kunt nu tijdelijke besturingssysteem schijven gebruiken met een VM die geen cache heeft of onvoldoende cache heeft, maar wel een tijdelijke/bron schijf heeft voor het opslaan van de tijdelijke besturingssysteem schijf, zoals Dav3, Dav4, Eav4 en Eav3. Als een virtuele machine voldoende cache-en tijdelijke ruimte heeft, kunt u nu ook opgeven waar u de tijdelijke besturingssysteem schijf wilt opslaan met behulp van een nieuwe eigenschap met de naam [DiffDiskPlacement](/rest/api/compute/virtualmachines/list#diffdiskplacement). Als er een Windows-VM is ingericht, configureren we het wissel bestand op de besturingssysteem schijf. Deze functie is momenteel beschikbaar als preview-product. Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. [Vraag toegang](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR6cQw0fZJzdIsnbfbI13601URTBCRUZPMkQwWFlCOTRIMFBSNkM1NVpQQS4u)aan om aan de slag te gaan.

## <a name="powershell"></a>PowerShell

Als u een tijdelijke schijf wilt gebruiken voor een implementatie van een Power shell-VM, gebruikt u [set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) in uw VM-configuratie. Stel de waarde `-DiffDiskSetting` in op `Local` en `-Caching` tot `ReadOnly` .     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

Voor implementaties van schaal sets gebruikt u de cmdlet [set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) in uw configuratie. Stel de waarde `-DiffDiskSetting` in op `Local` en `-Caching` tot `ReadOnly` .


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>CLI

Als u een tijdelijke schijf voor een CLI-VM-implementatie wilt gebruiken, stelt u de `--ephemeral-os-disk` para meter in [AZ VM Create](/cli/azure/vm#az-vm-create) to `true` en de `--os-disk-caching` para meter in op `ReadOnly` .

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --ephemeral-os-disk true \
  --os-disk-caching ReadOnly \
  --admin-username azureuser \
  --generate-ssh-keys
```

Voor schaal sets gebruikt u dezelfde `--ephemeral-os-disk true` para meter voor [AZ-vmss-Create](/cli/azure/vmss#az-vmss-create) en stelt u de `--os-disk-caching` para meter in op `ReadOnly` .

## <a name="portal"></a>Portal

In de Azure Portal kunt u ervoor kiezen om tijdelijke schijven te gebruiken bij het implementeren van een VM door de sectie **Advanced** van het tabblad **schijven** te openen. Voor het **gebruik van kortstondige besturingssysteem schijf** selecteert u **Ja**.

![Scherm opname van het keuze rondje voor het kiezen van een kortstondige besturingssysteem schijf](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

Als de optie voor het gebruik van een tijdelijke schijf grijs wordt weer gegeven, hebt u mogelijk een VM-grootte geselecteerd die geen cache grootte heeft die groter is dan de installatie kopie van het besturings systeem of die geen ondersteuning biedt voor Premium Storage. Ga terug naar de pagina met **basis informatie** en kies een andere VM-grootte.

U kunt ook schaal sets maken met tijdelijke besturingssysteem schijven met behulp van de portal. Zorg ervoor dat u een VM-grootte met een groot voldoende cache grootte selecteert en selecteer **Ja** in de **tijdelijke besturingssysteem schijf gebruiken** .

![Scherm afbeelding met het keuze rondje voor het kiezen van een tijdelijke besturingssysteem schijf voor uw schaalset](./media/virtual-machines-common-ephemeral/scale-set.png)

## <a name="scale-set-template-deployment"></a>Implementatie van sjabloon voor schaal sets  
Het proces voor het maken van een schaalset die gebruikmaakt van een kortstondige besturingssysteem schijf is de eigenschap toe te voegen `diffDiskSettings` aan het `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` resource type in de sjabloon. Daarnaast moet het cache beleid worden ingesteld op `ReadOnly` voor de tijdelijke besturingssysteem schijf. 


```json
{ 
  "type": "Microsoft.Compute/virtualMachineScaleSets", 
  "name": "myScaleSet", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "sku": { 
    "name": "Standard_DS2_v2", 
    "capacity": "2" 
  }, 
  "properties": { 
    "upgradePolicy": { 
      "mode": "Automatic" 
    }, 
    "virtualMachineProfile": { 
       "storageProfile": { 
        "osDisk": { 
          "diffDiskSettings": { 
            "option": "Local" 
          }, 
          "caching": "ReadOnly", 
          "createOption": "FromImage" 
        }, 
        "imageReference":  { 
          "publisher": "Canonical", 
          "offer": "UbuntuServer", 
          "sku": "16.04-LTS", 
          "version": "latest" 
        } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvmss", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
  } 
}  
```

## <a name="vm-template-deployment"></a>VM-sjabloon implementatie 
U kunt een virtuele machine met een tijdelijke besturingssysteem schijf implementeren met behulp van een sjabloon. Het proces voor het maken van een virtuele machine die tijdelijke besturingssysteem schijven gebruikt, is de eigenschap toe te voegen `diffDiskSettings` aan het resource type micro soft. Compute/informatie in de sjabloon. Daarnaast moet het cache beleid worden ingesteld op `ReadOnly` voor de tijdelijke besturingssysteem schijf. 

```json
{ 
  "type": "Microsoft.Compute/virtualMachines", 
  "name": "myVirtualMachine", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "properties": { 
       "storageProfile": { 
            "osDisk": { 
              "diffDiskSettings": { 
                "option": "Local" 
              }, 
              "caching": "ReadOnly", 
              "createOption": "FromImage" 
            }, 
            "imageReference": { 
                "publisher": "MicrosoftWindowsServer", 
                "offer": "WindowsServer", 
                "sku": "2016-Datacenter-smalldisk", 
                "version": "latest" 
            }, 
            "hardwareProfile": { 
                 "vmSize": "Standard_DS2_v2" 
             } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvirtualmachine", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
 } 
```


## <a name="reimage-a-vm-using-rest"></a>Installatie kopie van een virtuele machine terugzetten met behulp van REST
U kunt een installatie kopie van een exemplaar van een virtuele machine met behulp van REST API, zoals hieronder beschreven en via Azure Portal, naar het overzichts venster van de VM. Voor schaal sets is reimaging al beschikbaar via Power shell, CLI en de portal.

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>Veelgestelde vragen

**V: wat is de grootte van de schijven van het lokale besturings systeem?**

A: we ondersteunen platform en aangepaste installatie kopieën, tot aan de cache grootte van de virtuele machine, waarbij alle Lees-en schrijf bewerkingen naar de besturingssysteem schijf lokaal zijn op hetzelfde knoop punt als de VM. 

**V: kan de grootte van de tijdelijke besturingssysteem schijf worden gewijzigd?**

A: Nee, zodra de tijdelijke besturingssysteem schijf is ingericht, kan de grootte van de besturingssysteem schijf niet worden gewijzigd. 

**V: kan ik een Managed Disks koppelen aan een kortstondige VM?**

A: Ja, u kunt een beheerde gegevens schijf koppelen aan een virtuele machine die gebruikmaakt van een kortstondige besturingssysteem schijf. 

**V: worden alle VM-grootten ondersteund voor tijdelijke besturingssysteem schijven?**

A: Nee, de meeste Premium Storage VM-grootten worden ondersteund (DS, ES, FS, GS, M, etc.). Als u wilt weten of een bepaalde VM-grootte tijdelijke besturingssysteem schijven ondersteunt, kunt u het volgende doen:

De `Get-AzComputeResourceSku` Power shell-cmdlet aanroepen
```azurepowershell-interactive
 
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
   foreach($capability in $vmSize.capabilities)
   {
       if($capability.Name -eq 'EphemeralOSDiskSupported' -and $capability.Value -eq 'true')
       {
           $vmSize
       }
   }
}
```
 
**V: kan de tijdelijke besturingssysteem schijf worden toegepast op bestaande Vm's en schaal sets?**

A: Nee, kortstondige besturingssysteem schijf kan alleen worden gebruikt tijdens het maken van de virtuele machine en de schaalset. 

**V: kunt u tijdelijke en normale besturingssysteem schijven in een schaalset combi neren?**

A: Nee, u kunt niet beschikken over een combi natie van tijdelijke en permanente besturingssysteem schijf exemplaren binnen dezelfde schaalset. 

**V: kan de tijdelijke besturingssysteem schijf worden gemaakt met Power shell of CLI?**

A: Ja, u kunt virtuele machines maken met een tijdelijke besturingssysteem schijf met behulp van REST, sjablonen, Power shell en CLI.

**V: welke functies worden niet ondersteund met een tijdelijke besturingssysteem schijf?**

A: tijdelijke schijven bieden geen ondersteuning voor:
- VM-installatie kopieën vastleggen
- Momentopnamen van schijven 
- Azure Disk Encryption 
- Azure Backup
- Azure Site Recovery  
- Wisseling van besturingssysteem schijf 
 
## <a name="next-steps"></a>Volgende stappen
U kunt een virtuele machine maken met een tijdelijke besturingssysteem schijf met behulp van de [Azure cli](/cli/azure/vm#az-vm-create).