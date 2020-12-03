---
title: Zelfstudie voor het filteren en analyseren van gegevens met rekenproces voor GPU van Azure Stack Edge Pro | Microsoft Docs
description: Leer hoe u een compute-rol configureert in Azure Stack Edge Pro GPU en deze gebruikt om gegevens te transformeren voordat ze naar Azure worden verzonden.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/28/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 711da24b3edf08f4867109d0d70165955236c39a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184651"
---
# <a name="tutorial-configure-compute-on-azure-stack-edge-pro-gpu-device"></a>Zelfstudie: Compute configureren op Azure Stack Edge Pro GPU-apparaat

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

In deze zelfstudie wordt beschreven hoe u een compute-rol voor een configureert en een Kubernetes-cluster maakt op uw Azure Stack Edge Pro-apparaat. 

Deze procedure neemt ongeveer 20 tot 30 minuten in beslag.


In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Rekenproces configureren
> * Kubernetes-eindpunten ophalen

 
## <a name="prerequisites"></a>Vereisten

Zorg dat aan deze voorwaarde wordt voldaan voordat u een rekenprocesrol configureert op uw Azure Stack Edge Pro-apparaat:

- U hebt het Azure Stack Edge Pro-apparaat geactiveerd zoals beschreven in [Azure Stack Edge Pro activeren](azure-stack-edge-gpu-deploy-activate.md).
- Zorg ervoor dat u de instructies in [Rekennetwerk inschakelen](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network) hebt gevolgd en:
    - Een netwerkinterface hebt ingeschakeld voor berekeningen.
    - IP-adressen van Kubernetes-knooppunten en van externe Kubernetes-services hebt toegewezen.

## <a name="configure-compute"></a>Rekenproces configureren

Als u het rekenproces wilt configureren voor uw Azure Stack Edge Pro, maakt u een IoT Hub-resource via de Azure-portal.

1. Ga in Azure Portal van uw Azure Stack Edge-resource naar **Overzicht**. Selecteer in het rechterdeelvenster op de tegel **Compute** **Aan de slag**.

    ![Aan de slag met rekenproces](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-1.png)

2. Selecteer op de tegel **Het Edge-rekenproces configureren** **Rekenproces configureren**.

    ![Rekenproces configureren](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-2.png)

3. Voer op de blade **Het Edge-rekenproces configureren** het volgende in:

   
    |Veld  |Waarde  |
    |---------|---------|
    |IoT Hub     | Kies uit **Nieuwe** of **Bestaande**. <br> Standaard wordt er een standaard-laag (S1) gebruikt voor het maken van een IoT-resource. Als u een IoT-resource in een gratis laag wilt gebruiken, maakt u er een en selecteert u vervolgens de bestaande resource. <br> In elk geval gebruikt de IoT Hub-resource hetzelfde abonnement en dezelfde resourcegroep die wordt gebruikt door de resource Azure Stack Edge.     |
    |Naam     |Voer een naam in voor uw IoT Hub-resource.         |

    ![Aan de slag met rekenproces 2](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-3.png)

4. Selecteer **Maken**. Het maken van de IoT Hub-resource duurt enkele minuten. Nadat de IoT Hub-resource is gemaakt, wordt de tegel **Rekenproces configureren** bijgewerkt om de configuratie van het rekenproces weer te geven. 

    ![Aan de slag met rekenproces 3](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-4.png)

5. Als u wilt bevestigen dat de Edge-rekenprocesrol is geconfigureerd, selecteert u **Rekenproces weergeven** op de tegel **Rekenproces configureren**.
    
    ![Aan de slag met rekenproces 4](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-5.png)

    > [!NOTE]
    > Als het dialoogvenster **Rekenproces configureren** wordt gesloten voordat de IoT Hub aan het Azure Stack Edge Pro-apparaat is gekoppeld, wordt de IoT Hub gemaakt, maar wordt deze niet weergegeven in de rekenprocesconfiguratie. 
    
Wanneer de Edge-rekenprocesrol wordt geconfigureerd op het Edge-apparaat, worden er twee apparaten aangemaakt: een IoT-apparaat en een IoT Edge-apparaat. Beide apparaten kunnen worden weergegeven in de IoT Hub-resource. Er wordt ook een IoT Edge-runtime op dit IoT Edge-apparaat uitgevoerd. Op dit moment is alleen het Linux-platform beschikbaar voor uw IoT Edge-apparaat.

Het kan 20-30 minuten duren om het rekenproces te configureren aangezien er op de achtergrond virtuele machines en een Kubernetes-cluster worden gemaakt. 

Nadat u het rekenproces in de Azure-portal hebt geconfigureerd, beschikt u over een Kubernetes-cluster en een standaardgebruiker die is gekoppeld aan de IoT-naamruimte (een naamruimte van het systeem die wordt beheerd door Azure Stack Edge Pro). 

## <a name="get-kubernetes-endpoints"></a>Kubernetes-eindpunten ophalen

Als u een client wilt configureren voor toegang tot het Kubernetes-cluster, hebt u het Kubernetes-eindpunt nodig. Volg deze stappen om het Kubernetes API-eindpunt op te halen uit de lokale gebruikersinterface van uw Azure Stack Edge Pro-apparaat.

1. Ga in de lokale webinterface van uw apparaat naar de pagina **Apparaten**.
2. Kopieer het eindpunt van de **Kubernetes API-service** onder **Apparaateindpunten**. Dit eindpunt is een tekenreeks met de volgende indeling: `https://compute.<device-name>.<DNS-domain>[Kubernetes-cluster-IP-address]`. 

    ![Pagina Apparaat in lokale webinterface](./media/azure-stack-edge-j-series-create-kubernetes-cluster/device-kubernetes-endpoint-1.png)

3. Sla de tekenreeks met het eindpunt op. U gebruikt deze later bij het configureren van een client voor toegang tot het Kubernetes-cluster via kubectl.

4. Als u zich in de lokale webinterface bevindt, kunt u het volgende doen:

    - Ga naar de Kubernetes-API, selecteer **Geavanceerde instellingen** en down load een geavanceerd configuratiebestand voor Kubernetes. 

        ![Pagina Apparaat in lokale webinterface 1](./media/azure-stack-edge-gpu-deploy-configure-compute/download-advanced-config-1.png)

        Als u een sleutel van Microsoft hebt ontvangen (dit is mogelijk voor bepaalde gebruikers), kunt u dit configuratiebestand gebruiken.

        ![Pagina Apparaat in lokale webinterface 2](./media/azure-stack-edge-gpu-deploy-configure-compute/download-advanced-config-2.png)

    - U kunt ook naar het eindpunt van het **Kubernetes-dashboard** gaan en een `aseuser`-configuratiebestand downloaden. 
    
        ![Pagina Apparaat in lokale webinterface 3](./media/azure-stack-edge-gpu-deploy-configure-compute/download-aseuser-config-1.png)

        U kunt dit configuratiebestand gebruiken om u aan te melden bij het Kubernetes-dashboard of om problemen met het Kubernetes-cluster op te lossen. Zie [Toegang tot Kubernetes-dashboard](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#access-dashboard)voor meer informatie. 


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Rekenproces configureren
> * Kubernetes-eindpunten ophalen


Als u wilt weten hoe u uw Azure Stack Edge Pro-apparaat beheert, gaat u naar:

> [!div class="nextstepaction"]
> [De lokale webgebruikersinterface gebruiken voor het beheren van een Azure Stack Edge Pro](azure-stack-edge-manage-access-power-connectivity-mode.md)
