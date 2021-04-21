---
title: Topologie van virtuele Azure-netwerk weergeven | Microsoft Docs
description: Meer informatie over het weergeven van de resources in een virtueel netwerk en de relaties tussen de resources.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2018
ms.author: damendo
ms.openlocfilehash: f20fa22dac3fba4d01cbc5e398bafa4113e94a96
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780295"
---
# <a name="view-the-topology-of-an-azure-virtual-network"></a>De topologie van een virtueel Azure-netwerk weergeven

In dit artikel leert u hoe u resources kunt weergeven in een Microsoft Azure virtueel netwerk en de relaties tussen de resources. Een virtueel netwerk bevat bijvoorbeeld subnetten. Subnetten bevatten resources, zoals Azure Virtual Machines (VM). VM's hebben een of meer netwerkinterfaces. Aan elk subnet kunnen een netwerkbeveiligingsgroep en een routetabel zijn gekoppeld. Met de topologiefunctie van Azure Network Watcher kunt u alle resources in een virtueel netwerk, de resources die zijn gekoppeld aan resources in een virtueel netwerk en de relaties tussen de resources weergeven.

U kunt de [Azure Portal,](#azure-portal)de [Azure CLI](#azure-cli)of [PowerShell](#powershell) gebruiken om een topologie weer te geven.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="view-topology---azure-portal"></a><a name = "azure-portal"></a>Topologie weergeven - Azure Portal

1. Meld u aan [Azure Portal](https://portal.azure.com) account met de benodigde [machtigingen](required-rbac-permissions.md).
2. Selecteer alle services in de linkerbovenhoek **van de portal.**
3. Voer in **het filtervak** Alle services *Network Watcher.* Selecteer **Network Watcher** in de resultaten.
4. Selecteer **Topologie**. Voor het genereren van een topologie is een netwerk-watcher vereist in dezelfde regio als het virtuele netwerk waarin u de topologie wilt genereren. Als u geen netwerk-watcher hebt ingeschakeld in de regio waarin het virtuele netwerk waarin u een topologie wilt genereren zich in zich heeft, worden netwerk-watchers automatisch voor u gemaakt in alle regio's. De network watchers worden gemaakt in een resourcegroep met de **naam NetworkWatcherRG.**
5. Selecteer een abonnement, de resourcegroep van een virtueel netwerk waar u de topologie voor wilt weergeven en selecteer vervolgens het virtuele netwerk. In de volgende afbeelding wordt een topologie weergegeven voor een virtueel netwerk met de naam *MyVnet* in de resourcegroep met de *naam MyResourceGroup:*

    ![Topologie weergeven](./media/view-network-topology/view-topology.png)

    Zoals u in de vorige afbeelding kunt zien, bevat het virtuele netwerk drie subnetten. In één subnet is een virtuele machine geïmplementeerd. Aan de VM is één netwerkinterface gekoppeld en er is een openbaar IP-adres aan gekoppeld. Aan de andere twee subnetten is een routetabel gekoppeld. Elke routetabel bevat twee routes. Aan één subnet is een netwerkbeveiligingsgroep gekoppeld. Topologie-informatie wordt alleen weergegeven voor resources die:
    
    - Binnen dezelfde resourcegroep en regio als het virtuele *netwerk myVnet.* Een netwerkbeveiligingsgroep die zich in een andere resourcegroep dan *MyResourceGroup* bevindt, wordt bijvoorbeeld niet weergegeven, zelfs niet als de netwerkbeveiligingsgroep is gekoppeld aan een subnet in het virtuele netwerk *MyVnet.*
    - Binnen het virtuele netwerk *myVnet,* of gekoppeld aan resources in het virtuele netwerk myVnet. Een netwerkbeveiligingsgroep die niet is gekoppeld aan een subnet of netwerkinterface in het virtuele *netwerk myVnet* wordt bijvoorbeeld niet weergegeven, zelfs niet als de netwerkbeveiligingsgroep zich in de resourcegroep *MyResourceGroup.*

   De topologie die in de afbeelding wordt weergegeven, is voor het virtuele netwerk dat is gemaakt na de implementatie van het **scriptvoorbeeld** Verkeer via een virtueel netwerkapparaat implementeren, dat u kunt implementeren met behulp van [de Azure CLI](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)of [PowerShell](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

6. Selecteer **Topologie downloaden om** de afbeelding te downloaden als een bewerkbaar bestand, in SVG-indeling.

De resources die in het diagram worden weergegeven, zijn een subset van de netwerkonderdelen in het virtuele netwerk. Wanneer bijvoorbeeld een netwerkbeveiligingsgroep wordt weergegeven, worden de beveiligingsregels in deze groep niet weergegeven in het diagram. Hoewel de lijnen in het diagram niet zijn gedifferentieerd, vertegenwoordigen de lijnen een van twee relaties: *Insluiting* of *gekoppelde*. Als u de volledige lijst met resources in het virtuele netwerk en het type relatie tussen de resources wilt zien, genereert u de topologie met [PowerShell](#powershell) of [de Azure CLI.](#azure-cli)

## <a name="view-topology---azure-cli"></a><a name = "azure-cli"></a>Topologie weergeven - Azure CLI

U kunt de opdrachten uitvoeren in de volgende stappen:
- Selecteer in Azure Cloud Shell **rechtsboven** in een opdracht de optie Proberen. De Azure Cloud Shell is een gratis interactieve shell met algemene Azure-hulpprogramma's die vooraf zijn geïnstalleerd en geconfigureerd voor gebruik met uw account.
- Door de CLI uit te werken vanaf uw computer. Als u de CLI vanaf uw computer hebt uitgevoerd, is voor de stappen in dit artikel Versie 2.0.31 of hoger van Azure CLI vereist. Voer `az --version` uit om te kijken welke versie is geïnstalleerd. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli). Als u de Azure CLI lokaal gebruikt, moet u ook uitvoeren om `az login` een verbinding met Azure te maken.

Het account dat u gebruikt, moet de benodigde [machtigingen hebben.](required-rbac-permissions.md)

1. Als u al een netwerk-watcher hebt in dezelfde regio als het virtuele netwerk waar u een topologie voor wilt maken, gaat u verder met stap 3. Maak met az group create een resourcegroep die een network watcher [bevat.](/cli/azure/group) In het volgende voorbeeld wordt de resourcegroep gemaakt in de *regio eastus:*

    ```azurecli-interactive
    az group create --name NetworkWatcherRG --location eastus
    ```

2. Maak een netwerk-watcher met [az network watcher configure](/cli/azure/network/watcher#az_network_watcher_configure). In het volgende voorbeeld wordt een netwerk-watcher gemaakt in de *regio eastus:*

    ```azurecli-interactive
    az network watcher configure \
      --resource-group NetworkWatcherRG \
      --location eastus \
      --enabled true
    ```

3. Bekijk de topologie met [az network watcher show-topology](/cli/azure/network/watcher#az_network_watcher_show_topology). In het volgende voorbeeld wordt de topologie voor een resourcegroep met de *naam MyResourceGroup bekeken:*

    ```azurecli-interactive
    az network watcher show-topology --resource-group MyResourceGroup
    ```

    Topologiegegevens worden alleen geretourneerd voor resources die zich in dezelfde resourcegroep als de resourcegroep *MyResourceGroup* en dezelfde regio als de network watcher hebben. Een netwerkbeveiligingsgroep die bestaat in een andere resourcegroep dan *MyResourceGroup* wordt bijvoorbeeld niet weergegeven, zelfs niet als de netwerkbeveiligingsgroep is gekoppeld aan een subnet in het virtuele netwerk *MyVnet.*

   Meer informatie over de relaties en [eigenschappen](#properties) in de geretourneerde uitvoer. Als u geen bestaand virtueel netwerk hebt om een topologie voor weer te geven, kunt u er een maken met behulp van het scriptvoorbeeld Verkeer door een virtueel [netwerkapparaat](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) leiden. Als u een diagram van de topologie wilt bekijken en downloaden in een bewerkbaar bestand, gebruikt u de [portal](#azure-portal).

## <a name="view-topology---powershell"></a><a name = "powershell"></a>Topologie weergeven - PowerShell

U kunt de opdrachten uitvoeren in de volgende stappen:
- Selecteer in Azure Cloud Shell de optie **Proberen** rechtsboven in een opdracht. De Azure Cloud Shell is een gratis interactieve shell met algemene Azure-hulpprogramma's die vooraf zijn geïnstalleerd en geconfigureerd voor gebruik met uw account.
- Door PowerShell uit te werken vanaf uw computer. Als u PowerShell vanaf uw computer hebt uitgevoerd, is voor dit artikel de Azure PowerShell `Az` vereist. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

Het account dat u gebruikt, moet de benodigde [machtigingen hebben.](required-rbac-permissions.md)

1. Als u al een netwerk-watcher in dezelfde regio hebt als het virtuele netwerk waar u een topologie voor wilt maken, gaat u verder met stap 3. Maak een resourcegroep voor een netwerk-watcher met [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). In het volgende voorbeeld wordt de resourcegroep gemaakt in de *regio eastus:*

    ```azurepowershell-interactive
    New-AzResourceGroup -Name NetworkWatcherRG -Location EastUS
    ```

2. Maak een netwerk-watcher met [New-AzNetworkWatcher](/powershell/module/az.network/new-aznetworkwatcher). In het volgende voorbeeld wordt een netwerk-watcher gemaakt in de regio eastus:

    ```azurepowershell-interactive
    New-AzNetworkWatcher `
      -Name NetworkWatcher_eastus `
      -ResourceGroupName NetworkWatcherRG
    ```

3. Haal een Network Watcher op met [Get-AzNetworkWatcher](/powershell/module/az.network/get-aznetworkwatcher). In het volgende voorbeeld wordt een netwerk-watcher opgehaald in de regio VS - oost:

    ```azurepowershell-interactive
    $nw = Get-AzResource `
      | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "EastUS" }
    $networkWatcher = Get-AzNetworkWatcher `
      -Name $nw.Name `
      -ResourceGroupName $nw.ResourceGroupName
    ```

4. Haal een topologie op [met Get-AzNetworkWatcherTopology](/powershell/module/az.network/get-aznetworkwatchertopology). In het volgende voorbeeld wordt een topologie opgehaald voor een virtueel netwerk in de resourcegroep met de *naam MyResourceGroup:*

    ```azurepowershell-interactive
    Get-AzNetworkWatcherTopology `
      -NetworkWatcher $networkWatcher `
      -TargetResourceGroupName MyResourceGroup
    ```

   Topologie-informatie wordt alleen geretourneerd voor resources die zich binnen dezelfde resourcegroep als de resourcegroep *MyResourceGroup* en dezelfde regio als de network watcher. Een netwerkbeveiligingsgroep die zich in een andere resourcegroep dan *MyResourceGroup* bevindt, wordt bijvoorbeeld niet weergegeven, zelfs niet als de netwerkbeveiligingsgroep is gekoppeld aan een subnet in het virtuele netwerk *MyVnet.*

   Meer informatie over de relaties en [eigenschappen](#properties) in de geretourneerde uitvoer. Als u geen bestaand virtueel netwerk hebt om een topologie voor weer te geven, kunt u er een maken met behulp van het scriptvoorbeeld Verkeer door een virtueel [netwerkapparaat](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) leiden. Als u een diagram van de topologie wilt weergeven en downloaden in een bewerkbaar bestand, gebruikt u de [portal](#azure-portal).

## <a name="relationships"></a>Relaties

Alle resources die in een topologie worden geretourneerd, hebben een van de volgende typen relatie met een andere resource:

| Relatietype | Voorbeeld                                                                                                |
| ---               | ---                                                                                                    |
| Containment       | Een virtueel netwerk bevat een subnet. Een subnet bevat een netwerkinterface.                            |
| Bijbehorend        | Een netwerkinterface is gekoppeld aan een VM. Een openbaar IP-adres is gekoppeld aan een netwerkinterface. |

## <a name="properties"></a>Eigenschappen

Alle resources die in een topologie worden geretourneerd, hebben de volgende eigenschappen:

- **Naam:** de naam van de resource
- **Id:** de URI van de resource.
- **Locatie:** de Azure-regio waarin de resource zich bevindt.
- **Verbanden:** een lijst met verbanden met het object waarnaar wordt verwezen. Elke associatie heeft de volgende eigenschappen:
    - **AssociationType:** verwijst naar de relatie tussen het onderliggende object en het bovenliggende object. Geldige waarden zijn *Bevat* of *Gekoppeld.*
    - **Naam:** de naam van de resource waarnaar wordt verwezen.
    - **ResourceId:** de URI van de resource waarnaar wordt verwezen in de associatie.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [vaststellen van een probleem met netwerkverkeersfilters](diagnose-vm-network-traffic-filtering-problem.md) van of naar een VM met behulp Network Watcher ip-stroom controleren
- Meer informatie over het [diagnosticeren van een](diagnose-vm-network-routing-problem.md) probleem met routering van netwerkverkeer vanaf een virtuele Network Watcher van de volgende hopfunctie van Network Watcher
