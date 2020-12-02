---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/27/2020
ms.author: alkohli
ms.openlocfilehash: fa65a7354112a2b220686372459b348d45832dd9
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466637"
---
Voer de volgende stappen uit in de lokale webgebruikersinterface van uw apparaat. Deze stap duurt ongeveer 15 minuten, met inbegrip van het uploaden van het VPN-configuratie bestand (of het servicetag bestand). 

1. Ga naar **configuratie > VPN**. Selecteer **Configureren**.

    ![Lokale gebruikers interface 1 configureren](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-1.png)

2. Op de Blade **VPN configureren** :

    - **VPN-instellingen** inschakelen.
    - Geef het **gedeelde VPN-geheim** op. Dit is de gedeelde sleutel die u hebt ingevoerd tijdens het maken van de Azure VPN-verbindings resource.
    - Geef het **IP-adres van de VPN-gateway** op. Dit is het IP-adres van de lokale netwerk gateway van Azure.
    - Selecteer voor **PFS-groep** de optie **geen**. 
    - Selecteer voor **DH**-groep **group2**.
    - Selecteer voor **IPSec-integriteits methode** **sha256**.
    - Voor **IPSec cipher-transformatie constanten** selecteert u **GCMAES256**.
    - Selecteer **GCMAES256** voor **IPSec-verificatie transformatie constanten**.
    - Selecteer voor **IKE-versleutelings methode** **AES256**.
    - Selecteer **Toepassen**.

        ![Lokale gebruikers interface 2 configureren](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-2.png)

    Ga naar [over cryptografische vereisten en Azure VPN-gateways](../articles/vpn-gateway/vpn-gateway-about-compliance-crypto.md#ipsecike-policy-faq)voor meer informatie over de ondersteunde cryptografische algoritmen. 

3. Selecteer **uploaden** als u het configuratie bestand voor de VPN-route wilt uploaden. 

    ![Lokale gebruikers interface 3 configureren](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-3.png)

    - Blader naar het *JSON* -bestand met Service Tags dat u in de vorige stap hebt gedownload op uw lokale systeem.
    - Selecteer de regio als de Azure-regio die is gekoppeld aan het apparaat, het virtuele netwerk en de gateways.
    - Selecteer **Toepassen**.

        ![Lokale gebruikers interface 4 configureren](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-4.png)
    
    De upload duurt ongeveer 7-8 minuten op het apparaat.

4. Als u client-specifieke routes wilt toevoegen, moet u IP-adresbereiken zodanig configureren dat ze alleen toegankelijk zijn via VPN. 

    - Selecteer **configureren** onder **IP-adresbereiken voor toegang via alleen VPN**.
    - Geef een geldig IPv4-bereik op en selecteer **toevoegen**. Herhaal de stappen om andere bereiken toe te voegen.
    - Selecteer **Toepassen**.

        ![Lokale gebruikers interface 5 configureren](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-5.png)

