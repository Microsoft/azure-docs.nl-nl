---
title: 'Zelf studie: de toepassings sjabloon video Analytics-object en bewegings detectie implementeren Azure IoT Central'
description: 'Zelf studie: in deze hand leiding vindt u een overzicht van de stappen voor het implementeren van een Azure IoT Central-toepassing met behulp van de toepassings sjabloon video Analytics-object en Motion-detectie.'
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: f7f1952ab8c98553a447c3ed1955fb556ba48124
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831940"
---
# <a name="tutorial-how-to-deploy-an-iot-central-application-using-the-video-analytics---object-and-motion-detection-application-template"></a>Zelf studie: een IoT Central-toepassing implementeren met behulp van de toepassing video Analytics-object-en bewegings detectie

Zie [object-en bewegings detectie video Analytics-toepassings architectuur](architecture-video-analytics.md)voor een overzicht van de belangrijkste *video analyse-* onderdelen voor het detecteren van objecten en motion-toepassingen.

De volgende video biedt een overzicht van het gebruik van de _toepassings sjabloon video Analytics-object en Motion detectie_ om een IOT Central oplossing te implementeren:

> [!VIDEO https://www.youtube.com/embed/Bo3FziU9bSA]

## <a name="prerequisites"></a>Vereisten

Een Azure-abonnement wordt aanbevolen. U kunt ook een gratis proefversie voor zeven dagen gebruiken. Als u geen Azure-abonnement hebt, kunt u er een maken via de [Azure-registratiepagina](https://aka.ms/createazuresubscription).

## <a name="deploy-the-application"></a>De toepassing implementeren

Voer de volgende stappen uit om een IoT Central-toepassing te implementeren met behulp van de toepassing video Analytics:

1. Voltooi [een video Analytics-toepassing maken in azure IOT Central (YOLO v3)](tutorial-video-analytics-create-app-yolo-v3.md) of de zelf studie [een video analyse maken in azure IOT Central (openwijn &trade; )](tutorial-video-analytics-create-app-openvino.md) voor het volgende:
    - Een Azure Media Services-account maken.
    - Maak de IoT Central toepassing vanuit de sjabloon video Analytics-object-en bewegings detectie toepassing.
    - Een gateway apparaat configureren in de IoT Central-toepassing. Met de gateway kunnen camera apparaten verbinding maken met de toepassing.

1. Voer een [IOT Edge-exemplaar maken voor video Analytics (Linux-VM)](tutorial-video-analytics-iot-edge-vm.md) of de [zelf studie: een IOT Edge instantie voor video Analytics (Intel NUC)-](tutorial-video-analytics-iot-edge-nuc.md) zelf studie maken voor het volgende:
    - Maak een virtuele machine van Azure waarop de Azure IoT Edge-runtime is geïnstalleerd.: bereid de IoT Edge installatie voor om de video Analytics-module te hosten.
    - Verbind het IoT Edge apparaat met uw IoT Central-toepassing.

1. Voltooi de [controle en beheer](tutorial-video-analytics-manage.md) de zelf studie video Analytics-toepassing om het volgende te doen:
    - Voeg camera's voor object-en bewegings detectie toe aan de gateway in uw IoT Central-toepassing.
    - Start de verwerking van de camera.
    - Installeer een lokale media speler om vastgelegde video weer te geven in AMS.
    - Bekijk vastgelegde video waarin gedetecteerde objecten worden weer gegeven.
    - Opruimen.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met de toepassing, kunt u alle resources die u hebt gemaakt als volgt verwijderen:

1. Ga in de IoT Central-toepassing naar de pagina **Uw toepassing** in de sectie **Beheer**. Selecteer vervolgens **Verwijderen**.
1. Verwijder in Azure Portal de resourcegroep **lva-rg**.
1. Stop op de lokale machine de Docker-container **amp-viewer**.

## <a name="next-steps"></a>Volgende stappen

Nu hebt u een overzicht van de stappen voor het implementeren en gebruiken van de toepassing video Analytics, zie

> [!div class="nextstepaction"]
> [Een video Analytics-toepassing maken in Azure IOT Central (YOLO v3)](tutorial-video-analytics-create-app-yolo-v3.md) of

> [!div class="nextstepaction"]
> [Maak een video analyse in Azure IOT Central (openladen &trade; )](tutorial-video-analytics-create-app-openvino.md) om aan de slag te gaan.
