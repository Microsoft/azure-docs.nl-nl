---
title: 'Virtueel WAN van Azure: een virtueel netwerk apparaat (NVA) maken in de hub'
description: Meer informatie over het implementeren van een virtueel netwerk apparaat in de virtuele WAN-hub.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a Network Virtual Appliance (NVA) in my Virtual WAN hub.
ms.openlocfilehash: 1f80da4014d4b6b27155db17d32800e54b51f40d
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258062"
---
# <a name="how-to-create-a-network-virtual-appliance-in-an-azure-virtual-wan-hub-preview"></a>Een virtueel netwerk apparaat maken in een virtuele WAN-hub van Azure (preview)

Dit artikel laat u zien hoe u met Virtual WAN verbinding maakt met uw resources in azure via een **virtueel netwerk apparaat** (NVA) in Azure. Voor dit type verbinding moet er on-premises een VPN-apparaat aanwezig zijn waaraan een extern openbaar IP-adres is toegewezen. Zie [Wat is virtueel WAN?](virtual-wan-about.md)voor meer informatie over Virtual WAN.

Met de stappen in dit artikel kunt u een virtueel **Barracuda CLOUDGEN WAN** -netwerk maken in de virtuele WAN-hub. U kunt deze oefening alleen uitvoeren als u een Barracuda-Cloud apparaat (CPE) en een licentie voor het Barracuda CloudGen WAN-toestel hebt dat u in de hub implementeert voordat u begint.

Zie [Cisco Cloud opstap for multi-Cloud](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/cloudonramp/ios-xe-17/cloud-onramp-book-xe/cloud-onramp-multi-cloud.html#Cisco_Concept.dita_c61e0e7a-fff8-4080-afee-47b81e8df701)voor meer informatie over de implementatie van **Cisco SD-WAN** in azure Virtual WAN. 

## <a name="prerequisites"></a>Vereisten

Controleer voordat u met de configuratie begint of u aan de volgende criteria hebt voldaan:

* Verkrijg een licentie voor uw Barracuda CloudGen WAN-gateway. Zie de [Barracuda CLOUDGEN WAN-documentatie](https://www.barracuda.com/products/cloudgenwan) voor meer informatie over hoe u dit moet doen.

* U hebt een virtueel netwerk waarmee u verbinding wilt maken. Controleer of geen van de subnetten van uw on-premises netwerken overlapt met de virtuele netwerken waarmee u verbinding wilt maken. Zie de [snelstart](../virtual-network/quick-create-portal.md) als u een virtueel netwerk in de Azure-portal wilt maken.

* Uw virtuele netwerk heeft geen virtuele netwerkgateways. Als uw virtuele netwerk een gateway heeft (VPN of ExpressRoute), moet u alle gateways verwijderen. Voor deze configuratie moeten virtuele netwerken in plaats daarvan zijn verbonden met de Virtual WAN-hubgateway.

* Zorg dat u een IP-adresbereik krijgt voor uw hubregio. De hub is een virtueel netwerk dat wordt gemaakt en gebruikt door Virtual WAN. Het adresbereik dat u voor de hub opgeeft mag niet overlappen met een van de bestaande virtuele netwerken waarmee u verbinding wilt maken. Dit bereik mag ook niet overlappen met de adresbereiken waarmee u on-premises verbinding wilt maken. Als u de IP-adresbereiken in uw on-premises netwerkconfiguratie niet kent, moet u contact opnemen met iemand die deze gegevens kan verstrekken.

* Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan.

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>Een virtueel WAN maken

[!INCLUDE [Create virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-hub"></a><a name="hub"></a>Een hub maken

Een hub is een virtueel netwerk dat gateways kan bevatten voor site-naar-site-, ExpressRoute-, punt-naar-site-of netwerk-Virtual apparaat-functionaliteit. Zodra de hub is gemaakt, worden u kosten in rekening gebracht voor de hub, zelfs als u geen sites hebt toegevoegd. Als u ervoor kiest om een site-naar-site-VPN-gateway te maken, duurt het 30 minuten om de site-naar-site-VPN-gateway in de virtuele hub te maken. In tegens telling tot site-naar-site, ExpressRoute of punt-naar-site moet de hub eerst worden gemaakt voordat u een virtueel netwerk apparaat kunt implementeren in de hub VNet.

1. Zoek naar de virtuele WAN die u hebt gemaakt. Selecteer **hubs** op de **virtuele WAN** -pagina onder het gedeelte **connectiviteit** .
1. Selecteer op de pagina **hubs** + nieuwe hub om de pagina **virtuele hub maken** te openen.

   :::image type="content" source="./media/how-to-nva-hub/vwan-hub.png" alt-text="Basisinstellingen":::
1. Op de pagina **Basisinstellingen** van de pagina **Virtuele hub maken** vult u de volgende velden in:

   **Projectgegevens**

   * Regio (voorheen Locatie genoemd)
   * Naam
   * Privé-adresruimte van hub. De minimale adresruimte is /24 voor het maken van een hub, wat impliceert dat alles tussen /25 en /32 een fout produceert tijdens het maken. Azure Virtual WAN, een beheerde service van micro soft, maakt de juiste subnetten in de virtuele hub voor de verschillende gateways/Services. (Bijvoorbeeld: virtuele netwerk apparaten, VPN-gateways, ExpressRoute gateways, gebruikers VPN/punt-naar-site gateways, firewall, route ring, enzovoort). Het is niet nodig om de gebruiker expliciet te plannen voor de adres ruimte van het subnet voor de services in de virtuele hub, omdat micro soft dit als onderdeel van de service doet.
1. Selecteer **Beoordelen en maken** om de validatie uit te voeren.
1. Selecteer **Maken** om de hub te maken.

## <a name="create-the-network-virtual-appliance-in-the-hub"></a>Het virtuele netwerk apparaat maken in de hub

In deze stap maakt u een virtueel netwerk apparaat in de hub. De procedure voor elke NVA is verschillend voor elk product van de NVA-partner. Voor dit voor beeld maken we een Barracuda CloudGen WAN-gateway.

1. Zoek de virtuele WAN-hub die u in de vorige stap hebt gemaakt en open deze.

   :::image type="content" source="./media/how-to-nva-hub/nva-hub.png" alt-text="Virtuele hub":::
1. Zoek de tegel virtuele netwerk apparaten en selecteer de koppeling **maken** .
1. Selecteer op de Blade **virtueel netwerk apparaat** de optie **Barracuda CloudGen WAN** en selecteer vervolgens de knop **maken** .

   :::image type="content" source="./media/how-to-nva-hub/select-nva.png" alt-text="NVA selecteren":::
1. Hiermee gaat u naar de Azure Marketplace-aanbieding voor de Barracuda CloudGen WAN-gateway. Lees de voor waarden en selecteer vervolgens de knop **maken** wanneer u klaar bent.

   :::image type="content" source="./media/how-to-nva-hub/barracuda-create-basics.png" alt-text="Basis beginselen van Barracuda NVA":::
1. Op de pagina **basis beginselen** moet u de volgende informatie opgeven:

   * **Abonnement** : Kies het abonnement dat u hebt gebruikt voor het implementeren van de virtuele WAN en hub.
   * **Resource groep** : Kies dezelfde resource groep die u hebt gebruikt voor het implementeren van de virtuele WAN en hub.
   * **Regio** : Kies dezelfde regio waarin uw virtuele-hub-resource zich bevindt.
   * **Toepassings naam** : de BARRACUDA NextGen WAN is een beheerde toepassing. Kies een naam waarmee u deze resource eenvoudig kunt identificeren, omdat deze wordt aangeroepen wanneer deze in uw abonnement wordt weer gegeven.
   * **Beheerde resource groep** : dit is de naam van de beheerde resource groep waarin Barracuda resources implementeert die door hen worden beheerd. De naam moet vooraf zijn ingevuld.
1. Selecteer de knop **volgende: CLOUDGEN WAN-gateway** .

   :::image type="content" source="./media/how-to-nva-hub/barracuda-cloudgen-wan.png" alt-text="CloudGen WAN-gateway":::
1. Geef hier de volgende informatie op:

   * **Virtuele WAN-hub** : de virtuele WAN-hub waarop u deze NVA wilt implementeren.
   * **NVA infrastructuur eenheden** : Hiermee geeft u het aantal NVA-infrastructuur eenheden aan waarop u deze NVA wilt implementeren. Kies de hoeveelheid cumulatieve band breedte die u wilt bieden voor alle Branch sites die via deze NVA met deze hub worden verbonden.
   * Voor **token** -Barracuda moet u hier een verificatie token opgeven, zodat u zich kunt identificeren als een geregistreerde gebruiker van dit product. U moet dit verkrijgen van Barracuda.
1. Selecteer de knop **controleren en maken** om door te gaan.
1. Op deze pagina wordt u gevraagd om de voor waarden van de Co-Admin toegangs overeenkomst te accepteren. Dit is standaard met beheerde toepassingen waarbij de uitgever toegang heeft tot bepaalde resources in deze implementatie. Schakel het selectie vakje **Ik ga akkoord met de bovenstaande voor waarden** in en selecteer vervolgens **maken**.

## <a name="connect-the-vnet-to-the-hub"></a><a name="vnet"></a>VNet verbinden met de hub

[!INCLUDE [Connect](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="next-steps"></a>Volgende stappen

* Zie de pagina [Wat is een virtueel WAN?](virtual-wan-about.md) voor meer informatie over Virtual WAN.
* Zie [over het virtuele netwerk apparaat in de virtuele WAN-hub (preview)](about-nva-hub.md)voor meer informatie over nva's in een virtuele WAN-hub.
