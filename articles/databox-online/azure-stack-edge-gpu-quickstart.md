---
title: Quickstart voor het configureren en implementeren van Azure Stack Edge GPU | Microsoft Docs
description: Ga aan de slag met het implementeren van Azure Stack Edge GPU nadat het apparaat is ontvangen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: alkohli
ms.openlocfilehash: b6389152116a47bedbc9c2dc44231f3e1732fdc6
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055107"
---
# <a name="quickstart-get-started-with-azure-stack-edge-pro-with-gpu"></a>Quickstart: Aan de slag met Azure Stack Edge Pro met GPU 

Deze quickstart bevat details over de vereisten en de vereiste stappen voor het implementeren van uw Azure Stack Edge Pro GPU-apparaat. De stappen van de quickstart worden uitgevoerd in de Azure-portal en op de lokale webgebruikersinterface van het apparaat. 

Het duurt ongeveer 1,5 uur om de hele procedure te voltooien. Ga voor gedetailleerde stapsgewijze instructies naar [Zelfstudie: Voorbereiden op de implementatie van Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md#deployment-configuration-checklist). 


## <a name="prerequisites"></a>Vereisten

Voordat u implementeert, moet u ervoor zorgen dat aan de volgende vereisten is voldaan:

1. Het Azure Stack Edge Pro GPU-apparaat bevindt zich op uw locatie, is [uitgepakt](azure-stack-edge-gpu-deploy-install.md#unpack-the-device) en [op het rek gemonteerd](azure-stack-edge-gpu-deploy-install.md#rack-the-device). 
1. Configureer uw netwerk zo dat uw apparaat de [vermelde URL-patronen en poorten](azure-stack-edge-gpu-system-requirements.md#networking-port-requirements)kan bereiken. 
1. U hebt toegang als Eigenaar of Medewerker tot het Azure-abonnement.
1. Ga in de Azure-portal naar **Home > Abonnementen > Uw abonnement > Resourceproviders**. Ga naar `Microsoft.DataBoxEdge` en registreer de resourceprovider. Herhaal dit om `Microsoft.Devices` te registreren als u een IoT Hub-resource maakt om rekenworkloads te implementeren.
1. Zorg ervoor dat u minstens 2 gratis, statische, aaneengesloten IP-adressen voor Kubernetes-knooppunten hebt, en minstens 1 statisch IP-adres voor de IoT Edge-service. Voor elke module of externe service die u implementeert, hebt u 1 extra IP-adres nodig.
1. Raadpleeg de [controlelijst voor implementatie](azure-stack-edge-gpu-deploy-checklist.md) om ervoor te zorgen dat u alles hebt wat nodig is voor apparaatconfiguratie. 


## <a name="deployment-steps"></a>Implementatiestappen

1. **Installeren**: Sluit POORT 1 aan op een clientcomputer via een crossover-kabel of USB Ethernet-adapter. Verbind minstens één andere poort van het apparaat voor gegevens, bij voorkeur 25 GbE, (van POORT 3 naar POORT 6), met internet via minstens 1 GbE-schakelaar en koperen SFP+-kabels. Sluit de meegeleverde voedingskabels aan op de voedingseenheden en op aparte stopcontacten. Druk op de aan/uit-knop op het voorpaneel om het apparaat in te schakelen.  

    Zie [Cavium FastlinQ 41000 Series-interoperabiliteitsmatrix](https://www.marvell.com/documents/xalflardzafh32cfvi0z/) en [Compatibele producten met de Mellanox dual port 25G ConnectX-4-kanaalnetwerkadapter](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products) voor compatibele netwerkkabels en -schakelaars.

    Hier volgt de minimale bekabelingsconfiguratie die nodig is om uw apparaat te implementeren:  ![Achterpaneel van een bekabeld apparaat](./media/azure-stack-edge-gpu-quickstart/backplane-min-cabling-1.png)

2. **Verbinding maken**: Configureer de IPv4-instellingen voor de Ethernet-adapter op uw computer met het statische IP-adres **192.168.100.5** en subnet **255.255.255.0**. Open de browser en maak verbinding met de lokale web-UI van het apparaat op https://192.168.100.10. Dit kan enkele minuten duren. Ga naar de webpagina wanneer u de waarschuwing over een beveiligingscertificaat ziet.

3. **Aanmelden**: Meld u bij het apparaat aan met het standaardwachtwoord *Password1*. Wijzig het beheerderswachtwoord voor het apparaat. Het wachtwoord moet tussen de 8 en 16 tekens lang zijn, en 3 van de volgende tekens bevatten: kleine letters, hoofdletters, numerieke tekens en speciale tekens.

4. **Netwerk configureren**: Accepteer de standaard-DHCP-configuratie voor de verbonden gegevenspoort, als u een DHCP-server in uw netwerk hebt. Als dit niet het geval is, geeft u een IP-adres, DNS-server en standaardgateway op. Krijg meer informatie over [Netwerkinstellingen](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#configure-network).

5. **Rekennetwerk configureren**: Maak een virtuele schakelaar door een poort in te schakelen op het apparaat. Voer 2 gratis, aaneengesloten statische IP-adressen voor Kubernetes-knooppunten in hetzelfde netwerk in als waarin u de schakelaar hebt gemaakt. Bied minstens 1 statisch IP-adres voor de IoT Edge Hub-service voor toegang tot rekenmodules, en 1 statisch IP-adres voor elke extra service of container waartoe u toegang wilt vanaf buiten het Kubernetes-cluster. 

    Kubernetes is vereist om containerworkloads te implementeren. Krijg meer informatie over [Instellingen voor rekennetwerk](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network).

6. **Webproxy configureren**: Als u eeen webproxy gebruikt in uw omgeving, voert u het IP-adres van de webproxyserver in `http://<web-proxy-server-FQDN>:<port-id>` in. Stel verificatie in op **Geen**. Krijg meer informatie over [Instellingen voor webproxy](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#configure-web-proxy).

7. **Apparaat configureren**: Voer een apparaatnaam en een DNS-domein in, of accepteer de standaardwaarden. 

8. **Update-server configureren**: Accepteer de standaard-Microsoft Update-server, of geef een WSUS-server (Windows Server Update Services) en het pad naar de server op. 

9. **Tijdinstellingen configureren**: Accepteer de standaardinstellingen voor tijd, of stel de tijdzone, primaire NTP-server en secundaire NTP-server in op het lokale netwerk of als openbare servers.

10. **Certificaten configureren**: Als u de apparaatnaam en/of het DNS-domein hebt gewijzigd, moet u certificaten genereren of certificaten toevoegen om het apparaat te activeren. 

    - Als u niet-productieworkloads wilt testen, gebruikt u de [optie Certificaten genereren](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates). 
    - Als u uw eigen certificaten hebt, inclusief de ondertekeningsketen(s), kunt u [Certificaten toevoegen](azure-stack-edge-gpu-deploy-configure-certificates.md#bring-your-own-certificates) in de juiste indeling. Zorg ervoor dat u eerst de ondertekeningsketen uploadt. Zie [Certificaten maken](azure-stack-edge-gpu-create-certificates-tool.md) en [Certificaten uploaden via de lokale UI](azure-stack-edge-gpu-deploy-configure-certificates.md#bring-your-own-certificates).

11. **Activeren**: De activeringssleutel ophalen 

    1. Ga in de Azure-portal naar uw **Azure Stack Edge-resource > Overzicht > Apparaat installeren > Activeren > Sleutel genereren**. Kopieer de sleutel. 
    1. Ga in de lokale web-UI naar **Aan de slag > Activeren**, en geef de activeringssleutel op. Wanneer de sleutel is toegepast, duurt het enkele minuten voordat het apparaat wordt geactiveerd. Download het `<device-serial-number>`.json-bestand wanneer u wordt gevraagd om apparaatsleutels die nodig zijn voor een toekomstige herstelactie, veilig op te slaan. 

12. **Rekenproces configureren**: Ga in de Azure-portal naar **Overzicht > Apparaat**. Controleer of het apparaat **Online** is. Ga in het linkerdeelvenster naar **Edge-rekenproces > Aan de slag > Edge-rekenproces configureren > Rekenproces**. Geef een bestaande of nieuwe IoT Hub-service op en wacht ongeveer 20 minuten totdat het rekenproces is geconfigureerd. Ga voor meer informatie naar de [Zelfstudie: Rekenproces configureren op Azure Stack Edge Pro GPU-apparaat](azure-stack-edge-gpu-deploy-configure-compute.md)

U bent klaar om rekenworkloads te implementeren op uw apparaat [via IoT Edge](azure-stack-edge-gpu-deploy-sample-module-marketplace.md), [via `kubectl`](azure-stack-edge-gpu-create-kubernetes-cluster.md) of [via Kubernetes met Azure Arc](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md). Als u problemen ondervindt tijdens de installatie, gaat u naar [Problemen met het apparaat](), [problemen met orders](azure-stack-edge-gpu-troubleshoot.md), [certificaatproblemen](azure-stack-edge-gpu-certificate-troubleshooting.md) of Kubernetes-problemen oplossen. 

## <a name="next-steps"></a>Volgende stappen

[Azure Stack Edge Pro GPU installeren](./azure-stack-edge-gpu-deploy-install.md)



