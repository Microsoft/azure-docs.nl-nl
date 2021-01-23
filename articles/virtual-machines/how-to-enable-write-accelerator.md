---
title: Azure Write Accelerator
description: Documentatie over het inschakelen en gebruiken van Write Accelerator
author: raiye
manager: markkie
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure
ms.date: 2/20/2019
ms.author: raiye
ms.subservice: disks
ms.openlocfilehash: 827643866c23583051bc290c2c50bed3f1bdd421
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737911"
---
# <a name="enable-write-accelerator"></a>Write Accelerator inschakelen

Write Accelerator is een schijf mogelijkheid voor Virtual Machines van de M-serie (Vm's) op Premium Storage alleen met Azure Managed Disks. Als de naam statussen is het doel van de functionaliteit om de I/O-latentie van schrijf bewerkingen voor Azure Premium Storage te verbeteren. Write Accelerator is in het ideale geval waar logboek bestanden moeten worden opgeslagen op de schijf op een zeer krachtige manier voor moderne data bases.

Write Accelerator is algemeen beschikbaar voor virtuele machines uit de M-serie in de open bare Cloud.

## <a name="planning-for-using-write-accelerator"></a>Planning voor het gebruik van Write Accelerator

Write Accelerator moet worden gebruikt voor de volumes die het transactie logboek bevatten of het opnieuw uitvoeren van logboeken van een DBMS. Het is niet raadzaam om Write Accelerator te gebruiken voor de gegevens volumes van een DBMS, omdat de functie is geoptimaliseerd voor het gebruik van logboek schijven.

Write Accelerator werkt alleen in combi natie met [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/).

> [!IMPORTANT]
> Als u Write Accelerator inschakelt voor de besturingssysteem schijf van de virtuele machine, wordt de VM opnieuw opgestart.
>
> Als u Write Accelerator wilt inschakelen voor een bestaande Azure-schijf die geen deel uitmaakt van een volume met Windows-schijf-of volume beheerders, Windows-opslag ruimten, Windows scale-out file server (SOFS), Linux LVM of MDADM, moet de werk belasting die toegang heeft tot de Azure-schijf, worden afgesloten. Database toepassingen die gebruikmaken van de Azure-schijf, moeten worden afgesloten.
>
> Als u Write Accelerator wilt in-of uitschakelen voor een bestaand volume dat is gebouwd op meerdere Azure Premium Storage schijven en striped met Windows-schijf-of volume managers, Windows-opslag ruimten, Windows scale-out file server (SOFS), Linux LVM of MDADM, moeten alle schijven die het volume bouwen, in afzonderlijke stappen worden in-of uitgeschakeld voor Write Accelerator. **Voordat u write Accelerator in een dergelijke configuratie inschakelt, sluit u de Azure VM af**.

Het inschakelen van Write Accelerator voor besturingssysteem schijven moet niet nodig zijn voor SAP-gerelateerde VM-configuraties.

### <a name="restrictions-when-using-write-accelerator"></a>Beperkingen bij het gebruik van Write Accelerator

Bij het gebruik van Write Accelerator voor een Azure-schijf/VHD gelden de volgende beperkingen:

- De Premium-schijf cache moet worden ingesteld op ' geen ' of ' alleen-lezen '. Alle andere cache modi worden niet ondersteund.
- Moment opnamen worden momenteel niet ondersteund voor schijven met Write Accelerator-functionaliteit. Tijdens het maken van een back-up worden Write Accelerator schijven die zijn gekoppeld aan de virtuele machine automatisch door de Azure Backup-service uitgesloten.
- Alleen kleinere I/O-grootten (<= 512 KiB) nemen het versnelde pad in beslag. In werkbelasting situaties waarbij gegevens bulksgewijs worden geladen of waarbij de transactie logboek buffers van de verschillende DBMS worden gevuld naar een grotere mate voordat persistent wordt gemaakt voor de opslag, is de kans groot dat de I/O-schrijf bewerkingen naar de schijf geen versneld pad nemen.

Er zijn limieten van Azure Premium Storage Vhd's per VM die door Write Accelerator kunnen worden ondersteund. De huidige limieten zijn:

| VM-SKU | Aantal Write Accelerator schijven | Write Accelerator schijf-IOPS per VM |
| --- | --- | --- |
| M416ms_v2, M416s_v2| 16 | 20.000 |
| M208ms_v2, M208s_v2| 8 | 10.000 |
| M128ms, M128s | 16 | 20.000 |
| M64ms, M64ls, M64s | 8 | 10.000 |
| M32 MS, M32ls, M32ts, M32s | 4 | 5000 |
| M16 MS, M16s | 2 | 2500 |
| M8 MS, M8s | 1 | 1250 |

De limieten voor IOPS zijn per VM en *niet* per schijf. Alle Write Accelerator schijven delen dezelfde IOPS-limiet per VM. Gekoppelde schijven kunnen niet groter zijn dan de IOPS-limiet voor schrijf toegang voor een virtuele machine. Een voor beeld: Hoewel de gekoppelde schijven 30.000 IOPS kunnen doen, staat het systeem niet toe dat de schijven groter dan 20.000 IOPS voor M416ms_v2.

## <a name="enabling-write-accelerator-on-a-specific-disk"></a>Write Accelerator inschakelen op een bepaalde schijf

In de volgende secties wordt beschreven hoe Write Accelerator kan worden ingeschakeld op virtuele harde schijven van Azure Premium Storage.

### <a name="prerequisites"></a>Vereisten

De volgende vereisten zijn van toepassing op het gebruik van Write Accelerator op dit moment:

- De schijven waarop u Azure Write Accelerator wilt Toep assen, moeten [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/) zijn op Premium Storage.
- U moet een virtuele machine uit de M-serie gebruiken

## <a name="enabling-azure-write-accelerator-using-azure-powershell"></a>Azure Write Accelerator inschakelen met behulp van Azure PowerShell

De Azure Power shell-module van versie 5.5.0 bevat de wijzigingen in de relevante cmdlets om Write Accelerator voor specifieke Azure Premium Storage-schijven in of uit te scha kelen.
Voor het inschakelen of implementeren van schijven die door Write Accelerator worden ondersteund, zijn de volgende Power shell-opdrachten gewijzigd en uitgebreid om een para meter voor Write Accelerator te accepteren.

Er is een nieuwe switch parameter **-Write Accelerator** toegevoegd aan de volgende cmdlets:

- [Set-AzVMOsDisk](/powershell/module/az.compute/set-azvmosdisk)
- [Add-AzVMDataDisk](/powershell/module/az.compute/Add-AzVMDataDisk)
- [Set-AzVMDataDisk](/powershell/module/az.compute/Set-AzVMDataDisk)
- [Add-AzVmssDataDisk](/powershell/module/az.compute/Add-AzVmssDataDisk)

Als u de para meter niet opgeeft, wordt de eigenschap ingesteld op False en worden schijven zonder ondersteuning voor Write Accelerator geïmplementeerd.

Er is een nieuwe switch parameter **-osdiskwriteaccelerator is** toegevoegd aan de volgende cmdlets:

- [Set-AzVmssStorageProfile](/powershell/module/az.compute/Set-AzVmssStorageProfile)

Als u de para meter niet opgeeft, wordt de eigenschap standaard ingesteld op ONWAAR, waarbij schijven worden geretourneerd die geen gebruikmaken van Write Accelerator.

Een nieuwe optionele Booleaanse para meter (niet-nullable), **-osdiskwriteaccelerator is** is toegevoegd aan de volgende cmdlets:

- [Update-AzVM](/powershell/module/az.compute/Update-AzVM)
- [Update-AzVmss](/powershell/module/az.compute/Update-AzVmss)

Geef $true of $false op om de ondersteuning van Azure Write Accelerator met de schijven te beheren.

Voor beelden van opdrachten kunnen er als volgt uitzien:

```powershell
New-AzVMConfig | Set-AzVMOsDisk | Add-AzVMDataDisk -Name "datadisk1" | Add-AzVMDataDisk -Name "logdisk1" -WriteAccelerator | New-AzVM

Get-AzVM | Update-AzVM -OsDiskWriteAccelerator $true

New-AzVmssConfig | Set-AzVmssStorageProfile -OsDiskWriteAccelerator | Add-AzVmssDataDisk -Name "datadisk1" -WriteAccelerator:$false | Add-AzVmssDataDisk -Name "logdisk1" -WriteAccelerator | New-AzVmss

Get-AzVmss | Update-AzVmss -OsDiskWriteAccelerator:$false
```

Er kunnen twee belang rijke scenario's worden vastgelegd, zoals wordt weer gegeven in de volgende secties.

### <a name="adding-a-new-disk-supported-by-write-accelerator-using-powershell"></a>Een nieuwe schijf toevoegen die wordt ondersteund door Write Accelerator met behulp van Power shell

U kunt dit script gebruiken om een nieuwe schijf toe te voegen aan uw VM. De schijf die is gemaakt met dit script maakt gebruik van Write Accelerator.

Vervang `myVM` , `myWAVMs` , `log001` , grootte van de schijf en LunID van de schijf door de waarden die geschikt zijn voor uw specifieke implementatie.

```powershell
# Specify your VM Name
$vmName="myVM"
#Specify your Resource Group
$rgName = "myWAVMs"
#data disk name
$datadiskname = "log001"
#LUN Id
$lunid=8
#size
$size=1023
#Pulls the VM info for later
$vm=Get-AzVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Add-AzVMDataDisk -CreateOption empty -DiskSizeInGB $size -Name $vmname-$datadiskname -VM $vm -Caching None -WriteAccelerator:$true -lun $lunid
#Updates the VM with the disk config - does not require a reboot
Update-AzVM -ResourceGroupName $rgname -VM $vm
```

### <a name="enabling-write-accelerator-on-an-existing-azure-disk-using-powershell"></a>Write Accelerator in te scha kelen op een bestaande Azure-schijf met behulp van Power shell

U kunt dit script gebruiken om Write Accelerator in te scha kelen op een bestaande schijf. Vervang `myVM` , `myWAVMs` , en `test-log001` door de waarden die van toepassing zijn op uw specifieke implementatie. Het script voegt Write Accelerator toe aan een bestaande schijf waarvan de waarde voor **$NewStatus** is ingesteld op $True. Als u de waarde ' $false ' gebruikt, wordt Write Accelerator op een bepaalde schijf uitgeschakeld.

```powershell
#Specify your VM Name
$vmName="myVM"
#Specify your Resource Group
$rgName = "myWAVMs"
#data disk name
$datadiskname = "test-log001" 
#new Write Accelerator status ($true for enabled, $false for disabled) 
$newstatus = $true
#Pulls the VM info for later
$vm=Get-AzVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Set-AzVMDataDisk -VM $vm -Name $datadiskname -Caching None -WriteAccelerator:$newstatus
#Updates the VM with the disk config - does not require a reboot
Update-AzVM -ResourceGroupName $rgname -VM $vm
```

> [!Note]
> Als het bovenstaande script wordt uitgevoerd, ontkoppelt u de schijf die is opgegeven, schakelt u Write Accelerator op de schijf en koppelt u de schijf opnieuw

## <a name="enabling-write-accelerator-using-the-azure-portal"></a>Write Accelerator inschakelen met behulp van de Azure-portal

U kunt Write Accelerator inschakelen via de portal waar u de instellingen voor de schijf cache opgeeft:

![Write Accelerator op de Azure Portal](./media/virtual-machines-common-how-to-enable-write-accelerator/wa_scrnsht.png)

## <a name="enabling-write-accelerator-using-the-azure-cli"></a>Write Accelerator inschakelen met behulp van Azure CLI

U kunt de [Azure cli](/cli/azure/) gebruiken om Write Accelerator in te scha kelen.

Als u Write Accelerator op een bestaande schijf wilt inschakelen, gebruikt u [AZ VM update](/cli/azure/vm#az_vm_update), kunt u de volgende voor beelden gebruiken als u de diskettes, VMName en ResourceGroup vervangt door uw eigen waarden: `az vm update -g group1 -n vm1 -write-accelerator 1=true`

Als u een schijf wilt koppelen met Write Accelerator ingeschakeld Gebruik [AZ VM Disk attach](/cli/azure/vm/disk#az_vm_disk_attach), kunt u het volgende voor beeld gebruiken als u vervangt door uw eigen waarden: `az vm disk attach -g group1 -vm-name vm1 -disk d1 --enable-write-accelerator`

Als u Write Accelerator wilt uitschakelen, gebruikt u [AZ VM update](/cli/azure/vm#az_vm_update)en stelt u de eigenschappen in op False: `az vm update -g group1 -n vm1 -write-accelerator 0=false 1=false`

## <a name="enabling-write-accelerator-using-rest-apis"></a>Inschakelen van Write Accelerator met rest-Api's

Als u wilt implementeren met behulp van de rest API van Azure, moet u de Azure-armclient installeren.

### <a name="install-armclient"></a>Armclient installeren

Als u armclient wilt uitvoeren, moet u deze via chocolade installeren. U kunt dit installeren via cmd.exe of Power shell. Gebruik verhoogde rechten voor deze opdrachten ("als administrator uitvoeren").

Voer met behulp van cmd.exe de volgende opdracht uit: `@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"`

Gebruik Power shell om de volgende opdracht uit te voeren: `Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))`

U kunt de armclient nu installeren met behulp van de volgende opdracht in cmd.exe of Power shell `choco install armclient`

### <a name="getting-your-current-vm-configuration"></a>De huidige VM-configuratie ophalen

Als u de kenmerken van uw schijf configuratie wilt wijzigen, moet u eerst de huidige configuratie in een JSON-bestand ophalen. U kunt de huidige configuratie ophalen door de volgende opdracht uit te voeren: `armclient GET /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 > <<filename.json>>`

Vervang de voor waarden in ' <<   >> ' door uw gegevens, inclusief de bestands naam die het JSON-bestand moet bevatten.

De uitvoer kan er als volgt uitzien:

```JSON
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"

```

Werk vervolgens het JSON-bestand bij en om Write Accelerator in te scha kelen op de schijf met de naam ' log1 '. Dit kan worden bereikt door dit kenmerk toe te voegen aan het JSON-bestand na de cache vermelding van de schijf.

```JSON
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "writeAcceleratorEnabled": true,
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
```

Werk vervolgens de bestaande implementatie bij met deze opdracht: `armclient PUT /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 @<<filename.json>>`

De uitvoer moet er als volgt uitzien. U kunt zien dat Write Accelerator ingeschakeld voor één schijf.

```JSON
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "writeAcceleratorEnabled": true,
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"
```

Zodra u deze wijziging hebt aangebracht, moet het station door Write Accelerator worden ondersteund.
