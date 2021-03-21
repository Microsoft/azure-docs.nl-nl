---
title: Een GPU-module op Microsoft Azure Stack Edge Pro GPU-apparaat uitvoeren | Microsoft Docs
description: Hierin wordt beschreven hoe u een module op GPU kunt configureren en uitvoeren op een Azure Stack Edge Pro-apparaat via de Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/12/2021
ms.author: alkohli
ms.openlocfilehash: 348ddff56ed61cd608d6b9f28417e7cd4c4e6b13
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563960"
---
# <a name="configure-and-run-a-module-on-gpu-on-azure-stack-edge-pro-device"></a>Een module op GPU op Azure Stack Edge Pro-apparaat configureren en uitvoeren

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Uw Azure Stack Edge Pro-apparaat bevat een of meer GPU (graphics processing unit). GPU's zijn een populaire keuze voor AI-berekeningen omdat ze mogelijkheden voor parallelle verwerking bieden en sneller afbeeldingen weergeven dan CPU's (Central Processing Units). Voor meer informatie over de GPU die zich in uw Azure Stack Edge Pro-apparaat bevindt, gaat u naar de [technische specificaties van het Azure stack Edge Pro-apparaat](azure-stack-edge-gpu-technical-specifications-compliance.md).

In dit artikel wordt beschreven hoe u een module op de GPU op uw Azure Stack Edge Pro-apparaat configureert en uitvoert. In dit artikel gebruikt u een openbaar toegankelijke container module **cijfers** die zijn geschreven voor nvidia T4-gpu's. Deze procedure kan worden gebruikt voor het configureren van andere modules die door NVIDIA worden gepubliceerd voor deze Gpu's.


## <a name="prerequisites"></a>Vereisten

Zorg voordat u begint voor het volgende:

1. U hebt toegang tot een op GPU ingeschakelde apparaat met 1 knoop punt Azure Stack Edge Pro. Dit apparaat wordt geactiveerd met een resource in Azure.  

## <a name="configure-module-to-use-gpu"></a>Module configureren voor het gebruik van GPU

Als u een module wilt configureren voor het gebruik van de GPU op uw Azure Stack Edge Pro-apparaat om een module uit te voeren,<!--Can it be simplified? "To configure a module to be run by the GPU on your Azure Stack Edge Pro device,"?--> Volg deze stappen.

1. Ga in het Azure Portal naar de resource die aan uw apparaat is gekoppeld.

2. Selecteer in **overzicht** **IOT Edge**.

    ![Module configureren voor het gebruik van GPU 1](media/azure-stack-edge-gpu-configure-gpu-modules/configure-compute-1.png)

3. Selecteer in **IoT Edge-service inschakelen** de optie **Toevoegen**.

   ![Module configureren voor het gebruik van GPU 2](media/azure-stack-edge-gpu-configure-gpu-modules/configure-compute-2.png)

4. Voer in **IoT Edge-service maken** de instellingen voor uw IoT Hub-resource in:

   |Veld   |Waarde    |
   |--------|---------|
   |Abonnement      | Het abonnement dat wordt gebruikt door de Azure Stack Edge-resource. |
   |Resourcegroep    | De resourcegroep dat wordt gebruikt door de Azure Stack Edge-resource. |
   |IoT Hub           | Maak een keuze tussen **Nieuwe maken** en **Bestaande gebruiken**. <br> Standaard wordt er een standaard-laag (S1) gebruikt voor het maken van een IoT-resource. Als u een IoT-resource in een gratis laag wilt gebruiken, maakt u er een en selecteert u vervolgens de bestaande resource. <br> In elk geval gebruikt de IoT Hub-resource hetzelfde abonnement en dezelfde resourcegroep die wordt gebruikt door de resource Azure Stack Edge.     |
   |Naam              | Als u de standaardnaam van een nieuwe IoT Hub-resource niet wilt gebruiken, voert u een andere naam in. |

   Wanneer u de instellingen hebt voltooid, selecteert u **Bekijken en maken**. Controleer de instellingen voor uw IoT Hub-resource en selecteer **Maken**.

   ![Aan de slag met rekenproces 2](./media/azure-stack-edge-gpu-configure-gpu-modules/configure-compute-3.png)

   Het maken van de IoT Hub-resource duurt enkele minuten. Nadat de resource is gemaakt, wordt in het **Overzicht** aangegeven dat de IoT Edge-service nu wordt uitgevoerd.

   ![Aan de slag met rekenproces 3](./media/azure-stack-edge-gpu-configure-gpu-modules/configure-compute-4.png)

5. Selecteer **Eigenschappen** om te controleren of de rol van het Edge-rekenproces is geconfigureerd.

   ![Aan de slag met rekenproces 4](./media/azure-stack-edge-gpu-configure-gpu-modules/configure-compute-5.png)

6. Selecteer in **Eigenschappen** de koppeling voor **IOT edge apparaat**.

   ![Module configureren voor het gebruik van GPU 6](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-2.png)

   In het rechterdeel venster ziet u het IoT Edge apparaat dat is gekoppeld aan uw Azure Stack Edge Pro-apparaat. Dit apparaat komt overeen met het IoT Edge apparaat dat u hebt gemaakt bij het maken van de IoT Hub resource.
 
7. Selecteer dit IoT Edge apparaat.

   ![Module configureren voor het gebruik van GPU 7](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-3.png)

8. Selecteer **Modules instellen**.

   ![Module configureren voor het gebruik van GPU 8](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-4.png)

9. Selecteer **+ toevoegen** en selecteer vervolgens **+ IOT Edge module**. 

    ![Module configureren voor het gebruik van GPU 9](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-5.png)

10. Op het tabblad **IOT Edge module toevoegen** :

    1. Geef de **afbeeldings-URI** op. U gebruikt de openbaar beschik bare nvidia-module **cijfers** . 
    
    2. Stel **beleid voor opnieuw opstarten** in op **altijd**.
    
    3. Stel de **gewenste status** in op **actief**.
    
    ![Module configureren voor het gebruik van GPU 10](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-6.png)

11. Geef op het tabblad **omgevings variabelen** de naam op van de variabele en de bijbehorende waarde. 

    1. Gebruik de NVIDIA_VISIBLE_DEVICES om de huidige module één GPU op dit apparaat te laten gebruiken. 

    2. Stel de waarde in op 0 of 1. Met de waarde 0 of 1 zorgt u ervoor dat er ten minste één GPU door het apparaat wordt gebruikt voor deze module. Wanneer u de waarde instelt op 0, 1, betekent dit dat zowel de Gpu's op het apparaat door deze module worden gebruikt.

       ![Module configureren voor het gebruik van GPU 11](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-7.png)

       Voor meer informatie over omgevings variabelen die u met de NVIDIA GPU kunt gebruiken, gaat u naar [NVIDIA container runtime](https://github.com/NVIDIA/nvidia-container-runtime#environment-variables-oci-spec).

    > [!NOTE]
    > Een module kan één of geen Gpu's gebruiken.

12. Voer een naam in voor uw module. Op dit moment kunt u de optie voor het maken van een container opgeven en de dubbele instellingen voor de module wijzigen of als u klaar bent, selecteert u **toevoegen**. 

    ![Module configureren voor het gebruik van GPU 12](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-8.png)

13. Zorg ervoor dat de module wordt uitgevoerd en selecteer **controleren + maken**.

    ![Module configureren voor het gebruik van GPU 13](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-9.png)

14. Op het tabblad **controleren en maken** worden de implementatie opties weer gegeven die u hebt geselecteerd. Controleer de opties en selecteer **maken**.
    
    ![Module configureren voor het gebruik van GPU 14](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-10.png)

15. Noteer de **runtime status** van de module.
    
    ![Module configureren voor het gebruik van GPU 15](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-11.png)

    Het duurt enkele minuten voordat de module is geïmplementeerd. Selecteer **vernieuwen** en controleer of de **runtime status** update **actief** is.

    ![Module configureren voor het gebruik van GPU 16](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-12.png)


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [omgevings variabelen die u met de NVIDIA GPU kunt gebruiken](https://github.com/NVIDIA/nvidia-container-runtime#environment-variables-oci-spec).
