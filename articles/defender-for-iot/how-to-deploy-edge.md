---
title: IoT Edge Security module implementeren
description: Meer informatie over het implementeren van een Defender voor IoT-beveiligings agent op IoT Edge.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2020
ms.author: mlottner
ms.openlocfilehash: c2b440413599ce07112231af17daa0bc14817b76
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2020
ms.locfileid: "97832774"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>Een beveiligings module op uw IoT Edge-apparaat implementeren

De module **Defender voor IOT** biedt een uitgebreide beveiligings oplossing voor uw IOT edge-apparaten.
De beveiligings module verzamelt, aggregeert en analyseert onbewerkte beveiligings gegevens van uw besturings systeem en container systeem in aanbevelingen voor beveiliging en waarschuwingen.
Zie [beveiligings module voor IOT Edge voor](security-edge-architecture.md)meer informatie.

In dit artikel leert u hoe u een beveiligings module implementeert op uw IoT Edge-apparaat.

## <a name="deploy-security-module"></a>Beveiligings module implementeren

Gebruik de volgende stappen om een Defender for IoT-beveiligings module voor IoT Edge te implementeren.

### <a name="prerequisites"></a>Vereisten

1. Zorg ervoor dat uw apparaat is [geregistreerd als een IOT edge-apparaat](../iot-edge/how-to-manual-provision-symmetric-key.md#register-a-new-device)In uw IOT hub.

1. Voor de module Defender voor IoT Edge moet het [gecontroleerde Framework](https://linux.die.net/man/8/auditd) op het IOT edge-apparaat zijn geïnstalleerd.

    - Installeer het Framework door de volgende opdracht uit te voeren op uw IoT Edge-apparaat:

    `sudo apt-get install auditd audispd-plugins`

    - Controleer of controle actief is door de volgende opdracht uit te voeren:

    `sudo systemctl status auditd`<br>
    - Het verwachte antwoord is: `active (running)`

### <a name="deployment-using-azure-portal"></a>Implementatie met behulp van Azure Portal

1. Open **Marketplace** vanuit het Azure Portal.

1. Selecteer **Internet of Things**, zoek vervolgens naar **Defender voor IOT** en selecteer deze.

   :::image type="content" source="media/howto/edge-onboarding-8.png" alt-text="Selecteer Defender voor IoT":::

1. Klik op **maken** om de implementatie te configureren.

1. Kies het Azure- **abonnement** van uw IOT hub en selecteer vervolgens uw **IOT hub**.<br>Selecteer **implementeren op een apparaat** om één apparaat te richten of selecteer **implementeren op schaal** om meerdere apparaten te richten en klik op **maken**. Zie [How to deploy](../iot-edge/how-to-deploy-at-scale.md)(Engelstalig) voor meer informatie over de implementatie op schaal.

    >[!Note]
    >Als u **implementeren op schaal** hebt geselecteerd, voegt u de naam van het apparaat en de details toe voordat u doorgaat met het tabblad **modules toevoegen** in de volgende instructies.

Voltooi elke stap om uw IoT Edge-implementatie voor Defender voor IoT te volt ooien.

#### <a name="step-1-modules"></a>Stap 1: modules

1. Selecteer de **AzureSecurityCenterforIoT** -module.
1. Wijzig op het tabblad **module-instellingen** de **naam** in **azureiotsecurity**.
1. Voeg, indien nodig, een variabele toe op het tabblad **omgevings variabelen** (bijvoorbeeld debug-niveau).
1. Voeg op het tabblad Opties voor het maken van de **container** de volgende configuratie toe:

    ``` json
    {
        "NetworkingConfig": {
            "EndpointsConfig": {
                "host": {}
            }
        },
        "HostConfig": {
            "Privileged": true,
            "NetworkMode": "host",
            "PidMode": "host",
            "Binds": [
                "/:/host"
            ]
        }
    }
    ```

1. Voeg op het tabblad **dubbele instellingen** voor de module de volgende configuratie toe:

   Module dubbele eigenschap:
   
   ``` json
     "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration"
   ```

   Inhoud module dubbele eigenschap: 

   ```json
     {

     }
   ```
    
   Zie [beveiligings agenten configureren](./how-to-agent-configuration.md)voor meer informatie over het configureren van de agent.

1. Selecteer **Bijwerken**.

#### <a name="step-2-runtime-settings"></a>Stap 2: runtime-instellingen

1. Selecteer **runtime-instellingen**.
1. Wijzig onder **Edge hub** de **afbeelding** in **MCR.Microsoft.com/azureiotedge-hub:1.0.8.3**.
1. Controleer of de **Create-opties** zijn ingesteld op de volgende configuratie:

    ``` json
    {
       "HostConfig":{
          "PortBindings":{
             "8883/tcp":[
                {
                   "HostPort":"8883"
                }
             ],
             "443/tcp":[
                {
                   "HostPort":"443"
                }
             ],
             "5671/tcp":[
                {
                   "HostPort":"5671"
                }
             ]
          }
       }
    }
    ```

1. Selecteer **Opslaan**.

1. Selecteer **Next**.

#### <a name="step-3-specify-routes"></a>Stap 3: routes opgeven

1. Controleer op het tabblad **routes opgeven** of u een route (expliciet of impliciet) hebt waarmee berichten vanuit de **azureiotsecurity** -module worden doorgestuurd naar **$upstream** volgens de volgende voor beelden. Selecteer **volgende** wanneer de route is ingesteld.

   Voorbeeld routes:

    ```Default implicit route
    "route": "FROM /messages/* INTO $upstream"
    ```

    ```Explicit route
    "ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream"
    ```

1. Selecteer **Next**.

#### <a name="step-4-review-deployment"></a>Stap 4: de implementatie controleren

- Controleer uw implementatie gegevens op het tabblad **implementatie controleren** en selecteer vervolgens **maken** om de implementatie te volt ooien.

## <a name="diagnostic-steps"></a>Diagnostische stappen

Als er een probleem optreedt, zijn container Logboeken de beste manier om meer te weten te komen over de status van een IoT Edge Security module-apparaat. Gebruik de opdrachten en hulpprogramma's in deze sectie om informatie te verzamelen.

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>Controleer of de vereiste containers zijn geïnstalleerd en werken zoals verwacht

1. Voer de volgende opdracht uit op uw IoT Edge-apparaat:

    `sudo docker ps`

1. Controleer of de volgende containers worden uitgevoerd:

   | Naam | AFBEELDING |
   | --- | --- |
   | azureiotsecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:1.0.2 |
   | edgeHub | mcr.microsoft.com/azureiotedge-hub:1.0.8.3 |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0.1 |

   Als de mini maal vereiste containers niet aanwezig zijn, controleert u of uw IoT Edge-implementatie manifest is afgestemd op de aanbevolen instellingen. Zie [IOT Edge module implementeren](#deployment-using-azure-portal)voor meer informatie.

### <a name="inspect-the-module-logs-for-errors"></a>De module Logboeken controleren op fouten

1. Voer de volgende opdracht uit op uw IoT Edge-apparaat:

   `sudo docker logs azureiotsecurity`

1. Voor uitgebreidere logboeken voegt u de volgende omgevings variabele toe aan de implementatie van de **azureiotsecurity** -module: `logLevel=Debug` .

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over configuratie opties gaat u naar de hand leiding voor het configureren van de module.
> [!div class="nextstepaction"]
> [Hand leiding voor module configuratie](./how-to-agent-configuration.md)