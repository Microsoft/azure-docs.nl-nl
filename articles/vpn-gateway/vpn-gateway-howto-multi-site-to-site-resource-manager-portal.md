---
title: 'Meerdere VPN-gateway site-to-site-verbindingen toevoegen aan een VNet: Azure Portal'
description: Multi-site S2S-verbindingen toevoegen aan een VPN-gateway die een bestaande verbinding heeft
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/27/2020
ms.author: cherylmc
ms.openlocfilehash: 92d39b0d39511571701fd092f641cb8ca3ae42c7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92890145"
---
# <a name="add-additional-s2s-connections-to-a-vnet-azure-portal"></a>Aanvullende S2S-verbindingen toevoegen aan een VNet: Azure Portal

> [!div class="op_single_selector"]
> * [Azure-portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (klassiek)](vpn-gateway-multi-site.md)
>

Dit artikel helpt u bij het toevoegen van extra site-naar-site (S2S)-verbindingen met een VPN-gateway die een bestaande verbinding heeft. Deze architectuur wordt vaak een configuratie met meerdere locaties genoemd. U kunt een S2S-verbinding toevoegen aan een VNet dat al een S2S-verbinding, punt-naar-site-verbinding of VNet-naar-VNet-verbinding heeft. Er zijn enkele beperkingen bij het toevoegen van verbindingen. Raadpleeg de sectie [vereisten](#before) in dit artikel om te controleren voordat u begint met de configuratie.

Dit artikel is van toepassing op Resource Manager-VNets die een RouteBased VPN-gateway hebben. Deze stappen zijn niet van toepassing op nieuwe ExpressRoute/site-to-site naast elkaar bestaande verbindings configuraties. Als u echter alleen maar een nieuwe VPN-verbinding toevoegt aan een bestaande naast elkaar aanwezige configuratie, kunt u deze stappen gebruiken. Zie [ExpressRoute/S2S-bestaande verbindingen](../expressroute/expressroute-howto-coexist-resource-manager.md) voor informatie over naast elkaar bestaande verbindingen.

## <a name="prerequisites"></a><a name="before"></a>Vereisten

Controleer de volgende items:

* U configureert geen nieuwe cobestaande ExpressRoute-en VPN Gateway configuratie.
* U hebt een virtueel netwerk dat is gemaakt met behulp van het Resource Manager-implementatie model met een bestaande verbinding.
* De virtuele netwerk gateway voor uw VNet is RouteBased. Als u een PolicyBased VPN-gateway hebt, moet u de gateway van het virtuele netwerk verwijderen en een nieuwe VPN-gateway maken als RouteBased.
* Geen van de adresbereiken overlapt voor een van de VNets waarmee dit VNet verbinding maakt.
* U hebt een compatibel VPN-apparaat en iemand die dit kan configureren. Zie [About VPN Devices](vpn-gateway-about-vpn-devices.md) (Over VPN-apparaten). Als u niet weet hoe u uw VPN-apparaat moet configureren of de IP-adresbereiken in uw on-premises netwerkconfiguratie niet kent, moet u contact opnemen met iemand die u hierbij kan helpen en de benodigde gegevens kan verstrekken.
* U hebt een extern gericht openbaar IP-adres voor uw VPN-apparaat.

## <a name="configure-a-connection"></a><a name="configure"></a>Een verbinding configureren

1. Navigeer via een browser naar de [Azure Portal](https://portal.azure.com) en log, indien nodig, in met uw Azure-account.
1. Selecteer **alle resources** en zoek de **gateway van uw virtuele netwerk** in de lijst met resources en selecteer deze.
1. Selecteer **verbindingen** op de pagina **virtuele netwerk gateway** .

   :::image type="content" source="./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connections.png" alt-text="VPN-gateway verbindingen":::
1. Selecteer op de pagina **verbindingen** **+ toevoegen**.
1. Hiermee opent u de pagina **verbinding toevoegen** .

   :::image type="content" source="./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/add-connection.png" alt-text="Verbindings pagina toevoegen":::
1. Vul de volgende velden in op de pagina **verbinding toevoegen** :

   * **Naam:** De naam die u wilt toekennen aan de site waarmee u de verbinding maakt.
   * **Verbindings type:** Selecteer **site-naar-site (IPSec)**.

## <a name="add-a-local-network-gateway"></a><a name="local"></a>Een lokale netwerk gateway toevoegen

1. Selecteer in het veld **lokale netwerk gateway** de optie **_een lokale netwerk gateway_ selecteren *_. Hiermee opent u de pagina _* lokale netwerk gateway kiezen** .
1. Selecteer **+ nieuwe maken** om de pagina **lokale netwerk gateway maken** te openen.

   :::image type="content" source="./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/create-local-network-gateway.png" alt-text="Pagina lokale netwerk gateway maken":::
1. Vul op de pagina **lokale netwerk gateway maken** de volgende velden in:

   * **Naam:** De naam die u aan de lokale netwerk gateway resource wilt geven.
   * **Eind punt:** Het open bare IP-adres van het VPN-apparaat op de site waarmee u verbinding wilt maken of de FQDN-naam van het eind punt.
   * **Adres ruimte:** De adres ruimte die u wilt omleiden naar de nieuwe lokale netwerk site.
1. Selecteer **OK** op de pagina **lokale netwerk gateway maken** om de wijzigingen op te slaan.

## <a name="add-the-shared-key"></a><a name="part3"></a>De gedeelde sleutel toevoegen

1. Nadat u de lokale netwerk gateway hebt gemaakt, gaat u terug naar de pagina **verbinding toevoegen** .
1. Vul de resterende velden in. Voor de **gedeelde sleutel (PSK)** kunt u de gedeelde sleutel van uw VPN-apparaat ophalen of hier een van maken en vervolgens uw VPN-apparaat configureren voor het gebruik van dezelfde gedeelde sleutel. Het belangrijkste is dat de sleutels precies hetzelfde zijn.

## <a name="create-the-connection"></a><a name="create"></a>De verbinding maken

1. Selecteer aan de onderkant van de pagina **OK** om de verbinding te maken. De verbinding wordt direct gemaakt.
1. Zodra de verbinding is voltooid, kunt u deze weer geven en verifiëren.

## <a name="view-and-verify-the-vpn-connection"></a><a name="verify"></a>De VPN-verbinding weer geven en verifiëren

[!INCLUDE [Verify the connection](../../includes/vpn-gateway-verify-connection-portal-include.md)]

## <a name="next-steps"></a>Volgende stappen

Wanneer de verbinding is voltooid, kunt u virtuele machines aan uw virtuele netwerken toevoegen. Zie voor meer informatie paden voor het [leren van virtuele machines](/learn/paths/deploy-a-website-with-azure-virtual-machines/).
