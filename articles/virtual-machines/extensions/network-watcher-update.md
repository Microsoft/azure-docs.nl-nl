---
title: De Network Watcher-extensie bijwerken naar de nieuwste versie
description: Meer informatie over hoe u de Azure Network Watcher-extensie bijwerkt naar de nieuwste versie.
services: virtual-machines-windows
documentationcenter: ''
author: damendo
manager: balar
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.workload: infrastructure-services
ms.date: 09/23/2020
ms.author: damendo
ms.openlocfilehash: c427a206e0422e66cb526a29a462d8b6bdf6818e
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965932"
---
# <a name="update-the-network-watcher-extension-to-the-latest-version"></a>De Network Watcher-extensie bijwerken naar de nieuwste versie

## <a name="overview"></a>Overzicht

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) is een netwerk prestatie bewaking, diagnose en analyse service waarmee Azure-netwerken worden bewaakt. De extensie van de Network Watcher agent virtuele machine (VM) is een vereiste voor het vastleggen van netwerk verkeer op aanvraag en het gebruik van andere geavanceerde functionaliteit op Azure-Vm's. De uitbrei ding Network Watcher wordt gebruikt door functies als verbindings monitor, verbindings monitor (preview), verbindings problemen oplossen en pakket opname.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u de extensie Network Watcher hebt geïnstalleerd in uw VM.

## <a name="latest-version"></a>Nieuwste versie

De meest recente versie van de uitbrei ding van de Network Watcher is momenteel `1.4.1654.1` .

## <a name="update-your-extension"></a>Uw extensie bijwerken

Als u uw uitbrei ding wilt bijwerken, moet u de extensie versie weten.

### <a name="check-your-extension-version"></a>Controleer de versie van de extensie

U kunt de extensie versie controleren met behulp van de Azure Portal, de Azure CLI of Power shell.

#### <a name="usetheazureportal"></a>De Azure Portal gebruiken

1. Ga naar het deel venster **uitbrei dingen** van de virtuele machine in de Azure Portal.
1. Selecteer de **AzureNetworkWatcher** -extensie om het deel venster met details weer te geven.
1. Zoek het versie nummer in het veld **versie** .  

#### <a name="use-the-azure-cli"></a>Azure CLI gebruiken

Voer de volgende opdracht uit vanaf een Azure CLI-prompt:

```azurecli
az vm get-instance-view --resource-group  "SampleRG" --name "Sample-VM"
```
Zoek **' AzureNetworkWatcherExtension '** in de uitvoer op en Identificeer het versie nummer in het veld *' TypeHandlerVersion '* in de uitvoer.  Opmerking: informatie over de uitbrei ding wordt meerdere keren weer gegeven in de JSON-uitvoer. Bekijk het blok ' uitbrei dingen ' en controleer het volledige versie nummer van de uitbrei ding. 

U ziet iets zoals hieronder: ![ Azure cli-scherm afbeelding](./media/network-watcher/azure-cli-screenshot.png)

#### <a name="usepowershell"></a>Power shell gebruiken

Voer de volgende opdrachten uit vanuit een Power shell-prompt:

```powershell
Get-AzVM -ResourceGroupName "SampleRG" -Name "Sample-VM" -Status
```
Zoek naar de Azure Network Watcher-extensie in de uitvoer en zoek het versie nummer op uit het veld *' TypeHandlerVersion '* in de uitvoer.   

U ziet iets zoals hieronder: ![ Power shell-scherm afbeelding](./media/network-watcher/powershell-screenshot.png)

### <a name="update-your-extension"></a>Uw extensie bijwerken

Als uw versie eerder is dan `1.4.1654.1` , de huidige meest recente versie, moet u de uitbrei ding bijwerken met behulp van een van de volgende opties.

#### <a name="option-1-use-powershell"></a>Optie 1: Power shell gebruiken

Voer de volgende opdrachten uit:

```powershell
#Linux command
Set-AzVMExtension -ResourceGroupName "myResourceGroup1" -Location "WestUS" -VMName "myVM1" -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentLinux"

#Windows command
Set-AzVMExtension -ResourceGroupName "myResourceGroup1" -Location "WestUS" -VMName "myVM1" -Name "NetworkWatcherAgentWindows" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentWindows" -ForceRerun "True"

```

Als dat niet werkt. Verwijder de uitbrei ding en installeer deze opnieuw met behulp van de onderstaande stappen. Hiermee wordt automatisch de meest recente versie toegevoegd.

De uitbrei ding verwijderen 

```powershell
#Same command for Linux and Windows
Remove-AzVMExtension -ResourceGroupName "SampleRG" -VMName "Sample-VM" -Name "AzureNetworkWatcherExtension"
``` 

De extensie opnieuw installeren

```powershell
#Linux command
Set-AzVMExtension -ResourceGroupName "SampleRG" -Location "centralus" -VMName "Sample-VM" -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentLinux" -typeHandlerVersion "1.4"

#Windows command
Set-AzVMExtension -ResourceGroupName "SampleRG" -Location "centralus" -VMName "Sample-VM" -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentWindows" -typeHandlerVersion "1.4"
```

#### <a name="option-2-use-the-azure-cli"></a>Optie 2: de Azure CLI gebruiken

Een upgrade afdwingen.

```azurecli
#Linux command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher" --force-update

#Windows command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" --force-update
```

Als dat niet werkt, verwijdert u de uitbrei ding en installeert u deze opnieuw en volgt u deze stappen om automatisch de meest recente versie toe te voegen.

Verwijder de extensie.

```azurecli
#Same for Linux and Windows
az vm extension delete --resource-group "myResourceGroup1" --vm-name "myVM1" -n "AzureNetworkWatcherExtension"

```

Installeer de extensie opnieuw.

```azurecli
#Linux command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher"

#Windows command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher"

```

#### <a name="option-3-reboot-your-vms"></a>Optie 3: uw Vm's opnieuw opstarten

Als u de automatische upgrade hebt ingesteld op True voor de extensie Network Watcher, start u de VM-installatie opnieuw op naar de nieuwste extensie.

## <a name="support"></a>Ondersteuning

Als u op elk moment meer hulp nodig hebt, raadpleegt u de documentatie voor de Network Watcher-extensie voor [Linux](./network-watcher-linux.md) of [Windows](./network-watcher-windows.md). U kunt ook contact opnemen met de Azure-experts op [MSDN Azure en stack overflow forums](https://azure.microsoft.com/support/forums/). U kunt ook een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/)en selecteer **ondersteuning verkrijgen**. Lees de [Veelgestelde vragen over ondersteuning voor Microsoft Azure](https://azure.microsoft.com/support/faq/)voor meer informatie over het gebruik van Azure-ondersteuning.
