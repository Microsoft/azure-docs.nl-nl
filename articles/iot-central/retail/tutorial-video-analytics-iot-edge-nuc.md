---
title: 'Zelfstudie: -een IoT Edge-exemplaar voor videoanalyse maken in Azure IoT Central (Intel NUC)'
description: In deze zelfstudie leert u hoe u een IoT Edge-exemplaar voor videoanalyse kunt maken die kan worden gebruikt met de toepassingssjabloon voor object- en bewegingsdetectie in videoanalyse.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/27/2020
ms.openlocfilehash: 64cdb41540d9750be8664dc60c2b6ceda6c324ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99831923"
---
# <a name="tutorial-create-an-iot-edge-instance-for-video-analytics-intel-nuc"></a>Zelfstudie: Een IoT Edge-exemplaar voor videoanalyse maken (Intel NUC)

Azure IoT Edge is een volledig beheerde service die voorziet in lokale cloudintelligentie door het implementeren en uitvoeren van:

* Aangepaste logica
* Azure-services
* Kunstmatige intelligentie

In IoT Edge worden deze services rechtstreeks uitgevoerd op platformonafhankelijke IoT-apparaten, zodat u uw IoT-oplossing veilig en op schaal kunt uitvoeren in de cloud of offline.

In deze zelfstudie wordt uitgelegd hoe u de IoT Edge-runtime installeert en configureert op een Intel NUC-apparaat.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * IoT Edge bijwerken en configureren
> * Een IoT Edge-gateway instellen
> * Een lokale ONVIF-camera aansluiten op uw Intel NUC-apparaat

## <a name="prerequisites"></a>Vereisten

* Voordat u begint, moet u de vorige zelfstudie [Een toepassing voor livevideoanalyse maken in Azure IoT Central (YOLO v3)](./tutorial-video-analytics-create-app-yolo-v3.md) of [Een videoanalyse maken in Azure IoT Central (OpenVINO&trade;)](tutorial-video-analytics-create-app-openvino.md) voltooien.
* Een apparaat, zoals een Intel NUC, waarop Linux wordt uitgevoerd, waarop docker-containers kunnen worden uitgevoerd en dat voldoende verwerkingskracht heeft om videoanalyse uit te voeren.
* [IoT Edge-runtime is geïnstalleerd](../../iot-edge/how-to-install-iot-edge.md) en wordt uitgevoerd op het apparaat.
* U kunt vanaf uw Windows-computer verbinding maken met het IoT Edge-apparaat. U hebt de [PuTTy SSH-client](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) of een gelijkwaardig hulpprogramma nodig.
* U hebt ook een Azure-abonnement nodig. Als u geen Azure-abonnement hebt, kunt u er gratis een maken via de [Azure-aanmeldingspagina](https://aka.ms/createazuresubscription).

## <a name="configure-the-iot-edge-device"></a>Een IoT Edge-apparaat configureren

Als de runtime van IoT Edge niet is geïnstalleerd op uw Intel NUC-computer, raadpleegt u de instructies in [Azure IoT Edge runtime installeren op op Debian gebaseerde Linux-systemen](../../iot-edge/how-to-install-iot-edge.md).

De IoT Edge-runtime bijwerken:

1. Gebruik het PuTTy-hulpprogramma om verbinding te maken met het IoT Edge-apparaat.

1. Voer de volgende opdrachten uit om de versie van IoT Edge-runtime bij te werken en te controleren. Op het moment van schrijven is de versie 1.0.9:

    ```bash
    sudo apt-get update
    sudo apt-get install libiothsm iotedge
    sudo iotedge --version
    ```

1. Gebruik de volgende opdrachten voor het maken van de mappen die door de implementatie worden gebruikt met de benodigde machtigingen:

    ```bash
    sudo mkdir -p /data/storage
    sudo mkdir -p /data/media
    sudo chmod -R 777 /data
    ```

Het configuratiebestand *state.json* wilt toevoegen aan de map */data/storage* toevoegen op uw IoT Edge apparaat:

1. Gebruik een teksteditor om het bestand *state.json* te openen in de map *Lva-configuration* op uw lokale computer.

1. Werk de tijdelijke aanduidingen `system` en `iotCentral > properties` bij met tekenreekswaarden die uw gatewayapparaat beschrijven. U kunt deze waarden later weergeven in het dashboard van de IoT Central-toepassing.

1. Werk de tijdelijke aanduidingen `iotCentral > appKeys` bij met de waarden die u hebt genoteerd in het bestand *scratchpad.txt* in de vorige zelfstudie. Sla de wijzigingen op.

1. Gebruik het hulpprogramma PuTTY `scp` in een opdrachtprompt om het bestand *state.json* dat u zojuist hebt bewerkt, te kopiëren naar de map */data/storage* op uw IoT Edge-apparaat. In dit voorbeeld wordt `192.168.0.144` gebruikt als voorbeeld-IP-adres. Vervang het door het IP-adres van uw IoT Edge-apparaat:

    ```cmd
    scp state.json YourUserName@192.168.0.144:/data/storage/state.json`
    ```

Als u IoT Edge wilt configureren om uw IoT Central-toepassing te registreren en er verbinding mee te maken:

1. Gebruik het PuTTy-hulpprogramma om verbinding te maken met het IoT Edge-apparaat.

1. Gebruik een teksteditor zoals `nano` om het IoT Edge-bestand config.yaml te openen:

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

    > [!WARNING]
    > YAML-bestanden kunnen geen tabs gebruiken voor inspringing. Gebruik in plaats daarvan twee spaties. Items op het hoogste niveau kunnen geen voorloopspaties bevatten.

1. Schuif omlaag totdat u `# Manual provisioning configuration` ziet. Commentaar bij de volgende drie regels, zoals wordt weergegeven in het volgende fragment:

    ```yaml
    # Manual provisioning configuration
    #provisioning:
    #  source: "manual"
    #  device_connection_string: "temp"
    ```

1. Schuif omlaag totdat u `# DPS symmetric key provisioning configuration` ziet. Verwijder de opmerking bij de volgende acht regels, zoals wordt weergegeven in het volgende fragment:

    ```yaml
    # DPS symmetric key provisioning configuration
    provisioning:
      source: "dps"
      global_endpoint: "https://global.azure-devices-provisioning.net"
      scope_id: "{scope_id}"
      attestation:
        method: "symmetric_key"
        registration_id: "{registration_id}"
        symmetric_key: "{symmetric_key}"
    ```

1. Vervang `{scope_id}` door de **Id-bereik** die u hebt genoteerd in het bestand *scratchpad.txt* in de vorige zelfstudie.

1. Vervang `{registration_id}` door *gateway-001*, het apparaat dat u hebt gemaakt in de vorige zelfstudie.

1. Vervang `{symmetric_key}` door de **primaire sleutel** voor het apparaat **gateway-001** dat u hebt genoteerd in het bestand *scratchpad.txt* in de vorige zelfstudie.

1. Voer de volgende opdracht uit om de IoT Edge-daemon opnieuw te starten:

    ```bash
    sudo systemctl restart iotedge
    ```

1. Voer de volgende opdracht uit om de status van de IoT Edge-modules te controleren:

    ```bash
    iotedge list
    ```

    De uitvoer van de vorige opdracht toont vijf actieve modules. U kunt ook de status van de actieve modules in uw IoT Central-toepassing weergeven.

    > [!TIP]
    > U kunt deze opdracht opnieuw uitvoeren om de status te controleren. Mogelijk moet u wachten totdat alle modules worden uitgevoerd.

Als de IoT Edge-modules niet goed worden gestart, raadpleegt u [Problemen met uw IoT Edge-apparaat oplossen](../../iot-edge/troubleshoot.md).

## <a name="collect-the-rtsp-stream-from-your-camera"></a>De RTSP-stroom van uw camera verzamelen

Identificeer de URL's van de RTSP-stroom voor de camera's die zijn verbonden met uw IoT Edge-apparaat, bijvoorbeeld:

`rtsp://192.168.1.64:554/Streaming/Channels/101/`

> [!TIP]
> Probeer de camerastroom op de IoT Edge-computer weer te geven met een mediaspeler zoals VLC.

## <a name="clean-up-resources"></a>Resources opschonen

Als u klaar bent met de toepassing, kunt u alle resources die u hebt gemaakt, als volgt verwijderen:

1. Ga in de IoT Central-toepassing naar de pagina **Uw toepassing** in de sectie **Beheer**. Selecteer vervolgens **Verwijderen**.
1. Verwijder in Azure Portal de resourcegroep **lva-rg**.
1. Stop op de lokale machine de Docker-container **amp-viewer**.

## <a name="next-steps"></a>Volgende stappen

U hebt nu de IoT Edge-runtime en de LVA-modules geïmplementeerd op het Intel NUC-gatewayapparaat.

Als u de camera's wilt beheren, gaat u verder met de volgende zelfstudie:

> [!div class="nextstepaction"]
> [Een toepassingssjabloon voor object- en bewegingsdetectie in videoanalyse bewaken en beheren](./tutorial-video-analytics-manage.md)