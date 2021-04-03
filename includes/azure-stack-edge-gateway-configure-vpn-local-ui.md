---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/27/2020
ms.author: alkohli
ms.openlocfilehash: fa65a7354112a2b220686372459b348d45832dd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96466637"
---
Voer de volgende stappen uit in de lokale webgebruikersinterface van uw apparaat. Deze stap duurt ongeveer 15 minuten, met inbegrip van het uploaden van het VPN-configuratie bestand (of het servicetag bestand). 

1. Ga naar **configuratie > VPN**. Selecteer **Configureren**.

    ![Lokale gebruikers interface 1 configureren](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-1.png)

2. Doe het volgende op de blade **VPN configureren**:

    - Schakel **VPN-instellingen** in.
    - Geef het **gedeelde VPN-geheim** op. Dit is de gedeelde sleutel die u hebt ingevoerd tijdens het maken van de Azure VPN-verbindings resource.
    - Geef het **IP-adres van de VPN-gateway** op. Dit is het IP-adres van de lokale netwerkgateway van Azure.
    - Selecteer **Geen** bij **PFS-groep**. 
    - Selecteer **Groep2** bij **DH-groep**.
    - Selecteer **IPsec-integriteitsmethode** bij **SHA256**.
    - Voor **IPSec cipher-transformatie constanten** selecteert u **GCMAES256**.
    - Selecteer **Transformatieconstanten voor IPseccipher-verificatie** bij **GCMAES256**.
    - Selecteer **IKE-versleutelingsmethode** bij **AES256**.
    - Selecteer **Toepassen**.

        ![Lokale gebruikersinterface 2 configureren](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-2.png)

    Ga naar [over cryptografische vereisten en Azure VPN-gateways](../articles/vpn-gateway/vpn-gateway-about-compliance-crypto.md#ipsecike-policy-faq)voor meer informatie over de ondersteunde cryptografische algoritmen. 

3. Als u het configuratiebestand voor de VPN-route wilt uploaden, selecteert u **Uploaden**. 

    ![Lokale gebruikersinterface 3 configureren](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-3.png)

    - Blader naar het *JSON* -bestand met Service Tags dat u in de vorige stap hebt gedownload op uw lokale systeem.
    - Selecteer de regio als de Azure-regio die gekoppeld is aan het apparaat, het virtuele netwerk en de gateways.
    - Selecteer **Toepassen**.

        ![Lokale gebruikersinterface 4 configureren](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-4.png)
    
    De upload duurt ongeveer 7-8 minuten op het apparaat.

4. U voegt client-specifieke routes toe door IP-adresbereiken te configureren die alleen met VPN mogen worden geopend. 

    - Selecteer onder **IP-adresbereiken die alleen met VPN mogen worden geopend** de optie **Configureren**.
    - Geef een geldig IPv4-bereik op en selecteer **Toevoegen**. Herhaal de stappen om andere bereiken toe te voegen.
    - Selecteer **Toepassen**.

        ![Lokale gebruikersinterface 5 configureren](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-5.png)

