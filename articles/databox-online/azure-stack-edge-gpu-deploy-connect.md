---
title: Zelfstudie voor het verbinding maken met, configureren en activeren van Azure Stack Edge Pro-apparaat met GPU in de Azure-portal | Microsoft Docs
description: Leer hoe u verbinding met uw Azure Stack Edge-apparaat kunt maken met een onboard GPU door de lokale webgebruikersinterface te gebruiken.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/29/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: f069f711ccf5672041ea6f3b15a52fa2b3ba2022
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90900033"
---
# <a name="tutorial-connect-to-azure-stack-edge-pro-with-gpu"></a>Zelfstudie: Verbinding maken met Azure Stack Edge Pro met GPU

In deze zelfstudie wordt beschreven hoe u verbinding met uw Azure Stack Edge Pro-apparaat kunt maken met een onboard GPU door de lokale webgebruikersinterface te gebruiken.

Het verbindingsproces kan ongeveer vijf minuten duren.

In deze zelfstudie komen deze onderwerpen aan bod:

> [!div class="checklist"]
>
> * Vereisten
> * Verbinding maken met een fysiek apparaat


## <a name="prerequisites"></a>Vereisten

Zorg dat aan deze voorwaarden wordt voldaan voordat u uw Azure Stack Edge Pro-apparaat met GPU configureert en instelt:

* U hebt het fysieke apparaat geïnstalleerd zoals beschreven in [Azure Stack Edge Pro installeren](azure-stack-edge-gpu-deploy-install.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Verbinding maken met de lokale gebruikersinterface instellen

1. Configureer de Ethernet-adapter op de computer die u wilt verbinden met het Azure Stack Edge Pro-apparaat met het vaste IP-adres 192.168.100.5 en het subnet 255.255.255.0.

2. Verbind de computer met poort 1 op het apparaat. Als u de computer rechtstreeks op het apparaat wilt aansluiten (zonder een switch), gebruikt u een crossover-kabel of een USB-ethernetadapter. Gebruik de volgende afbeelding om poort 1 op uw apparaat te vinden.

    ![Achterpaneel van een bekabeld apparaat](./media/azure-stack-edge-gpu-deploy-install/ase-two-pci-slots.png)

    Het achterpaneel van het apparaat kan er iets anders uitzien, afhankelijk van welk model u hebt ontvangen. Zie [Uw apparaat bekabelen](azure-stack-edge-gpu-deploy-install.md#cable-the-device) voor meer informatie.


3. Open een browservenster en krijg toegang tot de lokale gebruikersinterface van het apparaat op `https://192.168.100.10`.  
    Deze actie kan enkele minuten duren nadat u het apparaat hebt ingeschakeld.

    U ziet nu een foutmelding of waarschuwing waarin wordt aangegeven dat er een probleem is met het beveiligingscertificaat van de website. 
   
    ![Foutbericht van beveiligingscertificaat voor website](./media/azure-stack-edge-deploy-connect-setup-activate/image2.png)

4. Selecteer **Doorgaan naar deze webpagina**.  
    Deze stappen kunnen variëren, afhankelijk van de browser die u gebruikt.

5. Meld u aan bij de webgebruikersinterface van uw apparaat. Het standaardapparaatwachtwoord is *Wachtwoord1*. 
   
    ![Aanmeldingspagina van het Azure Stack Edge Pro-apparaat](./media/azure-stack-edge-deploy-connect-setup-activate/image3.png)

6. Wijzig het beheerderswachtwoord voor het apparaat als u daarom wordt gevraagd.  
    Het nieuwe wachtwoord moet tussen 8 en 16 tekens bevatten. Het wachtwoord moet drie van de volgende tekens bevatten: kleine letters, hoofdletters, cijfers en speciale tekens.

U bent nu op de pagina **Overzicht** van uw apparaat. De volgende stap is het configureren van de netwerkinstellingen voor het apparaat.


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Vereisten
> * Verbinding maken met een fysiek apparaat


Als u wilt weten hoe u netwerkinstellingen op uw Azure Stack Edge Pro-apparaat configureert, raadpleegt u:

> [!div class="nextstepaction"]
> [Netwerk configureren](./azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)
