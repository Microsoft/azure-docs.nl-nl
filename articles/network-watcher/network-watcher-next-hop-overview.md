---
title: Inleiding tot volgende hop in azure Network Watcher | Microsoft Docs
description: Dit artikel bevat een overzicht van de Network Watcher volgende hop-mogelijkheid.
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: febf7bca-e0b7-41d5-838f-a5a40ebc5aac
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 33c22b5b68b6677e8cf271dc185007316ec44500
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "76844055"
---
# <a name="use-next-hop-to-diagnose-virtual-machine-routing-problems"></a>Volgende hop gebruiken om problemen met de route ring van virtuele machines op te sporen

Verkeer van een virtuele machine (VM) wordt verzonden naar een bestemming op basis van de efficiënte routes die zijn gekoppeld aan een netwerk interface (NIC). Met de volgende hop wordt het type van de volgende hop en het IP-adres van een pakket van een specifieke virtuele machine en NIC opgehaald. Als u de volgende hop weet, kunt u bepalen of verkeer wordt omgeleid naar de beoogde bestemming, of dat het verkeer nergens wordt verzonden. Een onjuiste configuratie van routes, waarbij verkeer wordt omgeleid naar een on-premises locatie of een virtueel apparaat, kan leiden tot verbindings problemen. De volgende hop retourneert ook de route tabel die is gekoppeld aan de volgende hop. Als de route wordt gedefinieerd als een door de gebruiker gedefinieerde route, wordt die route geretourneerd. Anders retourneert de volgende hop een **systeem route**.

![overzicht van volgende hop](./media/network-watcher-next-hop-overview/figure1.png)

De volgende hops die kunnen worden geretourneerd door de volgende hop-mogelijkheid zijn als volgt:

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VirtualNetwork
* VirtualNetworkPeering
* VirtualNetworkServiceEndpoint 
* MicrosoftEdge
* Geen

Zie [route ring Overview](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)voor meer informatie over elk type volgende hop.

## <a name="next-steps"></a>Volgende stappen

Zie problemen met VM-netwerk routering vaststellen met behulp van de [Azure Portal](diagnose-vm-network-routing-problem.md), [Power shell](diagnose-vm-network-routing-problem-powershell.md)of de [Azure cli](diagnose-vm-network-routing-problem-cli.md)voor meer informatie over het gebruik van de volgende hop om problemen met VM-netwerk routering te diagnosticeren.
