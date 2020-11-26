---
title: Zelfstudie voor het gebruik van aangepast toewijzingsbeleid met Azure IoT Hub Device Provisioning Service (DPS)
description: Zelfstudie voor het gebruik van aangepast toewijzingsbeleid met Azure IoT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 09/23/2020
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 4cab1765a387bbae61c9c242a8e7a1ca881ea1f5
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966646"
---
# <a name="tutorial-use-custom-allocation-policies-with-device-provisioning-service-dps"></a>Zelfstudie: Aangepast toewijzingsbeleid gebruiken met Device Provisioning Service (DPS)

Met een aangepast toewijzingsbeleid heeft u meer controle over de wijze waarop apparaten worden toegewezen aan een IoT-hub. Dit wordt gerealiseerd door gebruik te maken van aangepaste code in een [Azure-functie](../azure-functions/functions-overview.md) die wordt uitgevoerd tijdens het inrichten om apparaten toe te wijzen aan een IoT-hub. De service voor het inrichten van apparaten (Device Provisioning Service) roept de code van uw Azure-functie aan die alle relevante informatie over het apparaat en de inschrijving verschaft. De functiecode wordt uitgevoerd en retourneert de gegevens van de IoT-hub die worden gebruikt om het apparaat in te richten.

Door gebruik te maken van aangepast toewijzingsbeleid definieert u uw eigen toewijzingsbeleid wanneer het beleid dat door Device Provisioning Service wordt geleverd niet voldoet aan de vereisten van uw scenario.

Misschien wilt u bijvoorbeeld het certificaat bekijken dat door een apparaat wordt gebruikt tijdens het inrichten en het apparaat toewijzen aan een IoT-hub op basis van een certificaateigenschap. Of misschien hebt u gegevens opgeslagen in een database voor uw apparaten en moet u gegevens uit de database opvragen om te bepalen aan welke IoT-hub een apparaat moet worden toegewezen.

In dit artikel wordt een registratiegroep beschreven met een aangepast toewijzingsbeleid dat gebruikmaakt van een Azure-functie die is geschreven in C# om broodroosters in te richten met behulp van symmetrische sleutels. Een apparaat dat niet wordt herkend als een broodrooster wordt niet ingericht voor een IoT-hub.

Apparaten vragen inrichting aan met behulp van de voorbeeldcode voor het inrichten die is opgenomen in de [Azure IoT-SDK voor C](https://github.com/Azure/azure-iot-sdk-c).


In deze zelfstudie doet u het volgende:

> [!div class="checklist"]
> * Een nieuwe app voor een Azure-functie maken ter ondersteuning van een functie voor aangepaste toewijzing
> * Een nieuwe groepsregistratie maken met behulp van een Azure-functie voor het aangepaste toewijzingsbeleid
> * Apparaatsleutels voor twee apparaten maken
> * De ontwikkelomgeving instellen voor voorbeeldapparaatcode vanuit de [Azure IoT-SDK voor C](https://github.com/Azure/azure-iot-sdk-c)
> * De apparaten uitvoeren en controleren of deze zijn ingericht volgens het aangepaste toewijzingsbeleid


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* In dit artikel wordt er vanuit gegaan dat u de stappen in [IoT Hub Device Provisioning Service instellen met Azure Portal](./quick-setup-auto-provision.md) hebt uitgevoerd om de IoT-hub en DPS-instantie te maken.

* Meest recente versie van [Git](https://git-scm.com/download/) geïnstalleerd.

* Voor een Windows-ontwikkelomgeving is [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 vereist, waarbij de workload [Desktopontwikkeling met C++](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) is ingeschakeld. Visual Studio 2015 en Visual Studio 2017 worden ook ondersteund.

* Voor Linux of macOS raadpleegt u het desbetreffende gedeelte in [Uw ontwikkelomgeving voorbereiden](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) in de documentatie voor de [Azure IoT-SDK voor C](https://github.com/Azure/azure-iot-sdk-c).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-custom-allocation-function"></a>De functie voor aangepaste toewijzing maken

In deze sectie maakt u een Azure-functie waarmee u uw aangepaste toewijzingsbeleid implementeert. Met deze functie wordt bepaald of een apparaat moet worden geregistreerd bij uw IoT-hub op basis van het feit of de registratie-id het tekenreeksvoorvoegsel **contoso-toaster** bevat.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer op uw startpagina de optie **+ Een resource maken**.

2. Typ in het zoekvak *Marketplace doorzoeken* de tekst 'Functie-app'. Selecteer **Functie-app** in de vervolgkeuzelijst en selecteer vervolgens **Maken**.

3. Voer op de pagina voor het maken van de **functie-app** onder het tabblad **Basisgegevens** de volgende instellingen in voor uw nieuwe functie-app en selecteer **Controleren en maken**:

    **Abonnement**: Als u meerdere abonnementen hebt en het gewenste abonnement niet is geselecteerd, selecteert u het abonnement dat u wilt gebruiken.

    **Resourcegroep**: Met dit veld kunt u een nieuwe resourcegroep maken of een bestaande resourcegroep kiezen waarin de functie-app moet worden opgenomen. Kies de resourcegroep die de IoT-hub bevat die u eerder hebt gemaakt voor het testen, bijvoorbeeld **TestResources**. Door alle gerelateerde resources samen in een groep te plaatsen, kunt u ze samen beheren.

    **Naam van de functie-app**: Voer een unieke naam in voor de functie-app. In dit voorbeeld wordt **contoso-function-app** gebruikt.

    **Publiceren**: Controleer of **Code** is geselecteerd.

    **Runtimestack**: Selecteer **.NET Core** in de vervolgkeuzelijst.

    **Regio**: Selecteer dezelfde regio als uw resourcegroep. In dit voorbeeld wordt **US - west** gebruikt.

    > [!NOTE]
    > Application Insights is standaard ingeschakeld. Application Insights is niet nodig voor dit artikel, maar het kan nuttig zijn bij het begrijpen en onderzoeken van problemen die u tegenkomt bij de aangepaste toewijzing. Als u wilt, kunt u Application Insights uitschakelen door het tabblad **Controle** te selecteren en vervolgens **Nee** voor **Application Insights inschakelen**.

    ![Een app voor een Azure-functie maken om de functie voor aangepaste toewijzing te hosten](./media/tutorial-custom-allocation-policies/create-function-app.png)

4. Selecteer **Maken** op de pagina **Overzicht** om de functie-app te maken. De implementatie kan enkele minuten duren. Wanneer de implementatie is voltooid, selecteert u **Ga naar resource**.

5. Klik in het linkerdeelvenster onder **Functies** op **Functies** en vervolgens op **+ Toevoegen** om een nieuwe functie toe te voegen.

6. Selecteer op de pagina met sjablonen de tegel **HTTP-trigger** en selecteer vervolgens **Functie maken**. Er wordt een functie gemaakt met de naam **HttpTrigger1** en in de portal wordt de overzichtspagina voor uw functie weergegeven.

7. Klik op **Coderen en testen** voor uw nieuwe functie. In de portal wordt de inhoud van het codebestand **run.csx** weergegeven. 

8. Vervang de code voor de functie **HttpTrigger1** door de volgende code en selecteer **Opslaan**. Uw code voor de aangepaste toewijzing is klaar om te worden gebruikt.

    ```csharp
    #r "Newtonsoft.Json"

    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        // Get request body
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);

        log.LogInformation("Request.Body:...");
        log.LogInformation(requestBody);

        // Get registration ID of the device
        string regId = data?.deviceRuntimeContext?.registrationId;

        string message = "Uncaught error";
        bool fail = false;
        ResponseObj obj = new ResponseObj();

        if (regId == null)
        {
            message = "Registration ID not provided for the device.";
            log.LogInformation("Registration ID : NULL");
            fail = true;
        }
        else
        {
            string[] hubs = data?.linkedHubs.ToObject<string[]>();

            // Must have hubs selected on the enrollment
            if (hubs == null)
            {
                message = "No hub group defined for the enrollment.";
                log.LogInformation("linkedHubs : NULL");
                fail = true;
            }
            else
            {
                // This is a Contoso Toaster 
                if (regId.Contains("contoso-toaster"))
                {
                    //Log IoT hubs configured for the enrollment
                    foreach(string hubString in hubs)
                    {
                        log.LogInformation("linkedHub : " + hubString);
                    }

                    obj.iotHubHostName = hubs[0];
                    log.LogInformation("Selected hub : " + obj.iotHubHostName);
                }
                else
                {
                    fail = true;
                    message = "Unrecognized device registration.";
                    log.LogInformation("Unknown device registration");
                }
            }
        }

        log.LogInformation("\nResponse");
        log.LogInformation((obj.iotHubHostName != null) ? JsonConvert.SerializeObject(obj) : message);

        return (fail)
            ? new BadRequestObjectResult(message) 
            : (ActionResult)new OkObjectResult(obj);
    }

    public class ResponseObj
    {
        public string iotHubHostName {get; set;}
    }
    ```

9. Klik net onder het codebestand **run.csx** op **Logboeken** om de logboekregistratie van de functie voor de aangepaste toewijzing te controleren. 


## <a name="create-the-enrollment"></a>De registratie maken

In deze sectie maakt u een nieuwe registratiegroep die gebruikmaakt van het aangepaste toewijzingsbeleid. Ter vereenvoudiging wordt in dit artikel bij de registratie gebruikgemaakt van een [verklaring met symmetrische sleutels](concepts-symmetric-key-attestation.md). Voor een veiligere oplossing kunt u gebruikmaken van de [verklaring met het X.509-certificaat](concepts-x509-attestation.md) met een vertrouwensketen.

1. Open in [Azure Portal](https://portal.azure.com) de inrichtingsservice.

2. Selecteer **Registraties beheren** in het linkerdeelvenster en selecteer vervolgens bovenaan de pagina de knop **Registratiegroep toevoegen**.

3. Voer in **Registratiegroep toevoegen** de gegevens in de onderstaande tabel in en klik op de knop **Opslaan**.

    | Veld | Beschrijving en/of voorgestelde waarde |
    | :---- | :----------------------------- |
    | **Groepsnaam** | Voer **contoso-custom-allocated-devices** in |
    | **Type verklaring** | Selecteer **Symmetrische sleutel** |
    | **Sleutels automatisch genereren** | Dit selectievakje moet al zijn ingeschakeld. |
    | **Selecteer hoe apparaten aan hubs moeten worden toegewezen** | Selecteer **Aangepast (Azure-functie gebruiken)** |
    | **Selecteer de IoT-hubs waaraan deze groep kan worden toegewezen** | Selecteer de IoT-hub die u eerder hebt gemaakt toen u de quickstart hebt uitgevoerd. |
    | **Azure-functie selecteren** | Selecteer het abonnement dat de functie-app bevat die u hebt gemaakt. Selecteer vervolgens de **contoso-function-app** en **HttpTrigger1** voor de functie. |

    ![Registratiegroep voor de aangepaste toewijzing toevoegen voor de verklaring met symmetrische sleutels](./media/tutorial-custom-allocation-policies/create-custom-allocation-enrollment.png)

4. Nadat u de registratie hebt opgeslagen, opent u deze opnieuw en noteert u de **primaire sleutel**. U moet de registratie eerst opslaan voordat de sleutels kunnen worden gegenereerd. Deze primaire symmetrische sleutel wordt gebruikt voor het genereren van unieke apparaatsleutels voor apparaten die later worden ingericht. 

## <a name="derive-unique-device-keys"></a>Unieke apparaatsleutels afleiden

Apparaten maken niet rechtstreeks gebruik van de primaire symmetrische sleutel. In plaats daarvan gebruikt u de primaire sleutel om een apparaatsleutel af te leiden voor elk apparaat. In deze sectie maakt u twee unieke apparaatsleutels. Eén sleutel wordt gebruikt voor een gesimuleerde broodrooster. De andere sleutel wordt gebruikt voor een gesimuleerde warmtepomp. Met de gegenereerde sleutels kan voor beide apparaten een registratie worden uitgevoerd. Slechts één apparaatregistratie-id heeft een geldig achtervoegsel dat wordt geaccepteerd door de voorbeeldcode van het aangepaste toewijzingsbeleid. Als gevolg hiervan wordt één apparaat geaccepteerd en het andere geweigerd

Als u de apparaatsleutel wilt afleiden, gebruikt u de symmetrische sleutel die u eerder hebt genoteerd om de [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) van de registratie-id van het apparaat voor elk apparaat te berekenen en zet u het resultaat om in de Base64-indeling. Voor meer informatie over het maken van afgeleide apparaatsleutels bij registratiegroepen, raadpleegt u de sectie over groepsregistraties van [Verklaring met symmetrische sleutels](concepts-symmetric-key-attestation.md).

Gebruik voor het voorbeeld in dit artikel de volgende twee apparaatregistratie-id's met de onderstaande code om een apparaatsleutel voor beide apparaten te berekenen:

* **contoso-toaster-007**
* **contoso-heatpump-088**

Vervang de waarde van de variabele **KEY** door de **primaire sleutel** die u eerder hebt genoteerd nadat de registratiegroep is gemaakt. De sleutelwaarde en uitvoer die wordt weergegeven met de onderstaande code is slechts een voorbeeld.

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u een Windows-werkstation gebruikt, kunt u PowerShell gebruiken om de afgeleide apparaatsleutel te genereren, zoals wordt getoond in het volgende voorbeeld.

```powershell
$KEY='oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA=='

$REG_ID1='contoso-toaster-007'
$REG_ID2='contoso-heatpump-088'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($key)
$sig1 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID1))
$sig2 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID2))
$derivedkey1 = [Convert]::ToBase64String($sig1)
$derivedkey2 = [Convert]::ToBase64String($sig2)

echo "`n`n$REG_ID1 : $derivedkey1`n$REG_ID2 : $derivedkey2`n`n"
```

```powershell
contoso-toaster-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
contoso-heatpump-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
```

#### <a name="bash"></a>[Bash](#tab/bash)

Als u een Linux-werkstation gebruikt, kunt u openssl gebruiken om de afgeleide apparaatsleutels te genereren, zoals wordt getoond in het volgende Bash-voorbeeld.

```bash
KEY=oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA==

REG_ID1=contoso-toaster-007
REG_ID2=contoso-heatpump-088

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
devkey1=$(echo -n $REG_ID1 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)
devkey2=$(echo -n $REG_ID2 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)

echo -e $"\n\n$REG_ID1 : $devkey1\n$REG_ID2 : $devkey2\n\n"
```

```bash
contoso-toaster-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
contoso-heatpump-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
```

---


## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Een ontwikkelomgeving voorbereiden voor de Azure IoT C-SDK

Apparaten vragen inrichting aan met behulp van de voorbeeldcode voor het inrichten die is opgenomen in de [Azure IoT-SDK voor C](https://github.com/Azure/azure-iot-sdk-c).

In deze sectie bereidt u een ontwikkelomgeving voor die wordt gebruikt om de [Azure IoT-SDK voor C](https://github.com/Azure/azure-iot-sdk-c) te maken. De SDK bevat de voorbeeldcode voor het gesimuleerde apparaat. Dit gesimuleerde apparaat probeert de inrichting uit te voeren tijdens de opstartprocedure van het apparaat.

Deze sectie is gebaseerd op een Windows-werkstation. Bekijk de configuratie van de VM's in [Inrichten voor multitenancy](how-to-provision-multitenant.md) voor een Linux-voorbeeld.

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

5. Voer de volgende opdracht uit om een versie van de SDK te bouwen die specifiek is voor uw clientplatform voor ontwikkeling. Er wordt een Visual Studio-oplossing voor het gesimuleerde apparaat gegenereerd in de map `cmake`. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    Als `cmake` uw C++-compiler niet kan vinden, kunnen er opbouwfouten optreden tijdens het uitvoeren van de opdracht. Als dit gebeurt, voert u de opdracht uit in de [Visual Studio-opdrachtprompt](/dotnet/framework/tools/developer-command-prompt-for-vs).

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

## <a name="simulate-the-devices"></a>De apparaten simuleren

In deze sectie werkt u een inrichtingsvoorbeeld bij met de naam **prov\_dev\_client\_sample** dat zich in de Azure IoT-SDK voor C bevindt die u eerder hebt ingesteld.

Met deze voorbeeldcode wordt een opstartprocedure van het apparaat gesimuleerd waarbij de inrichtingsaanvraag naar uw Device Provisioning Service-instantie wordt verzonden. De opstartprocedure zorgt ervoor dat het broodrooster wordt herkend en toegewezen aan de IoT-hub met behulp van het aangepaste toewijzingsbeleid.

1. Selecteer in Azure Portal het tabblad **Overzicht** voor uw Device Provisioning-service en noteer de waarde van het **_Id-bereik_**.

    ![Device Provisioning Service-eindpuntgegevens uit de portalblade extraheren](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. Open in Visual Studio het oplossingsbestand **azure_iot_sdks.sln** dat eerder is gegenereerd door CMake uit te voeren. Het oplossingsbestand bevindt zich als het goed is op de volgende locatie:

    ```
    azure-iot-sdk-c\cmake\azure_iot_sdks.sln
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

6. Zoek in de functie `main()` de aanroep naar `Prov_Device_Register_Device()`. Voeg net vóór die aanroep de volgende regels code toe die gebruikmaken van [`Prov_Device_Set_Provisioning_Payload()`](/azure/iot-hub/iot-c-sdk-ref/prov-device-client-h/prov-device-set-provisioning-payload) voor het doorgeven van een aangepaste JSON-payload tijdens het inrichten. Dit kan worden gebruikt om meer informatie over uw aangepaste toewijzingsfuncties te krijgen. Dit kan ook worden gebruikt om het apparaattype door te geven in plaats van de registratie-id te onderzoeken.

    ```c
    // An example custom payload
    const char* custom_json_payload = "{\"MyDeviceFirmwareVersion\":\"12.0.2.5\",\"MyDeviceProvisioningVersion\":\"1.0.0.0\"}";

    prov_device_result = Prov_Device_Set_Provisioning_Payload(prov_device_handle, custom_json_payload);
    if (prov_device_result != PROV_DEVICE_RESULT_OK)
    {
        (void)printf("\r\nFailure setting provisioning payload: %s\r\n", MU_ENUM_TO_STRING(PROV_DEVICE_RESULT, prov_device_result));
    }
    ```

7. Klik met de rechtermuisknop op het **prov\_dev\_client\_sample**-project en selecteer **Set as Startup Project**.

### <a name="simulate-the-contoso-toaster-device"></a>Het Contoso-broodrooster simuleren

1. Als u het broodrooster wilt simuleren, zoekt u de aanroep naar `prov_dev_set_symmetric_key_info()` in **prov\_dev\_client\_sample.c** waarbij een opmerking is geplaatst.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Verwijder de opmerkingen bij de functieaanroep en vervang de waarden voor de tijdelijke aanduiding (inclusief de punthaken) door de registratie-id van het broodrooster en de afgeleide apparaatsleutel die u eerder hebt gegenereerd. De sleutelwaarde **JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=** die hieronder wordt weergegeven, dient alleen als voorbeeld.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-toaster-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```

    Sla het bestand op.

2. Selecteer in het menu van Visual Studio de optie **Debug** > **Start without debugging** om de oplossing uit te voeren. Selecteer **Yes** in de prompt voor het opnieuw maken van het project om het project opnieuw te maken voordat het wordt uitgevoerd.

    De onderstaande tekst is een voorbeeld van uitvoer van de logboekregistratie als gevolg van de code voor de functie van aangepaste toewijzing die wordt uitgevoerd voor het broodrooster. U ziet dat er een hub is geselecteerd voor een broodrooster. Het `payload`-lid bevat de aangepaste JSON-inhoud die u aan de code hebt toegevoegd. Deze inhoud is beschikbaar voor gebruik in de `deviceRuntimeContext`.

    U vindt deze logboekregistratie door op **Logboeken** te klikken onder de functiecode in de portal:

    ```cmd
    2020-09-23T11:44:37.505 [Information] Executing 'Functions.HttpTrigger1' (Reason='This function was programmatically called via the host APIs.', Id=4596d45e-086f-4e86-929b-4a02814eee40)
    2020-09-23T11:44:41.380 [Information] C# HTTP trigger function processed a request.
    2020-09-23T11:44:41.381 [Information] Request.Body:...
    2020-09-23T11:44:41.381 [Information] {"enrollmentGroup":{"enrollmentGroupId":"contoso-custom-allocated-devices","attestation":{"type":"symmetricKey"},"capabilities":{"iotEdge":false},"etag":"\"e8002126-0000-0100-0000-5f6b2a570000\"","provisioningStatus":"enabled","reprovisionPolicy":{"updateHubAssignment":true,"migrateDeviceData":true},"createdDateTimeUtc":"2020-09-23T10:58:31.62286Z","lastUpdatedDateTimeUtc":"2020-09-23T10:58:31.62286Z","allocationPolicy":"custom","iotHubs":["contoso-toasters-hub-1098.azure-devices.net"],"customAllocationDefinition":{"webhookUrl":"https://contoso-function-app.azurewebsites.net/api/HttpTrigger1?****","apiVersion":"2019-03-31"}},"deviceRuntimeContext":{"registrationId":"contoso-toaster-007","symmetricKey":{},"payload":{"MyDeviceFirmwareVersion":"12.0.2.5","MyDeviceProvisioningVersion":"1.0.0.0"}},"linkedHubs":["contoso-toasters-hub-1098.azure-devices.net"]}
    2020-09-23T11:44:41.687 [Information] linkedHub : contoso-toasters-hub-1098.azure-devices.net
    2020-09-23T11:44:41.688 [Information] Selected hub : contoso-toasters-hub-1098.azure-devices.net
    2020-09-23T11:44:41.688 [Information] Response
    2020-09-23T11:44:41.688 [Information] {"iotHubHostName":"contoso-toasters-hub-1098.azure-devices.net"}
    2020-09-23T11:44:41.689 [Information] Executed 'Functions.HttpTrigger1' (Succeeded, Id=4596d45e-086f-4e86-929b-4a02814eee40, Duration=4347ms)    
    ```

    In het volgende voorbeeld van apparaatuitvoer wordt vermeld dat het gesimuleerde broodrooster wordt opgestart en verbinding maakt met de instantie van de inrichtingsservice om te worden toegewezen aan een IoT-hub voor broodroosters door het aangepaste toewijzingsbeleid:

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: contoso-toaster-007

    Press enter key to exit:
    ```

### <a name="simulate-the-contoso-heat-pump-device"></a>De Contoso-warmtepomp simuleren

1. Als u de warmtepomp wilt simuleren, werkt u de aanroep van `prov_dev_set_symmetric_key_info()` weer bij naar **prov\_dev\_client\_sample.c** met de registratie-id van de warmtepomp en de afgeleide apparaatsleutel die u eerder hebt gegenereerd. De sleutelwaarde **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=** die hieronder wordt weergegeven, dient alleen als voorbeeld.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-heatpump-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```

    Sla het bestand op.

2. Selecteer in het menu van Visual Studio de optie **Debug** > **Start without debugging** om de oplossing uit te voeren. Selecteer **Yes** in de prompt voor het opnieuw maken van het project om het project opnieuw te maken voordat het wordt uitgevoerd.

    De onderstaande tekst is een voorbeeld van uitvoer van de logboekregistratie als gevolg van de code voor de functie van aangepaste toewijzing die wordt uitgevoerd voor de warmtepomp. Het aangepaste toewijzingsbeleid weigert deze registratie met een HTTP-fout '400 Ongeldige aanvraag'. Het `payload`-lid bevat de aangepaste JSON-inhoud die u aan de code hebt toegevoegd. Deze inhoud is beschikbaar voor gebruik in de `deviceRuntimeContext`.

    U vindt deze logboekregistratie door op **Logboeken** te klikken onder de functiecode in de portal:

    ```cmd
    2020-09-23T11:50:23.652 [Information] Executing 'Functions.HttpTrigger1' (Reason='This function was programmatically called via the host APIs.', Id=2fa77f10-42f8-43fe-88d9-a8c01d4d3f68)
    2020-09-23T11:50:23.653 [Information] C# HTTP trigger function processed a request.
    2020-09-23T11:50:23.654 [Information] Request.Body:...
    2020-09-23T11:50:23.654 [Information] {"enrollmentGroup":{"enrollmentGroupId":"contoso-custom-allocated-devices","attestation":{"type":"symmetricKey"},"capabilities":{"iotEdge":false},"etag":"\"e8002126-0000-0100-0000-5f6b2a570000\"","provisioningStatus":"enabled","reprovisionPolicy":{"updateHubAssignment":true,"migrateDeviceData":true},"createdDateTimeUtc":"2020-09-23T10:58:31.62286Z","lastUpdatedDateTimeUtc":"2020-09-23T10:58:31.62286Z","allocationPolicy":"custom","iotHubs":["contoso-toasters-hub-1098.azure-devices.net"],"customAllocationDefinition":{"webhookUrl":"https://contoso-function-app.azurewebsites.net/api/HttpTrigger1?****","apiVersion":"2019-03-31"}},"deviceRuntimeContext":{"registrationId":"contoso-heatpump-088","symmetricKey":{},"payload":{"MyDeviceFirmwareVersion":"12.0.2.5","MyDeviceProvisioningVersion":"1.0.0.0"}},"linkedHubs":["contoso-toasters-hub-1098.azure-devices.net"]}
    2020-09-23T11:50:23.654 [Information] Unknown device registration
    2020-09-23T11:50:23.654 [Information] Response
    2020-09-23T11:50:23.654 [Information] Unrecognized device registration.
    2020-09-23T11:50:23.655 [Information] Executed 'Functions.HttpTrigger1' (Succeeded, Id=2fa77f10-42f8-43fe-88d9-a8c01d4d3f68, Duration=11ms)
    ```

    In het volgende voorbeeld van apparaatuitvoer wordt vermeld dat de gesimuleerde warmtepomp wordt opgestart en verbinding maakt met de instantie van de inrichtingsservice om een registratie uit te voeren bij een IoT-hub met behulp van het aangepaste toewijzingsbeleid. Dit mislukt met een fout (`Custom allocation failed with status code: 400`) omdat het aangepaste toewijzingsbeleid alleen is ontworpen om broodroosters toe te staan:


    ```cmd
    Provisioning API Version: 1.3.7
    
    Registering Device
    
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Error: Time:Wed Sep 23 13:06:01 2020 File:d:\testing\azure-iot-sdk-c\provisioning_client\src\prov_device_ll_client.c Func:_prov_transport_process_json_reply Line:658 Provisioning Failure: OperationId: 4.eb89f3e8407a3711.2525bd34-02e9-4e91-a9c0-4dbc4ad5de66 - Date: 2020-09-23T17:05:58.2363145Z - Msg: Custom allocation failed with status code: 400
    Error: Time:Wed Sep 23 13:06:01 2020 File:d:\testing\azure-iot-sdk-c\provisioning_client\src\prov_transport_mqtt_common.c Func:_prov_transport_common_mqtt_dowork Line:1014 Unable to process registration reply.
    Error: Time:Wed Sep 23 13:06:01 2020 File:d:\testing\azure-iot-sdk-c\provisioning_client\src\prov_device_ll_client.c Func:_on_transport_registration_data Line:770 Failure retrieving data from the provisioning service
    
    Failure registering device: PROV_DEVICE_RESULT_DEV_AUTH_ERROR
    Press enter key to exit:    
    ```
    
## <a name="clean-up-resources"></a>Resources opschonen

Als u verder wilt werken met de resources die in dit artikel zijn gemaakt, kunt u deze behouden. Als u de resources niet meer wilt gebruiken, kunt u met de volgende stappen alle resources verwijderen die in dit artikel zijn gemaakt om onnodige kosten te voorkomen.

Bij de stappen die hier worden beschreven, wordt ervan uitgegaan dat u alle resources in dit artikel volgens de instructies hebt gemaakt in dezelfde resourcegroep met de naam **contoso-us-resource-group**.

> [!IMPORTANT]
> Het verwijderen van een resourcegroep kan niet ongedaan worden gemaakt. De resourcegroep en alle resources daarin worden permanent verwijderd. Zorg ervoor dat u niet per ongeluk de verkeerde resourcegroep of resources verwijdert. Als u de IoT Hub in een bestaande resourcegroep hebt gemaakt met resources die u wilt behouden, moet u alleen de IoT Hub-resource zelf verwijderen in plaats van de resourcegroep te verwijderen.
>

U verwijdert als volgt de resourcegroep op naam:

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en selecteer **Resourcegroepen**.

2. Typ in het tekstvak **Filteren op naam...** de naam van de resourcegroep die uw resources bevat, **contoso-us-resource-group**. 

3. Selecteer rechts van de resourcegroep in de lijst met resultaten **...** en vervolgens **Resourcegroep verwijderen**.

4. U wordt gevraagd om het verwijderen van de resourcegroep te bevestigen. Typ ter bevestiging nogmaals de naam van de resourcegroep. Selecteer vervolgens **Verwijderen**. Na enkele ogenblikken worden de resourcegroep en alle resources in de groep verwijderd.

## <a name="next-steps"></a>Volgende stappen

* Zie [Aangepast toewijzingsbeleid gebruiken](how-to-use-custom-allocation-policies.md) voor een gedetailleerder voorbeeld van aangepast toewijzingsbeleid.
* Zie [Concepten voor het opnieuw inrichten van IoT Hub-apparaten](concepts-device-reprovision.md) voor meer informatie over het opnieuw inrichten.
* Zie [Inrichting ongedaan maken van apparaten die eerder zijn ingericht](how-to-unprovision-devices.md) voor meer informatie over het ongedaan maken van de inrichting.