---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/07/2021
ms.author: alkohli
ms.openlocfilehash: c51577882e75facb1d8eb03c7cfab82467c5ec51
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99524801"
---
Als u het rekenproces wilt configureren voor uw Azure Stack Edge Pro, maakt u een IoT Hub-resource via de Azure-portal.

1. Ga in de Azure Portal van uw Azure Stack Edge-resource naar **Overzicht**. Selecteer vervolgens **IoT Edge**.

   ![Aan de slag met rekenproces](./media/azure-stack-edge-gateway-configure-compute/configure-compute-1.png)

2. Selecteer in **IoT Edge-service inschakelen** de optie **Toevoegen**.

   ![Rekenproces configureren](./media/azure-stack-edge-gateway-configure-compute/configure-compute-2.png)

3. Voer op de blade **Het Edge-rekenproces configureren** de volgende informatie in:
   
    |Veld  |Waarde  |
    |---------|---------|
    |Abonnement     |Selecteer een abonnement voor uw IoT Hub-resource. U kunt het abonnement gebruiken dat ook wordt gebruikt door de Azure Stack Edge-resource.         |
    |Resourcegroep     |Selecteer een resourcegroep voor uw IoT Hub-resource. U kunt de resourcegroep gebruiken die ook wordt gebruikt door de Azure Stack Edge-resource.         |
    |IoT Hub     | Kies uit **Nieuwe** of **Bestaande**. <br> Standaard wordt er een standaard-laag (S1) gebruikt voor het maken van een IoT-resource. Als u een IoT-resource in een gratis laag wilt gebruiken, maakt u er een en selecteert u vervolgens de bestaande resource. <br> In elk geval gebruikt de IoT Hub-resource hetzelfde abonnement en dezelfde resourcegroep die wordt gebruikt door de resource Azure Stack Edge.     |
    |Naam     |Accepteer de standaard naam of voer een naam in voor de IoT Hub resource.         |

   ![Aan de slag met rekenproces 2](./media/azure-stack-edge-gateway-configure-compute/configure-compute-3.png)

4. Wanneer u de instellingen hebt voltooid, selecteert u **Bekijken en maken**. Controleer de instellingen voor uw IoT Hub-resource en selecteer **Maken**.

   Het maken van de IoT Hub-resource duurt enkele minuten. Nadat de resource is gemaakt, wordt in het **Overzicht** aangegeven dat de IoT Edge-service nu wordt uitgevoerd.

   ![Aan de slag met rekenproces 3](./media/azure-stack-edge-gateway-configure-compute/configure-compute-4.png)

5. Ga naar **IoT Edge > eigenschappen** om te bevestigen dat de functie Edge Compute is geconfigureerd.

   ![Aan de slag met rekenproces 4](./media/azure-stack-edge-gateway-configure-compute/configure-compute-5.png)

   Wanneer de Edge-rekenprocesrol wordt geconfigureerd op het Edge-apparaat, worden er twee apparaten aangemaakt: een IoT-apparaat en een IoT Edge-apparaat. Beide apparaten kunnen worden weergegeven in de IoT Hub-resource. Er wordt ook een IoT Edge-runtime op dit IoT Edge-apparaat uitgevoerd. Op dit moment is alleen het Linux-platform beschikbaar voor uw IoT Edge-apparaat.

Het kan 20 tot 30 minuten duren om het rekenproces te configureren. Dit komt doordat er op de achtergrond virtuele machines en een Kubernetes-cluster worden gemaakt.

Nadat u het rekenproces in de Azure Portal hebt geconfigureerd, beschikt u over een Kubernetes-cluster en een standaardgebruiker die is gekoppeld aan de IoT-naamruimte (een naamruimte van het systeem die wordt beheerd door Azure Stack Edge Pro).
