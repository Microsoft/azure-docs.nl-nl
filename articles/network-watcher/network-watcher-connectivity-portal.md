---
title: Problemen met verbindingen oplossen-Azure Portal
titleSuffix: Azure Network Watcher
description: Meer informatie over het gebruik van de functie verbinding oplossen van Azure Network Watcher met behulp van de Azure Portal.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: damendo
ms.openlocfilehash: f33c5f0fdf69737df0d8bd83499ded1e0e0f4f88
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97898110"
---
# <a name="troubleshoot-connections-with-azure-network-watcher-using-the-azure-portal"></a>Verbindingen met Azure Network Watcher met de Azure Portal oplossen

> [!div class="op_single_selector"]
> - [Portal](network-watcher-connectivity-portal.md)
> - [PowerShell](network-watcher-connectivity-powershell.md)
> - [Azure-CLI](network-watcher-connectivity-cli.md)
> - [Azure REST-API](network-watcher-connectivity-rest.md)

Meer informatie over het gebruik van verbindings problemen oplossen om te controleren of een directe TCP-verbinding van een virtuele machine naar een bepaald eind punt tot stand kan worden gebracht.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u de volgende resources hebt:

* Een exemplaar van Network Watcher in de regio waarvoor u problemen met een verbinding wilt oplossen.
* Virtuele machines voor het oplossen van verbindingen met.

> [!IMPORTANT]
> Verbindings problemen oplossen vereist dat de VM-extensie is geïnstalleerd op de VM die u wilt oplossen `AzureNetworkWatcherExtension` . Voor het installeren van de uitbrei ding op een Windows-VM gaat u naar [azure Network Watcher agent-extensie voor virtuele machines voor Windows](../virtual-machines/extensions/network-watcher-windows.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) en voor Linux VM gaat u naar de [Azure Network Watcher agent-extensie voor virtuele machines voor Linux](../virtual-machines/extensions/network-watcher-linux.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). De uitbrei ding is niet vereist voor het eind punt van de bestemming.

## <a name="check-connectivity-to-a-virtual-machine"></a>Controleer de verbinding met een virtuele machine

In dit voor beeld wordt de verbinding met een virtuele doel machine via poort 80 gecontroleerd.

Ga naar uw Network Watcher en klik op **verbindings problemen oplossen**. Selecteer de virtuele machine waarvoor u de connectiviteit wilt controleren. Kies in de sectie **doel** de optie **Selecteer een virtuele machine** en kies de juiste virtuele machine en poort om te testen.

Wanneer u op **controleren** hebt geklikt, wordt de verbinding tussen de virtuele machines op de opgegeven poort gecontroleerd. In het voor beeld is de doel-VM onbereikbaar. er wordt een lijst met hops weer gegeven.

![De connectiviteits resultaten voor een virtuele machine controleren][1]

## <a name="check-remote-endpoint-connectivity"></a>Externe eindpuntconnectiviteit controleren

Als u de connectiviteit en latentie voor een extern eind punt wilt controleren, kiest u de keuze rondje **hand matig opgeven** in de sectie **doel** , voert u de URL en de poort in en klikt u op **controleren**.  Dit wordt gebruikt voor externe eind punten, zoals websites en opslag eindpunten.

![De connectiviteits resultaten controleren voor een website][2]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het automatiseren van pakket opnames met waarschuwingen voor virtuele machines door het weer geven van [een waarschuwing gegenereerde pakket opname maken](network-watcher-alert-triggered-packet-capture.md)

Controleren of bepaalde verkeer is toegestaan in of buiten uw virtuele machine door te kijken naar controle van de [IP-stroom](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-connectivity-portal/figure1.png
[2]: ./media/network-watcher-connectivity-portal/figure2.png