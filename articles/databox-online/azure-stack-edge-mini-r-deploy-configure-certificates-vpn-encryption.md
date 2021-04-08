---
title: Zelfstudie voor configureren van certificaten voor een Azure Stack Edge Mini R-apparaat in de Azure-portal | Microsoft Docs
description: In de zelfstudie voor het implementeren van Azure Stack Edge Mini R krijgt u de instructie om certificaten op uw fysieke apparaat te configureren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure certificates for Azure Stack Edge Mini R  so I can use it to transfer data to Azure.
ms.openlocfilehash: c3a09242b895234c96c64d9e23449d980e47e387
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100546734"
---
# <a name="tutorial-configure-certificates-vpn-encryption-for-your-azure-stack-edge-mini-r"></a>Zelfstudie: Certificaten, VPN en versleuteling voor uw Azure Stack Edge Mini R configureren

In deze zelfstudie wordt beschreven hoe u certificaten, VPN en versleuteling 'at rest' voor uw Azure Stack Edge Mini R-apparaat kunt configureren door de lokale webinterface te gebruiken.

De tijd die nodig is voor deze stap kan variëren afhankelijk van de specifieke optie die u kiest en hoe de certificaatstroom is opgezet in uw omgeving.

In deze zelfstudie komen deze onderwerpen aan bod:

> [!div class="checklist"]
>
> * Vereisten
> * Certificaten voor het fysieke apparaat configureren
> * VPN configureren
> * Versleuteling 'at rest' configureren

## <a name="prerequisites"></a>Vereisten

Zorg dat aan deze voorwaarden wordt voldaan voordat u uw Azure Stack Edge Mini R-apparaat configureert en instelt:

* U hebt het fysieke apparaat geïnstalleerd zoals beschreven in [Azure Stack Edge Mini R installeren](azure-stack-edge-mini-r-deploy-install.md).

* Als u van plan bent uw eigen certificaten te gebruiken:
    - Zorg ervoor dat uw certificaten bij de hand hebt in de juiste indeling, inclusief het certificaat van de ondertekeningsketen. Ga voor meer informatie over het certificaat naar [Certificaten beheren](azure-stack-edge-gpu-manage-certificates.md)

    - Als uw apparaat wordt geïmplementeerd in Azure Government of Azure Government Secret of een uiterst geheime Azure Government-cloud en niet wordt geïmplementeerd in een openbare Azure-cloud, is een certificaat van de ondertekeningsketen vereist voordat u uw apparaat kunt activeren. 
    Ga voor meer informatie over het certificaat naar [Certificaten beheren](azure-stack-edge-gpu-manage-certificates.md).


## <a name="configure-certificates-for-device"></a>Certificaten configureren voor apparaat

1. Op de pagina **Certificaten** gaat u uw certificaten configureren. Afhankelijk van of u de apparaatnaam of het DNS-domein hebt gewijzigd in de tegel **Apparaatinstallatie**, kunt u een van de volgende opties voor uw certificaten kiezen.

    - Als u de standaardapparaatnaam of het DNS-standaarddomein in de vorige stap niet hebt gewijzigd en u niet uw eigen certificaten wilt gebruiken, kunt u deze stap overslaan en met de volgende stap verdergaan. Om te beginnen heeft het apparaat automatisch zelfondertekende certificaten gegenereerd. 

       <!-- ![Local web UI "Certificates" page](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/certificate-1.png)-->

    - Als u de apparaatnaam of het DNS-domein hebt gewijzigd, ziet u dat de status van certificaten wordt weergegeven als **Niet geldig**. 

        ![Pagina 2 Certificaten van lokale webinterface](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/certificate-2.png)    

        Selecteer een certificaat om de details van de status weer te geven.

        <!--![Local web UI "Certificates" page 3](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1a.png)-->  

        De certificaatstatus is **Niet geldig** omdat de certificaten niet overeenkomen met de bijgewerkte apparaatnaam en het DNS-domein (die worden gebruikt bij onderwerpnaam en alternatief onderwerp). Om uw apparaat te activeren, kunt u uw eigen ondertekende eindpuntcertificaten en de bijbehorende ondertekeningsketens gebruiken. U voegt eerst de ondertekeningsketens toe en uploadt vervolgens de eindpuntcertificaten. Ga voor meer informatie naar [Uw eigen certificaten op uw Azure Stack Edge Mini R-apparaat gebruiken](#bring-your-own-certificates).


    - Als u de apparaatnaam of het DNS-domein hebt gewijzigd en u geen eigen certificaten gebruikt, wordt de **activering geblokkeerd**.


#### <a name="bring-your-own-certificates"></a>Uw eigen certificaten gebruiken

U hebt de ondertekeningsketen al in een eerdere stap toegevoegd op dit apparaat. U kunt nu de eindpuntcertificaten, het knooppuntcertificaat, het lokale gebruikersinterfacecertificaat en het VPN-certificaat uploaden. Volg deze stappen om uw eigen certificaten toe te voegen.

1. Als u een certificaat wilt uploaden, selecteert u op de pagina **Certificaat** de optie **+ Certificaat toevoegen**.

    [![Pagina 4 Certificaten van lokale webinterface](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-1.png#lightbox)


1. U kunt andere certificaten uploaden. U kunt bijvoorbeeld de Azure Resource Manager- en Blob Storage-eindpuntcertificaten uploaden.

    ![Pagina 6 Certificaten van lokale webinterface](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-3.png)

1. U kunt ook het lokale webinterfacecertificaat uploaden. Nadat u dit certificaat hebt geüpload, wordt u gevraagd om uw browser te starten en de cache te wissen. Vervolgens moet u verbinding maken met de lokale webinterface van het apparaat.  

    ![Pagina 7 Certificaten van lokale webinterface](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-4.png)

1. U kunt ook het knooppuntcertificaat uploaden.


    ![Pagina 8 Certificaten van lokale webinterface](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-5.png)

1. Tot slot kunt u het VPN-certificaat uploaden.
        
    ![Pagina Certificaten van lokale webinterface](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-6.png)

1. U kunt op elk gewenst moment een certificaat selecteren en de details weergeven om ervoor te zorgen dat deze overeenkomen met het certificaat dat u hebt geüpload.

    [![Pagina 9 Certificaten van lokale webinterface](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-7.png)](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-7.png#lightbox)

    De certificaatpagina zou moeten worden bijgewerkt en de zojuist toegevoegde certificaten weergeven.

    [![Pagina 10 Certificaten van lokale webinterface](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-8.png)](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-8.png#lightbox)  

    > [!NOTE]
    > Met uitzondering van de openbare Azure-cloud moeten ondertekeningsketencertificaten worden ingesteld voordat alle cloudconfiguraties worden geactiveerd (Azure Government of Azure Stack Hub).


## <a name="configure-vpn"></a>VPN configureren

1. Selecteer **Configureren** voor VPN op de tegel **Beveiliging**. 

    Als u VPN wilt configureren, moet u er eerst voor zorgen dat u alle noodzakelijke configuratie hebt uitgevoerd in Azure. Zie [VPN configureren via PowerShell voor uw Azure Stack Edge Mini R-apparaat](azure-stack-edge-placeholder.md) voor meer informatie. Zodra dit is voltooid, kunt u de configuratie in de lokale gebruikersinterface uitvoeren.
    
    1. Selecteer **Configureren** op de VPN-pagina.
        ![Lokale gebruikersinterface VPN configureren 1](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/configure-vpn-1.png)

    1. Doe het volgende op de blade **VPN configureren**:

        1. Geef de telefoonlijst op als invoer.
        2. Geef het JSON-bestand van het IP-bereik van Azure Data Center op als invoer. Download dit bestand van: [https://www.microsoft.com/download/details.aspx?id=56519](https://www.microsoft.com/download/details.aspx?id=56519).
        3. Selecteer **eastus** als regio.
        4. Selecteer **Toepassen**.

        ![Lokale gebruikersinterface VPN configureren 2](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/configure-vpn-2.png)
    
    1. IP-adresbereiken configureren die alleen met VPN mogen worden geopend. 
    
        - Selecteer onder **IP-adresbereiken die alleen met VPN mogen worden geopend** de optie **Configureren**.
        - Voer het VNET IPv4-bereik in dat u voor uw Azure Virtual Network hebt gekozen.
        - Selecteer **Toepassen**.
    
        ![Lokale gebruikersinterface VPN configureren 3](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/configure-vpn-3.png)

Het apparaat is nu gereed om te worden versleuteld. Configureer versleuteling 'at rest'.


## <a name="enable-encryption"></a>Versleuteling inschakelen

1. Selecteer **Configureren** voor versleuteling 'at rest' op de tegel **Beveiliging**. Dit is een vereiste instelling en totdat deze is geconfigureerd, kunt u het apparaat niet activeren. 

    ![Lokale webgebruikersinterface "versleuteling 'at rest'" pagina 1](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-1.png)

    In de fabriek wordt BitLocker-versleuteling op volumeniveau ingeschakeld zodra er een installatiekopie op de apparaten is geplaatst. Nadat u het apparaat hebt ontvangen, moet u de versleuteling 'at rest' configureren. De opslaggroep en -volumes worden opnieuw gemaakt en u kunt BitLocker-sleutels opgeven om versleuteling in te schakelen en daarmee een tweede laag met versleuteling maken voor uw data-at-rest.

1. Voer in het deelvenster **Versleuteling 'at rest'** een met Base-64 gecodeerde sleutel van 32 tekens (AES-256-bits) in. Dit is een eenmalige configuratie en deze sleutel wordt gebruikt om de daadwerkelijke versleutelingssleutel te beveiligen. 

    ![Lokale webgebruikersinterface "versleuteling 'at rest'" pagina 2](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-2.png)

    U kunt er ook voor kiezen deze sleutel automatisch te genereren.

    ![Lokale webgebruikersinterface "versleuteling 'at rest'" pagina 3](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-3.png)

1. Selecteer **Toepassen**. Deze bewerking duurt enkele minuten en de status van de bewerking wordt weergegeven op de tegel **Beveiliging**.

    ![Lokale webgebruikersinterface "versleuteling 'at rest'" pagina 4](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-3.png)

1. Nadat de status is weergegeven als **Voltooid**, gaat u terug naar **Aan de slag**.

Het apparaat is nu gereed om te worden geactiveerd.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie komen deze onderwerpen aan bod:

> [!div class="checklist"]
>
> * Vereisten
> * Certificaten voor het fysieke apparaat configureren
> * VPN configureren
> * Versleuteling 'at rest' configureren

Zie voor meer informatie over het activeren van uw Azure Stack Edge Mini R-apparaat:

> [!div class="nextstepaction"]
> [Azure Stack Edge Mini R-apparaat activeren](./azure-stack-edge-mini-r-deploy-activate.md)
