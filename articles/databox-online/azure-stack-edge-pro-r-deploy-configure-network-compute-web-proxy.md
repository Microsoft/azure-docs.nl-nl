---
title: Zelfstudie voor het configureren van netwerkinstellingen voor een Azure Stack Edge Pro R-apparaat in Azure Portal | Microsoft Docs
description: In deze zelfstudie voor het implementeren van Azure Stack Edge Pro R krijgt u instructies om instellingen voor het netwerk, het rekennetwerk en de webproxy voor uw fysieke apparaat te configureren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: alkohli
ms.openlocfilehash: a1f3966c8794b50f6ec369f1ea86905c4d8aaf3f
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059935"
---
# <a name="tutorial-configure-network-for-azure-stack-edge-pro-r"></a>Zelfstudie: Netwerk configureren voor Azure Stack Edge Pro R

In deze zelfstudie wordt beschreven hoe u het netwerk voor uw Azure Stack Edge Pro R-apparaat kunt configureren door de lokale webinterface te gebruiken.

Het verbindingsproces kan ongeveer twintig minuten duren.

In deze zelfstudie komen deze onderwerpen aan bod:

> [!div class="checklist"]
>
> * Vereisten
> * Netwerk configureren
> * Rekennetwerk inschakelen
> * Webproxy configureren


## <a name="prerequisites"></a>Vereisten

Zorg dat aan deze voorwaarden wordt voldaan voordat u uw Azure Stack Edge Pro R-apparaat configureert en instelt:

* U hebt het fysieke apparaat geïnstalleerd zoals beschreven in [Azure Stack Edge Pro R installeren](azure-stack-edge-gpu-deploy-install.md).
* U hebt verbinding gemaakt met de lokale webinterface van het apparaat, zoals beschreven in [Verbinding maken met Azure Stack Edge Pro R](azure-stack-edge-gpu-deploy-connect.md)


## <a name="configure-network"></a>Netwerk configureren

Op de pagina **Aan de slag** worden de verschillende instellingen weergegeven die nodig zijn voor het configureren en registreren van het fysieke apparaat bij de Azure Stack Edge-service. 

Volg deze stappen voor het configureren van het netwerk voor uw apparaat.

1. Ga in de lokale webinterface van uw apparaat naar de pagina **Aan de slag**. 

2. Selecteer op de tegel **Netwerk** de optie **Configureren** om naar de pagina **Netwerk** te gaan. 
    
    <!--![Local web UI "Network settings" tile](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-1.png)-->

    Er zijn vier netwerkinterfaces op het fysieke apparaat. Poort 1 en poort 2 zijn 1-Gbps-netwerkinterfaces. Poort 3 en poort 4 zijn beide 10/25-Gbps-netwerkinterfaces. Poort 1 wordt automatisch geconfigureerd als een beheerpoort en poort 2 tot en met poort 4 zijn allemaal gegevenspoorten. De pagina **Netwerk** is zoals hieronder wordt weergegeven.
    
    ![Pagina 'Netwerkinstellingen' van lokale webgebruikersinterface](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/network-2.png)

   
3. Als u de netwerkinstellingen wilt wijzigen, selecteert u een poort en wijzigt u in het rechterdeelvenster dat verschijnt, het IP-adres, het subnet, de gateway, de primaire DNS en de secundaire DNS. 

    - Als u poort 1 selecteert, ziet u dat deze vooraf is geconfigureerd als statisch. 

        ![Netwerkinstellingen van poort 1 van lokale webinterface](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/network-3.png)

    - Als u poort 2, poort 3 of poort 4 selecteert, worden al deze poorten standaard geconfigureerd als DHCP.

        ![Netwerkinstellingen van poort 3 van lokale webinterface](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/network-4.png)

    Houd bij het configureren van de netwerkinstellingen rekening met het volgende:

   * Als DHCP is ingeschakeld in uw omgeving, worden netwerkinterfaces automatisch geconfigureerd. Er wordt automatisch een IP-adres, subnet, gateway en DNS toegewezen.
   * Als DHCP niet is ingeschakeld, kunt u indien nodig statische IP-adressen toewijzen.
   * U kunt uw netwerkinterface configureren als IPv4.
   * Netwerk interface kaart (NIC) samen voeging of koppelings aggregatie wordt niet ondersteund met Azure Stack Edge.
   * Het serienummer van een poort komt overeen met het serienummer van het knooppunt.
    <!--* On the 25-Gbps interfaces, you can set the RDMA (Remote Direct Access Memory) mode to iWarp or RoCE (RDMA over Converged Ethernet). Where low latencies are the primary requirement and scalability is not a concern, use RoCE. When latency is a key requirement, but ease-of-use and scalability are also high priorities, iWARP is the best candidate.-->
    Zodra het apparaatnetwerk is geconfigureerd, wordt de pagina bijgewerkt, zoals hieronder weergegeven.

    ![Pagina 2, 'Netwerkinstellingen' van lokale webgebruikersinterface](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/network-2a.png)<!--change-->


     >[!NOTE]
     >
     > * Het is raadzaam om het lokale IP-adres van de netwerkinterface niet te wijzigen van statisch naar DHCP, tenzij u een ander IP-adres hebt om verbinding te maken met het apparaat. Als u één netwerkinterface gebruikt en u overschakelt naar DHCP, is er geen manier om het DHCP-adres te bepalen. Als u wilt overstappen op een DHCP-adres, wacht u totdat het apparaat bij de service is geactiveerd en brengt u vervolgens de wijziging aan. U kunt vervolgens de IP-adressen van alle adapters in de **Apparaateigenschappen** weergeven in de Azure Portal voor uw service.


    Nadat u de netwerkinstellingen hebt geconfigureerd en toegepast, selecteert u **Volgende: Berekening** om het rekennetwerk te configureren.

## <a name="enable-compute-network"></a>Rekennetwerk inschakelen

Voer de volgende stappen uit om het rekenproces in te schakelen en het rekennetwerk te configureren. 

1. Selecteer op de pagina **Berekening** een netwerkinterface die u voor rekenprocessen wilt inschakelen. 

    ![Pagina Berekening in lokale webinterface 2](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/compute-network-2.png)

1. Selecteer in het dialoogvenster **Netwerkinstellingen** de optie **Inschakelen**. Wanneer u een rekenproces inschakelt, wordt er op die netwerkinterface een virtuele switch op het apparaat gemaakt. De virtuele switch wordt gebruikt voor de rekeninfrastructuur op het apparaat. 
    
1. Wijs **IP-adressen aan Kubernetes-knooppunten** toe. Deze vaste IP-adressen zijn voor de reken-VM.  

    Voor een apparaat met *n* knooppunten wordt met behulp van de begin- en eind-IP-adressen een aansluitend bereik van minimaal *n + 1* IPv4-adressen (of meer) voor de reken-VM verstrekt. Aangezien Azure Stack Edge een apparaat met één knooppunt is, worden er minimaal twee aansluitende IPv4-adressen opgegeven. Deze IP-adressen moeten zich op hetzelfde netwerk bevinden als waarop u berekening hebt ingeschakeld en de virtuele switch is gemaakt.

    > [!IMPORTANT]
    > Kubernetes on Azure Stack Edge maakt gebruik van subnet 172.27.0.0/16 voor pods en subnet 172.28.0.0/16 voor services. Controleer of deze nog niet worden gebruikt in uw netwerk. Als deze subnetten al worden gebruikt in uw netwerk, kunt u deze subnetten wijzigen door de `Set-HcsKubeClusterNetworkInfo`-cmdlet uit te voeren vanuit de PowerShell-interface van het apparaat. Zie [Subnetten voor Kubernetes-pods en -services wijzigen](azure-stack-edge-gpu-connect-powershell-interface.md#change-kubernetes-pod-and-service-subnets) voor meer informatie.


1. Wijs **externe IP-adressen voor Kubernetes-services** toe. Dit zijn ook de IP-adressen voor de taakverdeling. Deze aansluitende IP-adressen zijn voor services die u buiten het Kubernetes-cluster zichtbaar wilt maken. U geeft het statische IP-bereik op, afhankelijk van het aantal weergegeven services. 
    
    > [!IMPORTANT]
    > U wordt ten zeerste aangeraden minimaal 1 IP-adres voor de Azure Stack Edge Pro R Hub-service op te geven om toegang te krijgen tot de rekenmodules. U kunt desgewenst extra IP-adressen opgeven voor andere services/IoT Edge-modules (één per service/module). Deze moeten toegankelijk zijn van buiten het cluster. De IP-adressen van de service kunnen later worden bijgewerkt. 
    
1. Selecteer **Toepassen**.

    ![Pagina Berekening in lokale webinterface 3](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/compute-network-3.png)

1. Het duurt enkele minuten voordat de configuratie is toegepast, en u moet de browser mogelijk vernieuwen. U kunt zien dat de opgegeven poort is ingeschakeld voor rekenen. 
 
    ![Pagina Berekening in lokale webinterface 4](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/compute-network-4.png)

    Selecteer **Volgende: Webproxy** om de webproxy te configureren.  

  
## <a name="configure-web-proxy"></a>Webproxy configureren

Dit is een optionele configuratie.

> [!IMPORTANT]
> * Als u berekening inschakelt en een IoT Edge-module op uw Azure Stack Edge Pro R-apparaat gebruikt, wordt u aangeraden om verificatie van de webproxy in te stellen op **Geen**. NTLM wordt niet ondersteund.
>* De bestanden voor het automatisch configureren van de proxy (PAC-bestanden) worden niet ondersteund. Een PAC-bestand definieert hoe webbrowsers en andere gebruikersagenten automatisch de juiste proxyserver (toegangsmethode) kunnen kiezen voor het ophalen van een bepaalde URL. Proxy's die al het verkeer proberen te onderscheppen en lezen (en vervolgens alles opnieuw ondertekenen met hun eigen certificering) zijn niet compatibel, omdat het certificaat van de proxy niet wordt vertrouwd. Normaal gesproken werken transparante proxy's goed in combinatie met Azure Stack Edge Pro R. Niet-transparante webproxy's worden niet ondersteund.


1. Op de pagina **Webproxyinstellingen** voert u de volgende stappen uit:

    1. Voer in het vak **Webproxy-URL** de URL in deze indeling in: `http://host-IP address or FQDN:Port number`. HTTPS-URL's worden niet ondersteund.

    2. Selecteer onder **Verificatie** **Geen** of **NTLM**. Als u berekening inschakelt en IoT Edge-module op uw Azure Stack Edge Pro R-apparaat gebruikt, raden we u aan om de verificatie van de webproxy in te stellen op **Geen**. **NTLM** wordt niet ondersteund.

    3. Als u verificatie gebruikt, voert u een gebruikersnaam en wachtwoord in.

    4. Selecteer **Toepassen** om de geconfigureerde webproxy-instellingen te valideren en toe te passen.
    
   ![Pagina 2, 'Webproxy-instellingen' voor lokale webgebruikersinterface](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/web-proxy-2.png)

2. Nadat de instellingen zijn toegepast, selecteert u **Volgende: Apparaat**.


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Vereisten
> * Netwerk configureren
> * Rekennetwerk inschakelen
> * Webproxy configureren


Als u wilt weten hoe u uw Azure Stack Edge Pro R-apparaat kunt instellen, gaat u naar:

> [!div class="nextstepaction"]
> [Apparaatinstellingen configureren](./azure-stack-edge-pro-r-deploy-set-up-device-update-time.md)
