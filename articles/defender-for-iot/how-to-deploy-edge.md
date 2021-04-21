---
title: Een IoT Edge implementeren
description: Meer informatie over het implementeren van een Defender for IoT-beveiligingsagent op IoT Edge.
ms.topic: conceptual
ms.date: 04/21/2021
ms.openlocfilehash: 71efb0bb12d1e20f918481a086fd411d3a237e33
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813592"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>Een beveiligingsmodule implementeren op uw IoT Edge apparaat

**De Defender for IoT-module** biedt een uitgebreide beveiligingsoplossing voor uw IoT Edge apparaten.
De beveiligingsmodule verzamelt, aggregeert en analyseert onbewerkte beveiligingsgegevens van uw besturingssysteem en containersysteem in actiebare beveiligingsaanbevelingen en -waarschuwingen.
Zie Beveiligingsmodule voor meer [informatie IoT Edge.](security-edge-architecture.md)

In dit artikel leert u hoe u een beveiligingsmodule implementeert op uw IoT Edge apparaat.

## <a name="deploy-security-module"></a>Beveiligingsmodule implementeren

Gebruik de volgende stappen om een Defender for IoT-beveiligingsmodule te implementeren voor IoT Edge.

### <a name="prerequisites"></a>Vereisten

1. Zorg ervoor IoT Hub uw apparaat is geregistreerd als een IoT Edge [apparaat](../iot-edge/how-to-register-device.md#register-a-new-device).

1. Defender for IoT Edge vereist dat het [AuditD-framework](https://linux.die.net/man/8/auditd) is geïnstalleerd op IoT Edge apparaat.

    - Installeer het framework door de volgende opdracht uit te voeren op IoT Edge apparaat:

    `sudo apt-get install auditd audispd-plugins`

    - Controleer of AuditD actief is door de volgende opdracht uit te voeren:

    `sudo systemctl status auditd`<br>
    - Verwacht antwoord is: `active (running)`

### <a name="deployment-using-azure-portal"></a>Implementatie met behulp van Azure Portal

1. Open marketplace Azure Portal de **Azure Portal.**

1. Selecteer **Internet of Things,** zoek naar **Azure Security Center naar IoT** en selecteer deze.

   :::image type="content" source="media/howto/edge-onboarding-8.png" alt-text="Selecteer Defender for IoT":::

1. Selecteer **Maken om** de implementatie te configureren.

1. Kies het **Azure-abonnement** van uw IoT Hub selecteer vervolgens uw **IoT Hub**.<br>Selecteer **Implementeren op een apparaat om** één apparaat te targeten of selecteer Op schaal **implementeren** om meerdere apparaten te targeten en selecteer **Maken.** Zie Voor meer informatie over het implementeren op schaal [How to deploy](../iot-edge/how-to-deploy-at-scale.md).

    >[!Note]
    >Als u Implementeren op **schaal hebt geselecteerd,** voegt u de apparaatnaam en -details toe voordat u verdergaat met het tabblad **Modules** toevoegen in de volgende instructies.

Voltooi elke stap om uw implementatie IoT Edge Defender for IoT te voltooien.

#### <a name="step-1-modules"></a>Stap 1: Modules

1. Selecteer de **module AzureSecurityCenterforIoT.**
1. Wijzig op **het tabblad** Module-instellingen de **naam** in **azureiotsecurity**.
1. Voeg op het tabblad **Omgevingsvariabelen** indien nodig een  variabele toe (u kunt bijvoorbeeld foutopsporingsniveau toevoegen en instellen op een van de volgende waarden: Fatal, Fout, Waarschuwing of Informatie).
1. Voeg op **het tabblad Opties** voor container maken de volgende configuratie toe:

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

1. Voeg op **het tabblad Module twin Settings** de volgende configuratie toe:

   Eigenschap Module twin:
   
   ``` json
     "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration"
   ```

   Inhoud van eigenschap module twin: 

   ```json
     {

     }
   ```
    
   Zie Beveiligingsagents configureren voor meer informatie over het [configureren van de agent.](./how-to-agent-configuration.md)

1. Selecteer **Update**.

#### <a name="step-2-runtime-settings"></a>Stap 2: Runtime-instellingen

1. Selecteer **Runtime-instellingen.**
2. Wijzig **onder Edge Hub** de afbeelding **in** **mcr.microsoft.com/azureiotedge-hub:1.0.8.3**.

    >[!Note]
    > Momenteel wordt versie 1.0.8.3 of ouder ondersteund.

3. Controleer **of Opties** maken is ingesteld op de volgende configuratie:

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

4. Selecteer **Opslaan**.

5. Selecteer **Next**.

#### <a name="step-3-specify-routes"></a>Stap 3: routes opgeven

1. Zorg ervoor **dat** u op het tabblad Routes opgeven een route (expliciet of impliciet)  hebt die berichten van de **azureiotsecurity-module** doorsturen naar $upstream volgens de volgende voorbeelden. Selecteer Pas wanneer de route is gebruikt, **Volgende.**

   Voorbeeldroutes:

    ```Default implicit route
    "route": "FROM /messages/* INTO $upstream"
    ```

    ```Explicit route
    "ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream"
    ```

1. Selecteer **Next**.

#### <a name="step-4-review-deployment"></a>Stap 4: Implementatie controleren

- Controleer op **het tabblad Implementatie** controleren uw implementatiegegevens en selecteer vervolgens Maken **om** de implementatie te voltooien.

## <a name="diagnostic-steps"></a>Diagnostische stappen

Als u een probleem ondervindt, zijn containerlogboeken de beste manier om meer te weten te komen over de status van een IoT Edge-beveiligingsmoduleapparaat. Gebruik de opdrachten en hulpprogramma's in deze sectie om informatie te verzamelen.

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>Controleer of de vereiste containers zijn geïnstalleerd en werken zoals verwacht

1. Voer de volgende opdracht uit op IoT Edge apparaat:

    `sudo docker ps`

1. Controleer of de volgende containers worden uitgevoerd:

   | Name | AFBEELDING |
   | --- | --- |
   | azureiotsecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:1.0.2 |
   | edgeHub | mcr.microsoft.com/azureiotedge-hub:1.0.8.3 |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0.1 |

   Als de minimaal vereiste containers niet aanwezig zijn, controleert u of uw IoT Edge implementatiemanifest is afgestemd op de aanbevolen instellingen. Zie Deploy [IoT Edge module (Module IoT Edge implementeren) voor meer informatie.](#deployment-using-azure-portal)

### <a name="inspect-the-module-logs-for-errors"></a>De modulelogboeken op fouten controleren

1. Voer de volgende opdracht uit op IoT Edge apparaat:

   `sudo docker logs azureiotsecurity`

1. Voeg voor uitgebreidere logboeken de volgende omgevingsvariabele toe aan de implementatie van **de azureiotsecurity-module:** `logLevel=Debug` .

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over configuratieopties gaat u verder met de handleiding voor moduleconfiguratie.
> [!div class="nextstepaction"]
> [Handleiding voor moduleconfiguratie](./how-to-agent-configuration.md)