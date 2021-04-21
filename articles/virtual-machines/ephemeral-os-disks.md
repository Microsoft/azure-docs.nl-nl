---
title: Kortstondige besturingssysteemschijven
description: Meer informatie over kortstondige besturingssysteemschijven voor Azure-VM's.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/23/2020
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 24b1be2ca55b057c887c8782ce7eea1150f143da
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762619"
---
# <a name="ephemeral-os-disks-for-azure-vms"></a>Kortstondige besturingssysteemschijven voor Azure-VM's

Kortstondige besturingssysteemschijven worden gemaakt op de lokale VM-opslag (virtuele machine) en niet opgeslagen op de externe Azure Storage. Kortstondige besturingssysteemschijven werken goed voor staatloze workloads, waarbij toepassingen tolerant zijn voor afzonderlijke VM-fouten, maar meer worden beïnvloed door de implementatietijd van de VM of het opnieuw maken van de installatie van de afzonderlijke VM-exemplaren. Met een kortstondige besturingssysteemschijf krijgt u een lagere lees-/schrijflatentie naar de besturingssysteemschijf en een snellere VM-reimage. 
 
De belangrijkste functies van kortstondige schijven zijn: 
- Ideaal voor staatloze toepassingen.
- Ze kunnen worden gebruikt met zowel Marketplace- als aangepaste afbeeldingen.
- Mogelijkheid om VM's snel opnieuw in te stellen of een nieuweimage te geven en exemplaren van de schaalset naar de oorspronkelijke opstarttoestand te herstellen.  
- Lagere latentie, vergelijkbaar met een tijdelijke schijf. 
- Kortstondige besturingssysteemschijven zijn gratis. Er worden geen opslagkosten voor de besturingssysteemschijf in u opgeslagen.
- Ze zijn beschikbaar in alle Azure-regio's. 
- Kortstondige besturingssysteemschijf wordt ondersteund door [Shared Image Gallery](./shared-image-galleries.md). 
 

 
Belangrijkste verschillen tussen permanente en kortstondige besturingssysteemschijven:

|                             | Permanente besturingssysteemschijf                          | Kortstondige besturingssysteemschijf                              |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| **Groottelimiet voor besturingssysteemschijf**      | 2 TiB                                                                                        | Cachegrootte voor de VM-grootte of 2TiB, wat kleiner is. Zie [DS,](sizes-general.md) [ES,](sizes-memory.md) [M,](sizes-memory.md) [FS](sizes-compute.md)en [GS](sizes-previous-gen.md#gs-series) voor de **cachegrootte in GiB**              |
| **Ondersteunde VM-grootten**          | Alles                                                                                          | VM-grootten die Premium-opslag ondersteunen, zoals DSv1, DSv2, DSv3, Esv3, Fs, FsV2, GS, M                                               |
| **Ondersteuning voor schijftype**           | Beheerde en niet-beheerde besturingssysteemschijf                                                                | Alleen beheerde besturingssysteemschijf                                                               |
| **Ondersteuning voor regio**              | Alle regio's                                                                                  | Alle regio's                              |
| **Gegevenspersistentie**            | Gegevens van besturingssysteemschijven die naar de besturingssysteemschijf worden geschreven, worden opgeslagen in Azure Storage                                  | Gegevens die naar de besturingssysteemschijf worden geschreven, worden opgeslagen in de lokale VM-opslag en worden niet opgeslagen Azure Storage. |
| **Status van niet-toegewezen toewijzing**      | VM's en exemplaren van schaalsets kunnen worden gestopt en opnieuw worden gestart vanuit de status van de toewijzing van de stop-deal | De toewijzing van VM's en schaalsets kan niet worden gestopt                                  |
| **Ondersteuning voor gespecialiseerde besturingssysteemschijven** | Ja                                                                                          | Nee                                                                                 |
| **Het besturingssysteem van de schijf wordt het besturingssysteem**              | Ondersteund tijdens het maken van de VM en nadat de toewijzing van de VM is gestopt                                | Alleen ondersteund tijdens het maken van een VM                                                  |
| **Grootte van een nieuwe VM-grootte**   | Gegevens van besturingssysteemschijven blijven behouden                                                                    | Gegevens op de besturingssysteemschijf worden verwijderd, het besturingssysteem wordt opnieuw ingericht       
| **Plaatsing van paginabestand**   | Voor Windows wordt het paginabestand opgeslagen op de resourceschijf                                              | Voor Windows wordt het paginabestand opgeslagen op de besturingssysteemschijf   |

## <a name="size-requirements"></a>Groottevereisten

U kunt VM- en exemplaar-afbeeldingen implementeren tot de grootte van de VM-cache. Standaard Windows Server-afbeeldingen van marketplace zijn bijvoorbeeld ongeveer 127 GiB, wat betekent dat u een VM-grootte nodig hebt met een cache die groter is dan 127 GiB. In dit geval heeft [Standard_DS2_v2](dv2-dsv2-series.md) een cachegrootte van 86 GiB, die niet groot genoeg is. De Standard_DS3_v2 heeft een cachegrootte van 172 GiB, wat groot genoeg is. In dit geval is Standard_DS3_v2 de kleinste grootte in de DSv2-serie die u met deze afbeelding kunt gebruiken. Basis-Linux-afbeeldingen in Marketplace en Windows Server-afbeeldingen die worden aangeduid met zijn meestal ongeveer 30 GiB en kunnen de meeste van de beschikbare `[smallsize]` VM-grootten gebruiken.

Tijdelijke schijven vereisen ook dat de VM-grootte Premium-opslag ondersteunt. De grootten hebben meestal (maar niet altijd) `s` een in de naam, zoals DSv2 en EsV3. Zie Azure [VM-grootten voor](sizes.md) meer informatie over welke grootten Ondersteuning bieden voor Premium-opslag.

## <a name="preview---ephemeral-os-disks-can-now-be-stored-on-temp-disks"></a>Preview- Tijdelijke besturingssysteemschijven kunnen nu worden opgeslagen op tijdelijke schijven
Tijdelijke besturingssysteemschijven kunnen nu naast de VM-cache worden opgeslagen op de tijdelijke/resourceschijf van de VM. U kunt nu tijdelijke besturingssysteemschijven gebruiken met VM's die geen cache hebben of onvoldoende cache hebben, maar een tijdelijke/resourceschijf hebben voor het opslaan van de tijdelijke besturingssysteemschijf, zoals Dav3, Dav4, Eav4 en Eav3. Als een VM voldoende cache- en tijdelijke ruimte heeft, kunt u nu ook opgeven waar u de tijdelijke besturingssysteemschijf wilt opslaan met behulp van een nieuwe eigenschap met de naam [DiffDiskPlacement.](/rest/api/compute/virtualmachines/list#diffdiskplacement) Met deze functie configureren we, wanneer een Windows-VM is ingericht, het paginabestand op de besturingssysteemschijf. Deze functie is momenteel beschikbaar als preview-product. Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Vraag toegang aan om [aan de slag te gaan.](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR6cQw0fZJzdIsnbfbI13601URTBCRUZPMkQwWFlCOTRIMFBSNkM1NVpQQS4u)

## <a name="powershell"></a>PowerShell

Als u een kortstondige schijf wilt gebruiken voor een Implementatie van een PowerShell-VM, gebruikt u [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) in uw VM-configuratie. Stel de `-DiffDiskSetting` in op en op `Local` `-Caching` `ReadOnly` .     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

Gebruik voor schaalsetimplementaties de cmdlet [Set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) in uw configuratie. Stel de `-DiffDiskSetting` in op en op `Local` `-Caching` `ReadOnly` .


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>CLI

Als u een kortstondige schijf wilt gebruiken voor een CLI-VM-implementatie, stelt u de `--ephemeral-os-disk` parameter in [az vm create](/cli/azure/vm#az_vm_create) in op en stelt u de parameter in op `true` `--os-disk-caching` `ReadOnly` .

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

Voor schaalsets gebruikt u dezelfde `--ephemeral-os-disk true` parameter voor [az-vmss-create](/cli/azure/vmss#az_vmss_create) en stelt u de `--os-disk-caching` parameter in op `ReadOnly` .

## <a name="portal"></a>Portal

In de Azure Portal kunt u ervoor kiezen om kortstondige schijven te gebruiken bij  het implementeren van een VM door de sectie Geavanceerd van het tabblad **Schijven te** openen. Bij **Kortstondige besturingssysteemschijf gebruiken selecteert** u **Ja.**

![Schermopname van het keuzerondje voor het gebruik van een kortstondige besturingssysteemschijf](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

Als de optie voor het gebruik van een tijdelijke schijf grijs wordt, hebt u mogelijk een VM-grootte geselecteerd die geen cachegrootte heeft die groter is dan de besturingssysteemafbeelding of die geen ondersteuning biedt voor Premium-opslag. Terug naar de **pagina Basisinformatie** en kies een andere VM-grootte.

U kunt ook schaalsets met kortstondige besturingssysteemschijven maken met behulp van de portal. Zorg ervoor dat u een VM-grootte selecteert met voldoende cachegrootte en selecteer vervolgens ja in **Kortstondige besturingssysteemschijf** **gebruiken.**

![Schermopname van het keuzerondje voor het kiezen van een kortstondige besturingssysteemschijf voor uw schaalset](./media/virtual-machines-common-ephemeral/scale-set.png)

## <a name="scale-set-template-deployment"></a>Implementatie van schaalsetsjabloon  
Het proces voor het maken van een schaalset die gebruikmaakt van een kortstondige besturingssysteemschijf is het toevoegen van de eigenschap aan het `diffDiskSettings` `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` resourcetype in de sjabloon. Het beleid voor caching moet ook worden ingesteld op `ReadOnly` voor de kortstondige besturingssysteemschijf. 


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

## <a name="vm-template-deployment"></a>Implementatie van VM-sjablonen 
U kunt een VM met een kortstondige besturingssysteemschijf implementeren met behulp van een sjabloon. Het proces voor het maken van een virtuele machine die gebruikmaakt van kortstondige besturingssysteemschijven is het toevoegen van de eigenschap aan het `diffDiskSettings` resourcetype Microsoft.Compute/virtualMachines in de sjabloon. Het beleid voor caching moet ook worden ingesteld op `ReadOnly` voor de kortstondige besturingssysteemschijf. 

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


## <a name="reimage-a-vm-using-rest"></a>Een VM opnieuw maken met REST
U kunt de afbeelding van een exemplaar van een virtuele machine met een kortstondige besturingssysteemschijf opnieuw maken met behulp van REST API zoals hieronder en via Azure Portal wordt beschreven door naar het deelvenster Overzicht van de virtuele machine te gaan. Voor schaalsets is het maken van een nieuwe animatie al beschikbaar via Powershell, CLI en de portal.

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>Veelgestelde vragen

**V: Wat is de grootte van de lokale besturingssysteemschijven?**

A: We ondersteunen platform- en aangepaste afbeeldingen, tot aan de grootte van de VM-cache, waarbij alle lees-/schrijf-/schrijf-gegevens naar de besturingssysteemschijf lokaal zijn op hetzelfde knooppunt als de virtuele machine. 

**V: Kan de kortstondige besturingssysteemschijf worden gedimd?**

A: Nee, zodra de kortstondige besturingssysteemschijf is ingericht, kan de besturingssysteemschijf niet meer worden geseed. 

**V: Kan ik een Managed Disks koppelen aan een kortstondige VM?**

A: Ja, u kunt een beheerde gegevensschijf koppelen aan een VM die gebruikmaakt van een kortstondige besturingssysteemschijf. 

**V: Worden alle VM-grootten ondersteund voor kortstondige besturingssysteemschijven?**

A: Nee, de Premium Storage VM-grootten worden ondersteund (DS, ES, FS, GS, M, enzovoort). Als u wilt weten of een bepaalde VM-grootte kortstondige besturingssysteemschijven ondersteunt, kunt u het volgende doen:

De `Get-AzComputeResourceSku` PowerShell-cmdlet aanroepen
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
 
**V: Kan de kortstondige besturingssysteemschijf worden toegepast op bestaande VM's en schaalsets?**

A: Nee, een kortstondige besturingssysteemschijf kan alleen worden gebruikt tijdens het maken van een VM en schaalset. 

**V: Kunt u kortstondige en normale besturingssysteemschijven combineren in een schaalset?**

A: Nee, u kunt geen combinatie van kortstondige en permanente exemplaren van besturingssysteemschijven binnen dezelfde schaalset hebben. 

**V: Kan de kortstondige besturingssysteemschijf worden gemaakt met Behulp van PowerShell of CLI?**

A: Ja, u kunt VM's met een kortstondige besturingssysteemschijf maken met behulp van REST, sjablonen, PowerShell en CLI.

**V: Welke functies worden niet ondersteund met een kortstondige besturingssysteemschijf?**

A: Kortstondige schijven bieden geen ondersteuning voor:
- VM-afbeeldingen vastleggen
- Momentopnamen van schijven 
- Azure Disk Encryption 
- Azure Backup
- Azure Site Recovery  
- Wisselen van besturingssysteemschijf 

> [!NOTE]
> 
> Kortstondige schijven zijn niet toegankelijk via de portal. U ontvangt de fout 'Resource niet gevonden' of '404' bij het openen van de kortstondige schijf die wordt verwacht.
> 
 
## <a name="next-steps"></a>Volgende stappen
U kunt een VM met een kortstondige besturingssysteemschijf maken met behulp van [de Azure CLI.](/cli/azure/vm#az_vm_create)
