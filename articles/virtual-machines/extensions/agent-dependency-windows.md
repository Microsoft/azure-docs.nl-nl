---
title: Extensie van de virtuele machine voor Azure Monitor afhankelijkheid voor Windows
description: Implementeer de Azure Monitor dependency agent op virtuele Windows-machines met behulp van de extensie van een virtuele machine.
services: virtual-machines-windows
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: magoedte
ms.openlocfilehash: 0151e7b3a30cd4b00dba75b7490563923cd5b8ff
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100580299"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-windows"></a>Extensie van de virtuele machine voor Azure Monitor afhankelijkheid voor Windows

De Azure Monitor voor VM's toewijzings functie haalt de gegevens op uit de micro soft-afhankelijkheids agent. De virtuele machine-extensie van de Azure VM-afhankelijkheids agent voor Windows wordt gepubliceerd en ondersteund door micro soft. De uitbrei ding installeert de afhankelijkheids agent op virtuele machines van Azure. Dit document bevat informatie over de ondersteunde platforms, configuraties en implementatie opties voor de extensie van de virtuele machine van de Azure VM-afhankelijkheids agent voor Windows.

## <a name="operating-system"></a>Besturingssysteem

De Azure VM dependency agent-extensie voor Windows kan worden uitgevoerd op basis van de ondersteunde besturings systemen die worden vermeld in de sectie [ondersteunde besturings systemen](../../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) van het artikel Azure monitor voor VM's-implementatie.

## <a name="extension-schema"></a>Extensieschema

De volgende JSON toont het schema voor de Azure VM dependency agent-extensie op een Azure Windows-VM.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Azure VM. Supported Windows Server versions:  2008 R2 and above (x64)."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

### <a name="property-values"></a>Eigenschaps waarden

| Name | Waarde/voor beeld |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| publisher | Micro soft. Azure. monitoring. DependencyAgent |
| type | DependencyAgentWindows |
| typeHandlerVersion | 9.5 |

## <a name="template-deployment"></a>Sjabloonimplementatie

U kunt de Azure VM-extensies implementeren met Azure Resource Manager sjablonen. U kunt het JSON-schema dat wordt beschreven in de vorige sectie van een Azure Resource Manager sjabloon gebruiken om de extensie van de Azure VM dependency agent uit te voeren tijdens het implementeren van een Azure Resource Manager sjabloon.

De JSON voor een extensie van een virtuele machine kan worden genest in de resource van de virtuele machine. Of u kunt het op het hoogste niveau van een resource manager-JSON-sjabloon plaatsen. De plaatsing van de JSON is van invloed op de waarde van de naam en het type van de resource. Zie voor meer informatie [naam en type voor onderliggende resources instellen](../../azure-resource-manager/templates/child-resource-name-type.md).

In het volgende voor beeld wordt ervan uitgegaan dat de extensie van de afhankelijkheids agent is genest in de resource van de virtuele machine. Wanneer u de extensie resource nest, wordt de JSON in het `"resources": []` object van de virtuele machine geplaatst.


```json
{
    "type": "extensions",
    "name": "DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

Wanneer u de JSON van de extensie in de hoofdmap van de sjabloon plaatst, bevat de resource naam een verwijzing naar de bovenliggende virtuele machine. Het type weerspiegelt de geneste configuratie.

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

## <a name="powershell-deployment"></a>PowerShell-implementatie

U kunt de `Set-AzVMExtension` opdracht gebruiken om de extensie van de virtuele machine van de afhankelijkheids agent te implementeren op een bestaande virtuele machine. Voordat u de opdracht uitvoert, moeten de open bare en persoonlijke configuraties worden opgeslagen in een Power shell-Hash-tabel.

```powershell

Set-AzVMExtension -ExtensionName "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ExtensionType "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>Problemen oplossen en ondersteuning

### <a name="troubleshoot"></a>Problemen oplossen

Gegevens over de status van uitbreidings implementaties kunnen worden opgehaald uit de Azure Portal en met behulp van de module Azure PowerShell. Als u de implementatie status van extensies voor een bepaalde virtuele machine wilt bekijken, voert u de volgende opdracht uit met behulp van de module Azure PowerShell:

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Uitvoer voor uitvoering van extensie wordt vastgelegd in bestanden die zijn gevonden in de volgende map:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Monitoring.DependencyAgent\
```

### <a name="support"></a>Ondersteuning

Als u op elk moment in dit artikel meer hulp nodig hebt, kunt u contact opnemen met de Azure-experts op [MSDN Azure en stack overflow forums](https://azure.microsoft.com/support/forums/). U kunt ook een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer **ondersteuning verkrijgen**. Lees de [Veelgestelde vragen over ondersteuning voor Microsoft Azure](https://azure.microsoft.com/support/faq/)voor meer informatie over het gebruik van Azure-ondersteuning.
