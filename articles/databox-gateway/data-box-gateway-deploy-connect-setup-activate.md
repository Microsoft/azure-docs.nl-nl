---
title: Verbinding maken met Azure Data Box Gateway in Azure Portal en dit configureren en activeren
description: De derde zelfstudie voor het implementeren van Data Box Gateway geeft instructies voor het verbinding maken, instellen en activeren van uw virtuele apparaat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: alkohli
ms.openlocfilehash: cb6ef6ac25c4afa72160ba437e0ea3b5492cfd93
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96580324"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-gateway"></a>Zelfstudie: Azure Data Box Gateway verbinden, instellen en activeren

## <a name="introduction"></a>Inleiding

In deze zelfstudie wordt beschreven hoe u met behulp van de lokale webinterface verbinding kunt maken met uw Data Box Gateway-apparaat, hoe u het apparaat instelt en activeert. 

Het installatie- en activeringsproces kan ongeveer 10 minuten duren. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Verbinding maken met een virtueel apparaat
> * Het virtuele apparaat instellen en activeren

## <a name="prerequisites"></a>Vereisten

Voordat u uw Data Box Gateway configureert en instelt, controleert u het volgende:

* U hebt een virtueel apparaat ingericht en een verbonden URL verkregen zoals beschreven in [Data Box Gateway inrichten in Hyper-V](data-box-gateway-deploy-provision-hyperv.md) of [Data Box Gateway inrichten in VMware](data-box-gateway-deploy-provision-vmware.md).
* U hebt de activeringssleutel van de Data Box Gateway-service die u hebt gemaakt voor het beheren van Data Box Gateway-apparaten. Ga voor meer informatie naar [Voorbereiding voor implementatie van Azure Data Box Gateway](data-box-gateway-deploy-prep.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Verbinding maken met de lokale gebruikersinterface instellen 

1. Open een browservenster en krijg toegang tot de lokale gebruikersinterface van het apparaat op:
   
   https:\//ip-address-of-network-interface
   
   Gebruik de verbindings-URL die u in de vorige zelfstudie hebt genoteerd. U ziet nu een foutmelding of waarschuwing waarin wordt aangegeven dat er een probleem is met het beveiligingscertificaat van de website.

2. Selecteer **Doorgaan naar deze webpagina**. Deze stappen kunnen variëren, afhankelijk van de browser die u gebruikt.
   
    ![Foutbericht van beveiligingscertificaat voor website](./media/data-box-gateway-deploy-connect-setup-activate/image2.png)

3. Meld u aan bij de webgebruikersinterface van uw virtuele apparaat. Het standaardapparaatwachtwoord is *Wachtwoord1*. 
   
    ![Aanmelden bij de lokale webgebruikersinterface](./media/data-box-gateway-deploy-connect-setup-activate/image3.png)

4. Wijzig het apparaatwachtwoord bij de prompt. Het nieuwe wachtwoord moet tussen 8 en 16 tekens bevatten. Het wachtwoord moet drie van de volgende tekens bevatten: kleine letters, hoofdletters, cijfers en speciale tekens.

    ![Wachtwoord voor apparaat wijzigen](./media/data-box-gateway-deploy-connect-setup-activate/image4.png)

U bent nu op het **dashboard** van uw apparaat.

## <a name="set-up-and-activate-the-virtual-device"></a>Het virtuele apparaat instellen en activeren
 
Uw dashboard geeft de verschillende instellingen weer die nodig zijn voor het configureren en registreren van het virtuele apparaat bij de Data Box Gateway-service. De **Apparaatnaam**, **Netwerkinstellingen**, **Instellingen webproxy** en **Tijdinstellingen** zijn optioneel. De enige vereiste instellingen zijn **Cloudinstellingen**.
   
![Pagina 'Dashboard' van de lokale webgebruikersinterface](./media/data-box-gateway-deploy-connect-setup-activate/image5.png)

1. Selecteer in het linkerdeelvenster **Apparaatnaam** en geef een beschrijvende naam op voor het apparaat. De beschrijvende naam moet tussen 1 en 15 tekens lang zijn en mag alleen letters, cijfers en afbreekstreepjes bevatten. 

    ![Pagina 'Apparaatnaam' van de lokale webgebruikersinterface](./media/data-box-gateway-deploy-connect-setup-activate/image6.png)

2. (Optioneel) Selecteer in het linkerdeelvenster **Netwerkinstellingen** en configureer vervolgens de instellingen. Op het virtuele apparaat ziet u ten minste één netwerkinterface en meer, afhankelijk van het aantal dat u hebt geconfigureerd in de onderliggende virtuele machine. De pagina **Netwerkinstellingen** voor een virtueel apparaat met één netwerkinterface ingeschakeld wordt zoals hieronder weergegeven.
    
    ![Pagina 'Netwerkinstellingen' van lokale webgebruikersinterface](./media/data-box-gateway-deploy-connect-setup-activate/image7.png)
   
    Houd bij het configureren van de netwerkinstellingen rekening met het volgende:

    - Als DHCP is ingeschakeld in uw omgeving, worden netwerkinterfaces automatisch geconfigureerd. Er wordt automatisch een IP-adres, subnet, gateway en DNS toegewezen.
    - Als DHCP niet is ingeschakeld, kunt u indien nodig statische IP-adressen toewijzen.
    - U kunt uw netwerkinterface configureren als IPv4.

     >[!NOTE] 
     > Het is raadzaam om het lokale IP-adres van de netwerkinterface niet te wijzigen van statisch naar DHCP, tenzij u een ander IP-adres hebt om verbinding te maken met het apparaat. Als u één netwerkinterface gebruikt en u overschakelt naar DHCP, is er geen manier om het DHCP-adres te bepalen. Als u wilt wijzigen naar een DHCP-adres, wacht u totdat het apparaat is geregistreerd bij de service en brengt u vervolgens de wijziging aan. U kunt vervolgens de IP-adressen van alle adapters in de **Apparaateigenschappen** weergeven in de Azure Portal voor uw service.

3. Configureer desgewenst uw webproxyserver. De configuratie van uw webproxy is weliswaar optioneel, maar als u een webproxy gebruikt, kan deze alleen op deze pagina worden geconfigureerd.
   
   ![Pagina 'Webproxy-instellingen' voor lokale webgebruikersinterface](./media/data-box-gateway-deploy-connect-setup-activate/image8.png)
   
   Ga op de pagina **Webproxy** als volgt te werkt:
   
   1. Voer in het vak **Webproxy-URL** de URL in deze indeling in: `http://&lt;host-IP address or FQDN&gt;:Port number`. HTTPS-URL's worden niet ondersteund.
   2. Selecteer onder **Verificatie** **Geen** of **NTLM**.
   3. Als u verificatie gebruikt, voert u een **Gebruikersnaam** en **Wachtwoord** in.
   4. Selecteer **Toepassen** om de geconfigureerde webproxy-instellingen te valideren en toe te passen.

   > [!NOTE]
   > De bestanden voor het automatisch configureren van de proxy (PAC-bestanden) worden niet ondersteund. Een PAC-bestand definieert hoe webbrowsers en andere gebruikersagenten automatisch de juiste proxyserver (toegangsmethode) kunnen kiezen voor het ophalen van een bepaalde URL.
   > Proxy's die al het verkeer proberen te onderscheppen en lezen (en vervolgens alles opnieuw ondertekenen met hun eigen certificering) zijn niet compatibel, omdat het certificaat van de proxy niet wordt vertrouwd.
   > Normaal gesproken werken transparante proxy's goed met Azure Data Box Gateway.

4. (Optioneel) Selecteer in het linkerdeelvenster **Tijdinstellingen** en configureer vervolgens de tijdzone en de primaire en secundaire NTP-servers voor uw apparaat. 

    NTP-servers zijn vereist, omdat uw apparaat de tijd moet synchroniseren voor verificatie met uw cloudserviceproviders.
    
    ![Pagina 'Tijdinstellingen' van lokale webgebruikersinterface](./media/data-box-gateway-deploy-connect-setup-activate/image9.png)
    
    Ga op de pagina **Tijdinstellingen** als volgt te werkt:
    
    1. Selecteer in de vervolgkeuzelijst **Tijdzone** de tijdzone die overeenkomt met de geografische locatie waar het apparaat wordt geïmplementeerd.
        De standaard tijdzone voor uw apparaat is PST. Het apparaat zal deze tijdzone gebruiken voor alle geplande bewerkingen.

    2. Geef een **Primaire NTP-server** op voor uw apparaat of accepteer de standaardwaarde van `time.windows.com`.   
        Zorg ervoor dat in uw netwerk NTP-verkeer kan worden doorgegeven van uw datacenter naar internet.

    3. Voer desgewenst in het vak **Secundaire NTP-server** een secundaire server in voor uw apparaat.

    4. Selecteer **Toepassen** om de geconfigureerde tijdinstellingen te valideren en toe te passen.

6. Selecteer in het linkerdeelvenster de optie **Cloudinstellingen** en activeer uw apparaat vervolgens met de Data Box Gateway-service in de Azure Portal.
    
    1. Voer in het vak **Activeringssleutel** de **activeringssleutel** in die u hebt ontvangen in [De activeringssleutel krijgen](data-box-gateway-deploy-prep.md#get-the-activation-key) voor Data Box Gateway.

    2. Selecteer **Activate**.
       
         ![Pagina 'Cloudinstellingen' van lokale webgebruikersinterface](./media/data-box-gateway-deploy-connect-setup-activate/image10a.png)
    
    3. Het apparaat wordt geactiveerd en essentiële updates, indien beschikbaar, worden automatisch toegepast. U ziet hiervoor een melding. Controleer de voortgang van de update via de Azure Portal.

        ![Pagina 2 van 'Cloudinstellingen' van de lokale webgebruikersinterface](./media/data-box-gateway-deploy-connect-setup-activate/image12.png)
        
        **Het dialoogvenster bevat ook een herstelsleutel die u moet kopiëren en opslaan op een veilige locatie. Deze sleutel wordt gebruikt om uw gegevens te herstellen in het geval dat het apparaat niet kan worden opgestart.**


    4. Mogelijk moet u enkele minuten wachten totdat de update is voltooid. Nadat de update is voltooid, meldt u zich aan bij het apparaat. De pagina **Cloudinstellingen** wordt bijgewerkt om aan te geven dat het apparaat is geactiveerd.

        ![Pagina 'Cloudinstellingen' van lokale webgebruikersinterface bijgewerkt](./media/data-box-gateway-deploy-connect-setup-activate/image13.png)

De apparaatinstelling is voltooid. U kunt nu shares toevoegen op uw apparaat.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Verbinding maken met een virtueel apparaat
> * Het virtuele apparaat instellen en activeren

Voor meer informatie over het overdragen van gegevens met uw Data Box Gateway raadpleegt u:

> [!div class="nextstepaction"]
> [Gegevens overdragen met Data Box Gateway](./data-box-gateway-deploy-add-shares.md).
