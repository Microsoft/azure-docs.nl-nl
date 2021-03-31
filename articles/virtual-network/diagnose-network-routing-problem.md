---
title: Een probleem met de route ring van een virtuele machine van Azure oplossen | Microsoft Docs
description: Meer informatie over het oplossen van problemen met route ring van virtuele machines door de efficiënte routes voor een virtuele machine weer te geven.
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
ms.openlocfilehash: 1c23244707179e05c63ed44b5915e58eefd3f4a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84705046"
---
# <a name="diagnose-a-virtual-machine-routing-problem"></a>Een routerings probleem van een virtuele machine vaststellen

In dit artikel leert u hoe u een routerings probleem kunt vaststellen door de routes weer te geven die van kracht zijn voor een netwerk interface in een virtuele machine (VM). Azure maakt verschillende standaard routes voor elk subnet van een virtueel netwerk. U kunt de standaard routes van Azure onderdrukken door routes in een route tabel te definiëren en vervolgens de route tabel aan een subnet te koppelen. De combi natie van routes die u maakt, de standaard routes van Azure en alle routes die zijn door gegeven vanaf uw on-premises netwerk via een Azure VPN-gateway (als uw virtuele netwerk is verbonden met uw on-premises netwerk) via het Border Gateway Protocol (BGP), zijn de efficiënte routes voor alle netwerk interfaces in een subnet. Zie overzicht van [virtueel netwerk](virtual-networks-overview.md), [netwerk interface](virtual-network-network-interface.md)en [route ring](virtual-networks-udr-overview.md)als u niet bekend bent met de concepten van het virtuele netwerk, de netwerk interface of de route ring.

## <a name="scenario"></a>Scenario

U probeert verbinding te maken met een virtuele machine, maar de verbinding is mislukt. Als u wilt bepalen waarom u geen verbinding kunt maken met de virtuele machine, kunt u de efficiënte routes voor een netwerk interface weer geven met behulp van de Azure- [Portal](#diagnose-using-azure-portal), [Power shell](#diagnose-using-powershell)of de [Azure cli](#diagnose-using-azure-cli).

In de volgende stappen wordt ervan uitgegaan dat u een bestaande virtuele machine hebt om de juiste routes voor te bekijken. Als u geen bestaande VM hebt, implementeert u eerst een [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -of [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -VM om de taken in dit artikel uit te voeren met. De voor beelden in dit artikel zijn voor een virtuele machine met de naam *myVM* met een netwerk interface met de naam *myVMNic1*. De virtuele machine en netwerk interface bevinden zich in een resource groep met de naam *myResourceGroup* en bevinden zich in de regio *VS-Oost* . Wijzig de waarden in de stappen, indien van toepassing, voor de virtuele machine waarvoor u het probleem wilt vaststellen.

## <a name="diagnose-using-azure-portal"></a>Problemen vaststellen met behulp van Azure Portal

1. Meld u aan bij Azure [Portal](https://portal.azure.com) met een Azure-account dat over de [benodigde machtigingen](virtual-network-network-interface.md#permissions)beschikt.
2. Voer boven aan de Azure Portal de naam in van een virtuele machine die de status wordt uitgevoerd in het zoekvak. Wanneer de naam van de virtuele machine wordt weer gegeven in de zoek resultaten, selecteert u deze.
3. Onder **instellingen** aan de linkerkant selecteert u **netwerken** en navigeert u naar de netwerk interface bron door de naam ervan te selecteren.
     ![Netwerk interfaces weer geven](./media/diagnose-network-routing-problem/view-nics.png)
4. Selecteer aan de linkerkant de optie **efficiënte routes**. De actieve routes voor een netwerk interface met de naam **myVMNic1** worden weer gegeven in de volgende afbeelding: ![ werkelijke routes weer geven](./media/diagnose-network-routing-problem/view-effective-routes.png)

    Als er meerdere netwerk interfaces aan de virtuele machine zijn gekoppeld, kunt u de werkelijke routes voor elke netwerk interface weer geven door deze te selecteren. Aangezien elke netwerk interface zich in een ander subnet kan bevinden, kan elke netwerk interface verschillende efficiënte routes hebben.

    In het voor beeld dat in de vorige afbeelding wordt weer gegeven, zijn de vermelde routes standaard routes die Azure voor elk subnet maakt. De lijst bevat ten minste deze routes, maar kan extra routes bevatten, afhankelijk van de mogelijkheden die u hebt ingeschakeld voor uw virtuele netwerk, zoals het peeren met een ander virtueel netwerk of een verbinding met uw on-premises netwerk via een Azure VPN-gateway. Zie [virtueel netwerk verkeer routeren](virtual-networks-udr-overview.md)voor meer informatie over elk van de routes en andere routes die u kunt zien voor uw netwerk interface. Als uw lijst een groot aantal routes bevat, is het wellicht eenvoudiger om **downloaden** te selecteren, een. CSV-bestand te downloaden met de lijst met routes.

Hoewel bij de vorige stappen daad werkelijke routes werden bekeken via de VM, kunt u ook efficiënte routes weer geven via een:
- **Afzonderlijke netwerk interface**: meer informatie over het [weer geven van een netwerk interface](virtual-network-network-interface.md#view-network-interface-settings).
- **Afzonderlijke route tabel**: informatie over het [weer geven van een route tabel](manage-route-table.md#view-details-of-a-route-table).

## <a name="diagnose-using-powershell"></a>Problemen vaststellen met Power shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U kunt de opdrachten uitvoeren die volgen in de [Azure Cloud shell](https://shell.azure.com/powershell), of door Power shell uit te voeren vanaf uw computer. De Azure Cloud Shell is een gratis interactieve shell. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Als u Power shell vanaf uw computer uitvoert, hebt u de Azure PowerShell module versie 1.0.0 of hoger nodig. Voer uit `Get-Module -ListAvailable Az` op uw computer om de geïnstalleerde versie te vinden. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps). Als u Power shell lokaal uitvoert, moet u ook uitvoeren `Connect-AzAccount` om u aan te melden bij Azure met een account dat over de [benodigde machtigingen](virtual-network-network-interface.md#permissions)beschikt.

Haal de efficiënte routes op voor een netwerk interface met [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable). In het volgende voor beeld worden de efficiënte routes opgehaald voor een netwerk interface met de naam *myVMNic1*, die zich in een resource groep met de naam *myResourceGroup* bevindt:

```azurepowershell-interactive
Get-AzEffectiveRouteTable `
  -NetworkInterfaceName myVMNic1 `
  -ResourceGroupName myResourceGroup `
  | Format-Table
```

Zie [route ring Overview](virtual-networks-udr-overview.md)voor meer informatie over de gegevens die in de uitvoer worden geretourneerd. De uitvoer wordt alleen geretourneerd als de virtuele machine de status actief heeft. Als er meerdere netwerk interfaces aan de virtuele machine zijn gekoppeld, kunt u de werkelijke routes voor elke netwerk interface controleren. Aangezien elke netwerk interface zich in een ander subnet kan bevinden, kan elke netwerk interface verschillende efficiënte routes hebben. Als u nog steeds een communicatie probleem hebt, raadpleegt u [aanvullende diagnose](#additional-diagnosis) en [overwegingen](#considerations).

Als u de naam van een netwerk interface niet kent, maar wel de naam weet van de VM waaraan de netwerk interface is gekoppeld, retour neren de volgende opdrachten de Id's van alle netwerk interfaces die zijn gekoppeld aan een virtuele machine:

```azurepowershell-interactive
$VM = Get-AzVM -Name myVM `
  -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

U ontvangt uitvoer die er ongeveer zo uitziet als in het volgende voor beeld:

```powershell
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMNic1
```

In de vorige uitvoer is de naam van de netwerk interface *myVMNic1*.

## <a name="diagnose-using-azure-cli"></a>Diagnoses uitvoeren met Azure CLI

U kunt de opdrachten uitvoeren die volgen in de  [Azure Cloud shell](https://shell.azure.com/bash), of door de CLI vanaf uw computer uit te voeren. Voor dit artikel is de Azure CLI-versie 2.0.32 of hoger vereist. Voer `az --version` uit om te kijken welke versie is geïnstalleerd. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren. Als u de Azure CLI lokaal uitvoert, moet u ook uitvoeren en u `az login` Aanmelden bij Azure met een account dat over de [benodigde machtigingen](virtual-network-network-interface.md#permissions)beschikt.

Haal de meest efficiënte routes op voor een netwerk interface met [AZ Network NIC show-ingangsdatum-route tabel](/cli/azure/network/nic#az-network-nic-show-effective-route-table). In het volgende voor beeld worden de efficiënte routes opgehaald voor een netwerk interface met de naam *myVMNic1* die zich in een resource groep met de naam *myResourceGroup* bevindt:

```azurecli-interactive
az network nic show-effective-route-table \
  --name myVMNic1 \
  --resource-group myResourceGroup
```

Zie [route ring Overview](virtual-networks-udr-overview.md)voor meer informatie over de gegevens die in de uitvoer worden geretourneerd. De uitvoer wordt alleen geretourneerd als de virtuele machine de status actief heeft. Als er meerdere netwerk interfaces aan de virtuele machine zijn gekoppeld, kunt u de werkelijke routes voor elke netwerk interface controleren. Aangezien elke netwerk interface zich in een ander subnet kan bevinden, kan elke netwerk interface verschillende efficiënte routes hebben. Als u nog steeds een communicatie probleem hebt, raadpleegt u [aanvullende diagnose](#additional-diagnosis) en [overwegingen](#considerations).

Als u de naam van een netwerk interface niet kent, maar wel de naam weet van de VM waaraan de netwerk interface is gekoppeld, retour neren de volgende opdrachten de Id's van alle netwerk interfaces die zijn gekoppeld aan een virtuele machine:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

## <a name="resolve-a-problem"></a>Een probleem oplossen

Het oplossen van routerings problemen bestaat doorgaans uit:

- Er wordt een aangepaste route toegevoegd voor het overschrijven van een van de standaard routes van Azure. Meer informatie over het [toevoegen van een aangepaste route](manage-route-table.md#create-a-route).
- Een aangepaste route wijzigen of verwijderen waardoor route ring naar een ongewenste locatie kan leiden. Meer informatie over het [wijzigen](manage-route-table.md#change-a-route) of [verwijderen](manage-route-table.md#delete-a-route) van een aangepaste route.
- Zorg ervoor dat de route tabel die aangepaste routes bevat die u hebt gedefinieerd, is gekoppeld aan het subnet waarin de netwerk interface zich bevindt. Meer informatie over het [koppelen van een route tabel aan een subnet](manage-route-table.md#associate-a-route-table-to-a-subnet).
- Ervoor zorgen dat apparaten zoals Azure VPN-gateway of virtuele netwerk apparaten die u hebt geïmplementeerd, kunnen worden gebruikt. Gebruik de functie voor [Diagnostische gegevens van VPN](../network-watcher/diagnose-communication-problem-between-networks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) van Network Watcher om problemen met een Azure VPN-gateway te bepalen.

Als u nog steeds communicatie problemen ondervindt, raadpleegt u [overwegingen](#considerations) en aanvullende diagnose.

## <a name="considerations"></a>Overwegingen

Houd rekening met de volgende punten bij het oplossen van communicatie problemen:

- Route ring is gebaseerd op het langste voor voegsel (LPM) tussen de routes die u hebt gedefinieerd, het Border Gateway Protocol (BGP) en de systeem routes. Als er meer dan één route met dezelfde LPM overeenkomt, wordt een route geselecteerd op basis van de oorsprong in de volg orde die wordt vermeld in [route ring-overzicht](virtual-networks-udr-overview.md#how-azure-selects-a-route). Met behulp van efficiënte routes kunt u alleen efficiënte routes weer geven die overeenkomen met de waarde van LPM, op basis van alle beschik bare routes. Wanneer u ziet hoe de routes worden geëvalueerd voor een netwerk interface, is het veel eenvoudiger om specifieke routes op te lossen die van invloed kunnen zijn op de communicatie van uw VM.
- Als u aangepaste routes hebt gedefinieerd voor een virtueel netwerk apparaat (NVA), met *virtueel apparaat* als het volgende hop-type, moet u ervoor zorgen dat door sturen via IP is ingeschakeld op de NVA die het verkeer ontvangt, of pakketten worden verwijderd. Meer informatie over [het inschakelen van door sturen via IP voor een netwerk interface](virtual-network-network-interface.md#enable-or-disable-ip-forwarding). Daarnaast moet het besturings systeem of de toepassing binnen de NVA ook netwerk verkeer kunnen door sturen en zo worden geconfigureerd.
- Als u een route naar 0.0.0.0/0 hebt gemaakt, wordt al het uitgaande Internet verkeer doorgestuurd naar de volgende hop die u hebt opgegeven, bijvoorbeeld naar een NVA of VPN-gateway. Het maken van een dergelijke route wordt vaak geforceerde tunneling genoemd. Externe verbindingen met de RDP-of SSH-protocollen van het internet naar uw virtuele machine werken mogelijk niet met deze route, afhankelijk van de manier waarop de volgende hop het verkeer afhandelt. Geforceerde tunneling kan worden ingeschakeld:
    - Wanneer u site-naar-site-VPN gebruikt, maakt u een route met het type van de volgende hop van *VPN gateway*. Meer informatie over het [configureren van geforceerde tunneling](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - Als een 0.0.0.0/0 (standaard route) via BGP wordt geadverteerd via een virtuele netwerk gateway wanneer u een site-naar-site VPN-of ExpressRoute-circuit gebruikt. Meer informatie over het gebruik van BGP met een [site-naar-site VPN-](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) of [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ip-addresses-used-for-azure-private-peering).
- Om het peering-verkeer van virtuele netwerken correct te laten werken, moet er een systeem route met het type van de volgende hop van *VNet-peering* bestaan voor het voor voegsel van het gekoppelde virtuele netwerk. Als een dergelijke route niet bestaat, is de koppeling van de peering van het virtuele netwerk **verbonden**:
    - Wacht een paar seconden en probeer het opnieuw. Als het een nieuw vastgelegde peering-koppeling is, duurt het af en toe langer om routes door te geven aan alle netwerk interfaces in een subnet. Zie [overzicht van virtuele netwerk peering](virtual-network-peering-overview.md) en [beheer van peering](virtual-network-manage-peering.md)op het virtuele netwerk voor meer informatie over peering in virtuele netwerken.
    - De regels voor de netwerk beveiligings groep kunnen invloed hebben op de communicatie. Zie [een probleem met het netwerk verkeer van een virtuele machine vaststellen](diagnose-network-traffic-filter-problem.md)voor meer informatie.
- Azure wijst standaard routes toe aan elke Azure-netwerk interface, als er meerdere netwerk interfaces zijn gekoppeld aan de virtuele machine, wordt alleen aan de primaire netwerk interface een standaard route (0.0.0.0/0) of gateway in het besturings systeem van de virtuele machine toegewezen. Meer informatie over het maken van een standaard route voor secundaire netwerk interfaces die zijn gekoppeld aan een [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) -of [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) -VM. Meer informatie over de [primaire en secundaire netwerk interfaces](virtual-network-network-interface-vm.md#constraints).

## <a name="additional-diagnosis"></a>Aanvullende diagnose

* Als u een snelle test wilt uitvoeren om het type van de volgende hop te bepalen voor verkeer dat is bestemd voor een locatie, gebruikt u de [volgende hop](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -mogelijkheid van Azure Network Watcher. De volgende hop vertelt u wat het type van de volgende hop is voor verkeer dat is bestemd voor een opgegeven locatie.
* Als er geen routes zijn die de netwerk communicatie van een virtuele machine laten mislukken, kan het probleem worden veroorzaakt door Firewall software die wordt uitgevoerd in het besturings systeem van de virtuele machine.
* Als u het verkeer naar een on-premises apparaat [afdwingt](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) via een VPN-gateway of NVA, is het mogelijk dat u geen verbinding kunt maken met een virtuele machine via internet, afhankelijk van hoe u route ring voor de apparaten hebt geconfigureerd. Controleer of de route ring die u voor het apparaat hebt geconfigureerd, verkeer routeert naar een openbaar of particulier IP-adres voor de virtuele machine.
* Gebruik de [verbindings](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) functie voor het oplossen van problemen met de mogelijkheden van Network Watcher om route ring, filters en in-OS oorzaken van uitgaande communicatie problemen vast te stellen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over alle taken, eigenschappen en instellingen voor een [route tabel en routes](manage-route-table.md).
- Meer informatie over alle typen van de [volgende hop, systeem routes en hoe Azure een route selecteert](virtual-networks-udr-overview.md).
