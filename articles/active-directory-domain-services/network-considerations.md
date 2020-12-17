---
title: Netwerk planning en verbindingen voor Azure AD Domain Services | Microsoft Docs
description: Meer informatie over een aantal overwegingen voor het ontwerpen van virtuele netwerken en bronnen die worden gebruikt voor connectiviteit wanneer u Azure Active Directory Domain Services uitvoert.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 12/16/2020
ms.author: justinha
ms.openlocfilehash: d1a3ab5face03754bf84f442ac0fa73768b0fc80
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97615814"
---
# <a name="virtual-network-design-considerations-and-configuration-options-for-azure-active-directory-domain-services"></a>Ontwerp overwegingen voor het virtuele netwerk en configuratie opties voor Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure AD DS) biedt verificatie-en beheer services aan andere toepassingen en werk belastingen. De netwerk verbinding is een belang rijk onderdeel. Zonder correct geconfigureerde virtuele netwerk bronnen kunnen toepassingen en werk belastingen niet communiceren met en gebruikmaken van de functies van Azure AD DS. Plan de vereisten van uw virtuele netwerk om ervoor te zorgen dat Azure AD DS uw toepassingen en werk belastingen naar behoefte kan leveren.

In dit artikel vindt u een overzicht van overwegingen en vereisten voor het ontwerp van een virtueel Azure-netwerk ter ondersteuning van Azure AD DS.

## <a name="azure-virtual-network-design"></a>Azure Virtual Network-ontwerp

Als u verbinding met het netwerk wilt maken en toepassingen en services wilt toestaan voor verificatie bij een Azure AD DS beheerd domein, gebruikt u een virtueel Azure-netwerk en-subnet. In het ideale geval moet het beheerde domein worden geïmplementeerd in een eigen virtueel netwerk.

U kunt een afzonderlijk toepassings subnet in hetzelfde virtuele netwerk toevoegen om uw beheer-VM of lichte toepassings werkbelastingen te hosten. Een apart virtueel netwerk voor grotere of complexe toepassings werkbelastingen, gekoppeld aan het virtuele netwerk van Azure AD DS, is doorgaans het meest geschikte ontwerp.

Andere opties voor ontwerpen zijn geldig, op voor waarden dat u voldoet aan de vereisten die worden beschreven in de volgende secties voor het virtuele netwerk en subnet.

Wanneer u het virtuele netwerk voor Azure AD DS ontwerpt, zijn de volgende overwegingen van toepassing:

* Azure AD DS moet in dezelfde Azure-regio worden geïmplementeerd als uw virtuele netwerk.
    * Op dit moment kunt u slechts één beheerd domein implementeren per Azure AD-Tenant. Het beheerde domein wordt geïmplementeerd in een enkele regio. Zorg ervoor dat u een virtueel netwerk maakt of selecteert in een [regio die Azure-AD DS ondersteunt](https://azure.microsoft.com/global-infrastructure/services/?products=active-directory-ds&regions=all).
* Denk na over de nabijheid van andere Azure-regio's en de virtuele netwerken die als host fungeren voor de werk belasting van uw toepassing.
    * Als u de latentie wilt minimaliseren, houdt u uw belangrijkste toepassingen dicht bij of in dezelfde regio als het subnet van het virtuele netwerk voor uw beheerde domein. U kunt peering van virtuele netwerken of VPN-verbindingen (virtueel particulier netwerk) tussen virtuele netwerken van Azure gebruiken. Deze verbindings opties worden beschreven in een van de volgende secties.
* Het virtuele netwerk kan niet afhankelijk zijn van andere DNS-services dan die services die door het beheerde domein worden verschaft.
    * Azure AD DS biedt een eigen DNS-service. Het virtuele netwerk moet worden geconfigureerd voor het gebruik van deze DNS-service adressen. Naam omzetting voor extra naam ruimten kan worden uitgevoerd met behulp van voorwaardelijke doorstuur servers.
    * U kunt geen aangepaste DNS-server instellingen gebruiken om query's te sturen van andere DNS-servers, inclusief op Vm's. Resources in het virtuele netwerk moeten gebruikmaken van de DNS-service van het beheerde domein.

> [!IMPORTANT]
> U kunt Azure-AD DS niet verplaatsen naar een ander virtueel netwerk nadat u de service hebt ingeschakeld.

Een beheerd domein maakt verbinding met een subnet in een virtueel Azure-netwerk. Ontwerp dit subnet voor Azure AD DS met de volgende overwegingen:

* Een beheerd domein moet worden geïmplementeerd in een eigen subnet. Gebruik geen bestaand subnet of een gateway-subnet.
* Er wordt een netwerk beveiligings groep gemaakt tijdens de implementatie van een beheerd domein. Deze netwerk beveiligings groep bevat de vereiste regels voor de juiste service communicatie.
    * Maak of gebruik geen bestaande netwerk beveiligings groep met uw eigen aangepaste regels.
* Voor een beheerd domein zijn 3-5 IP-adressen vereist. Zorg ervoor dat het IP-adres bereik van uw subnet dit aantal adressen kan bevatten.
    * Het beperken van de beschik bare IP-adressen kan verhinderen dat het beheerde domein twee domein controllers beheert.

In het volgende voorbeeld diagram wordt een geldig ontwerp beschreven waarbij het beheerde domein een eigen subnet heeft, er is een gateway-subnet voor externe verbinding en de werk belasting van toepassingen bevindt zich in een verbonden subnet binnen het virtuele netwerk:

![Aanbevolen subnet ontwerpen](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

## <a name="connections-to-the-azure-ad-ds-virtual-network"></a>Verbindingen met het virtuele netwerk van Azure AD DS

Zoals u in de vorige sectie hebt genoteerd, kunt u alleen een beheerd domein in één virtueel netwerk in azure maken. er kan slechts één beheerd domein worden gemaakt per Azure AD-Tenant. Op basis van deze architectuur moet u mogelijk een of meer virtuele netwerken koppelen die de werk belasting van uw toepassing hosten naar het virtuele netwerk van uw beheerde domein.

U kunt met behulp van een van de volgende methoden toepassings werkbelastingen die worden gehost in andere virtuele netwerken van Azure verbinden:

* Peering op virtueel netwerk
* VPN-verbinding (Virtual Private Network)

### <a name="virtual-network-peering"></a>Peering op virtueel netwerk

Peering op virtueel netwerk is een mechanisme dat twee virtuele netwerken in dezelfde regio verbindt via het Azure-backbone-netwerk. Wereld wijde virtuele netwerk peering kan virtueel netwerk verbinden tussen Azure-regio's. Als de twee virtuele netwerken zijn gekoppeld, kunnen resources, zoals virtuele machines, rechtstreeks communiceren met behulp van privé-IP-adressen. Met Virtual Network-peering kunt u een beheerd domein implementeren met de workloads van uw toepassingen die in andere virtuele netwerken zijn geïmplementeerd.

![Verbinding met virtueel netwerk met behulp van peering](./media/active-directory-domain-services-design-guide/vnet-peering.png)

Zie [overzicht van Azure Virtual Network-peering](../virtual-network/virtual-network-peering-overview.md)voor meer informatie.

### <a name="virtual-private-networking-vpn"></a>Virtueel particulier netwerk (VPN)

U kunt een virtueel netwerk verbinden met een ander virtueel netwerk (VNet-naar-VNet) op dezelfde manier als u een virtueel netwerk op een on-premises site locatie kunt configureren. Beide verbindingen gebruiken een VPN-gateway om een beveiligde tunnel met IPsec/IKE te maken. Met dit verbindings model kunt u het beheerde domein implementeren in een virtueel Azure-netwerk en vervolgens on-premises locaties of andere Clouds verbinden.

![Verbinding met het virtuele netwerk met behulp van een VPN Gateway](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

Voor meer informatie over het gebruik van virtuele particuliere netwerken, Lees [een vnet-naar-vnet VPN-gateway verbinding configureren met behulp van de Azure Portal](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).

## <a name="name-resolution-when-connecting-virtual-networks"></a>Naam omzetting bij het verbinden van virtuele netwerken

Virtuele netwerken die zijn verbonden met het virtuele netwerk van het beheerde domein hebben doorgaans hun eigen DNS-instellingen. Wanneer u virtuele netwerken verbindt, wordt de naam omzetting voor het verbinding maken met het virtuele netwerk niet automatisch geconfigureerd om services op te lossen die worden geboden door het beheerde domein. De naam omzetting van de virtuele netwerken die verbinding maken moet zo worden geconfigureerd dat werk belastingen van toepassingen het beheerde domein kunnen vinden.

U kunt naam omzetting inschakelen met behulp van voorwaardelijke DNS-doorstuur servers op de DNS-server die de verbinding met virtuele netwerken ondersteunt, of door gebruik te maken van dezelfde DNS IP-adressen van het virtuele netwerk van het beheerde domein.

## <a name="network-resources-used-by-azure-ad-ds"></a>Netwerk bronnen die worden gebruikt door Azure AD DS

Een beheerd domein maakt sommige netwerk bronnen tijdens de implementatie. Deze resources zijn nodig voor een geslaagde bewerking en beheer van het beheerde domein, en mogen niet hand matig worden geconfigureerd.

| Azure-resource                          | Beschrijving |
|:----------------------------------------|:---|
| Netwerk interface kaart                  | Azure AD DS fungeert als host voor het beheerde domein op twee domein controllers (Dc's) die worden uitgevoerd op Windows Server als Azure-Vm's. Elke VM heeft een virtuele netwerk interface die verbinding maakt met het subnet van het virtuele netwerk. |
| Dynamisch standaard openbaar IP-adres      | Azure AD DS communiceert met de synchronisatie-en beheer service met een standaard-SKU openbaar IP-adres. Zie [IP-adres typen en toewijzings methoden in azure](../virtual-network/public-ip-addresses.md)voor meer informatie over open bare IP-adressen. |
| Azure Standard load balancer            | Azure AD DS maakt gebruik van een standaard-SKU load balancer voor Network Address Translation (NAT) en taak verdeling (bij gebruik met beveiligde LDAP). Zie [Wat is Azure Load Balancer?](../load-balancer/load-balancer-overview.md) voor meer informatie over Azure load balancers. |
| NAT-regels (Network Address Translation) | Azure AD DS maakt en gebruikt drie NAT-regels voor de load balancer-één regel voor beveiligd HTTP-verkeer en twee regels voor beveiligde communicatie met Power shell. |
| Regels voor load balancers                     | Wanneer een beheerd domein is geconfigureerd voor beveiligde LDAP op TCP-poort 636, worden er drie regels gemaakt en gebruikt in een load balancer om het verkeer te distribueren. |

> [!WARNING]
> Verwijder of wijzig geen van de netwerk resources die zijn gemaakt door Azure AD DS, zoals het hand matig configureren van de load balancer of regels. Als u een van de netwerk bronnen verwijdert of wijzigt, kan er een onderbreking van de Azure AD DS-service optreden.

## <a name="network-security-groups-and-required-ports"></a>Netwerk beveiligings groepen en de vereiste poorten

Een [netwerkbeveiligingsgroep](../virtual-network/network-security-groups-overview.md) (NSG: Network Security Group) bevat een lijst met beveiligingsregels waarmee netwerkverkeer naar verkeer in een virtueel Azure-netwerk wordt toegestaan of geweigerd. Er wordt een netwerk beveiligings groep gemaakt wanneer u een beheerd domein implementeert dat een set regels bevat waarmee de service verificatie-en beheer functies kan bieden. Deze standaard netwerk beveiligings groep is gekoppeld aan het subnet van het virtuele netwerk waarop uw beheerde domein is geïmplementeerd.

De volgende regels voor de netwerk beveiligings groep zijn vereist voor het beheerde domein voor het leveren van verificatie-en beheer Services. Wijzig of verwijder deze regels voor netwerk beveiligings groepen niet voor het subnet van het virtuele netwerk waarop uw beheerde domein is geïmplementeerd.

| Poortnummer | Protocol | Bron                             | Doel | Bewerking | Vereist | Doel |
|:-----------:|:--------:|:----------------------------------:|:-----------:|:------:|:--------:|:--------|
| 5986        | TCP      | AzureActiveDirectoryDomainServices | Alle         | Toestaan  | Ja      | Beheer van uw domein. |
| 3389        | TCP      | CorpNetSaw                         | Alle         | Toestaan  | Optioneel      | Fout opsporing voor ondersteuning. |

Er wordt een Standard Load Balancer van Azure gemaakt waarvoor deze regels moeten worden uitgevoerd. Deze netwerkbeveiligingsgroep beveiligt Azure AD DS en is vereist voor een juiste werking van het beheerde domein. Verwijder deze netwerk beveiligings groep niet. De load balancer werkt niet zonder problemen.

Als dat nodig is, kunt u [de vereiste netwerk beveiligings groep en-regels maken met behulp van Azure PowerShell](powershell-create-instance.md#create-a-network-security-group).

> [!WARNING]
> Bewerk deze netwerk bronnen en configuraties niet hand matig. Wanneer u een onjuist geconfigureerde netwerk beveiligings groep of een door de gebruiker gedefinieerde route tabel koppelt aan het subnet waarin het beheerde domein is geïmplementeerd, kunt u de mogelijkheid van micro soft om het domein te onderhouden en beheren te verstoren. De synchronisatie tussen uw Azure AD-Tenant en uw beheerde domein wordt ook verstoord.
>
> Als u beveiligde LDAP gebruikt, kunt u de vereiste regel voor de TCP-poort 636 toevoegen om extern verkeer toe te staan, indien nodig. Door deze regel toe te voegen, worden de regels voor de netwerk beveiligings groep niet in een niet-ondersteunde staat geplaatst. Zie [beveiligde LDAP-toegang via internet vergren delen](tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet) voor meer informatie.
>
> De standaard regels voor *AllowVnetInBound*, *AllowAzureLoadBalancerInBound*, *DenyAllInBound*, *AllowVnetOutBound*, *AllowInternetOutBound* en *DenyAllOutBound* bestaan ook voor de netwerk beveiligings groep. Bewerk of verwijder deze standaard regels niet.
>
> De Azure SLA is niet van toepassing op implementaties waarbij een onjuist geconfigureerde netwerk beveiligings groep en/of door de gebruiker gedefinieerde route tabellen zijn toegepast waardoor Azure AD DS het bijwerken en beheren van uw domein blokkeert.

### <a name="port-5986---management-using-powershell-remoting"></a>Poort 5986-beheer met externe communicatie van Power shell

* Wordt gebruikt voor het uitvoeren van beheer taken met externe communicatie van Power shell in uw beheerde domein.
* Als u geen toegang hebt tot deze poort, kan uw beheerde domein niet worden bijgewerkt, geconfigureerd, ondersteund of gecontroleerd.
* Voor beheerde domeinen die gebruikmaken van een virtueel netwerk op basis van Resource Manager, kunt u de inkomende toegang tot deze poort beperken tot het *AzureActiveDirectoryDomainServices* -service label.
    * Voor verouderde beheerde domeinen die gebruikmaken van een klassiek virtueel netwerk, kunt u de inkomende toegang tot deze poort beperken tot de volgende bron-IP-adressen: *52.180.183.8*, *23.101.0.70*, *52.225.184.198*, *52.179.126.223*, *13.74.249.156*, *52.187.117.83*, *52.161.13.95*, *104.40.156.18* en *104.40.87.209*.

    > [!NOTE]
    > In 2017 is Azure AD Domain Services beschikbaar voor de host in een Azure Resource Manager netwerk. Sindsdien hebben we een veiligere service met de moderne mogelijkheden van Azure Resource Manager kunnen bouwen. Omdat Azure Resource Manager implementaties volledig worden vervangen door klassieke implementaties, worden Azure AD DS-implementaties met een klassiek virtueel netwerk ingetrokken op 1 maart 2023.
    >
    > Zie voor meer informatie de [officiële afschaffing melding](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

### <a name="port-3389---management-using-remote-desktop"></a>Poort 3389-beheer met extern bureau blad

* Wordt gebruikt voor extern bureau blad-verbindingen met domein controllers in uw beheerde domein.
* De standaard regel voor de netwerk beveiligings groep maakt gebruik van het *CorpNetSaw* -service label om het verkeer verder te beperken.
    * Met deze servicetag wordt alleen beveiligde toegang tot werk stations in het bedrijfs netwerk van micro soft toegestaan om extern bureau blad te gebruiken voor het beheerde domein.
    * Toegang is alleen toegestaan met zakelijke redenen, zoals voor beheer-of probleemoplossings scenario's.
* Deze regel kan worden ingesteld op *weigeren* en alleen ingesteld op *toestaan* wanneer dit nodig is. De meeste beheer-en bewakings taken worden uitgevoerd met externe communicatie met Power shell. RDP wordt alleen gebruikt in de zeldzame gebeurtenis die micro soft nodig heeft om extern verbinding te maken met uw beheerde domein voor geavanceerde probleem oplossing.

> [!NOTE]
> U kunt de *CorpNetSaw* -servicetag niet hand matig selecteren in de portal als u deze regel voor de netwerk beveiligings groep probeert te bewerken. U moet Azure PowerShell of de Azure CLI gebruiken om hand matig een regel te configureren die gebruikmaakt van het *CorpNetSaw* -service label.
>
> U kunt bijvoorbeeld het volgende script gebruiken om een regel voor het maken van RDP toe te voegen: 
>
> `Get-AzureRmNetworkSecurityGroup -Name "nsg-name" -ResourceGroupName "resource-group-name" | Add-AzureRmNetworkSecurityRuleConfig -Name "new-rule-name" -Access "Allow" -Protocol "TCP" -Direction "Inbound" -Priority "priority-number" -SourceAddressPrefix "CorpNetSaw" -SourcePortRange "" -DestinationPortRange "3389" -DestinationAddressPrefix "" | Set-AzureRmNetworkSecurityGroup`

## <a name="user-defined-routes"></a>Door de gebruiker gedefinieerde routes

Door de gebruiker gedefinieerde routes worden niet standaard gemaakt en zijn niet vereist voor een juiste werking van Azure AD DS. Als u gebruik wilt maken van route tabellen, moet u geen wijzigingen aanbrengen in de *0.0.0.0* -route. Wijzigingen in deze route zorgen voor een onderbreking van Azure AD DS en plaatst het beheerde domein in een niet-ondersteunde status.

U moet ook binnenkomend verkeer omleiden van de IP-adressen die zijn opgenomen in de respectieve Azure-service tags naar het subnet van het beheerde domein. Zie voor meer informatie over service tags en het bijbehorende IP-adres [Azure IP-bereiken en service Tags-open bare Cloud](https://www.microsoft.com/en-us/download/details.aspx?id=56519).

> [!CAUTION]
> Deze IP-bereiken van Azure-data centers kunnen zonder kennisgeving worden gewijzigd. Zorg ervoor dat u over processen beschikt om te controleren of u de meest recente IP-adressen hebt.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over sommige netwerk bronnen en verbindings opties die worden gebruikt door Azure AD DS:

* [Peering van het virtuele netwerk van Azure](../virtual-network/virtual-network-peering-overview.md)
* [Azure VPN-gateways](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)
* [Netwerkbeveiligingsgroepen in Azure](../virtual-network/network-security-groups-overview.md)