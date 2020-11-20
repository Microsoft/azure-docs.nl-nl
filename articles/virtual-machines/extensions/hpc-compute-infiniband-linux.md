---
title: InfiniBand-stuur programma-uitbrei ding-Azure Linux-Vm's
description: Microsoft Azure-extensie voor het installeren van InfiniBand-Stuur Programma's op de H-en N-serie Compute-Vm's waarop Linux wordt uitgevoerd.
services: virtual-machines-linux
documentationcenter: ''
author: vermagit
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/20/2020
ms.author: amverma
ms.openlocfilehash: 2a5aa8983e6cbb0745e05ce275edeadeccb60736
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966034"
---
# <a name="infiniband-driver-extension-for-linux"></a>InfiniBand-stuur programma-uitbrei ding voor Linux

Met deze uitbrei ding worden InfiniBand OFED-Stuur Programma's geïnstalleerd op InfiniBand en SR-IOV-ingeschakeld (r-grootten) [H-Series](../sizes-hpc.md) en Vm's met [N-serie](../sizes-gpu.md) waarop Linux wordt uitgevoerd. Afhankelijk van de VM-serie installeert de extensie de juiste Stuur Programma's voor de Connect-X-NIC.

Instructies voor de hand matige installatie van de OFED-Stuur Programma's zijn [hier](../workloads/hpc/enable-infiniband.md#manual-installation)beschikbaar.

Er is ook een uitbrei ding beschikbaar voor het installeren van InfiniBand-Stuur Programma's voor [Windows-vm's](hpc-compute-infiniband-windows.md).

## <a name="prerequisites"></a>Vereisten

### <a name="operating-system"></a>Besturingssysteem

Deze extensie ondersteunt de volgende OS distributies, afhankelijk van de ondersteuning van Stuur Programma's voor een specifieke versie van het besturings systeem.

| Distributie | Versie |
|---|---|
| Linux: Ubuntu | 16,04 LTS, 18,04 LTS, 20,04 LTS |
| Linux: CentOS | 7,4, 7,5, 7,6, 7,7, 8,1, 8, 2 |
| Linux: Red Hat Enterprise Linux | 7,4, 7,5, 7,6, 7,7, 8,1, 8, 2 |

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
    "type": "InfiniBandDriverLinux",
    "typeHandlerVersion": "1.1",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="properties"></a>Eigenschappen

| Name | Waarde/voor beeld | Gegevenstype |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | datum |
| publisher | Micro soft. HpcCompute | tekenreeks |
| type | InfiniBandDriverLinux | tekenreeks |
| typeHandlerVersion | 1.1 | int |



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
    "type": "InfiniBandDriverLinux",
    "typeHandlerVersion": "1.1",
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
    -ExtensionName "InfiniBandDriverLinux" `
    -ExtensionType "InfiniBandDriverLinux" `
    -TypeHandlerVersion 1.1 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name InfiniBandDriverLinux \
  --publisher Microsoft.HpcCompute \
  --version 1.1 
```

### <a name="add-extension-to-a-virtual-machine-scale-set"></a>Extensie toevoegen aan een Schaalset voor virtuele machines

In het volgende voor beeld wordt de meest recente versie 1,1 InfiniBandDriverLinux-uitbrei ding geïnstalleerd op alle virtuele machines met RDMA-functionaliteit in een bestaande schaalset van de VM met de naam *myVMSS* geïmplementeerd in de resource groep met de naam *myResourceGroup*:

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.1"
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

```bash
/var/log/azure/ib-vmext-status
```

### <a name="exit-codes"></a>Afsluit codes

De volgende tabel beschrijft de betekenis en aanbevolen actie op basis van de afsluit codes van het uitbreidings installatie proces.

| Afsluitcode | Betekenis | Mogelijke actie |
| :---: | --- | --- |
| 0 | Bewerking is voltooid |
| 1 | Onjuist gebruik van uitbrei ding | Uitvoer logboek van uitvoering controleren |
| 10 | Linux-integratie Services voor Hyper-V en Azure zijn niet beschikbaar of geïnstalleerd | Controleer de uitvoer van lspci |
| 11 | Mellanox InfiniBand niet gevonden op deze VM-grootte | Een [ondersteunde VM-grootte en besturings systeem](../sizes-hpc.md) gebruiken |
| 12 | Aanbieding met installatie kopieën wordt niet ondersteund |
| 13 | VM-grootte wordt niet ondersteund | Een [n-Series en](../sizes-hpc.md) [n-](../sizes-gpu.md)Series-VM implementeren die is ingeschakeld met InfiniBand (r) |
| 14 | De bewerking is mislukt | Uitvoer logboek van uitvoering controleren |


### <a name="support"></a>Ondersteuning

Als u op elk moment in dit artikel meer hulp nodig hebt, kunt u contact opnemen met de Azure-experts op [MSDN Azure en stack overflow forums](https://azure.microsoft.com/support/community/). U kunt ook een ondersteunings incident opslaan via de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/). Lees de [Veelgestelde vragen over ondersteuning voor Microsoft Azure](https://azure.microsoft.com/support/faq/)voor meer informatie over het gebruik van Azure-ondersteuning.

## <a name="next-steps"></a>Volgende stappen
Zie [H-Series](../sizes-hpc.md) en [N-serie](../sizes-gpu.md) vm's voor meer informatie over de grootte van Infiniband (r).

> [!div class="nextstepaction"]
> [Meer informatie over extensies en functies voor Linux-Vm's](features-linux.md)