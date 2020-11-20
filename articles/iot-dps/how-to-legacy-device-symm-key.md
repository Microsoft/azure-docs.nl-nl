---
title: Apparaten inrichten met symmetrische sleutels-Azure IoT Hub Device Provisioning Service
description: Symmetrische sleutels gebruiken om apparaten in te richten met het DPS-exemplaar (Device Provisioning Service)
author: wesmc7777
ms.author: wesmc
ms.date: 07/13/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: eliotga
ms.openlocfilehash: dc33dcd2c80b2a6d4a1cc27778e49dc06ac48b34
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967309"
---
# <a name="how-to-provision-devices-using-symmetric-key-enrollment-groups"></a>Apparaten inrichten met behulp van symmetrische sleutel registratie groepen

In dit artikel wordt beschreven hoe u veilig meerdere symmetrische-sleutel apparaten kunt inrichten voor één IoT Hub met behulp van een registratie groep.

Sommige apparaten beschikken mogelijk niet over een certificaat, TPM of een andere beveiligings functie die kan worden gebruikt om het apparaat veilig te identificeren. De Device Provisioning Service omvat [symmetrische sleutel Attestation](concepts-symmetric-key-attestation.md). Symmetrische-sleutel Attestation kan worden gebruikt om een apparaat op basis van unieke gegevens, zoals het MAC-adres of een serie nummer, te identificeren.

Als u een [HSM (Hardware Security module)](concepts-service.md#hardware-security-module) en een certificaat eenvoudig kunt installeren, is dat mogelijk een betere benadering voor het identificeren en inrichten van uw apparaten. Omdat u met deze benadering kunt u het bijwerken van de code die is geïmplementeerd op al uw apparaten, niet meer, en er geen geheime sleutel is inge sloten in de installatie kopie van het apparaat.

In dit artikel wordt ervan uitgegaan dat geen HSM of een certificaat een levensvat bare optie is. Er wordt echter wel van uitgegaan dat u een bepaalde methode hebt voor het bijwerken van de Device Provisioning Service om deze apparaten in te richten. 

In dit artikel wordt ervan uitgegaan dat de update van het apparaat in een beveiligde omgeving plaatsvindt om onbevoegde toegang tot de hoofd groep of de afgeleide apparaatwachtwoord te voor komen.

Dit artikel is gericht op een Windows-gebaseerd werkstation. U kunt de procedures echter ook uitvoeren op Linux. Zie [Inrichten voor multitenancy](how-to-provision-multitenant.md) voor een Linux-voorbeeld.

> [!NOTE]
> Het voor beeld dat in dit artikel wordt gebruikt, is geschreven in C. Er is ook een voor beeld van een [symmetrische sleutel](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/SymmetricKeySample) voor het inrichten van een C#-apparaat beschikbaar. Als u dit voor beeld wilt gebruiken, downloadt of kloont u de opslag plaats [Azure-IOT-samples-csharp](https://github.com/Azure-Samples/azure-iot-samples-csharp) en volgt u de instructies in de voorbeeld code. Volg de instructies in dit artikel om een groep voor de registratie van symmetrische sleutels te maken met behulp van de portal en om het ID-bereik en de primaire en secundaire sleutel van de registratie groep te vinden die nodig zijn om het voor beeld uit te voeren. U kunt ook afzonderlijke inschrijvingen maken met behulp van het voor beeld.

## <a name="overview"></a>Overzicht

Er wordt voor elk apparaat een unieke registratie-ID gedefinieerd op basis van informatie die het apparaat identificeert. Bijvoorbeeld het MAC-adres of een serie nummer.

Een registratie groep die gebruikmaakt van [symmetrische sleutel attest](concepts-symmetric-key-attestation.md) wordt gemaakt met de Device Provisioning Service. De registratie groep bevat een groeps hoofd sleutel. Deze hoofd sleutel wordt gebruikt om elke unieke registratie-ID te hashen om een unieke apparaatcode te maken voor elk apparaat. Het apparaat gebruikt de afgeleide apparaatcode met de unieke registratie-ID om te voldoen aan de Device Provisioning Service en moet worden toegewezen aan een IoT-hub.

De apparaatcode die in dit artikel wordt beschreven, volgt hetzelfde patroon als de [Snelstartgids: richt een gesimuleerd apparaat in met symmetrische sleutels](quick-create-simulated-device-symm-key.md). Met de code wordt een apparaat gesimuleerd met behulp van een voor beeld van de [Azure IOT C-SDK](https://github.com/Azure/azure-iot-sdk-c). Het gesimuleerde apparaat wordt bij een registratie groep verklaard in plaats van een afzonderlijke registratie, zoals wordt getoond in de Quick Start.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Vereisten

* Volt ooien van het [instellen van IOT hub Device Provisioning Service met de Azure Portal](./quick-setup-auto-provision.md) Snelstartgids.

De volgende vereisten gelden voor een ontwikkelomgeving in Windows. Voor Linux of macOS raadpleegt u het desbetreffende gedeelte in [Uw ontwikkelomgeving voorbereiden](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) in de SDK-documentatie.

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 met de workload [Desktopontwikkeling met C++](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) ingeschakeld. Visual Studio 2015 en Visual Studio 2017 worden ook ondersteund.

* Meest recente versie van [Git](https://git-scm.com/download/) geïnstalleerd.

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Een ontwikkelomgeving voorbereiden voor de Azure IoT C-SDK

In deze sectie bereidt u een ontwikkelomgeving voor die wordt gebruikt om de [Azure IoT C-SDK](https://github.com/Azure/azure-iot-sdk-c) te bouwen. 

De SDK bevat de voorbeeldcode voor het gesimuleerde apparaat. Dit gesimuleerde apparaat probeert de inrichting uit te voeren tijdens de opstartprocedure van het apparaat.

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

4. Maak een submap `cmake` in de hoofdmap van de Git-opslag plaats en navigeer naar die map. Voer de volgende opdrachten uit vanuit de map `azure-iot-sdk-c`:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. Voer de volgende opdracht uit om een versie van de SDK te bouwen die specifiek is voor uw clientplatform voor ontwikkeling. Er wordt een Visual Studio-oplossing voor het gesimuleerde apparaat gegenereerd in de map `cmake`. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```
    
    Als `cmake` uw C++-compiler niet kan vinden, kunnen er fouten in de build optreden tijdens het uitvoeren van de bovenstaande opdracht. Als dit gebeurt, voert u deze opdracht uit bij de [Visual Studio-opdrachtprompt](/dotnet/framework/tools/developer-command-prompt-for-vs). 

    Zodra het bouwen is voltooid, zijn de laatste paar uitvoerregels vergelijkbaar met de volgende uitvoer:

    ```cmd/sh
    $ cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```


## <a name="create-a-symmetric-key-enrollment-group"></a>Een groep voor de registratie van symmetrische sleutels maken

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en open uw Device Provisioning service-exemplaar.

2. Selecteer het tabblad **inschrijvingen beheren** en klik vervolgens op de knop **registratie groep toevoegen** boven aan de pagina. 

3. Voer in **registratie groep toevoegen** de volgende gegevens in en klik op de knop **Opslaan** .

   - **Groeps naam**: Voer **mylegacydevices** in.

   - **Attestation-type**: Selecteer **symmetrische sleutel**.

   - **Automatisch sleutels genereren**: schakel dit selectievakje in.

   - **Selecteer hoe u apparaten aan hubs wilt toewijzen**: Selecteer **statische configuratie** zodat u kunt toewijzen aan een specifieke hub.

   - **Selecteer de IOT-hubs waaraan deze groep kan worden toegewezen**: Selecteer een van uw hubs.

     ![Registratie groep voor symmetrische sleutel attest toevoegen](./media/how-to-legacy-device-symm-key/symm-key-enrollment-group.png)

4. Zodra u uw inschrijving hebt opgeslagen, worden de **primaire sleutel** en **secundaire sleutel** gegenereerd en aan de inschrijvingsvermelding toegevoegd. De registratie groep voor symmetrische sleutels wordt weer gegeven als **mylegacydevices** onder de kolom *groeps naam* op het tabblad *registratie groepen* . 

    Open de inschrijving en kopieer de waarde van uw gegenereerde **primaire sleutel**. Deze sleutel is de sleutel van uw hoofd groep.


## <a name="choose-a-unique-registration-id-for-the-device"></a>Een unieke registratie-ID voor het apparaat kiezen

Er moet een unieke registratie-ID worden gedefinieerd om elk apparaat te identificeren. U kunt het MAC-adres, serie nummer of unieke informatie van het apparaat gebruiken. 

In dit voor beeld gebruiken we een combi natie van een MAC-adres en serie nummer dat de volgende teken reeks voor een registratie-ID vormt.

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Maak een unieke registratie-ID voor uw apparaat. Geldige tekens zijn kleine letters en streepjes ('-').


## <a name="derive-a-device-key"></a>Een apparaatcode afleiden 

Als u de apparaatcode wilt genereren, gebruikt u de groeps hoofd sleutel om een [HMAC-sha256](https://wikipedia.org/wiki/HMAC) van de unieke registratie-id voor het apparaat te berekenen en converteert u het resultaat naar Base64-indeling.

> [!WARNING]
> De apparaatcode mag alleen de afgeleide apparaatwachtwoord voor het afzonderlijke apparaat bevatten. Neem uw groeps hoofd sleutel niet op in de code van uw apparaat. Een gemanipuleerde hoofd sleutel is de mogelijkheid om de beveiliging van alle apparaten die ermee worden geverifieerd, in gevaar te brengen.


#### <a name="linux-workstations"></a>Linux-werk stations

Als u een Linux-werk station gebruikt, kunt u openssl gebruiken om uw afgeleide apparaatwachtwoord te genereren, zoals wordt weer gegeven in het volgende voor beeld.

Vervang de waarde van **Key** door de **primaire sleutel** die u eerder hebt genoteerd.

Vervang de waarde van **REG_ID** door de registratie-id.

```bash
KEY=8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw==
REG_ID=sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```


#### <a name="windows-based-workstations"></a>Windows-werk stations

Als u een Windows-werk station gebruikt, kunt u Power shell gebruiken om uw afgeleide apparaatwachtwoord te genereren, zoals wordt weer gegeven in het volgende voor beeld.

Vervang de waarde van **Key** door de **primaire sleutel** die u eerder hebt genoteerd.

Vervang de waarde van **REG_ID** door de registratie-id.

```powershell
$KEY='8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw=='
$REG_ID='sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```


Op uw apparaat wordt de afgeleide-apparaatwachtwoord gebruikt met uw unieke registratie-ID voor het uitvoeren van symmetrische sleutel attest met de registratie groep tijdens het inrichten.



## <a name="create-a-device-image-to-provision"></a>Een te inrichten installatie kopie voor een apparaat maken

In deze sectie gaat u een inrichtings voorbeeld met de naam **Prov \_ dev \_ client \_** -voor beeld bijwerken in de Azure IOT C-SDK die u eerder hebt ingesteld. 

Met deze voorbeeldcode wordt een opstartprocedure van het apparaat gesimuleerd waarbij de inrichtingsaanvraag naar uw Device Provisioning Service-instantie wordt verzonden. De opstart procedure zorgt ervoor dat het apparaat wordt herkend en toegewezen aan de IoT-hub die u hebt geconfigureerd voor de registratie groep.

1. Selecteer in Azure Portal het tabblad **Overzicht** voor uw Device Provisioning-service en noteer de waarde van het **_Id-bereik_**.

    ![Device Provisioning Service-eindpuntgegevens uit de portalblade extraheren](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. Open in Visual Studio het oplossingsbestand **azure_iot_sdks.sln** dat eerder is gegenereerd door CMake uit te voeren. Het oplossingsbestand bevindt zich als het goed is op de volgende locatie:

    ```
    \azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

3. Navigeer in het deelvenster *Solution Explorer* van Visual Studio naar de map **Provision\_Samples**. Vouw het voorbeeldproject met de naam **prov\_dev\_client\_sample** uit. Vouw **Source Files** uit en open **prov\_dev\_client\_sample.c**.

4. Zoek de constante `id_scope` op en vervang de waarde door uw **Id-bereik**-waarde die u eerder hebt gekopieerd. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Zoek de definitie voor de functie `main()` op in hetzelfde bestand. Zorg ervoor dat de variabele `hsm_type` is ingesteld op `SECURE_DEVICE_TYPE_SYMMETRIC_KEY`, zoals hieronder wordt weergegeven:

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. Zoek de aanroep naar `prov_dev_set_symmetric_key_info()` in **prov\_dev\_client\_sample.c**, waarbij een opmerking is geplaatst.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Verwijder de opmerking over de functie aanroep en vervang de waarden van de tijdelijke aanduiding (inclusief de punt haken) door de unieke registratie-ID voor het apparaat en de afgeleide-apparaatwachtwoord die u hebt gegenereerd.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6", "Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=");
    ```
   
    Sla het bestand op.

7. Klik met de rechtermuisknop op het **prov\_dev\_client\_sample**-project en selecteer **Set as Startup Project**. 

8. Selecteer in het menu van Visual Studio de optie **Debug** > **Start without debugging** om de oplossing uit te voeren. Klik in de prompt om het project opnieuw te bouwen op **Yes** om het project opnieuw te bouwen voordat het wordt uitgevoerd.

    De volgende output is een voorbeeld waarbij het gesimuleerde apparaat met succes opstart en verbinding maakt met het inrichtingsservice-exemplaar voor toewijzing aan een IoT-hub:

    ```cmd
    Provisioning API Version: 1.2.8

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: 
    test-docs-hub.azure-devices.net, deviceId: sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

    Press enter key to exit:
    ```

9. Navigeer in de portal naar de IoT-hub waaraan uw gesimuleerde apparaat is toegewezen en klik op het tabblad **IOT-apparaten** . Wanneer het inrichten van het gesimuleerde naar de hub is geslaagd, wordt de apparaat-ID weer gegeven op de Blade **IOT-apparaten** , met de *status* **ingeschakeld**. Mogelijk moet u bovenaan op de knop **Vernieuwen** klikken. 

    ![Apparaat wordt geregistreerd voor de IoT-hub](./media/how-to-legacy-device-symm-key/hub-registration.png) 



## <a name="security-concerns"></a>Beveiligings problemen

Houd er rekening mee dat dit de afgeleide apparaatcode bevat die is opgenomen als onderdeel van de installatie kopie. Dit is geen aanbevolen beveiligings best practice. Dit is een reden waarom de beveiliging en het gebruiks gemak van toepassing zijn. 





## <a name="next-steps"></a>Volgende stappen

* Zie [IOT hub-concepten](concepts-device-reprovision.md) voor het opnieuw inrichten van apparaten voor meer informatie. 
* [Snelstartgids: een gesimuleerd apparaat inrichten met symmetrische sleutels](quick-create-simulated-device-symm-key.md)
* Zie voor meer informatie over het ongedaan maken van de inrichting van [apparaten die eerder automatisch zijn ingericht](how-to-unprovision-devices.md) , ongedaan maken