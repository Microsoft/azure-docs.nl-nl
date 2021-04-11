---
title: Zelfstudie voor configureren van netwerkinstellingen voor een Azure Stack Edge Mini R-apparaat in de Azure-portal
description: In deze zelfstudie voor het implementeren van Azure Stack Edge Mini R krijgt u instructies om instellingen voor het netwerk, het rekennetwerk en de webproxy voor uw fysieke apparaat te configureren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: alkohli
ms.openlocfilehash: 34a11679626653afd04b0cd17c77188cbc995308
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061720"
---
# <a name="tutorial-configure-network-for-azure-stack-edge-mini-r"></a>Zelfstudie: Netwerk configureren voor Azure Stack Edge Mini R

In deze zelfstudie wordt beschreven hoe u het netwerk voor uw Azure Stack Edge Mini R-apparaat met een onboard GPU kunt configureren door de lokale webinterface te gebruiken.

Het verbindingsproces kan ongeveer twintig minuten duren.

In deze zelfstudie komen deze onderwerpen aan bod:

> [!div class="checklist"]
>
> * Vereisten
> * Netwerk configureren
> * Rekennetwerk inschakelen
> * Webproxy configureren


## <a name="prerequisites"></a>Vereisten

Zorg dat aan deze voorwaarden wordt voldaan voordat u uw Azure Stack Edge Mini R-apparaat configureert en instelt:

* U hebt het fysieke apparaat geïnstalleerd zoals beschreven in [Azure Stack Edge Mini R installeren](azure-stack-edge-gpu-deploy-install.md).
* U hebt verbinding gemaakt met de lokale webinterface van het apparaat, zoals beschreven in [Verbinding maken met Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-connect.md)


## <a name="configure-network"></a>Netwerk configureren

Op de pagina **Aan de slag** worden de verschillende instellingen weergegeven die nodig zijn voor het configureren en registreren van het fysieke apparaat bij de Azure Stack Edge-service. 

Volg deze stappen voor het configureren van het netwerk voor uw apparaat.

1. Ga in de lokale webinterface van uw apparaat naar de pagina **Aan de slag**. 

2. Als er een zero day-update nodig is, kunt u die hier uitvoeren door een gegevenspoort te configureren met een bekabelde verbinding. Zie [Uw apparaat van een bekabelde verbinding voorzien](azure-stack-edge-mini-r-deploy-install.md#cable-the-device) voor meer instructies over het instellen van een bekabelde verbinding voor dit apparaat. Nadat de update is voltooid, kunt u de bekabelde verbinding verwijderen.

3. Certificaten maken voor Wi-Fi en ondertekening. Zowel de ondertekeningsketen als de Wi-Fi-certificaten moeten de DER-indeling hebben met een *.CER*-bestandsextensie. Zie [Certificaten maken](azure-stack-edge-gpu-manage-certificates.md) voor instructies.

4. Ga in de lokale webinterface naar **Aan de slag**. Selecteer op de tegel **Beveiliging** de optie **Certificaten** en selecteer vervolgens **Configureren**. 

    [![Pagina Certificaten van lokale webinterface](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/get-started-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/get-started-1.png#lightbox)

    1. Selecteer **+ Certificaat toevoegen**. 
    
        [![Pagina 1 "Certificaten" van lokale webinterface](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-1.png#lightbox)

    2. Upload de ondertekeningsketen en selecteer **Toepassen**.

        ![Pagina 2 Certificaten van lokale webinterface](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-2.png)

    3. Herhaal de procedure met het Wi-Fi-certificaat. 

        ![Pagina 3 Certificaten van lokale webinterface](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-4.png)

    4. De nieuwe certificaten moeten worden weergegeven op de pagina **Certificaten**. 
    
        [![Pagina 4 Certificaten van lokale webinterface](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-5.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-5.png#lightbox)

    5. Ga terug naar **Aan de slag**.

3. Selecteer op de tegel **Netwerk** de optie **Configureren**.  
    
    Er zijn vijf netwerkinterfaces op het fysieke apparaat. Poort 1 en poort 2 zijn 1-Gbps-netwerkinterfaces. Poort 3 en poort 4 zijn beide 10-Gbps-netwerkinterfaces. De vijfde poort is de Wi-Fi-poort. 

    [![Pagina 1, 'Netwerkinstellingen' van lokale webgebruikersinterface](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png#lightbox)    
    
    Selecteer de Wi-Fi-poort en configureer de poortinstellingen. 
    
    > [!IMPORTANT]
    > We raden u sterk aan een vast IP-adres voor de Wi-Fi-poort te configureren.  

    ![Pagina 2, 'Netwerkinstellingen' van lokale webgebruikersinterface](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-2.png)

    De pagina **Netwerk** wordt bijgewerkt nadat u de instellingen voor de Wi-Fi-poort hebt toegepast.

    ![Pagina 3, 'Netwerkinstellingen' van lokale webgebruikersinterface](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-4.png)
   
4. Selecteer **Wi-Fi-profiel toevoegen** en upload uw Wi-Fi-profiel. 

    ![Netwerkinstellingen van Wi-Fi-poort van lokale webinterface](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-1.png)
    
    Het profiel van een draadloos netwerk bevat de SSID (netwerknaam), wachtwoordsleutel en beveiligingsgegevens waarmee verbinding kan worden gemaakt met een draadloos netwerk. U kunt het Wi-Fi-profiel voor uw omgeving van uw netwerkbeheerder krijgen.

    ![Netwerkinstellingen van poort 2 van lokale webinterface](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-2.png)

    Nadat het profiel is toegevoegd, wordt de lijst met Wi-Fi-profielen bijgewerkt om het nieuwe profiel weer te geven. In het profiel moet de **Verbindingsstatus** worden weergegeven als **Niet-verbonden**. 

    ![Netwerkinstellingen van poort 3 van lokale webinterface](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-3.png)


5. Nadat het profiel van het draadloos netwerk is geladen, maakt u verbinding met dit profiel. Selecteer **Verbinding maken met Wi-Fi-profiel**. 

    ![Netwerkinstellingen van poort 4 van lokale webinterface](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-4.png)

6. Selecteer het Wi-Fi profiel dat u in de vorige stap hebt toegevoegd en selecteer **Toep assen**. 

    ![Netwerkinstellingen van poort 5 van lokale webinterface](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-5.png)

    De **Verbindingsstatus** moet worden bijgewerkt naar **Verbonden**. De signaalsterkte wordt bijgewerkt om de kwaliteit van het signaal aan te geven. 

    ![Netwerkinstellingen van poort 6 van lokale webinterface](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-6.png)

    > [!NOTE]
    > Als u grote hoeveelheden gegevens wilt overdragen, wordt u aangeraden een bekabelde verbinding te gebruiken in plaats van het draadloze netwerk. 

6. Koppel poort 1 op het apparaat los van de laptop. 

7. Houd bij het configureren van de netwerkinstellingen rekening met het volgende:

   - Als DHCP is ingeschakeld in uw omgeving, worden netwerkinterfaces automatisch geconfigureerd. Er wordt automatisch een IP-adres, subnet, gateway en DNS toegewezen.
   - Als DHCP niet is ingeschakeld, kunt u indien nodig statische IP-adressen toewijzen.
   - U kunt uw netwerkinterface configureren als IPv4.
   - Netwerk interface kaart (NIC) samen voeging of koppelings aggregatie wordt niet ondersteund met Azure Stack Edge.
   - Het serienummer van een poort komt overeen met het serienummer van het knooppunt. Voor een apparaat uit de K-serie wordt slechts één serienummer weergegeven.

     >[!NOTE] 
     > Het is raadzaam om het lokale IP-adres van de netwerkinterface niet te wijzigen van statisch naar DHCP, tenzij u een ander IP-adres hebt om verbinding te maken met het apparaat. Als u één netwerkinterface gebruikt en u overschakelt naar DHCP, is er geen manier om het DHCP-adres te bepalen. Als u wilt wijzigen naar een DHCP-adres, wacht u totdat het apparaat is geregistreerd bij de service en brengt u vervolgens de wijziging aan. U kunt vervolgens de IP-adressen van alle adapters in de **Apparaateigenschappen** weergeven in de Azure Portal voor uw service.

Nadat u de netwerkinstellingen hebt geconfigureerd en toegepast, selecteert u **Volgende: Berekening** om het rekennetwerk te configureren.

## <a name="enable-compute-network"></a>Rekennetwerk inschakelen

Voer de volgende stappen uit om het rekenproces in te schakelen en het rekennetwerk te configureren. 


1. Selecteer op de pagina **Berekening** een netwerkinterface die u voor rekenprocessen wilt inschakelen. 

    ![Pagina Berekening in lokale webinterface 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/compute-network-1.png)

1. Selecteer in het dialoogvenster **Netwerkinstellingen** de optie **Inschakelen**. Wanneer u een rekenproces inschakelt, wordt er op die netwerkinterface een virtuele switch op het apparaat gemaakt. De virtuele switch wordt gebruikt voor de rekeninfrastructuur op het apparaat. 
    
1. Wijs **IP-adressen aan Kubernetes-knooppunten** toe. Deze vaste IP-adressen zijn voor de reken-VM.  

    Voor een apparaat met *n* knooppunten wordt met behulp van de begin- en eind-IP-adressen een aansluitend bereik van minimaal *n + 1* IPv4-adressen (of meer) voor de reken-VM verstrekt. Aangezien Azure Stack Edge een apparaat met één knooppunt is, worden er minimaal twee aansluitende IPv4-adressen opgegeven.

    > [!IMPORTANT]
    > Kubernetes on Azure Stack Edge maakt gebruik van subnet 172.27.0.0/16 voor pods en subnet 172.28.0.0/16 voor services. Controleer of deze nog niet worden gebruikt in uw netwerk. Als deze subnetten al worden gebruikt in uw netwerk, kunt u deze subnetten wijzigen door de `Set-HcsKubeClusterNetworkInfo`-cmdlet uit te voeren vanuit de PowerShell-interface van het apparaat. Zie [Subnetten voor Kubernetes-pods en -services wijzigen](azure-stack-edge-gpu-connect-powershell-interface.md#change-kubernetes-pod-and-service-subnets) voor meer informatie.


1. Wijs **externe IP-adressen voor Kubernetes-services** toe. Dit zijn ook de IP-adressen met taak verdeling. Deze aaneengesloten IP-adressen zijn voor services die u buiten het Kubernetes-cluster zichtbaar wilt maken en u geeft het statische IP-bereik op, afhankelijk van het aantal weer gegeven Services. 
    
    > [!IMPORTANT]
    > U wordt ten sterkste aangeraden minimaal 1 IP-adres voor de Azure Stack Edge Mini R Hub-service op te geven om toegang te krijgen tot de rekenmodules. U kunt desgewenst extra IP-adressen opgeven voor andere services/IoT Edge-modules (één per service/module). Deze moeten toegankelijk zijn van buiten het cluster. De IP-adressen van de service kunnen later worden bijgewerkt. 
    
1. Selecteer **Toepassen**.

    ![Pagina Berekening in lokale webinterface 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/compute-network-3.png)

1. Het duurt enkele minuten voordat de configuratie is toegepast, en u moet de browser mogelijk vernieuwen. U kunt zien dat de opgegeven poort is ingeschakeld voor rekenen. 
 
    ![Pagina Berekening in lokale webinterface 4](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/compute-network-4.png)

    Selecteer **Volgende: Webproxy** om de webproxy te configureren.  

  
## <a name="configure-web-proxy"></a>Webproxy configureren

Dit is een optionele configuratie.

> [!IMPORTANT]
> * Als u berekening inschakelt en een IoT Edge-module op uw Azure Stack Edge Mini R-apparaat gebruikt, wordt u aangeraden de verificatie van de webproxy in te stellen op **Geen**. NTLM wordt niet ondersteund.
>* De bestanden voor het automatisch configureren van de proxy (PAC-bestanden) worden niet ondersteund. Een PAC-bestand definieert hoe webbrowsers en andere gebruikersagenten automatisch de juiste proxyserver (toegangsmethode) kunnen kiezen voor het ophalen van een bepaalde URL. Proxy's die al het verkeer proberen te onderscheppen en lezen (en vervolgens alles opnieuw ondertekenen met hun eigen certificering) zijn niet compatibel, omdat het certificaat van de proxy niet wordt vertrouwd. Normaal gesproken werken transparante proxy's goed in combinatie met Azure Stack Edge Mini R. Niet-transparante webproxy's worden niet ondersteund.


1. Op de pagina **Webproxyinstellingen** voert u de volgende stappen uit:

    1. Voer in het vak **Webproxy-URL** de URL in deze indeling in: `http://host-IP address or FQDN:Port number`. HTTPS-URL's worden niet ondersteund.

    2. Selecteer onder **Verificatie** **Geen** of **NTLM**. Als u berekening inschakelt en een IoT Edge-module op uw Azure Stack Edge Mini R-apparaat gebruikt, wordt u aangeraden de verificatie van de webproxy in te stellen op **Geen**. **NTLM** wordt niet ondersteund.

    3. Als u verificatie gebruikt, voert u een gebruikersnaam en wachtwoord in.

    4. Selecteer **Toepassen** om de geconfigureerde webproxy-instellingen te valideren en toe te passen.
    
   ![Pagina 'Webproxy-instellingen' voor lokale webgebruikersinterface](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/web-proxy-1.png)

2. Nadat de instellingen zijn toegepast, selecteert u **Volgende: Apparaat**.


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Vereisten
> * Netwerk configureren
> * Rekennetwerk inschakelen
> * Webproxy configureren


Als u wilt weten hoe u uw Azure Stack Edge Mini R-apparaat kunt instellen, gaat u naar:

> [!div class="nextstepaction"]
> [Apparaatinstellingen configureren](./azure-stack-edge-mini-r-deploy-set-up-device-update-time.md)
