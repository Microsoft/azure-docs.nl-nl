---
title: Zelfstudie voor het filteren en analyseren van gegevens met rekenproces voor GPU van Azure Stack Edge Pro | Microsoft Docs
description: Leer hoe u een compute-rol configureert in Azure Stack Edge Pro GPU en deze gebruikt om gegevens te transformeren voordat ze naar Azure worden verzonden.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: 626c4b192eeeeb8a07130d0a9ecf6754eebbe068
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065681"
---
# <a name="tutorial-configure-compute-on-azure-stack-edge-pro-gpu-device"></a>Zelfstudie: Compute configureren op Azure Stack Edge Pro GPU-apparaat

<!--ALPA WILL VERIFY - [!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

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

[!INCLUDE [configure-compute](../../includes/azure-stack-edge-gateway-configure-compute.md)]

## <a name="get-kubernetes-endpoints"></a>Kubernetes-eindpunten ophalen

Als u een client wilt configureren voor toegang tot het Kubernetes-cluster, hebt u het Kubernetes-eindpunt nodig. Volg deze stappen om het Kubernetes API-eindpunt op te halen uit de lokale gebruikersinterface van uw Azure Stack Edge Pro-apparaat.

1. Ga in de lokale webinterface van uw apparaat naar de pagina **Apparaten**.
2. Kopieer het eindpunt van de **Kubernetes API-service** onder **Apparaateindpunten**. Dit eindpunt is een tekenreeks met de volgende indeling: `https://compute.<device-name>.<DNS-domain>[Kubernetes-cluster-IP-address]`. 

    ![Pagina Apparaat in lokale webinterface](./media/azure-stack-edge-gpu-create-kubernetes-cluster/device-kubernetes-endpoint-1.png)

3. Sla de tekenreeks met het eindpunt op. U gebruikt deze eindpunttekenreeks later, wanneer u een client voor toegang tot het Kubernetes-cluster configureert via kubectl.

4. Als u zich in de lokale webinterface bevindt, kunt u het volgende doen:

    - Ga naar de Kubernetes-API, selecteer **Geavanceerde instellingen** en download een geavanceerd configuratiebestand voor Kubernetes. 

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
