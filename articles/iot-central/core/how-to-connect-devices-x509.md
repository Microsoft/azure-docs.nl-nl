---
title: Apparaten verbinden met X. 509-certificaten in een Azure IoT Central-toepassing
description: Apparaten verbinden met X. 509-certificaten met Node.js apparaat-SDK voor IoT Central toepassing
author: dominicbetts
ms.author: dobett
ms.date: 08/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: bffff099e8df2b944cbef50a074ef625267ed238
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944636"
---
# <a name="how-to-connect-devices-with-x509-certificates-using-nodejs-device-sdk-for-iot-central-application"></a>Apparaten verbinden met X. 509-certificaten met Node.js apparaat-SDK voor IoT Central toepassing

IoT Central ondersteunt zowel Shared Access signatures (SAS) als X. 509-certificaten voor het beveiligen van de communicatie tussen een apparaat en uw toepassing. De zelf studie [een client toepassing maken en verbinden met uw Azure IOT Central-toepassing](./tutorial-connect-device.md) maakt gebruik van SAS. In dit artikel leert u hoe u het code voorbeeld kunt wijzigen om X. 509 te gebruiken.  X. 509-certificaten worden aanbevolen in productie omgevingen. Zie [Get connected to Azure IOT Central](./concepts-get-connected.md)voor meer informatie.

In dit artikel ziet u twee manieren van het gebruik van X. 509: [groeps registraties](how-to-connect-devices-x509.md#use-a-group-enrollment) die doorgaans worden gebruikt in een productie omgeving en [afzonderlijke registraties](how-to-connect-devices-x509.md#use-an-individual-enrollment) die nuttig zijn voor het testen.

## <a name="prerequisites"></a>Vereisten

- Volt ooien van de zelf studie [een client toepassing maken en verbinden met uw Azure IOT Central-toepassing (Java script)](./tutorial-connect-device.md) .
- [Git](https://git-scm.com/download/).
- Down load en Installeer [openssl](https://www.openssl.org/). Als u met Windows werkt, kunt u de binaire bestanden van de [openssl-pagina op sourceforge](https://sourceforge.net/projects/openssl/)gebruiken.

## <a name="use-a-group-enrollment"></a>Een groeps registratie gebruiken

Gebruik X. 509-certificaten met een groeps registratie in een productie omgeving. In een groeps registratie voegt u een basis-of tussenliggend X. 509-certificaat toe aan uw IoT Central-toepassing. Apparaten met Leaf-certificaten die zijn afgeleid van het basis-of het tussenliggende certificaat kunnen verbinding maken met uw toepassing.

## <a name="generate-root-and-device-cert"></a>Basis-en apparaat certificaat genereren

In deze sectie gebruikt u een X. 509-certificaat om een apparaat te verbinden met een certificaat dat is afgeleid van het certificaat van de registratie groep, waarmee verbinding kan worden gemaakt met uw IoT Central-toepassing.

> [!WARNING]
> Deze manier om X. 509-certificaten te genereren, is alleen voor test doeleinden. Voor een productie omgeving moet u uw officiële, veilige mechanisme gebruiken voor het genereren van certificaten.

1. Open een opdrachtprompt. Kloon de GitHub-opslag plaats voor de scripts voor het genereren van certificaten:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node.git
    ```

1. Navigeer naar het script voor certificaat generator en installeer de vereiste pakketten:

    ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    npm install
    ```

1. Een basis certificaat maken en vervolgens een certificaat voor een apparaat afleiden door het script uit te voeren:

    ```cmd/sh
    node create_test_cert.js root mytestrootcert
    node create_test_cert.js device sample-device-01 mytestrootcert
    ```

    > [!TIP]
    > Een apparaat-id mag alleen letters, cijfers en het teken `-` bevatten.

Met deze opdrachten worden drie bestanden voor de hoofdmap en het certificaat van het apparaat geproduceerd

bestandsnaam | gehele
-------- | --------
\<name\>_cert. pem | Het open bare gedeelte van het x509-certificaat
\<name\>_key. pem | De persoonlijke sleutel voor het x509-certificaat
\<name\>_fullchain. pem | De volledige sleutel hanger voor het x509-certificaat.

## <a name="create-a-group-enrollment"></a>Een groeps registratie maken

1. Open uw IoT Central-toepassing en navigeer naar **beheer**  in het linkerdeel venster en selecteer **apparaat-verbinding**.

1. Selecteer **+ registratie groep maken** en maak een nieuwe registratie groep met de naam _MyX509Group_ met een Attestation-type van **certificaten (X. 509)**.

1. Open de registratie groep die u hebt gemaakt en selecteer **beheren primair**.

1. Selecteer Bestands optie en upload het basis certificaat bestand met de naam _mytestrootcert_cert. pem_ dat u eerder hebt gegenereerd:

    ![Certificaat uploaden](./media/how-to-connect-devices-x509/certificate-upload.png)

1. Als u de verificatie wilt volt ooien, genereert u de verificatie code, kopieert u deze en gebruikt u deze om een X. 509-verificatie certificaat te maken bij de opdracht prompt:

    ```cmd/sh
    node create_test_cert.js verification --ca mytestrootcert_cert.pem --key mytestrootcert_key.pem --nonce  {verification-code}
    ```

1. Upload het ondertekende verificatie certificaat _verification_cert. pem_ om de verificatie te volt ooien:

    ![Geverifieerd certificaat](./media/how-to-connect-devices-x509/verified.png)

U kunt nu apparaten verbinden die een X. 509-certificaat hebben dat is afgeleid van dit primaire basis certificaat.

Nadat u de registratie groep hebt opgeslagen, noteert u het ID-bereik.

## <a name="run-sample-device-code"></a>Voorbeeld code van apparaat uitvoeren

1. Kopieer de bestanden **sampleDevice01_key. pem** en **sampleDevice01_cert. pem** naar de map _Azure-IOT-SDK-Node/device/samples/PnP_ die de **simple_thermostat.js** toepassing bevat. U hebt deze toepassing gebruikt toen u de [zelf studie een apparaat verbinden (Java script)](./tutorial-connect-device.md)hebt voltooid.

1. Ga naar de map _Azure-IOT-SDK-Node/device/samples/PnP_ die de **simple_thermostat.js** -toepassing bevat en voer de volgende opdracht uit om het pakket X. 509 te installeren:

    ```cmd/sh
    npm install azure-iot-security-x509 --save
    ```

1. Open het **simple_thermostat.js** -bestand in een tekst editor.

1. Bewerk de `require` instructies zodat deze het volgende bevatten:

    ```javascript
    const fs = require('fs');
    const X509Security = require('azure-iot-security-x509').X509Security;
    ```

1. Voeg de volgende vier regels toe aan de sectie ' DPS-verbindings gegevens ' om de variabele te initialiseren `deviceCert` :

    ```javascript
    const deviceCert = {
      cert: fs.readFileSync(process.env.IOTHUB_DEVICE_X509_CERT).toString(),
      key: fs.readFileSync(process.env.IOTHUB_DEVICE_X509_KEY).toString()
    };
    ```

1. Bewerk de `provisionDevice` functie waarmee de client wordt gemaakt door de eerste regel te vervangen door de volgende:

    ```javascript
    var provSecurityClient = new X509Security(registrationId, deviceCert);
    ```

1. In dezelfde functie wijzigt u de regel waarmee de `deviceConnectionString` variabele als volgt wordt ingesteld:

    ```javascript
    deviceConnectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';x509=true';
    ```

1. Voeg in de `main` functie de volgende regel toe na de regel die aanroept `Client.fromConnectionString` :

    ```javascript
    client.setOptions(deviceCert);
    ```

1. Stel in uw shell-omgeving de volgende twee omgevings variabelen in:

    ```cmd/sh
    set IOTHUB_DEVICE_X509_CERT=sampleDevice01_cert.pem
    set IOTHUB_DEVICE_X509_KEY=sampleDevice01_key.pem
    ```

    > [!TIP]
    > U stelt de andere vereiste omgevings variabelen in wanneer u de zelf studie [een client toepassing maken en verbinden met uw Azure IOT Central-toepassing](./tutorial-connect-device.md) hebt voltooid.

1. Voer het script uit en controleer of het apparaat is ingericht:

    ```cmd/sh
    node simple_thermostat.js
    ```

    U kunt ook controleren of telemetrie wordt weer gegeven op het dash board.

    ![Telemetrie van apparaat controleren](./media/how-to-connect-devices-x509/telemetry.png)

## <a name="use-an-individual-enrollment"></a>Een afzonderlijke inschrijving gebruiken

Gebruik X. 509-certificaten met een afzonderlijke inschrijving om uw apparaat en oplossing te testen. Bij een individuele inschrijving is er geen basis-of tussenliggende X. 509-certificaat in uw IoT Central-toepassing. Apparaten gebruiken een zelfondertekend X. 509-certificaat om verbinding te maken met uw toepassing.

## <a name="generate-self-signed-device-cert"></a>Zelfondertekend certificaat voor een apparaat genereren

In deze sectie gebruikt u een zelfondertekend X. 509-certificaat om apparaten te verbinden voor afzonderlijke registratie, die worden gebruikt voor het inschrijven van één apparaat. Zelfondertekende certificaten zijn alleen voor test doeleinden.

Een zelfondertekend X. 509-apparaat certificaat maken door het script uit te voeren. Zorg ervoor dat u alleen kleine letters en afbreek streepjes gebruikt voor certificaat naam:

  ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    node create_test_cert.js device mytestselfcertprimary
    node create_test_cert.js device mytestselfcertsecondary 
  ```

## <a name="create-individual-enrollment"></a>Afzonderlijke inschrijving maken

1. Selecteer in de Azure IoT Central-toepassing **apparaten** en maak een nieuw apparaat met de **apparaat-id** als _mytestselfcertprimary_ in de sjabloon van het Thermo-apparaat. Noteer het **id-bereik**, u kunt dit later gebruiken.

1. Open het apparaat dat u hebt gemaakt en selecteer **verbinding maken**.

1. Selecteer **afzonderlijke inschrijvingen** als de **Connect-methode** en **certificaten (X. 509)** als mechanisme:

    ![Individuele inschrijving](./media/how-to-connect-devices-x509/individual-device-connect.png)

1. Selecteer de optie bestand onder primair en upload het certificaat bestand met de naam _mytestselfcertprimary_cert. pem_ dat u eerder hebt gegenereerd.

1. Selecteer de optie bestand voor het secundaire certificaat en upload het certificaat bestand met de naam _mytestselfcertsecondary_cert. pem._ Selecteer vervolgens **Opslaan**:

    ![Afzonderlijk registratie certificaat uploaden](./media/how-to-connect-devices-x509/individual-enrollment.png)

Het apparaat is nu ingericht met het X. 509-certificaat.

## <a name="run-a-sample-individual-enrollment-device"></a>Een voor beeld van een afzonderlijk inschrijvings apparaat uitvoeren

1. Kopieer de bestanden _mytestselfcertprimary_key. pem_ en _mytestselfcertprimary_cert. pem_ naar de map _Azure-IOT-SDK-Node/device/samples/PnP_ die de **simple_thermostat.js** toepassing bevat. U hebt deze toepassing gebruikt toen u de [zelf studie een apparaat verbinden (Java script)](./tutorial-connect-device.md)hebt voltooid.

1. Wijzig de omgevings variabelen die u in het bovenstaande voor beeld hebt gebruikt als volgt:

    ```cmd/sh
    set IOTHUB_DEVICE_DPS_DEVICE_ID=mytestselfcertprimary
    set IOTHUB_DEVICE_X509_CERT=mytestselfcertprimary_cert.pem
    set IOTHUB_DEVICE_X509_KEY=mytestselfcertprimary_key.pem
    ```

1. Voer het script uit en controleer of het apparaat is ingericht:

    ```cmd/sh
    node environmentalSensor.js
    ```

    U kunt ook controleren of telemetrie wordt weer gegeven op het dash board.

    ![Individuele telemetrie-inschrijving](./media/how-to-connect-devices-x509/telemetry-primary.png)

U kunt de bovenstaande stappen ook herhalen voor _mytestselfcertsecondary_ -certificaat.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u apparaten verbindt met X. 509-certificaten, is de voorgestelde volgende stap informatie over het [controleren van de connectiviteit van apparaten met behulp van Azure cli](howto-monitor-devices-azure-cli.md)
