---
title: Zelfstudie voor het verbinding maken met, configureren en activeren van een Azure Stack Edge Pro-apparaat met GPU in de Azure-portal | Microsoft Docs
description: In de zelfstudie voor het implementeren van Azure Stack Edge Pro GPU krijgt u instructie om uw fysieke apparaat te verbinden, in te stellen en te activeren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: ceaa16ee2a54886cde45f37ea90ed617abafffc1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100546904"
---
# <a name="tutorial-configure-the-device-settings-for-azure-stack-edge-pro-with-gpu"></a>Zelfstudie: apparaatinstellingen configureren voor Azure Stack Edge Pro met GPU

In deze zelfstudie wordt beschreven hoe u apparaatinstellingen voor uw Azure Stack Edge Pro-apparaat met een onboard GPU kunt configureren. U kunt de apparaatnaam, update-server en tijdserver instellen via de lokale webinterface.

Het kan 5-7 minuten duren voordat de apparaatinstellingen zijn voltooid.

In deze zelfstudie komen deze onderwerpen aan bod:

> [!div class="checklist"]
>
> * Vereisten
> * Apparaatinstellingen configureren
> * Update configureren 
> * Tijd configureren

## <a name="prerequisites"></a>Vereisten

Voordat u de apparaatinstellingen op uw Azure Stack Edge Pro-apparaat met GPU gaat configureren, dient u het volgende te controleren:

* Voor uw fysieke apparaat:

    - U hebt het fysieke apparaat geïnstalleerd zoals beschreven in [Azure Stack Edge Pro installeren](azure-stack-edge-gpu-deploy-install.md).
    - U hebt het netwerk geconfigureerd en het rekennetwerk op uw apparaat geconfigureerd, zoals wordt beschreven in [Zelfstudie: Netwerk configureren voor Azure Stack Edge Pro met GPU](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).


## <a name="configure-device-settings"></a>Apparaatinstellingen configureren

Volg deze stappen voor het configureren van apparaatinstellingen:

1. Voer op de pagina **Apparaat** de volgende stappen uit:

    1. Voer een beschrijvende naam voor het apparaat in. De beschrijvende naam moet tussen 1 en 13 tekens lang zijn en mag alleen letters, cijfers en afbreekstreepjes bevatten.

    2. Geef een **DNS-domein** voor uw apparaat op. Dit domein wordt gebruikt om het apparaat in te stellen als een bestandsserver.

    3. Selecteer **Toepassen** om de geconfigureerde apparaatinstellingen te valideren en toe te passen.

        ![Pagina Apparaat van de lokale webinterface 1](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-2.png)

        Als u de naam van het apparaat en het DNS-domein hebt gewijzigd, werken de automatisch gegenereerde zelfondertekende certificaten op het apparaat niet. Kies een van de volgende opties wanneer u certificaten configureert: 
        
        - Apparaatcertificaten genereren en downloaden. 
        - Uw eigen certificaten voor het apparaat gebruiken, inclusief de ondertekeningsketen.
    

        ![Pagina Apparaat van de lokale webinterface 2](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-3.png)

    4. Wanneer de apparaatnaam en het DNS-domein worden gewijzigd, wordt het SMB-eindpunt gemaakt.  

    5. Nadat de instellingen zijn toegepast, selecteert u **Volgende: Server bijwerken**.

        ![Pagina 'Apparaat' van de lokale webinterface 3](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-4.png)

## <a name="configure-update"></a>Update configureren

1. U kunt nu op de pagina **Bijwerken** de locatie configureren waar u de updates voor uw apparaat kunt downloaden.  

    - U kunt de updates rechtstreeks van de **Microsoft Update-server** ophalen.

        ![Pagina Update-server van lokale webinterface](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/update-2.png)

        U kunt er ook voor kiezen om updates te implementeren via **Windows Server Update Services** (WSUS). Geef het pad naar de WSUS-server op.
        
        ![Pagina Update-server van lokale webinterface 2](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/update-3.png)

        > [!NOTE] 
        > Als er een afzonderlijke Windows Update-server is geconfigureerd en u ervoor kiest om verbinding te maken via *https* (in plaats van *http*), zijn er certificaten voor de ondertekeningsketen nodig, die vereist zijn om verbinding te maken met de update-server. Ga naar [Certificaten beheren](azure-stack-edge-gpu-manage-certificates.md) voor meer informatie over het maken en uploaden van certificaten. 

2. Selecteer **Toepassen**.
3. Nadat de update-server is geconfigureerd, selecteert u **Volgende: Tijd**.
    

## <a name="configure-time"></a>Tijd configureren

Volg deze stappen voor het configureren van tijdinstellingen op uw apparaat. 

> [!IMPORTANT]
> Hoewel de tijdinstellingen optioneel zijn, raden we u ten zeerste aan dat u een primaire NTP-server en een secundaire NTP-server op het lokale netwerk voor uw apparaat configureert. Als de lokale server niet beschikbaar is, kunnen openbare NTP-servers worden geconfigureerd.

NTP-servers zijn vereist, omdat uw apparaat de tijd moet synchroniseren voor verificatie met uw cloudserviceproviders.

1. Op de pagina **Tijd** kunt u de tijdzone en de primaire en secundaire NTP-servers voor uw apparaat selecteren.  
    
    1. Selecteer in de vervolgkeuzelijst **Tijdzone** de tijdzone die overeenkomt met de geografische locatie waar het apparaat wordt geïmplementeerd.
        De standaard tijdzone voor uw apparaat is PST. Het apparaat zal deze tijdzone gebruiken voor alle geplande bewerkingen.

    2. Voer in het vak **Primaire NTP-server** de primaire server voor uw apparaat in of accepteer de standaardwaarde van time.windows.com.  
        Zorg ervoor dat in uw netwerk NTP-verkeer kan worden doorgegeven van uw datacenter naar internet.

    3. Voer desgewenst in het vak **secundaire NTP-server** een secundaire server in voor uw apparaat.

    4. Selecteer **Toepassen** om de geconfigureerde tijdinstellingen te valideren en toe te passen.

        ![Pagina Tijd van lokale webinterface](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/time-2.png)

2. Nadat de instellingen zijn toegepast, selecteert u **Volgende: Certificaten**.


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie komen deze onderwerpen aan bod:

> [!div class="checklist"]
>
> * Vereisten
> * Apparaatinstellingen configureren
> * Update configureren 
> * Tijd configureren

Als u wilt weten hoe u certificaten voor uw Azure Stack Edge Pro kunt configureren, raadpleegt u:

> [!div class="nextstepaction"]
> [Certificaten configureren](./azure-stack-edge-gpu-deploy-configure-certificates.md)
