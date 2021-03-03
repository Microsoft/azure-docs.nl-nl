---
title: Over virtuele hub-routering
titleSuffix: Azure Virtual WAN
description: In dit artikel wordt de route ring van virtuele hub beschreven
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: c1a2a54bf2d4c5de3e6cfca66256f60592fc1f3e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101737508"
---
# <a name="about-virtual-hub-routing"></a>Over virtuele hub-routering

De routerings mogelijkheden in een virtuele hub worden geboden door een router die alle route ring beheert tussen gateways met behulp van Border Gateway Protocol (BGP). Een virtuele hub kan meerdere gateways bevatten, zoals een site-naar-site-VPN-gateway, ExpressRoute-gateway, punt-naar-site-gateway Azure Firewall. Deze router biedt ook transit connectiviteit tussen virtuele netwerken die verbinding maken met een virtuele hub en die een geaggregeerde door Voer van 50 Gbps kan ondersteunen. Deze routerings mogelijkheden zijn van toepassing op standaard virtuele WAN-klanten.

Zie [route ring van virtuele hub configureren voor meer informatie over het](how-to-virtual-hub-routing.md)configureren van route ring.

## <a name="routing-concepts"></a><a name="concepts"></a>Routerings concepten

In de volgende secties worden de belangrijkste concepten in virtuele hub-route ring beschreven.

### <a name="hub-route-table"></a><a name="hub-route"></a>Route tabel van de hub

Een route tabel van de virtuele hub kan een of meer routes bevatten. Een route bevat de naam, een label, een doel type, een lijst met doel voorvoegsels en de volgende hop-informatie voor een pakket dat moet worden doorgestuurd. Een **verbinding** heeft meestal een routerings configuratie die aan een route tabel is gekoppeld of door gegeven.

### <a name="connections"></a><a name="connection"></a>Verbindingen

Verbindingen zijn Resource Manager-resources die een routerings configuratie hebben. De vier typen verbindingen zijn:

* **VPN-verbinding**: Hiermee wordt een VPN-site verbonden met een virtuele hub VPN-gateway.
* **ExpressRoute-verbinding**: Hiermee verbindt u een ExpressRoute-circuit met een virtuele hub ExpressRoute-gateway.
* **P2S-configuratie verbinding**: verbindt een VPN-configuratie (punt-naar-site) met een virtuele-hub-gebruikers-VPN (punt-naar-site)-gateway.
* **Virtuele hub-netwerk verbinding**: Hiermee verbindt u virtuele netwerken met een virtuele hub.

U kunt de routerings configuratie voor een virtuele netwerk verbinding instellen tijdens de installatie. Standaard worden alle verbindingen gekoppeld en door gegeven aan de standaard route tabel.

### <a name="association"></a><a name="association"></a>Organisatie

Elke verbinding is gekoppeld aan één route tabel. Als u een verbinding met een route tabel koppelt, kan het verkeer worden verzonden naar de bestemming die wordt aangegeven als routes in de route tabel. In de routerings configuratie van de verbinding wordt de bijbehorende route tabel weer gegeven.  Meerdere verbindingen kunnen worden gekoppeld aan dezelfde route tabel. Alle VPN-, ExpressRoute-en gebruikers-VPN-verbindingen zijn gekoppeld aan dezelfde routerings tabel (standaard).

Standaard zijn alle verbindingen gekoppeld aan een **standaard route tabel** in een virtuele hub. Elke virtuele hub heeft zijn eigen standaard route tabel, die kan worden bewerkt om een statische route (s) toe te voegen. Routes die statisch worden toegevoegd, hebben voor rang boven dynamisch geleerde routes voor dezelfde voor voegsels.

:::image type="content" source="./media/about-virtual-hub-routing/concepts-association.png" alt-text="Organisatie":::

### <a name="propagation"></a><a name="propagation"></a>Doorgifte

Verbindingen geven dynamische routes door aan een route tabel. Met een VPN-verbinding, een ExpressRoute-verbinding of een P2S-configuratie verbinding worden routes door gegeven van de virtuele hub naar de on-premises router met behulp van BGP. Routes kunnen worden door gegeven aan een of meer route tabellen.

Er is ook een **route tabel geen** beschikbaar voor elke virtuele hub. Door door te geven aan de geen route tabel, houdt in dat er geen routes moeten worden door gegeven van de verbinding. VPN-, ExpressRoute-en gebruikers-VPN-verbindingen sturen routes door naar dezelfde set route tabellen.

:::image type="content" source="./media/about-virtual-hub-routing/concepts-propagation.png" alt-text="Doorgifte":::

### <a name="labels"></a><a name="labels"></a>Labels

Labels bieden een mechanisme voor het logisch groeperen van route tabellen. Dit is met name handig tijdens het door geven van routes van verbindingen naar meerdere route tabellen. De **standaard route tabel** heeft bijvoorbeeld een ingebouwd label met de naam default. Wanneer gebruikers verbindings routes door geven naar een standaard label, wordt deze automatisch toegepast op alle standaard route tabellen voor elke hub in het virtuele WAN.

### <a name="configuring-static-routes-in-a-virtual-network-connection"></a><a name="static"></a>Statische routes configureren in een virtuele netwerk verbinding

Het configureren van statische routes biedt een mechanisme voor het stuur verkeer via een volgende hop-IP. Dit kan een virtueel netwerk apparaat (NVA) zijn dat is ingericht in een spoke-VNet dat is gekoppeld aan een virtuele hub. De statische route bestaat uit een route naam, een lijst met doel voorvoegsels en het volgende hop-IP-adres.

## <a name="route-tables-for-pre-existing-routes"></a><a name="route"></a>Route tabellen voor reeds bestaande routes

Routeringstabellen hebben nu functies voor koppeling en doorgifte. Een vooraf bestaande routeringstabel is een routeringstabel die deze functies niet heeft. Als u al bestaande routes in hubroutering hebt en u de nieuwe mogelijkheden wilt gebruiken, moet u rekening houden met het volgende:

* **Standaard virtuele WAN-klanten met al bestaande routes in de virtuele hub**:

   Als u bestaande routes hebt in de sectie Routering voor de hub in Azure Portal, verwijder die dan eerst en probeer vervolgens nieuwe routetabellen te maken (beschikbaar in de sectie Routeringstabellen voor de hub in Azure Portal).

* **Eenvoudige virtuele WAN-klanten met al bestaande routes in de virtuele hub**:

   Als u bestaande routes hebt in de sectie Routering voor de hub in Azure Portal, verwijder die dan eerst en **upgrade** uw virtuele WAN Basic naar Standard. Zie [Een virtueel WAN upgraden van Basic naar Standard](upgrade-virtual-wan.md).

## <a name="hub-reset"></a><a name="reset"></a>Hub opnieuw instellen

**Resetten** van de virtuele hub zijn alleen beschikbaar in de Azure Portal. Opnieuw instellen biedt een manier om mislukte resources zoals route tabellen, hub-router of de virtuele-hub-resource zelf terug te brengen naar de inrichtings status Rightful. Overweeg de hub opnieuw in te stellen voordat u contact opneemt met micro soft voor ondersteuning. Met deze bewerking worden de gateways in een virtuele hub niet opnieuw ingesteld.

## <a name="additional-considerations"></a><a name="considerations"></a>Aanvullende overwegingen

Houd rekening met het volgende bij het configureren van virtuele WAN-route ring:

* Alle vertakkings verbindingen (punt-naar-site, site-naar-site-en ExpressRoute) moeten aan de standaard route tabel zijn gekoppeld. Op die manier komen alle branches overeen met dezelfde voor voegsels.
* Alle vertakkings verbindingen moeten hun routes door geven aan dezelfde set route tabellen. Als u bijvoorbeeld besluit dat vertakkingen moeten worden door gegeven aan de standaard route tabel, moet deze configuratie consistent zijn voor alle vertakkingen. Als gevolg hiervan kunnen alle verbindingen die aan de standaard route tabel zijn gekoppeld, alle vertakkingen bereiken.
* Vertakking-naar-vertakking via Azure Firewall wordt momenteel niet ondersteund.
* Wanneer u Azure Firewall in meerdere regio's gebruikt, moeten alle spoke-virtuele netwerken aan dezelfde route tabel zijn gekoppeld. Als er bijvoorbeeld een subset van de VNets door de Azure Firewall, terwijl andere VNets overs Laan, is het niet mogelijk om de Azure Firewall in dezelfde virtuele hub over te slaan.
* Eén volgende hop-IP kan per VNet-verbinding worden geconfigureerd.

## <a name="next-steps"></a>Volgende stappen

* Zie [route ring van virtuele hub configureren voor meer informatie over het](how-to-virtual-hub-routing.md)configureren van route ring.
* Raadpleeg de [Veelgestelde vragen](virtual-wan-faq.md)voor meer informatie over Virtual WAN.
