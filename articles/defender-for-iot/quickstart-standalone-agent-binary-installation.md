---
title: Defender voor IoT micro agent installeren
titleSuffix: Azure Defender for IoT
description: Meer informatie over het installeren en verifiëren van de Defender micro agent.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 3/3/2021
ms.topic: quickstart
ms.service: azure
ms.openlocfilehash: ccf28c47e2e1438a141e2497da70d32c1832ddb9
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102120433"
---
# <a name="install-defender-for-iot-micro-agent"></a>Defender voor IoT micro agent installeren 

Dit artikel bevat een uitleg over het installeren en verifiëren van de Defender micro agent.

## <a name="prerequisites"></a>Vereisten

Voordat u de Defender voor IoT-module installeert, moet u een module-ID maken in de IoT Hub. Zie voor meer informatie over het maken van een module-identiteit [een Defender IOT micro Agent-module maken, twee ](quickstart-create-micro-agent-module-twin.md).

## <a name="install-the-package"></a>Het pakket installeren

Installeer en configureer de micro soft-pakket opslagplaats door [deze instructies](/windows-server/administration/linux-package-repository-for-microsoft-software)te volgen. 

Voor Debian 9 bevat de instructies niet de opslag plaats die moet worden toegevoegd. Gebruik de volgende opdrachten om de opslag plaats toe te voegen: 

```azurecli
curl -sSL https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add - 

sudo apt-get install software-properties-common

sudo apt-add-repository https://packages.microsoft.com/debian/9/multiarch/prod

sudo apt-get update
```

Gebruik de volgende opdracht om het pakket voor de Defender-agent te installeren op Debian en op Ubuntu gebaseerde Linux-distributies:

```azurecli
sudo apt-get install defender-iot-micro-agent 
```

## <a name="micro-agent-authentication-methods"></a>Verificatie methoden van micro agent 

De twee opties die worden gebruikt voor het verifiëren van de Defender voor IoT micro agent zijn: 

- Verbindings reeks. 

- Certificaat.

### <a name="authenticate-using-a-connection-string"></a>Verifiëren met een verbindingsreeks

Verificatie met behulp van een connection string:

1. Plaats een bestand `connection_string.txt` met de naam met de Connection String gecodeerd in UTF-8 in het pad van de map Defender-agent `/var/defender_iot_micro_agent` door de volgende opdracht in te voeren:

    ```azurecli
    sudo bash -c 'echo "<connection string" > /var/defender_iot_micro_agent/connection_string.txt' 
    ```

    De `connection_string.txt` moet zich nu bevinden op de volgende locatie van het pad `/var/defender_iot_micro_agent/connection_string.txt` .

1. Start de service opnieuw met de volgende opdracht:  

    ```azurecli
    sudo systemctl restart defender-iot-micro-agent.service 
    ```

### <a name="authenticate-using-a-certificate"></a>Verifiëren met behulp van een certificaat

Verificatie met behulp van een certificaat:

1. Een certificaat aanschaffen door [deze instructies](../iot-hub/iot-hub-security-x509-get-started.md)te volgen.

1. Plaats het PEM-versleutelde open bare deel van het certificaat en de persoonlijke sleutel in de map van de Defender-agent in naar het bestand met de naam `certificate_public.pem` en `certificate_private.pem` . 

1. Plaats de juiste connection string in het `connection_string.txt` bestand. de connection string moet er als volgt uitzien: 

    `HostName=<the host name of the iot hub>;DeviceId=<the id of the device>;ModuleId=<the id of the module>;x509=true` 

    Deze teken reeks waarschuwt de Defender-agent om te verwachten dat er een certificaat voor verificatie wordt gegeven. 

1. Start de service opnieuw met de volgende opdracht:  

    ```azurecli
    sudo systemctl restart defender-iot-micro-agent.service
    ```

### <a name="validate-your-installation"></a>Uw installatie valideren

Uw installatie valideren:

1. Zorg ervoor dat micro agent correct wordt uitgevoerd met de volgende opdracht:  

    ```azurecli
    systemctl status defender-iot-micro-agent.service
    ```
1. Zorg ervoor dat de service stabiel is door ervoor te zorgen dat deze is `active` en dat de uptime van het proces geschikt is.

    :::image type="content" source="media/quickstart-standalone-agent-binary-installation/active-running.png" alt-text="Controleer of uw service stabiel en actief is.":::
 
## <a name="testing-the-system-end-to-end"></a>End-to-end van het systeem testen 

U kunt het systeem vanaf het einde testen door een trigger bestand te maken op het apparaat. Het trigger bestand zorgt ervoor dat de basislijn scan in de agent het bestand detecteert als een basislijn schending. 

Maak een bestand op het bestands systeem met de volgende opdracht:

```azurecli
sudo touch /tmp/DefenderForIoTOSBaselineTrigger.txt 
```
Een aanbeveling voor validatie van de basis lijn wordt uitgevoerd in de hub, met een `CceId` van CIS-Debian-9-DEFENDER_FOR_IOT_TEST_CHECKS-0,0: 

:::image type="content" source="media/quickstart-standalone-agent-binary-installation/validation-failure.png" alt-text="De aanbeveling voor het valideren van de basislijn validatie die optreedt in de hub." lightbox="media/quickstart-standalone-agent-binary-installation/validation-failure-expanded.png":::

Maxi maal één uur toestaan dat de aanbeveling wordt weer gegeven in de hub. 

## <a name="micro-agent-versioning"></a>Micro agent-versies 

Als u een specifieke versie van de Defender IoT micro agent wilt installeren, voert u de volgende opdracht uit: 

```azurecli
sudo apt-get install defender-iot-micro-agent=<version>
```

## <a name="next-steps"></a>Volgende stappen

[De Defender micro agent bouwen op basis van de bron code](quickstart-building-the-defender-micro-agent-from-source.md)