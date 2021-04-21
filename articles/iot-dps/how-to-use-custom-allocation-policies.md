---
title: Aangepast toewijzingsbeleid met Azure IoT Hub Device Provisioning Service
description: Aangepast toewijzingsbeleid gebruiken met de Azure IoT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 01/26/2021
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 1432aee341509d8a5bdc9fffe89dd9bad33fc7de
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766475"
---
# <a name="how-to-use-custom-allocation-policies"></a>Aangepast toewijzingsbeleid gebruiken

Met een aangepast toewijzingsbeleid heeft u meer controle over de wijze waarop apparaten worden toegewezen aan een IoT-hub. Dit wordt bereikt door aangepaste code in een [Azure-functie te](../azure-functions/functions-overview.md) gebruiken om apparaten toe te wijzen aan een IoT-hub. De service voor het inrichten van apparaten (Device Provisioning Service) roept de code van uw Azure-functie aan die alle relevante informatie over het apparaat en de inschrijving verschaft. De functiecode wordt uitgevoerd en retourneert de gegevens van de IoT-hub die worden gebruikt om het apparaat in te richten.

Door gebruik te maken van aangepast toewijzingsbeleid definieert u uw eigen toewijzingsbeleid wanneer het beleid dat door Device Provisioning Service wordt geleverd niet voldoet aan de vereisten van uw scenario.

Misschien wilt u bijvoorbeeld het certificaat bekijken dat door een apparaat wordt gebruikt tijdens het inrichten en het apparaat toewijzen aan een IoT-hub op basis van een certificaateigenschap. Of misschien hebt u gegevens opgeslagen in een database voor uw apparaten en moet u gegevens uit de database opvragen om te bepalen aan welke IoT-hub een apparaat moet worden toegewezen.

In dit artikel wordt een aangepast toewijzingsbeleid gedemonstreerd met behulp van een Azure-functie die is geschreven in C#. Er worden twee nieuwe IoT-hubs gemaakt die een *Contoso Toasters Division* en een *Contoso Heat Pumps Division vertegenwoordigen.* Apparaten die inrichting aanvragen, moeten een registratie-id hebben met een van de volgende achtervoegsels die moeten worden geaccepteerd voor het inrichten:

* **-contoso-tstrsd-007:** Contoso Toasters Division
* **-contoso-hpsd-088:** Contoso Heat Pumps Division

De apparaten worden ingericht op basis van een van deze vereiste achtervoegsels op de registratie-id. Deze apparaten worden gesimuleerd met behulp van een inrichtingsvoorbeeld dat is opgenomen in de [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c).

In dit artikel voert u de volgende stappen uit:

* Gebruik de Azure CLI om twee Contoso-divisie IoT-hubs te maken (**Contoso Toasters Division** en **Contoso Heat Pumps Division**)
* Een nieuwe groepsregistratie maken met behulp van een Azure-functie voor het aangepaste toewijzingsbeleid
* Maak apparaatsleutels voor twee apparaatsimulaties.
* De ontwikkelomgeving instellen voor de Azure IoT C SDK
* Simuleer de apparaten en controleer of ze zijn ingericht volgens de voorbeeldcode in het aangepaste toewijzingsbeleid

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

De volgende vereisten gelden voor een ontwikkelomgeving in Windows. Voor Linux of macOS raadpleegt u het desbetreffende gedeelte in [Uw ontwikkelomgeving voorbereiden](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) in de SDK-documentatie.

- [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 met de workload [Desktopontwikkeling met C++](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) ingeschakeld. Visual Studio 2015 en Visual Studio 2017 worden ook ondersteund.

- Meest recente versie van [Git](https://git-scm.com/download/) geïnstalleerd.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-the-provisioning-service-and-two-divisional-iot-hubs"></a>De inrichtingsservice en twee divisie-IoT-hubs maken

In deze sectie gebruikt u de Azure Cloud Shell om een inrichtingsservice en twee IoT-hubs te maken die de **Contoso Toasters Division** en de **Contoso Heat Pumps-divisie vertegenwoordigen.**

> [!TIP]
> Met de opdrachten die in dit artikel worden gebruikt, maakt u de inrichtingsservice en andere resources op de locatie VS - west. U wordt aangeraden uw resources te maken in de dichtstbijzijnde regio die Device Provisioning Service ondersteunt. U kunt een lijst met beschikbare locaties weergeven door de opdracht `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` uit te voeren of door naar de pagina [Status van Azure](https://azure.microsoft.com/status/) te gaan en te zoeken naar 'device provisioning service'. In opdrachten kunnen locaties worden opgegeven in één woord of in meerdere woorden, bijvoorbeeld: uswest, VS - west, US WEST enzovoort. De waarde is niet hoofdlettergevoelig. Als u meerdere woorden gebruikt om een locatie op te geven, voeg dan de waarde toe tussen aanhalingstekens; bijvoorbeeld: `-- location "West US"`.
>

1. Gebruik de Azure Cloud Shell om een resourcegroep te maken met de [opdracht az group create.](/cli/azure/group#az_group_create) Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

    In het volgende voorbeeld wordt een resourcegroep met de *naam contoso-us-resource-group* gemaakt in de *regio westus.* U wordt aangeraden deze groep te gebruiken voor alle resources die in dit artikel zijn gemaakt. Met deze aanpak wordt het ops schonen gemakkelijker nadat u klaar bent.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location westus
    ```

2. Gebruik de Azure Cloud Shell om een Device Provisioning Service (DPS) te maken met de [opdracht az iot dps create.](/cli/azure/iot/dps#az_iot_dps_create) De inrichtingsservice wordt toegevoegd aan *contoso-us-resource-group.*

    In het volgende voorbeeld wordt een inrichtingsservice met de *naam contoso-provisioning-service-1098* gemaakt op de *locatie VS* - west. U moet een unieke servicenaam gebruiken. Maak uw eigen achtervoegsel in de servicenaam in plaats van **1098.**

    ```azurecli-interactive 
    az iot dps create --name contoso-provisioning-service-1098 --resource-group contoso-us-resource-group --location westus
    ```

    Het volledig uitvoeren van de opdracht kan even duren.

3. Gebruik de Azure Cloud Shell om de IoT-hub **Contoso Toasters Division** te maken met de [opdracht az iot hub create.](/cli/azure/iot/hub#az_iot_hub_create) De IoT-hub wordt toegevoegd aan *contoso-us-resource-group.*

    In het volgende voorbeeld wordt een IoT-hub met de *naam contoso-toasters-hub-1098* gemaakt op de *locatie VS* - west. U moet een unieke hubnaam gebruiken. Maak uw eigen achtervoegsel in de hubnaam in plaats van **1098.** 

    > [!CAUTION]
    > Voor de voorbeeldcode van de Azure-functie voor het aangepaste toewijzingsbeleid is de subtekenreeks `-toasters-` in de naam van de hub vereist. Zorg ervoor dat u een naam gebruikt die de vereiste subtekenreeks voor broodroosters bevat.
    
    ```azurecli-interactive 
    az iot hub create --name contoso-toasters-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Het volledig uitvoeren van de opdracht kan even duren.

4. Gebruik de Azure Cloud Shell om de IoT-hub **Contoso Heat Pumps Division** te maken met de [opdracht az iot hub create.](/cli/azure/iot/hub#az_iot_hub_create) Deze IoT-hub wordt ook toegevoegd aan *contoso-us-resource-group.*

    In het volgende voorbeeld wordt een IoT-hub met de *naam contoso-heatpumps-hub-1098* gemaakt op de *locatie westus.* U moet een unieke hubnaam gebruiken. Maak uw eigen achtervoegsel in de naam van de hub in plaats van **1098.** 

    > [!CAUTION]
    > Voor de voorbeeldcode van de Azure-functie voor het aangepaste toewijzingsbeleid is de subtekenreeks `-heatpumps-` in de naam van de hub vereist. Zorg ervoor dat u een naam gebruikt die de vereiste heatpumpsubtekenreeks bevat.

    ```azurecli-interactive 
    az iot hub create --name contoso-heatpumps-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Het volledig uitvoeren van de opdracht kan even duren.

5. De IoT-hubs moeten worden gekoppeld aan de DPS-resource. 

    Voer de volgende twee opdrachten uit om de verbindingsreeksen op te halen voor de hubs die u zojuist hebt gemaakt. Vervang de namen van de hubresources door de namen die u in elke opdracht hebt gekozen:

    ```azurecli-interactive 
    hubToastersConnectionString=$(az iot hub connection-string show --hub-name contoso-toasters-hub-1098 --key primary --query connectionString -o tsv)
    hubHeatpumpsConnectionString=$(az iot hub connection-string show --hub-name contoso-heatpumps-hub-1098 --key primary --query connectionString -o tsv)
    ```

    Voer de volgende opdrachten uit om de hubs te koppelen aan de DPS-resource. Vervang de NAAM van de DPS-resource door de naam die u in elke opdracht hebt gekozen:

    ```azurecli-interactive 
    az iot dps linked-hub create --dps-name contoso-provisioning-service-1098 --resource-group contoso-us-resource-group --connection-string $hubToastersConnectionString --location westus
    az iot dps linked-hub create --dps-name contoso-provisioning-service-1098 --resource-group contoso-us-resource-group --connection-string $hubHeatpumpsConnectionString --location westus
    ```




## <a name="create-the-custom-allocation-function"></a>De functie voor aangepaste toewijzing maken

In deze sectie maakt u een Azure-functie waarmee u uw aangepaste toewijzingsbeleid implementeert. Met deze functie wordt bepaald op welke IoT-hub voor divisies een apparaat moet worden geregistreerd, op basis van of de registratie-id de tekenreeks **-contoso-tstrsd-007** of **-contoso-hpsd-088 bevat.** Ook wordt de initiële status van de apparaattweeling bepaald op basis van of het apparaat een broodrooster of een warmtepomp is.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer op uw startpagina de optie **+ Een resource maken**.

2. Typ in het zoekvak *Marketplace doorzoeken* de tekst 'Functie-app'. Selecteer **Functie-app** in de vervolgkeuzelijst en selecteer vervolgens **Maken**.

3. Voer op de pagina voor het maken van de **functie-app** onder het tabblad **Basisgegevens** de volgende instellingen in voor uw nieuwe functie-app en selecteer **Controleren en maken**:

    **Resourcegroep:** selecteer **de resourcegroep contoso-us-resource om** alle resources die in dit artikel zijn gemaakt bij elkaar te houden.

    **Naam van de functie-app**: Voer een unieke naam in voor de functie-app. In dit voorbeeld wordt **contoso-function-app-1098 gebruikt.**

    **Publiceren**: Controleer of **Code** is geselecteerd.

    **Runtimestack**: Selecteer **.NET Core** in de vervolgkeuzelijst.

    **Versie:** selecteer **3.1** in de vervolgkeuzekeuze.

    **Regio**: Selecteer dezelfde regio als uw resourcegroep. In dit voorbeeld wordt **US - west** gebruikt.

    > [!NOTE]
    > Application Insights is standaard ingeschakeld. Application Insights is niet nodig voor dit artikel, maar het kan nuttig zijn bij het begrijpen en onderzoeken van problemen die u tegenkomt bij de aangepaste toewijzing. Als u wilt, kunt u Application Insights uitschakelen door het tabblad **Controle** te selecteren en vervolgens **Nee** voor **Application Insights inschakelen**.

    ![Een app voor een Azure-functie maken om de functie voor aangepaste toewijzing te hosten](./media/how-to-use-custom-allocation-policies/create-function-app.png)

4. Selecteer **Maken** op de pagina **Overzicht** om de functie-app te maken. De implementatie kan enkele minuten duren. Wanneer de implementatie is voltooid, selecteert u **Ga naar resource**.

5. Klik in het linkerdeelvenster van de **pagina** Overzicht van de functie-app op **Functies** en vervolgens op + **Toevoegen om** een nieuwe functie toe te voegen.

6. Klik op **de pagina** Functie toevoegen op **HTTP-trigger** en klik vervolgens op **de knop** Toevoegen.

7. Klik op de volgende pagina op **Code + Testen.** Hiermee kunt u de code voor de functie met de **naam HttpTrigger1 bewerken.** Het **codebestand run.csx** moet worden geopend voor bewerking.

8. Verwijs naar de vereiste NuGet-pakketten. Voor het maken van de eerste apparaat-dubbel maakt de aangepaste toewijzingsfunctie gebruik van klassen die zijn gedefinieerd in twee NuGet-pakketten die in de hostingomgeving moeten worden geladen. Met Azure Functions wordt naar NuGet-pakketten verwezen met behulp van een *function.proj-bestand.* In deze stap gaat u een *function.proj-bestand* opslaan en uploaden voor de vereiste assemblies.  Zie Voor meer informatie [NuGet-pakketten gebruiken met Azure Functions](../azure-functions/functions-reference-csharp.md#using-nuget-packages).

    1. Kopieer de volgende regels in uw favoriete editor en sla het bestand op uw computer op als *function.proj.*

        ```xml
        <Project Sdk="Microsoft.NET.Sdk">  
            <PropertyGroup>  
                <TargetFramework>netstandard2.0</TargetFramework>  
            </PropertyGroup>  
            <ItemGroup>  
                <PackageReference Include="Microsoft.Azure.Devices.Provisioning.Service" Version="1.16.3" />
                <PackageReference Include="Microsoft.Azure.Devices.Shared" Version="1.27.0" />
            </ItemGroup>  
        </Project>
        ```

    2. Klik op **de knop Uploaden** boven de code-editor om het bestand *function.proj te* uploaden. Na het uploaden selecteert u het bestand in de code-editor met behulp van de vervolgkeuzevak om de inhoud te controleren.

9. Zorg ervoor *dat run.csx* voor **HttpTrigger1** is geselecteerd in de code-editor. Vervang de code voor de **functie HttpTrigger1** door de volgende code en selecteer **Opslaan:**

    ```csharp
    #r "Newtonsoft.Json"

    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    using Microsoft.Azure.Devices.Shared;               // For TwinCollection
    using Microsoft.Azure.Devices.Provisioning.Service; // For TwinState

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
                // This is a Contoso Toaster Model 007
                if (regId.Contains("-contoso-tstrsd-007"))
                {
                    //Find the "-toasters-" IoT hub configured on the enrollment
                    foreach(string hubString in hubs)
                    {
                        if (hubString.Contains("-toasters-"))
                            obj.iotHubHostName = hubString;
                    }

                    if (obj.iotHubHostName == null)
                    {
                        message = "No toasters hub found for the enrollment.";
                        log.LogInformation(message);
                        fail = true;
                    }
                    else
                    {
                        // Specify the initial tags for the device.
                        TwinCollection tags = new TwinCollection();
                        tags["deviceType"] = "toaster";

                        // Specify the initial desired properties for the device.
                        TwinCollection properties = new TwinCollection();
                        properties["state"] = "ready";
                        properties["darknessSetting"] = "medium";

                        // Add the initial twin state to the response.
                        TwinState twinState = new TwinState(tags, properties);
                        obj.initialTwin = twinState;
                    }
                }
                // This is a Contoso Heat pump Model 008
                else if (regId.Contains("-contoso-hpsd-088"))
                {
                    //Find the "-heatpumps-" IoT hub configured on the enrollment
                    foreach(string hubString in hubs)
                    {
                        if (hubString.Contains("-heatpumps-"))
                            obj.iotHubHostName = hubString;
                    }

                    if (obj.iotHubHostName == null)
                    {
                        message = "No heat pumps hub found for the enrollment.";
                        log.LogInformation(message);
                        fail = true;
                    }
                    else
                    {
                        // Specify the initial tags for the device.
                        TwinCollection tags = new TwinCollection();
                        tags["deviceType"] = "heatpump";

                        // Specify the initial desired properties for the device.
                        TwinCollection properties = new TwinCollection();
                        properties["state"] = "on";
                        properties["temperatureSetting"] = "65";

                        // Add the initial twin state to the response.
                        TwinState twinState = new TwinState(tags, properties);
                        obj.initialTwin = twinState;
                    }
                }
                // Unrecognized device.
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
        public TwinState initialTwin {get; set;}
    }
    ```

## <a name="create-the-enrollment"></a>De registratie maken

In deze sectie maakt u een nieuwe registratiegroep die gebruikmaakt van het aangepaste toewijzingsbeleid. Ter vereenvoudiging wordt in dit artikel bij de registratie gebruikgemaakt van een [verklaring met symmetrische sleutels](concepts-symmetric-key-attestation.md). Voor een veiligere oplossing kunt u gebruikmaken van de [verklaring met het X.509-certificaat](concepts-x509-attestation.md) met een vertrouwensketen.

1. Open in [Azure Portal](https://portal.azure.com) de inrichtingsservice.

2. Selecteer **Registraties beheren** in het linkerdeelvenster en selecteer vervolgens bovenaan de pagina de knop **Registratiegroep toevoegen**.

3. Voer **in Registratiegroep toevoegen** de volgende gegevens in en selecteer de **knop** Opslaan.

    **Groepsnaam:** voer **contoso-custom-allocated-devices in.**

    **Attestation-type:** selecteer **Symmetrische sleutel.**

    **Sleutels automatisch genereren:** dit selectievakje moet al zijn ingeschakeld.

    **Selecteer hoe u apparaten wilt toewijzen aan hubs:** selecteer **Aangepast (Azure-functie gebruiken).**

    **Abonnement:** selecteer het abonnement waarin u uw Azure-functie hebt gemaakt.

    **Functie-app:** selecteer uw functie-app op naam. **in dit voorbeeld is contoso-function-app-1098** gebruikt.

    **Functie:** selecteer de **functie HttpTrigger1.**

    ![Registratiegroep voor de aangepaste toewijzing toevoegen voor de verklaring met symmetrische sleutels](./media/how-to-use-custom-allocation-policies/create-custom-allocation-enrollment.png)

4. Nadat u de registratie hebt opgeslagen, opent u deze opnieuw en noteert u de **primaire sleutel**. U moet de registratie eerst opslaan voordat de sleutels kunnen worden gegenereerd. Deze sleutel wordt later gebruikt voor het genereren van unieke apparaatsleutels voor gesimuleerde apparaten.

## <a name="derive-unique-device-keys"></a>Unieke apparaatsleutels afleiden

In deze sectie maakt u twee unieke apparaatsleutels. Eén sleutel wordt gebruikt voor een gesimuleerde broodrooster. De andere sleutel wordt gebruikt voor een gesimuleerde warmtepomp.

Voor het genereren van de  apparaatsleutel gebruikt u de primaire sleutel die u eerder hebt genoteerd om de [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) van de apparaatregistratie-id voor elk apparaat te berekenen en het resultaat te converteren naar de Base64-indeling. Voor meer informatie over het maken van afgeleide apparaatsleutels bij registratiegroepen, raadpleegt u de sectie over groepsregistraties van [Verklaring met symmetrische sleutels](concepts-symmetric-key-attestation.md).

Gebruik voor het voorbeeld in dit artikel de volgende twee apparaatregistratie-ID's en bereken een apparaatsleutel voor beide apparaten. Beide registratie-ID's hebben een geldig achtervoegsel om te werken met de voorbeeldcode voor het aangepaste toewijzingsbeleid:

* **breakroom499-contoso-tstrsd-007**
* **mainbuilding167-contoso-hpsd-088**


# <a name="windows"></a>[Windows](#tab/windows)

Als u een Windows-werkstation gebruikt, kunt u PowerShell gebruiken om de afgeleide apparaatsleutel te genereren, zoals wordt getoond in het volgende voorbeeld.

Vervang de waarde van **KEY door** de primaire **sleutel die** u eerder hebt genoteerd.

```powershell
$KEY='oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA=='

$REG_ID1='breakroom499-contoso-tstrsd-007'
$REG_ID2='mainbuilding167-contoso-hpsd-088'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig1 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID1))
$sig2 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID2))
$derivedkey1 = [Convert]::ToBase64String($sig1)
$derivedkey2 = [Convert]::ToBase64String($sig2)

echo "`n`n$REG_ID1 : $derivedkey1`n$REG_ID2 : $derivedkey2`n`n"
```

```powershell
breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
```

# <a name="linux"></a>[Linux](#tab/linux)

Als u een Linux-werkstation gebruikt, kunt u openssl gebruiken om uw afgeleide apparaatsleutels te genereren, zoals wordt weergegeven in het volgende voorbeeld.

Vervang de waarde van **KEY door** de primaire **sleutel die** u eerder hebt genoteerd.

```bash
KEY=oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA==

REG_ID1=breakroom499-contoso-tstrsd-007
REG_ID2=mainbuilding167-contoso-hpsd-088

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
devkey1=$(echo -n $REG_ID1 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)
devkey2=$(echo -n $REG_ID2 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)

echo -e $"\n\n$REG_ID1 : $devkey1\n$REG_ID2 : $devkey2\n\n"
```

```bash
breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
```

---

De gesimuleerde apparaten gebruiken de afgeleide apparaatsleutels bij elke registratie-id om attestation met symmetrische sleutels uit te voeren.

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Een ontwikkelomgeving voorbereiden voor de Azure IoT C-SDK

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

6. Klik met de rechtermuisknop op het **prov\_dev\_client\_sample**-project en selecteer **Set as Startup Project**.

### <a name="simulate-the-contoso-toaster-device"></a>Het Contoso-broodrooster simuleren

1. Als u het broodrooster wilt simuleren, zoekt u de aanroep naar `prov_dev_set_symmetric_key_info()` in **prov\_dev\_client\_sample.c** waarbij een opmerking is geplaatst.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Verwijder de opmerkingen bij de functieaanroep en vervang de waarden voor de tijdelijke aanduiding (inclusief de punthaken) door de registratie-id van het broodrooster en de afgeleide apparaatsleutel die u eerder hebt gegenereerd. De sleutelwaarde **JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=** die hieronder wordt weergegeven, dient alleen als voorbeeld.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("breakroom499-contoso-tstrsd-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```

    Sla het bestand op.

2. Selecteer in het menu van Visual Studio de optie **Debug** > **Start without debugging** om de oplossing uit te voeren. Selecteer **Yes** in de prompt voor het opnieuw maken van het project om het project opnieuw te maken voordat het wordt uitgevoerd.

    De volgende uitvoer is een voorbeeld van het gesimuleerde broodrooster dat is opgestart en verbinding maakt met het inrichtingsservice-exemplaar dat door het aangepaste toewijzingsbeleid aan de IoT-hub van de broodroosters moet worden toegewezen:

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: breakroom499-contoso-tstrsd-007

    Press enter key to exit:
    ```

### <a name="simulate-the-contoso-heat-pump-device"></a>De Contoso-warmtepomp simuleren

1. Als u de warmtepomp wilt simuleren, werkt u de aanroep van `prov_dev_set_symmetric_key_info()` weer bij naar **prov\_dev\_client\_sample.c** met de registratie-id van de warmtepomp en de afgeleide apparaatsleutel die u eerder hebt gegenereerd. De sleutelwaarde **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=** die hieronder wordt weergegeven, dient alleen als voorbeeld.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("mainbuilding167-contoso-hpsd-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```

    Sla het bestand op.

2. Selecteer in het menu van Visual Studio de optie **Debug** > **Start without debugging** om de oplossing uit te voeren. Selecteer **Yes** in de prompt voor het opnieuw maken van het project om het project opnieuw te maken voordat het wordt uitgevoerd.

    De volgende uitvoer is een voorbeeld van het gesimuleerde warmtepompapparaat dat is opgestart en verbinding maakt met het inrichtingsservice-exemplaar dat door het aangepaste toewijzingsbeleid aan de IoT-hub van Contoso-warmtepompen moet worden toegewezen:

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-heatpumps-hub-1098.azure-devices.net, deviceId: mainbuilding167-contoso-hpsd-088

    Press enter key to exit:
    ```

## <a name="troubleshooting-custom-allocation-policies"></a>Problemen met aangepast toewijzingsbeleid oplossen

De volgende tabel bevat verwachte scenario's en de foutcodes van de resultaten die u mogelijk ontvangt. Gebruik deze tabel om problemen met aangepast toewijzingsbeleid op te lossen met uw Azure Functions.

| Scenario | Registratieresultaat van Provisioning Service | Resultaten van inrichtings-SDK |
| -------- | --------------------------------------------- | ------------------------ |
| De webhook retourneert 200 OK, met 'iotHubHostName' ingesteld op een geldige hostnaam voor de IoT-hub | Resultaatstatus: Toegewezen  | SDK retourneert PROV_DEVICE_RESULT_OK hubgegevens |
| De webhook retourneert 200 OK met 'iotHubHostName' in het antwoord, maar ingesteld op een lege tekenreeks of null | Resultaatstatus: Mislukt<br><br> Foutcode: CustomAllocationIotHubNotSpecified (400208) | SDK retourneert PROV_DEVICE_RESULT_HUB_NOT_SPECIFIED |
| De webhook retourneert 401 Niet geautoriseerd | Resultaatstatus: Mislukt<br><br>Foutcode: CustomAllocationUnauthorizedAccess (400209) | SDK retourneert PROV_DEVICE_RESULT_UNAUTHORIZED |
| Er is een afzonderlijke inschrijving gemaakt om het apparaat uit te schakelen | Resultaatstatus: Uitgeschakeld | SDK retourneert PROV_DEVICE_RESULT_DISABLED |
| De webhook retourneert foutcode >= 429 | Dps-orchestration zal het een aantal keer opnieuw proberen. Het beleid voor opnieuw proberen is momenteel:<br><br>&nbsp;&nbsp;- Aantal nieuwe poging: 10<br>&nbsp;&nbsp;- Eerste interval: 1s<br>&nbsp;&nbsp;- Verhoging: negens | SDK negeert de fout en verstuurt een ander get-statusbericht in de opgegeven tijd |
| De webhook retourneert alle andere statuscodes | Resultaatstatus: Mislukt<br><br>Foutcode: CustomAllocationFailed (400207) | SDK retourneert PROV_DEVICE_RESULT_DEV_AUTH_ERROR |

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

* Zie de concepten voor het opnieuw in IoT Hub inrichting van apparaten voor meer informatie over [het opnieuw invisioneren van apparaten](concepts-device-reprovision.md) 
* Zie Deprovisioning van apparaten die eerder automatisch zijn inrichtingen, voor meer informatie over het in deprovisioneren van apparaten die [eerder zijn in gebruik.](how-to-unprovision-devices.md)