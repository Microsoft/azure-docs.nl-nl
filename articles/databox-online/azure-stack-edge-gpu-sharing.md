---
title: Delen van GPU op Azure Stack Edge Pro GPU-apparaat
description: Hierin worden de benaderingen beschreven voor het delen van Gpu's op Azure Stack Edge Pro GPU-apparaat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: ff1c7b79a49b0b659056c89af3c61f28b72ebc50
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105645237"
---
# <a name="gpu-sharing-on-your-azure-stack-edge-pro-gpu-device"></a>GPU delen op uw Azure Stack Edge Pro GPU-apparaat

GPU (graphics processing unit) is een gespecialiseerde processor die is ontworpen om grafische rendering te versnellen. Met Gpu's kunnen veel gegevens tegelijk worden verwerkt, waardoor ze nuttig zijn voor machine learning, video bewerking en gaming toepassingen. Naast CPU voor algemeen gebruik kan uw Azure Stack Edge Pro GPU-apparaten een of twee Nvidia Tesla T4-Gpu's bevatten voor computerintensieve werk belastingen, zoals versneld deprocessoren van hardware. Zie [Nvidia Tesla T4 GPU](https://www.nvidia.com/en-us/data-center/tesla-t4/)(Engelstalig) voor meer informatie.


## <a name="about-gpu-sharing"></a>Over het delen van GPU

Veel machine learning of andere reken werkbelastingen hebben mogelijk geen toegewezen GPU nodig. Gpu's kunnen worden gedeeld en het delen van Gpu's onder containers of VM-workloads helpt het GPU-gebruik te verhogen zonder de prestatie voordelen van GPU aanzienlijk te beïnvloeden.  

## <a name="using-gpu-with-vms"></a>GPU gebruiken met Vm's

Op uw Azure Stack Edge Pro-apparaat kan een GPU niet worden gedeeld bij het implementeren van VM-workloads. Een GPU kan slechts worden toegewezen aan één virtuele machine. Dit betekent dat u slechts één GPU-VM kunt hebben op een apparaat met één GPU en twee virtuele machines op een apparaat dat is uitgerust met twee Gpu's. Er zijn andere factoren die ook moeten worden overwogen wanneer u GPU-Vm's gebruikt op een apparaat met Kubernetes die zijn geconfigureerd voor workloads met containers. Zie [GPU vm's en Kubernetes](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#gpu-vms-and-kubernetes)voor meer informatie.


## <a name="using-gpu-with-containers"></a>GPU gebruiken met containers

Als u gecontainerde werk belastingen implementeert, kan een GPU op meer dan één manier worden gedeeld in de hardware-en software-laag. Met de Tesla T4 GPU op uw Azure Stack Edge Pro-apparaat zijn we beperkt tot het delen van software. Op uw apparaat worden de volgende twee benaderingen voor het delen van software van GPU gebruikt: 

- De eerste benadering omvat het gebruik van omgevings variabelen om het aantal Gpu's op te geven dat gedeeld kan worden. Houd rekening met het volgende voor behoud wanneer u deze aanpak gebruikt:

    - U kunt een of beide of geen Gpu's met deze methode opgeven. Het is niet mogelijk om fractioneel gebruik op te geven.
    - Meerdere modules kunnen worden toegewezen aan één GPU, maar dezelfde module kan niet aan meer dan één GPU worden toegewezen.
    - Met de NVIDIA SMI-uitvoer kunt u het totale GPU-gebruik bekijken, inclusief het geheugen gebruik.
    
    Zie [een IOT Edge module implementeren die gebruikmaakt van GPU](azure-stack-edge-gpu-configure-gpu-modules.md) op uw apparaat voor meer informatie.

- Voor de tweede benadering moet u de service voor meerdere processen inschakelen op uw NVIDIA-Gpu's. MPS is een runtime service waarmee meerdere processen die gebruikmaken van CUDA, gelijktijdig kunnen worden uitgevoerd op één gedeelde GPU. MPS maakt het mogelijk om de kernel-en memcopy-bewerkingen uit verschillende processen op de GPU te laten overlappen om Maxi maal gebruik te maken. Zie [multi-process service](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf)voor meer informatie.

    Houd rekening met het volgende voor behoud wanneer u deze aanpak gebruikt:
    
    - Met MPS kunt u meer vlaggen opgeven in de GPU-implementatie.
    - U kunt fractioneel gebruik via MPS opgeven, waardoor het gebruik van elke toepassing die op het apparaat is geïmplementeerd, wordt beperkt. U kunt het GPU-percentage opgeven dat voor elke app moet worden gebruikt in de `env` sectie van de `deployment.yaml` door de volgende para meter toe te voegen: 

    ```yml
    // Example: application wants to limit gpu percentage to 20%
    
        env:
              - name: CUDA_MPS_ACTIVE_THREAD_PERCENTAGE 
                value: "20"    
    ```

## <a name="gpu-utilization"></a>GPU-gebruik
 
Wanneer u GPU deelt op in containers geplaatste workloads die op uw apparaat zijn geïmplementeerd, kunt u de NVIDIA-SMI (System Management Interface) gebruiken. NVIDIA-SMI is een opdracht regel programma waarmee u NVIDIA GPU-apparaten kunt beheren en bewaken. Zie de [NVIDIA-Systeembeheer interface](https://developer.nvidia.com/nvidia-system-management-interface)voor meer informatie.

Als u het GPU-gebruik wilt weer geven, moet u eerst verbinding maken met de Power shell-interface van het apparaat. Voer de `Get-HcsNvidiaSmi` opdracht uit en Bekijk de NVIDIA SMI-uitvoer. U kunt ook bekijken hoe het GPU-gebruik wordt gewijzigd door MPS in te scha kelen en vervolgens meerdere werk belastingen op het apparaat te implementeren. Zie [enable multi-process service](azure-stack-edge-gpu-connect-powershell-interface.md#enable-multi-process-service-mps)(Engelstalig) voor meer informatie.


## <a name="next-steps"></a>Volgende stappen

- [GPU delen voor Kubernetes-implementaties op uw Azure stack Edge Pro](azure-stack-edge-gpu-deploy-kubernetes-gpu-sharing.md).
- [GPU delen voor IOT-implementaties op uw Azure stack Edge Pro](azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing.md).
