---
title: 'Azure ExpressRoute: een open bare peering verplaatsen naar micro soft-peering'
description: In dit artikel ziet u de stappen voor het verplaatsen van uw open bare peering naar micro soft-peering op ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/12/2019
ms.author: duau
ms.openlocfilehash: 608d6c87442821e904fde16c6b75841fe792e3f0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92206285"
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>Overstappen van openbare peering op Microsoft-peering

Dit artikel helpt u bij het verplaatsen van een configuratie voor een open bare peering naar micro soft-peering zonder uitval tijd. ExpressRoute biedt ondersteuning voor het gebruik van Microsoft-peering met routefilters voor Azure PaaS-services, zoals Azure Storage en Azure SQL Database. U hebt nu maar één routeringsdomein nodig voor toegang tot Microsoft PaaS- en SaaS-services. U kunt routefilters gebruiken om de PaaS-servicevoorvoegsels selectief te adverteren voor Azure-regio's die u wilt verbruiken.

Open bare Azure-peering heeft één NAT IP-adres dat is gekoppeld aan elke BGP-sessie. Met micro soft-peering kunt u uw eigen NAT-toewijzingen configureren, maar ook route filters gebruiken voor selectieve voor voegsels van advertenties. Open bare peering is een service in één richting waarmee de connectiviteit altijd vanuit uw WAN naar Microsoft Azure Services wordt gestart. Microsoft Azure Services kunnen geen verbindingen met uw netwerk initiëren via dit routerings domein.

Zodra open bare peering is ingeschakeld, kunt u verbinding maken met alle Azure-Services. U kunt niet selectief Services picken waarvoor we routes aankondigen. Micro soft-peering is een bidirectionele connectiviteit waarbij verbinding kan worden gestart vanuit Microsoft Azure service samen met uw WAN. Zie voor meer informatie over routerings domeinen en peering [ExpressRoute-circuits en routerings domeinen](expressroute-circuit-peerings.md).

## <a name="before-you-begin"></a><a name="before"></a>Voordat u begint

Als u verbinding wilt maken met micro soft-peering, moet u NAT instellen en beheren. Uw connectiviteits provider kan de NAT instellen en beheren als een beheerde service. Als u van plan bent om toegang te krijgen tot de Azure PaaS-en Azure SaaS-Services op micro soft-peering, is het belang rijk om de IP-adres groep van NAT correct te maken. Voor meer informatie over NAT voor ExpressRoute raadpleegt u de [NAT-vereisten voor micro soft-peering](expressroute-nat.md#nat-requirements-for-microsoft-peering). Wanneer u verbinding maakt met micro soft via Azure ExpressRoute (micro soft-peering), hebt u meerdere koppelingen naar micro soft. De ene koppeling is uw bestaande internetverbinding en de andere gaat via ExpressRoute. Bepaald verkeer naar Microsoft kan via internet gaan maar kan terugkeren via ExpressRoute of andersom.

![Bidirectionele connectiviteit](./media/how-to-move-peering/bidirectional-connectivity.jpg)

> [!Warning]
> De NAT IP-adresgroep die wordt geadverteerd aan Microsoft mag niet worden geadverteerd op internet. Dit verbreekt de connectiviteit met andere Microsoft-services.

Raadpleeg [asymmetrische route ring met meerdere netwerk paden](./expressroute-asymmetric-routing.md) voor voor behoud van asymmetrische route ring voordat u micro soft-peering configureert.

* Als u gebruikmaakt van open bare peering en momenteel IP-netwerk regels voor open bare IP-adressen gebruikt voor toegang tot [Azure Storage](../storage/common/storage-network-security.md) of [Azure SQL database](../azure-sql/database/vnet-service-endpoint-rule-overview.md), moet u ervoor zorgen dat de NAT IP-adres groep die is geconfigureerd met micro soft-peering, is opgenomen in de lijst met open bare IP-adressen voor het Azure-opslag account of het Azure SQL-account.<br>
* Als u wilt overschakelen naar micro soft-peering zonder downtime, volgt u de stappen in dit artikel in de volg orde waarin ze worden weer gegeven.

## <a name="1-create-microsoft-peering"></a><a name="create"></a>1. micro soft-peering maken

Als micro soft-peering niet is gemaakt, gebruikt u een van de volgende artikelen om micro soft-peering te maken. Als uw connectiviteits provider beheerde Layer 3-services biedt, kunt u de connectiviteits provider vragen om micro soft-peering voor uw circuit in te scha kelen.

Als de laag 3 door u wordt beheerd, is de volgende informatie vereist voordat u doorgaat:

* Een /30-subnet voor de primaire koppeling. Dit moet een geldig openbaar IPv4-voorvoegsel zijn waarvan u eigenaar bent en dat is geregistreerd in een RIR/IRR. Vanuit dit subnet wijst u het eerste bebruikbaar bare IP-adres toe aan uw router, aangezien micro soft gebruikmaakt van het tweede bebruikbaarde IP voor de router.<br>
* Een /30-subnet voor de secundaire koppeling. Dit moet een geldig openbaar IPv4-voorvoegsel zijn waarvan u eigenaar bent en dat is geregistreerd in een RIR/IRR. Vanuit dit subnet wijst u het eerste bebruikbaar bare IP-adres toe aan uw router, aangezien micro soft gebruikmaakt van het tweede bebruikbaarde IP voor de router.<br>
* Een geldige VLAN-id waarop u deze peering wilt instellen. Controleer of er geen andere peering in het circuit is die dezelfde VLAN-id gebruikt. Voor zowel primaire als secundaire koppelingen moet u dezelfde VLAN-ID gebruiken.<br>
* AS-nummer voor peering. U kunt 2-bytes en 4-bytes AS-nummers gebruiken.<br>
* Geadverteerde voorvoegsels: u moet een lijst verstrekken van alle voorvoegsels die u via de BGP-sessie wilt adverteren. Alleen openbare IP-adresvoorvoegsels worden geaccepteerd. U kunt een met komma's gescheiden lijst verzenden als u een set voorvoegsels wilt verzenden. Deze voorvoegsels moeten voor u zijn geregistreerd in een RIR/IRR.<br>
* Naam van routeringsregister: u kunt het RIR/IRR opgeven waarbij het AS-nummer en de voorvoegsels zijn geregistreerd.

* **Optioneel** : klant-ASN: als u voor voegsels adverteert die niet zijn geregistreerd voor het peering als-nummer, kunt u het as-nummer opgeven waarop ze zijn geregistreerd.<br>
* **Optioneel** : een MD5-hash als u ervoor kiest om er een te gebruiken.

Gedetailleerde instructies voor het inschakelen van micro soft-peering vindt u in de volgende artikelen:

* [Micro soft-peering maken met behulp van Azure Portal](expressroute-howto-routing-portal-resource-manager.md#msft)<br>
* [Micro soft-peering maken met behulp van Azure Power shell](expressroute-howto-routing-arm.md#msft)<br>
* [Micro soft-peering maken met behulp van Azure CLI](howto-routing-cli.md#msft)

## <a name="2-validate-microsoft-peering-is-enabled"></a><a name="validate"></a>2. valideren of micro soft-peering is ingeschakeld

Controleer of de micro soft-peering is ingeschakeld en of de aangekondigde open bare voor voegsels de geconfigureerde status hebben.

* [Azure-portal](expressroute-howto-routing-portal-resource-manager.md#getmsft)<br>
* [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)<br>
* [Azure-CLI](howto-routing-cli.md#getmsft)

## <a name="3-configure-and-attach-a-route-filter-to-the-circuit"></a><a name="routefilter"></a>3. een route filter configureren en koppelen aan het circuit

Nieuwe micro soft-peering adverteert standaard geen voor voegsels totdat een route filter aan het circuit is gekoppeld. Wanneer u een route filter regel maakt, kunt u de lijst met Service community's opgeven voor Azure-regio's die u wilt gebruiken voor Azure PaaS Services. Dit biedt u de flexibiliteit om de routes te filteren op basis van uw vereiste, zoals wordt weer gegeven in de volgende scherm afbeelding:

![Open bare peering samen voegen](./media/how-to-move-peering/routefilter.jpg)

Configureer route filters met behulp van een van de volgende artikelen:

* [Route filters configureren voor micro soft-peering met behulp van Azure Portal](how-to-routefilter-portal.md)<br>
* [Route filters configureren voor micro soft-peering met behulp van Azure PowerShell](how-to-routefilter-powershell.md)<br>
* [Route filters configureren voor micro soft-peering met behulp van Azure CLI](how-to-routefilter-cli.md)

## <a name="4-delete-the-public-peering"></a><a name="delete"></a>4. de open bare peering verwijderen

Nadat u hebt gecontroleerd of de micro soft-peering is geconfigureerd en de voor voegsels die u wilt gebruiken, op de juiste wijze worden geadverteerd op micro soft-peering, kunt u de open bare peering verwijderen. Als u de open bare peering wilt verwijderen, gebruikt u een van de volgende artikelen:

* [Open bare Azure-peering met Azure PowerShell verwijderen](about-public-peering.md#powershell)
* [Open bare Azure-peering verwijderen met CLI](about-public-peering.md#cli)
  
## <a name="5-view-peerings"></a><a name="view"></a>5. peerings weer geven
  
U kunt een lijst weer geven met alle ExpressRoute-circuits en peerings in de Azure Portal. Zie [informatie over micro soft-peering weer geven](expressroute-howto-routing-portal-resource-manager.md#getmsft)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md)voor meer informatie over ExpressRoute.