---
title: De GPU-module implementeren op uw Microsoft Azure Stack Edge Pro-apparaat vanuit Azure Marketplace | Microsoft Docs
description: Hierin wordt beschreven hoe u een IoT-module met GPU-functionaliteit implementeert op uw Azure Stack Edge Pro GPU-apparaat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: e6a58a1f404228564e4e1ac6d77051a747debaa7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102438150"
---
# <a name="deploy-a-gpu-enabled-iot-module-from-azure-marketplace-on-azure-stack-edge-pro-gpu-device"></a>Een IoT-module met GPU-functionaliteit implementeren vanuit Azure Marketplace op Azure Stack Edge Pro GPU-apparaat

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

In dit artikel wordt beschreven hoe u een IoT Edge module voor grafische verwerkings eenheden (GPU) die is ingeschakeld, implementeert vanuit Azure Marketplace op uw Azure Stack Edge Pro-apparaat. 

In dit artikel leert u het volgende:
  - Bereid Azure Stack Edge Pro voor op het uitvoeren van een GPU-module.
  - Down load en implementeer een IoT-module die is ingeschakeld voor GPU vanuit Azure Marketplace.
  - De module-uitvoer bewaken.

## <a name="about-sample-module"></a>Over de voorbeeld module

De GPU-voorbeeld module in dit artikel bevat PyTorch-en tensor flow-benchmark code voor CPU-voor-GPU.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, controleert u of u over het volgende beschikt:

- U hebt toegang tot een met GPU ingeschakelde 1-knoop punt Azure Stack edge-apparaat. Dit apparaat wordt geactiveerd met een resource in Azure. 
- U hebt Compute geconfigureerd op dit apparaat. Volg de stappen in [zelf studie: Configure Compute op uw Azure stack edge-apparaat](azure-stack-edge-gpu-deploy-configure-compute.md).
- De volgende ontwikkel bronnen op een Windows-client:
    - [Visual Studio Code](https://code.visualstudio.com/)  
    - [Azure IOT Edge-extensie voor Visual Studio code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).   


## <a name="get-module-from-azure-marketplace"></a>Module ophalen van Azure Marketplace

1. Blader door alle [apps in azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps).

    ![Door apps bladeren in azure Marketplace](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/browse-apps-marketplace-1.png)

2. Zoek naar **aan de slag met gpu's**.

    ![Voorbeeld module voor GPU zoeken](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/search-gpu-sample-module-1.png)

3. Selecteer **nu downloaden**.

    ![Voorbeeld module ophalen](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/get-sample-module-1.png)

4. Selecteer **door gaan** om de gebruiks voorwaarden en het privacybeleid van de provider te bevestigen. 

    ![Voorbeeld module ophalen 2](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/terms-of-use-1.png)

5. Kies het abonnement dat u hebt gebruikt voor het implementeren van uw Azure Stack Edge Pro-apparaat.

    ![Abonnement selecteren](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/select-subscription-1.png)

6. Voer de naam in van de IoT Hub-service die u hebt gemaakt tijdens het configureren van uw Azure Stack Edge Pro-apparaat. Als u deze IoT Hub-service naam wilt vinden, gaat u naar de Azure Stack Edge-resource die is gekoppeld aan uw apparaat in Azure Portal. 

    1. Ga in de menu opties van het linkerdeel venster naar **Edge services > IOT Edge**. 

        ![Reken configuratie weer geven](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/view-config-1.png)

    1. Ga naar **Eigenschappen**. 

        1. Noteer de IoT Hub-service die is gemaakt tijdens het configureren van de reken kracht op uw Azure Stack Edge Pro-apparaat.
        2. Noteer de naam van het IoT Edge apparaat dat is gemaakt tijdens het configureren van compute. U gebruikt deze naam in de volgende stap.

        ![Edge Compute-configuratie](media/azure-stack-edge-gpu-deploy-sample-module/view-compute-config-1.png)

10. Kies **implementeren op een apparaat**.

11. Voer de naam van het IoT Edge apparaat in of selecteer **apparaat zoeken** om te bladeren tussen de apparaten die zijn geregistreerd bij de hub.

    ![Apparaat zoeken](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/find-device-1.png)

12. Selecteer **maken** om door te gaan met het standaard proces voor het configureren van een implementatie manifest, inclusief het toevoegen van andere modules, indien gewenst. Details voor de nieuwe module, zoals afbeeldings-URI, opties voor maken en gewenste eigenschappen, zijn vooraf gedefinieerd, maar kunnen worden gewijzigd.

    ![Selecteer Maken](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/target-devices-iot-edge-module-1.png)


13. Controleer of de module is geïmplementeerd in uw IoT Hub in de Azure Portal. Selecteer uw apparaat, selecteer **modules instellen** en de module moet worden weer gegeven in de sectie **IOT Edge modules** .

    ![Selecteer 2 maken](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/running-module-iotres-1.png)

## <a name="monitor-the-module"></a>De module bewaken  

1. Voer in het opdrachtenpalet van VS Code de opdracht **Azure IoT Hub: Select IoT Hub** uit.

2. Kies het abonnement en de IoT-hub met het IoT Edge-apparaat dat u wilt configureren. In dit geval selecteert u het abonnement dat is gebruikt om het Azure Stack Edge Pro-apparaat te implementeren en selecteert u het IoT Edge apparaat dat u hebt gemaakt voor uw Azure Stack Edge Pro-apparaat. Dit gebeurt wanneer u Compute configureert via de Azure Portal in de eerdere stappen.

3. Vouw in de VS code Explorer de sectie Azure IoT Hub uit. Onder **apparaten** wordt het IOT edge apparaat weer geven dat overeenkomt met uw Azure stack Edge Pro-apparaat. 

    1. Selecteer dat apparaat, klik met de rechter muisknop en selecteer **controle van ingebouwd gebeurtenis begin punt starten**.
  
        ![Bewaking starten](media/azure-stack-edge-gpu-deploy-sample-module/monitor-builtin-event-endpoint-1.png)  

    2. Ga naar **apparaten > modules** en u ziet dat uw **GPU-module** wordt uitgevoerd.

    3. De VS code-terminal moet ook de IoT Hub gebeurtenissen weer geven als de bewakings uitvoer voor uw Azure Stack Edge Pro-apparaat.

        ![Bewakings uitvoer](media/azure-stack-edge-gpu-deploy-sample-module/monitor-events-output-1.png) 

        U kunt zien dat de tijd die nodig is om dezelfde set bewerkingen uit te voeren (5000 herhalingen van vorm transformatie) door GPU veel minder is dan voor CPU.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over hoe u [GPU kunt configureren voor het gebruik van een module](azure-stack-edge-j-series-configure-gpu-modules.md).
