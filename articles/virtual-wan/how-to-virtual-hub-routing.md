---
title: Virtuele hubroutering configureren
titleSuffix: Azure Virtual WAN
description: In dit artikel wordt beschreven hoe u route ring van virtuele hub configureert
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: cae74a5f4859d208765c9a7e5cde05ff3c0c3096
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91313685"
---
# <a name="how-to-configure-virtual-hub-routing"></a>Virtuele hubroutering configureren

Een virtuele hub kan meerdere gateways bevatten, zoals een site-naar-site-VPN-gateway, ExpressRoute-gateway, Point-to-site gateway en Azure Firewall. De routerings mogelijkheden in de virtuele hub worden geboden door een router die alle route ring beheert, met inbegrip van transit routering, tussen de gateways met behulp van Border Gateway Protocol (BGP). Deze router biedt ook transit connectiviteit tussen virtuele netwerken die verbinding maken met een virtuele hub en die een geaggregeerde door Voer van 50 Gbps kan ondersteunen. Deze routerings mogelijkheden zijn van toepassing op standaard virtuele WAN-klanten.

Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie.

## <a name="create-a-route-table"></a><a name="create-table"></a>Een route tabel maken

1. Navigeer in het Azure Portal naar de virtuele hub.
2. Onder **connectiviteit** selecteert u **route ring**. Op de pagina route ring ziet u de **standaard** -en **geen** -route tabellen.

   :::image type="content" source="./media/how-to-virtual-hub-routing/routing.png" alt-text="Routerings pagina":::
3. Selecteer **+ route tabel maken** om de pagina **route tabel maken** te openen.
4. Vul de volgende velden in op het tabblad **basis** pagina's voor route tabel maken.

   :::image type="content" source="./media/how-to-virtual-hub-routing/basics.png" alt-text="Tabblad Basisbeginselen":::

   * **Naam**
   * **Routes**
   * **Routenaam**
   * **Doeltype**
   * **Voor voegsel voor bestemming**: u kunt voor voegsels samen voegen. Bijvoorbeeld: VNet 1:10.1.0.0/24 en VNet 2:10.1.1.0/24 kunnen worden geaggregeerd als 10.1.0.0/16. **Vertakkings** routes zijn van toepassing op alle verbonden VPN-sites, ExpressRoute-circuits en gebruikers-VPN-verbindingen.
   * **Volgende hop**: een lijst met virtuele netwerk verbindingen, of Azure firewall.

     Als u een virtuele netwerk verbinding selecteert, worden **statische routes configureren** weer geven. Dit is een optionele configuratie-instelling. Zie [statische routes configureren](about-virtual-hub-routing.md#static)voor meer informatie.

      :::image type="content" source="./media/how-to-virtual-hub-routing/next-hop.png" alt-text="Volgende hop":::

5. Selecteer het tabblad **labels** om label namen te configureren. Labels bieden een mechanisme voor het logisch groeperen van route tabellen.

    :::image type="content" source="./media/how-to-virtual-hub-routing/labels.png" alt-text="Label namen configureren":::

6. Selecteer het tabblad **koppelingen** om verbindingen met de route tabel te koppelen.
U ziet **vertakkingen**, **virtuele netwerken** en de **huidige instellingen** van de verbindingen.

    :::image type="content" source="./media/how-to-virtual-hub-routing/associations.png" alt-text="Koppelings verbindingen met de route tabel":::

7. Selecteer het tabblad **doorgiftes** om routes door te geven van verbindingen met de route tabel.

    :::image type="content" source="./media/how-to-virtual-hub-routing/propagations.png" alt-text="Routes door geven":::

8. Selecteer **maken** om de route tabel te maken.

## <a name="to-edit-a-route-table"></a><a name="edit-table"></a>Een route tabel bewerken

Zoek in de Azure Portal de route tabel van de virtuele hub. Selecteer de route tabel om gegevens te bewerken.

## <a name="to-delete-a-route-table"></a><a name="delete-table"></a>Een route tabel verwijderen

Zoek in de Azure Portal de route tabel van de virtuele hub. U kunt een standaard-of geen route tabel verwijderen. U kunt echter alle aangepaste route tabellen verwijderen. Klik op **'... '** en selecteer **verwijderen**.

## <a name="to-view-effective-routes"></a><a name="view-routes"></a>Efficiënte routes weer geven

Zoek in de Azure Portal de route tabel van de virtuele hub. Klik op **'... '** en selecteer de **juiste routes** om de routes weer te geven die zijn geleerd door de geselecteerde route tabel. Door gegeven routes van de verbinding met de route tabel worden automatisch ingevuld in de **juiste routes** van de route tabel. Zie [informatie over efficiënte routes](effective-routes-virtual-hub.md)voor meer informatie.

:::image type="content" source="./media/how-to-virtual-hub-routing/effective.png" alt-text="Efficiënte routes weer geven" lightbox="./media/how-to-virtual-hub-routing/effective-expand.png":::

## <a name="to-set-up-routing-configuration-for-a-virtual-network-connection"></a><a name="routing-configuration"></a>Routerings configuratie voor een virtuele netwerk verbinding instellen

1. Ga in het Azure Portal naar uw virtuele WAN en selecteer **Virtual Network verbindingen** onder **connectiviteit**.
1. Selecteer **+ verbinding toevoegen**.
1. Selecteer het virtuele netwerk in de vervolg keuzelijst.
1. Stel de routerings configuratie in om aan een route tabel te koppelen. Selecteer voor **koppelen route tabel** de route tabel in de vervolg keuzelijst.
1. Stel de routerings configuratie zo in dat deze wordt door gegeven aan een of meer route tabellen. Selecteer in de vervolg keuzelijst voor **door geven naar route tabel**.
1. Configureer voor **statische routes** statische routes voor het virtuele netwerk apparaat (indien van toepassing). Virtual WAN ondersteunt één volgende hop-IP voor statische route in een virtuele netwerk verbinding. Als u bijvoorbeeld een apart virtueel apparaat hebt voor binnenkomend en uitgaand verkeer, kunt u het beste de virtuele apparaten in afzonderlijke VNETs en de VNETs koppelen aan de virtuele hub.


:::image type="content" source="./media/how-to-virtual-hub-routing/routing-configuration.png" alt-text="Routerings configuratie instellen" lightbox="./media/how-to-virtual-hub-routing/routing-configuration-expand.png":::

## <a name="next-steps"></a>Volgende stappen

Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over virtuele-hub-route ring.
Raadpleeg de [Veelgestelde vragen](virtual-wan-faq.md)voor meer informatie over Virtual WAN.
