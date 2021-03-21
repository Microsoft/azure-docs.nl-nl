---
title: Een Azure Firewall-testomgeving maken
description: In dit voorbeeldscript wordt een firewall en een testnetwerkomgeving gemaakt. Het netwerk heeft één VNet met drie subnetten.
services: virtual-network
author: vhorne
ms.service: firewall
ms.devlang: powershell
ms.topic: sample
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 4158bc07373a2d0aa6fb6ceaf2dce62b50bb6bd7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94658363"
---
# <a name="create-an-azure-firewall-test-environment"></a>Een Azure Firewall-testomgeving maken

In dit voorbeeldscript wordt een firewall en een testnetwerkomgeving gemaakt. Het netwerk heeft één VNet met drie subnetten: een *AzureFirewallSubnet*, een *ServersSubnet* en een *JumpboxSubnet*. Het ServersSubnet en het JumpboxSubnet hebben elk één Windows Server met twee kernen.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

De firewall bevindt zich in het AzureFirewallSubnet en is geconfigureerd met een toepassingsregelverzameling met één regel die toegang geeft tot `www.microsoft.com`.

Er wordt een gebruikergedefinieerde route gemaakt die het netwerkverkeer vanuit het ServersSubnet door de firewall leidt, waar de firewallregels worden toegepast.

U kunt het script uitvoeren vanuit de Azure [Cloud Shell](https://shell.azure.com/powershell) of vanuit een lokale installatie van PowerShell. 

Als u PowerShell lokaal uitvoert, is Azure PowerShell vereist voor dit script. Voer `Get-Module -ListAvailable Az` uit om na te gaan welke versie er is geïnstalleerd. 

Als u moet upgraden, kunt u `PowerShellGet` gebruiken, wat in Windows 10 en Windows Server 2016 is ingebouwd.

> [!NOTE]
>Voor andere Windows-versies moet u `PowerShellGet` installeren voordat u het kunt gebruiken. U kunt `Get-Module -Name PowerShellGet -ListAvailable | Select-Object -Property Name,Version,Path` uitvoeren om te bepalen of het op uw systeem is geïnstalleerd. Als de uitvoer leeg is, moet u het nieuwste [Windows Management Framework](https://www.microsoft.com/download/details.aspx?id=54616) installeren.

Zie [Azure PowerShell installeren](/powershell/azure/install-Az-ps) voor meer informatie

Een bestaande Azure PowerShell-installatie die met het webplatforminstallatieprogramma is uitgevoerd, conflicteert met de PowerShellGet-installatie en moet worden verwijderd.

Vergeet niet dat als u PowerShell lokaal uitvoert, u ook `Connect-AzAccount` moet uitvoeren om verbinding te kunnen maken met Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript


[!code-azurepowershell-interactive[main](../../../powershell_scripts/firewall/create-fw-test.ps1  "Create a firewall test environment")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie 

Voer de volgende opdracht uit om de resourcegroep, VM en alle gerelateerde resources te verwijderen:

```powershell
Remove-AzResourceGroup -Name AzfwSampleScriptEastUS -Force
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt voor het maken van een resourcegroep, een virtueel netwerk en netwerkbeveiligingsgroepen. Elke opdracht in de volgende tabel is een koppeling naar opdrachtspecifieke documentatie:

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Hiermee maakt u een subnetconfiguratie |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Hiermee maakt u een virtueel Azure-netwerk en front-end-subnet. |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | Hiermee maakt u beveiligingsregels voor verbindingen die worden toegewezen aan een netwerkbeveiligingsgroep. |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) |Hiermee maakt u NSG-regels die bepaalde poorten tot specifieke subnetten blokkeren of toestaan. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Hiermee koppelt u NSG's aan subnetten. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Hiermee maakt u een openbaar IP-adres voor toegang tot de VM via internet. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Hiermee maakt u virtuele netwerkinterfaces en koppelt u ze aan de front-end- en back-end-subnetten van het virtuele netwerk. |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Hiermee maakt u een VM-configuratie. Deze configuratie bevat informatie zoals de naam, het besturingssysteem en de beheerdersreferenties van de virtuele machine. De configuratie wordt gebruikt tijdens het maken van de virtuele machine. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Hiermee maakt u een virtuele machine. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep en alle daarin opgenomen resources. |
|[New-AzFirewall](/powershell/module/az.network/new-azfirewall)| Hiermee wordt een nieuwe Azure Firewall gemaakt.|
|[Get-AzFirewall](/powershell/module/az.network/get-azfirewall)|Hiermee wordt een Azure Firewall-object opgehaald.|
|[New-AzFirewallApplicationRule](/powershell/module/az.network/new-azfirewallapplicationrule)|Hiermee wordt een regel voor een Azure Firewall-toepassing gemaakt.|
|[Set-AzFirewall](/powershell/module/az.network/set-azfirewall)|Hiermee worden wijzigingen aan het Azure Firewall-object doorgevoerd.|

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](/powershell/azure/) voor meer informatie over Azure PowerShell.