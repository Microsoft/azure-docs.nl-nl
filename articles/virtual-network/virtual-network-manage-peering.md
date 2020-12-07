---
title: Een Azure Virtual Network-peering maken, wijzigen of verwijderen | Microsoft Docs
description: Maken, wijzigen of verwijderen van de peering van een virtueel netwerk. Met Virtual Network-peering verbindt u virtuele netwerken in dezelfde regio en in verschillende regio's.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: altambaw
ms.openlocfilehash: b5099f839da4547ab9551ea131fa65100d25fe65
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96763040"
---
# <a name="create-change-or-delete-a-virtual-network-peering"></a>Een peering op een virtueel netwerk maken, wijzigen of verwijderen

Meer informatie over het maken, wijzigen of verwijderen van peering op een virtueel netwerk. Met Virtual Network-peering kunt u virtuele netwerken in dezelfde regio en tussen regio's (ook wel bekend als Global VNet-peering) verbinden via het Azure-backbone-netwerk. Als de virtuele netwerken zijn gekoppeld, worden ze nog steeds als afzonderlijke bronnen beheerd. Als u niet bekend bent met peering op het virtuele netwerk, kunt u meer informatie hierover vinden in het [overzicht van peering in het virtuele netwerk](virtual-network-peering-overview.md) of door een [zelf studie](tutorial-connect-virtual-networks-portal.md)uit te voeren.

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Voer de volgende taken uit voordat u de stappen in een van de secties van dit artikel uitvoert:

- Als u nog geen Azure-account hebt, kunt u zich aanmelden voor een [gratis proef account](https://azure.microsoft.com/free).
- Als u de portal gebruikt, opent u https://portal.azure.com en meldt u zich aan met een account dat de [benodigde machtigingen](#permissions) heeft om met peerings te werken.
- Als u Power shell-opdrachten gebruikt om taken in dit artikel te volt ooien, moet u de opdrachten uitvoeren in de [Azure Cloud shell](https://shell.azure.com/powershell)of Power shell uitvoeren vanaf uw computer. Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Voor deze zelf studie is de Azure PowerShell module versie 1.0.0 of hoger vereist. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u Power shell lokaal uitvoert, moet u ook uitvoeren `Connect-AzAccount` met een account dat beschikt over de [vereiste machtigingen](#permissions) om te werken met peering om een verbinding te maken met Azure.
- Als u Azure-opdracht regel interface opdrachten gebruikt om taken in dit artikel te volt ooien, moet u de opdrachten uitvoeren in de [Azure Cloud shell](https://shell.azure.com/bash)of door de CLI vanaf uw computer uit te voeren. Voor deze zelf studie is de Azure CLI-versie 2.0.31 of hoger vereist. Voer `az --version` uit om te kijken welke versie is geïnstalleerd. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren. Als u de Azure CLI lokaal uitvoert, moet u ook uitvoeren `az login` met een account met de [vereiste machtigingen](#permissions) om met peering te werken, om een verbinding te maken met Azure.

Het account waarmee u zich aanmeldt of verbinding maakt met Azure met, moet worden toegewezen aan de rol [netwerk bijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) of aan een [aangepaste rol](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) waaraan de juiste acties zijn toegewezen die worden vermeld in [machtigingen](#permissions).

## <a name="create-a-peering"></a>Een peering maken

Voordat u een peering maakt, moet u vertrouwd raken met de vereisten en beperkingen en de [vereiste machtigingen](#permissions).

1. Voer in het zoekvak boven aan de Azure Portal *virtuele netwerken* in het zoekvak in. Wanneer **virtuele netwerken** worden weer gegeven in de zoek resultaten, selecteert u deze. Selecteer geen **virtuele netwerken (klassiek)** als deze in de lijst worden weer gegeven, omdat u geen peering kunt maken van een virtueel netwerk dat is geïmplementeerd via het klassieke implementatie model.
2. Selecteer het virtuele netwerk in de lijst waarvoor u een peering wilt maken.
3. Selecteer onder **instellingen** de optie **peerings**.
4. Selecteer **+ Toevoegen**. 
5. <a name="add-peering"></a>Typ of selecteer waarden voor de volgende instellingen:
    - **Naam:** De naam voor de peering moet uniek zijn binnen het virtuele netwerk.
    - **Implementatie model van het virtuele netwerk:** Selecteer het implementatie model van het virtuele netwerk waarmee u een peer wilt uitvoeren, dat is geïmplementeerd via.
    - **Ik weet wat mijn resource-id is:** Als u lees toegang hebt tot het virtuele netwerk waarmee u wilt peeren, laat u dit selectie vakje uitgeschakeld. Schakel dit selectie vakje in als u geen lees toegang hebt tot het virtuele netwerk of het abonnement waarmee u wilt worden gepeerd. Voer de volledige Resource-ID in van het virtuele netwerk waarmee u een peer wilt doen, in het vak **resource-id** dat werd weer gegeven wanneer u het selectie vakje inschakelt. De resource-ID die u invoert, moet voor een virtueel netwerk zijn dat zich in dezelfde of een [ondersteunde](#requirements-and-constraints) Azure- [regio](https://azure.microsoft.com/regions) bevindt als dit virtuele netwerk. De volledige Resource-ID ziet er als volgt uit `/subscriptions/<Id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>` . U kunt de resource-ID voor een virtueel netwerk ophalen door de eigenschappen voor een virtueel netwerk weer te geven. Zie [virtuele netwerken beheren](manage-virtual-network.md#view-virtual-networks-and-settings)voor meer informatie over het weer geven van de eigenschappen voor een virtueel netwerk. Als het abonnement is gekoppeld aan een andere Azure Active Directory Tenant dan het abonnement met het virtuele netwerk waarvan u de peering maakt, voegt u eerst een gebruiker toe van elke Tenant als [gast gebruiker](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) in de tegenoverliggende Tenant.
    - **Abonnement:** Selecteer het [abonnement](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) van het virtuele netwerk waarmee u een peer wilt met. Een of meer abonnementen worden weer gegeven, afhankelijk van het aantal abonnementen waarvoor uw account lees toegang heeft. Als u het selectie vakje voor de **resource-id** hebt ingeschakeld, is deze instelling niet beschikbaar.
    - **Virtueel netwerk:** Selecteer het virtuele netwerk waarmee u een peer wilt met. U kunt een virtueel netwerk selecteren dat is gemaakt via een Azure-implementatie model. Als u een virtueel netwerk in een andere regio wilt selecteren, moet u een virtueel netwerk in een [ondersteunde regio](#cross-region)selecteren. U moet lees toegang tot het virtuele netwerk hebben om te kunnen worden weer gegeven in de lijst. Als een virtueel netwerk wordt vermeld, maar grijs wordt weer gegeven, kan het zijn dat de adres ruimte voor het virtuele netwerk overlapt met de adres ruimte voor dit virtuele netwerk. Als de adres ruimten van het virtuele netwerk elkaar overlappen, kunnen ze niet worden gekoppeld. Als u het selectie vakje voor de **resource-id** hebt ingeschakeld, is deze instelling niet beschikbaar.
    - **Toegang tot virtueel netwerk toestaan:** Selecteer **ingeschakeld** (standaard) als u communicatie tussen de twee virtuele netwerken wilt inschakelen via de standaard `VirtualNetwork` stroom. Door communicatie tussen virtuele netwerken in te scha kelen, kunnen resources die zijn verbonden met een virtueel netwerk communiceren met elkaar met dezelfde band breedte en latentie alsof ze zijn verbonden met hetzelfde virtuele netwerk. Alle communicatie tussen resources in de twee virtuele netwerken bevindt zich in het particuliere netwerk van Azure. Het **VirtualNetwork** -service label voor netwerk beveiligings groepen omvat het virtuele netwerk en het gekoppelde virtuele netwerk als deze instelling is **ingeschakeld**. (Zie [overzicht van netwerk beveiligings groepen](security-overview.md#service-tags)voor meer informatie over de service tags voor de netwerk beveiligings groep.) Selecteer **uitgeschakeld** als u niet wilt dat verkeer standaard naar het gekoppelde virtuele netwerk stromen. U kunt **uitgeschakeld** selecteren als u een virtueel netwerk met een ander virtueel netwerk hebt gekoppeld, maar af en toe wilt uitschakelen de standaard verkeers stroom tussen de twee virtuele netwerken. Het is mogelijk dat u het in-en uitschakelen van de peers kunt vergemakkelijken. Als deze instelling is uitgeschakeld, loopt verkeer niet standaard tussen de gekoppelde virtuele netwerken. verkeer kan echter nog steeds stroom lopen als dit expliciet wordt toegestaan via een regel voor [netwerk beveiligings groepen](security-overview.md) die de juiste IP-adressen of toepassings beveiligings groepen bevat.
      > [!WARNING]
      > Als u de instelling **toegang tot virtueel netwerk toestaan** uitschakelt, wordt alleen de definitie van de **VirtualNetwork** -servicetag gewijzigd. Het verkeer wordt *niet* volledig voor komen via de peer verbinding, zoals wordt uitgelegd in de beschrijving van deze instelling.    
    - **Doorgestuurd verkeer toestaan:** Schakel dit selectie vakje in om verkeer dat wordt *doorgestuurd* door een virtueel netwerk apparaat in een virtueel netwerk (dat niet afkomstig is van het virtuele netwerk), te laten door lopen op dit virtuele netwerk via een peering. Denk bijvoorbeeld aan drie virtuele netwerken met de naam Spoke1, Spoke2 en hub. Er bestaat een peering tussen elk spoke-virtueel netwerk en het virtuele hub-netwerk, maar er zijn geen peerings tussen de spoke-virtuele netwerken. Een virtueel netwerk apparaat wordt geïmplementeerd in het virtuele netwerk van de hub en door de gebruiker gedefinieerde routes worden toegepast op elk spoke-virtueel netwerk dat verkeer tussen de subnetten via het virtuele netwerk apparaat routeert. Als dit selectie vakje niet is ingeschakeld voor de peering tussen elk spoke-virtueel netwerk en het virtuele netwerk van de hub, loopt verkeer niet tussen de spoke-virtuele netwerken omdat de hub het verkeer tussen de virtuele netwerken niet doorstuurt. Hoewel het inschakelen van deze mogelijkheid het doorgestuurde verkeer via de peering toestaat, worden er geen door de gebruiker gedefinieerde routes of virtuele netwerk apparaten gemaakt. Door de gebruiker gedefinieerde routes en virtuele netwerk apparaten worden afzonderlijk gemaakt. Meer informatie over door de [gebruiker gedefinieerde routes](virtual-networks-udr-overview.md#user-defined). U hoeft deze instelling niet te controleren als verkeer wordt doorgestuurd tussen virtuele netwerken via een Azure-VPN Gateway.
    - **Transit door gateway toestaan:** Schakel dit selectie vakje in als u een virtuele netwerk gateway hebt die is gekoppeld aan dit virtuele netwerk en verkeer wilt toestaan van het gekoppelde virtuele netwerk om via de gateway te stromen. Dit virtuele netwerk kan bijvoorbeeld worden gekoppeld aan een on-premises netwerk via een virtuele netwerk gateway. De gateway kan een ExpressRoute of een VPN-gateway zijn. Als u dit selectie vakje inschakelt, kan verkeer van het gekoppelde virtuele netwerk stromen via de gateway die is gekoppeld aan dit virtuele netwerk aan het on-premises netwerk. Als u dit selectie vakje inschakelt, kan er geen gateway worden geconfigureerd in het gekoppelde virtuele netwerk. Voor het gekoppelde virtuele netwerk moet het selectie vakje **externe gateways gebruiken** zijn ingeschakeld bij het instellen van de peering van het andere virtuele netwerk op dit virtuele netwerk. Als u dit selectie vakje niet inschakelt, wordt verkeer van het gekoppelde virtuele netwerk nog steeds doorverbonden met dit virtuele netwerk, maar kan er geen stroom worden gemaakt door een virtuele netwerk gateway die is gekoppeld aan dit virtuele netwerk. Als de peering tussen een virtueel netwerk (Resource Manager) en een virtueel netwerk (klassiek) is, moet de gateway zich in het virtuele netwerk (Resource Manager) bevindt.

       Naast het door sturen van verkeer naar een on-premises netwerk, kan een VPN-gateway netwerk verkeer door sturen tussen virtuele netwerken die zijn gekoppeld aan het virtuele netwerk waarin de gateway zich bevindt, zonder dat de virtuele netwerken met elkaar moeten worden gekoppeld. Het gebruik van een VPN-gateway voor het door sturen van verkeer is handig als u een VPN-gateway in een hub wilt gebruiken (Zie het voor beeld van hub en spoke dat is beschreven voor het **toestaan van doorgestuurd verkeer**) virtueel netwerk verkeer routeren tussen spoke-virtuele netwerken die niet met elkaar zijn verbonden. Zie [een VPN-gateway configureren voor door Voer in een virtueel netwerk peering](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie over het toestaan van het gebruik van een gateway voor door voer. Dit scenario vereist het implementeren van door de gebruiker gedefinieerde routes waarmee de virtuele netwerk gateway wordt opgegeven als het type van de volgende hop. Meer informatie over door de [gebruiker gedefinieerde routes](virtual-networks-udr-overview.md#user-defined). U kunt een VPN-gateway alleen opgeven als een type volgende hop in een door de gebruiker gedefinieerde route. u kunt geen ExpressRoute-gateway opgeven als het type van de volgende hop in een door de gebruiker gedefinieerde route.

    - **Externe gateways gebruiken:** Schakel dit selectie vakje in om verkeer van dit virtuele netwerk te laten stromen via een virtuele netwerk gateway die is gekoppeld aan het virtuele netwerk waarmee u peering uitvoert. Het virtuele netwerk waarmee u een peer maakt, heeft bijvoorbeeld een VPN-gateway die is gekoppeld aan een on-premises netwerk.  Als u dit selectie vakje inschakelt, kan verkeer van dit virtuele netwerk stromen via de VPN-gateway die is gekoppeld aan het gekoppelde virtuele netwerk. Als u dit selectie vakje inschakelt, moet er een virtuele netwerk gateway aan zijn gekoppeld aan het gekoppelde virtuele netwerk en moet het selectie vakje **Gateway doorvoer toestaan** zijn ingeschakeld. Als u dit selectie vakje uitschakelt (standaard), kan verkeer van het gekoppelde virtuele netwerk nog steeds naar dit virtuele netwerk stromen, maar kan er geen stroom worden gemaakt door een virtuele netwerk gateway die is gekoppeld aan dit virtuele netwerk.
    
      Deze instelling kan alleen worden ingeschakeld voor één peering voor dit virtuele netwerk.

      U kunt geen externe gateways gebruiken als u al een gateway in uw virtuele netwerk hebt geconfigureerd. Zie [een VPN-gateway configureren voor door Voer in een virtueel netwerk peering](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json) voor meer informatie over het gebruik van een gateway voor door voer.
        
    > [!NOTE]
    > Als u een Virtual Network gateway gebruikt voor het verzenden van on-premises verkeer dat transiteert naar een gepeerd VNet, moet het gepeerde VNet IP-bereik voor het on-premises VPN-apparaat worden ingesteld op interessant verkeer. Anders kunnen uw on-premises resources niet communiceren met resources in het gekoppelde VNet.

6. Selecteer **OK** om de peering toe te voegen aan het virtuele netwerk dat u hebt geselecteerd.

Zie [volgende stappen](#next-steps)voor stapsgewijze instructies voor het implementeren van peering tussen virtuele netwerken in verschillende abonnementen en implementatie modellen.

### <a name="commands"></a>Opdracht

- **Azure cli**: [AZ Network vnet peering Create](/cli/azure/network/vnet/peering)
- **Power shell**: [add-AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering)

## <a name="view-or-change-peering-settings"></a>Instellingen voor peering weer geven of wijzigen

Voordat u een peering wijzigt, moet u vertrouwd raken met de vereisten en beperkingen en de [vereiste machtigingen](#permissions).

1. Voer in het zoekvak boven aan de portal *virtuele netwerken* in het zoekvak in. Wanneer **virtuele netwerken** worden weer gegeven in de zoek resultaten, selecteert u deze. Selecteer geen **virtuele netwerken (klassiek)** als deze in de lijst worden weer gegeven, omdat u geen peering kunt maken van een virtueel netwerk dat is geïmplementeerd via het klassieke implementatie model.
2. Selecteer het virtuele netwerk in de lijst waarvoor u de instellingen voor peering wilt wijzigen.
3. Selecteer onder **instellingen** de optie **peerings**.
4. Selecteer de peering waarvan u de instellingen wilt weer geven of wijzigen.
5. Wijzig de juiste instelling. Meer informatie over de opties voor elke instelling in [stap 5](#add-peering) van een peering maken.
6. Selecteer **Opslaan**.

**Opdrachten**

- **Azure cli**: [AZ Network vnet peering List](/cli/azure/network/vnet/peering) to list peerings voor een virtueel netwerk, [AZ Network vnet peering laat](/cli/azure/network/vnet/peering) instellingen weer geven voor een specifieke peering en [AZ Network vnet peering update](/cli/azure/network/vnet/peering) om instellingen voor peering te wijzigen. |
- **Power shell**: [Get-AzVirtualNetworkPeering](/powershell/module/az.network/get-azvirtualnetworkpeering) voor het ophalen van instellingen voor het weer geven van peering en [set-AzVirtualNetworkPeering](/powershell/module/az.network/set-azvirtualnetworkpeering) om instellingen te wijzigen.

## <a name="delete-a-peering"></a>Een peering verwijderen

Voordat u een peering verwijdert, moet u controleren of uw account over de [benodigde machtigingen](#permissions)beschikt.

Wanneer een peering wordt verwijderd, loopt verkeer van een virtueel netwerk niet meer door naar het gekoppelde virtuele netwerk. Wanneer virtuele netwerken die via Resource Manager zijn geïmplementeerd, zijn gekoppeld, heeft elk virtueel netwerk een peering met het andere virtuele netwerk. Hoewel het verwijderen van de peering van één virtueel netwerk de communicatie tussen de virtuele netwerken uitschakelt, wordt de peering niet verwijderd uit het andere virtuele netwerk. De peering-status voor de peering die bestaat in het andere virtuele netwerk is **verbroken**. U kunt de peering pas opnieuw maken wanneer u de peering opnieuw hebt gemaakt in het eerste virtuele netwerk en de peering-status voor beide virtuele netwerken verandert in *verbonden*.

Als u wilt dat virtuele netwerken soms communiceren, maar niet altijd, in plaats van een peering te verwijderen, kunt u in plaats daarvan de instelling voor **toegang tot virtueel netwerk toestaan** instellen op **uitgeschakeld** . Lees stap 6 van de sectie een peering maken in dit artikel voor meer informatie. Mogelijk is het uitschakelen en inschakelen van netwerk toegang eenvoudiger dan het verwijderen en opnieuw maken van peerings.

1. Voer in het zoekvak boven aan de portal *virtuele netwerken* in het zoekvak in. Wanneer **virtuele netwerken** worden weer gegeven in de zoek resultaten, selecteert u deze. Selecteer geen **virtuele netwerken (klassiek)** als deze in de lijst worden weer gegeven, omdat u geen peering kunt maken van een virtueel netwerk dat is geïmplementeerd via het klassieke implementatie model.
2. Selecteer het virtuele netwerk in de lijst waarvoor u een peering wilt verwijderen.
3. Selecteer onder **instellingen** de optie **peerings**.
4. Selecteer aan de rechter kant van de peering die u wilt verwijderen **...**, selecteer **verwijderen** en selecteer vervolgens **Ja** om de peering uit het eerste virtuele netwerk te verwijderen.
5. Voltooi de voor gaande stappen om de peering te verwijderen uit het andere virtuele netwerk in de peering.

**Opdrachten**

- **Azure cli**: [AZ Network vnet peering verwijderen](/cli/azure/network/vnet/peering)
- **Power shell**: [Remove-AzVirtualNetworkPeering](/powershell/module/az.network/remove-azvirtualnetworkpeering)

## <a name="requirements-and-constraints"></a>Vereisten en beperkingen

- <a name="cross-region"></a>U kunt virtuele netwerken in dezelfde regio of in verschillende regio's peeren. Peering van virtuele netwerken in verschillende regio's wordt ook wel *wereld wijde VNet-peering* genoemd. 
- Wanneer u een wereld wijde peering maakt, kunnen de gekoppelde virtuele netwerken bestaan in alle open bare Cloud regio's van Azure of in de Cloud regio's in de Cloud. U kunt geen peers tussen verschillende Clouds. Een VNet in de open bare Azure-Cloud kan bijvoorbeeld niet worden gekoppeld aan een VNet in azure China-Cloud.
- Resources in het ene virtueel netwerk kunnen niet communiceren met het front-end IP-adres van een interne Basic-load balancer in een wereldwijd gekoppeld virtueel netwerk. Ondersteuning voor Basic-load balancers bestaat alleen binnen dezelfde regio. Ondersteuning voor Standard-load balancers bestaat voor zowel VNet-peering als Global VNet-peering. Services die gebruikmaken van een basis load balancer die niet meer werken dan wereld wijde VNet-peering, worden [hier beschreven.](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)
- U kunt externe gateways gebruiken of gateway doorvoer toestaan in wereld wijd gekoppelde virtuele netwerken en lokaal peered virtuele netwerken.
- De virtuele netwerken kunnen zich in dezelfde of verschillende abonnementen bevindt. Wanneer u virtuele netwerken in verschillende abonnementen peert, kunnen beide abonnementen worden gekoppeld aan dezelfde of een andere Azure Active Directory Tenant. Als u nog geen AD-Tenant hebt, kunt u [er een maken](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json-a-new-azure-ad-tenant).
- De virtuele netwerken die u peert, moeten niet-overlappende IP-adres ruimten hebben.
- U kunt geen adresbereiken toevoegen aan of verwijderen uit de adres ruimte van een virtueel netwerk wanneer een virtueel netwerk is gekoppeld aan een ander virtueel netwerk. Om adresbereiken toe te voegen of te verwijderen, verwijdert u de peering, voegt u de adresbereiken toe of verwijdert u de peering en maakt u deze opnieuw. Zie [virtuele netwerken beheren](manage-virtual-network.md)als u adresbereiken wilt toevoegen aan of adresbereiken uit virtuele netwerken wilt verwijderen.
- U kunt twee virtuele netwerken die zijn geïmplementeerd met Resource Manager of een virtueel netwerk dat is geïmplementeerd via Resource Manager, peeren met een virtueel netwerk dat is geïmplementeerd via het klassieke implementatie model. U kunt niet twee virtuele netwerken peeren die zijn gemaakt via het klassieke implementatie model. Als u niet bekend bent met Azure-implementatie modellen, lees dan het artikel over [Azure-implementatie modellen begrijpen](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) . U kunt [VPN Gateway](../vpn-gateway/design.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) gebruiken om twee virtuele netwerken te koppelen die zijn gemaakt via het klassieke implementatiemodel.
- Wanneer u twee virtuele netwerken koppelt die zijn gemaakt via Resource Manager, moet voor elk virtueel netwerk een peering worden geconfigureerd. U ziet een van de volgende typen voor de peering-status: 
  - *Gestart:* Wanneer u de peering maakt voor het tweede virtuele netwerk vanuit het eerste virtuele netwerk, wordt de status van de peering *geïnitieerd*. 
  - *Verbonden:* Wanneer u de peering van het tweede virtuele netwerk naar het eerste virtuele netwerk maakt, is de peering-status *verbonden*. Als u de status van de peering voor het eerste virtuele netwerk bekijkt, ziet u dat de status is gewijzigd van *geactiveerd* in *verbonden*. De peering wordt pas tot stand gebracht als de peering-status voor beide peering van virtuele netwerken is *verbonden*.
- Bij het koppelen van een virtueel netwerk dat is gemaakt via Resource Manager met een virtueel netwerk dat is gemaakt via het klassieke implementatie model, configureert u alleen een peering voor het virtuele netwerk dat is geïmplementeerd via Resource Manager. U kunt peering niet configureren voor een virtueel netwerk (klassiek) of tussen twee virtuele netwerken die zijn geïmplementeerd via het klassieke implementatie model. Wanneer u de peering van het virtuele netwerk (Resource Manager) naar het virtuele netwerk (klassiek) maakt, wordt de status van de peering *bijgewerkt* en worden er vervolgens binnenkort wijzigingen in *verbonden*.
- Er wordt een peering tot stand gebracht tussen twee virtuele netwerken. Peerings op zichzelf zijn niet transitief. Als u peerings maakt tussen:
  - VirtualNetwork1 & VirtualNetwork2-VirtualNetwork1 & VirtualNetwork2
  - VirtualNetwork2 & VirtualNetwork3-VirtualNetwork2 & VirtualNetwork3


  Er is geen peering tussen VirtualNetwork1 en VirtualNetwork3 via VirtualNetwork2. Als u een virtuele netwerk peering tussen VirtualNetwork1 en VirtualNetwork3 wilt maken, moet u een peering maken tussen VirtualNetwork1 en VirtualNetwork3. Er is geen peering tussen VirtualNetwork1 en VirtualNetwork3 via VirtualNetwork2. Als u wilt dat VirtualNetwork1 en VirtualNetwork3 rechtstreeks communiceren, moet u een expliciete peering maken tussen VirtualNetwork1 en VirtualNetwork3 of door lopen van een NVA in het hub-netwerk.  
- U kunt geen namen omzetten in gekoppelde virtuele netwerken met behulp van de standaard naam omzetting van Azure. Als u namen in andere virtuele netwerken wilt omzetten, moet u [Azure DNS gebruiken voor particuliere domeinen](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) of een aangepaste DNS-server. Zie [naam omzetting met uw eigen DNS-server](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)voor meer informatie over het instellen van uw eigen DNS-server.
- Resources in gekoppelde virtuele netwerken in dezelfde regio kunnen met elkaar communiceren met dezelfde band breedte en latentie alsof ze zich in hetzelfde virtuele netwerk bevinden. De grootte van elke virtuele machine heeft echter een eigen maximale netwerk bandbreedte. Zie grootten van virtuele machines voor [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) of [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) voor meer informatie over de maximale netwerk bandbreedte voor verschillende groottes van virtuele machines.
- Een virtueel netwerk kan worden gekoppeld aan een ander virtueel netwerk en ook worden verbonden met een ander virtueel netwerk met een virtuele Azure-netwerk gateway. Wanneer virtuele netwerken zijn verbonden via peering en een gateway, loopt verkeer tussen de virtuele netwerken via de peering-configuratie, in plaats van de gateway.
- Punt-naar-site-VPN-clients moeten opnieuw worden gedownload nadat de peering van het virtuele netwerk is geconfigureerd om ervoor te zorgen dat de nieuwe routes naar de client worden gedownload.
- Er wordt een nominaal bedrag in rekening gebracht voor inkomend en uitgaand verkeer dat gebruikmaakt van een virtueel netwerk-peering. Zie de pagina [prijzen](https://azure.microsoft.com/pricing/details/virtual-network) voor meer informatie.

## <a name="permissions"></a>Machtigingen

De accounts die u gebruikt om te werken met de peering van het virtuele netwerk moeten worden toegewezen aan de volgende rollen:

- [Inzender voor netwerken](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor): voor een virtueel netwerk dat is geïmplementeerd via Resource Manager.
- [Inzender voor klassieke netwerken](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor): voor een virtueel netwerk dat is geïmplementeerd via het klassieke implementatie model.

Als uw account niet is toegewezen aan een van de vorige rollen, moet dit worden toegewezen aan een [aangepaste rol](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) waaraan de nodige acties zijn toegewezen in de volgende tabel:

| Actie                                                          | Naam |
|---                                                              |---   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write  | Vereist voor het maken van een peering van een virtueel netwerk A naar een virtueel netwerk B. virtueel netwerk A moet een virtueel netwerk zijn (Resource Manager)          |
| Micro soft. Network/virtualNetworks/peer/Action                   | Vereist voor het maken van een peering van een virtueel netwerk B (Resource Manager) naar een virtueel netwerk                                                       |
| Micro soft. ClassicNetwork/virtualNetworks/peer/Action                   | Vereist voor het maken van een peering van een virtueel netwerk B (klassiek) naar een virtueel netwerk                                                                |
| Micro soft. Network/virtualNetworks/virtualNetworkPeerings/lezen   | Een peering van een virtueel netwerk lezen   |
| Micro soft. Network/virtualNetworks/virtualNetworkPeerings/verwijderen | Een peering van een virtueel netwerk verwijderen |

## <a name="next-steps"></a>Volgende stappen

- Peering in virtuele netwerken kan tot stand worden gebracht tussen virtuele netwerken die zijn gemaakt via hetzelfde implementatiemodel of via verschillende implementatiemodellen die tot hetzelfde abonnement of tot verschillende abonnementen behoren. Volg een zelfstudie voor een van de volgende scenario's:

  |Azure-implementatiemodel             | Abonnement  |
  |---------                          |---------|
  |Beide in Resource Manager              |[Hetzelfde](tutorial-connect-virtual-networks-portal.md)|
  |                                   |[Verschillend](create-peering-different-subscriptions.md)|
  |Eén in Resource Manager, één klassiek  |[Hetzelfde](create-peering-different-deployment-models.md)|
  |                                   |[Verschillend](create-peering-different-deployment-models-subscriptions.md)|

- Meer informatie over het maken van een [hub-en-spoke-netwerktopologie](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Een virtuele netwerk-peering maken met behulp van [Power shell](powershell-samples.md) of [Azure cli](cli-samples.md) -voorbeeld scripts of met behulp van Azure [Resource Manager-sjablonen](template-samples.md)
- [Azure Policy definities](policy-samples.md) voor virtuele netwerken maken en toewijzen
