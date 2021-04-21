---
title: Inleiding tot de weergave Effectieve beveiligingsregels in Azure Network Watcher | Microsoft Docs
description: Deze pagina biedt een overzicht van de mogelijkheden Network Watcher weergave van effectieve beveiligingsregels
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: damendo
ms.openlocfilehash: 54f1ef8f135c16b841df55094327c6bc5be521be
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778602"
---
# <a name="introduction-to-effective-security-rules-view-in-azure-network-watcher"></a>Inleiding tot de weergave Effectieve beveiligingsregels in Azure Network Watcher

Netwerkbeveiligingsgroepen worden gekoppeld op subnetniveau of op NIC-niveau. Wanneer deze is gekoppeld op subnetniveau, is dit van toepassing op alle VM-exemplaren in het subnet. Effectieve beveiligingsregelsweergave retourneert alle geconfigureerde NSG's en regels die zijn gekoppeld op NIC- en subnetniveau voor een virtuele machine die inzicht biedt in de configuratie. Bovendien worden de effectieve beveiligingsregels geretourneerd voor elk van de NIC's in een VM. Met de weergave Effectieve beveiligingsregels kunt u een VM beoordelen op beveiligingsproblemen in het netwerk, zoals open poorten. U kunt ook controleren of uw netwerkbeveiligingsgroep werkt zoals verwacht, op basis van een vergelijking tussen de geconfigureerde en [de goedgekeurde beveiligingsregels.](network-watcher-nsg-auditing-powershell.md)

Een uitgebreidere use-case is in naleving en controle van de beveiliging. U kunt een prescriptieve set beveiligingsregels definiëren als een model voor beveiligingsgovernance in uw organisatie. Een periodieke nalevingscontrole kan programmatisch worden geïmplementeerd door de voorschrijvende regels te vergelijken met de effectieve regels voor elk van de VM's in uw netwerk.

In de portal worden regels weergegeven voor elke netwerkinterface en gegroepeerd op binnenkomende versus uitgaande. Dit biedt een eenvoudige weergave van de regels die zijn toegepast op een virtuele machine. Er is een downloadknop beschikbaar om eenvoudig alle beveiligingsregels te downloaden, ongeacht het tabblad in een CSV-bestand.

![weergave van beveiligingsgroep][1]

Regels kunnen worden geselecteerd en er wordt een nieuwe blade geopend met de voorvoegsels netwerkbeveiligingsgroep en bron- en doel. Op deze blade kunt u rechtstreeks naar de resource netwerkbeveiligingsgroep navigeren.

![inzoomen][2]

### <a name="next-steps"></a>Volgende stappen

U kunt ook de functie *Effectieve beveiligingsgroepen gebruiken* via andere methoden die hieronder worden vermeld:
* [REST API](/rest/api/virtualnetwork/NetworkInterfaces/ListEffectiveNetworkSecurityGroups)
* [PowerShell](/powershell/module/az.network/get-azeffectivenetworksecuritygroup)
* [Azure-CLI](/cli/azure/network/nic#az_network_nic_list_effective_nsg)

Meer informatie over het controleren van de instellingen van uw netwerkbeveiligingsgroep gaat u naar [Instellingen voor netwerkbeveiligingsgroep controleren met PowerShell](network-watcher-nsg-auditing-powershell.md)

[1]: ./media/network-watcher-security-group-view-overview/securitygroupview.png
[2]: ./media/network-watcher-security-group-view-overview/figure1.png