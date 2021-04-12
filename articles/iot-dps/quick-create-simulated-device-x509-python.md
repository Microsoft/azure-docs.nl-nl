---
title: Quickstart - Een gesimuleerd X.509-apparaat inrichten voor Azure IoT Hub met behulp van Python
description: 'Quickstart: Een gesimuleerd X.509-apparaat met de SDK voor Python maken en inrichten voor IoT Hub Device Provisioning Service (DPS). In deze snelstart wordt gebruikgemaakt van afzonderlijke inschrijvingen.'
author: wesmc7777
ms.author: wesmc
ms.date: 01/29/2021
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: python
ms.custom: mvc, devx-track-python
ms.openlocfilehash: 5db02f8ca1f0c311617a787525ee2fa5856eb5dc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105045260"
---
# <a name="quickstart-create-and-provision-a-simulated-x509-device-using-python-device-sdk-for-iot-hub-device-provisioning-service"></a>Quickstart: Een gesimuleerd X.509-apparaat met de SDK voor Python maken en inrichten voor IoT Hub Device Provisioning Service

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

In deze Snelstartgids richt u een ontwikkel machine in als een python X. 509-apparaat. Gebruik de voorbeeld code uit de [Azure IOT PYTHON SDK](https://github.com/Azure/azure-iot-sdk-python) om het apparaat te verbinden met uw IOT-hub. In dit voor beeld wordt een afzonderlijke registratie gebruikt met de Device Provisioning Service (DPS).

## <a name="prerequisites"></a>Vereisten

- Vertrouwd zijn met de concepten van [inrichten](about-iot-dps.md#provisioning-process).
- U hebt [IoT Hub Device Provisioning Service instellen met Azure Portal](./quick-setup-auto-provision.md) voltooid.
- Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Python 3,6 of hoger](https://www.python.org/downloads/)
- [Git](https://git-scm.com/download/).


[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>De omgeving voorbereiden 

1. Zorg ervoor dat `git` op de computer wordt geïnstalleerd en toegevoegd aan de omgevingsvariabelen die voor het opdrachtvenster toegankelijk zijn. Zie [Software Freedom Conservancy's Git client tools](https://git-scm.com/download/) (Git-clienthulpprogramma's van Software Freedom Conservancy) om de nieuwste versie van `git`-hulpprogramma's te installeren, waaronder **Git Bash**, de opdrachtregel-app die u kunt gebruiken voor interactie met de lokale Git-opslagplaats. 

2. Open een Git Bash-prompt. Kloon de GitHub opslag plaats voor [Azure IOT PYTHON SDK](https://github.com/Azure/azure-iot-sdk-python).
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python.git --recursive
    ```


## <a name="create-a-self-signed-x509-device-certificate"></a>Een zelfondertekend X.509-apparaatcertificaat maken 

In deze sectie gaat u een zelfondertekend X. 509-certificaat maken. Het is belangrijk rekening te houden met de volgende punten:

* Zelfondertekende certificaten zijn alleen voor testdoeleinden en moeten niet in productieomgevingen worden gebruikt.
* De standaardvervaltermijn voor een zelfondertekend certificaat is één jaar.

Als u de certificaten van uw apparaat nog niet hebt om een apparaat te verifiëren, kunt u een zelfondertekend certificaat met OpenSSL maken om met dit artikel te testen.  OpenSSL is opgenomen in de Git-installatie. 

1. Voer de volgende opdracht uit in de Git Bash-prompt.

    # <a name="windows"></a>[Windows](#tab/windows)
    
    ```bash
    winpty openssl req -outform PEM -x509 -sha256 -newkey rsa:4096 -keyout ./python-device.key.pem -out ./python-device.pem -days 365 -extensions usr_cert -subj "//CN=Python-device-01"
    ```

    > [!IMPORTANT]
    > De extra slash die is opgegeven voor de onderwerpnaam ( `//CN=Python-device-01` ) is alleen vereist voor het escaperen van de teken reeks met Git op Windows-platforms. 

    # <a name="linux"></a>[Linux](#tab/linux)
    
    ```bash
    openssl req -outform PEM -x509 -sha256 -newkey rsa:4096 -keyout ./python-device.key.pem -out ./python-device.pem -days 365 -extensions usr_cert -subj "/CN=Python-device-01"
    ```
    
    ---
    
2. Wanneer u wordt gevraagd om **PEM wachtwoordzin in te voeren:**, gebruikt u de wachtwoordzin `1234` voor het testen met dit artikel.    

3. Wanneer het opnieuw wordt gevraagd **, voert u de PEM-** wachtwoordzin in:, gebruikt u de wachtwoordzin `1234` opnieuw.    

Een test certificaat bestand (*python-device. pem*) en een bestand met een persoonlijke sleutel (*python-device. key. pem*) worden gegenereerd in de map waarin u de opdracht hebt uitgevoerd `openssl` .


## <a name="create-an-individual-enrollment-entry-in-dps"></a>Een afzonderlijke inschrijvings vermelding maken in DPS


Azure IoT Device Provisioning Service ondersteunt twee typen inschrijvingen:

- [Inschrijvingsgroepen](concepts-service.md#enrollment-group): Wordt gebruikt om meerdere gerelateerde apparaten in te schrijven.
- [Individuele inschrijvingen](concepts-service.md#individual-enrollment): Wordt gebruikt om één apparaat in te schrijven.

In dit artikel ziet u een afzonderlijke inschrijving voor één apparaat dat moet worden ingericht met een IoT-hub.

1. Meld u aan bij Azure Portal, selecteer in het linkermenu de knop **Alle resources** en open uw Provisioning-service.

2. Selecteer **Inschrijvingen beheren** in het Device Provisioning Service-menu. Selecteer het tabblad **Individuele inschrijvingen** en selecteer vervolgens de knop **Individuele inschrijving toevoegen** bovenaan. 

3. Voer in het deelvenster **Inschrijving toevoegen** de volgende gegevens in:
   - Selecteer **X.509** als *mechanisme* voor identiteitscontrole.
   - Kies onder het *primaire certificaat. pem-of. cer-bestand* de optie *Selecteer een bestand* om het certificaat bestand **python-device. pem** te selecteren als u het test certificaat gebruikt dat u eerder hebt gemaakt.
   - Desgewenst kunt u de volgende informatie verstrekken:
     - Selecteer een IoT-hub die is gekoppeld aan uw inrichtingsservice.
     - Werk de **initiële status van de apparaatdubbel** bij met de gewenste beginconfiguratie voor het apparaat.
   - Klik op de knop **Opslaan** als u klaar bent. 

     [![Afzonderlijke inschrijving voor X.509-attestation toevoegen in de portal](./media/python-quick-create-simulated-device-x509/device-enrollment.png)](./media/python-quick-create-simulated-device-x509/device-enrollment.png#lightbox)

   Bij een geslaagde inschrijving wordt uw X. 509-apparaat weer gegeven als **python-apparaat-01** onder de kolom *registratie-id* op het tabblad *afzonderlijke inschrijvingen* . Deze registratie waarde is afkomstig van de onderwerpnaam op het certificaat van het apparaat. 

## <a name="simulate-the-device"></a>Het apparaat simuleren

Het python-voorbereidings voorbeeld, [provision_x509. py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/provision_x509.py) bevindt zich in de `azure-iot-sdk-python/azure-iot-device/samples/async-hub-scenarios` map. In dit voor beeld worden zes omgevings variabelen gebruikt om een IoT-apparaat te verifiëren en in te richten met behulp van DPS. Deze omgevings variabelen zijn:

| Naam van de variabele              | Beschrijving                                     |
| :------------------------- | :---------------------------------------------- |
| `PROVISIONING_HOST`        |  Deze waarde is het globale eind punt dat wordt gebruikt om verbinding te maken met uw DPS-resource |    
| `PROVISIONING_IDSCOPE`     |  Deze waarde is het ID-bereik voor uw DPS-resource |    
| `DPS_X509_REGISTRATION_ID` |  Deze waarde is de ID voor uw apparaat. Het moet ook overeenkomen met de naam van het certificaat van het apparaat |    
| `X509_CERT_FILE`           |  Bestands naam van het apparaat |    
| `X509_KEY_FILE`            |  De naam van de persoonlijke sleutel voor het certificaat van uw apparaat |
| `PASS_PHRASE`              |  De wachtwoordzin die u hebt gebruikt voor het versleutelen van het certificaat en het bestand met de persoonlijke sleutel ( `1234` ). |    

1. Selecteer **Overzicht** in het Device Provisioning Service-menu. Noteer uw _id-bereik_ en het _eind punt van het globale apparaat_.

    ![Service-informatie](./media/python-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. Gebruik in de Git Bash-prompt de volgende opdrachten om de omgevings variabelen toe te voegen voor het eind punt van het globale apparaat en de ID-Scope.

    ```bash
    $export PROVISIONING_HOST=global.azure-devices-provisioning.net
    $export PROVISIONING_IDSCOPE=<ID scope for your DPS resource>
    ```

3. De registratie-ID voor het IoT-apparaat moet overeenkomen met de onderwerpnaam van het certificaat van het apparaat. Als u een zelfondertekend test certificaat hebt gegenereerd, `Python-device-01` zijn de onderwerpnaam en registratie-id voor het apparaat. 

    Als u al een certificaat voor een apparaat hebt, kunt u gebruiken `certutil` om de algemene naam van het onderwerp te controleren die wordt gebruikt voor uw apparaat, zoals hieronder wordt weer gegeven voor een zelfondertekend test certificaat:

    ```bash
    $ certutil python-device.pem
    X509 Certificate:
    Version: 3
    Serial Number: fa33152fe1140dc8
    Signature Algorithm:
        Algorithm ObjectId: 1.2.840.113549.1.1.11 sha256RSA
        Algorithm Parameters:
        05 00
    Issuer:
        CN=Python-device-01
      Name Hash(sha1): 1dd88de40e9501fb64892b698afe12d027011000
      Name Hash(md5): a62c784820daa931b9d3977739b30d12
    
     NotBefore: 1/29/2021 7:05 PM
     NotAfter: 1/29/2022 7:05 PM
    
    Subject:
        ===> CN=Python-device-01 <===
      Name Hash(sha1): 1dd88de40e9501fb64892b698afe12d027011000
      Name Hash(md5): a62c784820daa931b9d3977739b30d12
    ```

    Stel in de Git Bash-prompt de omgevings variabele voor de registratie-ID als volgt in:

    ```bash
    $export DPS_X509_REGISTRATION_ID=Python-device-01
    ```

4. Stel in de Git Bash-prompt de omgevings variabelen voor het certificaat bestand, het persoonlijke sleutel bestand en de wachtwoordzin in.

    ```bash
    $export X509_CERT_FILE=./python-device.pem
    $export X509_KEY_FILE=./python-device.key.pem
    $export PASS_PHRASE=1234
    ```

5. Controleer de code voor [provision_x509. py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/provision_x509.py) als u geen gebruikmaakt van **Python-versie 3,7** of hoger, zorg ervoor dat de [code die hier wordt vermeld](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios#advanced-iot-hub-scenario-samples-for-the-azure-iot-hub-device-sdk) , wordt vervangen `asyncio.run(main())` en sla de wijziging op. 

6. Voet het voorbeeld uit. Het voor beeld wordt verbonden, het apparaat wordt ingericht op een hub en er worden een aantal test berichten verzonden naar de hub.

    ```bash
    $ winpty python azure-iot-sdk-python/azure-iot-device/samples/async-hub-scenarios/provision_x509.py
    RegistrationStage(RequestAndResponseOperation): Op will transition into polling after interval 2.  Setting timer.
    The complete registration result is
    Python-device-01
    TestHub12345.azure-devices.net
    initialAssignment
    null
    Will send telemetry from the provisioned device
    sending message #4
    sending message #7
    sending message #2
    sending message #8
    sending message #5
    sending message #9
    sending message #1
    sending message #6
    sending message #10
    sending message #3
    done sending message #4
    done sending message #7
    done sending message #2
    done sending message #8
    done sending message #5
    done sending message #9
    done sending message #1
    done sending message #6
    done sending message #10
    done sending message #3
    ```

7. Navigeer in de portal naar de IoT-hub die is gekoppeld aan uw provisioning-service en open de Blade **IOT-apparaten** in de sectie **Explorers** in het linkermenu. Wanneer het inrichten van het gesimuleerde X.509-apparaat voor de hub is geslaagd, wordt de apparaat-ID weergegeven op de blade **Device Explorer** met de *STATUS***ingeschakeld**. U moet mogelijk op de knop **Vernieuwen** bovenaan drukken als u de blade vóór het uitvoeren van de voorbeeldapparaattoepassing al hebt geopend. 

    ![Apparaat wordt geregistreerd voor de IoT-hub](./media/python-quick-create-simulated-device-x509/registration.png) 

> [!NOTE]
> Als u de standaardwaarde van de *initiële status van de apparaatdubbel* hebt gewijzigd in de inschrijvingsvermelding voor uw apparaat, kan de gewenste status van de dubbel uit de hub worden gehaald en er dienovereenkomstig naar worden gehandeld. Zie [Apparaatdubbels begrijpen en gebruiken in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) voor meer informatie.
>

## <a name="clean-up-resources"></a>Resources opschonen

Als u wilt blijven doorwerken met het voorbeeld van de apparaatclient en deze beter wilt leren kennen, wis de resources die in deze quickstart zijn gemaakt dan niet. Als u niet wilt doorgaan, gebruikt u de volgende stappen om alle resources te verwijderen die via deze quickstart zijn gemaakt.

1. Sluit het uitvoervenster van het voorbeeld van de apparaatclient op de computer.
2. Selecteer in het linkermenu in Azure Portal **Alle resources** en selecteer uw Device Provisioning Service. Open de blade **Inschrijvingen beheren** voor uw service en selecteer vervolgens het tabblad **Individuele inschrijvingen**. Schakel het selectievakje naast de *Registratie-id* in van het apparaat dat u hebt ingeschreven in deze quickstart. Druk vervolgens op de knop **Verwijderen** bovenaan het deelvenster. 
3. Selecteer in het linkermenu in Azure Portal **Alle resources** en selecteer vervolgens uw IoT-hub. Open de blade **IoT-apparaten** voor uw hub, schakel het selectievakje *DEVICE ID* in van het apparaat dat u hebt geregistreerd in deze quickstart en druk vervolgens bovenaan op de knop **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een gesimuleerd X. 509-apparaat op uw ontwikkel computer gemaakt en het ingericht voor uw IoT-hub met behulp van de Azure-IoT Hub Device Provisioning Service op de portal. Als u wilt weten hoe u uw X.509-apparaat programmatisch kunt registreren, gaat u verder met de quickstart voor programmatische registratie van een X.509-apparaat. 

> [!div class="nextstepaction"]
> [Azure-quickstart: X.509-apparaat inschrijven bij Azure IoT Hub Device Provisioning Service](quick-enroll-device-x509-python.md)
