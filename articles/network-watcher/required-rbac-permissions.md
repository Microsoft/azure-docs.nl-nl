---
title: Azure RBAC-machtigingen die zijn vereist voor het gebruik van mogelijkheden
titleSuffix: Azure Network Watcher
description: Meer informatie over op rollen gebaseerde toegangs beheer machtigingen van Azure zijn vereist om met Network Watcher mogelijkheden te werken.
services: network-watcher
documentationcenter: ''
author: damendo
ms.service: network-watcher
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: damendo
ms.openlocfilehash: 9a5bd6dff05fe39d62e5bf5f897bc61936686bb7
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94887226"
---
# <a name="azure-role-based-access-control-permissions-required-to-use-network-watcher-capabilities"></a>Op rollen gebaseerde toegangs beheer machtigingen voor Azure die zijn vereist voor het gebruik van Network Watcher mogelijkheden

Met op rollen gebaseerd toegangs beheer (Azure RBAC) van Azure kunt u alleen de specifieke acties toewijzen aan leden van uw organisatie die ze nodig hebben om hun toegewezen verantwoordelijkheden te volt ooien. Als u Network Watcher mogelijkheden wilt gebruiken, moet het account waarmee u zich aanmeldt bij Azure met, worden toegewezen aan de ingebouwde rollen [eigenaar](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#owner), [bijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#contributor)of [netwerk bijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#network-contributor) of worden toegewezen aan een [aangepaste rol](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) waaraan de acties worden toegewezen die worden vermeld voor elke Network Watcher functionaliteit in de volgende secties. Zie [Wat is Network Watcher?](network-watcher-monitoring-overview.md)voor meer informatie over de mogelijkheden van Network Watcher.

## <a name="network-watcher"></a>Network Watcher

| Bewerking                                                              | Beschrijving                                                           |
| ---------                                                           | -------------                                                  |
| Micro soft. Network/networkWatchers/lezen                              | Een netwerk-Watcher ophalen                                          |
| Micro soft. Network/networkWatchers/schrijven                             | Een netwerk-Watcher maken of bijwerken                             |
| Micro soft. Network/networkWatchers/verwijderen                            | Een netwerk-Watcher verwijderen                                       |

## <a name="nsg-flow-logs"></a>NSG-stroomlogboeken

| Bewerking                                                              | Beschrijving                                                           |
| ---------                                                           | -------------                                                  |
| Micro soft. Network/networkWatchers/configureFlowLog/Action           | Een stroom logboek configureren                                           |
| Micro soft. Network/networkWatchers/queryFlowLogStatus/Action         | Query status voor een stroom logboek                                    |

## <a name="connection-troubleshoot"></a>Problemen met de verbinding oplossen

| Bewerking                                                              | Beschrijving                                                           |
| ---------                                                           | -------------                                                  |
| Micro soft. Network/networkWatchers/connectivityCheck/Action          | Problemen met testen van verbinding starten
| Micro soft. Network/networkWatchers/queryTroubleshootResult/Action    | Query resultaten van de test verbinding oplossen                |
| Micro soft. netwerk/networkWatchers/problemen oplossen/actie               | Een test voor verbinding oplossen uitvoeren                             |

## <a name="connection-monitor"></a>Verbindings monitor

| Bewerking                                                              | Beschrijving                                                           |
| ---------                                                           | -------------                                                  |
| Micro soft. Network/networkWatchers/connectionMonitors/start/Action   | Verbindings monitor starten                                     |
| Micro soft. Network/networkWatchers/connectionMonitors/stop/Action    | Verbindings monitor stoppen                                      |
| Micro soft. Network/networkWatchers/connectionMonitors/query/actie   | Query's uitvoeren op een verbindings monitor                                     |
| Micro soft. Network/networkWatchers/connectionMonitors/lezen           | Een verbindings monitor ophalen                                       |
| Micro soft. Network/networkWatchers/connectionMonitors/schrijven          | Een verbindingsmonitor maken                                    |
| Micro soft. Network/networkWatchers/connectionMonitors/verwijderen         | Een verbindings monitor verwijderen                                    |

## <a name="packet-capture"></a>Pakketopname

| Bewerking                                                              | Beschrijving                                                           |
| ---------                                                           | -------------                                                  |
| Micro soft. Network/networkWatchers/packetCaptures/queryStatus/Action | De status van een pakket opname opvragen                           |
| Micro soft. Network/networkWatchers/packetCaptures/stop/Action        | Een pakket opname stoppen                                          |
| Micro soft. Network/networkWatchers/packetCaptures/lezen               | Pakket opname ophalen                                           |
| Micro soft. Network/networkWatchers/packetCaptures/schrijven              | Een pakket opname maken                                        |
| Micro soft. Network/networkWatchers/packetCaptures/verwijderen             | Een pakket opname verwijderen                                        |

## <a name="ip-flow-verify"></a>IP-stroom controleren

| Bewerking                                                              | Beschrijving                                                           |
| ---------                                                           | -------------                                                  |
| Micro soft. Network/networkWatchers/ipFlowVerify/Action               | Een IP-stroom controleren                                              |

## <a name="next-hop"></a>Volgende hop

| Bewerking                                                              | Beschrijving                                                           |
| ---------                                                           | -------------                                                  |
| Micro soft. Network/networkWatchers/nextHop/actie                    | De volgende hop van een virtuele machine ophalen                                     |

## <a name="network-security-group-view"></a>Netwerkbeveiligingsgroep weergeven

| Bewerking                                                              | Beschrijving                                                           |
| ---------                                                           | -------------                                                  |
| Micro soft. Network/networkWatchers/securityGroupView/Action          | Beveiligings groepen weer geven                                           |

## <a name="topology"></a>Topologie

| Bewerking                                                              | Beschrijving                                                           |
| ---------                                                           | -------------                                                  |
| Micro soft. Network/networkWatchers/Topology/actie                   | Topologie ophalen                                                   |
| Micro soft. Network/networkWatchers/Topology/lezen                     | Hetzelfde als hierboven                                                  |

## <a name="reachability-report"></a>Bereik baarheids rapport

| Bewerking                                                              | Beschrijving                                                           |
| ---------                                                           | -------------                                                  |
| Micro soft. Network/networkWatchers/azureReachabilityReport/Action    | Een Azure-bereik baarheids rapport ophalen                               |


## <a name="additional-actions"></a>Aanvullende acties

Voor Network Watcher mogelijkheden zijn ook de volgende acties vereist:

| Actie (s)                                                           | Beschrijving                                                    |
| ---------                                                           | -------------                                                  |
| Micro soft. Authorization/ \* /Read                                     | Wordt gebruikt voor het ophalen van Azure-Roltoewijzingen en beleids definities          |
| Micro soft. resources/abonnementen/resourceGroups/lezen               | Gebruikt voor het inventariseren van alle resource groepen in een abonnement    |
| Micro soft. Storage/Storage accounts/lezen                              | Wordt gebruikt om de eigenschappen van het opgegeven opslag account op te halen   |
| Micro soft. Storage/Storage accounts/listServiceSas/Action, </br> Micro soft. Storage/Storage accounts/listAccountSas/Action, <br> Micro soft. Storage/Storage accounts/Listkeys ophalen/Action| Gebruikt voor het ophalen van Shared Access signatures (SAS) om [beveiligde toegang tot het opslag account](https://docs.microsoft.com/azure/storage/common/storage-sas-overview) in te scha kelen en te schrijven naar het opslag account |
| Micro soft. Compute/informatie/lezen, </br> Micro soft. Compute/informatie/schrijven| Wordt gebruikt om u aan te melden bij de VM, een pakket vast te leggen en dit te uploaden naar een opslag account|
| Micro soft. Compute/informatie/uitbrei dingen/lezen </br> Micro soft. Compute/informatie/Extensions/write| Wordt gebruikt om te controleren of Network Watcher extensie aanwezig is en zo nodig installeren |
| Micro soft. Compute/virtualMachineScaleSets/lezen, </br> Micro soft. Compute/virtualMachineScaleSets/schrijven| Wordt gebruikt om toegang te krijgen tot de schaal sets van virtuele machines, pakketten vastleggen en uploaden naar het opslag account|
| Micro soft. Compute/virtualMachineScaleSets/uitbrei dingen/lezen, </br> Micro soft. Compute/virtualMachineScaleSets/Extensions/write| Wordt gebruikt om te controleren of Network Watcher extensie aanwezig is en zo nodig installeren |
| Micro soft. Insights/alertRules/*                                     | Gebruikt voor het instellen van metrische waarschuwingen                                     |
| Micro soft. support/*                                                 | Wordt gebruikt om ondersteunings tickets te maken en bij te werken van Network Watcher |
