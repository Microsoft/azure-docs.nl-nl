---
title: Azure Virtual Network-topologie weer geven | Microsoft Docs
description: Meer informatie over het weer geven van de resources in een virtueel netwerk en de relaties tussen de resources.
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
ms.openlocfilehash: c04da65af27ebd5ac654bc059ae004c157a20f33
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84737526"
---
# <a name="view-the-topology-of-an-azure-virtual-network"></a>De topologie van een virtueel Azure-netwerk weer geven

In dit artikel leert u hoe u resources kunt weer geven in een Microsoft Azure virtueel netwerk en de relaties tussen de resources. Een virtueel netwerk bevat bijvoorbeeld subnetten. Subnetten bevatten bronnen, zoals Azure Virtual Machines (VM). Vm's hebben een of meer netwerk interfaces. Aan elk subnet kan een netwerk beveiligings groep en een bijbehorende route tabel zijn gekoppeld. Met de topologie mogelijkheden van Azure Network Watcher kunt u alle resources in een virtueel netwerk, de resources die zijn gekoppeld aan resources in een virtueel netwerk en de relaties tussen de bronnen weer geven.

U kunt de [Azure Portal](#azure-portal), de [Azure cli](#azure-cli)of [Power shell](#powershell) gebruiken om een topologie weer te geven.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="view-topology---azure-portal"></a><a name = "azure-portal"></a>Topologie weer geven-Azure Portal

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met een account dat over de benodigde [machtigingen](required-rbac-permissions.md)beschikt.
2. Selecteer in de linkerbovenhoek van de portal **alle services**.
3. Voer in het vak **alle services** filteren de *Network Watcher* in. Selecteer **Network Watcher** in de resultaten.
4. Selecteer **Topologie**. Voor het genereren van een topologie is een Network Watcher vereist in dezelfde regio waarin het virtuele netwerk waarvan u de topologie wilt genereren, bestaat. Als u geen Network Watcher hebt ingeschakeld in de regio waarin het virtuele netwerk waarvoor u een topologie wilt genereren, in is opgenomen, worden de netwerk-volgers automatisch voor u in alle regio's gemaakt. De netwerk-volgers worden gemaakt in een resource groep met de naam **NetworkWatcherRG**.
5. Selecteer een abonnement, de resource groep van een virtueel netwerk waarvan u de topologie wilt weer geven en selecteer vervolgens het virtuele netwerk. In de volgende afbeelding wordt een topologie weer gegeven voor een virtueel netwerk met de naam *MyVnet*, in de resource groep met de naam *MyResourceGroup*:

    ![Topologie weer geven](./media/view-network-topology/view-topology.png)

    Zoals u kunt zien in de vorige afbeelding, bevat het virtuele netwerk drie subnetten. In het ene subnet is een VM geïmplementeerd. Er is één netwerk interface gekoppeld aan de virtuele machine en er is een openbaar IP-adres gekoppeld aan de VM. Aan de andere twee subnetten is een route tabel gekoppeld. Elke route tabel bevat twee routes. Aan één subnet is een netwerk beveiligings groep gekoppeld. De topologie gegevens worden alleen weer gegeven voor de volgende resources:
    
    - Binnen dezelfde resource groep en regio als het virtuele *myVnet* -netwerk. Bijvoorbeeld, een netwerk beveiligings groep die bestaat in een andere resource groep dan *MyResourceGroup*, wordt niet weer gegeven, zelfs niet als de netwerk beveiligings groep is gekoppeld aan een subnet in het virtuele *MyVnet* -netwerk.
    - Binnen, of gekoppeld aan resources binnen, het virtuele *myVnet* -netwerk. Bijvoorbeeld, een netwerk beveiligings groep die niet is gekoppeld aan een subnet of netwerk interface in het virtuele netwerk *myVnet* wordt niet weer gegeven, zelfs niet als de netwerk beveiligings groep zich in de *MyResourceGroup* -resource groep bevindt.

   De topologie die in de afbeelding wordt weer gegeven, is voor het virtuele netwerk dat is gemaakt na het implementeren van het **route verkeer via een voor beeld van een virtueel netwerk** voor het uitvoeren van netwerken, dat u kunt implementeren met behulp van [Azure cli](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)of [Power shell](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

6. Selecteer **down load topologie** om de installatie kopie te downloaden als een bewerkbaar bestand, in SVG-indeling.

De resources die in het diagram worden weer gegeven, zijn een subset van de netwerk onderdelen in het virtuele netwerk. Als er bijvoorbeeld een netwerk beveiligings groep wordt weer gegeven, worden de beveiligings regels in het diagram niet weer gegeven in de grafiek. Hoewel het diagram niet is gedifferentieerd, vertegenwoordigen de lijnen een van de twee relaties: *opnemen* of *gekoppeld*. Genereer de topologie met [Power shell](#powershell) of de [Azure cli](#azure-cli)voor een overzicht van de volledige lijst met resources in het virtuele netwerk en het type relatie tussen de resources.

## <a name="view-topology---azure-cli"></a><a name = "azure-cli"></a>Topologie weer geven-Azure CLI

U kunt de opdrachten uitvoeren in de volgende stappen:
- In de Azure Cloud Shell, door het **selectie vakje** in de rechter bovenhoek van elke opdracht te selecteren. De Azure Cloud Shell is een gratis interactieve shell met algemene Azure-hulpprogram ma's die vooraf zijn geïnstalleerd en geconfigureerd voor gebruik met uw account.
- Door de CLI vanaf uw computer uit te voeren. Als u de CLI vanaf uw computer uitvoert, is voor de stappen in dit artikel de Azure CLI-versie 2.0.31 of hoger vereist. Voer `az --version` uit om te kijken welke versie is geïnstalleerd. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli). Als u de Azure CLI lokaal uitvoert, moet u ook uitvoeren `az login` om een verbinding te maken met Azure.

Het account dat u gebruikt, moet over de vereiste [machtigingen](required-rbac-permissions.md)beschikken.

1. Als u al een Network Watcher in dezelfde regio hebt als het virtuele netwerk waarvoor u een topologie wilt maken, gaat u naar stap 3. Maak een resource groep die een Network Watcher bevat met [AZ Group Create](/cli/azure/group). In het volgende voor beeld wordt de resource groep gemaakt in de regio *eastus* :

    ```azurecli-interactive
    az group create --name NetworkWatcherRG --location eastus
    ```

2. Maak een Network Watcher met [AZ Network Watcher configure](/cli/azure/network/watcher#az-network-watcher-configure). In het volgende voor beeld wordt een Network Watcher gemaakt in de regio *oostus* :

    ```azurecli-interactive
    az network watcher configure \
      --resource-group NetworkWatcherRG \
      --location eastus \
      --enabled true
    ```

3. Bekijk de topologie met [AZ Network Watcher show-topologie](/cli/azure/network/watcher#az-network-watcher-show-topology). In het volgende voor beeld wordt de topologie voor een resource groep met de naam *MyResourceGroup* weer gegeven:

    ```azurecli-interactive
    az network watcher show-topology --resource-group MyResourceGroup
    ```

    De topologie gegevens worden alleen geretourneerd voor bronnen die zich in dezelfde resource groep bevinden als de *MyResourceGroup* -resource groep en dezelfde regio als de Network Watcher. Bijvoorbeeld, een netwerk beveiligings groep die bestaat in een andere resource groep dan *MyResourceGroup*, wordt niet weer gegeven, zelfs niet als de netwerk beveiligings groep is gekoppeld aan een subnet in het virtuele *MyVnet* -netwerk.

   Meer informatie over de relaties en [Eigenschappen](#properties) in de geretourneerde uitvoer. Als u geen bestaand virtueel netwerk hebt om een topologie voor weer te geven, kunt u er een maken met behulp van het [route ring verkeer via een netwerk voorbeeld script van een virtueel apparaat](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) . Gebruik de [Portal](#azure-portal)om een diagram van de topologie weer te geven en te downloaden in een bewerkbaar bestand.

## <a name="view-topology---powershell"></a><a name = "powershell"></a>Topologie weer geven-Power shell

U kunt de opdrachten uitvoeren in de volgende stappen:
- In de Azure Cloud Shell, door het **selectie vakje** in de rechter bovenhoek van elke opdracht te selecteren. De Azure Cloud Shell is een gratis interactieve shell met algemene Azure-hulpprogram ma's die vooraf zijn geïnstalleerd en geconfigureerd voor gebruik met uw account.
- Door Power shell uit te voeren vanaf uw computer. Als u Power shell vanaf uw computer uitvoert, is voor dit artikel de module Azure PowerShell vereist `Az` . Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

Het account dat u gebruikt, moet over de vereiste [machtigingen](required-rbac-permissions.md)beschikken.

1. Als u al een Network Watcher in dezelfde regio hebt als het virtuele netwerk waarvoor u een topologie wilt maken, gaat u naar stap 3. Maak een resource groep die een Network Watcher bevat met [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). In het volgende voor beeld wordt de resource groep gemaakt in de regio *eastus* :

    ```azurepowershell-interactive
    New-AzResourceGroup -Name NetworkWatcherRG -Location EastUS
    ```

2. Maak een Network Watcher met [New-AzNetworkWatcher](/powershell/module/az.network/new-aznetworkwatcher). In het volgende voor beeld wordt een Network Watcher gemaakt in de regio oostus:

    ```azurepowershell-interactive
    New-AzNetworkWatcher `
      -Name NetworkWatcher_eastus `
      -ResourceGroupName NetworkWatcherRG
    ```

3. Haal een Network Watcher-exemplaar op met [Get-AzNetworkWatcher](/powershell/module/az.network/get-aznetworkwatcher). In het volgende voor beeld wordt een Network Watcher opgehaald in de regio VS-Oost:

    ```azurepowershell-interactive
    $nw = Get-AzResource `
      | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "EastUS" }
    $networkWatcher = Get-AzNetworkWatcher `
      -Name $nw.Name `
      -ResourceGroupName $nw.ResourceGroupName
    ```

4. Haal een topologie op met [Get-AzNetworkWatcherTopology](/powershell/module/az.network/get-aznetworkwatchertopology). In het volgende voor beeld wordt een topologie opgehaald voor een virtueel netwerk in de resource groep met de naam *MyResourceGroup*:

    ```azurepowershell-interactive
    Get-AzNetworkWatcherTopology `
      -NetworkWatcher $networkWatcher `
      -TargetResourceGroupName MyResourceGroup
    ```

   De topologie gegevens worden alleen geretourneerd voor bronnen die zich in dezelfde resource groep bevinden als de *MyResourceGroup* -resource groep en dezelfde regio als de Network Watcher. Bijvoorbeeld, een netwerk beveiligings groep die bestaat in een andere resource groep dan *MyResourceGroup*, wordt niet weer gegeven, zelfs niet als de netwerk beveiligings groep is gekoppeld aan een subnet in het virtuele *MyVnet* -netwerk.

   Meer informatie over de relaties en [Eigenschappen](#properties) in de geretourneerde uitvoer. Als u geen bestaand virtueel netwerk hebt om een topologie voor weer te geven, kunt u er een maken met behulp van het [route ring verkeer via een netwerk voorbeeld script van een virtueel apparaat](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) . Gebruik de [Portal](#azure-portal)om een diagram van de topologie weer te geven en te downloaden in een bewerkbaar bestand.

## <a name="relationships"></a>Relaties

Alle resources die in een topologie worden geretourneerd, hebben een van de volgende typen relaties met een andere resource:

| Relatietype | Voorbeeld                                                                                                |
| ---               | ---                                                                                                    |
| Containment       | Een virtueel netwerk bevat een subnet. Een subnet bevat een netwerk interface.                            |
| Bijbehorend        | Een netwerk interface is gekoppeld aan een virtuele machine. Een openbaar IP-adres is gekoppeld aan een netwerk interface. |

## <a name="properties"></a>Eigenschappen

Alle resources die in een topologie worden geretourneerd, hebben de volgende eigenschappen:

- **Naam**: de naam van de resource
- **Id**: de URI van de resource.
- **Locatie**: de Azure-regio waarin de resource zich bevindt.
- **Koppelingen**: een lijst met koppelingen naar het object waarnaar wordt verwezen. Elke koppeling heeft de volgende eigenschappen:
    - **AssociationType**: verwijst naar de relatie tussen het onderliggende object en de bovenliggende. Geldige waarden zijn *bevat* of *zijn gekoppeld*.
    - **Naam**: de naam van de resource waarnaar wordt verwezen.
    - **ResourceID**:-de URI van de resource waarnaar wordt verwezen in de koppeling.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [vaststellen van een probleem met het filteren van netwerk verkeer naar of van een virtuele machine](diagnose-vm-network-traffic-filtering-problem.md) met behulp van de IP-stroom verificatie van Network Watcher
- Meer informatie over het oplossen [van een netwerk verkeer routerings probleem van een virtuele machine](diagnose-vm-network-routing-problem.md) met behulp van de volgende hop-mogelijkheid van Network Watcher
