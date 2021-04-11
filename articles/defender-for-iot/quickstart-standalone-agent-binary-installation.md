---
title: 'Snelstartgids: Defender installeren voor IoT micro agent (preview)'
description: In deze Quick Start leert u hoe u de Defender micro agent kunt installeren en verifiëren.
ms.date: 3/9/2021
ms.topic: quickstart
ms.openlocfilehash: a153b640a1d1e86f9b761817d05fda7d3e47da98
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384405"
---
# <a name="quickstart-install-defender-for-iot-micro-agent-preview"></a>Snelstartgids: Defender installeren voor IoT micro agent (preview)

Dit artikel bevat een uitleg over het installeren en verifiëren van de Defender micro agent.

## <a name="prerequisites"></a>Vereisten

Voordat u de Defender voor IoT-module installeert, moet u een module-ID maken in de IoT Hub. Zie [een Defender IOT micro Agent-module maken (preview)](quickstart-create-micro-agent-module-twin.md)voor meer informatie over het maken van een module-identiteit.

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

- Module-identiteits connection string. 

- Certificaat.

### <a name="authenticate-using-a-module-identity-connection-string"></a>Verifiëren met behulp van een module-id connection string

Zorg ervoor dat aan de [vereisten](#prerequisites) voor dit artikel wordt voldaan en dat u een module-identiteit maakt voordat u deze stappen uitvoert. 

#### <a name="get-the-module-identity-connection-string"></a>De module-identiteit ophalen connection string

De module-identiteits connection string ophalen uit de IoT Hub: 

1. Ga naar het IoT Hub en selecteer uw hub.

1. Selecteer in het menu links, onder de sectie **Explorers** , IOT- **apparaten**.

   :::image type="content" source="media/quickstart-standalone-agent-binary-installation/iot-devices.png" alt-text="Selecteer IoT-apparaten in het menu aan de linkerkant.":::

1. Selecteer een apparaat in de lijst apparaat-ID om de **detail** pagina van het apparaat weer te geven.

1. Selecteer het tabblad **module-identiteiten**   en selecteer vervolgens de module **DefenderIotMicroAgent**   in de lijst met module-identiteiten die zijn gekoppeld aan het apparaat.

   :::image type="content" source="media/quickstart-standalone-agent-binary-installation/module-identities.png" alt-text="Selecteer het tabblad module-identiteiten.":::

1. Kopieer de primaire sleutel op de pagina **id-Details van module** door de knop **kopiëren** te selecteren.

   :::image type="content" source="media/quickstart-standalone-agent-binary-installation/copy-button.png" alt-text="Selecteer de Kopieer knop om de primaire sleutel te kopiëren.":::

#### <a name="configure-authentication-using-a-module-identity-connection-string"></a>Verificatie configureren met behulp van een module-id connection string

De agent configureren om te verifiëren met behulp van een module-id connection string:

1. Plaats een bestand `connection_string.txt` met de naam met de Connection String gecodeerd in UTF-8 in het pad van de map Defender-agent `/var/defender_iot_micro_agent` door de volgende opdracht in te voeren:

    ```azurecli
    sudo bash -c 'echo "<connection string" > /var/defender_iot_micro_agent/connection_string.txt' 
    ```

    De `connection_string.txt` moet zich bevinden op de volgende locatie van het pad `/var/defender_iot_micro_agent/connection_string.txt` .

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

> [!div class="nextstepaction"]
> [De Defender micro agent bouwen op basis van de bron code](quickstart-building-the-defender-micro-agent-from-source.md)
