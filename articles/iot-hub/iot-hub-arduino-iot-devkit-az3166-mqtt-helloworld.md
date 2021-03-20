---
title: Berichten verzenden naar een MQTT-server met behulp van de Azure MQTT-client bibliotheek
description: Meer informatie over het gebruik van de MQTT-client bibliotheek voor het verzenden van berichten naar een MQTT Broker. Leer ook hoe u uw mXChip IoT DevKit kunt configureren als een MQTT-client.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/02/2018
ms.author: liydu
ms.custom: mqtt
ms.openlocfilehash: fb8bf593568825793a1a205a2955599b16fa78cf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92151752"
---
# <a name="send-messages-to-an-mqtt-server"></a>Berichten verzenden naar een MQTT-server

Internet of Things IoT-systemen worden vaak uitgevoerd met periodieke, slechte kwaliteit of trage Internet verbindingen. MQTT is een M2M-connectiviteits Protocol (machine-to-machine) dat is ontwikkeld met het oog op dergelijke uitdagingen. 

De MQTT-client bibliotheek die hier wordt gebruikt, maakt deel uit van het PAHO-project van de [eclips](https://www.eclipse.org/paho/) . Dit biedt api's voor het gebruik van MQTT via meerdere transport methoden.

## <a name="what-you-learn"></a>Wat u leert

In dit project leert u het volgende:
- Het gebruik van de MQTT-client bibliotheek voor het verzenden van berichten naar een MQTT Broker.
- Uw MXChip IOT DevKit configureren als een MQTT-client.

## <a name="what-you-need"></a>Wat u nodig hebt

De aan de slag- [hand leiding](./iot-hub-arduino-iot-devkit-az3166-get-started.md) volt ooien om:

* Uw DevKit verbonden met Wi-Fi
* De ontwikkelomgeving voorbereiden

## <a name="open-the-project-folder"></a>Open de projectmap

1. Als de DevKit al is verbonden met uw computer, verbreekt u de verbinding.

2. Start VS Code.

3. Verbind de DevKit met uw computer.

## <a name="open-the-mqttclient-sample"></a>Het MQTTClient-voor beeld openen

Vouw de sectie **ARDUINO-voor beelden** aan de linkerkant uit, blader naar **voor beelden voor MXCHIP AZ3166 > MQTT** en selecteer **MQTTClient**. Er wordt een nieuw versus code venster geopend met daarin een projectmap.

> [!NOTE]
> U kunt ook een voor beeld openen vanuit het opdracht palet. Gebruik `Ctrl+Shift+P` (macOS: `Cmd+Shift+P` ) om het opdracht palet te openen, typ **Arduino** en zoek en selecteer vervolgens **Arduino: voor beelden**.

## <a name="build-and-upload-the-arduino-sketch-to-the-devkit"></a>Bouw en upload de Arduino-schets naar de DevKit

Typ `Ctrl+P` (macOS: `Cmd+P` ) om uit te voeren `task device-upload` . Nadat het uploaden is voltooid, wordt de DevKit opnieuw gestart en uitgevoerd.

![Scherm afbeelding toont een opdracht prompt venster dat de Arduino-schets uploadt en uitvoert.](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/device-upload.jpg)

> [!NOTE]
> Het fout bericht ' fout: AZ3166: Unknown package ' kan worden weer gegeven. Deze fout treedt op wanneer de pakket index van het board niet correct wordt vernieuwd. Raadpleeg de [sectie ontwikkeling van de veelgestelde vragen over IOT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development)om deze fout op te lossen.

## <a name="test-the-project"></a>Het project testen

In VS code kunt u deze procedure volgen om de seriële monitor te openen en in te stellen:

1. Klik op het `COM[X]` woord op de status balk om de juiste COM-poort in te stellen met `STMicroelectronics` : ![ scherm opname toont Visual Studio code met COM8 S T Micro Electronics geselecteerd.](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-com-port.jpg)

2. Klik op het pictogram voor de netstroom op de status balk om de seriële monitor te openen: ![ de release-samen vatting en het netplug-pictogram in de status balk.](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-monitor.jpg)
  
3. Klik op de status balk op het nummer waarmee de baud-rate wordt aangeduid en stel deze in op `115200` : ![ scherm afbeelding toont het instellen van de baudrate in Visual Studio code.](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-baud-rate.jpg)

In de seriële monitor worden alle berichten weer gegeven die door de voorbeeld schets worden verzonden. De schets verbindt de DevKit met Wi-Fi. Zodra de Wi-Fi verbinding is geslaagd, stuurt de schets een bericht naar de MQTT-Broker. Daarna verzendt het voor beeld herhaaldelijk twee "iot.eclipse.org"-berichten die respectievelijk gebruikmaken van QoS 0 en QoS 1.

![Scherm afbeelding toont de seriële monitor die de berichten weergeeft die worden verzonden door de schets.](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-output.jpg)

## <a name="problems-and-feedback"></a>Problemen en feedback

Als u problemen ondervindt, raadpleegt u de [Veelgestelde vragen over IOT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) of maakt u verbinding met behulp van de volgende kanalen:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="see-also"></a>Zie ook

* [Een verbinding maken tussen IoT DevKit AZ3166 en Azure IoT Hub in de Cloud](iot-hub-arduino-iot-devkit-az3166-get-started.md)
* [Schud, schud voor een Tweet](iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message.md)

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u uw MXChip IOT DevKit als een MQTT-client configureert en de MQTT-client bibliotheek gebruikt voor het verzenden van berichten naar een MQTT-Broker, volgt u de voorgestelde volgende stap: overzicht van de [Azure IOT-oplossing voor externe controle](/azure/iot-suite/)