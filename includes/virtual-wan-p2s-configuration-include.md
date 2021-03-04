---
author: cherylmc
ms.author: cherylmc
ms.date: 02/23/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 567c0bb75c30a1f0ccdcde7ec1b0f04f5d6e54c5
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102048251"
---
[!INCLUDE [Portal feature rollout](virtual-wan-portal-feature-rollout.md)]

1. Ga naar **alle resources** en selecteer het virtuele WAN dat u hebt gemaakt en selecteer vervolgens **gebruikers VPN-configuraties** in het menu aan de linkerkant.
1. Selecteer op de pagina **configuratie van gebruikers-VPN** de optie **+ gebruikers VPN-configuratie maken** boven aan de pagina om de pagina **nieuwe gebruiker VPN-configuratie maken** te openen.

   :::image type="content" source="media/virtual-wan-p2s-configuration/user-vpn.png" alt-text="Scherm afbeelding van de pagina met VPN-configuraties voor gebruikers.":::

1. Voer op het tabblad **basis** **informatie onder Details van exemplaar** de **naam** in die u wilt toewijzen aan uw VPN-configuratie.
1. Selecteer bij **Tunnel Type**, in de vervolg keuzelijst, het tunnel type dat u wilt. De opties voor het tunnel type zijn: **IKEV2 VPN**, **openvpn** en **openvpn en IKEv2**.
1. Gebruik de volgende stappen die overeenkomen met het tunnel type dat u hebt geselecteerd. Nadat alle waarden zijn opgegeven, klikt u op **controleren + maken** en vervolgens op **maken** om de configuratie te maken.

   **IKEv2 VPN**

   * **Vereisten:** Wanneer u het **IKEv2** -Tunnel Type selecteert, wordt er een bericht weer gegeven waarin u wordt omgeleid om een verificatie methode te selecteren. Voor IKEv2 kunt u slechts één verificatie methode opgeven. U kunt kiezen voor Azure-certificaat, Azure Active Directory of verificatie op basis van RADIUS.

   * **Aangepaste IPSec-para meters:** Als u de para meters voor IKE fase 1 en IKE fase 2 wilt aanpassen, schakelt u de IPsec-switch in op **aangepast** en selecteert u de parameter waarden. Zie het [aangepaste IPsec-](../articles/virtual-wan/point-to-site-ipsec.md) artikel voor meer informatie over aanpas bare para meters.

     :::image type="content" source="media/virtual-wan-p2s-configuration/custom.png" alt-text="Scherm opname van IPsec-switch naar aangepast.":::

   * **Verificatie:** Navigeer naar het verificatie mechanisme dat u wilt gebruiken door te klikken op **volgende** onder aan de pagina om naar de verificatie methode te gaan of klik op het juiste tabblad boven aan de pagina. Schakel de optie in op **Ja** om de methode te selecteren.

     In dit voor beeld wordt RADIUS-verificatie geselecteerd. Voor RADIUS-gebaseerde verificatie kunt u een IP-adres en server geheim voor een secundaire RADIUS-server opgeven.

     :::image type="content" source="media/virtual-wan-p2s-configuration/ike-radius.png" alt-text="Scherm opname van IKE.":::

   **OpenVPN**

   * **Vereisten:** Wanneer u het tunnel type **openvpn** selecteert, wordt er een bericht weer gegeven waarin u wordt omgeleid om een verificatie mechanisme te selecteren. Als OpenVPN is geselecteerd als het tunnel type, kunt u meerdere verificatie methoden opgeven. U kunt kiezen uit een subset van Azure-certificaat, Azure Active Directory of verificatie op basis van RADIUS. Voor RADIUS-gebaseerde verificatie kunt u een IP-adres en server geheim voor een secundaire RADIUS-server opgeven.

   * **Verificatie:** Ga naar de verificatie methode (s) die u wilt gebruiken door te klikken op **volgende** onder aan de pagina om naar de verificatie methode te gaan of klik op het juiste tabblad boven aan de pagina.
   Voor elke methode die u wilt selecteren, schakelt u over naar **Ja** en voert u de juiste waarden in.

     In dit voor beeld is Azure Active Directory geselecteerd.

     :::image type="content" source="media/virtual-wan-p2s-configuration/openvpn.png" alt-text="Scherm opname van de pagina OpenVPN.":::
