---
title: InfiniBand-stuur programma-uitbrei ding-Azure Windows-Vm's
description: Microsoft Azure-extensie voor het installeren van InfiniBand-Stuur Programma's op de H-en N-serie reken-Vm's waarop Windows wordt uitgevoerd.
services: virtual-machines
documentationcenter: ''
author: vermagit
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.subservice: hpc
ms.collection: windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/01/2021
ms.author: amverma
ms.openlocfilehash: 81720c18ce858cd5667413a9d39afdadc95acb23
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102559695"
---
# <a name="infiniband-driver-extension-for-windows"></a>InfiniBand-stuur programma-uitbrei ding voor Windows

Deze uitbrei ding installeert InfiniBand ND-Stuur programma's (voor niet-SR-IOV ingeschakeld) en OFED-Stuur programma's (voor SR-IOV-ingeschakeld) (r-grootten) en virtuele machines uit de [H-serie](../sizes-hpc.md) en [N-serie](../sizes-gpu.md) waarop Windows wordt uitgevoerd. Afhankelijk van de VM-serie installeert de extensie de juiste Stuur Programma's voor de Connect-X-NIC.

Er is ook een uitbrei ding beschikbaar om InfiniBand-Stuur Programma's voor [Linux vm's](hpc-compute-infiniband-linux.md)te installeren.

## <a name="prerequisites"></a>Vereisten

### <a name="operating-system"></a>Besturingssysteem

Deze extensie ondersteunt de volgende OS distributies, afhankelijk van de ondersteuning van Stuur Programma's voor een specifieke versie van het besturings systeem. Let op de juiste InfiniBand-NIC voor de VM-grootten van de H en N-serie.

| Distributie | InfiniBand NIC-Stuur Programma's |
|---|---|
| Windows 10 | CX5, CX6 |
| Windows Server 2019 | CX5, CX6 |
| Windows Server 2016 | CX3-Pro, CX5, CX6 |
| Windows Server 2012 R2 | CX3-Pro, CX5, CX6 |
| Windows Server 2012 | CX3-Pro, CX5, CX6 |

### <a name="internet-connectivity"></a>Internetconnectiviteit

De uitbrei ding van de Microsoft Azure voor InfiniBand-Stuur Programma's vereist dat de doel-VM is verbonden met en toegang heeft tot internet.

## <a name="extension-schema"></a>Extensieschema

In de volgende JSON wordt het schema voor de uitbrei ding weer gegeven.

```json
{
  "name": "<myExtensionName>",
  "type": "extensions",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <myVM>)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "InfiniBandDriverWindows",
    "typeHandlerVersion": "1.2",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="properties"></a>Eigenschappen

| Name | Waarde/voor beeld | Gegevenstype |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Micro soft. HpcCompute | tekenreeks |
| type | InfiniBandDriverWindows | tekenreeks |
| typeHandlerVersion | 1.2 | int |



## <a name="deployment"></a>Implementatie


### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sjabloon 

Azure VM-extensies kunnen worden geïmplementeerd met Azure Resource Manager sjablonen. Sjablonen zijn ideaal bij het implementeren van een of meer virtuele machines waarvoor na de implementatie configuratie een vereiste is.

De JSON-configuratie voor een extensie van een virtuele machine kan worden genest in de resource van de virtuele machine of worden geplaatst op het hoofd niveau of op de hoogste niveaus van een JSON-sjabloon van Resource Manager. De plaatsing van de JSON-configuratie is van invloed op de waarde van de naam en het type van de resource. Zie voor meer informatie [naam en type voor onderliggende resources instellen](../../azure-resource-manager/templates/child-resource-name-type.md). 

In het volgende voor beeld wordt ervan uitgegaan dat de extensie is genest in de resource van de virtuele machine. Bij het nesten van de extensie bron wordt de JSON in het `"resources": []` object van de virtuele machine geplaatst.

```json
{
  "name": "myExtensionName",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', myVM)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "InfiniBandDriverWindows",
    "typeHandlerVersion": "1.2",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="powershell"></a>PowerShell

```powershell
Set-AzVMExtension
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Location "southcentralus" `
    -Publisher "Microsoft.HpcCompute" `
    -ExtensionName "InfiniBandDriverWindows" `
    -ExtensionType "InfiniBandDriverWindows" `
    -TypeHandlerVersion 1.2 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name InfiniBandDriverWindows \
  --publisher Microsoft.HpcCompute \
  --version 1.2 
```

### <a name="add-extension-to-a-virtual-machine-scale-set"></a>Extensie toevoegen aan een Schaalset voor virtuele machines

In het volgende voor beeld wordt de meest recente versie 1,2 InfiniBandDriverWindows-uitbrei ding geïnstalleerd op alle virtuele machines met RDMA-functionaliteit in een bestaande schaalset van de VM met de naam *myVMSS* geïmplementeerd in de resource groep met de naam *myResourceGroup*:

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.2"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
```


## <a name="troubleshoot-and-support"></a>Problemen oplossen en ondersteuning

### <a name="troubleshoot"></a>Problemen oplossen

Gegevens over de status van uitbreidings implementaties kunnen worden opgehaald uit de Azure Portal en met behulp van Azure PowerShell en Azure CLI. Voer de volgende opdracht uit om de implementatie status van uitbrei dingen voor een bepaalde virtuele machine weer te geven.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Uitvoer voor uitvoering van extensie wordt vastgelegd in het volgende bestand. Raadpleeg dit bestand om de status van de installatie bij te houden en om eventuele fouten op te lossen.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.HpcCompute.InfiniBandDriverWindows\
```

### <a name="exit-codes"></a>Afsluit codes

De volgende tabel beschrijft de betekenis en aanbevolen actie op basis van de afsluit codes van het uitbreidings installatie proces.

| Foutcode | Betekenis | Mogelijke actie |
| :---: | --- | --- |
| 0 | Bewerking is voltooid |
| 3010 | De bewerking is voltooid. Opnieuw opstarten is vereist. |
| 100 | De bewerking wordt niet ondersteund of kan niet worden voltooid. | Mogelijke oorzaken: de Power shell-versie wordt niet ondersteund, de VM-grootte is geen met InfiniBand ingeschakelde VM, fout bij het downloaden van gegevens. Controleer de logboek bestanden om de oorzaak van de fout te achterhalen. |
| 240, 840 | Time-out van bewerking. | Probeer het opnieuw. |
| -1 | Uitzonde ring opgetreden. | Controleer de logboek bestanden om de oorzaak van de uitzonde ring te bepalen. |

### <a name="support"></a>Ondersteuning

Als u op elk moment in dit artikel meer hulp nodig hebt, kunt u contact opnemen met de Azure-experts op [MSDN Azure en stack overflow forums](https://azure.microsoft.com/support/community/). U kunt ook een ondersteunings incident opslaan via de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/). Lees de [Veelgestelde vragen over ondersteuning voor Microsoft Azure](https://azure.microsoft.com/support/faq/)voor meer informatie over het gebruik van Azure-ondersteuning.

## <a name="next-steps"></a>Volgende stappen
Zie [H-Series](../sizes-hpc.md) en [N-serie](../sizes-gpu.md) vm's voor meer informatie over de grootte van Infiniband (r).

> [!div class="nextstepaction"]
> [Meer informatie over extensies en functies voor Linux-Vm's](features-linux.md)
