---
title: Zelfstudie voor configureren van certificaten voor een Azure Stack Edge Pro R-apparaat in de Azure-portal | Microsoft Docs
description: In de zelfstudie voor het implementeren van Azure Stack Edge Pro R krijgt u de instructie om certificaten op uw fysieke apparaat te configureren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/19/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure certificates for Azure Stack Edge Pro R so I can use it to transfer data to Azure.
ms.openlocfilehash: fad3e5dcb0ecda82f3fb35cadf1719a62c99bd97
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96464061"
---
# <a name="tutorial-configure-certificates-for-your-azure-stack-edge-pro-r"></a>Zelfstudie: Certificaten voor uw Azure Stack Edge Pro R configureren

In deze zelfstudie wordt beschreven hoe u certificaten voor uw Azure Stack Edge Pro R-apparaat kunt configureren door de lokale webinterface te gebruiken.

De tijd die nodig is voor deze stap kan variëren afhankelijk van de specifieke optie die u kiest en hoe de certificaatstroom is opgezet in uw omgeving.

In deze zelfstudie komen deze onderwerpen aan bod:

> [!div class="checklist"]
>
> * Vereisten
> * Certificaten voor het fysieke apparaat configureren
> * VPN configureren
> * Configureer versleuteling 'at rest'

## <a name="prerequisites"></a>Vereisten

Zorg dat aan deze voorwaarden wordt voldaan voordat u uw Azure Stack Edge Pro R-apparaat configureert en instelt:

* U hebt het fysieke apparaat geïnstalleerd zoals beschreven in [Azure Stack Edge Pro R installeren](azure-stack-edge-pro-r-deploy-install.md).
* Als u van plan bent uw eigen certificaten te gebruiken:
    - Zorg ervoor dat uw certificaten bij de hand hebt in de juiste indeling, inclusief het certificaat van de ondertekeningsketen. Ga voor meer informatie over het certificaat naar [Certificaten beheren](azure-stack-edge-j-series-manage-certificates.md)



## <a name="configure-certificates-for-device"></a>Certificaten configureren voor apparaat

1. Op de pagina **Certificaten** gaat u uw certificaten configureren. Afhankelijk van of u de apparaatnaam of het DNS-domein hebt gewijzigd in de tegel **Apparaatinstallatie**, kunt u een van de volgende opties voor uw certificaten kiezen.

    - Als u de apparaatnaam of het DNS-domein in de vorige stap niet hebt gewijzigd, kunt u deze stap overslaan en met de volgende stap verdergaan. Om te beginnen heeft het apparaat automatisch zelfondertekende certificaten gegenereerd. 

    - Als u de apparaatnaam of het DNS-domein hebt gewijzigd, ziet u dat de status van certificaten wordt weergegeven als **Niet geldig**. 

        ![Pagina 2 Certificaten van lokale webinterface](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/generate-certificate-1.png)    

        Selecteer een certificaat om de details van de status weer te geven.

        ![Pagina 3 Certificaten van lokale webinterface](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/generate-certificate-1a.png)  

        Dit komt doordat de certificaten niet overeenkomen met de bijgewerkte apparaatnaam en het DNS-domein (die worden gebruikt bij onderwerpnaam en alternatief onderwerp). Om uw apparaat te activeren, kunt u uw eigen ondertekende eindpuntcertificaten en de bijbehorende ondertekeningsketens gebruiken. U voegt eerst de ondertekeningsketens toe en uploadt vervolgens de eindpuntcertificaten. Ga voor meer informatie naar [Uw eigen certificaten op uw Azure Stack Edge Pro R-apparaat gebruiken](#bring-your-own-certificates).

    - Als u de apparaatnaam of het DNS-domein hebt gewijzigd en u geen eigen certificaten gebruikt, wordt de **activering geblokkeerd**.

    
#### <a name="bring-your-own-certificates"></a>Uw eigen certificaten gebruiken

Volg deze stappen om uw eigen certificaten met inbegrip van de ondertekeningsketen toe te voegen.

1. Als u een certificaat wilt uploaden, selecteert u op de pagina **Certificaat** de optie **+ Certificaat toevoegen**.

    ![Pagina 4 Certificaten van lokale webinterface](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-1.png)

2. Upload eerst de ondertekeningsketen en selecteer **Valideren en toevoegen**.

    ![Pagina 5 Certificaten van lokale webinterface](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-2.png)

3. U kunt nu andere certificaten uploaden. U kunt bijvoorbeeld de Azure Resource Manager- en Blob Storage-eindpuntcertificaten uploaden.

    ![Pagina 6 Certificaten van lokale webinterface](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-3.png)

    U kunt ook het lokale webinterfacecertificaat uploaden. Nadat u dit certificaat hebt geüpload, wordt u gevraagd om uw browser te starten en de cache te wissen. Vervolgens moet u verbinding maken met de lokale webinterface van het apparaat.  

    ![Pagina 7 Certificaten van lokale webinterface](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-5.png)

    U kunt ook het knooppuntcertificaat uploaden.

    ![Pagina 8 Certificaten van lokale webinterface](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-4.png)

    Tot slot kunt u het VPN-certificaat uploaden.

    ![Pagina 9 Certificaten van lokale webinterface](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-4.png)

    U kunt op elk gewenst moment een certificaat selecteren en de details weergeven om ervoor te zorgen dat deze overeenkomen met het certificaat dat u hebt geüpload.

    De certificaatpagina zou moeten worden bijgewerkt en de zojuist toegevoegde certificaten weergeven.

    ![Pagina 10 Certificaten van lokale webinterface](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-7.png)  

    > [!NOTE]
    > Met uitzondering van de openbare Azure-cloud moeten ondertekeningsketencertificaten worden ingesteld voordat alle cloudconfiguraties worden geactiveerd (Azure Government of Azure Stack).

1. Selecteer **< Terug om aan de slag te gaan**.

## <a name="configure-vpn"></a>VPN configureren

1. Selecteer **Configureren** voor VPN op de tegel **Beveiliging**.

    ![Pagina VPN van lokale webinterface](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-1.png)  

    Als u VPN wilt configureren, moet u er eerst voor zorgen dat u alle noodzakelijke configuratie hebt uitgevoerd in Azure. Zie [Vereisten configureren](azure-stack-edge-placeholder.md) en [Azure-resources voor VPN configureren](azure-stack-edge-placeholder.md) voor meer informatie. Zodra dit is voltooid, kunt u de configuratie in de lokale gebruikersinterface uitvoeren.
    
    1. Selecteer **Configureren** op de VPN-pagina.
    2. Doe het volgende op de blade **VPN configureren**:

    - Schakel **VPN-instellingen** in.
    - Geef het **gedeelde VPN-geheim** op. Dit is de gedeelde sleutel die u hebt opgegeven tijdens het maken van het Azure VPN-verbindingsobject.
    - Geef het **IP-adres van de VPN-gateway** op. Dit is het IP-adres van de lokale netwerkgateway van Azure.
    - Selecteer **Geen** bij **PFS-groep**. 
    - Selecteer **Groep2** bij **DH-groep**.
    - Selecteer **IPsec-integriteitsmethode** bij **SHA256**.
    - Selecteer **Transformatieconstanten voor IPseccipher** bij **GCMAES256**.
    - Selecteer **Transformatieconstanten voor IPseccipher-verificatie** bij **GCMAES256**.
    - Selecteer **IKE-versleutelingsmethode** bij **AES256**.
    - Selecteer **Toepassen**.

        ![Lokale gebruikersinterface 2 configureren](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-2.png)

    3. Als u het configuratiebestand voor de VPN-route wilt uploaden, selecteert u **Uploaden**. 
    
        ![Lokale gebruikersinterface 3 configureren](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-3.png)
    
        - Open het *JSON*-bestand voor VPN-configuratie dat u in de vorige stap hebt gedownload naar uw lokale systeem.
        - Selecteer de regio als de Azure-regio die gekoppeld is aan het apparaat, het virtuele netwerk en de gateways.
        - Selecteer **Toepassen**.
    
            ![Lokale gebruikersinterface 4 configureren](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-4.png)
    
    4. U voegt client-specifieke routes toe door IP-adresbereiken te configureren die alleen met VPN mogen worden geopend. 
    
        - Selecteer onder **IP-adresbereiken die alleen met VPN mogen worden geopend** de optie **Configureren**.
        - Geef een geldig IPv4-bereik op en selecteer **Toevoegen**. Herhaal de stappen om andere bereiken toe te voegen.
        - Selecteer **Toepassen**.
    
            ![Lokale gebruikersinterface 5 configureren](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-5.png)

1. Selecteer **< Terug om aan de slag te gaan**.

## <a name="configure-encryption-at-rest"></a>Configureer versleuteling 'at rest'

1. Selecteer **Configureren** voor versleuteling 'at rest' op de tegel **Beveiliging**. Dit is een vereiste instelling en totdat deze is geconfigureerd, kunt u het apparaat niet activeren. 

    In de fabriek wordt BitLocker-versleuteling op volumeniveau ingeschakeld zodra er een installatiekopie op de apparaten is geplaatst. Nadat u het apparaat hebt ontvangen, moet u de versleuteling 'at rest' configureren. De opslaggroep en -volumes worden opnieuw gemaakt en u kunt BitLocker-sleutels opgeven om versleuteling in te schakelen en daarmee een tweede laag met versleuteling maken voor uw data-at-rest.

1. Voer in het deelvenster **Versleuteling 'at rest'** een met Base-64 gecodeerde sleutel van 32 tekens in. Dit is een eenmalige configuratie en deze sleutel wordt gebruikt om de daadwerkelijke versleutelingssleutel te beveiligen. U kunt er voor kiezen deze sleutel automatisch te genereren of er een in te voeren.

    ![Deelvenster "Versleuteling 'at rest'" van lokale webinterface](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/encryption-key-1.png)

    De sleutel wordt opgeslagen in een sleutelbestand op de pagina **Cloudgegevens** nadat het apparaat is geactiveerd.

1. Selecteer **Toepassen**. Deze bewerking duurt enkele minuten en de status van de bewerking wordt weergegeven op de tegel **Beveiliging**.

    ![Pagina "Versleuteling 'at rest'" van lokale webinterface](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-status-1.png)

1. Nadat de status is weergegeven als **Voltooid**, selecteert u **< Terug naar Aan de slag**.

Het apparaat is nu gereed om te worden geactiveerd. 


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie komen deze onderwerpen aan bod:

> [!div class="checklist"]
>
> * Vereisten
> * Certificaten voor het fysieke apparaat configureren
> * VPN configureren
> * Configureer versleuteling 'at rest'

Zie voor meer informatie over het activeren van uw Azure Stack Edge Pro R-apparaat:

> [!div class="nextstepaction"]
> [Azure Stack Edge Pro R-apparaat activeren](./azure-stack-edge-pro-r-deploy-activate.md)
