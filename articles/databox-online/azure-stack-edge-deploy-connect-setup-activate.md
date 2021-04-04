---
title: Zelfstudie voor verbinding maken met, configureren en activeren van Azure Stack Edge Pro-apparaat in Azure Portal | Microsoft Docs
description: In de zelfstudie voor het implementeren van Azure Stack Edge Pro krijgt u instructie om uw fysieke apparaat te verbinden, in te stellen en te activeren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: 8a143dadffb3f89ef67dc20a2038bb3c9bf5a0e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91743332"
---
# <a name="tutorial-connect-set-up-and-activate-azure-stack-edge-pro"></a>Zelfstudie: Azure Stack Edge Pro verbinden, instellen en activeren 

In deze zelfstudie wordt beschreven hoe u met behulp van de lokale webinterface verbinding kunt maken met uw Azure Stack Edge Pro-apparaat, hoe u het apparaat instelt en activeert.

Het installatie- en activeringsproces kan ongeveer 20 minuten duren.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Verbinding maken met een fysiek apparaat
> * Het fysieke apparaat instellen en activeren

## <a name="prerequisites"></a>Vereisten

Zorg dat aan deze voorwaarden wordt voldaan voordat u uw Azure Stack Edge Pro-apparaat configureert en instelt:

* U hebt het fysieke apparaat geïnstalleerd zoals beschreven in [Azure Stack Edge Pro installeren](azure-stack-edge-deploy-install.md).
* U hebt de activeringssleutel van de Azure Stack Edge-service die u hebt gemaakt om het Azure Stack Edge Pro-apparaat te beheren. Ga voor meer informatie naar [Voorbereiding voor implementatie van Azure Stack Edge Pro](azure-stack-edge-deploy-prep.md).

## <a name="connect-to-the-local-web-ui-setup"></a>Verbinding maken met de lokale gebruikersinterface instellen

1. Configureer de Ethernet-adapter op de computer die u wilt verbinden met het Azure Stack Edge Pro-apparaat met het vaste IP-adres 192.168.100.5 en het subnet 255.255.255.0.

2. Verbind de computer met poort 1 op het apparaat. Gebruik de volgende afbeelding om poort 1 op uw apparaat te vinden.

    ![Achterpaneel van een bekabeld apparaat](./media/azure-stack-edge-deploy-install/backplane-cabled.png)

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

U bent nu op het dashboard van uw apparaat.

## <a name="set-up-and-activate-the-physical-device"></a>Het fysieke apparaat instellen en activeren
 
Uw dashboard geeft de verschillende instellingen weer die nodig zijn voor het configureren en registreren van het fysieke apparaat bij de Azure Stack Edge-service. De **Apparaatnaam**, **Netwerkinstellingen**, **Instellingen webproxy** en **Tijdinstellingen** zijn optioneel. De enige vereiste instellingen zijn **Cloudinstellingen**.
   
![Pagina 'Dashboard' van de lokale webgebruikersinterface](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-1.png)

1. Selecteer in het linkerdeelvenster **Apparaatnaam** en geef een beschrijvende naam op voor het apparaat.  
    De beschrijvende naam moet tussen 1 en 15 tekens lang zijn en mag alleen letters, cijfers en afbreekstreepjes bevatten.

    ![Pagina 'Apparaatnaam' van de lokale webgebruikersinterface](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-2.png)

2. (Optioneel) Selecteer in het linkerdeelvenster **Netwerkinstellingen** en configureer vervolgens de instellingen.  
    Er zijn zes netwerkinterfaces op het fysieke apparaat. Poort 1 en poort 2 zijn 1-Gbps-netwerkinterfaces. Poort 3, poort 4, poort 5 en poort 6 zijn allemaal netwerkinterfaces van 25 Gbps die ook kunnen dienen als netwerkinterfaces van 10 Gbps. Poort 1 wordt automatisch geconfigureerd als een beheerpoort en poort 2 tot poort 6 zijn allemaal gegevenspoorten. De pagina **Netwerkinstellingen** is zoals hieronder wordt weergegeven.
    
    ![Pagina 'Netwerkinstellingen' van lokale webgebruikersinterface](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-3.png)
   
    Houd bij het configureren van de netwerkinstellingen rekening met het volgende:

   - Als DHCP is ingeschakeld in uw omgeving, worden netwerkinterfaces automatisch geconfigureerd. Er wordt automatisch een IP-adres, subnet, gateway en DNS toegewezen.
   - Als DHCP niet is ingeschakeld, kunt u indien nodig statische IP-adressen toewijzen.
   - U kunt uw netwerkinterface configureren als IPv4.

     >[!NOTE] 
     > Het is raadzaam om het lokale IP-adres van de netwerkinterface niet te wijzigen van statisch naar DHCP, tenzij u een ander IP-adres hebt om verbinding te maken met het apparaat. Als u één netwerkinterface gebruikt en u overschakelt naar DHCP, is er geen manier om het DHCP-adres te bepalen. Als u wilt wijzigen naar een DHCP-adres, wacht u totdat het apparaat is geregistreerd bij de service en brengt u vervolgens de wijziging aan. U kunt vervolgens de IP-adressen van alle adapters in de **Apparaateigenschappen** weergeven in de Azure Portal voor uw service.

3. (Optioneel) Selecteer in het linkerdeelvenster de optie **Webproxy-instellingen** en configureer vervolgens uw webproxyserver. De configuratie van uw webproxy is weliswaar optioneel, maar als u een webproxy gebruikt, kan deze alleen op deze pagina worden geconfigureerd.
   
   ![Pagina 'Webproxy-instellingen' voor lokale webgebruikersinterface](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-4.png)
   
   Ga op de pagina **Webproxy-instellingen** als volgt te werkt:
   
   a. Voer in het vak **Webproxy-URL** de URL in deze indeling in: `http://host-IP address or FQDN:Port number`. HTTPS-URL's worden niet ondersteund.

   b. Selecteer onder **Verificatie** **Geen** of **NTLM**. Als u berekening inschakelt en IoT Edge-module op uw Azure Stack Edge Pro-apparaat gebruikt, raden we u aan om de verificatie van de webproxy in te stellen op **Geen**. **NTLM** wordt niet ondersteund.

   c. Als u verificatie gebruikt, voert u een gebruikersnaam en wachtwoord in.

   d. Selecteer **Instellingen toepassen** om de geconfigureerde webproxy-instellingen te valideren en toe te passen.

   > [!NOTE]
   > De bestanden voor het automatisch configureren van de proxy (PAC-bestanden) worden niet ondersteund. Een PAC-bestand definieert hoe webbrowsers en andere gebruikersagenten automatisch de juiste proxyserver (toegangsmethode) kunnen kiezen voor het ophalen van een bepaalde URL.
   > Proxy's die al het verkeer proberen te onderscheppen en lezen (en vervolgens alles opnieuw ondertekenen met hun eigen certificering) zijn niet compatibel, omdat het certificaat van de proxy niet wordt vertrouwd.
   > Normaal gesproken werken transparante proxy's goed met Azure Stack Edge Pro.

4. (Optioneel) Selecteer in het linkerdeelvenster **Tijdinstellingen** en configureer vervolgens de tijdzone en de primaire en secundaire NTP-servers voor uw apparaat.  
    NTP-servers zijn vereist, omdat uw apparaat de tijd moet synchroniseren voor verificatie met uw cloudserviceproviders.
       
    Ga op de pagina **Tijdinstellingen** als volgt te werkt:
    
    1. Selecteer in de vervolgkeuzelijst **Tijdzone** de tijdzone die overeenkomt met de geografische locatie waar het apparaat wordt geïmplementeerd.
        De standaard tijdzone voor uw apparaat is PST. Het apparaat zal deze tijdzone gebruiken voor alle geplande bewerkingen.

    2. Voer in het vak **Primaire NTP-server** de primaire server voor uw apparaat in of accepteer de standaardwaarde van time.windows.com.  
        Zorg ervoor dat in uw netwerk NTP-verkeer kan worden doorgegeven van uw datacenter naar internet.

    3. Voer desgewenst in het vak **secundaire NTP-server** een secundaire server in voor uw apparaat.

    4. Selecteer **Instellingen toepassen** om de geconfigureerde tijdinstellingen te valideren en toe te passen.

        ![Pagina 'Tijdinstellingen' van lokale webgebruikersinterface](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-5.png)

5. (Optioneel) Selecteer in het linkerdeelvenster **Opslaginstellingen** om de tolerantie voor opslag op uw apparaat te configureren. Deze functie is momenteel beschikbaar als preview-product. De opslag op het apparaat is standaard niet tolerant en er treedt gegevensverlies op als een gegevensschijf op het apparaat mislukt. Wanneer u de optie Tolerant inschakelt, wordt de opslag op het apparaat opnieuw geconfigureerd en kan het apparaat de storing van één gegevensschijf doorstaan zonder dat dit gegevensverlies tot gevolg heeft. Als u de opslag configureert als tolerant, vermindert u de bruikbare capaciteit van uw apparaat.

    > [!IMPORTANT] 
    > De tolerantie kan alleen worden geconfigureerd voordat u het apparaat activeert. 

    ![Pagina 'Opslaginstellingen' van lokale webgebruikersinterface](./media/azure-stack-edge-deploy-connect-setup-activate/storage-settings.png)

6. Selecteer in het linkerdeelvenster de optie **Cloudinstellingen** en activeer uw apparaat vervolgens met de Azure Stack Edge-service in de Azure Portal.
    
    1. Voer in het vak **Activeringssleutel** de activeringssleutel in die u hebt ontvangen in [De activeringssleutel krijgen](azure-stack-edge-deploy-prep.md#get-the-activation-key) voor Azure Stack Edge Pro.
    2. Selecteer **Toepassen**.
       
        ![Pagina 'Cloudinstellingen' van lokale webgebruikersinterface](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-6.png)

    3. Eerst wordt het apparaat geactiveerd. Het apparaat wordt vervolgens gescand op essentiële updates en, indien beschikbaar, worden de updates automatisch toegepast. U ziet hiervoor een melding.

        Het dialoogvenster bevat ook een herstelsleutel die u moet kopiëren en opslaan op een veilige locatie. Deze sleutel wordt gebruikt om uw gegevens te herstellen in het geval dat het apparaat niet kan worden opgestart.

        ![Pagina 'Cloudinstellingen' van lokale webgebruikersinterface bijgewerkt](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-7.png)

    4. Mogelijk moet u enkele minuten wachten nadat de update is voltooid. De pagina wordt bijgewerkt om aan te geven dat het apparaat is geactiveerd.

        ![Pagina 'Cloudinstellingen' van lokale webgebruikersinterface bijgewerkt 2](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-8.png)

De apparaatinstelling is voltooid. U kunt nu shares toevoegen op uw apparaat.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Verbinding maken met een fysiek apparaat
> * Het fysieke apparaat instellen en activeren

Als u wilt weten hoe u gegevens overdraagt met uw Azure Stack Edge Pro-apparaat, gaat u naar:

> [!div class="nextstepaction"]
> [Gegevens overdragen met Azure Stack Edge Pro](./azure-stack-edge-deploy-add-shares.md).
