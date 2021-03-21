---
title: Snelstart - Een gesimuleerd X.509-apparaat inrichten voor Azure IoT Hub met behulp van C
description: In deze snelstart wordt gebruikgemaakt van afzonderlijke inschrijvingen. In deze quickstart maakt u een gesimuleerd X.509-apparaat met de IoT C SDK voor Azure IoT Hub Device Provisioning Service (DPS) en richt u het apparaat in.
author: wesmc7777
ms.author: wesmc
ms.date: 04/26/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 8f4c35a61edc970f5d4d3beb0c4255ccfe4e336a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94968142"
---
# <a name="quickstart-provision-an-x509-simulated-device-using-the-azure-iot-c-sdk"></a>Snelstart: Een gesimuleerd X.509-apparaat inrichten met de Azure IoT C SDK

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

In deze snelstart leert u hoe u een X.509-apparaatsimulator op een Windows-ontwikkelcomputer kunt maken en uitvoeren. U configureert dit gesimuleerde apparaat voor toewijzing aan een IoT-hub met behulp van een inschrijving bij een Device Provisioning Service-exemplaar. Er wordt voorbeeldcode van de [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) gebruikt voor het simuleren van een opstartvolgorde voor het apparaat. Het apparaat wordt herkend op basis van de inschrijving bij de inschrijvingsservice en wordt toegewezen aan de IoT-hub.

Raadpleeg [Concepten voor automatische inrichting](about-iot-dps.md#provisioning-process) als u niet bekend bent met het proces van automatisch inrichten. Controleer ook of u de stappen in [IoT Hub Device Provisioning Service instellen met Azure Portal](quick-setup-auto-provision.md) hebt voltooid voordat u verdergaat met deze snelstart. 

Azure IoT Device Provisioning Service ondersteunt twee typen inschrijvingen:

* [Inschrijvingsgroepen](concepts-service.md#enrollment-group): Wordt gebruikt om meerdere gerelateerde apparaten in te schrijven.
* [Afzonderlijke inschrijvingen](concepts-service.md#individual-enrollment): Wordt gebruikt om één apparaat in te schrijven.

In dit artikel worden afzonderlijke inschrijvingen gedemonstreerd.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

De volgende vereisten gelden voor een ontwikkelomgeving in Windows. Voor Linux of macOS raadpleegt u het desbetreffende gedeelte in [Uw ontwikkelomgeving voorbereiden](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) in de SDK-documentatie.

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 met de workload [Desktopontwikkeling met C++](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) ingeschakeld. Visual Studio 2015 en Visual Studio 2017 worden ook ondersteund.

* Meest recente versie van [Git](https://git-scm.com/download/) geïnstalleerd.

## <a name="prepare-a-development-environment-for-the-azure-iot-c-sdk"></a>Een ontwikkelomgeving voorbereiden voor de Azure IoT C SDK

In deze sectie bereidt u een ontwikkelomgeving voor die wordt gebruikt om de [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) te bouwen die de voorbeeldcode voor de x.509-opstartvolgorde bevat.

1. Download het [CMake-buildsysteem](https://cmake.org/download/).

    Het is belangrijk dat de vereisten voor Visual Studio met (Visual Studio en de workload Desktopontwikkeling met C++) op uw computer zijn geïnstalleerd **voordat** de `CMake`-installatie wordt gestart. Zodra aan de vereisten is voldaan en de download is geverifieerd, installeert u het CMake-bouwsysteem.

2. Zoek de tagnaam voor de [nieuwste versie](https://github.com/Azure/azure-iot-sdk-c/releases/latest) van de SDK.

3. Open een opdrachtprompt of Git Bash-shell. Voer de volgende opdrachten uit om de nieuwste release van de GitHub-opslagplaats van de [Azure IoT C-SDK](https://github.com/Azure/azure-iot-sdk-c) te klonen. Gebruik de tag die u in de vorige stap hebt gevonden als waarde voor de parameter `-b`:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Deze bewerking kan enkele minuten in beslag nemen.

4. Maak de submap `cmake` in de hoofdmap van de Git-opslagplaats en navigeer naar die map. Voer de volgende opdrachten uit vanuit de map `azure-iot-sdk-c`:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. De voorbeeldcode gebruikt een X.509-certificaat voor attestation via x.509-verificatie. Voer de volgende opdracht uit om een versie van de SDK te bouwen die specifiek is voor uw platform voor ontwikkeling dat de client voor apparaatinrichting bevat. Er wordt een Visual Studio-oplossing voor het gesimuleerde apparaat gegenereerd in de map `cmake`.

    ```cmd
    cmake -Duse_prov_client:BOOL=ON ..
    ```

    Als `cmake` uw C++-compiler niet kan vinden, kunnen er fouten in de build optreden tijdens het uitvoeren van de bovenstaande opdracht. Als dit gebeurt, voert u deze opdracht uit bij de [Visual Studio-opdrachtprompt](/dotnet/framework/tools/developer-command-prompt-for-vs).

    Zodra het bouwen is voltooid, zijn de laatste paar uitvoerregels vergelijkbaar met de volgende uitvoer:

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON ..
    -- Building for: Visual Studio 16 2019
    -- The C compiler identification is MSVC 19.23.28107.0
    -- The CXX compiler identification is MSVC 19.23.28107.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: C:/code/azure-iot-sdk-c/cmake
    ```

## <a name="create-a-self-signed-x509-device-certificate"></a>Een zelfondertekend X.509-apparaatcertificaat maken

In deze sectie gebruikt u een zelf-ondertekend X.509-certificaat. Het is hierbij belangrijk dat u rekening houdt met de volgende punten:

* Zelfondertekende certificaten zijn alleen voor testdoeleinden en moeten niet in productieomgevingen worden gebruikt.
* De standaardvervaltermijn voor een zelfondertekend certificaat is één jaar.

U gaat voorbeeldcode van de Azure IoT C-SDK gebruiken om het certificaat te maken dat moet worden gebruikt met de afzonderlijke inschrijvingsvermelding voor het gesimuleerde apparaat.

1. Open Visual Studio en open het nieuwe oplossingbestand met de naam `azure_iot_sdks.sln`. Dit oplossingsbestand bevindt zich in de map `cmake` die u eerder hebt gemaakt in de hoofdmap van de azure-iot-sdk-c git-opslagplaats.

2. Selecteer in het menu van Visual Studio, **Build** > **Build Solution** om alle projecten in de oplossing te bouwen.

3. Navigeer in het deelvenster *Solution Explorer* van Visual Studio naar de map **Provision\_Tools**. Klik met de rechtermuisknop op het **dice\_device\_enrollment**-project en selecteer **Set as Startup Project**.

4. Selecteer in het menu van Visual Studio de optie **Debug** > **Start without debugging** om de oplossing uit te voeren. Voer in het uitvoervenster **i** in voor individuele registratie wanneer hierom wordt gevraagd.

    In het uitvoervenster wordt een lokaal gegenereerd zelfondertekend X.509-certificaat weergegeven voor uw gesimuleerde apparaat. Kopieer de uitvoer naar Klembord vanaf **-----BEGIN CERTIFICATE-----** tot en met de eerste **-----END CERTIFICATE-----**, en zorg ervoor dat deze beide regels ook zijn opgenomen. U hebt alleen het eerste certificaat uit het uitvoervenster nodig.

5. Sla het certificaat met behulp van een teksteditor op naar een nieuw bestand met de naam **_X509testcert.pem_**.

## <a name="create-a-device-enrollment-entry-in-the-portal"></a>Een vermelding voor apparaatregistratie maken in de portal

1. Meld u aan bij Azure Portal, selecteer in het linkermenu de knop **Alle resources** en open Device Provisioning Service.

2. Selecteer het tabblad **Inschrijvingen beheren** en klik vervolgens op de knop **Afzonderlijke inschrijvingen toevoegen** bovenaan.

3. Voer in het deelvenster **Inschrijving toevoegen** de volgende informatie in en druk vervolgens op de knop **Opslaan**.

    * **Mechanisme:** selecteer **X.509** als *mechanisme* voor identiteitscontrole.
    * **Pem- of cer-bestand voor primair certificaat:** Klik op **Een bestand selecteren** om het certificaatbestand X509testcert.pem te selecteren dat u eerder hebt gemaakt.
    * **IoT Hub-apparaat-id:** voer **test-docs-cert-device** in als id voor het apparaat.

      [![Afzonderlijke inschrijving voor X.509-attestation toevoegen in de portal](./media/quick-create-simulated-device-x509/device-enrollment.png)](./media/quick-create-simulated-device-x509/device-enrollment.png#lightbox)

      Als het apparaat is ingeschreven, wordt uw X.509-apparaat weergegeven als **riot-device-cert** onder de kolom *Registratie-id* op het tabblad *Afzonderlijke registraties*. 

## <a name="simulate-first-boot-sequence-for-the-device"></a>Eerste opstartvolgorde voor het apparaat simuleren

In deze sectie werkt u de voorbeeldcode voor het verzenden van de opstartvolgorde van het apparaat naar uw Device Provisioning Service-exemplaar bij. Deze opstartvolgorde zorgt ervoor dat het apparaat kan worden herkend en toegewezen aan een IoT-hub die is gekoppeld aan het Device Provisioning Service-exemplaar.

1. Selecteer in Azure Portal het tabblad **Overzicht** voor uw Device Provisioning-service en noteer de waarde van het **_Id-bereik_**.

    ![Device Provisioning Service-eindpuntgegevens uit de portalblade extraheren](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. Navigeer in het deelvenster *Solution Explorer* van Visual Studio naar de map **Provision\_Samples**. Vouw het voorbeeldproject met de naam **prov\_dev\_client\_sample** uit. Vouw **Source Files** uit en open **prov\_dev\_client\_sample.c**.

3. Zoek de constante `id_scope` op en vervang de waarde door uw **Id-bereik**-waarde die u eerder hebt gekopieerd. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

4. Zoek de definitie voor de functie `main()` op in hetzelfde bestand. Zorg ervoor dat de variabele `hsm_type` is ingesteld op `SECURE_DEVICE_TYPE_X509` in plaats van `SECURE_DEVICE_TYPE_TPM`, zoals hieronder wordt weergegeven.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

5. Klik met de rechtermuisknop op het **prov\_dev\_client\_sample**-project en selecteer **Set as Startup Project**.

6. Selecteer in het menu van Visual Studio de optie **Debug** > **Start without debugging** om de oplossing uit te voeren. Klik in de prompt om het project opnieuw te bouwen op **Yes** om het project opnieuw te bouwen voordat het wordt uitgevoerd.

    De volgende uitvoer is een voorbeeld van de voorbeeldcode van de Provisioning Device-client die met succes opstart en verbinding maakt met het Provisioning Service-exemplaar om IoT hub-informatie op te halen en zich te registreren:

    ```cmd
    Provisioning API Version: 1.2.7

    Registering... Press enter key to interrupt.

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service:
    test-docs-hub.azure-devices.net, deviceId: test-docs-cert-device
    ```

7. Navigeer in de portal naar de IoT-hub die is gekoppeld aan uw Provisioning-service en selecteer het tabblad **IoT-apparaten**. Wanneer het inrichten van het gesimuleerde X.509-apparaat voor de hub is voltooid, wordt de apparaat-id weergegeven op de blade **IoT-apparaten** met de *STATUS* **ingeschakeld**. Mogelijk moet u bovenaan op de knop **Vernieuwen** drukken. 

    ![Apparaat wordt geregistreerd voor de IoT-hub](./media/quick-create-simulated-device-x509/hub-registration.png) 

## <a name="clean-up-resources"></a>Resources opschonen

Als u wilt blijven doorwerken met het voorbeeld van de apparaatclient en deze beter wilt leren kennen, wis de resources die in deze quickstart zijn gemaakt dan niet. Als u niet wilt doorgaan, gebruikt u de volgende stappen om alle resources te verwijderen die via deze quickstart zijn gemaakt.

1. Sluit het uitvoervenster van het voorbeeld van de apparaatclient op de computer.
1. Selecteer in het linkermenu in Azure Portal **Alle resources** en selecteer uw Device Provisioning Service. Open het tabblad **Inschrijvingen beheren** voor uw service en klik vervolgens op het tabblad **Afzonderlijke inschrijvingen**. Schakel het selectievakje naast de *Registratie-id* in van het apparaat dat u hebt ingeschreven in deze quickstart. Druk vervolgens op de knop **Verwijderen** bovenaan het deelvenster. 
1. Selecteer in het linkermenu in Azure Portal **Alle resources** en selecteer vervolgens uw IoT-hub. Open **IoT-apparaten** voor uw hub, schakel het selectievakje *DEVICE ID* in van het apparaat dat u hebt geregistreerd in deze quickstart en druk vervolgens bovenaan op de knop **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een gesimuleerd X.509-apparaat op uw Windows-computer gemaakt en het ingericht voor uw IoT-hub met de Azure IoT Hub Device Provisioning Service in de portal. Als u wilt weten hoe u uw X.509-apparaat programmatisch kunt registreren, gaat u verder met de quickstart voor programmatische registratie van een X.509-apparaat. 

> [!div class="nextstepaction"]
> [Azure-quickstart: X.509-apparaat inschrijven bij Azure IoT Hub Device Provisioning Service](quick-enroll-device-x509-java.md)