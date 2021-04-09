---
title: Een IPSec-tunnel maken in een Azure VMware-oplossing
description: Meer informatie over het instellen van een VPN-site-naar-site-verbinding (IPsec IKEv1 en IKEv2) in azure VMware-oplossingen.
ms.topic: how-to
ms.date: 03/23/2021
ms.openlocfilehash: 280ffdd3fec77208d5b49c8e624b7b22bca1daaf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105026997"
---
# <a name="create-an-ipsec-tunnel-into-azure-vmware-solution"></a>Een IPSec-tunnel maken in een Azure VMware-oplossing

In dit artikel gaan we de stappen door lopen om een VPN-verbinding (IPsec IKEv1 en IKEv2) tot stand te brengen die in de Microsoft Azure virtuele WAN-hub wordt beëindigd. De hub bevat de Azure VMware Solution ExpressRoute-gateway en de site-naar-site-VPN-gateway. Er wordt verbinding gemaakt met een on-premises VPN-apparaat met een Azure VMware-oplossings eindpunt.

:::image type="content" source="media/create-ipsec-tunnel/vpn-s2s-tunnel-architecture.png" alt-text="Diagram met de VPN-site-naar-site-tunnel architectuur." border="false":::

In deze procedure gaat u het volgende doen:
- Maak een virtuele WAN-hub met Azure en een VPN-gateway waaraan een openbaar IP-adres is gekoppeld. 
- Maak een Azure ExpressRoute-gateway en stel een Azure VMware-oplossings eindpunt in. 
- Een op beleid gebaseerde VPN on-premises instellen inschakelen. 

## <a name="prerequisites"></a>Vereisten
U moet een openbaar IP-adres hebben dat wordt beëindigd op een on-premises VPN-apparaat.

## <a name="step-1-create-an-azure-virtual-wan"></a>Stap 1. Een virtueel WAN van Azure maken

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="step-2-create-a-virtual-wan-hub-and-gateway"></a>Stap 2. Een virtuele WAN-hub en-gateway maken

>[!TIP]
>U kunt ook [een gateway maken in een bestaande hub](../virtual-wan/virtual-wan-expressroute-portal.md#existinghub).

1. Selecteer het virtuele WAN dat u in de vorige stap hebt gemaakt.

1. Selecteer **virtuele hub maken**, voer de vereiste velden in en selecteer **volgende: site naar site**. 

   Voer het subnet in met behulp van een `/24` (mini maal).

   :::image type="content" source="media/create-ipsec-tunnel/create-virtual-hub.png" alt-text="Scherm afbeelding van de pagina virtuele hub maken.":::

4. Selecteer het tabblad **site-naar-site** , definieer de site-naar-site-gateway door de geaggregeerde door Voer in te stellen op basis van de vervolg keuzelijst met **schaal eenheden** voor de gateway. 

   >[!TIP]
   >De schaal eenheden bevinden zich in paren voor redundantie, elke ondersteuning van 500 Mbps (één schaal eenheid = 500 Mbps). 
  
   :::image type="content" source="../../includes/media/virtual-wan-tutorial-hub-include/site-to-site.png" alt-text="Scherm opname van de site-naar-site-details.":::

5. Selecteer het tabblad **ExpressRoute** en maak een ExpressRoute-gateway. 

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-er-hub-include/hub2.png" alt-text="Scherm opname van de ExpressRoute-instellingen.":::

   >[!TIP]
   >Een waarde voor de schaal eenheid is 2 Gbps. 

    Het duurt ongeveer 30 minuten om elke hub te maken. 

## <a name="step-3-create-a-site-to-site-vpn"></a>Stap 3. Een site-naar-site VPN-verbinding maken

1. Selecteer in de Azure Portal de virtuele WAN die u eerder hebt gemaakt.

2. In het **overzicht** van de virtuele hub selecteert u   >  **VPN-verbinding (site-naar-site)**  >  **nieuwe VPN-site maken**.

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics.png" alt-text="Scherm afbeelding van de overzichts pagina voor de virtuele hub, met VPN (site-naar-site) en het maken van een nieuwe VPN-site geselecteerd.":::  
 
3. Op het tabblad **basis beginselen** voert u de vereiste velden in. 

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics2.png" alt-text="Scherm afbeelding van het tabblad basis beginselen voor de nieuwe VPN-site.":::  

   1. Stel de **Border Gateway Protocol** in op **ingeschakeld**.  Wanneer deze functie is ingeschakeld, zorgt dit ervoor dat zowel de Azure VMware-oplossing als de on-premises servers hun routes via de tunnel adverteren. Als deze is uitgeschakeld, moeten de subnetten die moeten worden aangekondigd hand matig worden onderhouden. Als er subnetten worden gemist, mislukt de HCX het service-net. Zie  [over BGP met Azure VPN gateway](../vpn-gateway/vpn-gateway-bgp-overview.md)voor meer informatie.
   
   1. Voer voor de **persoonlijke adres ruimte** het on-premises CIDR-blok in. Dit wordt gebruikt voor het routeren van al het verkeer dat is gebonden voor on-premises via de tunnel. Het CIDR-blok is alleen vereist als u BGP niet inschakelt.

1. Selecteer **volgende: koppelingen** en vul de vereiste velden in. Als u namen van koppelingen en providers opgeeft, kunt u onderscheid maken tussen een wille keurig aantal gateways dat uiteindelijk kan worden gemaakt als onderdeel van de hub. BGP en autonoom systeem nummer (ASN) moet uniek zijn binnen uw organisatie.

   :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-links.png" alt-text="Scherm opname van koppelings Details.":::

1. Selecteer **Controleren + maken**. 

1. Ga naar de gewenste virtuele hub en schakel de **koppeling hub** uit om uw VPN-site te verbinden met de hub.
 
   :::image type="content" source="../../includes/media/virtual-wan-tutorial-site-include/connect.png" alt-text="Scherm opname van het deel venster verbonden sites voor de virtuele HUB die gereed is voor Vooraf gedeelde sleutel en de bijbehorende instellingen.":::   

## <a name="step-4-optional-create-policy-based-vpn-site-to-site-tunnels"></a>Stap 4. Beschrijving Op beleid gebaseerde VPN-site-naar-site-tunnels maken

>[!IMPORTANT]
>Dit is een optionele stap die alleen van toepassing is op op beleid gebaseerde Vpn's. 

Voor het instellen van op beleid gebaseerde VPN-instellingen moeten on-premises en Azure VMware-oplossings netwerken worden opgegeven, met inbegrip van de hub-bereiken.  Met deze hub-bereiken wordt het versleutelings domein voor het op beleid gebaseerde VPN-tunnel op locatie opgegeven.  Voor de oplossings kant van de Azure VMware moet alleen de op beleid gebaseerde Traffic selector-indicator worden ingeschakeld. 

1. Ga in het Azure Portal naar de site van uw virtuele WAN-hub. Onder **connectiviteit** selecteert u **VPN (site-naar-site)**.

2. Selecteer de naam van de VPN-site, het weglatings teken (...) aan de rechter kant en bewerk vervolgens de **VPN-verbinding met deze hub**.
 
   :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png" alt-text="Scherm afbeelding van de pagina in azure voor de virtuele WAN-hub-site met een weglatings teken dat is geselecteerd voor toegang tot het bewerken van een VPN-verbinding met deze hub." lightbox="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png":::

3. Bewerk de verbinding tussen de VPN-site en de hub en selecteer vervolgens **Opslaan**.
   - Selecteer in Internet Protocol Security (IPSec) de optie **aangepast**.
   - Gebruik op beleid gebaseerde verkeers selectie, selecteer **inschakelen**
   - Geef de details op voor **IKE fase 1** en **IKE fase 2 (IPSec)**. 
 
   :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-connection.png" alt-text="Scherm afbeelding van de pagina VPN-verbinding bewerken."::: 
 
   De selectie vakjes voor verkeer of subnetten die deel uitmaken van het op beleid gebaseerde versleutelings domein moeten zijn:
    
   - Virtuele WAN-hub `/24`
   - Persoonlijke cloud van Azure VMware-oplossing `/22`
   - Verbonden Azure Virtual Network (indien aanwezig)

## <a name="step-5-connect-your-vpn-site-to-the-hub"></a>Stap 5. Uw VPN-site verbinden met de hub

1. Selecteer de naam van de VPN-site en selecteer vervolgens **VPN-sites verbinden**. 

1. Voer in het veld **vooraf gedeelde sleutel** de sleutel in die u eerder hebt gedefinieerd voor het on-premises eind punt. 

   >[!TIP]
   >Als u nog geen eerder gedefinieerde sleutel hebt, kunt u dit veld leeg laten. Er wordt automatisch een sleutel voor u gegenereerd. 

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/connect.png" alt-text="Scherm opname van het deel venster verbonden sites voor een virtuele HUB die gereed is voor een Vooraf gedeelde sleutel en de bijbehorende instellingen. "::: 

1. Als u een firewall in de hub implementeert en de volgende hop is, stelt u de optie **standaard route door geven** in op **inschakelen**. 

   Als deze functie is ingeschakeld, wordt de virtuele WAN-hub alleen aan een verbinding door gegeven als de hub de standaard route al heeft geleerd tijdens het implementeren van een firewall in de hub of als een andere verbonden site geforceerde tunneling heeft ingeschakeld. De standaardroute is niet afkomstig van de Virtual WAN-hub.  

1. Selecteer **Verbinding maken**. Na een paar minuten wordt de verbinding en connectiviteits status weer gegeven op de site.

   :::image type="content" source="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/status.png" alt-text="Scherm afbeelding met een site-naar-site-verbinding en connectiviteits status." lightbox="../../includes/media/virtual-wan-tutorial-connect-vpn-site-include/status.png":::

1. [Down load het VPN-configuratie bestand](../virtual-wan/virtual-wan-site-to-site-portal.md#device) voor het on-premises eind punt.  

3. Patch de Azure VMware Solution ExpressRoute in de virtuele WAN-hub. 

   >[!IMPORTANT]
   >U moet eerst een privécloud maken voordat u het platform kunt bijwerken. 

   [!INCLUDE [request-authorization-key](includes/request-authorization-key.md)]

4. Koppel de Azure VMware-oplossing en de VPN-gateway samen in de virtuele WAN-hub. U gebruikt de autorisatie sleutel en de ExpressRoute-ID (peer circuit URI) uit de vorige stap.

   1. Selecteer uw ExpressRoute-gateway en selecteer vervolgens **autorisatie sleutel inwisselen**.

      :::image type="content" source="media/create-ipsec-tunnel/redeem-authorization-key.png" alt-text="Scherm afbeelding van de ExpressRoute-pagina voor de privécloud, waarbij de autorisatie sleutel voor inwisselen is geselecteerd.":::

   1. Plak de autorisatie sleutel in het veld **autorisatie sleutel** .
   1. Plak de ExpressRoute-ID in het URL-veld van het **peer circuit** . 
   1. Schakel **Dit ExpressRoute-circuit automatisch koppelen aan het hub** selectie vakje in. 
   1. Selecteer **toevoegen** om de koppeling tot stand te brengen. 

5. Test uw verbinding door [een NSX-T-segment te maken](./tutorial-nsx-t-network-segment.md) en een VM in het netwerk in te richten. Ping de eind punten van de on-premises en Azure VMware-oplossing.
