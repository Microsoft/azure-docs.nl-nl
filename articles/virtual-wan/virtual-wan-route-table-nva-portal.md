---
title: 'Virtueel WAN: route tabel voor virtuele hub maken naar NVA: Azure Portal'
description: Route tabel virtuele WAN-hub voor het stuur verkeer naar een virtueel netwerk apparaat via de portal.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a route table using the portal.
ms.openlocfilehash: 8de7ad2808e5319819410b3125472e28496647b0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91267139"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>Een route tabel voor virtuele WAN-hub maken voor Nva's: Azure Portal

In dit artikel wordt beschreven hoe u verkeer van een vertakking (on-premises site) kunt sturen die is verbonden met de virtuele WAN-hub met een spoke Virtual Network (VNet) via een virtueel netwerk apparaat (NVA).

![Virtual WAN-diagram](./media/virtual-wan-route-table-nva/vwanroute.png)

## <a name="before-you-begin"></a>Voordat u begint

Controleer of u aan de volgende criteria hebt voldaan:

*  U hebt een virtueel netwerk apparaat (NVA). Een virtueel netwerk apparaat is een software van derden van uw keuze die doorgaans wordt ingericht vanuit Azure Marketplace in een virtueel netwerk.

    * Een privé-IP-adres moet worden toegewezen aan de NVA-netwerk interface.

    * De NVA is niet geïmplementeerd in de virtuele hub. Het moet worden geïmplementeerd in een afzonderlijk virtueel netwerk.

    *  Er zijn mogelijk een of meer virtuele netwerken verbonden met het virtuele NVA-netwerk. In dit artikel verwijzen we naar het virtuele NVA-netwerk als een ' indirecte spoke-VNet '. Deze virtuele netwerken kunnen worden verbonden met het NVA VNet met behulp van VNet-peering. De VNet-peering koppelingen worden weer gegeven met zwarte pijlen in de bovenstaande afbeelding tussen VNet 1, VNet 2 en NVA VNet.
*  U hebt twee virtuele netwerken gemaakt. Ze worden gebruikt als spoke VNets.

    * De VNet-spoke-adres ruimten zijn: VNet1:10.0.2.0/24 en VNet2:10.0.3.0/24. Zie [een virtueel netwerk maken](../virtual-network/quick-create-portal.md)als u meer informatie nodig hebt over het maken van een virtueel netwerk.

    * Zorg ervoor dat er geen virtuele netwerk gateways aanwezig zijn in een van de VNets.

    * Voor de VNets is geen gateway-subnet vereist.

## <a name="1-sign-in"></a><a name="signin"></a>1. aanmelden

Open een browser, ga naar [Azure Portal](https://portal.azure.com) en meld u aan met uw Azure-account.

## <a name="2-create-a-virtual-wan"></a><a name="vwan"></a>2. een virtueel WAN maken

Maak een virtueel WAN. Gebruik de volgende voorbeeld waarden:

* **Virtuele WAN-naam:** myVirtualWAN
* **Resource groep:** testRG
* **Locatie:** VS-West

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="3-create-a-hub"></a><a name="hub"></a>3. een hub maken

Maak de hub. Gebruik de volgende voorbeeld waarden:

* **Locatie:** VS-West
* **Naam:** westushub
* **Privé-adres ruimte van hub:** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="4-create-and-apply-a-hub-route-table"></a><a name="route"></a>4. een hub-route tabel maken en Toep assen

De hub bijwerken met een route tabel van de hub. Gebruik de volgende voorbeeld waarden:

* **Spoke VNet-adres ruimten:** (VNet1 en VNet2) 10.0.2.0/24 en 10.0.3.0/24
* **Privé IP-adres van DMZ NVA Network Interface:** 10.0.4.5

1. Navigeer naar uw virtuele WAN.
2. Klik op de hub waarvoor u een route tabel wilt maken.
3. Klik op de **...** en klik vervolgens op **virtuele hub bewerken**.
4. Schuif op de pagina **virtuele hub bewerken** omlaag en selecteer de selectie vakje **tabel gebruiken voor route ring**.
5. Voeg in de kolom **als het voor voegsel** van de bestemming de adres ruimten toe. Voeg in de kolom **verzenden naar volgende hop** het privé IP-adres van de DMZ NVA-netwerk interface toe.

   > [!NOTE]
   > Het DMZ NVA-netwerk is van toepassing op de lokale hub.
   
6. Klik op **bevestigen** om de resource hub bij te werken met de instellingen van de route tabel.

## <a name="5-create-the-vnet-connections"></a><a name="connections"></a>5. de VNet-verbindingen maken

Maak een virtuele netwerk verbinding van elke indirecte spoke VNet (VNet1 en VNet2) naar de hub. Deze virtuele netwerk verbindingen worden weer gegeven door de blauwe pijlen in de bovenstaande afbeelding. Maak vervolgens een VNet-verbinding van de NVA VNet naar de hub (zwarte pijl in de afbeelding).

 Voor deze stap kunt u de volgende waarden gebruiken:

| Naam van virtueel netwerk| Verbindingsnaam|
| --- | --- |
| VNet1 | testconnection1 |
| VNet2 | testconnection2 |
| NVAVNet | testconnection3 |

Herhaal de volgende procedure voor elk virtueel netwerk waarmee u verbinding wilt maken.

1. Klik op de pagina voor uw virtuele WAN op **Virtuele netwerkverbinding**.
2. Klik op de pagina Virtuele netwerkverbinding op **+Verbinding toevoegen**.
3. Vul de volgende velden in op de pagina **Verbinding toevoegen**:

    * **Verbindingsnaam** - voer een naam in voor uw verbinding.
    * **Hubs** - selecteer de hub die u wilt koppelen aan deze verbinding.
    * **Abonnement** - controleer of het abonnement klopt.
    * **Virtueel netwerk** - selecteer het virtuele netwerk dat met deze hub wilt verbinden. Het virtuele netwerk mag geen bestaande virtuele netwerkgateway hebben.
4. Klik op **OK** om de verbinding te maken.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Virtual WAN de pagina [Overzicht van Virtual WAN](virtual-wan-about.md).
