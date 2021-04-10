---
title: Uitbrei ding van NVIDIA GPU-stuur programma-Azure Linux-Vm's
description: Microsoft Azure-extensie voor het installeren van NVIDIA GPU-Stuur Programma's op virtuele machines met een N-serie en Linux.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.subservice: hpc
ms.collection: linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/21/2021
ms.author: amverma
ms.openlocfilehash: fa2b82f3246fd87830010f572ba23aa2df075053
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102566223"
---
# <a name="nvidia-gpu-driver-extension-for-linux"></a>Uitbrei ding van NVIDIA GPU-stuur programma voor Linux

## <a name="overview"></a>Overzicht

Met deze uitbrei ding worden NVIDIA GPU-Stuur Programma's geïnstalleerd op Vm's uit de Linux N-serie. Afhankelijk van de VM-familie installeert de uitbrei ding CUDA of GRID-Stuur Programma's. Wanneer u NVIDIA-Stuur Programma's installeert met behulp van deze extensie, accepteert u de voor waarden van de [nvidia End-User License Agreement](https://go.microsoft.com/fwlink/?linkid=874330)en gaat u ermee akkoord. Tijdens het installatie proces kan de virtuele machine opnieuw worden opgestart om de installatie van het stuur programma te volt ooien.

Instructies voor de hand matige installatie van de Stuur Programma's en de huidige ondersteunde versies zijn [hier](../linux/n-series-driver-setup.md)beschikbaar.
Er is ook een uitbrei ding beschikbaar om NVIDIA GPU-Stuur Programma's te installeren op Vm's uit de [Windows N-serie](hpccompute-gpu-windows.md).

## <a name="prerequisites"></a>Vereisten

### <a name="operating-system"></a>Besturingssysteem

Deze extensie ondersteunt de volgende OS distributies, afhankelijk van de ondersteuning van Stuur Programma's voor een specifieke versie van het besturings systeem.

| Distributie | Versie |
|---|---|
| Linux: Ubuntu | 16,04 LTS, 18,04 LTS |
| Linux: Red Hat Enterprise Linux | 7,3, 7,4, 7,5, 7,6, 7,7, 7,8 |
| Linux: CentOS | 7,3, 7,4, 7,5, 7,6, 7,7, 7,8 |

### <a name="internet-connectivity"></a>Internetconnectiviteit

De uitbrei ding van de Microsoft Azure voor NVIDIA GPU-Stuur Programma's vereist dat de doel-VM is verbonden met internet en toegang heeft.

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
    "type": "NvidiaGpuDriverLinux",
    "typeHandlerVersion": "1.3",
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
| type | NvidiaGpuDriverLinux | tekenreeks |
| typeHandlerVersion | 1.3 | int |

### <a name="settings"></a>Instellingen

Alle instellingen zijn optioneel. Standaard wordt de kernel niet bijgewerkt als deze niet is vereist voor de installatie van het stuur programma, installeert u het meest recente ondersteunde stuur programma en de CUDA Toolkit (indien van toepassing).

| Naam | Beschrijving | Standaardwaarde | Geldige waarden | Gegevenstype |
| ---- | ---- | ---- | ---- | ---- |
| updateOS | De kernel bijwerken, zelfs als deze niet vereist is voor installatie van Stuur Programma's | onjuist | de waarde True, false | booleaans |
| driverVersion | NV: raster versie van het stuur programma<br> NC/ND: CUDA Toolkit-versie. De meest recente Stuur Programma's voor de gekozen CUDA worden automatisch geïnstalleerd. | meest recente | [Lijst](https://github.com/Azure/azhpc-extensions/blob/master/NvidiaGPU/resources.json) met ondersteunde versies van Stuur Programma's | tekenreeks |
| installCUDA | Installeer de CUDA Toolkit. Alleen relevant voor virtuele machines van de NC/ND-serie. | true | de waarde True, false | booleaans |


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
    "type": "NvidiaGpuDriverLinux",
    "typeHandlerVersion": "1.3",
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
    -ExtensionName "NvidiaGpuDriverLinux" `
    -ExtensionType "NvidiaGpuDriverLinux" `
    -TypeHandlerVersion 1.3 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

In het volgende voor beeld worden de bovenstaande Azure Resource Manager-en Power shell-voor beelden Spie gels.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name NvidiaGpuDriverLinux \
  --publisher Microsoft.HpcCompute \
  --version 1.3 
```

In het volgende voor beeld worden ook twee optionele aangepaste instellingen toegevoegd als voor beeld voor een niet-standaard installatie van een stuur programma. Met name wordt de kernel van het besturings systeem bijgewerkt naar de meest recente versie en wordt een specifiek stuur programma van de CUDA Toolkit geïnstalleerd. Let op: de instellingen zijn optioneel en standaard. Houd er rekening mee dat het bijwerken van de kernel de installatie tijden van de extensie kan verhogen. Ook is het kiezen van een specifieke (oudere) CUDA tolkit-versie mogelijk niet altijd compatibel met nieuwere kernels.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name NvidiaGpuDriverLinux \
  --publisher Microsoft.HpcCompute \
  --version 1.3 \
  --settings '{ \
    "updateOS": true, \
    "driverVersion": "10.0.130" \
  }'
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

Uitvoer voor uitvoering van extensie wordt vastgelegd in het volgende bestand. Raadpleeg dit bestand om de status van (een wille keurige, langdurige) installatie bij te houden en om eventuele fouten op te lossen.

```bash
/var/log/azure/nvidia-vmext-status
```

### <a name="exit-codes"></a>Afsluit codes

| Afsluitcode | Betekenis | Mogelijke actie |
| :---: | --- | --- |
| 0 | Bewerking is voltooid |
| 1 | Onjuist gebruik van uitbrei ding | Uitvoer logboek van uitvoering controleren |
| 10 | Linux-integratie Services voor Hyper-V en Azure zijn niet beschikbaar of geïnstalleerd | Controleer de uitvoer van lspci |
| 11 | NVIDIA GPU niet gevonden op deze VM-grootte | Een [ondersteunde VM-grootte en besturings systeem](../linux/n-series-driver-setup.md) gebruiken |
| 12 | Aanbieding met installatie kopieën wordt niet ondersteund |
| 13 | VM-grootte wordt niet ondersteund | Een N-serie-VM gebruiken om te implementeren |
| 14 | De bewerking is mislukt | Uitvoer logboek van uitvoering controleren |


### <a name="support"></a>Ondersteuning

Als u op elk moment in dit artikel meer hulp nodig hebt, kunt u contact opnemen met de Azure-experts op [MSDN Azure en stack overflow forums](https://azure.microsoft.com/support/community/). U kunt ook een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer ondersteuning verkrijgen. Lees de [Veelgestelde vragen over ondersteuning voor Microsoft Azure](https://azure.microsoft.com/support/faq/)voor meer informatie over het gebruik van Azure-ondersteuning.

## <a name="next-steps"></a>Volgende stappen
Zie [virtuele machines en functies voor Linux](features-linux.md)voor meer informatie over uitbrei dingen.

Zie [grootten van virtuele machines](../sizes-gpu.md)die zijn geoptimaliseerd voor GPU voor meer informatie over vm's van de N-serie.
