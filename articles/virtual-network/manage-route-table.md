---
title: Een Azure-routetabel maken, wijzigen of verwijderen
titlesuffix: Azure Virtual Network
description: Ontdek waar u informatie vindt over verkeersroutering van virtuele netwerken en hoe u een routetabel maakt, wijzigt of verwijdert.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: kumud
ms.openlocfilehash: cdf702abb10b7330a4ca0f5478751df4bce3d7f3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783373"
---
# <a name="create-change-or-delete-a-route-table"></a>Een routeringstabel maken, wijzigen of verwijderen

Azure routeert automatisch verkeer tussen Azure-subnetten, virtuele netwerken en on-premises netwerken. Als u de standaardroutering van Azure wilt wijzigen, doet u dit door een routetabel te maken. Als u geen kennis hebt met routering in virtuele netwerken, kunt u hier meer over te weten komen [in](virtual-networks-udr-overview.md) Routering van virtueel netwerkverkeer of door een zelfstudie te [voltooien.](tutorial-create-route-table-portal.md)

## <a name="before-you-begin"></a>Voordat u begint

Als u nog geen account hebt, stelt u een Azure-account in met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Voltooi vervolgens een van deze taken voordat u de stappen in een sectie van dit artikel start:

- **Portalgebruikers:** meld u aan bij [de Azure Portal](https://portal.azure.com) met uw Azure-account.

- **PowerShell-gebruikers:** voer de opdrachten uit in [de Azure Cloud Shell](https://shell.azure.com/powershell)of voer PowerShell uit vanaf uw computer. Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Zoek op Azure Cloud Shell browsertabblad  de vervolgkeuzelijst Omgeving selecteren en kies **vervolgens PowerShell** als dit nog niet is geselecteerd.

    Als u PowerShell lokaal gebruikt, gebruikt u Azure PowerShell moduleversie 1.0.0 of hoger. Voer `Get-Module -ListAvailable Az.Network` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Voer ook `Connect-AzAccount` uit om een verbinding met Azure te maken.

- **Azure CLI-gebruikers (opdrachtregelinterface)**: voer de opdrachten uit in de [Azure Cloud Shell](https://shell.azure.com/bash)of voer de CLI uit vanaf uw computer. Gebruik Azure CLI versie 2.0.31 of hoger als u de Azure CLI lokaal gebruikt. Voer `az --version` uit om te kijken welke versie is geïnstalleerd. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren. Voer ook `az login` uit om een verbinding met Azure te maken.

Het account bij wie u zich aanmeldt of verbinding [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) maakt met Azure, moet worden toegewezen aan de rol Netwerkbijdrager of aan een aangepaste rol die de juiste acties krijgt toegewezen die worden vermeld in [Machtigingen.](#permissions) [](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="create-a-route-table"></a>Een routetabel maken

Er is een limiet voor het aantal routetabellen dat u per Azure-locatie en -abonnement kunt maken. Zie Netwerklimieten - Azure Resource Manager voor [meer informatie.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)

1. Selecteer in het menu van de [Azure-portal](https://portal.azure.com) of op de **startpagina** de optie **Een resource maken**.

1. Voer in het zoekvak *Routeringstabel* in. Wanneer **Routeringstabel** wordt weergegeven in de zoekresultaten, selecteert u dit.

1. Selecteer op de pagina **Routeringstabel** de optie **Maken**.

1. In het **dialoogvenster Routetabel** maken:

    1. Voer een **naam in** voor de routetabel.
    1. Kies uw **abonnement**.
    1. Kies een bestaande **resourcegroep of** selecteer **Nieuwe maken om** een nieuwe resourcegroep te maken.
    1. Kies een **Locatie**.
    1. Als u van plan bent om de routetabel te koppelen aan een subnet in een virtueel netwerk dat via een VPN-gateway is verbonden met uw on-premises netwerk en u uw on-premises routes niet wilt doorgeven aan de netwerkinterfaces in het subnet, stelt u Door propagatie van de **gatewayroute** van het virtuele netwerk in op **Uitgeschakeld.**

1. Selecteer **Maken om** de nieuwe routetabel te maken.

### <a name="create-route-table---commands"></a>Routetabel maken - opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az network route-table create](/cli/azure/network/route-table#az_network_route_table_create) |
| PowerShell | [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) |

## <a name="view-route-tables"></a>Routetabellen weergeven

Ga naar de [Azure-portal](https://portal.azure.com) om uw virtuele netwerk te beheren. Zoek en selecteer **Routeringstabellen**. De routetabellen die in uw abonnement aanwezig zijn, worden weergegeven.

### <a name="view-route-table---commands"></a>Routetabel weergeven - opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az network route-table list](/cli/azure/network/route-table#az_network_route_table_list) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="view-details-of-a-route-table"></a>Details van een routetabel weergeven

1. Ga naar de [Azure-portal](https://portal.azure.com) om uw virtuele netwerk te beheren. Zoek en selecteer **Routeringstabellen**.

1. Kies in de lijst routetabel de routetabel voor wie u details wilt weergeven.

1. Bekijk op de pagina routetabel onder **Instellingen** de **routes** in de routetabel of de **subnetten** waar de routetabel aan is gekoppeld.

Zie de volgende informatie voor meer informatie over algemene Azure-instellingen:

- [Activiteitenlogboek](../azure-monitor/essentials/platform-logs-overview.md)
- [Toegangsbeheer (IAM)](../role-based-access-control/overview.md)
- [Tags](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Vergrendelingen](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Automation-script](../azure-resource-manager/templates/export-template-portal.md)

### <a name="view-details-of-route-table---commands"></a>Details van routetabel weergeven - opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az network route-table show](/cli/azure/network/route-table#az_network_route_table_show) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="change-a-route-table"></a>Een routetabel wijzigen

1. Ga naar de [Azure-portal](https://portal.azure.com) om uw virtuele netwerk te beheren. Zoek en selecteer **Routeringstabellen**.

1. Kies in de lijst routetabel de routetabel die u wilt wijzigen.

De meest voorkomende wijzigingen zijn [het](#create-a-route) toevoegen van [routes,](#delete-a-route) het verwijderen van [routes,](#associate-a-route-table-to-a-subnet) het koppelen van routetabellen aan subnetten of het ontkoppelen van [routetabellen](#dissociate-a-route-table-from-a-subnet) uit subnetten.

### <a name="change-a-route-table---commands"></a>Een routetabel wijzigen - opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az network route-table update](/cli/azure/network/route-table#az_network_route_table_update) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/set-azroutetable) |

## <a name="associate-a-route-table-to-a-subnet"></a>Een routetabel aan een subnet koppelen

U kunt eventueel een routetabel aan een subnet koppelen. Een routetabel kan worden gekoppeld aan nul of meer subnetten. Omdat routetabellen niet zijn gekoppeld aan virtuele netwerken, moet u een routetabel koppelen aan elk subnet waar u de routetabel aan wilt koppelen. Azure routeert al het verkeer dat het subnet verlaat op basis van routes die u hebt gemaakt in routetabellen, [](virtual-networks-udr-overview.md#default)standaardroutes en routes die zijn doorgegeven vanuit een on-premises netwerk, als het virtuele netwerk is verbonden met een virtuele Azure-netwerkgateway (ExpressRoute of VPN). U kunt routetabellen alleen koppelen aan subnetten in virtuele netwerken die zich op dezelfde Azure-locatie en in hetzelfde abonnement bevinden als de routetabel.

1. Ga naar de [Azure-portal](https://portal.azure.com) om uw virtuele netwerk te beheren. Zoek en selecteer **Virtuele netwerken**.

1. Kies in de lijst met virtuele netwerken het virtuele netwerk met het subnet waar u een routetabel aan wilt koppelen.

1. Kies subnetten in de menubalk **van het virtuele netwerk.**

1. Selecteer het subnet waar u de routetabel aan wilt koppelen.

1. Kies **in Routetabel** de routetabel die u aan het subnet wilt koppelen.

1. Selecteer **Opslaan**.

Als uw virtuele netwerk is verbonden met een Azure VPN-gateway, koppelt u geen routetabel aan het [gatewaysubnet](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) dat een route met de bestemming *0.0.0.0/0 bevat.* Als u dit wel doet, functioneert de gateway mogelijk niet juist. Zie Routering van virtueel netwerkverkeer voor meer informatie over het gebruik van *0.0.0.0/0* in [een route.](virtual-networks-udr-overview.md#default-route)

### <a name="associate-a-route-table---commands"></a>Een routetabel koppelen - opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="dissociate-a-route-table-from-a-subnet"></a>Een routetabel ontkoppelen van een subnet

Wanneer u een routetabel ontkoppelt van een subnet, routeert Azure verkeer op basis van de [standaardroutes](virtual-networks-udr-overview.md#default).

1. Ga naar de [Azure-portal](https://portal.azure.com) om uw virtuele netwerk te beheren. Zoek en selecteer **Virtuele netwerken**.

1. Kies in de lijst met virtuele netwerken het virtuele netwerk met het subnet waar u een routetabel van wilt loskoppelen.

1. Kies subnetten in de menubalk **van het virtuele netwerk.**

1. Selecteer het subnet waar u de routetabel van wilt loskoppelen.

1. Kies **geen in Routetabel.** 

1. Selecteer **Opslaan**.

### <a name="dissociate-a-route-table---commands"></a>Een routetabel ontkoppelen - opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-route-table"></a>Een routetabel verwijderen

U kunt een routetabel die aan subnetten is gekoppeld, niet verwijderen. [Koppel een routetabel los](#dissociate-a-route-table-from-a-subnet) van alle subnetten voordat u deze probeert te verwijderen.

1. Ga naar de [Azure Portal](https://portal.azure.com) om uw routetabellen te beheren. Zoek en selecteer **Routeringstabellen**.

1. Kies in de lijst routetabel de routetabel die u wilt verwijderen.

1. Selecteer **Verwijderen** en selecteer vervolgens **Ja** in het bevestigingsvenster.

### <a name="delete-a-route-table---commands"></a>Een routetabel verwijderen - opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az network route-table delete](/cli/azure/network/route-table#az_network_route_table_delete) |
| PowerShell | [Remove-AzRouteTable](/powershell/module/az.network/remove-azroutetable) |

## <a name="create-a-route"></a>Een route maken

Er geldt een limiet voor het aantal routes per routetabel per Azure-locatie en -abonnement. Zie Netwerklimieten - Azure Resource Manager voor [meer informatie.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)

1. Ga naar de [Azure Portal](https://portal.azure.com) om uw routetabellen te beheren. Zoek en selecteer **Routeringstabellen**.

1. Kies in de lijst routetabel de routetabel waar u een route aan wilt toevoegen.

1. Kies routes toevoegen in de menubalk van de  >  **routetabel.**

1. Voer een unieke **routenaam in** voor de route in de routetabel.

1. Voer het **adres voorvoegsel** in de CIDR-notatie (Classless Inter-Domain Routing) in waar u verkeer naar wilt routeren. Het voorvoegsel kan niet in meer dan één route in de routetabel worden gedupliceerd, maar het voorvoegsel kan wel binnen een ander voorvoegsel liggen. Als u bijvoorbeeld *10.0.0.0/16* als voorvoegsel in één route hebt gedefinieerd, kunt u nog steeds een andere route definiëren met het adres *voorvoegsel 10.0.0.0/22.* Azure selecteert een route voor verkeer op basis van de langste overeenkomst voor het voorvoegsel. Zie Hoe Azure een [route selecteert voor meer informatie.](virtual-networks-udr-overview.md#how-azure-selects-a-route)

1. Kies een **volgend hoptype.** Zie Routering van virtueel netwerkverkeer voor meer informatie over 'volgende [hoptypen'.](virtual-networks-udr-overview.md)

1. Als u een volgend **hoptype van** virtueel apparaat **hebt gekozen,** voert u een IP-adres in voor **Het volgende hopadres.**

1. Selecteer **OK**.

### <a name="create-a-route---commands"></a>Een route maken - opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az network route-table route create](/cli/azure/network/route-table/route#az_network_route_table_route_create) |
| PowerShell | [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig) |

## <a name="view-routes"></a>Routes weergeven

Een routetabel bevat nul of meer routes. Zie Routering van virtueel netwerkverkeer voor meer informatie over de informatie die wordt weergegeven bij het weergeven van [routes.](virtual-networks-udr-overview.md)

1. Ga naar de [Azure Portal](https://portal.azure.com) om uw routetabellen te beheren. Zoek en selecteer **Routeringstabellen**.

1. Kies in de lijst routetabel de routetabel voor wie u routes wilt weergeven.

1. Kies routes in de menubalk van de routetabel **om** de lijst met routes weer te geven.

### <a name="view-routes---commands"></a>Routes weergeven - opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az network route-table route list](/cli/azure/network/route-table/route#az_network_route_table_route_list) |
| PowerShell | [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="view-details-of-a-route"></a>Details van een route weergeven

1. Ga naar de [Azure Portal](https://portal.azure.com) om uw routetabellen te beheren. Zoek en selecteer **Routeringstabellen**.

1. Kies in de lijst met routetabelen de routetabel met de route die u wilt weergeven.

1. Kies routes in de menubalk van de routetabel **om** de lijst met routes weer te geven.

1. Selecteer de route waar u de details van wilt bekijken.

### <a name="view-details-of-a-route---commands"></a>Details van een route weergeven - opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az network route-table route show](/cli/azure/network/route-table/route#az_network_route_table_route_show) |
| PowerShell | [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="change-a-route"></a>Een route wijzigen

1. Ga naar de [Azure Portal](https://portal.azure.com) om uw routetabellen te beheren. Zoek en selecteer **Routeringstabellen**.

1. Kies in de lijst routetabel de routetabel met de route die u wilt wijzigen.

1. Kies routes in de menubalk van de routetabel **om** de lijst met routes weer te geven.

1. Kies de route die u wilt wijzigen.

1. Wijzig bestaande instellingen in de nieuwe instellingen en selecteer vervolgens **Opslaan.**

### <a name="change-a-route---commands"></a>Een route wijzigen - opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az network route-table route update](/cli/azure/network/route-table/route#az_network_route_table_route_update) |
| PowerShell | [Set-AzRouteConfig](/powershell/module/az.network/set-azrouteconfig) |

## <a name="delete-a-route"></a>Een route verwijderen

1. Ga naar de [Azure Portal](https://portal.azure.com) om uw routetabellen te beheren. Zoek en selecteer **Routeringstabellen**.

1. Kies in de lijst routetabel de routetabel met de route die u wilt verwijderen.

1. Kies routes in de menubalk van de routetabel **om** de lijst met routes weer te geven.

1. Kies de route die u wilt verwijderen.

1. Selecteer **Verwijderen** en selecteer vervolgens **Ja** in het bevestigingsdialoogvenster.

### <a name="delete-a-route---commands"></a>Een route verwijderen - opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az network route-table route delete](/cli/azure/network/route-table/route#az_network_route_table_route_delete) |
| PowerShell | [Remove-AzRouteConfig](/powershell/module/az.network/remove-azrouteconfig) |

## <a name="view-effective-routes"></a>Effectieve routes weergeven

De effectieve routes voor elke aan VM gekoppelde netwerkinterface zijn een combinatie van routetabellen die u hebt gemaakt, de standaardroutes van Azure en alle routes die via de Border Gateway Protocol (BGP) via een virtuele Azure-netwerkgateway worden doorgegeven vanuit on-premises netwerken. Inzicht in de effectieve routes voor een netwerkinterface is handig bij het oplossen van routeringsproblemen. U kunt de effectieve routes weergeven voor elke netwerkinterface die is gekoppeld aan een VM die wordt uitgevoerd.

1. Ga naar de [Azure Portal](https://portal.azure.com) om uw VM's te beheren. Zoek en selecteer **virtuele machines**.

1. Kies in de lijst met virtuele machines de VM voor wie u effectieve routes wilt weergeven.

1. Kies Netwerken in de menubalk **van** de VM.

1. Selecteer de naam van een netwerkinterface.

1. Selecteer effectieve routes in de menubalk **van de netwerkinterface.**

1. Bekijk de lijst met effectieve routes om te zien of de juiste route bestaat voor waar u verkeer naar wilt doorsroutes. Meer informatie over de volgende hoptypen die u in deze lijst ziet, vindt u in [Routering van virtueel netwerkverkeer.](virtual-networks-udr-overview.md)

### <a name="view-effective-routes---commands"></a>Effectieve routes weergeven - opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az network nic show-effective-route-table](/cli/azure/network/nic#az_network_nic_show_effective_route_table) |
| PowerShell | [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable) |

## <a name="validate-routing-between-two-endpoints"></a>Routering tussen twee eindpunten valideren

U kunt het 'volgende hoptype' tussen een virtuele machine en het IP-adres van een andere Azure-resource, een on-premises resource of een resource op internet bepalen. Het bepalen van de routering van Azure is handig bij het oplossen van routeringsproblemen. Als u deze taak wilt uitvoeren, moet u een bestaande netwerk-watcher hebben. Als u geen bestaande network watcher hebt, maakt u er een door de stappen in Een Network Watcher [maken.](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

1. Ga naar de [Azure Portal](https://portal.azure.com) uw netwerk-watchers te beheren. Zoek en selecteer **Network Watcher**.

1. Kies volgende **hop** in de menubalk van Network Watcher.

1. In de **Network Watcher | Pagina volgende hop:**

    1. Kies uw **abonnement en** de **resourcegroep van** de bron-VM van waar u routering wilt valideren.

    1. Kies de **virtuele machine** en **de netwerkinterface** die aan de virtuele machine zijn gekoppeld.
    
    1. Voer een **bron-IP-adres in** dat is toegewezen aan de netwerkinterface van waar u routering wilt valideren.

    1. Voer een **DOEL-IP-adres** in dat u wilt valideren voor routering.

1. Selecteer **Volgende hop.**

Na een korte wachttijd geeft Azure u het type volgende hop en de id van de route die het verkeer heeft gerouteerd. Meer informatie over volgende hoptypen die worden geretourneerd in [Routering van virtueel netwerkverkeer.](virtual-networks-udr-overview.md)

### <a name="validate-routing-between-two-endpoints---commands"></a>Routering tussen twee eindpunten valideren - opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az network watcher show-next-hop](/cli/azure/network/watcher#az_network_watcher_show_next_hop) |
| PowerShell | [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop) |

## <a name="permissions"></a>Machtigingen

Als u taken wilt uitvoeren voor routetabellen en [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) routes, moet [](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) uw account worden toegewezen aan de rol Netwerkbijdrager of aan een aangepaste rol die de juiste acties krijgt toegewezen die in de volgende tabel worden vermeld:

| Bewerking                                                          |   Name                                                  |
|--------------------------------------------------------------   |   -------------------------------------------           |
| Microsoft.Network/routeTables/read                              |   Een routetabel lezen                                    |
| Microsoft.Network/routeTables/write                             |   Een routetabel maken of bijwerken                        |
| Microsoft.Network/routeTables/delete                            |   Een routetabel verwijderen                                  |
| Microsoft.Network/routeTables/join/action                       |   Een routetabel aan een subnet koppelen                   |
| Microsoft.Network/routeTables/routes/read                       |   Een route lezen                                          |
| Microsoft.Network/routeTables/routes/write                      |   Een route maken of bijwerken                              |
| Microsoft.Network/routeTables/routes/delete                     |   Een route verwijderen                                        |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action  |   De effectieve routetabel voor een netwerkinterface op te halen |
| Microsoft.Network/networkWatchers/nextHop/action                |   Haalt de volgende hop van een VM op                           |

## <a name="next-steps"></a>Volgende stappen

- Een routetabel maken met [powershell-](powershell-samples.md) of [Azure CLI-voorbeeldscripts](cli-samples.md) of Azure [Resource Manager sjablonen](template-samples.md)
- Definities voor [virtuele Azure Policy maken](./policy-reference.md) en toewijzen