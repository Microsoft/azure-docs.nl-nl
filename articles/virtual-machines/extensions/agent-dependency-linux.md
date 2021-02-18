---
title: Extensie van de virtuele machine voor Azure Monitor afhankelijkheid voor Linux
description: Implementeer de Azure Monitor dependency agent op een virtuele Linux-machine met behulp van een extensie van de virtuele machine.
services: virtual-machines-linux
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: magoedte
ms.openlocfilehash: 9e75b159f994a869a2bbd4498332a1e160bf5462
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100580304"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-linux"></a>Extensie van de virtuele machine voor Azure Monitor afhankelijkheid voor Linux

De Azure Monitor voor VM's toewijzings functie haalt de gegevens op uit de micro soft-afhankelijkheids agent. De Azure VM dependency agent-extensie voor virtuele machines voor Linux wordt gepubliceerd en ondersteund door micro soft. De uitbrei ding installeert de afhankelijkheids agent op virtuele machines van Azure. Dit document bevat een overzicht van de ondersteunde platforms, configuraties en implementatie opties voor de extensie van de virtuele machine van de Azure VM-afhankelijkheids agent voor Linux.

## <a name="prerequisites"></a>Vereisten

### <a name="operating-system"></a>Besturingssysteem

De Azure VM dependency agent-extensie voor Linux kan worden uitgevoerd op basis van de ondersteunde besturings systemen die worden vermeld in de sectie [ondersteunde besturings systemen](../../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) van het artikel Azure monitor voor VM's-implementatie.

## <a name="extension-schema"></a>Extensieschema

De volgende JSON toont het schema voor de Azure VM dependency agent-extensie op een virtuele Azure Linux-machine. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Linux Azure VM."
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
        "type": "DependencyAgentLinux",
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
| type | DependencyAgentLinux |
| typeHandlerVersion | 9.5 |

## <a name="template-deployment"></a>Sjabloonimplementatie

U kunt Azure VM-extensies implementeren met Azure Resource Manager sjablonen. U kunt het JSON-schema dat wordt beschreven in de vorige sectie van een Azure Resource Manager sjabloon gebruiken om de extensie van de Azure VM dependency agent uit te voeren tijdens het implementeren van een Azure Resource Manager sjabloon.

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
        "type": "DependencyAgentLinux",
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
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

## <a name="azure-cli-deployment"></a>Implementatie van Azure CLI

U kunt de Azure CLI gebruiken voor het implementeren van de VM-extensie van de afhankelijkheids agent naar een bestaande virtuele machine.  

```azurecli

az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 
```

## <a name="troubleshoot-and-support"></a>Problemen oplossen en ondersteuning

### <a name="troubleshoot"></a>Problemen oplossen

Gegevens over de status van uitbreidings implementaties kunnen worden opgehaald uit de Azure Portal en met behulp van de Azure CLI. Als u de implementatie status van extensies voor een bepaalde virtuele machine wilt bekijken, voert u de volgende opdracht uit met behulp van de Azure CLI:

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Uitvoer voor uitvoering van extensie wordt vastgelegd in het volgende bestand:

```
/opt/microsoft/dependency-agent/log/install.log 
```

### <a name="support"></a>Ondersteuning

Als u op elk gewenst moment meer hulp nodig hebt, neemt u contact op met de Azure-experts op [MSDN Azure en stack overflow forums](https://azure.microsoft.com/support/forums/). U kunt ook een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer **ondersteuning verkrijgen**. Lees de [Veelgestelde vragen over ondersteuning voor Microsoft Azure](https://azure.microsoft.com/support/faq/)voor meer informatie over het gebruik van Azure-ondersteuning.
