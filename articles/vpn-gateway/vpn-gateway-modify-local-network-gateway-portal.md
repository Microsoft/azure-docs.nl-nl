---
title: 'VPN Gateway: Wijzig de IP-adres instellingen van de gateway: Azure Portal'
description: In dit artikel vindt u instructies voor het wijzigen van IP-adres voorvoegsels voor uw lokale netwerk gateway met behulp van de Azure Portal.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/16/2020
ms.author: cherylmc
ms.openlocfilehash: 4ff4f1238764d7bdab6e74d29254a6388ea76d78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92143155"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Instellingen voor lokale netwerkgateway wijzigen via Azure-portal

Soms worden de instellingen voor de AddressPrefix of GatewayIPAddress van uw lokale netwerk gateway gewijzigd. In dit artikel wordt beschreven hoe u de instellingen van uw lokale netwerk gateway wijzigt. U kunt deze instellingen ook met behulp van een andere methode wijzigen door een andere optie te selecteren in de volgende lijst:

> [!div class="op_single_selector"]
> * [Azure-portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure-CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>

## <a name="local-network-gateway-configuration"></a><a name="configure-lng"></a>Configuratie van de lokale netwerk gateway

In de onderstaande scherm afbeelding ziet u de **configuratie** pagina van een lokale netwerk gateway resource die gebruikmaakt van een openbaar IP-adres eindpunt:

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/settings.png" alt-text="Instellingen voor IP-adres" lightbox="./media/vpn-gateway-modify-local-network-gateway-portal/settings-expand.png":::

Dit is de configuratie pagina met een FQDN-eind punt:

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/name.png" alt-text="FQDN-instellingen":::

## <a name="to-modify-the-gateway-ip-address-or-fqdn"></a><a name="ip"></a>Het IP-adres of de FQDN van de gateway wijzigen

> [!NOTE]
> U kunt een lokale netwerk gateway niet wijzigen tussen het FQDN-eind punt en het eind punt van het IP-adres. U moet alle verbindingen verwijderen die zijn gekoppeld aan deze lokale netwerk gateway, een nieuwe maken met het nieuwe eind punt (IP-adres of FQDN-naam) en vervolgens de verbindingen opnieuw maken.
>

Als het open bare IP-adres van het VPN-apparaat waarmee u verbinding wilt maken, is gewijzigd, wijzigt u de lokale netwerk gateway door de volgende stappen uit te voeren:

1. Selecteer op de lokale netwerk gateway resource in de sectie **instellingen** de optie **configuratie**.
2. Wijzig het IP-adres in het vak **IP-adres** .
3. Selecteer **Opslaan** om de instellingen op te slaan.

Als het VPN-apparaat waarmee u verbinding wilt maken, de FQDN-naam (Fully Qualified Domain Name) heeft gewijzigd, wijzigt u de lokale netwerk gateway door de volgende stappen uit te voeren:

1. Selecteer op de lokale netwerk gateway resource in de sectie **instellingen** de optie **configuratie**.
2. Wijzig de domein naam in het vak **FQDN** .
3. Selecteer **Opslaan** om de instellingen op te slaan.

## <a name="to-modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>IP-adres voorvoegsels wijzigen

Ga als volgt te werk om aanvullende voorvoegsels toe te voegen:

1. Selecteer op de lokale netwerk gateway resource in de sectie **instellingen** de optie **configuratie**.
2. Voeg de IP-adres ruimte toe in het vak *extra adres bereik toevoegen* .
3. Selecteer **Opslaan** om uw instellingen op te slaan.

Ga als volgt te werk om adresvoorvoegsels te verwijderen:

1. Selecteer op de lokale netwerk gateway resource in de sectie **instellingen** de optie **configuratie**.
2. Selecteer **'... '** op de regel met het voor voegsel dat u wilt verwijderen.
3. Selecteer **Verwijderen**.
4. Selecteer **Opslaan** om uw instellingen op te slaan.

## <a name="to-modify-bgp-settings"></a><a name="bgp"></a>BGP-instellingen wijzigen

BGP-instellingen toevoegen of bijwerken:

1. Selecteer op de lokale netwerk gateway resource in de sectie **instellingen** de optie **configuratie**.
2. Selecteer **BGP-instellingen configureren** om de BGP-configuraties voor deze lokale netwerk gateway weer te geven of bij te werken
3. Het autonoom systeem nummer of het IP-adres van de BGP-peer toevoegen of bijwerken in de bijbehorende velden
4. Selecteer **Opslaan** om uw instellingen op te slaan.

De BGP-instellingen verwijderen:

1. Selecteer op de lokale netwerk gateway resource in de sectie **instellingen** de optie **configuratie**.
2. Schakel het selectie vakje **BGP-instellingen configureren** uit om het bestaande IP-adres van de BGP-ASN en BGP-peer te verwijderen
3. Selecteer **Opslaan** om uw instellingen op te slaan.

## <a name="next-steps"></a>Volgende stappen

U kunt uw gateway verbinding controleren. Zie [een gateway verbinding controleren](vpn-gateway-verify-connection-resource-manager.md).
