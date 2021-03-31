---
title: Inleiding tot diagnostische gegevens over netwerk configuratie in azure Network Watcher | Microsoft Docs
description: Op deze pagina vindt u een overzicht van de diagnostische gegevens voor de netwerk configuratie van Network Watcher
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2020
ms.author: damendo
ms.openlocfilehash: 5feef79a08789ad381b0c93cb938abd9effdfcc8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102502005"
---
# <a name="introduction-to-network-configuration-diagnostics-in-azure-network-watcher"></a>Inleiding tot diagnostische gegevens over netwerk configuratie in azure Network Watcher

Het hulp programma voor diagnostische netwerk configuratie helpt klanten te begrijpen welke verkeers stromen worden toegestaan of geweigerd in uw Azure Virtual Network en gedetailleerde informatie voor fout opsporing. Het kan u helpen om te weten of uw NSG-regels correct zijn geconfigureerd. 

## <a name="pre-requisites"></a>Vereisten
Voor het gebruik van diagnostische gegevens over netwerk configuratie moet Network Watcher zijn ingeschakeld in uw abonnement. Zie [een Azure Network Watcher-exemplaar maken](./network-watcher-create.md) om in te scha kelen.

## <a name="background"></a>Achtergrond

- Uw resources in azure zijn verbonden via virtuele netwerken (VNETs) en subnetten. De beveiliging van deze VNets en subnetten kan worden beheerd met behulp van een netwerk beveiligings groep (NSG).
- Een NSG bevat een lijst met beveiligings regels waarmee netwerk verkeer wordt toegestaan of geweigerd voor bronnen waarmee de verbinding is gemaakt. Nsg's kunnen worden gekoppeld aan subnetten, afzonderlijke Vm's of afzonderlijke netwerk interfaces (Nic's) die zijn gekoppeld aan Vm's. 
- Alle verkeers stromen in uw netwerk worden geëvalueerd aan de hand van de regels in de desbetreffende NSG.
- Regels worden geëvalueerd op basis van prioriteits nummer van laag naar hoog 

## <a name="how-does-network-configuration-diagnostic-work"></a>Hoe werkt de diagnostische netwerk configuratie? 

Voor een bepaalde stroom voert het NCD-hulp programma een simulatie van de stroom uit en retourneert of de stroom zou worden toegestaan (of geweigerd) en gedetailleerde informatie over de regels voor het toestaan/weigeren van de stroom.  Klanten moeten gegevens opgeven over een stroom zoals de bron, het doel, het Protocol, enzovoort. Het hulp programma retourneert of verkeer is toegestaan of geweigerd, de NSG-regels die voor de opgegeven stroom zijn geëvalueerd en de evaluatie resultaten voor elke regel.

## <a name="next-steps"></a>Volgende stappen

Diagnostische netwerk configuratie gebruiken via andere interfaces
 - [REST API](/rest/api/network-watcher/networkwatchers/getnetworkconfigurationdiagnostic)
 - [PowerShell](/powershell/module/az.network/invoke-aznetworkwatchernetworkconfigurationdiagnostic)
 - [Azure-CLI](/cli/azure/network/watcher#az_network_watcher_run_configuration_diagnostic)