---
title: Verkeer routeren via Nva's met aangepaste instellingen
titleSuffix: Azure Virtual WAN
description: Dit scenario helpt u bij het routeren van verkeer via Nva's met behulp van een andere NVA voor Internet verkeer.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/25/2021
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 8916fbc7c2a0b9789dcc73697324cee370f1fc1c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101704902"
---
# <a name="scenario-route-traffic-through-nvas-by-using-custom-settings"></a>Scenario: verkeer routeren via Nva's met aangepaste instellingen

Wanneer u met virtuele WAN-hub van Azure werkt, hebt u een aantal opties die voor u beschikbaar zijn. De focus van dit artikel is wanneer u verkeer wilt routeren via een virtueel netwerk apparaat (NVA) voor communicatie tussen virtuele netwerken en filialen, en gebruik een andere NVA voor Internet verkeer. Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie.

## <a name="design"></a>Ontwerp

* **Spokes** voor virtuele netwerken die zijn verbonden met de virtuele hub. (Bijvoorbeeld VNet 1, VNet 2 en VNet 3 in het diagram verderop in dit artikel).
* **Service-VNet** voor virtuele netwerken waarin gebruikers een NVA hebben geïmplementeerd om niet-Internet verkeer te controleren en mogelijk met algemene services die worden gebruikt door spokes. (Bijvoorbeeld VNet 4 in het diagram verderop in dit artikel).
* **Perimeter VNet** voor virtuele netwerken waarin gebruikers een NVA hebben geïmplementeerd die moeten worden gebruikt om het Internet verkeer te inspecteren. (Bijvoorbeeld VNet 5 in het diagram verderop in dit artikel).
* **Hubs** voor virtuele WAN-hubs die worden beheerd door micro soft.

De volgende tabel bevat een overzicht van de verbindingen die in dit scenario worden ondersteund:

| Van          | Tot|Knooppunten|Service-VNet|Vertakkingen|Internet|
|---|:---:|:---:|:---:|:---:|:---:|
| **Knooppunten**| ->| rechtstreeks |rechtstreeks | via service-VNet |via perimeter VNet |
| **Service-VNet**| ->| rechtstreeks |n.v.t.| rechtstreeks | |
| **Vertakkingen** | ->| via service-VNet |rechtstreeks| rechtstreeks |  |

Elk van de cellen in de verbindings matrix geeft aan of connectiviteit rechtstreeks via Virtual WAN of via een van de virtuele netwerken met een NVA.

Let op de volgende details:

* Knoop punten
  * Spokes bereiken rechtstreeks via virtuele WAN-hubs andere spokes.
  * Spokes krijgen verbindingen met vertakkingen via een statische route die verwijst naar het service-VNet. Ze leren geen specifieke voor voegsels van de vertakkingen, omdat deze voor voegsels specifiek zijn en de samen vatting onderdrukken.
  * Spokes verzenden Internet verkeer naar het perimeter VNet via een directe VNet-peering.
* Vertakkingen krijgen toegang tot spokes via een statische route ring die verwijst naar het service-VNet. Ze leren geen specifieke voor voegsels van de virtuele netwerken die de samenvatte statische route overschrijven.
* Het service-VNet is vergelijkbaar met een VNet voor gedeelde services dat bereikbaar moet zijn vanuit elk virtueel netwerk en elke vertakking.
* De perimeter VNet heeft geen verbinding nodig via Virtual WAN, omdat het enige verkeer dat wordt ondersteund, wordt geleverd via directe virtuele-netwerk peering. Gebruik voor het vereenvoudigen van de configuratie echter hetzelfde connectiviteits model als voor de perimeter VNet.

Er zijn drie verschillende verbindings patronen die worden omgezet in drie route tabellen. De koppelingen naar de verschillende virtuele netwerken zijn:

* Knoop punten
  * Gekoppelde route tabel: **RT_V2B**
  * Door geven aan route tabellen: **RT_V2B** en **RT_SHARED**
* NVA VNets (Service VNet en DMZ VNet):
  * Gekoppelde route tabel: **RT_SHARED**
  * Door geven aan route tabellen: **RT_SHARED**
* Sleutel
  * Gekoppelde route tabel: **standaard**
  * Door geven aan route tabellen: **RT_SHARED** en **standaard**

> [!NOTE]
> Zorg ervoor dat de spoke-VNets niet worden door gegeven aan het standaard label. Dit zorgt ervoor dat verkeer van branches naar spoke VNets wordt doorgestuurd naar de Nva's.

Deze statische routes zorgen ervoor dat verkeer van en naar het virtuele netwerk en de vertakking via de NVA in het service-VNet (VNet 4):

| Beschrijving | Routetabel | Statische route              |
| ----------- | ----------- | ------------------------- |
| Vertakkingen    | RT_V2B      | 10.2.0.0/16-> vnet4conn  |
| NVA-spokes  | Standaard     | 10.1.0.0/16-> vnet4conn  |

U kunt nu Virtual WAN gebruiken om de juiste verbinding te selecteren om de pakketten naar te verzenden. U moet ook virtuele WAN gebruiken om de juiste actie te selecteren die moet worden uitgevoerd wanneer deze pakketten worden ontvangen. U gebruikt de volgende verbindings route tabellen:

| Beschrijving | Verbinding | Statische route            |
| ----------- | ---------- | ----------------------- |
| VNet2Branch | vnet4conn  | 10.2.0.0/16-> 10.4.0.5 |
| Branch2VNet | vnet4conn  | 10.1.0.0/16-> 10.4.0.5 |

Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie.

## <a name="architecture"></a>Architectuur

In het volgende diagram ziet u de architectuur die eerder in het artikel is beschreven.

In het diagram bevindt zich één hub. **Hub 1**.

* **Hub 1** is rechtstreeks verbonden met NVA VNets **Vnet 4** en **vnet 5**.

* Verkeer tussen VNets 1, 2, 3 en de vertakkingen wordt naar verwachting via **VNet 4 NVA** 10.4.0.5.

* Alle Internet gebonden verkeer van VNets 1, 2 en 3 wordt verwacht door te gaan via **VNet 5 NVA** 10.5.0.5.

:::image type="content" source="./media/routing-scenarios/nva-custom/architecture.png" alt-text="Diagram van de netwerk architectuur.":::

## <a name="workflow"></a><a name="workflow"></a>Werkstroom

:::image type="content" source="./media/routing-scenarios/nva-custom/workflow.png" alt-text="Diagram van de werk stroom." lightbox="./media/routing-scenarios/nva-custom/workflow.png":::

Als u route ring via NVA wilt instellen, kunt u de volgende stappen overwegen:

1. Voor Internet-gebonden verkeer om via VNet 5 te gaan, hebt u VNets 1, 2 en 3 nodig om rechtstreeks verbinding te maken via de peering van het virtuele netwerk met VNet 5. U hebt ook een door de gebruiker gedefinieerde route ingesteld in de virtuele netwerken voor 0.0.0.0/0 en de 10.5.0.5 van de volgende hop.

   Als u geen verbinding wilt maken met VNets 1, 2 en 3 in VNet 5 en u in plaats daarvan alleen de NVA in VNet 4 gebruikt voor het routeren van 0.0.0.0/0 verkeer van filialen (on-premises VPN-of ExpressRoute-verbindingen), gaat u naar de [alternatieve werk stroom](#alternate).

   Als u het VNet-naar-VNet-verkeer echter wilt door lopen via de NVA, moet u VNet 1, 2, 3 van de virtuele hub verbreken en koppelen of boven de NVA Spaak en vnet4. In Virtual WAN wordt het VNet-naar-VNet-verkeer door gang via de virtuele WAN-hub of de Azure Firewall van een virtuele WAN-hub (beveiligde hub). Als VNets peer rechtstreeks gebruikmaakt van VNet-peering, kunnen ze rechtstreeks communiceren via de virtuele hub.

1. Ga in het Azure Portal naar uw virtuele hub en maak een aangepaste route tabel met de naam **RT_Shared**. In deze tabel vindt u informatie over routes via doorgifte van alle virtuele netwerken en vertakkings verbindingen. U ziet deze lege tabel in het volgende diagram.

   * **Routes:** U hoeft geen statische routes toe te voegen.

   * **Koppeling:** Selecteer VNets 4 en 5. Dit betekent dat de verbindingen van deze virtuele netwerken worden gekoppeld aan de route tabel **RT_Shared**.

   * **Doorgifte:** Omdat u wilt dat alle vertakkingen en virtuele netwerk verbindingen de routes dynamisch door geven aan deze route tabel, selecteert u vertakkingen en alle virtuele netwerken.

1. Maak een aangepaste route tabel met de naam **RT_V2B** voor het omleiden van verkeer van VNets 1, 2 en 3 naar branches.

   * **Routes:** Voeg een geaggregeerde statische route vermelding voor vertakkingen toe, met de volgende hop als de VNet 4-verbinding. Configureer een statische route in de verbinding van VNet 4 voor voor voegsels voor branches. Geef de volgende hop op als het specifieke IP-adres van de NVA in VNet 4.

   * **Koppeling:** Selecteer alle **VNets 1, 2 en 3**. Dit betekent dat de VNet-verbindingen 1, 2 en 3 aan deze route tabel zullen worden gekoppeld en dat er routes (statisch en dynamisch via doorgifte) in deze route tabel kunnen worden weer gegeven.

   * **Doorgifte:** Verbindingen geven routes door aan route tabellen. Als u VNets 1, 2 en 3 selecteert, kunnen routes worden door gegeven van VNets 1, 2 en 3 naar deze route tabel. U hoeft geen routes van de vertakkings verbindingen naar **RT_V2B** door te geven, omdat virtueel netwerk verkeer van de vertakking via de NVA in VNet 4 gaat.
  
1. Bewerk de standaard route tabel **DefaultRouteTable**.

   Alle VPN-, Azure ExpressRoute-en VPN-verbindingen tussen gebruikers zijn gekoppeld aan de standaard route tabel. Alle VPN-, ExpressRoute-en gebruikers-VPN-verbindingen sturen routes door naar dezelfde set route tabellen.

   * **Routes:** Voeg een geaggregeerde statische route vermelding toe voor VNets 1, 2 en 3, met de volgende hop als de VNet 4-verbinding. Configureer een statische route in de verbinding van VNet 4 voor de geaggregeerde voor voegsels voor VNet 1, 2 en 3. Geef de volgende hop op als het specifieke IP-adres van de NVA in VNet 4.

   * **Koppeling:** Zorg ervoor dat de optie voor vertakkingen (VPN/er/P2S) is geselecteerd, zodat de on-premises vertakkings verbindingen aan de standaard route tabel zijn gekoppeld.

   * **Doorgifte van:** Zorg ervoor dat de optie voor vertakkingen (VPN/er/P2S) is geselecteerd, zodat lokale verbindingen routes naar de standaard route tabel door geven.

## <a name="alternate-workflow"></a><a name="alternate"></a>Alternatieve werk stroom

In deze werk stroom maakt u geen verbinding met VNets 1, 2 en 3 in VNet 5. In plaats daarvan gebruikt u de NVA in VNet 4 voor het routeren van 0.0.0.0/0 verkeer van filialen (on-premises VPN-of ExpressRoute-verbindingen).

:::image type="content" source="./media/routing-scenarios/nva-custom/alternate.png" alt-text="Diagram van alternatieve werk stroom." lightbox="./media/routing-scenarios/nva-custom/alternate.png":::

Als u route ring via NVA wilt instellen, kunt u de volgende stappen overwegen:

1. Ga in het Azure Portal naar uw virtuele hub en maak een aangepaste route tabel met de naam **RT_NVA** voor het omleiden van verkeer via de NVA-10.4.0.5  

   * **Routes:**   Geen actie vereist.

   * **Koppeling:**   Selecteer **en vnet4**. Dit betekent dat VNet-verbinding 4 aan deze route tabel moet worden gekoppeld en kan routes (statisch en dynamisch via door geven) in deze route tabel kunnen ontdekken.

   * **Doorgifte:**   Verbindingen geven routes door aan route tabellen. Als u VNets 1, 2 en 3 selecteert, kunnen routes worden door gegeven van VNets 1, 2 en 3 naar deze route tabel. Als u vertakkingen (VPN/er/P2S) selecteert, kunnen er routes worden door gegeven vanuit vertakkingen/on-premises verbindingen met deze route tabel. Alle VPN-, ExpressRoute-en gebruikers-VPN-verbindingen sturen routes door naar dezelfde set route tabellen.

1. Maak een aangepaste route tabel met de naam **RT_VNET** voor het omleiden van verkeer van VNets 1, 2 en 3 naar branches of Internet (0.0.0.0/0) via de en VNET4-NVA. Het VNet-naar-VNet-verkeer wordt direct en niet via de NVA van VNet 4. Als u verkeer wilt door lopen via de NVA, verbreekt u de verbinding met VNet 1, 2 en 3 en verbindt u deze met behulp van VNet-peering met en vnet4.

   * **Stuurt**  

     * Voeg een geaggregeerde route ' 10.2.0.0/16 ' toe met de volgende hop als de VNet 4-verbinding voor verkeer van VNets 1, 2 en 3 naar filialen. Configureer in de en vnet4-verbinding een route voor ' 10.2.0.0/16 ' en geef de volgende hop op als het specifieke IP-adres van de NVA in VNet 4.

     * Voeg een route ' 0.0.0.0/0 ' toe met de volgende hop als de VNet 4-verbinding. ' 0.0.0.0/0 ' is toegevoegd om verkeer naar Internet te verzenden. Het impliceert geen specifieke adres voorvoegsels die betrekking hebben op VNets of branches. Configureer in de en vnet4-verbinding een route voor ' 0.0.0.0/0 ' en geef de volgende hop op als het specifieke IP-adres van de NVA in VNet 4.

   * **Koppeling:**   Selecteer alle **VNets 1, 2 en 3**. Dit betekent dat de VNet-verbindingen 1, 2 en 3 aan deze route tabel zullen worden gekoppeld en dat er routes (statisch en dynamisch via doorgifte) in deze route tabel kunnen worden weer gegeven.

   * **Doorgifte:**   Verbindingen geven routes door aan route tabellen. Als u VNets 1, 2 en 3 selecteert, kunnen routes worden door gegeven van VNets 1, 2 en 3 naar deze route tabel. Zorg ervoor dat de optie voor vertakkingen (VPN/er/P2S) niet is geselecteerd. Dit zorgt ervoor dat on-premises verbindingen niet rechtstreeks kunnen worden opgehaald naar de VNets 1, 2 en 3.

1. Bewerk de standaard route tabel **DefaultRouteTable**.

   Alle VPN-, Azure ExpressRoute-en VPN-verbindingen tussen gebruikers zijn gekoppeld aan de standaard route tabel. Alle VPN-, ExpressRoute-en gebruikers-VPN-verbindingen sturen routes door naar dezelfde set route tabellen.

   * **Stuurt**  

     * Voeg een geaggregeerde route ' 10.1.0.0/16 ' toe voor **VNets 1, 2 en 3** met de volgende hop als de **VNet 4-verbinding**.

     * Voeg een route ' 0.0.0.0/0 ' toe met de volgende hop als de **VNet 4-verbinding**. ' 0.0.0.0/0 ' is toegevoegd om verkeer naar Internet te verzenden. Het impliceert geen specifieke adres voorvoegsels die betrekking hebben op VNets of branches. In de vorige stap voor de en vnet4-verbinding hebt u al een route voor ' 0.0.0.0/0 ' geconfigureerd, waarbij de volgende hop het specifieke IP-adres van de NVA in VNet 4 is.

   * **Koppeling:**   Zorg ervoor dat de optie voor vertakkingen **(VPN/er/P2S)** is geselecteerd. Dit zorgt ervoor dat on-premises vertakkings verbindingen aan de standaard route tabel zijn gekoppeld. Alle VPN-, Azure ExpressRoute-en VPN-verbindingen tussen gebruikers worden alleen gekoppeld aan de standaard route tabel.

   * **Doorgifte van:**   Zorg ervoor dat de optie voor vertakkingen **(VPN/er/P2S)** is geselecteerd. Dit zorgt ervoor dat de on-premises verbindingen routes door geven aan de standaard route tabel. Alle VPN-, ExpressRoute-en gebruikers-VPN-verbindingen sturen routes door naar de ' dezelfde set van route tabellen '.

   >[!Note]
   >
   > * Gebruikers van de portal moeten ' door geven aan standaard route ' inschakelen voor verbindingen (VPN/er/P2S/VNet) om de 0.0.0.0/0-route van kracht te laten worden.
   > * PS/CLI/REST-gebruikers moeten de vlag ' enableinternetsecurity ' instellen op True als de route 0.0.0.0/0 van kracht worden.
   >

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg de [Veelgestelde vragen](virtual-wan-faq.md)voor meer informatie over Virtual WAN.
* Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over virtuele-hub-route ring.
