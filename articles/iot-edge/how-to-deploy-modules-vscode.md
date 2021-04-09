---
title: Modules implementeren vanuit Visual Studio code-Azure IoT Edge
description: Gebruik Visual Studio code met de Azure IoT-Hulpprogram Ma's om een IoT Edge module van uw IoT Hub naar uw IoT Edge-apparaat te pushen, zoals geconfigureerd door een implementatie manifest.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.reviewer: ''
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b4840b36d5dadc14bbd664b844e3bcce6f6fec4d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201679"
---
# <a name="deploy-azure-iot-edge-modules-from-visual-studio-code"></a>Azure IoT Edge-modules implementeren vanuit Visual Studio code

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Wanneer u IoT Edge modules met uw bedrijfs logica hebt gemaakt, wilt u deze implementeren op uw apparaten zodat ze aan de rand kunnen worden uitgevoerd. Als u meerdere modules hebt die samen werken om gegevens te verzamelen en te verwerken, kunt u ze allemaal tegelijk implementeren en de routerings regels declareren waarmee ze verbinding maken.

In dit artikel wordt beschreven hoe u een JSON-implementatie manifest maakt en dat bestand gebruikt om de implementatie naar een IoT Edge apparaat te pushen. Voor informatie over het maken van een implementatie die is gericht op meerdere apparaten op basis van hun gedeelde labels, raadpleegt [u IOT Edge modules implementeren op schaal met behulp van Visual Studio code](how-to-deploy-vscode-at-scale.md).

## <a name="prerequisites"></a>Vereisten

* Een [IOT-hub](../iot-hub/iot-hub-create-through-portal.md) in uw Azure-abonnement.
* Een IoT Edge apparaat

  Als u geen IoT Edge apparaat hebt ingesteld, kunt u er een maken in een virtuele Azure-machine. Volg de stappen in een van de Quick Start-artikelen voor het [maken van een virtueel Linux-apparaat](quickstart-linux.md) of [het maken van een virtueel Windows-apparaat](quickstart.md).

* [Visual Studio Code](https://code.visualstudio.com/).
* [Azure IOT-Hulpprogram ma's](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) voor Visual Studio code.

## <a name="configure-a-deployment-manifest"></a>Een implementatie manifest configureren

Een implementatie manifest is een JSON-document waarin wordt beschreven welke modules moeten worden geïmplementeerd, hoe gegevens stromen tussen de modules en gewenste eigenschappen van de module apparaatdubbels. Zie [begrijpen hoe IOT Edge modules kunnen worden gebruikt, geconfigureerd en opnieuw worden gebruikt](module-composition.md)voor meer informatie over hoe implementatie manifesten werken en hoe u ze kunt maken.

Als u modules wilt implementeren met Visual Studio code, slaat u het implementatie manifest lokaal op als een. JSON-bestand. Wanneer u de opdracht uitvoert om de configuratie toe te passen op uw apparaat, gebruikt u het bestandspad in de volgende sectie.

Hier volgt een basis implementatie manifest met een module als voor beeld:

>[!NOTE]
>In dit voor beeld-implementatie manifest wordt schema versie 1,1 gebruikt voor de IoT Edge agent en hub. Schema versie 1,1 is uitgebracht samen met IoT Edge versie 1.0.10 en biedt functies als opstart volgorde voor modules en prioriteiten voor route ring.

   ```json
   {
     "modulesContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.1",
           "runtime": {
             "type": "docker",
             "settings": {
               "minDockerVersion": "v1.25",
               "loggingOptions": "",
               "registryCredentials": {}
             }
           },
           "systemModules": {
             "edgeAgent": {
               "type": "docker",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-agent:1.1",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
                 "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
               }
             }
           },
           "modules": {
             "SimulatedTemperatureSensor": {
               "version": "1.0",
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                 "createOptions": "{}"
               }
             }
           }
         }
       },
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.1",
           "routes": {
               "route": "FROM /messages/* INTO $upstream"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       },
       "SimulatedTemperatureSensor": {
         "properties.desired": {}
       }
     }
   }
   ```

## <a name="sign-in-to-access-your-iot-hub"></a>Meld u aan voor toegang tot uw IoT-hub

U kunt de Azure IoT-uitbrei dingen voor Visual Studio code gebruiken om bewerkingen uit te voeren met uw IoT-hub. Als u deze bewerkingen wilt gebruiken, moet u zich aanmelden bij uw Azure-account en de IoT-hub selecteren waaraan u werkt.

1. Open in Visual Studio code de weer gave **Explorer** .

1. Vouw de sectie **Azure-IOT hub** aan de onderkant van de Explorer uit.

   ![De sectie Azure-IoT Hub uitvouwen](./media/how-to-deploy-modules-vscode/azure-iot-hub-devices.png)

1. Klik in de koptekst van de Azure- **IOT hub** op de sectie **..** .. Als u het weglatings teken niet ziet, beweegt u de muis aanwijzer over de koptekst.

1. Kies **IOT hub selecteren**.

1. Als u niet bent aangemeld bij uw Azure-account, volgt u de aanwijzingen om dit te doen.

1. Selecteer uw Azure-abonnement.

1. Selecteer uw IoT-hub.

## <a name="deploy-to-your-device"></a>Uw apparaat implementeren

U implementeert modules op uw apparaat door het implementatie manifest toe te passen dat u met de module gegevens hebt geconfigureerd.

1. Vouw in de weer gave Visual Studio code Explorer het gedeelte **Azure IOT hub** uit en vouw vervolgens het knoop punt **apparaten** uit.

1. Klik met de rechter muisknop op het IoT Edge apparaat dat u wilt configureren met het implementatie manifest.

    > [!TIP]
    > Als u wilt controleren of het apparaat dat u hebt gekozen een IoT Edge apparaat is, selecteert u dit om de lijst met modules uit te vouwen en de aanwezigheid van **$edgeHub** en **$edgeAgent** te controleren. Elk IoT Edge apparaat bevat deze twee modules.

1. Selecteer **implementatie maken voor één apparaat**.

1. Navigeer naar het JSON-bestand van het implementatie manifest dat u wilt gebruiken en klik op **Edge-implementatie manifest selecteren**.

   ![Een Edge-implementatie manifest selecteren](./media/how-to-deploy-modules-vscode/select-deployment-manifest.png)

De resultaten van uw implementatie worden afgedrukt in de VS code-uitvoer. Geslaagde implementaties worden binnen enkele minuten toegepast als het doel apparaat actief is en is verbonden met internet.

## <a name="view-modules-on-your-device"></a>Modules op uw apparaat weer geven

Zodra u modules hebt geïmplementeerd op uw apparaat, kunt u deze bekijken in de sectie **Azure IOT hub** . Selecteer de pijl naast uw IoT Edge apparaat om het uit te vouwen. Alle modules die momenteel worden uitgevoerd, worden weer gegeven.

Als u onlangs nieuwe modules op een apparaat hebt geïmplementeerd, houdt u de muis aanwijzer over de sectie **Azure IOT Hub-apparaten** en selecteert u het pictogram Vernieuwen om de weer gave bij te werken.

Klik met de rechter muisknop op de naam van een module om de module te bekijken en te bewerken.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [implementeren en bewaken van IOT Edge modules op schaal met Visual Studio code](how-to-deploy-at-scale.md)
