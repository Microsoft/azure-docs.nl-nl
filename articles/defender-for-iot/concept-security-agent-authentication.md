---
title: Verificatie van beveiligings agent (preview-versie)
description: Voer micro agent-verificatie uit met twee mogelijke methoden.
ms.date: 1/20/2021
ms.topic: conceptual
ms.openlocfilehash: 8dd8abaedaaababf4d84330c5bf8cb030bac55bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104779236"
---
# <a name="micro-agent-authentication-methods-preview"></a>Verificatie methoden van micro agent (preview)

Er zijn twee opties voor verificatie met de Defender voor IoT micro agent: 

- Verbindingsreeks 

- Certificaat 

## <a name="authentication-using-a-connection-string"></a>Verificatie met behulp van een connection string 

Als u een connection string wilt gebruiken, moet u een bestand toevoegen dat gebruikmaakt van de connection string gecodeerd in UTF-8 in de map van de Defender-agent in een bestand met de naam `connection_string.txt` . Bijvoorbeeld:

```azurecli
echo “<connection string>” > connection_string.txt 
```

Zodra u dat hebt gedaan, moet u de service opnieuw starten met deze opdracht.

```azurecli
sudo systemctl restart defender-iot-micro-agent.service
``` 

## <a name="authentication-using-a-certificate"></a>Verificatie met behulp van een certificaat 


Verificatie uitvoeren met behulp van een certificaat: 

1. Plaats het door de PEM gecodeerde open bare deel van een certificaat in de map van de Defender-agent, in een bestand met de naam `certificate_public.pem` .
1. Plaats de met PEM gecodeerde persoonlijke sleutel in de map van de Defender-agent in een bestand met de naam `certificate_private.pem` .
1. Plaats de juiste connection string in een bestand met de naam `connection_string.txt` . Bijvoorbeeld:

    ```azurecli
    HostName=<the host name of the iot hub>;DeviceId=<the id of the device>;ModuleId=<the id of the module>;x509=true 
    ```

    Deze actie geeft aan dat de Defender-agent verwacht dat er een certificaat voor verificatie wordt gegeven. 

1. Start de service opnieuw met de volgende code: 

    ```azurecli
    sudo systemctl restart defender-iot-micro-agent.service 
    ```

## <a name="ensure-the-micro-agent-is-running-correctly"></a>Controleer of de micro agent correct wordt uitgevoerd 

1. Voer de volgende opdracht uit: 
    ```azurecli
    systemctl status defender-iot-micro-agent.service 
    ```
1. Controleer of de service stabiel is door te controleren of deze **actief** is en of de uptime van het proces geschikt is: 

    :::image type="content" source="media/concept-security-agent-authentication/active.png" alt-text="Zorg ervoor dat uw service stabiel is door ervoor te zorgen dat deze actief is.":::

## <a name="next-steps"></a>Volgende stappen

Controleer uw [beveiligings-postuur – CIS-benchmark test](concept-security-posture.md).
