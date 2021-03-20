---
title: Persoonlijke koppeling-Azure Database for PostgreSQL-één server
description: Meer informatie over de werking van een persoonlijke koppeling voor Azure Database for PostgreSQL-één server.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: e0d1789d61bbe57c735f4dd2a70a1c2a8f183d90
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98881058"
---
# <a name="private-link-for-azure-database-for-postgresql-single-server"></a>Persoonlijke koppeling voor Azure data base for PostgreSQL-Single-server

Met persoonlijke koppeling kunt u privé-eind punten voor Azure Database for PostgreSQL-één server maken om deze binnen uw Virtual Network (VNet) te brengen. Met het persoonlijke eind punt wordt een privé-IP-adres weer gegeven in een subnet dat u kunt gebruiken om verbinding te maken met uw database server net als elke andere resource in het VNet.

Raadpleeg de [documentatie](../private-link/index.yml)van de privé-koppeling voor een lijst met PaaS-services die ondersteuning bieden voor persoonlijke koppelings functionaliteit. Een persoonlijk eind punt is een privé-IP-adres binnen een specifiek [VNet](../virtual-network/virtual-networks-overview.md) en subnet.

> [!NOTE]
> De functie voor persoonlijke koppelingen is alleen beschikbaar voor Azure Database for PostgreSQL servers in de prijs Categorieën Algemeen of geoptimaliseerd voor geheugen. Zorg ervoor dat de database server zich in een van deze prijs categorieën bevindt.

## <a name="data-exfiltration-prevention"></a>Preventie van gegevensexfiltratie

Gegevens die worden gefilterd in Azure Database for PostgreSQL één server is wanneer een geautoriseerde gebruiker, zoals een database beheerder, gegevens uit het ene systeem kan ophalen en verplaatsen naar een andere locatie of een ander systeem buiten de organisatie. De gebruiker verplaatst bijvoorbeeld de gegevens naar een opslagaccount dat eigendom is van een derde partij.

Overweeg een scenario met een gebruiker die PGAdmin uitvoert in een Azure virtual machine (VM) die verbinding maakt met een Azure Database for PostgreSQL enkele server die in VS West is ingericht. In het onderstaande voor beeld ziet u hoe u de toegang tot de open bare eind punten op Azure Database for PostgreSQL afzonderlijke servers kunt beperken met behulp van besturings elementen voor netwerk toegang.

* Schakel alle Azure-service verkeer uit om één server te Azure Database for PostgreSQL via het open bare eind punt door de instelling *Azure-Services toestaan* in te scha kelen. Zorg ervoor dat er geen IP-adressen of bereiken toegang hebben tot de server via [firewall regels](./concepts-firewall-rules.md) of [virtuele netwerk service-eind punten](./concepts-data-access-and-security-vnet.md).

* Alleen verkeer toestaan voor de Azure Database for PostgreSQL één server met behulp van het privé-IP-adres van de virtuele machine. Zie de artikelen over [service-eind punten](concepts-data-access-and-security-vnet.md) en VNet- [firewall regels](howto-manage-vnet-using-portal.md) voor meer informatie.

* Op de Azure VM beperkt u het bereik van de uitgaande verbinding met behulp van Netwerkbeveiligingsgroepen (NSG's) en servicetags als volgt

    * Geef een NSG-regel op om verkeer voor *service label = SQL toe te staan. Westus* : alleen verbinding toestaan met Azure database for PostgreSQL één server in VS-West
    * Geef een NSG-regel (met een hogere prioriteit) op om verkeer te weigeren voor *service Tags = SQL* -verbindingen met een postgresql-data base in alle regio's weigeren</br></br>

Aan het einde van deze installatie kan de virtuele machine van Azure alleen verbinding maken met Azure Database for PostgreSQL één server in de regio vs-West. De connectiviteit is echter niet beperkt tot één Azure Database for PostgreSQL één server. De virtuele machine kan nog steeds verbinding maken met een Azure Database for PostgreSQL één server in de regio vs-West, met inbegrip van de data bases die geen onderdeel zijn van het abonnement. Hoewel we het bereik van de gegevensexfiltratie in het bovenstaande scenario naar een bepaalde regio hebben gereduceerd, hebben we het niet geheel verwijderd.</br>

Met persoonlijke koppeling kunt u nu netwerk toegangs beheer instellen, zoals Nsg's, zodat de toegang tot het persoonlijke eind punt wordt beperkt. Afzonderlijke Azure PaaS-resources worden vervolgens toegewezen aan specifieke privé-eindpunten. Een kwaadwillende Insider heeft alleen toegang tot de toegewezen PaaS-resource (bijvoorbeeld een Azure Database for PostgreSQL één server) en geen andere resource.

## <a name="on-premises-connectivity-over-private-peering"></a>On-premises connectiviteit via privé-peering

Wanneer u verbinding maakt met het open bare eind punt vanaf de on-premises machines, moet uw IP-adres worden toegevoegd aan de op IP gebaseerde firewall met behulp van een firewall regel op server niveau. Hoewel dit model goed werkt voor het toestaan van toegang tot afzonderlijke computers voor ontwikkel- of testwerkbelastingen, is het moeilijk te beheren in een productieomgeving.

Met persoonlijke koppeling kunt u cross-premises toegang tot het privé-eind punt inschakelen met behulp van [Express route](https://azure.microsoft.com/services/expressroute/) (er), persoonlijke peering of [VPN-tunnel](../vpn-gateway/index.yml). Ze kunnen vervolgens alle toegang uitschakelen via het open bare eind punt en de op IP gebaseerde firewall niet gebruiken.

> [!NOTE]
> In sommige gevallen bevinden de Azure Database for PostgreSQL en het VNet-subnet zich in verschillende abonnementen. In deze gevallen moet u ervoor zorgen dat u de volgende configuraties hebt:
> - Zorg ervoor dat voor beide abonnementen de resource provider **micro soft. DBforPostgreSQL** is geregistreerd. Raadpleeg [Resource-Manager-registratie][resource-manager-portal] voor meer informatie

## <a name="configure-private-link-for-azure-database-for-postgresql-single-server"></a>Persoonlijke koppeling voor Azure Database for PostgreSQL één server configureren

### <a name="creation-process"></a>Proces maken

Privé-eind punten zijn vereist om een persoonlijke koppeling in te scha kelen. U kunt dit doen met behulp van de volgende hand leidingen.

* [Azure-portal](./howto-configure-privatelink-portal.md)
* [CLI](./howto-configure-privatelink-cli.md)

### <a name="approval-process"></a>Goedkeuringsproces
Zodra de netwerk beheerder het persoonlijke eind punt (PE) heeft gemaakt, kan de PostgreSQL-beheerder de verbinding met het privé-eind punt (PEC) beheren met Azure Database for PostgreSQL. Deze schei ding van taken tussen de netwerk beheerder en de DBA is handig voor het beheer van de Azure Database for PostgreSQL-verbinding. 

* Navigeer naar de Azure Database for PostgreSQL Server-Resource in de Azure Portal. 
    * Selecteer de verbindingen met het privé-eind punt in het linkerdeel venster
    * Geeft een lijst weer van alle privé-eindpunt verbindingen (PECs)
    * Er is een overeenkomend persoonlijk eind punt (PE) gemaakt

:::image type="content" source="media/concepts-data-access-and-security-private-link/select-private-link-portal.png" alt-text="de portal voor het persoonlijke eind punt selecteren":::

* Selecteer een individuele PEC uit de lijst door deze te kiezen.

:::image type="content" source="media/concepts-data-access-and-security-private-link/select-private-link.png" alt-text="de goed keuring van het privé-eind punt in behandeling selecteren":::

* De beheerder van de PostgreSQL-server kan ervoor kiezen om een PEC goed te keuren of af te wijzen en eventueel een korte tekst reactie toe te voegen.

:::image type="content" source="media/concepts-data-access-and-security-private-link/select-private-link-message.png" alt-text="het bericht van het privé-eind punt selecteren":::

* Na goed keuring of weigering wordt in de lijst de juiste staat en de antwoord tekst weer gegeven

:::image type="content" source="media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png" alt-text="de eind status van het privé-eind punt selecteren":::

## <a name="use-cases-of-private-link-for-azure-database-for-postgresql"></a>Cases van een persoonlijke koppeling gebruiken voor Azure Database for PostgreSQL

Clients kunnen verbinding maken met het persoonlijke eind punt van hetzelfde VNet, gepeerd VNet in dezelfde regio, of via VNet-naar-VNet-verbindingen tussen regio's. Daarnaast kunnen clients via on-premises verbinding maken met behulp van ExpressRoute, privé peering of VPN-tunneling. Hieronder ziet u een vereenvoudigd diagram waarin de algemene gebruikscases worden weergegeven.

:::image type="content" source="media/concepts-data-access-and-security-private-link/show-private-link-overview.png" alt-text="het overzicht van het persoonlijke eind punt selecteren":::

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Verbinding maken vanaf een Azure VM in een Peered Virtual Network (VNet)
Configureer [VNet-peering](../virtual-network/tutorial-connect-virtual-networks-powershell.md) om verbinding te maken met de Azure database for PostgreSQL-één server van een Azure-vm in een gekoppeld VNet.

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Verbinding maken vanaf een Azure VM in VNet-naar-VNet-omgeving
Configureer de [vnet-naar-VNet VPN-gateway verbinding](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) om verbinding te maken met een Azure database for PostgreSQL-één server van een Azure-vm in een andere regio of een ander abonnement.

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Verbinding maken vanuit een on-premises omgeving via VPN
Als u verbinding wilt maken vanuit een on-premises omgeving naar de Azure Database for PostgreSQL-één server, kiest en implementeert u een van de volgende opties:

* [Punt-naar-site-verbinding](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
* [Site-naar-site-VPN-verbinding](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [ExpressRoute-circuit](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)

## <a name="private-link-combined-with-firewall-rules"></a>Privé koppeling gecombineerd met firewall regels

De volgende situaties en resultaten zijn mogelijk wanneer u een persoonlijke koppeling gebruikt in combi natie met firewall regels:

* Als u geen firewall regels configureert, heeft geen verkeer standaard toegang tot de Azure Database for PostgreSQL één server.

* Als u openbaar verkeer of een service-eind punt configureert en u persoonlijke eind punten maakt, worden verschillende soorten binnenkomend verkeer geautoriseerd door het bijbehorende type firewall regel.

* Als u geen openbaar verkeer of service-eind punt configureert en u persoonlijke eind punten maakt, is de Azure Database for PostgreSQL single-server alleen toegankelijk via de persoonlijke eind punten. Als u geen openbaar verkeer of een service-eind punt configureert, hebben geen verkeer meer toegang tot de Azure Database for PostgreSQL enkele server nadat alle goedgekeurde privé-eind punten zijn afgewezen of verwijderd.

## <a name="deny-public-access-for-azure-database-for-postgresql-single-server"></a>Open bare toegang voor Azure Database for PostgreSQL één server weigeren

Als u alleen wilt vertrouwen op privé-eind punten voor toegang tot hun Azure Database for PostgreSQL één server, kunt u het instellen van alle open bare eind punten ([firewall regels](concepts-firewall-rules.md) en [VNet-service-eind punten](concepts-data-access-and-security-vnet.md)) uitschakelen door de configuratie voor het **weigeren van open bare netwerk toegang** op de database server in te stellen. 

Als deze instelling is ingesteld op *Ja* , worden alleen verbindingen via persoonlijke eind punten toegestaan voor uw Azure database for PostgreSQL. Als deze instelling is ingesteld op *Nee* , kunnen clients geen verbinding maken met uw Azure database for PostgreSQL op basis van de instellingen van de firewall of het VNet-service-eind punt. Nadat de waarde van de toegang tot het particuliere netwerk is ingesteld, kunnen klanten ook bestaande firewall regels en VNet-service-eindpunt regels toevoegen en/of bijwerken.

> [!Note]
> Deze functie is beschikbaar in alle Azure-regio's waar Azure Database for PostgreSQL-één server ondersteunt de prijs categorieën voor Algemeen en geoptimaliseerd voor geheugen.
>
> Deze instelling heeft geen invloed op de SSL-en TLS-configuraties voor uw Azure Database for PostgreSQL één server.

Voor informatie over het instellen van de toegang voor het **weigeren van open bare netwerken** voor uw Azure database for PostgreSQL één server van Azure Portal, raadpleegt u [toegang tot open bare netwerken configureren](howto-deny-public-network-access.md).

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over het Azure Database for PostgreSQL van de beveiligings functies van één server:

* Zie [firewall ondersteuning](./concepts-firewall-rules.md)als u een firewall wilt configureren voor Azure database for PostgreSQL één server.

* Zie [toegang vanaf virtuele netwerken configureren](./concepts-data-access-and-security-vnet.md)voor meer informatie over het configureren van een service-eind punt voor een virtueel netwerk voor uw Azure database for PostgreSQL één server.

* Zie [Azure database for PostgreSQL Connectivity Architecture](./concepts-connectivity-architecture.md) (Engelstalig) voor een overzicht van Azure database for PostgreSQL verbinding met één server.

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md