---
title: Zelfstudie voor configureren van certificaten voor een Azure Stack Edge Pro-apparaat met GPU in Azure Portal | Microsoft Docs
description: In de zelfstudie voor het implementeren van Azure Stack Edge Pro met GPU krijgt u de instructie om certificaten op uw fysieke apparaat te configureren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure certificates for Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: d8d21f9eea0258175195529c4d8b72ee9085dc77
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100546972"
---
# <a name="tutorial-configure-certificates-for-your-azure-stack-edge-pro-with-gpu"></a>Zelfstudie: Certificaten voor uw Azure Stack Edge Pro met GPU configureren

In deze zelfstudie wordt beschreven hoe u certificaten voor uw Azure Stack Edge Pro-apparaat met een onboard GPU kunt configureren door de lokale webinterface te gebruiken.

De tijd die nodig is voor deze stap kan variëren afhankelijk van de specifieke optie die u kiest en hoe de certificaatstroom is opgezet in uw omgeving.

In deze zelfstudie komen deze onderwerpen aan bod:

> [!div class="checklist"]
>
> * Vereisten
> * Certificaten voor het fysieke apparaat configureren

## <a name="prerequisites"></a>Vereisten

Zorg dat aan deze voorwaarden is voldaan voordat u uw Azure Stack Edge Pro-apparaat met GPU configureert en instelt:

* U hebt het fysieke apparaat geïnstalleerd zoals beschreven in [Azure Stack Edge Pro installeren](azure-stack-edge-gpu-deploy-install.md).
* Als u van plan bent uw eigen certificaten te gebruiken:
    - Zorg ervoor dat uw certificaten bij de hand hebt in de juiste indeling, inclusief het certificaat van de ondertekeningsketen. Ga voor meer informatie over het certificaat naar [Certificaten beheren](azure-stack-edge-gpu-manage-certificates.md)

<!--    - If your device is deployed in Azure Government or Azure Government Secret or Azure Government top secret cloud and not deployed in Azure public cloud, a signing chain certificate is required before you can activate your device. 
    For details on certificate, go to [Manage certificates](azure-stack-edge-gpu-manage-certificates.md).-->


## <a name="configure-certificates-for-device"></a>Certificaten configureren voor apparaat

1. Op de pagina **Certificaten** gaat u uw certificaten configureren. Afhankelijk van of u de apparaatnaam of het DNS-domein hebt gewijzigd in de tegel **Apparaatinstallatie**, kunt u een van de volgende opties voor uw certificaten kiezen.

    - Als u de apparaatnaam of het DNS-domein in de vorige stap niet hebt gewijzigd en u niet uw eigen certificaten wilt gebruiken, kunt u deze stap overslaan en met de volgende stap verdergaan. Om te beginnen heeft het apparaat automatisch zelfondertekende certificaten gegenereerd. 

        ![Pagina Certificaten van lokale webinterface](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-2.png)

    - Als u de apparaatnaam of het DNS-domein hebt gewijzigd, ziet u dat de status van certificaten wordt weergegeven als **Niet geldig**. 

        ![Pagina 2 Certificaten van lokale webinterface](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1.png)    

        Selecteer een certificaat om de details van de status weer te geven.

        ![Pagina 3 Certificaten van lokale webinterface](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1a.png)  

        Dit komt doordat de certificaten niet overeenkomen met de bijgewerkte apparaatnaam en het DNS-domein (die worden gebruikt bij onderwerpnaam en alternatief onderwerp). Kies een van de volgende opties om uw apparaat te activeren: 
    
        - **Alle apparaatcertificaten genereren**. Deze apparaten mogen alleen worden gebruikt voor testen en niet worden gebruikt met productie-workloads. Ga voor meer informatie naar [Uw eigen certificaten op uw Azure Stack Edge Pro-apparaat genereren](#generate-device-certificates).

        - **Uw eigen certificaten gebruiken**. U kunt uw eigen ondertekende eindpuntcertificaten en de bijbehorende ondertekeningsketens gebruiken. U voegt eerst de ondertekeningsketens toe en uploadt vervolgens de eindpuntcertificaten. **U wordt aangeraden altijd uw eigen certificaten te maken voor productieworkloads.** Ga voor meer informatie naar [Uw eigen certificaten op uw Azure Stack Edge Pro-apparaat gebruiken](#bring-your-own-certificates).
    
        - U kunt een aantal van uw eigen certificaten gebruiken en een aantal apparaatcertificaten genereren. Met de optie **Certificaten genereren** worden alleen de apparaatcertificaten opnieuw gegenereerd.

    - Als u de apparaatnaam of het DNS-domein hebt gewijzigd en u geen certificaten genereert of als u uw eigen certificaten gebruikt, wordt de **activering geblokkeerd**.


### <a name="generate-device-certificates"></a>Apparaatcertificaten genereren

Volg deze stappen om apparaatcertificaten te genereren.

Gebruik de volgende stappen om de Azure Stack Edge Pro-apparaatcertificaten opnieuw te genereren en te downloaden:

1. Ga in de lokale gebruikersinterface van uw apparaat naar **Configuratie > Certificaten**. Selecteer **Certificaten genereren**.

    ![Genereer en download certificaat 1](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-3.png)

2. Selecteer **Genereren** bij **Apparaatcertificaten genereren**. 

    ![Genereer en download certificaat 2](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-4.png)

    De apparaatcertificaten worden nu gegenereerd en toegepast. Het duurt enkele minuten om de certificaten te genereren en toe te passen.
    
    > [!IMPORTANT]
    > Terwijl de bewerking voor het genereren van het certificaat wordt uitgevoerd, moet u uw eigen certificaten niet gebruiken, en probeer deze toe te voegen met de optie **+ Certificaat toevoegen**.

    U krijgt een melding wanneer de bewerking is voltooid. **Start de browser opnieuw om mogelijke problemen met de cache te voorkomen.**
    
    ![Genereer en download certificaat 4](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-5.png)

3. Nadat de certificaten zijn gegenereerd: 

    - De status van alle certificaten wordt weergegeven als **Geldig**. 

        ![Genereer en download certificaat 5](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6.png)

    - U kunt een specifieke certificaatnaam selecteren en de certificaatgegevens weergeven. 

        ![Genereer en download certificaat 6](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6a.png)

    - De kolom **Downloaden** is nu ingevuld. Deze kolom bevat koppelingen voor het downloaden van de opnieuw gegenereerde certificaten. 

        ![Genereer en download certificaat 7](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6b.png)


4. Selecteer de downloadkoppeling voor een certificaat en sla het certificaat op wanneer dit wordt gevraagd. 

    ![Genereer en download certificaat 8](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-7.png)

5. Herhaal dit proces voor alle certificaten die u wilt downloaden. 
    
    ![Genereer en download certificaat 9](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-8.png)

    De door het apparaat gegenereerde certificaten worden opgeslagen als DER-certificaten met de volgende naamindeling: 

    `<Device name>_<Endpoint name>.cer`. Deze certificaten bevatten de openbare sleutel voor de bijbehorende certificaten op het apparaat. 

U moet deze certificaten installeren op het clientsysteem dat u gebruikt om toegang tot de eindpunten op het ASE-apparaat te krijgen. Met deze certificaten wordt een vertrouwensrelatie tussen de client en het apparaat tot stand gebracht.

Als u deze certificaten wilt importeren en installeren op de client die u gebruikt om toegang tot het apparaat te krijgen, volgt u de stappen in [Certificaten importeren op de clients met toegang tot uw Azure Stack Edge Pro-apparaat](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device). 

Als Azure Storage Explorer wordt gebruikt, moet u certificaten op uw client in de PEM-indeling installeren en moet u de door het apparaat gegenereerde certificaten converteren naar de PEM-indeling. 

> [!IMPORTANT]
> - De downloadkoppeling is alleen beschikbaar voor de door het apparaat gegenereerde certificaten en niet als u uw eigen certificaten gebruikt.
> - U kunt ervoor kiezen om een combinatie van door apparaten gegenereerde certificaten te hebben en uw eigen certificaten te gebruiken, zolang er maar aan andere certificaatvereisten wordt voldaan. Ga naar [Certificaatvereisten](azure-stack-edge-gpu-certificate-requirements.md) voor meer informatie.
    

### <a name="bring-your-own-certificates"></a>Uw eigen certificaten gebruiken

Volg deze stappen om uw eigen certificaten met inbegrip van de ondertekeningsketen toe te voegen.

1. Als u een certificaat wilt uploaden, selecteert u op de pagina **Certificaat** de optie **+ Certificaat toevoegen**.

    ![Pagina 4 Certificaten van lokale webinterface](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-1.png)

2. Upload eerst de ondertekeningsketen en selecteer **Valideren en toevoegen**.

    ![Pagina 5 Certificaten van lokale webinterface](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-2.png)

3. U kunt nu andere certificaten uploaden. U kunt bijvoorbeeld de Azure Resource Manager- en Blob Storage-eindpuntcertificaten uploaden.

    ![Pagina 6 Certificaten van lokale webinterface](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-3.png)

    U kunt ook het lokale webinterfacecertificaat uploaden. Nadat u dit certificaat hebt geüpload, wordt u gevraagd om uw browser te starten en de cache te wissen. Vervolgens moet u verbinding maken met de lokale webinterface van het apparaat.  

    ![Pagina 7 Certificaten van lokale webinterface](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-5.png)

    U kunt ook het knooppuntcertificaat uploaden.

    ![Pagina 8 Certificaten van lokale webinterface](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-4.png)

    U kunt op elk gewenst moment een certificaat selecteren en de details weergeven om ervoor te zorgen dat deze overeenkomen met het certificaat dat u hebt geüpload.

    ![Pagina 9 Certificaten van lokale webinterface](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-6.png)

    De certificaatpagina zou moeten worden bijgewerkt en de zojuist toegevoegde certificaten weergeven.

    ![Pagina 10 Certificaten van lokale webinterface](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-7.png)  

    > [!NOTE]
    > Met uitzondering van de openbare Azure-cloud moeten ondertekeningsketencertificaten worden ingesteld voordat alle cloudconfiguraties worden geactiveerd (Azure Government of Azure Stack).


Het apparaat is nu gereed om te worden geactiveerd. Selecteer **< Terug om aan de slag te gaan**.


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie komen deze onderwerpen aan bod:

> [!div class="checklist"]
>
> * Vereisten
> * Certificaten voor het fysieke apparaat configureren

Zie voor meer informatie over het activeren van uw Azure Stack Edge Pro-apparaat:

> [!div class="nextstepaction"]
> [Azure Stack Edge Pro-apparaat activeren](./azure-stack-edge-gpu-deploy-activate.md)
