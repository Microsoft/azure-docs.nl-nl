---
title: Een routeringsprobleem met virtuele Azure-machines | Microsoft Docs
description: Ontdek hoe u een routeringsprobleem met virtuele machines kunt vaststellen door de effectieve routes voor een virtuele machine te bekijken.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/30/2018
ms.author: kumud
ms.openlocfilehash: f84b74b054a073f2c1ae5ba2ac7d0d0a968367c6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767669"
---
# <a name="diagnose-a-virtual-machine-routing-problem"></a>Een routeringsprobleem met virtuele machines vaststellen

In dit artikel leert u hoe u een routeringsprobleem kunt diagnosticeren door de routes te bekijken die effectief zijn voor een netwerkinterface in een virtuele machine (VM). Azure maakt verschillende standaardroutes voor elk subnet van een virtueel netwerk. U kunt de standaardroutes van Azure overschrijven door routes in een routetabel te definiëren en vervolgens de routetabel aan een subnet te koppelen. De combinatie van routes die u maakt, de standaardroutes van Azure en alle routes die vanuit uw on-premises netwerk worden doorgegeven via een Azure VPN-gateway (als uw virtuele netwerk is verbonden met uw on-premises netwerk) via het Border Gateway Protocol (BGP), zijn de effectieve routes voor alle netwerkinterfaces in een subnet. Zie Overzicht van virtuele netwerken, Netwerkinterface en Routeringsoverzicht [](virtual-network-network-interface.md)als u niet bekend bent met concepten voor virtueel netwerk, netwerkinterface [of routering.](virtual-networks-udr-overview.md) [](virtual-networks-overview.md)

## <a name="scenario"></a>Scenario

U probeert verbinding te maken met een VM, maar de verbinding mislukt. Als u wilt bepalen waarom u geen verbinding kunt maken met de VM, kunt u de effectieve routes voor een netwerkinterface bekijken met behulp van Azure [Portal,](#diagnose-using-azure-portal) [PowerShell](#diagnose-using-powershell)of [de Azure CLI.](#diagnose-using-azure-cli)

Bij de volgende stappen wordt ervan uitgenomen dat u een bestaande VM hebt om de effectieve routes voor te bekijken. Als u geen bestaande VM hebt, implementeert u eerst een [Linux-](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) of [Windows-VM](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) om de taken in dit artikel mee uit te voeren. De voorbeelden in dit artikel zijn voor een VM met de naam *myVM met* een netwerkinterface met de *naam myVMNic1.* De VM en netwerkinterface maken deel uit van een resourcegroep met de naam *myResourceGroup* en zijn in de regio *VS -* oost. Wijzig de waarden in de stappen, waar van toepassing, voor de VM voor wie u het probleem wilt vaststellen.

## <a name="diagnose-using-azure-portal"></a>Diagnoses stellen met Azure Portal

1. Meld u aan bij Azure [Portal](https://portal.azure.com) met een Azure-account met [de benodigde machtigingen.](virtual-network-network-interface.md#permissions)
2. Voer bovenaan de Azure Portal de naam in van een VM met de status Actief in het zoekvak. Wanneer de naam van de VM wordt weergegeven in de zoekresultaten, selecteert u deze.
3. Selecteer **onder Instellingen** aan de linkerkant **Netwerken** en navigeer naar de resource van de netwerkinterface door de naam ervan te selecteren.
     ![Netwerkinterfaces weergeven](./media/diagnose-network-routing-problem/view-nics.png)
4. Selecteer aan de linkerkant **Effectieve routes.** De effectieve routes voor een netwerkinterface met de **naam myVMNic1** worden weergegeven in de volgende afbeelding: ![ Effectieve routes weergeven](./media/diagnose-network-routing-problem/view-effective-routes.png)

    Als er meerdere netwerkinterfaces zijn gekoppeld aan de VM, kunt u de effectieve routes voor elke netwerkinterface bekijken door deze te selecteren. Omdat elke netwerkinterface zich in een ander subnet kan, kan elke netwerkinterface verschillende effectieve routes hebben.

    In het voorbeeld dat in de vorige afbeelding wordt weergegeven, zijn de vermelde routes standaardroutes die Azure voor elk subnet maakt. Uw lijst heeft ten minste deze routes, maar kan aanvullende routes hebben, afhankelijk van de mogelijkheden die u mogelijk hebt ingeschakeld voor uw virtuele netwerk, zoals peering met een ander virtueel netwerk of verbonden met uw on-premises netwerk via een Azure VPN-gateway. Zie Routering van virtueel netwerkverkeer voor meer informatie over elk van de routes en andere routes die u mogelijk ziet voor uw [netwerkinterface.](virtual-networks-udr-overview.md) Als uw lijst een groot aantal routes bevat, is het wellicht eenvoudiger om **Downloaden** te selecteren om een CSV-bestand met de lijst met routes te downloaden.

Hoewel effectieve routes in de vorige stappen via de VM zijn bekeken, kunt u ook effectieve routes weergeven via een:
- **Afzonderlijke netwerkinterface:** informatie over het [weergeven van een netwerkinterface.](virtual-network-network-interface.md#view-network-interface-settings)
- **Afzonderlijke routetabel:** informatie over het [weergeven van een routetabel.](manage-route-table.md#view-details-of-a-route-table)

## <a name="diagnose-using-powershell"></a>Diagnoses stellen met behulp van PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U kunt de opdrachten uitvoeren die volgen in de [Azure Cloud Shell](https://shell.azure.com/powershell)of door PowerShell uit te voeren vanaf uw computer. De Azure Cloud Shell is een gratis interactieve shell. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Als u PowerShell vanaf uw computer hebt uitgevoerd, hebt u de Azure PowerShell module versie 1.0.0 of hoger nodig. Voer `Get-Module -ListAvailable Az` uit op uw computer om de geïnstalleerde versie te vinden. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps). Als u PowerShell lokaal gebruikt, moet u ook uitvoeren om u aan te melden bij Azure met een account met `Connect-AzAccount` [de benodigde machtigingen.](virtual-network-network-interface.md#permissions)

Haal de effectieve routes voor een netwerkinterface op [met Get-AzEffectiveRouteTable.](/powershell/module/az.network/get-azeffectiveroutetable) In het volgende voorbeeld worden de effectieve routes voor een netwerkinterface met de naam *myVMNic1,* die zich in een resourcegroep met de *naam myResourceGroup:*

```azurepowershell-interactive
Get-AzEffectiveRouteTable `
  -NetworkInterfaceName myVMNic1 `
  -ResourceGroupName myResourceGroup `
  | Format-Table
```

Zie Overzicht van routering voor meer informatie over de informatie die wordt geretourneerd [in de uitvoer.](virtual-networks-udr-overview.md) Uitvoer wordt alleen geretourneerd als de VM actief is. Als er meerdere netwerkinterfaces zijn gekoppeld aan de VM, kunt u de effectieve routes voor elke netwerkinterface bekijken. Omdat elke netwerkinterface zich in een ander subnet kan, kan elke netwerkinterface verschillende effectieve routes hebben. Als u nog steeds een communicatieprobleem hebt, bekijkt u [aanvullende diagnose](#additional-diagnosis) en [overwegingen.](#considerations)

Als u de naam van een netwerkinterface niet weet, maar wel de naam weet van de VM waar de netwerkinterface aan is gekoppeld, retourneren de volgende opdrachten de ID's van alle netwerkinterfaces die zijn gekoppeld aan een VM:

```azurepowershell-interactive
$VM = Get-AzVM -Name myVM `
  -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

U ontvangt uitvoer die vergelijkbaar is met het volgende voorbeeld:

```powershell
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMNic1
```

In de vorige uitvoer is de naam van de netwerkinterface *myVMNic1.*

## <a name="diagnose-using-azure-cli"></a>Diagnose uitvoeren met behulp van Azure CLI

U kunt de opdrachten uitvoeren die volgen in de  [Azure Cloud Shell](https://shell.azure.com/bash)of door de CLI uit te voeren vanaf uw computer. Voor dit artikel is Versie 2.0.32 of hoger van Azure CLI vereist. Voer `az --version` uit om te kijken welke versie is geïnstalleerd. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren. Als u de Azure CLI lokaal gebruikt, moet u ook uitvoeren en u aanmelden bij Azure met een account met `az login` [de benodigde machtigingen.](virtual-network-network-interface.md#permissions)

Haal de effectieve routes voor een netwerkinterface [op met az network nic show-effective-route-table](/cli/azure/network/nic#az_network_nic_show_effective_route_table). In het volgende voorbeeld worden de effectieve routes voor een netwerkinterface met de *naam myVMNic1,* die zich in een resourcegroep met de *naam myResourceGroup:*

```azurecli-interactive
az network nic show-effective-route-table \
  --name myVMNic1 \
  --resource-group myResourceGroup
```

Zie Overzicht van routering voor meer informatie over de informatie die in de uitvoer [wordt geretourneerd.](virtual-networks-udr-overview.md) Uitvoer wordt alleen geretourneerd als de VM actief is. Als er meerdere netwerkinterfaces zijn gekoppeld aan de VM, kunt u de effectieve routes voor elke netwerkinterface bekijken. Omdat elke netwerkinterface zich in een ander subnet kan, kan elke netwerkinterface verschillende effectieve routes hebben. Als u nog steeds een communicatieprobleem hebt, bekijkt u [aanvullende diagnose](#additional-diagnosis) [en overwegingen.](#considerations)

Als u de naam van een netwerkinterface niet weet, maar wel de naam weet van de VM waar de netwerkinterface aan is gekoppeld, retourneren de volgende opdrachten de ID's van alle netwerkinterfaces die zijn gekoppeld aan een VM:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

## <a name="resolve-a-problem"></a>Een probleem oplossen

Het oplossen van routeringsproblemen bestaat doorgaans uit:

- Een aangepaste route toevoegen om een van de standaardroutes van Azure te overschrijven. Meer informatie over het [toevoegen van een aangepaste route.](manage-route-table.md#create-a-route)
- Wijzig of verwijder een aangepaste route die kan leiden tot routering naar een ongewenste locatie. Meer informatie over [het wijzigen](manage-route-table.md#change-a-route) of [verwijderen van](manage-route-table.md#delete-a-route) een aangepaste route.
- Ervoor zorgen dat de routetabel die aangepaste routes bevat die u hebt gedefinieerd, is gekoppeld aan het subnet waarin de netwerkinterface zich in zich. Meer informatie over het [koppelen van een routetabel aan een subnet.](manage-route-table.md#associate-a-route-table-to-a-subnet)
- Ervoor zorgen dat apparaten zoals Azure VPN-gateway of virtuele netwerkapparaten die u hebt geïmplementeerd, kunnen worden gebruikt. Gebruik de [diagnostische vpn-mogelijkheden](../network-watcher/diagnose-communication-problem-between-networks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) van Network Watcher om eventuele problemen met een Azure VPN-gateway te bepalen.

Zie Overwegingen en Aanvullende diagnose [](#considerations) als u nog steeds communicatieproblemen hebt.

## <a name="considerations"></a>Overwegingen

Houd rekening met de volgende punten bij het oplossen van communicatieproblemen:

- Routering is gebaseerd op LPM (longest prefix match) tussen routes die u hebt gedefinieerd, border gateway protocol (BGP) en systeemroutes. Als er meer dan één route met dezelfde LPM-overeenkomst is, wordt een route geselecteerd op basis van de oorsprong in de volgorde die wordt vermeld in [Routeringsoverzicht.](virtual-networks-udr-overview.md#how-azure-selects-a-route) Met effectieve routes kunt u alleen effectieve routes zien die overeenkomen met LPM, op basis van alle beschikbare routes. Als u ziet hoe de routes worden geëvalueerd voor een netwerkinterface, is het veel eenvoudiger om problemen met specifieke routes op te lossen die van invloed kunnen zijn op de communicatie van uw VM.
- Als u aangepaste routes naar een virtueel netwerkapparaat  (NVA) hebt gedefinieerd, met Virtueel apparaat als het volgende hoptype, moet u ervoor zorgen dat doorsturen via IP is ingeschakeld op de NVA die het verkeer ontvangt, of dat pakketten worden uitgevallen. Meer informatie over het [inschakelen van doorsturen via IP voor een netwerkinterface.](virtual-network-network-interface.md#enable-or-disable-ip-forwarding) Daarnaast moet het besturingssysteem of de toepassing binnen de NVA ook netwerkverkeer kunnen doorsturen en geconfigureerd zijn om dit te doen.
- Als u een route naar 0.0.0.0/0 hebt gemaakt, wordt al het uitgaande internetverkeer gerouteerd naar de volgende hop die u hebt opgegeven, zoals een NVA- of VPN-gateway. Het maken van een dergelijke route wordt vaak geforceerd tunnelen genoemd. Externe verbindingen met behulp van de RDP- of SSH-protocollen van internet naar uw VM werken mogelijk niet met deze route, afhankelijk van hoe de volgende hop het verkeer verwerkt. Geforceerd tunnelen kan worden ingeschakeld:
    - Wanneer u site-naar-site-VPN gebruikt, door een route te maken met een volgend hoptype *van VPN Gateway*. Meer informatie over [het configureren van geforceerd tunnelen.](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    - Als een 0.0.0.0/0 (standaardroute) wordt geadverteerd via BGP via een virtuele netwerkgateway wanneer u een site-naar-site-VPN of ExpressRoute-circuit gebruikt. Meer informatie over het gebruik van BGP met [een site-naar-site-VPN](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) of [ExpressRoute.](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ip-addresses-used-for-azure-private-peering)
- Om peeringverkeer voor virtuele netwerken correct te laten werken, moet er een systeemroute met een volgend hoptype *van VNet-peering* bestaan voor het voorvoegselbereik van het peered virtuele netwerk. Als een dergelijke route niet bestaat en de peeringkoppeling voor het virtuele netwerk **verbonden** is:
    - Wacht een paar seconden en het opnieuw. Als het een nieuw tot stand gebrachte peeringkoppeling is, duurt het af en toe langer om routes door te delen naar alle netwerkinterfaces in een subnet. Zie Overzicht van [peering](virtual-network-peering-overview.md) voor virtuele netwerken en peering voor virtuele netwerken beheren voor meer informatie over [peering voor virtuele netwerken.](virtual-network-manage-peering.md)
    - Regels voor netwerkbeveiligingsgroep zijn mogelijk van invloed op de communicatie. Zie Diagnose a virtual machine network traffic filter problem (Diagnose voor een [probleem met netwerkverkeersfilters](diagnose-network-traffic-filter-problem.md)op een virtuele machine) voor meer informatie.
- Hoewel Azure standaardroutes toewijst aan elke Azure-netwerkinterface, wordt aan alleen de primaire netwerkinterface een standaardroute (0.0.0.0/0) of een gateway toegewezen binnen het besturingssysteem van de VM als u meerdere netwerkinterfaces hebt. Meer informatie over het maken van een standaardroute voor secundaire netwerkinterfaces die zijn gekoppeld aan een [Windows-](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) of [Linux-VM.](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) Meer informatie over primaire [en secundaire netwerkinterfaces.](virtual-network-network-interface-vm.md#constraints)

## <a name="additional-diagnosis"></a>Aanvullende diagnose

* Als u een snelle test wilt uitvoeren om het volgende hoptype te bepalen voor verkeer dat is bestemd voor een locatie, gebruikt u de functie Volgende [hop](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) van Azure Network Watcher. Volgende hop vertelt u wat het volgende hoptype is voor verkeer dat is bestemd voor een opgegeven locatie.
* Als er geen routes zijn waardoor de netwerkcommunicatie van een VM mislukt, kan het probleem worden veroorzaakt door firewallsoftware die wordt uitgevoerd in het besturingssysteem van de VM
* Als u [](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) tunneling van verkeer naar een on-premises apparaat forceerd via een VPN-gateway of NVA, kunt u mogelijk geen verbinding maken met een VM via internet, afhankelijk van hoe u routering voor de apparaten hebt geconfigureerd. Controleer of de routering die u hebt geconfigureerd voor het apparaat verkeer routeert naar een openbaar of privé IP-adres voor de VM.
* Gebruik de [verbindingsoplossingsmogelijkheid](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) van Network Watcher om routering, filtering en oorzaken van uitgaande communicatieproblemen in het besturingssysteem te bepalen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over alle taken, eigenschappen en instellingen voor een [routetabel en routes.](manage-route-table.md)
- Meer informatie over [alle volgende hoptypen, systeemroutes en hoe Azure een route selecteert.](virtual-networks-udr-overview.md)
