---
title: 'Zelfstudie: ExpressRoute-verbindingen maken met behulp van Azure Virtual WAN'
description: In deze zelfstudie leert u hoe u Azure Virtual WAN gebruikt om ExpressRoute-verbindingen met Azure en on-premises omgevingen te maken.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 10/07/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my corporate on-premises network(s) to my VNets using Virtual WAN and ExpressRoute.
ms.openlocfilehash: 080136b8fc25b08a6b96464f0a61115a4bb2f3f8
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102426605"
---
# <a name="tutorial-create-an-expressroute-association-using-azure-virtual-wan"></a>Zelfstudie: Een ExpressRoute-koppeling maken met behulp van Azure Virtual WAN

In deze zelfstudie leert u hoe u Azure Virtual WAN gebruikt om uw resources in Azure te verbinden via een ExpressRoute-circuit. Zie het [Overzicht van Virtual WAN](virtual-wan-about.md) voor meer informatie over Virtual WAN en Virtual WAN-resources.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een virtueel WAN maken
> * Een hub en een gateway maken
> * Een VNet verbinden met een hub
> * Een circuit verbinden met een hub-gateway
> * Connectiviteit testen
> * De grootte van een gateway wijzigen
> * Een standaardroute adverteren

## <a name="prerequisites"></a>Vereisten

Controleer voordat u met de configuratie begint of u aan de volgende criteria hebt voldaan:

* U hebt een virtueel netwerk waarmee u verbinding wilt maken. Controleer of geen van de subnetten van uw on-premises netwerken overlapt met de virtuele netwerken waarmee u verbinding wilt maken. Zie de [snelstart](../virtual-network/quick-create-portal.md) als u een virtueel netwerk in de Azure-portal wilt maken.

* Uw virtuele netwerk heeft geen virtuele netwerkgateways. Als uw virtuele netwerk een gateway heeft (VPN of ExpressRoute), moet u alle gateways verwijderen. Voor deze configuratie moeten virtuele netwerken in plaats daarvan zijn verbonden met de Virtual WAN-hubgateway.

* Zorg dat u een IP-adresbereik krijgt voor uw hubregio. De hub is een virtueel netwerk dat wordt gemaakt en gebruikt door Virtual WAN. Het adresbereik dat u voor de hub opgeeft mag niet overlappen met een van de bestaande virtuele netwerken waarmee u verbinding wilt maken. Dit bereik mag ook niet overlappen met de adresbereiken waarmee u on-premises verbinding wilt maken. Als u de IP-adresbereiken in uw on-premises netwerkconfiguratie niet kent, moet u contact opnemen met iemand die deze gegevens kan verstrekken.

* Het ExpressRoute-circuit moet een Premium- of Standard-circuit zijn om verbinding met de hub-gateway te maken.

* Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan.

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>Een virtueel WAN maken

Open een browser, ga naar [Azure Portal](https://portal.azure.com) en meld u aan met uw Azure-account.

1. Ga naar de pagina Virtual WAN. Klik in de portal op **+Een resource maken**. Typ **Virtual WAN** in het zoekvak en selecteer Enter.
2. Selecteer **Virtual WAN** uit de resultaten. Klik op de pagina Virtual WAN op **Maken** om de pagina WAN maken te openen.
3. Vul op de pagina **Wan maken**, op het tabblad **Basisprincipes**, de volgende velden in:

   ![WAN maken](./media/virtual-wan-expressroute-portal/createwan.png)

   * **Abonnement** - selecteer het abonnement dat u wilt gebruiken.
   * **Resourcegroep** - maak een nieuwe resourcegroep of gebruik een bestaande.
   * **Locatie van de resourcegroep** - kies een resourcelocatie uit de vervolgkeuzelijst. Een WAN een globale resource en bevindt zich niet in een bepaalde regio. U moet echter een regio selecteren om de WAN-resource die u maakt eenvoudiger te kunnen beheren en vinden.
   * **Naam** - typ de naam die u voor uw WAN hebt gekozen.
   * **Type** - selecteer **Standaard**. U kunt geen ExpressRoute-gateway maken met behulp van de Basic-SKU.
4. Nadat u klaar bent met het invullen van de velden, selecteert u **+Maken**.
5. Wanneer de validatie is geslaagd, selecteert u **Maken** om het virtuele WAN te maken.

## <a name="create-a-virtual-hub-and-gateway"></a><a name="hub"></a>Een virtuele hub en gateway maken

Een virtuele hub is een virtueel netwerk dat wordt gemaakt en gebruikt door Virtual WAN. Het kan verschillende gateways bevatten, zoals VPN en ExpressRoute. In deze sectie maakt u een ExpressRoute-gateway voor uw virtuele hub. U kunt de gateway maken door [een nieuwe virtuele hub te maken](#newhub) of door de gateway in een [bestaande hub](#existinghub) te bewerken. 

ExpressRoute-gateways worden ingericht in eenheden van 2 Gbps. 1 schaaleenheid = 2 Gbps met ondersteuning voor maximaal 10 schaaleenheden = 20 Gbps. Het duurt ongeveer 30 minuten voordat een virtuele hub en gateway volledig zijn gemaakt.

### <a name="to-create-a-new-virtual-hub-and-a-gateway"></a><a name="newhub"></a>Een nieuwe virtuele hub en een gateway maken

Maak een nieuwe virtuele hub. Zodra een hub is gemaakt, worden u kosten in rekening gebracht voor de hub, zelfs als u geen sites hebt toegevoegd.

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-er-hub-include.md)]

### <a name="to-create-a-gateway-in-an-existing-hub"></a><a name="existinghub"></a>Een gateway maken in een bestaande hub

U kunt ook een gateway in een bestaande hub maken door deze te bewerken.

1. Ga naar de virtuele hub die u wilt bewerken en selecteer deze.
2. Schakel op de pagina **Virtuele hub bewerken** het selectievakje **ExpressRoute-gateway opnemen** in.
3. Selecteer **Bevestigen** om uw wijzigingen op te slaan. Het duurt ongeveer 30 minuten voordat de hub en de hub-resources volledig zijn gemaakt.

   ![bestaande hub](./media/virtual-wan-expressroute-portal/edithub.png "een hub bewerken")

### <a name="to-view-a-gateway"></a>Een gateway weergeven

Wanneer u een ExpressRoute-gateway hebt gemaakt, kunt u de detailgegevens van de gateway weergeven. Navigeer naar de hub, selecteer **ExpressRoute** en geef de gateway weer.

![Gateway weergeven](./media/virtual-wan-expressroute-portal/viewgw.png "gateway weergeven")

## <a name="connect-your-vnet-to-the-hub"></a><a name="connectvnet"></a>Uw VNet verbinden met de hub

In deze sectie brengt u de peering-verbinding tussen uw hub en een VNet tot stand. Herhaal deze stappen voor elk VNet dat u wilt verbinden.

1. Klik op de pagina voor uw virtuele WAN op **Virtuele netwerkverbinding**.
2. Klik op de pagina Virtuele netwerkverbinding op **+Verbinding toevoegen**.
3. Vul de volgende velden in op de pagina **Verbinding toevoegen**:

    * **Verbindingsnaam** - voer een naam in voor uw verbinding.
    * **Hubs** - selecteer de hub die u wilt koppelen aan deze verbinding.
    * **Abonnement** - controleer of het abonnement klopt.
    * **Virtueel netwerk** - selecteer het virtuele netwerk dat met deze hub wilt verbinden. Het virtuele netwerk mag geen bestaande virtuele netwerkgateway (VPN of ExpressRoute) hebben.

## <a name="connect-your-circuit-to-the-hub-gateway"></a><a name="connectcircuit"></a>Uw circuit verbinden met de hub-gateway

Zodra de gateway is gemaakt, kunt u deze verbinden met een [ExpressRoute-circuit](../expressroute/expressroute-howto-circuit-portal-resource-manager.md). ExpressRoute Standard- of Premium-circuits die zich in Global Reach ExpressRoute-ondersteunde locaties bevinden, kunnen verbinding maken met een virtuele WAN ExpressRoute-gateway en gebruikmaken van alle virtuele WAN-Transit mogelijkheden (VPN naar VPN, VPN en ExpressRoute Transit). ExpressRoute Standard- en Premium-circuits die zich in niet Global Reach locaties bevinden, kunnen verbinding maken met Azure-resources, maar kunnen geen virtuele WAN-Transit mogelijkheden gebruiken. ExpressRoute Local wordt ondersteund met Azure Virtual WAN-hubs, zolang de spoke-VNET's die met een Virtual WAN-hub zijn verbonden, zich in dezelfde regio bevinden als de Virtual WAN-hub.

### <a name="to-connect-the-circuit-to-the-hub-gateway"></a>Het circuit verbinden met de hub-gateway

Ga in de portal naar de pagina **Virtuele hub -> Connectiviteit -> ExpressRoute**. Als u in uw abonnement toegang hebt tot een ExpressRoute-circuit, ziet u het circuit dat u wilt gebruiken in de lijst met circuits. Als er geen circuits worden weergegeven, maar u wel een autorisatiesleutel en een peercircuit-URI hebt gekregen, kunt u een circuit inwisselen en verbinden. Zie [Verbinding maken door een autorisatiesleutel in te wisselen](#authkey) voor meer informatie.

1. Selecteer het circuit.
2. Selecteer **Circuit(s) verbinden**.

   ![circuits verbinden](./media/virtual-wan-expressroute-portal/cktconnect.png "circuits verbinden")

### <a name="to-connect-by-redeeming-an-authorization-key"></a><a name="authkey"></a>Verbinding maken door een autorisatiesleutel in te wisselen

Gebruik de autorisatiesleutel en circuit-URI die u hebt gekregen om verbinding te maken.

1. Klik op **+Autorisatiesleutel inwisselen** op de pagina ExpressRoute

   ![Schermopname van ExpressRoute voor een virtuele hub waarvoor de autorisatiesleutel voor inwisselen is geselecteerd.](./media/virtual-wan-expressroute-portal/redeem.png "inwisselen")
2. Vul de waarden in op de pagina Autorisatiesleutel inwisselen.

   ![waarden voor het inwisselen van een sleutel](./media/virtual-wan-expressroute-portal/redeemkey2.png "waarden voor het inwisselen van een sleutel")
3. Selecteer **Toevoegen** om de sleutel toe te voegen.
4. Geef het circuit weer. Een ingewisseld circuit bevat alleen de naam (zonder het type, de provider en andere informatie) omdat deze zich in een ander abonnement bevindt dan dat van de gebruiker.

## <a name="to-test-connectivity"></a>Connectiviteit testen

Nadat de verbinding met het circuit tot stand is gebracht, wordt 'deze hub' als hub-verbindingsstatus aangegeven. Dit betekent dat de verbinding met de hub-ExpressRoute-gateway tot stand is gebracht. Wacht ongeveer 5 minuten voordat u de connectiviteit van een client achter uw ExpressRoute-circuit, bijvoorbeeld een virtuele machine in het VNet dat u eerder hebt gemaakt, test.

Als u sites hebt verbonden met een Virtual WAN-VPN-gateway in dezelfde hub als de ExpressRoute-gateway, kunt u een bidirectionele verbinding hebben tussen VPN- en ExpressRoute-eindpunten. Dynamische routering (BGP) wordt ondersteund. De ASN van de gateways in de hub is vast en kan op dit moment niet worden bewerkt.

## <a name="to-change-the-size-of-a-gateway"></a>De grootte van een gateway wijzigen

Als u de grootte van uw ExpressRoute-gateway wilt wijzigen, zoekt u de ExpressRoute-gateway in de hub en selecteert u de schaaleenheden in de vervolgkeuzelijst. Sla uw wijziging op. Het duurt ongeveer 30 minuten om de hub-gateway bij te werken.

![grootte van gateway wijzigen](./media/virtual-wan-expressroute-portal/changescale.png "grootte van gateway wijzigen")

## <a name="to-advertise-default-route-00000-to-endpoints"></a>Standaardroute 0.0.0.0/0 adverteren naar eindpunten

Als u wilt dat de virtuele Azure-hub de standaardroute 0.0.0.0/0 adverteert naar uw ExpressRoute-eindpunten, moet u 'Standaardroute doorgeven' inschakelen.

1. Selecteer **Circuit -> ... -> Verbinding bewerken**.

   ![Verbinding bewerken](./media/virtual-wan-expressroute-portal/defaultroute1.png "Verbinding bewerken")

2. Selecteer **Inschakelen** om de standaardroute door te geven.

   ![Standaardroute doorgeven](./media/virtual-wan-expressroute-portal/defaultroute2.png "Standaardroute doorgeven")

## <a name="clean-up-resources"></a><a name="cleanup"></a>Resources opschonen

Als u de resources die u hebt gemaakt, niet meer nodig hebt, verwijdert u deze. Sommige virtuele WAN-resources moeten in een bepaalde volg orde worden verwijderd vanwege afhankelijkheden. Het duurt ongeveer 30 minuten om het verwijderen te volt ooien.

[!INCLUDE [Delete resources](../../includes/virtual-wan-resource-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Virtual WAN:

> [!div class="nextstepaction"]
> * [Veelgestelde vragen over Virtual WAN](virtual-wan-faq.md)
