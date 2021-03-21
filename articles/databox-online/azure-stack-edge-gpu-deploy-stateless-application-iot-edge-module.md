---
title: Kubernetes stateless app implementeren op Azure Stack Edge Pro GPU via IoT Edge module | Microsoft Docs
description: Hierin wordt beschreven hoe u een Kubernetes stateless toepassing implementeert op uw Azure Stack Edge Pro GPU-apparaat met behulp van een IoT Edge module die wordt geopend via een extern IP-adres.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 4eda6eaf69fad6f8600651660ae4ac6223fe8f8c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102438060"
---
# <a name="use-iot-edge-module-to-run-a-kubernetes-stateless-application-on-your-azure-stack-edge-pro-gpu-device"></a>IoT Edge-module gebruiken voor het uitvoeren van een Kubernetes stateless toepassing op uw Azure Stack Edge Pro GPU-apparaat

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In dit artikel wordt beschreven hoe u een IoT Edge module kunt gebruiken om een staatloze toepassing te implementeren op uw Azure Stack Edge Pro-apparaat.

Als u de stateless toepassing wilt implementeren, voert u de volgende stappen uit:

- Zorg ervoor dat de vereisten zijn voltooid voordat u een IoT Edge module implementeert.
- Voeg een IoT Edge module toe om toegang te krijgen tot het Compute-netwerk op uw Azure Stack Edge Pro.
- Controleer of de module toegang heeft tot de ingeschakelde netwerk interface.

In dit artikel wordt uitgelegd hoe u een module webserver-app gebruikt om het scenario te demonstreren.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, hebt u het volgende nodig:

- Een Azure Stack Edge Pro-apparaat. Zorg voor het volgende:

    - De berekenings netwerk instellingen worden geconfigureerd op het apparaat.
    - Het apparaat wordt geactiveerd volgens de stappen in de [zelf studie: Activeer uw apparaat](azure-stack-edge-gpu-deploy-activate.md).
- U hebt de stappen voor het configureren van de **reken** procedure voltooid volgens de [zelf studie: Configureer Compute op uw Azure stack Edge Pro-apparaat](azure-stack-edge-gpu-deploy-configure-compute.md) op uw apparaat. Op uw apparaat moet een IoT Hub resource, een IoT-apparaat en een IoT Edge apparaat zijn geïnstalleerd.


## <a name="add-webserver-app-module"></a>Webserver-app-module toevoegen

Voer de volgende stappen uit om een module webserver-app op uw Azure Stack Edge Pro-apparaat toe te voegen.

1. Ga in de IoT Hub resource die aan uw apparaat is gekoppeld, naar **automatische apparaatbeheer > IOT Edge**.
1. Selecteer en klik op het IoT Edge apparaat dat aan uw Azure Stack Edge Pro-apparaat is gekoppeld. 

    ![IoT Edge apparaat selecteren](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/select-iot-edge-device-1.png)  

1. Selecteer **Modules instellen**. Selecteer in **modules op apparaat instellen** **+ toevoegen** en selecteer vervolgens **IOT Edge module**.

    ![IoT Edge module selecteren](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/select-iot-edge-module-1.png)

1. In de **module IOT Edge toevoegen**:

    1. Geef een **naam** op voor de module webserver-app die u wilt implementeren.
    2. Geef op het tabblad **module-instellingen** een **afbeeldings-URI** op voor de module installatie kopie. Er wordt een module opgehaald die overeenkomt met de gegeven naam en tags. In dit geval `nginx:stable` wordt een stabiele nginx-installatie kopie (gelabeld als stabiel) opgehaald uit de open bare [docker-opslag plaats](https://hub.docker.com/_/nginx/).

        ![IoT Edge module toevoegen](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/set-module-settings-1.png)    

    3. Plak de volgende voorbeeld code op het tabblad Opties voor het **maken van containers** :  

        ```
        {
            "HostConfig": {
                "PortBindings": {
                    "80/tcp": [
                        {
                            "HostPort": "8080"
                        }
                    ]
                }
            }
        }
        ```

        Met deze configuratie kunt u de module openen met behulp van het IP-adres van het Compute-netwerk via *http* op TCP-poort 8080 (met de standaard poort van de webserver 80). Selecteer **Toevoegen**.

        ![Poort gegevens opgeven in de Blade aangepaste module IoT Edge](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/verify-module-status-1.png)

    4. Selecteer **Controleren + maken**. Controleer de module gegevens en selecteer **maken**.

## <a name="verify-module-access"></a>Module toegang controleren

1. Controleer of de module is geïmplementeerd en wordt uitgevoerd. Op het tabblad **modules** moet de runtime status van de module worden **uitgevoerd**.  

    ![Controleer of de module status wordt uitgevoerd](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/verify-module-status-1.png)

1. Als u het externe eind punt van de webserver-app wilt ophalen, [opent u het Kubernetes-dash board](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#access-dashboard). 
1. Filter in het linkerdeel venster van het dash board op **iotedge** -naam ruimte. Ga naar **detectie-en taak verdeling > Services**. Zoek in de lijst met Services het externe eind punt voor de module webserver-app. 

    ![Verbinding maken met de webserver-app op het externe eind punt](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/connect-external-endpoint-1.png)

1. Selecteer het externe eind punt om een nieuw browser venster te openen.

    U ziet dat de webserver-app wordt uitgevoerd.

    ![Verbinding met module via opgegeven poort controleren](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/verify-webserver-app-1.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het beschikbaar maken van een stateful toepassing via een IoT Edge module<!--insert link-->.
