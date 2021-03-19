---
title: Toepassingspoort openen in load balancer in Azure
description: Azure PowerShell-voorbeeldscript - Een poort in de Azure-load balancer openen voor een Service Fabric-toepassing.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 05/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 4be9944a53aaf0c697d55ff567dee7f2fbb3ed85
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87038083"
---
# <a name="open-an-application-port-in-the-azure-load-balancer"></a>Een toepassingspoort openen in de Azure-load balancer

Een Service Fabric-toepassing die in Azure wordt uitgevoerd, bevindt zich achter de Azure-load balancer. Met dit voorbeeldscript wordt een poort geopend in een Azure-load balancer, zodat een Service Fabric-toepassing kan communiceren met externe clients. Pas de parameters zo nodig aan. Als uw cluster deel uitmaakt van een netwerkbeveiligingsgroep, voegt u ook [een regel voor inkomende netwerkbeveiligingsgroepen toe](service-fabric-powershell-add-nsg-rule.md) om ingaand verkeer toe te staan.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Installeer indien nodig de Service Fabric PowerShell-module met de [Service Fabric-SDK](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/service-fabric/open-port-in-load-balancer/open-port-in-load-balancer.ps1 "Open a port in the load balancer")]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar opdracht-specifieke documentatie.

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Haalt een Azure-resource op.  |
| [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer) | Haalt de Azure-load balancer op. |
| [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) | Voegt een testconfiguratie toe aan een load balancer.|
| [Get-AzLoadBalancerProbeConfig](/powershell/module/az.network/get-azloadbalancerprobeconfig) | Haalt een testconfiguratie op voor een load balancer. |
| [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) | Voegt een regelconfiguratie toe aan een load balancer. |
| [Set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer) | Stelt de doelstatus voor een load balancer in. |

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/).

Meer PowerShell-voorbeelden voor Azure Service Fabric vindt u in de [voorbeelden van Azure PowerShell](../service-fabric-powershell-samples.md).
