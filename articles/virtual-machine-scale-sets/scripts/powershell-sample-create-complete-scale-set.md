---
title: Voorbeelden van Azure PowerShell - Een volledige virtuele-machineschaalset maken
description: Met dit script maakt u een schaalset voor virtuele machines met Windows Server 2016, waar afzonderlijke resources worden geconfigureerd en gemaakt.
author: mimckitt
ms.author: mimckitt
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.subservice: powershell
ms.date: 05/29/2018
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: d37f5f624459db6bc336884987a16c60503492a8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89078500"
---
# <a name="create-a-complete-virtual-machine-scale-set-with-powershell"></a>Een volledige virtuele-machineschaalset maken met PowerShell

Met dit script maakt u een virtuele-machineschaalset waarop Windows Server 2016 wordt uitgevoerd. Afzonderlijke resources worden geconfigureerd en gemaakt. Deze maken geen gebruik van de [ingebouwde opties voor het maken van resources die hier beschikbaar zijn in New-AzVmss](powershell-sample-create-simple-scale-set.md). Nadat het script is uitgevoerd, hebt u via RDP toegang tot de virtuele machine.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/complete-scale-set/complete-scale-set.ps1 "Create a complete virtual machine scale set")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie
Gebruik de volgende opdracht om de resourcegroep, de schaalset en alle gerelateerde resources te verwijderen.

```powershell
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="script-explanation"></a>Uitleg van het script
In dit script worden de volgende opdrachten gebruikt om de implementatie te maken. Elk item in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Hiermee maakt u een subnetconfiguratie. Deze configuratie wordt gebruikt bij het maken van het virtueel netwerk. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Hiermee maakt u een virtueel netwerk. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Hiermee maakt u een openbaar IP-adres. |
| [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) | Hiermee maakt u een front-endconfiguratie voor een IP-adres voor een load balancer. |
| [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) | Hiermee maakt u een back-endconfiguratie voor een adresgroep voor een load balancer. |
| [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) | Hiermee maakt u binnenkomende NAT-regelconfiguratie voor een load balancer. |
| [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) | Hiermee maakt u een load balancer. |
| [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) | Hiermee maakt u een testconfiguratie voor een load balancer. |
| [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) | Hiermee maakt u een regelconfiguratie voor een load balancer. |
| [Set-AzLoadBalancer](/powershell/module/az.Network/Set-azLoadBalancer) | Werk de load balancer bij met de opgegeven gegevens. |
| [New-AzVmssIpConfig](/powershell/module/az.Compute/New-azVmssIpConfig) | Maak een IP-configuratie voor de VM-exemplaren van de schaalset. De VM-exemplaren zijn verbonden met de back-endpool van de load balancer, de NAT-pool en het virtueel-netwerksubnet. |
| [New-AzVmssConfig](/powershell/module/az.Compute/New-azVmssConfig) | Hiermee wordt een schaalsetconfiguratie gemaakt. Deze configuratie bevat informatie zoals het aantal VM-exemplaren dat moet worden gemaakt, de VM-SKU (grootte) en de upgradebeleidsmodus. De configuratie wordt toegevoegd aan andere cmdlets en wordt gebruikt tijdens het maken van de schaalset. |
| [Set-AzVmssStorageProfile](/powershell/module/az.Compute/Set-azVmssStorageProfile) | Definieer de installatiekopie die moet worden gebruikt voor de VM-exemplaren en voeg deze toe aan de schaalsetconfiguratie. |
| [Set-AzVmssOsProfile](/powershell/module/az.Compute/Set-azVmssStorageProfile) | Definieer de gebruikersnaam- en wachtwoordreferenties van de beheerder en het VM-naamgevingsvoorvoegsel. Voeg deze waarden toe aan de schaalsetconfiguratie. |
| [Add-AzVmssNetworkInterfaceConfiguration](/powershell/module/az.Compute/Add-azVmssNetworkInterfaceConfiguration) | Voeg de virtuele-netwerkinterface toe aan de VM-exemplaren op basis van de IP-configuratie. Voeg deze waarden toe aan de schaalsetconfiguratie. |
| [New-AzVmss](/powershell/module/az.Compute/New-azVmss) | Maak de schaalaanpassingsset op basis van de informatie in de schaalsetconfiguratie. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep en alle daarin opgenomen resources. |

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/).
