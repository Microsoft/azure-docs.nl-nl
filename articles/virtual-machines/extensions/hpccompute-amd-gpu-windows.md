---
title: Uitbrei ding van AMD GPU-stuur programma-Azure Windows-Vm's
description: Microsoft Azure-extensie voor het installeren van AMD GPU-Stuur Programma's op virtuele machines uit de NVv4-serie waarop Windows wordt uitgevoerd.
services: virtual-machines-windows
author: vikancha-MSFT
manager: jkabat
ms.topic: article
ms.service: virtual-machines
ms.subservice: hpc
ms.collection: windows
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/10/2020
ms.author: vikancha
ms.openlocfilehash: 42dac9edc91d7cb935e8c20398c4d31343b358e1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102559678"
---
# <a name="amd-gpu-driver-extension-for-windows"></a>Uitbrei ding van AMD GPU-stuur programma voor Windows

Dit artikel bevat een overzicht van de VM-extensie voor het implementeren van AMD GPU-Stuur Programma's op virtuele machines uit de Windows [NVv4-serie](../nvv4-series.md) . Wanneer u AMD-Stuur Programma's installeert met behulp van deze uitbrei ding, accepteert en gaat u akkoord met de voor waarden van de [licentie overeenkomst voor AMD End-User](https://amd.com/radeonsoftwarems). Tijdens het installatie proces kan de virtuele machine opnieuw worden opgestart om de installatie van het stuur programma te volt ooien.

Instructies voor de hand matige installatie van de Stuur Programma's en de huidige ondersteunde versies zijn [hier](../windows/n-series-amd-driver-setup.md)beschikbaar.

## <a name="prerequisites"></a>Vereisten

### <a name="operating-system"></a>Besturingssysteem

Deze extensie ondersteunt de volgende OSs:

| Distributie | Versie |
|---|---|
| Windows 10 EMS | Build 1903 |
| Windows 10 | Build 1809 |
| Windows Server 2016 | Kern |
| Windows Server 2019 | Kern |

### <a name="internet-connectivity"></a>Internetconnectiviteit

De uitbrei ding van de Microsoft Azure voor AMD GPU-Stuur Programma's vereist dat de doel-VM is verbonden met internet en toegang heeft.

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
    "type": "AmdGpuDriverWindows",
    "typeHandlerVersion": "1.0",
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
| type | AmdGpuDriverWindows | tekenreeks |
| typeHandlerVersion | 1.0 | int |


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
    "type": "AmdGpuDriverWindows",
    "typeHandlerVersion": "1.0",
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
    -ExtensionName "AmdGpuDriverWindows" `
    -ExtensionType "AmdGpuDriverWindows" `
    -TypeHandlerVersion 1.0 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name AmdGpuDriverWindows `
  --publisher Microsoft.HpcCompute `
  --version 1.0 `
  --settings '{ `
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

Uitvoer voor uitvoering van extensie wordt vastgelegd in de volgende map:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.HpcCompute.AmdGpuDriverMicrosoft\
```

### <a name="error-codes"></a>Foutcodes

| Foutcode | Betekenis | Mogelijke actie |
| :---: | --- | --- |
| 0 | Bewerking is voltooid |
| 1 | De bewerking is voltooid. Opnieuw opstarten is vereist. |
| 100 | De bewerking wordt niet ondersteund of kan niet worden voltooid. | Mogelijke oorzaken: de Power shell-versie wordt niet ondersteund, de VM-grootte is geen VM van de N-serie, fout bij het downloaden van gegevens. Controleer de logboek bestanden om de oorzaak van de fout te achterhalen. |
| 240, 840 | Time-out van bewerking. | Probeer het opnieuw. |
| -1 | Uitzonde ring opgetreden. | Controleer de logboek bestanden om de oorzaak van de uitzonde ring te bepalen. |
| -5x | De bewerking is onderbroken omdat opnieuw opstarten in behandeling is. | Start de VM opnieuw op. De installatie wordt voortgezet nadat de computer opnieuw is opgestart. Uninstall moet hand matig worden aangeroepen. |


### <a name="support"></a>Ondersteuning

Als u op elk moment in dit artikel meer hulp nodig hebt, kunt u contact opnemen met de Azure-experts op [MSDN Azure en stack overflow forums](https://azure.microsoft.com/support/community/). U kunt ook een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer ondersteuning verkrijgen. Lees de [Veelgestelde vragen over ondersteuning voor Microsoft Azure](https://azure.microsoft.com/support/faq/)voor meer informatie over het gebruik van Azure-ondersteuning.

## <a name="next-steps"></a>Volgende stappen
Zie [virtuele machines en functies voor Windows](features-windows.md)voor meer informatie over uitbrei dingen.

Zie [grootten van virtuele machines](../sizes-gpu.md)die zijn geoptimaliseerd voor GPU voor meer informatie over vm's van de N-serie.
