---
title: 'BGP configureren voor VPN Gateway: Portal'
titleSuffix: Azure VPN Gateway
description: Dit artikel begeleidt u bij de stappen voor het configureren van BGP met Azure VPN Gateway met behulp van Power shell.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/22/2021
ms.author: yushwang
ms.openlocfilehash: 479a8fac111be6e5b1ae2c6ea21fff801ba26f83
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863578"
---
# <a name="how-to-configure-bgp-on-azure-vpn-gateways"></a>BGP op Azure VPN-gateways configureren

Dit artikel begeleidt u stapsgewijs door de stappen voor het inschakelen van BGP op een cross-premises site-naar-site (S2S) VPN-verbinding en een VNet-naar-VNet-verbinding met behulp van de Azure Portal.

## <a name="about-bgp"></a><a name="about"></a>Over BGP

BGP is het standaardprotocol voor routering dat doorgaans op internet wordt gebruikt voor het uitwisselen van routerings- en bereikbaarheidsgegevens tussen twee of meer netwerken. Met BGP worden de Azure VPN-gateways en uw on-premises VPN-apparaten, BGP-peers of neighbors, geexchange "routes" waarmee beide gateways worden geïnformeerd over de beschik baarheid en de mogelijkheid om deze voor voegsels door te lopen voor de gateways of routers. Met BGP kan ook transitroutering tussen meerdere netwerken worden ingeschakeld door routes die een BGP-gateway leert van één BGP te propageren naar alle andere BGP-peers.

Zie [overzicht van BGP met Azure VPN-gateways](vpn-gateway-bgp-overview.md)voor meer informatie over de voor delen van BGP en om inzicht te krijgen in de technische vereisten en overwegingen voor het gebruik van BGP.

## <a name="getting-started"></a>Aan de slag

Elk deel van dit artikel helpt u bij het maken van een basis bouwsteen voor het inschakelen van BGP in uw netwerk verbinding. Als u alle drie de onderdelen hebt voltooid, bouwt u de topologie op zoals weer gegeven in diagram 1.

**Diagram 1**

:::image type="content" source="./media/bgp-howto/bgp-crosspremises-v2v.png" alt-text="Diagram van weer gave van netwerk architectuur en-instellingen" border="false":::

U kunt delen samen combi neren om een complexere, multi-hop en Transit netwerk te bouwen dat aan uw behoeften voldoet.

### <a name="prerequisites"></a>Vereisten

Controleer of u een Azure-abonnement hebt. Als u nog geen Azure-abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of [u aanmelden voor een gratis account](https://azure.microsoft.com/pricing/free-trial/).

## <a name="part-1-configure-bgp-on-the-virtual-network-gateway"></a><a name ="config"></a>Deel 1: BGP configureren op de gateway van het virtuele netwerk

In deze sectie maakt en configureert u een virtueel netwerk, maakt en configureert u een virtuele netwerk gateway met BGP-para meters en haalt u het IP-adres van de Azure BGP-peer op. In diagram 2 ziet u de configuratie-instellingen die moeten worden gebruikt bij het werken met de stappen in deze sectie.

**Diagram 2**

:::image type="content" source="./media/bgp-howto/bgp-gateway.png" alt-text="Diagram met de instellingen voor de virtuele netwerk gateway" border="false":::

### <a name="1-create-and-configure-testvnet1"></a>1. TestVNet1 maken en configureren

In deze stap maakt en configureert u TestVNet1. Volg de stappen in de [zelf studie een gateway maken](./tutorial-create-gateway-portal.md) om uw virtuele Azure-netwerk en VPN-gateway te maken en te configureren. Gebruik de referentie-instellingen in de onderstaande scherm afbeeldingen.

* Virtueel netwerk:

   :::image type="content" source="./media/bgp-howto/testvnet-1.png" alt-text="TestVNet1 met bijbehorende adres voorvoegsels":::

* Subnetten:

   :::image type="content" source="./media/bgp-howto/testvnet-1-subnets.png" alt-text="TestVNet1-subnetten":::

### <a name="2-create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>2. Maak de VPN-gateway voor TestVNet1 met BGP-para meters

In deze stap maakt u een VPN-gateway met de bijbehorende BGP-para meters.

1. Ga in het Azure Portal naar de resource **Virtual Network gateway** vanuit Marketplace en selecteer **maken**.

1. Vul de para meters in, zoals hieronder wordt weer gegeven:

   :::image type="content" source="./media/bgp-howto/create-gateway-1.png" alt-text="VNG1 maken":::

1. Configureer de volgende instellingen in het gedeelte gemarkeerde **BGP configureren** van de pagina:

   :::image type="content" source="./media/bgp-howto/create-gateway-1-bgp.png" alt-text="BGP configureren":::

   * Selecteer **BGP**  -  **ingeschakeld** configureren om de sectie BGP-configuratie weer te geven.

   * Vul uw ASN in (autonoom systeem nummer).

   * Het veld **Azure APIPA BGP IP Address** is optioneel. Als uw on-premises VPN-apparaten APIPA-adres voor BGP gebruiken, moet u een adres selecteren in het door Azure gereserveerde APIPA-adres bereik voor VPN, dat van **169.254.21.0** tot **169.254.22.255**. In dit voor beeld wordt 169.254.21.11 gebruikt.

   * Als u een actieve VPN-gateway maakt, wordt in de BGP-sectie een extra **tweede aangepast BGP-IP-adres voor Azure** weer gegeven. Selecteer een ander IP-adres in het toegestane APIPA-bereik (**169.254.21.0** naar **169.254.22.255**). Het tweede IP-adres moet anders zijn dan het eerste adres.

   > [!IMPORTANT]
   >
   > * Standaard wijst Azure automatisch een persoonlijk IP-adres uit het GatewaySubnet-voorvoegsel bereik toe als het Azure BGP IP-adres op de Azure VPN-gateway. Het aangepaste BGP-adres van Azure APIPA is nodig als uw on-premises VPN-apparaten een APIPA-adres (169.254.0.1 tot en met 169.254.255.254) als BGP-IP gebruiken. In azure VPN Gateway wordt het aangepaste APIPA-adres gekozen als de bijbehorende lokale netwerk gateway resource (on-premises netwerk) een APIPA-adres heeft als BGP-peer-IP. Als de lokale netwerk gateway een normaal IP-adres (geen APIPA) gebruikt, wordt Azure VPN Gateway teruggezet naar het privé-IP-adres uit het GatewaySubnet-bereik.
   >
   > * De APIPA BGP-adressen mogen niet overlappen tussen de on-premises VPN-apparaten en alle verbonden Azure VPN-gateways.
   >
   > * Wanneer APIPA-adressen worden gebruikt op Azure VPN-gateways, initiëren de gateways geen BGP-peering sessies met IP-adressen van de APIPA-bron. Het on-premises VPN-apparaat moet BGP-peering verbindingen initiëren.
   >

1. Selecteer **Beoordelen en maken** om de validatie uit te voeren. Wanneer de validatie is geslaagd, selecteert u **Maken** om de VPN-gateway te implementeren. Het kan tot 45 minuten duren voordat een gateway volledig is gemaakt en geïmplementeerd. U kunt de implementatiestatus bekijken op de overzichtspagina van uw gateway.

### <a name="3-obtain-the-azure-bgp-peer-ip-addresses"></a>3. de IP-adressen van de Azure BGP-peer verkrijgen

Zodra de gateway is gemaakt, kunt u de IP-adressen van de BGP-peer verkrijgen op de Azure VPN-gateway. Deze adressen zijn nodig om uw on-premises VPN-apparaten te configureren voor het instellen van BGP-sessies met de Azure VPN-gateway.

1. Navigeer naar de resource van de virtuele netwerk gateway en selecteer de **configuratie** pagina om de BGP-configuratie gegevens te zien, zoals weer gegeven in de volgende scherm afbeelding. Op deze pagina kunt u alle BGP-configuratie gegevens op uw Azure VPN-gateway: ASN, openbaar IP-adres en de bijbehorende IP-adressen van de BGP-peer aan de Azure-zijde (standaard en APIPA) weer geven.

   :::image type="content" source="./media/bgp-howto/vnet-1-gw-bgp.png" alt-text="BGP-gateway":::

1. Op de pagina **configuratie** kunt u de volgende configuratie wijzigingen aanbrengen:

   * U kunt het ASN of het IP-adres van de APIPA-BGP zo nodig bijwerken.
   * Als u een actief/actief VPN-gateway hebt, worden op deze pagina het open bare IP-adres, de standaard en de BGP-IP-adressen van het tweede exemplaar van de Azure VPN-gateway weer gegeven.

1. Als u wijzigingen hebt aangebracht, selecteert u **Opslaan** om de wijzigingen in uw Azure VPN-gateway door te voeren.

## <a name="part-2-configure-bgp-on-cross-premises-s2s-connections"></a><a name ="crosspremises"></a>Deel 2: BGP configureren voor cross-premises S2S-verbindingen

Als u een cross-premises verbinding tot stand wilt brengen, moet u een *lokale netwerk gateway* maken om uw on-PREMISES VPN-apparaat aan te duiden, en een *verbinding* om de VPN-gateway te verbinden met de lokale netwerk gateway, zoals wordt uitgelegd in [site-naar-site-verbinding maken](./tutorial-site-to-site-portal.md). Dit artikel bevat de aanvullende eigenschappen die vereist zijn voor het opgeven van de BGP-configuratie parameters.

**Diagram 3**

:::image type="content" source="./media/bgp-howto/bgp-crosspremises.png" alt-text="Diagram waarin IPsec wordt weer gegeven" border="false":::

### <a name="1-configure-bgp-on-the-local-network-gateway"></a>1. Configureer BGP op de lokale netwerk gateway

In deze stap configureert u BGP op de lokale netwerk gateway. Gebruik de volgende scherm afbeelding als voor beeld. De scherm opname toont de lokale netwerk gateway (Site5) met de para meters die zijn opgegeven in diagram 3.

:::image type="content" source="./media/bgp-howto/create-local-bgp.png" alt-text="BGP configureren voor de lokale netwerk gateway":::

#### <a name="important-configuration-considerations"></a>Belang rijke overwegingen met betrekking tot de configuratie

* De ASN en het IP-adres van de BGP-peer moeten overeenkomen met de configuratie van uw on-premises VPN-router.
* U kunt de **adres ruimte** alleen leeg laten als u gebruikmaakt van BGP om verbinding te maken met dit netwerk. Azure VPN-gateway voegt intern een route van uw BGP-peer-IP-adres toe aan de bijbehorende IPsec-tunnel. Als u **geen** BGP tussen de Azure VPN-gateway en dit specifieke netwerk gebruikt, **moet** u een lijst met geldige adres voorvoegsels voor de **adres ruimte** opgeven.
* U kunt eventueel een **APIPA IP-adres** (169.254. x. x) gebruiken als uw on-PREMISES BGP-peer-IP, indien nodig. U moet echter ook een IP-adres van APIPA opgeven, zoals eerder in dit artikel is beschreven voor uw Azure VPN-gateway, anders kan de BGP-sessie niet tot stand worden gebracht voor deze verbinding.
* U kunt de BGP-configuratie gegevens opgeven tijdens het maken van de gateway van het lokale netwerk, of u kunt de BGP-configuratie toevoegen of wijzigen op de pagina **configuratie** van de lokale netwerk gateway resource.

**Voorbeeld**

In dit voor beeld wordt een APIPA-adres (169.254.100.1) gebruikt als het IP-adres van de on-premises BGP-peer:

:::image type="content" source="./media/bgp-howto/local-apipa.png" alt-text="Lokale netwerk gateway APIPA en BGP":::

### <a name="2-configure-a-s2s-connection-with-bgp-enabled"></a>2. een S2S-verbinding configureren waarvoor BGP is ingeschakeld

In deze stap maakt u een nieuwe verbinding waarvoor BGP is ingeschakeld. Als u al een verbinding hebt en u BGP hierop wilt inschakelen, kunt u [een bestaande verbinding bijwerken](#update).

#### <a name="to-create-a-connection-with-bgp-enabled"></a>Een verbinding maken waarvoor BGP is ingeschakeld

Als u een nieuwe verbinding wilt maken waarvoor BGP is ingeschakeld, vult u de waarden in op de pagina **verbinding toevoegen** en schakelt u de optie **BGP inschakelen** in om BGP in te scha kelen voor deze verbinding. Selecteer **OK** om de verbinding te maken.

:::image type="content" source="./media/bgp-howto/ipsec-connection-bgp.png" alt-text="IPsec-cross-premises verbinding met BGP":::

#### <a name="to-update-an-existing-connection"></a><a name ="update"></a>Een bestaande verbinding bijwerken

Als u de BGP-optie voor een verbinding wilt wijzigen, gaat u naar de pagina **configuratie** van de verbindings bron en schakelt u de **BGP** -optie in als gemarkeerd in het volgende voor beeld. Selecteer **Opslaan** om de wijzigingen op te slaan.

:::image type="content" source="./media/bgp-howto/update-bgp.png" alt-text="BGP voor een verbinding bijwerken":::

## <a name="part-3-configure-bgp-on-vnet-to-vnet-connections"></a><a name ="v2v"></a>Deel 3: BGP configureren voor VNet-naar-VNet-verbindingen

De stappen voor het in-of uitschakelen van BGP op een VNet-naar-VNet-verbinding zijn hetzelfde als de S2S-stappen in [deel 2](#crosspremises). U kunt BGP inschakelen bij het maken van de verbinding of de configuratie bijwerken voor een bestaande VNet-naar-VNet-verbinding.

>[!NOTE] 
>Een VNet-naar-VNet-verbinding zonder BGP beperkt de communicatie met de twee verbonden VNets alleen. Schakel BGP in om Transit routering mogelijk te maken voor andere S2S-of VNet-naar-VNet-verbindingen van deze twee VNets.
>

Als BGP moet worden uitgeschakeld tussen TestVNet2 en TestVNet1, worden de routes voor het on-premises netwerk, site5 en daarom niet met site 5 gecommuniceerd, voor context, die betrekking hebben op **diagram 4**. Als u BGP inschakelt, zoals weer gegeven in diagram 4, kunnen alle drie de netwerken communiceren via IPsec-en VNet-naar-VNet-verbindingen.

**Diagram 4**

:::image type="content" source="./media/bgp-howto/bgp-crosspremises-v2v.png" alt-text="Diagram waarin het volledige netwerk wordt weer gegeven" border="false":::

## <a name="next-steps"></a>Volgende stappen

Wanneer de verbinding is voltooid, kunt u virtuele machines aan uw virtuele netwerken toevoegen. Zie [Een virtuele machine maken](../virtual-machines/windows/quick-create-portal.md) voor de stappen.