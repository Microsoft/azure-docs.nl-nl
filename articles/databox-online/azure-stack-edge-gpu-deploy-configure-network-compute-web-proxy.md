---
title: Zelfstudie voor configureren van netwerkinstellingen voor een Azure Stack Edge Pro-apparaat met GPU in de Azure-portal | Microsoft Docs
description: In deze zelfstudie voor het implementeren van Azure Stack Edge Pro GPU krijgt u instructies om instellingen voor het netwerk, het rekennetwerk en de webproxy voor uw fysieke apparaat te configureren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: ac64233467166ca6567f1601c3b90f80fdba3dcf
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954645"
---
# <a name="tutorial-configure-network-for-azure-stack-edge-pro-with-gpu"></a>Zelfstudie: Netwerk configureren voor Azure Stack Edge Pro met GPU

In deze zelfstudie wordt beschreven hoe u het netwerk voor uw Azure Stack Edge Pro-apparaat met een onboard GPU kunt configureren door de lokale webinterface te gebruiken.

Het verbindingsproces kan ongeveer twintig minuten duren.

In deze zelfstudie komen deze onderwerpen aan bod:

> [!div class="checklist"]
>
> * Vereisten
> * Netwerk configureren
> * Rekennetwerk inschakelen
> * Webproxy configureren


## <a name="prerequisites"></a>Vereisten

Zorg dat aan deze voorwaarden wordt voldaan voordat u uw Azure Stack Edge Pro-apparaat met GPU configureert en instelt:

* U hebt het fysieke apparaat geïnstalleerd zoals beschreven in [Azure Stack Edge Pro installeren](azure-stack-edge-gpu-deploy-install.md).
* U hebt verbinding gemaakt met de lokale webinterface van het apparaat, zoals beschreven in [Verbinding maken met Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-connect.md)


## <a name="configure-network"></a>Netwerk configureren

Op de pagina **Aan de slag** worden de verschillende instellingen weergegeven die nodig zijn voor het configureren en registreren van het fysieke apparaat bij de Azure Stack Edge-service. 

Volg deze stappen voor het configureren van het netwerk voor uw apparaat.

1. Ga in de lokale webinterface van uw apparaat naar de pagina **Aan de slag**. 

2. Selecteer op de tegel **Netwerk** de optie **Configureren**.  
    
    ![Tegel Netwerkinstellingen van lokale webinterface](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-1.png)

    Er zijn zes netwerkinterfaces op het fysieke apparaat. Poort 1 en poort 2 zijn 1-Gbps-netwerkinterfaces. Poort 3, poort 4, poort 5 en poort 6 zijn allemaal netwerkinterfaces van 25 Gbps die ook kunnen dienen als netwerkinterfaces van 10 Gbps. Poort 1 wordt automatisch geconfigureerd als een beheerpoort en poort 2 tot poort 6 zijn allemaal gegevenspoorten. Voor een nieuw apparaat ziet de pagina **Netwerkinstellingen** eruit zoals hieronder wordt weergegeven.
    
    ![Pagina 'Netwerkinstellingen' van lokale webgebruikersinterface](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-2a.png)


   
3. Als u de netwerkinstellingen wilt wijzigen, selecteert u een poort en wijzigt u in het rechterdeelvenster dat verschijnt, het IP-adres, het subnet, de gateway, de primaire DNS en de secundaire DNS. 

    - Als u poort 1 selecteert, ziet u dat deze vooraf is geconfigureerd als statisch. 

        ![Netwerkinstellingen van poort 1 van lokale webinterface](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-3.png)

    - Als u poort 2, poort 3, poort 4 of poort 5 selecteert, worden al deze poorten standaard geconfigureerd als DHCP.

        ![Netwerkinstellingen van poort 3 van lokale webinterface](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-4.png)

    Houd bij het configureren van de netwerkinstellingen rekening met het volgende:

   * Als DHCP is ingeschakeld in uw omgeving, worden netwerkinterfaces automatisch geconfigureerd. Er wordt automatisch een IP-adres, subnet, gateway en DNS toegewezen.
   * Als DHCP niet is ingeschakeld, kunt u indien nodig statische IP-adressen toewijzen.
   * U kunt uw netwerkinterface configureren als IPv4.
   * Op de 25 Gbps-interfaces kunt u de RDMA-modus (Remote Direct Access Memory) instellen op iWarp of RoCE (RDMA over Converged Ethernet). Wanneer lage latenties de primaire vereiste zijn en schaalbaarheid er niet toe doet, gebruikt u RoCE. Wanneer latentie een belangrijke vereiste is, maar gebruiksgemak en schaalbaarheid ook een hoge prioriteit kennen, is iWARP de beste kandidaat.
   * Het serienummer van een poort komt overeen met het serienummer van het knooppunt.

    Zodra het apparaatnetwerk is geconfigureerd, wordt de pagina bijgewerkt, zoals hieronder weergegeven.

    ![Pagina 2, 'Netwerkinstellingen' van lokale webgebruikersinterface](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-2.png)


     >[!NOTE]
     >
     > * Het is raadzaam om het lokale IP-adres van de netwerkinterface niet te wijzigen van statisch naar DHCP, tenzij u een ander IP-adres hebt om verbinding te maken met het apparaat. Als u één netwerkinterface gebruikt en u overschakelt naar DHCP, is er geen manier om het DHCP-adres te bepalen. Als u wilt overstappen op een DHCP-adres, wacht u totdat het apparaat bij de service is geactiveerd en brengt u vervolgens de wijziging aan. U kunt vervolgens de IP-adressen van alle adapters in de **Apparaateigenschappen** weergeven in de Azure Portal voor uw service.


    Nadat u de netwerkinstellingen hebt geconfigureerd en toegepast, selecteert u Volgende: Berekening om het rekennetwerk te configureren.

## <a name="enable-compute-network"></a>Rekennetwerk inschakelen

Voer de volgende stappen uit om het rekenproces in te schakelen en het rekennetwerk te configureren. 

<!--1. Go to the **Get started** page in the local web UI of your device. On the **Network** tile, select **Compute network**.  

    ![Compute page in local UI 1](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-1.png)-->

1. Selecteer op de pagina **Berekening** een netwerkinterface die u voor rekenprocessen wilt inschakelen. 

    ![Pagina Berekening in lokale webinterface 2](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-2.png)

1. Selecteer in het dialoogvenster **Netwerkinstellingen** de optie **Inschakelen**. Wanneer u een rekenproces inschakelt, wordt er op die netwerkinterface een virtuele switch op het apparaat gemaakt. De virtuele switch wordt gebruikt voor de rekeninfrastructuur op het apparaat. 
    
1. Wijs **IP-adressen aan Kubernetes-knooppunten** toe. Deze vaste IP-adressen zijn voor de reken-VM.  

    Voor een apparaat met *n* knooppunten wordt met behulp van de begin- en eind-IP-adressen een aansluitend bereik van minimaal *n + 1* IPv4-adressen (of meer) voor de reken-VM verstrekt. Aangezien Azure Stack Edge een apparaat met één knooppunt is, worden er minimaal twee aansluitende IPv4-adressen opgegeven.

    > [!IMPORTANT]
    > Kubernetes on Azure Stack Edge maakt gebruik van subnet 172.27.0.0/16 voor pods en subnet 172.28.0.0/16 voor services. Controleer of deze nog niet worden gebruikt in uw netwerk. Als deze subnetten al worden gebruikt in uw netwerk, kunt u deze subnetten wijzigen door de `Set-HcsKubeClusterNetworkInfo`-cmdlet uit te voeren vanuit de PowerShell-interface van het apparaat. Zie [Subnetten voor Kubernetes-pods en -services wijzigen](azure-stack-edge-gpu-connect-powershell-interface.md#change-kubernetes-pod-and-service-subnets) voor meer informatie.


1. Wijs **externe IP-adressen voor Kubernetes-services** toe. Dit zijn ook de IP-adressen voor de taakverdeling. Deze aansluitende IP-adressen zijn voor services die u buiten het Kubernetes-cluster zichtbaar wilt maken. U geeft het statische IP-bereik op, afhankelijk van het aantal weergegeven services. 
    
    > [!IMPORTANT]
    > U wordt ten sterkste aangeraden minimaal 1 IP-adres voor de Azure Stack Edge Pro Hub-service op te geven om toegang te krijgen tot de rekenmodules. U kunt desgewenst extra IP-adressen opgeven voor andere services/IoT Edge-modules (één per service/module). Deze moeten toegankelijk zijn van buiten het cluster. De IP-adressen van de service kunnen later worden bijgewerkt. 
    
1. Selecteer **Toepassen**.

    ![Pagina Berekening in lokale webinterface 3](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-3.png)

1. Het duurt enkele minuten voordat de configuratie is toegepast, en u moet de browser mogelijk vernieuwen. U kunt zien dat de opgegeven poort is ingeschakeld voor rekenen. 
 
    ![Pagina Berekening in lokale webinterface 4](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-4.png)

    Selecteer **Volgende: Webproxy** om de webproxy te configureren.  

  
## <a name="configure-web-proxy"></a>Webproxy configureren

Dit is een optionele configuratie.

> [!IMPORTANT]
> * Als u berekening inschakelt en een IoT Edge-module op uw Azure Stack Edge Pro-apparaat gebruikt, wordt u aangeraden de verificatie van de webproxy in te stellen op **Geen**. NTLM wordt niet ondersteund.
> * De bestanden voor het automatisch configureren van de proxy (PAC-bestanden) worden niet ondersteund. Een PAC-bestand definieert hoe webbrowsers en andere gebruikersagenten automatisch de juiste proxyserver (toegangsmethode) kunnen kiezen voor het ophalen van een bepaalde URL. 
> * Transparante proxy's werken goed met Azure Stack Edge Pro. Voor niet-transparante proxy's die alle verkeer (via hun eigen certificaten die op de proxy server zijn geïnstalleerd) onderscheppen en lezen, uploadt u de open bare sleutel van het proxy certificaat als de handtekening keten op uw Azure Stack Edge Pro-apparaat. U kunt vervolgens de instellingen voor de proxy server configureren op uw Azure Stack edge-apparaat. Zie [uw eigen certificaten meenemen en uploaden via de lokale gebruikers interface](azure-stack-edge-gpu-deploy-configure-certificates.md#bring-your-own-certificates)voor meer informatie.  

<!--1. Go to the **Get started** page in the local web UI of your device.
2. On the **Network** tile, configure your web proxy server settings. Although web proxy configuration is optional, if you use a web proxy, you can configure it on this page only.

   ![Local web UI "Web proxy settings" page](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/web-proxy-1.png)-->

1. Op de pagina **Webproxyinstellingen** voert u de volgende stappen uit:

    1. Voer in het vak **Webproxy-URL** de URL in deze indeling in: `http://host-IP address or FQDN:Port number`. HTTPS-URL's worden niet ondersteund.

    2. Selecteer onder **Verificatie** **Geen** of **NTLM**. Als u berekening inschakelt en IoT Edge-module op uw Azure Stack Edge Pro-apparaat gebruikt, raden we u aan om de verificatie van de webproxy in te stellen op **Geen**. **NTLM** wordt niet ondersteund.

    3. Als u verificatie gebruikt, voert u een gebruikersnaam en wachtwoord in.

    4. Selecteer **Toepassen** om de geconfigureerde webproxy-instellingen te valideren en toe te passen.
    
   ![Pagina 2, 'Webproxy-instellingen' voor lokale webgebruikersinterface](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/web-proxy-2.png)

2. Nadat de instellingen zijn toegepast, selecteert u **Volgende: Apparaat**.


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Vereisten
> * Netwerk configureren
> * Rekennetwerk inschakelen
> * Webproxy configureren


Als u wilt weten hoe u uw Azure Stack Edge Pro-apparaat kunt instellen, gaat u naar:

> [!div class="nextstepaction"]
> [Apparaatinstellingen configureren](./azure-stack-edge-gpu-deploy-set-up-device-update-time.md)
