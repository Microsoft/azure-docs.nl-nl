---
title: Een Azure-route tabel maken, wijzigen of verwijderen
titlesuffix: Azure Virtual Network
description: Informatie over het vinden van informatie over route ring van virtueel netwerk verkeer en het maken, wijzigen of verwijderen van een route tabel.
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
ms.openlocfilehash: 642a9a9f798492d85ee2a9784a1fe5ad4f854d58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100574117"
---
# <a name="create-change-or-delete-a-route-table"></a>Een routeringstabel maken, wijzigen of verwijderen

Azure routeert automatisch verkeer tussen Azure-subnetten, virtuele netwerken en on-premises netwerken. Als u de standaard routering van Azure wilt wijzigen, doet u dat door een route tabel te maken. Als u niet bekend bent met route ring in virtuele netwerken, kunt u meer informatie hierover vinden in [virtueel netwerk verkeer routeren](virtual-networks-udr-overview.md) of door een [zelf studie](tutorial-create-route-table-portal.md)te volt ooien.

## <a name="before-you-begin"></a>Voordat u begint

Als u er nog geen hebt, stelt u een Azure-account in met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Voer vervolgens een van deze taken uit voordat u begint met de stappen in een van de volgende secties:

- **Portal gebruikers**: Meld u aan bij de [Azure Portal](https://portal.azure.com) met uw Azure-account.

- **Power shell-gebruikers**: Voer de opdrachten uit in de [Azure Cloud shell](https://shell.azure.com/powershell)of voer Power shell uit vanaf uw computer. Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Zoek de vervolg keuzelijst **omgeving selecteren** op het tabblad Azure Cloud shell browser en kies vervolgens **Power shell** als deze optie nog niet is geselecteerd.

    Als u Power shell lokaal uitvoert, gebruikt u Azure PowerShell module versie 1.0.0 of hoger. Voer `Get-Module -ListAvailable Az.Network` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Voer ook uit `Connect-AzAccount` om een verbinding te maken met Azure.

- **Azure-opdracht regel interface (CLI)-gebruikers**: Voer de opdrachten uit in de [Azure Cloud shell](https://shell.azure.com/bash)of voer de CLI uit vanaf uw computer. Gebruik Azure CLI versie 2.0.31 of hoger als u de Azure CLI lokaal uitvoert. Voer `az --version` uit om te kijken welke versie is geïnstalleerd. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren. Voer ook uit `az login` om een verbinding te maken met Azure.

Het account waarmee u zich aanmeldt of verbinding maakt met Azure, moet worden toegewezen aan de [rol netwerk bijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) of aan een [aangepaste rol](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) waaraan de juiste acties zijn toegewezen die worden vermeld in [machtigingen](#permissions).

## <a name="create-a-route-table"></a>Een routetabel maken

Er is een limiet voor het aantal route tabellen dat u per Azure-locatie en-abonnement kunt maken. Zie [netwerk limieten-Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)voor meer informatie.

1. Selecteer in het menu van de [Azure-portal](https://portal.azure.com) of op de **startpagina** de optie **Een resource maken**.

1. Voer in het zoekvak *Routeringstabel* in. Wanneer **Routeringstabel** wordt weergegeven in de zoekresultaten, selecteert u dit.

1. Selecteer op de pagina **Routeringstabel** de optie **Maken**.

1. In het dialoog venster **route tabel maken** :

    1. Voer een **naam** in voor de route tabel.
    1. Kies uw **abonnement**.
    1. Kies een bestaande **resource groep** of selecteer **nieuwe maken** om een nieuwe resource groep te maken.
    1. Kies een **Locatie**.
    1. Als u van plan bent om de route tabel te koppelen aan een subnet in een virtueel netwerk dat is verbonden met uw on-premises netwerk via een VPN-gateway en u uw on-premises routes niet wilt door geven aan de netwerk interfaces in het subnet, stelt u de **doorgifte van Gateway routes van het virtuele netwerk** in op **uitgeschakeld**.

1. Selecteer **maken** om uw nieuwe route tabel te maken.

### <a name="create-route-table---commands"></a>Route tabel maken-opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az network route-table create](/cli/azure/network/route-table#az-network-route-table-create) |
| PowerShell | [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) |

## <a name="view-route-tables"></a>Route tabellen weer geven

Ga naar de [Azure-portal](https://portal.azure.com) om uw virtuele netwerk te beheren. Zoek en selecteer **Routeringstabellen**. De route tabellen die in uw abonnement aanwezig zijn, worden weer gegeven.

### <a name="view-route-table---commands"></a>Route tabel weer geven-opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [AZ Network Route-Table List](/cli/azure/network/route-table#az-network-route-table-list) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="view-details-of-a-route-table"></a>Details van een route tabel weer geven

1. Ga naar de [Azure-portal](https://portal.azure.com) om uw virtuele netwerk te beheren. Zoek en selecteer **Routeringstabellen**.

1. Kies in de lijst route tabel de route tabel waarvan u de details wilt weer geven.

1. Bekijk op de pagina route tabel onder **instellingen** de **routes** in de route tabel of de **subnetten** waaraan de route tabel is gekoppeld.

Voor meer informatie over algemene Azure-instellingen raadpleegt u de volgende informatie:

- [Activiteitenlogboek](../azure-monitor/essentials/platform-logs-overview.md)
- [Toegangsbeheer (IAM)](../role-based-access-control/overview.md)
- [Tags](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Vergrendelingen](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Automation-script](../azure-resource-manager/templates/export-template-portal.md)

### <a name="view-details-of-route-table---commands"></a>Details van route tabel-opdrachten weer geven

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [AZ Network Route-Table show](/cli/azure/network/route-table#az-network-route-table-show) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="change-a-route-table"></a>Een route tabel wijzigen

1. Ga naar de [Azure-portal](https://portal.azure.com) om uw virtuele netwerk te beheren. Zoek en selecteer **Routeringstabellen**.

1. Kies in de lijst route tabel de route tabel die u wilt wijzigen.

De meest voorkomende wijzigingen zijn het [toevoegen](#create-a-route) van routes, het [verwijderen](#delete-a-route) van routes, het [koppelen](#associate-a-route-table-to-a-subnet) van route tabellen aan subnetten of het [loskoppelen](#dissociate-a-route-table-from-a-subnet) van route tabellen uit subnetten.

### <a name="change-a-route-table---commands"></a>Een route tabel wijzigen-opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [AZ Network Route-Table Update](/cli/azure/network/route-table#az-network-route-table-update) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/set-azroutetable) |

## <a name="associate-a-route-table-to-a-subnet"></a>Een routetabel aan een subnet koppelen

U kunt eventueel een route tabel koppelen aan een subnet. Een route tabel kan worden gekoppeld aan nul of meer subnetten. Omdat route tabellen niet aan virtuele netwerken zijn gekoppeld, moet u een route tabel koppelen aan elk subnet waaraan de route tabel is gekoppeld. Azure stuurt al het verkeer dat het subnet verlaat op basis van routes die u hebt gemaakt in route tabellen, [standaard routes](virtual-networks-udr-overview.md#default)en routes die worden door gegeven vanuit een on-premises netwerk, als het virtuele netwerk is verbonden met een virtuele Azure-netwerk gateway (EXPRESSROUTE of VPN). U kunt routetabellen alleen koppelen aan subnetten in virtuele netwerken die zich op dezelfde Azure-locatie en in hetzelfde abonnement bevinden als de routetabel.

1. Ga naar de [Azure-portal](https://portal.azure.com) om uw virtuele netwerk te beheren. Zoek en selecteer **Virtuele netwerken**.

1. Kies in de lijst virtueel netwerk het virtuele netwerk dat het subnet bevat waaraan u een route tabel wilt koppelen.

1. Kies **subnetten** in de menu balk van het virtuele netwerk.

1. Selecteer het subnet waaraan u de route tabel wilt koppelen.

1. Kies in **route tabel** de route tabel die u wilt koppelen aan het subnet.

1. Selecteer **Opslaan**.

Als uw virtuele netwerk is verbonden met een Azure VPN-gateway, koppelt u een route tabel niet aan het [Gateway-subnet](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) dat een route bevat met een bestemming van *0.0.0.0/0*. Als u dit wel doet, functioneert de gateway mogelijk niet juist. Zie [virtueel netwerk verkeer routeren](virtual-networks-udr-overview.md#default-route)voor meer informatie over het gebruik van *0.0.0.0/0* in een route.

### <a name="associate-a-route-table---commands"></a>Een route tabel koppelen-opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="dissociate-a-route-table-from-a-subnet"></a>Een routetabel ontkoppelen van een subnet

Wanneer u een route tabel loskoppelt van een subnet, routeert Azure verkeer op basis van de [standaard routes](virtual-networks-udr-overview.md#default).

1. Ga naar de [Azure-portal](https://portal.azure.com) om uw virtuele netwerk te beheren. Zoek en selecteer **Virtuele netwerken**.

1. Kies in de lijst virtueel netwerk het virtuele netwerk dat het subnet bevat dat u wilt loskoppelen van een route tabel.

1. Kies **subnetten** in de menu balk van het virtuele netwerk.

1. Selecteer het subnet dat u wilt loskoppelen van de route tabel.

1. Kies in **route tabel** de optie **geen**.

1. Selecteer **Opslaan**.

### <a name="dissociate-a-route-table---commands"></a>Een route tabel ontkoppelen-opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-route-table"></a>Een routetabel verwijderen

U kunt een route tabel die is gekoppeld aan een subnet, niet verwijderen. [Koppel een routetabel los](#dissociate-a-route-table-from-a-subnet) van alle subnetten voordat u deze probeert te verwijderen.

1. Ga naar de [Azure Portal](https://portal.azure.com) om uw route tabellen te beheren. Zoek en selecteer **Routeringstabellen**.

1. Kies in de lijst route tabel de route tabel die u wilt verwijderen.

1. Selecteer **verwijderen** en selecteer vervolgens **Ja** in het bevestigings venster.

### <a name="delete-a-route-table---commands"></a>Een route tabel verwijderen-opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [AZ Network Route-Table Delete](/cli/azure/network/route-table#az-network-route-table-delete) |
| PowerShell | [Remove-AzRouteTable](/powershell/module/az.network/remove-azroutetable) |

## <a name="create-a-route"></a>Een route maken

Er is een limiet voor het aantal routes per route tabel dat per Azure-locatie en-abonnement kan worden gemaakt. Zie [netwerk limieten-Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)voor meer informatie.

1. Ga naar de [Azure Portal](https://portal.azure.com) om uw route tabellen te beheren. Zoek en selecteer **Routeringstabellen**.

1. Kies in de lijst route tabel de route tabel waaraan u een route wilt toevoegen.

1. Kies **routes** toevoegen in de menu balk van de route tabel  >  .

1. Voer een unieke **route naam** in voor de route in de route tabel.

1. Geef het **adres voorvoegsel** op in de CIDR-notatie (Classless Inter-Domain route ring) waarnaar u verkeer wilt door sturen. Het voor voegsel kan niet worden gedupliceerd in meer dan één route in de route tabel, maar het voor voegsel kan zich in een ander voor voegsel bevindt. Als u bijvoorbeeld *10.0.0.0/16* als voor voegsel in één route hebt gedefinieerd, kunt u nog steeds een andere route definiëren met het adres voorvoegsel *10.0.0.0/22* . Azure selecteert een route voor verkeer op basis van het voor voegsel van de voor voegsels. Zie [hoe Azure een route selecteert](virtual-networks-udr-overview.md#how-azure-selects-a-route)voor meer informatie.

1. Kies een **type van de volgende hop**. Zie [virtueel netwerk verkeer routeren](virtual-networks-udr-overview.md)voor meer informatie over de volgende typen hops.

1. Als u het type van de **volgende hop** hebt gekozen voor het **virtuele apparaat**, voert u een IP-adres in voor het adres van de **volgende hop**.

1. Selecteer **OK**.

### <a name="create-a-route---commands"></a>Een route-opdrachten maken

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az network route-table route create](/cli/azure/network/route-table/route#az-network-route-table-route-create) |
| PowerShell | [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig) |

## <a name="view-routes"></a>Routes weer geven

Een route tabel bevat nul of meer routes. Zie [virtueel netwerk verkeer routeren](virtual-networks-udr-overview.md)voor meer informatie over de informatie die wordt weer gegeven wanneer u routes bekijkt.

1. Ga naar de [Azure Portal](https://portal.azure.com) om uw route tabellen te beheren. Zoek en selecteer **Routeringstabellen**.

1. Kies in de lijst route tabel de route tabel waarvoor u routes wilt weer geven.

1. Kies **routes** in de menu balk van de route tabel om de lijst met routes weer te geven.

### <a name="view-routes---commands"></a>Routes weer geven-opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [AZ Network Route-Table Route List](/cli/azure/network/route-table/route#az-network-route-table-route-list) |
| PowerShell | [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="view-details-of-a-route"></a>Details van een route weergeven

1. Ga naar de [Azure Portal](https://portal.azure.com) om uw route tabellen te beheren. Zoek en selecteer **Routeringstabellen**.

1. Kies in de lijst route tabel de route tabel met de route waarvan u de details wilt weer geven.

1. Kies **routes** in de menu balk van de route tabel om de lijst met routes weer te geven.

1. Selecteer de route waarvan u de details wilt weer geven.

### <a name="view-details-of-a-route---commands"></a>Details van een route-opdrachten weer geven

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [AZ Network Route-Table Route show](/cli/azure/network/route-table/route#az-network-route-table-route-show) |
| PowerShell | [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="change-a-route"></a>Een route wijzigen

1. Ga naar de [Azure Portal](https://portal.azure.com) om uw route tabellen te beheren. Zoek en selecteer **Routeringstabellen**.

1. Kies in de lijst route tabel de route tabel met de route die u wilt wijzigen.

1. Kies **routes** in de menu balk van de route tabel om de lijst met routes weer te geven.

1. Kies de route die u wilt wijzigen.

1. Wijzig de bestaande instellingen in de nieuwe instellingen en selecteer vervolgens **Opslaan**.

### <a name="change-a-route---commands"></a>Een route-opdrachten wijzigen

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [AZ Network Route-Table Route update](/cli/azure/network/route-table/route#az-network-route-table-route-update) |
| PowerShell | [Set-AzRouteConfig](/powershell/module/az.network/set-azrouteconfig) |

## <a name="delete-a-route"></a>Een route verwijderen

1. Ga naar de [Azure Portal](https://portal.azure.com) om uw route tabellen te beheren. Zoek en selecteer **Routeringstabellen**.

1. Kies in de lijst route tabel de route tabel met de route die u wilt verwijderen.

1. Kies **routes** in de menu balk van de route tabel om de lijst met routes weer te geven.

1. Kies de route die u wilt verwijderen.

1. Selecteer **verwijderen** en selecteer vervolgens **Ja** in het bevestigings venster.

### <a name="delete-a-route---commands"></a>Een route-opdrachten verwijderen

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [AZ Network Route-Table route delete](/cli/azure/network/route-table/route#az-network-route-table-route-delete) |
| PowerShell | [Remove-AzRouteConfig](/powershell/module/az.network/remove-azrouteconfig) |

## <a name="view-effective-routes"></a>Efficiënte routes weer geven

De meest efficiënte routes voor elke netwerk interface die aan de VM is gekoppeld, zijn een combi natie van route tabellen die u hebt gemaakt, de standaard routes van Azure en alle routes die worden door gegeven via on-premises netwerken via de Border Gateway Protocol (BGP) via een virtuele Azure-netwerk gateway. Informatie over de efficiënte routes voor een netwerk interface is handig bij het oplossen van problemen met route ring. U kunt de actieve routes weer geven voor elke netwerk interface die is gekoppeld aan een actieve virtuele machine.

1. Ga naar de [Azure Portal](https://portal.azure.com) om uw virtuele machines te beheren. Zoek en selecteer **virtuele machines**.

1. Kies in de lijst met virtuele machines de VM waarvoor u de juiste routes wilt weer geven.

1. Kies in de menu balk van de VM de optie **netwerken**.

1. Selecteer de naam van een netwerk interface.

1. Selecteer in de menu balk van de netwerk interface de optie **actieve routes**.

1. Bekijk de lijst met efficiënte routes om te zien of de juiste route bestaat voor waar u verkeer wilt door sturen. Meer informatie over de typen van de volgende hop die u ziet in deze lijst in [virtuele netwerk verkeer routeren](virtual-networks-udr-overview.md).

### <a name="view-effective-routes---commands"></a>Efficiënte routes weer geven-opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [AZ Network NIC show-ingangsdatum-route tabel](/cli/azure/network/nic#az-network-nic-show-effective-route-table) |
| PowerShell | [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable) |

## <a name="validate-routing-between-two-endpoints"></a>Route ring tussen twee eind punten valideren

U kunt het type van de volgende hop bepalen tussen een virtuele machine en het IP-adres van een andere Azure-resource, een on-premises resource of een bron op internet. Het bepalen van de route ring van Azure is handig bij het oplossen van problemen met route ring. Als u deze taak wilt volt ooien, moet u een bestaande netwerk-Watcher hebben. Als u geen bestaande netwerk-Watcher hebt, maakt u er een door de stappen in [een Network Watcher-exemplaar maken](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)uit te voeren.

1. Ga naar de [Azure Portal](https://portal.azure.com) om uw netwerk-webobjecten te beheren. Zoek en selecteer **Network Watcher**.

1. Kies in de menu balk van Network Watcher **volgende hop**.

1. In de **Network Watcher | Volgende hop** -pagina:

    1. Kies uw **abonnement** en de **resource groep** van de bron-VM waarvan u de route ring wilt valideren.

    1. Kies de **virtuele machine** en de **netwerk interface** die is gekoppeld aan de VM.
    
    1. Voer het **IP-adres** van de bron in die is toegewezen aan de netwerk interface waarvan u de route ring wilt valideren.

    1. Voer het **IP-adres** van de bestemming in waarnaar u de route ring wilt valideren.

1. Selecteer **volgende hop**.

Na een korte wacht tijd geeft Azure u het type van de volgende hop en de ID van de route die het verkeer stuurt. Meer informatie over de volgende typen hops die worden weer gegeven in de [route ring van virtueel netwerk verkeer](virtual-networks-udr-overview.md).

### <a name="validate-routing-between-two-endpoints---commands"></a>Route ring tussen twee eind punten valideren-opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [AZ Network Watcher show-next-hop](/cli/azure/network/watcher#az-network-watcher-show-next-hop) |
| PowerShell | [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop) |

## <a name="permissions"></a>Machtigingen

Om taken uit te voeren op route tabellen en routes, moet uw account worden toegewezen aan de [rol netwerk bijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) of aan een [aangepaste rol](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) waaraan de juiste acties in de volgende tabel worden toegewezen:

| Bewerking                                                          |   Name                                                  |
|--------------------------------------------------------------   |   -------------------------------------------           |
| Micro soft. Network/routeTables/lezen                              |   Een route tabel lezen                                    |
| Micro soft. Network/routeTables/schrijven                             |   Een route tabel maken of bijwerken                        |
| Micro soft. Network/routeTables/verwijderen                            |   Een routetabel verwijderen                                  |
| Micro soft. Network/routeTables/samen voegen/actie                       |   Een routetabel aan een subnet koppelen                   |
| Micro soft. Network/routeTables/routes/lezen                       |   Een route lezen                                          |
| Micro soft. Network/routeTables/routes/schrijven                      |   Een route maken of bijwerken                              |
| Micro soft. Network/routeTables/routes/verwijderen                     |   Een route verwijderen                                        |
| Micro soft. Network/networkInterfaces/effectiveRouteTable/Action  |   De doel tabel van de route ophalen voor een netwerk interface |
| Micro soft. Network/networkWatchers/nextHop/actie                |   Hiermee wordt de volgende hop van een virtuele machine opgehaald                           |

## <a name="next-steps"></a>Volgende stappen

- Een route tabel maken met behulp van [Power shell](powershell-samples.md) of [Azure cli](cli-samples.md) -voorbeeld scripts of Azure [Resource Manager-sjablonen](template-samples.md)
- [Azure Policy definities](./policy-reference.md) voor virtuele netwerken maken en toewijzen