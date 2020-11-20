---
title: VM-extensie van Azure Network Watcher Agent voor Windows installeren
description: Implementeer de Network Watcher-agent op virtuele Windows-machines met behulp van een extensie van een virtuele machine.
services: virtual-machines-windows
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: azure-resource-manager
ms.assetid: 27e46af7-2150-45e8-b084-ba33de8c5e3f
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: mimckitt
ms.openlocfilehash: 813fc7db2a028590797bef1c54fff20b231c3d7d
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965898"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-windows"></a>Extensie van de virtuele machine van Network Watcher agent voor Windows

## <a name="overview"></a>Overzicht

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) is een bewakings-, diagnose-en analyse service voor netwerk prestaties waarmee Azure-netwerken kunnen worden bewaakt. De extensie van de virtuele machine van Network Watcher agent is een vereiste voor het vastleggen van netwerk verkeer op aanvraag en andere geavanceerde functionaliteit op virtuele machines van Azure.


In dit document vindt u informatie over de ondersteunde platforms en implementatie opties voor de virtuele machine-extensie Network Watcher agent voor Windows. Installatie van de agent verstoort de virtuele machine niet of moet deze opnieuw worden opgestart. U kunt de uitbrei ding implementeren op virtuele machines die u implementeert. Als de virtuele machine wordt geïmplementeerd door een Azure-service, raadpleegt u de documentatie voor de service om te bepalen of de installatie van uitbrei dingen in de virtuele machine is toegestaan.

## <a name="prerequisites"></a>Vereisten

### <a name="operating-system"></a>Besturingssysteem

De Network Watcher agent-extensie voor Windows kan worden uitgevoerd op versies van Windows Server 2008 R2, 2012, 2012 R2, 2016 en 2019. Nano server wordt op dit moment niet ondersteund.

### <a name="internet-connectivity"></a>Internetconnectiviteit

Voor sommige functies van de Network Watcher-agent moet de virtuele doel machine zijn verbonden met internet. Zonder de mogelijkheid om uitgaande verbindingen tot stand te brengen, kan de Network Watcher-agent geen pakket opnames uploaden naar uw opslag account. Raadpleeg de [documentatie van Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md)voor meer informatie.

## <a name="extension-schema"></a>Extensieschema

De volgende JSON toont het schema voor de uitbrei ding van de Network Watcher agent. De uitbrei ding vereist geen door de gebruiker opgegeven instellingen en is afhankelijk van de standaard configuratie.

```json
{
    "type": "extensions",
    "name": "Microsoft.Azure.NetworkWatcher",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.NetworkWatcher",
        "type": "NetworkWatcherAgentWindows",
        "typeHandlerVersion": "1.4",
        "autoUpgradeMinorVersion": true
    }
}
```

### <a name="property-values"></a>Eigenschaps waarden

| Name | Waarde/voor beeld |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Micro soft. Azure. NetworkWatcher |
| type | NetworkWatcherAgentWindows |
| typeHandlerVersion | 1.4 |


## <a name="template-deployment"></a>Sjabloonimplementatie

U kunt Azure VM-extensies implementeren met Azure Resource Manager sjablonen. U kunt het JSON-schema in de vorige sectie van een Azure Resource Manager sjabloon gebruiken om de Network Watcher agent-extensie uit te voeren tijdens de implementatie van een Azure Resource Manager-sjabloon.

## <a name="powershell-deployment"></a>PowerShell-implementatie

Gebruik de `Set-AzVMExtension` opdracht om de extensie van de virtuele machine van Network Watcher agent te implementeren op een bestaande virtuele machine:

```powershell
Set-AzVMExtension `
  -ResourceGroupName "myResourceGroup1" `
  -Location "WestUS" `
  -VMName "myVM1" `
  -Name "networkWatcherAgent" `
  -Publisher "Microsoft.Azure.NetworkWatcher" `
  -Type "NetworkWatcherAgentWindows" `
  -TypeHandlerVersion "1.4"
```

## <a name="troubleshooting-and-support"></a>Probleem oplossing en ondersteuning

### <a name="troubleshooting"></a>Problemen oplossen

U kunt gegevens ophalen over de status van uitbreidings implementaties van de Azure Portal en Power shell. Als u de implementatie status van extensies voor een bepaalde virtuele machine wilt bekijken, voert u de volgende opdracht uit met behulp van de module Azure PowerShell:

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup1 -VMName myVM1 -Name networkWatcherAgent
```

Uitvoer voor uitvoering van extensie wordt vastgelegd in bestanden die zijn gevonden in de volgende map:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentWindows\
```

### <a name="support"></a>Ondersteuning

Als u op elk gewenst moment meer hulp nodig hebt, raadpleegt u de documentatie voor de Network Watcher-gebruikers handleiding of neemt u contact op met de Azure-experts op [MSDN Azure en stack overflow forums](https://azure.microsoft.com/support/forums/). U kunt ook een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer ondersteuning verkrijgen. Lees de [Veelgestelde vragen over ondersteuning voor Microsoft Azure](https://azure.microsoft.com/support/faq/)voor meer informatie over het gebruik van Azure-ondersteuning.
