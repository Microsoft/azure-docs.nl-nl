---
title: 'Quickstart: een gesimuleerd TPM-apparaat inrichten voor Azure IoT Hub met Node.js'
description: 'Quickstart: een gesimuleerd TPM-apparaat met de apparaat-SDK voor Node.js maken en inrichten voor Azure IoT Hub Device Provisioning Service (DPS). In deze snelstart wordt gebruikgemaakt van afzonderlijke inschrijvingen.'
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom:
- mvc
- amqp
- mqtt
- devx-track-js
ms.openlocfilehash: 6e7e986f658570553763001afdd58d7bb1880f94
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94968176"
---
# <a name="quickstart-create-and-provision-a-simulated-tpm-device-using-nodejs-device-sdk-for-iot-hub-device-provisioning-service"></a>Quickstart: Een gesimuleerd TPM-apparaat maken en inrichten voor IoT Hub Device Provisioning Service met de apparaat-SDK voor Node.js

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

In deze quickstart maakt u een gesimuleerd IoT-apparaat op uw Windows-computer. Het gesimuleerde apparaat bevat een TPM-simulator als HSM (Hardware Security Module). U gebruikt Node.js-voorbeeldcode voor het apparaat om dit gesimuleerde apparaat te verbinden met uw IoT-hub met behulp van een individuele inschrijving bij de Device Provisioning Service (DPS).

## <a name="prerequisites"></a>Vereisten

- Vertrouwd zijn met de concepten van [inrichten](about-iot-dps.md#provisioning-process).
- U hebt [IoT Hub Device Provisioning Service instellen met Azure Portal](./quick-setup-auto-provision.md) voltooid.
- Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Node.js v4.0+](https://nodejs.org).
- [Git](https://git-scm.com/download/).

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>De omgeving voorbereiden 

1. Zorg ervoor dat [Node.js v4.0 of hoger](https://nodejs.org) is geïnstalleerd op de computer.

1. Zorg ervoor dat `git` op de computer wordt geïnstalleerd en toegevoegd aan de omgevingsvariabelen die voor het opdrachtvenster toegankelijk zijn. Zie [Software Freedom Conservancy's Git client tools](https://git-scm.com/download/) (Git-clienthulpprogramma's van Software Freedom Conservancy) om de nieuwste versie van `git`-hulpprogramma's te installeren, waaronder **Git Bash**, de opdrachtregel-app die u kunt gebruiken voor interactie met de lokale Git-opslagplaats. 


## <a name="simulate-a-tpm-device"></a>TPM-apparaat simuleren

1. Open een opdrachtprompt of Git Bash. Kloon de GitHub-opslagplaats `azure-utpm-c`:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-utpm-c.git --recursive
    ```

1. Ga naar de GitHub-hoofdmap en voer de [TPM](/windows/device-security/tpm/trusted-platform-module-overview)-simulator uit, zodat deze de [HSM](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) wordt van het gesimuleerde apparaat. Deze luistert via een socket op poorten 2321 en 2322. Sluit dit opdrachtvenster niet. De simulator moet actief blijven tot u deze quickstarthandleiding hebt voltooid: 

    ```cmd/sh
    .\azure-utpm-c\tools\tpm_simulator\Simulator.exe
    ```

1. Maak een nieuwe, lege map met de naam **registerdevice**. Maak in de map **registerdevice** een bestand met de naam package.json door achter de opdrachtprompt de volgende opdracht op te geven. Beantwoord alle vragen die door `npm` worden gesteld of accepteer de standaardwaarden als deze voor u geschikt zijn:
   
    ```cmd/sh
    npm init
    ```

1. Installeer de volgende precursorpakketten:

    ```cmd/sh
    npm install node-gyp -g
    npm install ffi -g
    ```

    > [!NOTE]
    > Het installeren van de bovengenoemde pakketten kan problemen met zich meebrengen. U kunt deze oplossen door `npm install --global --production windows-build-tools` uit te voeren via een opdrachtprompt in de modus **Uitvoeren als administrator**. Vervolgens voert u `SET VCTargetsPath=C:\Program Files (x86)\MSBuild\Microsoft.Cpp\v4.0\V140` uit nadat u het pad hebt vervangen door dat van de geïnstalleerde versie. Voer ten slotte de bovenstaande installatieopdrachten opnieuw uit.
    >

1. Installeer de volgende pakketten met de onderdelen die worden gebruikt tijdens de registratie:

   - een beveiligingsclient die werkt met TPM: `azure-iot-security-tpm`
   - een transport voor het apparaat om verbinding te maken met de Device Provisioning Service: `azure-iot-provisioning-device-http` of `azure-iot-provisioning-device-amqp`
   - een client om het transport en de beveiligingsclient te kunnen gebruiken: `azure-iot-provisioning-device`

     Zodra het apparaat is geregistreerd, kunt u de normale IoT Hub Device Client-pakketten gebruiken om het apparaat te verbinden met behulp van de referenties die u tijdens de registratie hebt gekregen. U hebt het volgende nodig:

   - de apparaatclient: `azure-iot-device`
   - een transportmiddel: hetzij `azure-iot-device-amqp`, `azure-iot-device-mqtt` of `azure-iot-device-http`
   - de beveiligingsclient die u al hebt geïnstalleerd: `azure-iot-security-tpm`

     > [!NOTE]
     > De voorbeelden hieronder maken gebruik van de transporten `azure-iot-provisioning-device-http` en `azure-iot-device-mqtt`.
     > 

     U kunt al deze pakketten tegelijk installeren door de volgende opdracht via de opdrachtprompt in de map **registerdevice** uit te voeren:

       ```cmd/sh
       npm install --save azure-iot-device azure-iot-device-mqtt azure-iot-security-tpm azure-iot-provisioning-device-http azure-iot-provisioning-device
       ```

1. Maak met behulp van een teksteditor een nieuw bestand **ExtractDevice.js** in de map **registerdevice**.

1. Voeg de volgende `require`-instructies toe aan het begin van het bestand **ExtractDevice.js**:
   
    ```
    'use strict';

    var tpmSecurity = require('azure-iot-security-tpm');
    var tssJs = require("tss.js");

    var myTpm = new tpmSecurity.TpmSecurityClient(undefined, new tssJs.Tpm(true));
    ```

1. Voeg de volgende functie toe om de methode te implementeren:
   
    ```
    myTpm.getEndorsementKey(function(err, endorsementKey) {
      if (err) {
        console.log('The error returned from get key is: ' + err);
      } else {
        console.log('the endorsement key is: ' + endorsementKey.toString('base64'));
        myTpm.getRegistrationId((getRegistrationIdError, registrationId) => {
          if (getRegistrationIdError) {
            console.log('The error returned from get registration id is: ' + getRegistrationIdError);
          } else {
            console.log('The Registration Id is: ' + registrationId);
            process.exit();
          }
        });
      }
    });
    ```

1. Sla het bestand **ExtractDevice.js** op en sluit het. Voer het voorbeeld uit:

    ```cmd/sh
    node ExtractDevice.js
    ```

1. In het uitvoervenster worden de voor apparaatinschrijving vereiste **_goedkeuringssleutel_** en **_registratie-id_** weergegeven. Noteer deze waarden. 


## <a name="create-a-device-entry"></a>Apparaatvermelding maken

Azure IoT Device Provisioning Service ondersteunt twee typen inschrijvingen:

- [Inschrijvingsgroepen](concepts-service.md#enrollment-group): Wordt gebruikt om meerdere gerelateerde apparaten in te schrijven.
- [Individuele inschrijvingen](concepts-service.md#individual-enrollment): Wordt gebruikt om één apparaat in te schrijven.

In dit artikel worden individuele inschrijvingen gedemonstreerd.

1. Meld u aan bij Azure Portal, selecteer in het linkermenu de knop **Alle resources** en open Device Provisioning Service.

1. Selecteer **Inschrijvingen beheren** in het Device Provisioning Service-menu. Selecteer het tabblad **Individuele inschrijvingen** en selecteer vervolgens de knop **Individuele inschrijving toevoegen** bovenaan. 

1. Voer in het deelvenster **Inschrijving toevoegen** de volgende gegevens in:
   - Selecteer **TPM** als *mechanisme* voor identiteitscontrole.
   - Voer de *registratie-id* en *goedkeuringssleutel* voor het TPM-apparaat in aan de hand van de waarden die u eerder hebt genoteerd.
   - Selecteer een IoT-hub die is gekoppeld aan uw inrichtingsservice.
   - Desgewenst kunt u de volgende informatie verstrekken:
       - Voer een unieke *apparaat-id* in. Vermijd gevoelige gegevens bij het benoemen van uw apparaat. Als u ervoor kiest om er geen op te geven, wordt in plaats daarvan de registratie-id gebruikt om het apparaat te identificeren.
       - Werk de **initiële status van de apparaatdubbel** bij met de gewenste beginconfiguratie voor het apparaat.
   - Klik op de knop **Opslaan** als u klaar bent. 

     ![Gegevens van apparaatinschrijving invoeren in de portalblade](./media/quick-create-simulated-device/enter-device-enrollment.png)  

   Als het apparaat is ingeschreven, wordt de *registratie-id* ervan weergegeven in de lijst onder het tabblad *Individuele inschrijvingen*. 


## <a name="register-the-device"></a>Apparaat registreren

1. Selecteer in Azure Portal de blade **Overzicht** voor uw Device Provisioning-service en noteer de waarden voor **_Globaal apparaateindpunt_** en **_Id-bereik_**.

    ![Device Provisioning Service-eindpuntgegevens uit de portalblade extraheren](./media/quick-create-simulated-device/extract-dps-endpoints.png) 

1. Maak met behulp van een teksteditor een nieuw bestand **RegisterDevice.js** in de map **registerdevice**.

1. Voeg de volgende `require`-instructies toe aan het begin van het bestand **RegisterDevice.js**:
   
    ```
    'use strict';

    var ProvisioningTransport = require('azure-iot-provisioning-device-http').Http;
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var tpmSecurity = require('azure-iot-security-tpm');
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    ```

    > [!NOTE]
    > **Azure IoT SDK for Node.js** ondersteunt aanvullende protocollen, zoals _AMQP_, _AMQP WS_ en _MQTT WS_.  Zie [Device Provisioning Service SDK for Node.js samples](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/device/samples) (Device Provisioning Service-SDK voor Node.js-voorbeelden) voor meer voorbeelden.
    > 

1. Voeg de variabelen **globalDeviceEndpoint** en **idScope** toe en gebruik ze om een **ProvisioningDeviceClient**-exemplaar te maken. Vervang **{globalDeviceEndpoint}** en **{idScope}** door de waarden **_Global Device Endpoint_** en **_ID Scope_** uit **Stap 1**:
   
    ```
    var provisioningHost = '{globalDeviceEndpoint}';
    var idScope = '{idScope}';

    var tssJs = require("tss.js");
    var securityClient = new tpmSecurity.TpmSecurityClient('', new tssJs.Tpm(true));
    // if using non-simulated device, replace the above line with following:
    //var securityClient = new tpmSecurity.TpmSecurityClient();

    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), securityClient);
    ```

1. Voeg de volgende functie toe om de methode in het apparaat te implementeren:
   
    ```
    provisioningClient.register(function(err, result) {
      if (err) {
        console.log("error registering device: " + err);
      } else {
        console.log('registration succeeded');
        console.log('assigned hub=' + result.registrationState.assignedHub);
        console.log('deviceId=' + result.registrationState.deviceId);
        var tpmAuthenticationProvider = tpmSecurity.TpmAuthenticationProvider.fromTpmSecurityClient(result.registrationState.deviceId, result.registrationState.assignedHub, securityClient);
        var hubClient = Client.fromAuthenticationProvider(tpmAuthenticationProvider, iotHubTransport);

        var connectCallback = function (err) {
          if (err) {
            console.error('Could not connect: ' + err.message);
          } else {
            console.log('Client connected');
            var message = new Message('Hello world');
            hubClient.sendEvent(message, printResultFor('send'));
          }
        };

        hubClient.open(connectCallback);

        function printResultFor(op) {
          return function printResult(err, res) {
            if (err) console.log(op + ' error: ' + err.toString());
            if (res) console.log(op + ' status: ' + res.constructor.name);
            process.exit(1);
          };
        }
      }
    });
    ```

1. Sla het bestand **RegisterDevice.js** op en sluit het. Voer het voorbeeld uit:

    ```cmd/sh
    node RegisterDevice.js
    ```

1. De gegevens van uw IoT-hub leest u in de berichten die het opstarten van het apparaat en het verbinding maken met Device Provisioning Service simuleren. Als het gesimuleerde apparaat is ingericht met de IoT-hub die is gekoppeld met de provisioning-service, wordt de apparaat-id weergegeven op de blade **IoT-apparaten** van de hub. 

    ![Apparaat wordt geregistreerd voor de IoT-hub](./media/quick-create-simulated-device/hub-registration.png) 

    Als u de standaardwaarde van de *initiële status van de apparaatdubbel* hebt gewijzigd in de inschrijvingsvermelding voor uw apparaat, kan de gewenste status van de dubbel uit de hub worden gehaald en er dienovereenkomstig naar worden gehandeld. Zie [Understand and use device twins in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) (Apparaatdubbelen begrijpen en gebruiken in IoT Hub) voor meer informatie


## <a name="clean-up-resources"></a>Resources opschonen

Als u wilt blijven doorwerken met het voorbeeld van de apparaatclient en deze beter wilt leren kennen, wis de resources die in deze quickstart zijn gemaakt dan niet. Als u niet wilt doorgaan, gebruikt u de volgende stappen om alle resources te verwijderen die via deze quickstart zijn gemaakt.

1. Sluit het uitvoervenster van het voorbeeld van de apparaatclient op de computer.
1. Sluit het TPM-simulatorvenster op de computer.
1. Selecteer in het linkermenu in Azure Portal **Alle resources** en selecteer uw Device Provisioning Service. Open de blade **Inschrijvingen beheren** voor uw service en selecteer vervolgens het tabblad **Individuele inschrijvingen**. Schakel het selectievakje naast de *Registratie-id* in van het apparaat dat u hebt ingeschreven in deze quickstart. Druk vervolgens op de knop **Verwijderen** bovenaan het deelvenster. 
1. Selecteer in het linkermenu in Azure Portal **Alle resources** en selecteer vervolgens uw IoT-hub. Open de blade **IoT-apparaten** voor uw hub, schakel het selectievakje *DEVICE ID* in van het apparaat dat u hebt geregistreerd in deze quickstart en druk vervolgens bovenaan op de knop **Verwijderen**.


## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een gesimuleerd TPM-apparaat op de computer gemaakt en dit ingericht voor uw IoT-hub met IoT Hub Device Provisioning Service. Als u wilt weten hoe u uw TPM-apparaat programmatisch kunt registreren, gaat u verder met de quickstart voor programmatische registratie van een TPM-apparaat. 

> [!div class="nextstepaction"]
> [Azure-quickstart: TPM-apparaat registreren bij Azure IoT Hub Device Provisioning Service](quick-enroll-device-tpm-node.md)