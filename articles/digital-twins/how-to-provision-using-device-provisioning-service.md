---
title: Apparaten automatisch beheren met Device Provisioning Service
titleSuffix: Azure Digital Twins
description: Zie hoe u een geautomatiseerd proces in kunt stellen om IoT-apparaten in te Azure Digital Twins met behulp van Device Provisioning Service (DPS).
author: baanders
ms.author: baanders
ms.date: 3/21/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 51b5714f9009cbe48aa49c6a04a1434cec12396e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790685"
---
# <a name="auto-manage-devices-in-azure-digital-twins-using-device-provisioning-service-dps"></a>Apparaten automatisch beheren in Azure Digital Twins met Device Provisioning Service (DPS)

In dit artikel leert u hoe u Azure Digital Twins integreert met [Device Provisioning Service (DPS).](../iot-dps/about-iot-dps.md)

Met de oplossing die in dit artikel wordt **** beschreven, kunt u het proces voor het inrichten en IoT Hub apparaten in Azure Digital Twins automatiseren met behulp van Device Provisioning Service. **** 

Zie de sectie  Apparaatlevenscyclus van de documentatie over apparaatbeheer van IoT Hub voor meer informatie over [  ](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) de fasen voor het inrichten en in gebruik stellen van apparaten en voor een beter begrip van de algemene fasen voor apparaatbeheer die gemeenschappelijk zijn voor alle IoT-projecten van het bedrijf. 

## <a name="prerequisites"></a>Vereisten

Voordat u de inrichting kunt instellen, moet u het volgende instellen:
* een **Azure Digital Twins-exemplaar**. Volg de instructies in Instructies: Een exemplaar en verificatie instellen om een Azure Digital [*Twins-exemplaar*](how-to-set-up-instance-portal.md) te maken. Verzamel de **_hostnaam_** van het exemplaar in de Azure Portal ([instructies](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).
* een **IoT-hub**. Zie de sectie Een IoT Hub maken van *IoT Hub* [quickstart voor instructies.](../iot-hub/quickstart-send-telemetry-cli.md)
* een [**Azure-functie**](../azure-functions/functions-overview.md) die gegevens van digitale tweelingen bij werkt op basis van IoT Hub gegevens. Volg de instructies in [*Instructies: IoT-hubgegevens opnemen om*](how-to-ingest-iot-hub-data.md) deze Azure-functie te maken. Verzamel de **_functienaam om_** deze in dit artikel te gebruiken.

In dit voorbeeld wordt ook een **apparaatsimulator** gebruikt die het inrichten met device provisioning service omvat. De apparaatsimulator bevindt zich [hier: Azure Digital Twins en IoT Hub Integration Sample](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/). Haal het voorbeeldproject op uw computer op door naar de voorbeeldkoppeling te gaan en de knop Bladeren in **code** onder de titel te selecteren. Hiermee gaat u naar de GitHub-opslagplaats voor het voorbeeld, die u als een kunt *downloaden. Zip-bestand* door de knop **Code en** ZIP downloaden **te selecteren.** 

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/download-repo-zip.png" alt-text="Schermopname van de opslagplaats digital-twins-iothub-integration op GitHub. De knop Code is geselecteerd. Er wordt een klein dialoogvenster weergegeven waarin de knop ZIP downloaden is gemarkeerd." lightbox="media/how-to-provision-using-device-provisioning-service/download-repo-zip.png":::

Los de gedownloade map uit.

U moet deze [**Node.js**](https://nodejs.org/download) op uw computer. De apparaatsimulator is **gebaseerdNode.js**, versie 10.0.x of hoger.

## <a name="solution-architecture"></a>Architectuur voor de oplossing

In de onderstaande afbeelding ziet u de architectuur van deze oplossing met behulp Azure Digital Twins device provisioning service. U ziet zowel de stroom voor het inrichten van het apparaat als de stroom voor het in gebruik stellen van apparaten.

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/flows.png" alt-text="Diagram van apparaten en verschillende Azure-services in een end-to-end scenario. Gegevens stromen heen en weer tussen een thermostaatapparaat en DPS. Gegevens stromen ook van DPS naar IoT Hub en Azure Digital Twins via een Azure-functie met het label 'Toewijzing'. Gegevens van een handmatige actie 'Apparaat verwijderen' stromen door IoT Hub > Event Hubs > Azure Functions > Azure Digital Twins." lightbox="media/how-to-provision-using-device-provisioning-service/flows.png":::

Dit artikel is onderverdeeld in twee secties:
* [*Apparaat automatisch inrichten met Device Provisioning Service*](#auto-provision-device-using-device-provisioning-service)
* [*Apparaat automatisch uit gebruik te IoT Hub levenscyclusgebeurtenissen*](#auto-retire-device-using-iot-hub-lifecycle-events)

Zie de afzonderlijke secties verder in het artikel voor een gedetailleerde uitleg van elke stap in de architectuur.

## <a name="auto-provision-device-using-device-provisioning-service"></a>Apparaat automatisch inrichten met Device Provisioning Service

In deze sectie koppelt u Device Provisioning Service aan Azure Digital Twins automatisch inrichten van apparaten via het onderstaande pad. Dit is een fragment van de volledige architectuur die eerder [is weergegeven.](#solution-architecture)

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/provision.png" alt-text="Diagram van inrichtingsstroom: een fragment van het architectuurdiagram van de oplossing, met secties voor het labelen van getallen van de stroom. Gegevens stromen heen en weer tussen een thermostaatapparaat en DPS (1 voor device > DPS en 5 voor DPS > apparaat). Gegevens stromen ook van DPS naar IoT Hub (4) en naar Azure Digital Twins (3) via een Azure-functie met het label Toewijzing (2)." lightbox="media/how-to-provision-using-device-provisioning-service/provision.png":::

Hier is een beschrijving van de processtroom:
1. Het apparaat neemt contact op met het DPS-eindpunt en verstrekt identificatiegegevens om de identiteit te bewijzen.
2. DPS valideert de apparaat-id door de registratie-id en -sleutel te valideren op basis van de registratielijst en roept een [Azure-functie](../azure-functions/functions-overview.md) aan om de toewijzing uit te voeren.
3. De Azure-functie maakt een nieuwe [tweeling](concepts-twins-graph.md) in Azure Digital Twins voor het apparaat. De tweeling heeft dezelfde naam als de registratie-id van **het apparaat.**
4. DPS registreert het apparaat bij een IoT-hub en vult de gewenste dubbel-status van het apparaat in.
5. De IoT-hub retourneert informatie over de apparaat-id en de verbindingsgegevens van de IoT-hub naar het apparaat. Het apparaat kan nu verbinding maken met de IoT-hub.

In de volgende secties worden de stappen voor het instellen van deze apparaatstroom voor automatisch inrichten doorlopen.

### <a name="create-a-device-provisioning-service"></a>Een Device Provisioning Service maken

Wanneer een nieuw apparaat wordt ingericht met Device Provisioning Service, kan er een nieuwe dubbel voor dat apparaat worden gemaakt in Azure Digital Twins met dezelfde naam als de registratie-id.

Maak een Device Provisioning Service-exemplaar dat wordt gebruikt voor het inrichten van IoT-apparaten. U kunt de onderstaande Azure CLI-instructies gebruiken of de Azure Portal: [*Quickstart: De*](../iot-dps/quick-setup-auto-provision.md)IoT Hub Device Provisioning Service instellen met de Azure Portal .

Met de volgende Azure CLI-opdracht maakt u een Device Provisioning Service. U moet een Device Provisioning Service-naam, resourcegroep en regio opgeven. Als u wilt zien in welke regio's Device Provisioning Service wordt ondersteund, gaat u naar [*Azure-producten die beschikbaar zijn per regio.*](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub)
De opdracht kan worden uitgevoerd in [Cloud Shell](https://shell.azure.com)of lokaal als u de Azure CLI [op uw computer hebt geïnstalleerd.](/cli/azure/install-azure-cli)

```azurecli-interactive
az iot dps create --name <Device Provisioning Service name> --resource-group <resource group name> --location <region>
```

### <a name="add-a-function-to-use-with-device-provisioning-service"></a>Een functie toevoegen voor gebruik met Device Provisioning Service

In uw functie-app-project dat u hebt gemaakt in de sectie [vereisten,](#prerequisites) maakt u een nieuwe functie voor gebruik met device provisioning service. Deze functie wordt gebruikt door de Device Provisioning Service in een aangepast toewijzingsbeleid [om](../iot-dps/how-to-use-custom-allocation-policies.md) een nieuw apparaat in terichten.

Open eerst het functie-app-project in Visual Studio computer en volg de onderstaande stappen.

#### <a name="step-1-add-a-new-function"></a>Stap 1: een nieuwe functie toevoegen 

Voeg een nieuwe functie van het type *HTTP-trigger toe* aan het functie-app-project in Visual Studio.

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/add-http-trigger-function-visual-studio.png" alt-text="Schermopname van de Visual Studio azure-functie van het type HTTP-trigger toe te voegen aan uw functie-app-project." lightbox="media/how-to-provision-using-device-provisioning-service/add-http-trigger-function-visual-studio.png":::

#### <a name="step-2-fill-in-function-code"></a>Stap 2: functiecode invullen

Voeg een nieuw NuGet-pakket toe aan het project: [Microsoft.Azure.Devices.Provisioning.Service.](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) Mogelijk moet u ook meer pakketten aan uw project toevoegen als de pakketten die in de code worden gebruikt, nog geen deel uitmaken van het project.

Plak in het zojuist gemaakte functiecodebestand de volgende code, wijzig de naam van de functie in *DpsAdtAllocationFunc.cs* en sla het bestand op.

:::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DpsAdtAllocationFunc.cs":::

#### <a name="step-3-publish-the-function-app-to-azure"></a>Stap 3: de functie-app publiceren naar Azure

Publiceer het project *met de functie DpsAdtAllocationFunc.cs* naar de functie-app in Azure.

[!INCLUDE [digital-twins-publish-and-configure-function-app.md](../../includes/digital-twins-publish-and-configure-function-app.md)]

### <a name="create-device-provisioning-enrollment"></a>Device Provisioning-inschrijving maken

Vervolgens moet u een inschrijving maken in Device Provisioning Service met behulp van een **aangepaste toewijzingsfunctie**. Volg de instructies hiervoor in de sectie [*De*](../iot-dps/how-to-use-custom-allocation-policies.md#create-the-enrollment) inschrijving maken van het artikel Aangepast toewijzingsbeleid in de documentatie van Device Provisioning Service.

Zorg ervoor dat u tijdens het doorstromen de volgende opties selecteert om de inschrijving te koppelen aan de functie die u zojuist hebt gemaakt.

* **Selecteer hoe u apparaten wilt toewijzen aan hubs:** Aangepast (Azure-functie gebruiken).
* **Selecteer de IoT-hubs aan welke deze groep kan worden toegewezen:** Kies de naam van uw IoT-hub of selecteer de knop Een nieuwe *IoT-hub* koppelen en kies uw IoT-hub in de vervolgkeuzekeuze.

Kies vervolgens de *knop Een nieuwe functie selecteren* om uw functie-app te koppelen aan de registratiegroep. Vul vervolgens de volgende waarden in:

* **Abonnement:** uw Azure-abonnement wordt automatisch ingevuld. Zorg ervoor dat dit het juiste abonnement is.
* **Functie-app:** kies de naam van uw functie-app.
* **Functie:** kies DpsAdtAllocationFunc.

Sla uw gegevens op.                  

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/link-enrollment-group-to-iot-hub-and-function-app.png" alt-text="Schermopname van het venster Met details van de inschrijvingsgroep Voor het selecteren van aangepaste taken (Azure-functie gebruiken) en de naam van uw IoT-hub in de secties Selecteren hoe u apparaten wilt toewijzen aan hubs en Selecteer de IoT-hubs aan wie deze groep kan worden toegewezen. Selecteer ook uw abonnement, functie-app in de vervolgkeuzekeuze en zorg ervoor dat u DpsAdtAllocationFunc selecteert." lightbox="media/how-to-provision-using-device-provisioning-service/link-enrollment-group-to-iot-hub-and-function-app.png":::

Nadat de inschrijving is gemaakt, wordt de **primaire** sleutel voor de inschrijving later gebruikt om de apparaatsimulator voor dit artikel te configureren.

### <a name="set-up-the-device-simulator"></a>De apparaatsimulator instellen

In dit voorbeeld wordt een apparaatsimulator gebruikt die het inrichten met device provisioning service omvat. De apparaatsimulator bevindt zich in [Azure Digital Twins en IoT Hub Integration Sample](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/) dat u hebt gedownload in de [sectie](#prerequisites) Vereisten.

#### <a name="upload-the-model"></a>Het model uploaden

De apparaatsimulator is een thermostaatapparaat dat gebruikmaakt van het model met de volgende id: `dtmi:contosocom:DigitalTwins:Thermostat;1` . U moet dit model uploaden naar Azure Digital Twins voordat u een dubbel van dit type voor het apparaat kunt maken.

[!INCLUDE [digital-twins-thermostat-model-upload.md](../../includes/digital-twins-thermostat-model-upload.md)]

Zie [*How-to: Manage models*](how-to-manage-model.md#upload-models)(Modellen beheren) voor meer informatie over modellen.

#### <a name="configure-and-run-the-simulator"></a>De simulator configureren en uitvoeren

Navigeer in het opdrachtvenster naar de gedownloade *voorbeeld-Azure Digital Twins en IoT Hub Integration* die u eerder hebt uitgepakt en ga vervolgens naar de map *device-simulator.* Installeer vervolgens de afhankelijkheden voor het project met behulp van de volgende opdracht:

```cmd
npm install
```

Kopieer vervolgens in de map van de apparaatsimulator het bestand .env.template naar een nieuw bestand met de naam .env en verzamel de volgende waarden om de instellingen in te vullen:

* PROVISIONING_IDSCOPE: om deze waarde op te halen, gaat u naar de device provisioning service in de [Azure Portal](https://portal.azure.com/)en selecteert u Overzicht *in* de menuopties en gaat u naar het veld *Id-bereik.*

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/id-scope.png" alt-text="Schermopname van de Azure Portal van de overzichtspagina voor apparaatinrichting om de waarde voor id-bereik te kopiëren." lightbox="media/how-to-provision-using-device-provisioning-service/id-scope.png":::

* PROVISIONING_REGISTRATION_ID: u kunt een registratie-id voor uw apparaat kiezen.
* ADT_MODEL_ID: `dtmi:contosocom:DigitalTwins:Thermostat;1`
* PROVISIONING_SYMMETRIC_KEY: Dit is de primaire sleutel voor de inschrijving die u eerder hebt ingesteld. Om deze waarde opnieuw op te halen, gaat u naar de device provisioning service in de Azure Portal, selecteert u Inschrijvingen beheren en selecteert u vervolgens de registratiegroep die u eerder hebt gemaakt en kopieert u de *Primaire sleutel*.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/sas-primary-key.png" alt-text="Schermopname van de Azure Portal van de pagina inschrijvingen beheren door de Device Provisioning Service om de waarde van de primaire SAS-sleutel te kopiëren." lightbox="media/how-to-provision-using-device-provisioning-service/sas-primary-key.png":::

Gebruik nu de bovenstaande waarden om de bestandsinstellingen voor .env bij te werken.

```cmd
PROVISIONING_HOST = "global.azure-devices-provisioning.net"
PROVISIONING_IDSCOPE = "<Device Provisioning Service Scope ID>"
PROVISIONING_REGISTRATION_ID = "<Device Registration ID>"
ADT_MODEL_ID = "dtmi:contosocom:DigitalTwins:Thermostat;1"
PROVISIONING_SYMMETRIC_KEY = "<Device Provisioning Service enrollment primary SAS key>"
```

Sla het bestand op en sluit het.

### <a name="start-running-the-device-simulator"></a>Start met het uitvoeren van de apparaatsimulator

Start de apparaatsimulator nog steeds in de map *device-simulator* in het opdrachtvenster met behulp van de volgende opdracht:

```cmd
node .\adt_custom_register.js
```

U ziet dat het apparaat wordt geregistreerd en verbonden met IoT Hub en vervolgens berichten begint te verzenden.
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/output.png" alt-text="Schermopname van de Opdrachtvenster apparaatregistratie en het verzenden van berichten" lightbox="media/how-to-provision-using-device-provisioning-service/output.png":::

### <a name="validate"></a>Valideren

Als gevolg van de stroom die u in dit artikel hebt ingesteld, wordt het apparaat automatisch geregistreerd in Azure Digital Twins. Gebruik de volgende [Azure Digital Twins CLI-opdracht](how-to-use-cli.md) om de dubbel van het apparaat te vinden in Azure Digital Twins exemplaar dat u hebt gemaakt.

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id "<Device Registration ID>"
```

Als het goed is, ziet u dat de dubbel van het apparaat wordt gevonden in Azure Digital Twins exemplaar.
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/show-provisioned-twin.png" alt-text="Schermopname van de Opdrachtvenster met de zojuist gemaakte tweeling." lightbox="media/how-to-provision-using-device-provisioning-service/show-provisioned-twin.png":::

## <a name="auto-retire-device-using-iot-hub-lifecycle-events"></a>Apparaat automatisch uit gebruik maken met behulp IoT Hub levenscyclusgebeurtenissen

In deze sectie koppelt u de levenscyclusgebeurtenissen IoT Hub aan Azure Digital Twins apparaten automatisch uit gebruik te kunnen maken via het onderstaande pad. Dit is een fragment van de volledige architectuur die eerder [is weergegeven.](#solution-architecture)

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/retire.png" alt-text="Diagram van de apparaatstroom Apparaat uit gebruik maken: een fragment van het architectuurdiagram van de oplossing, met secties van de stroom met het labelen van getallen. Het thermostaatapparaat wordt weergegeven zonder verbindingen met de Azure-services in het diagram. Gegevens van een handmatige actie Apparaat verwijderen stromen via IoT Hub (1) > Event Hubs (2) > Azure Functions > Azure Digital Twins (3)." lightbox="media/how-to-provision-using-device-provisioning-service/retire.png":::

Hier is een beschrijving van de processtroom:
1. Een extern of handmatig proces activeert het verwijderen van een apparaat in IoT Hub.
2. IoT Hub verwijdert het apparaat en genereert [](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) een levenscyclusgebeurtenis van het apparaat die wordt doorgeleid naar een [Event Hub.](../event-hubs/event-hubs-about.md)
3. Een Azure-functie verwijdert de dubbel van het apparaat in Azure Digital Twins.

De volgende secties doorlopen de stappen voor het instellen van deze apparaatstroom voor automatisch buiten gebruik stellen.

### <a name="create-an-event-hub"></a>Een Event Hub maken

Vervolgens maakt u een Azure Event [Hub](../event-hubs/event-hubs-about.md) voor het ontvangen van IoT Hub levenscyclusgebeurtenissen. 

Volg de stappen die worden beschreven in de quickstart [*Een Event Hub*](../event-hubs/event-hubs-create.md) maken. Noem de levenscyclus van uw Event *Hubevents.* In de volgende secties gebruikt u deze event hub-naam wanneer u IoT Hub route en een Azure-functie in te stellen.

In de onderstaande schermopname ziet u hoe de Event Hub wordt gemaakt.
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/create-event-hub-lifecycle-events.png" alt-text="Schermopname van het Azure Portal voor het maken van een Event Hub met de naam lifecycleevents." lightbox="media/how-to-provision-using-device-provisioning-service/create-event-hub-lifecycle-events.png":::

#### <a name="create-sas-policy-for-your-event-hub"></a>SAS-beleid maken voor uw Event Hub

Vervolgens moet u een [SAS-beleid (Shared Access Signature)](../event-hubs/authorize-access-shared-access-signature.md) maken om de Event Hub te configureren met uw functie-app.
Dit wilt doen
1. Navigeer naar de Event Hub die u zojuist hebt gemaakt in de Azure Portal **selecteer** Gedeeld toegangsbeleid in de menuopties aan de linkerkant.
2. Selecteer **Toevoegen**. Voer in *het venster SAS-beleid* toevoegen dat wordt geopend een beleidsnaam van uw keuze in en schakel *het* selectievakje Luisteren in.
3. Selecteer **Maken**.
    
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/add-event-hub-sas-policy.png" alt-text="Schermopname van de Azure Portal een SAS-beleid voor event hub toe te voegen." lightbox="media/how-to-provision-using-device-provisioning-service/add-event-hub-sas-policy.png":::

#### <a name="configure-event-hub-with-function-app"></a>Event Hub configureren met functie-app

Configureer vervolgens de Azure-functie-app die u hebt ingesteld in de sectie [Vereisten](#prerequisites) om te werken met uw nieuwe Event Hub. U doet dit door een omgevingsvariabele in te stellen in de functie-app met de connection string.

1. Open het beleid dat u zojuist hebt gemaakt en kopieer de waarde **verbindingsreeks-primaire sleutel.**

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/event-hub-sas-policy-connection-string.png" alt-text="Schermopname van de Azure Portal de primaire sleutel connection string kopiëren." lightbox="media/how-to-provision-using-device-provisioning-service/event-hub-sas-policy-connection-string.png":::

2. Voeg de connection string toe als een variabele in de instellingen van de functie-app met de volgende Azure CLI-opdracht. De opdracht kan worden uitgevoerd in [Cloud Shell](https://shell.azure.com)of lokaal als u de Azure CLI op [uw computer hebt geïnstalleerd.](/cli/azure/install-azure-cli)

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EVENTHUB_CONNECTIONSTRING=<Event Hubs SAS connection string Listen>" -g <resource group> -n <your App Service (function app) name>
    ```

### <a name="add-a-function-to-retire-with-iot-hub-lifecycle-events"></a>Een functie toevoegen om deze uit te IoT Hub levenscyclusgebeurtenissen

In uw functie-app-project [](#prerequisites) dat u hebt gemaakt in de sectie vereisten, maakt u een nieuwe functie om een bestaand apparaat buiten gebruik te stellen met behulp IoT Hub levenscyclusgebeurtenissen.

Zie niet-telemetriegebeurtenissen voor [*IoT Hub over levenscyclusgebeurtenissen.*](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events) Zie voor meer informatie over het Event Hubs met Azure-functies [*Azure Event Hubs trigger voor Azure Functions.*](../azure-functions/functions-bindings-event-hubs-trigger.md)

Open eerst het functie-app-project in Visual Studio computer en volg de onderstaande stappen.

#### <a name="step-1-add-a-new-function"></a>Stap 1: een nieuwe functie toevoegen
     
Voeg een nieuwe functie van het type *Event Hub-trigger toe* aan het functie-app-project in Visual Studio.

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/create-event-hub-trigger-function.png" alt-text="Schermopname van het Visual Studio om een Azure-functie van het type Event Hub-trigger toe te voegen aan uw functie-app-project." lightbox="media/how-to-provision-using-device-provisioning-service/create-event-hub-trigger-function.png":::

#### <a name="step-2-fill-in-function-code"></a>Stap 2: functiecode invullen

Plak in het zojuist gemaakte functiecodebestand de volgende code, wijzig de naam van de functie in `DeleteDeviceInTwinFunc.cs` en sla het bestand op.

:::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DeleteDeviceInTwinFunc.cs":::

#### <a name="step-3-publish-the-function-app-to-azure"></a>Stap 3: de functie-app publiceren naar Azure

Publiceer het project met de functie *DeleteDeviceInTwinFunc.cs* naar de functie-app in Azure.

[!INCLUDE [digital-twins-publish-and-configure-function-app.md](../../includes/digital-twins-publish-and-configure-function-app.md)]

### <a name="create-an-iot-hub-route-for-lifecycle-events"></a>Een route IoT Hub levenscyclusgebeurtenissen maken

U gaat nu een route voor IoT Hub instellen om levenscyclusgebeurtenissen van apparaten te routeer. In dit geval luistert u specifiek naar gebeurtenissen voor het verwijderen van apparaten, geïdentificeerd door `if (opType == "deleteDeviceIdentity")` . Hiermee wordt het verwijderen van het digitale dubbelitem activeert, en wordt de pensioenfase van een apparaat en de digitale dubbel ervan definitief.

Eerst moet u een Event Hub-eindpunt maken in uw IoT-hub. Vervolgens voegt u een route toe in IoT Hub om levenscyclusgebeurtenissen naar dit Event Hub-eindpunt te verzenden.
Volg deze stappen om een Event Hub-eindpunt te maken:

1. [Navigeer in Azure Portal](https://portal.azure.com/)naar de IoT-hub [](#prerequisites) die u hebt gemaakt in de sectie Vereisten en selecteer Berichtroutering **in** de menuopties aan de linkerkant.
2. Selecteer het **tabblad Aangepaste eindpunten.**
3. Selecteer **+ Toevoegen en** kies Event **Hubs om** een eindpunt van het type Event Hubs toe te voegen.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/event-hub-custom-endpoint.png" alt-text="Schermopname van het Visual Studio om een aangepast eindpunt voor de Event Hub toe te voegen." lightbox="media/how-to-provision-using-device-provisioning-service/event-hub-custom-endpoint.png":::

4. Kies in het *venster Een Event Hub-eindpunt toevoegen dat* wordt geopend de volgende waarden:
    * **Eindpuntnaam:** kies een eindpuntnaam.
    * **Event Hub-naamruimte:** selecteer uw Event Hub-naamruimte in de vervolgkeuzelijst.
    * **Event Hub-exemplaar:** kies de naam van de Event Hub die u in de vorige stap hebt gemaakt.
5. Selecteer **Maken**. Houd dit venster geopend om een route toe te voegen in de volgende stap.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/add-event-hub-endpoint.png" alt-text="Schermopname van het Visual Studio om een Event Hub-eindpunt toe te voegen." lightbox="media/how-to-provision-using-device-provisioning-service/add-event-hub-endpoint.png":::

Vervolgens voegt u een route toe die verbinding maakt met het eindpunt dat u in de bovenstaande stap hebt gemaakt, met een routeringsquery die de verwijdergebeurtenissen verzendt. Volg deze stappen om een route te maken:

1. Navigeer naar *het tabblad Routes* en selecteer Toevoegen **om** een route toe te voegen.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/add-message-route.png" alt-text="Schermopname van het Visual Studio om een route toe te voegen voor het verzenden van gebeurtenissen." lightbox="media/how-to-provision-using-device-provisioning-service/add-message-route.png":::

2. Kies op *de pagina Een route* toevoegen die wordt geopend de volgende waarden:

   * **Naam:** kies een naam voor uw route. 
   * **Eindpunt:** kies het Event Hubs-eindpunt dat u eerder hebt gemaakt in de vervolgkeuzekeuze.
   * **Gegevensbron:** kies *Levenscyclusgebeurtenissen voor apparaten.*
   * **Routeringsquery:** voer `opType='deleteDeviceIdentity'` in. Deze query beperkt de levenscyclusgebeurtenissen van het apparaat om alleen de verwijdergebeurtenissen te verzenden.

3. Selecteer **Opslaan**.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/lifecycle-route.png" alt-text="Schermopname van het Azure Portal om een route toe te voegen voor het verzenden van levenscyclusgebeurtenissen." lightbox="media/how-to-provision-using-device-provisioning-service/lifecycle-route.png":::

Zodra u deze stroom hebt doorgegaan, is alles ingesteld om apparaten end-to-end uit gebruik te stellen.

### <a name="validate"></a>Valideren

Als u het pensioenproces wilt activeren, moet u het apparaat handmatig verwijderen uit IoT Hub.

U kunt dit doen met een [Azure CLI-opdracht](/cli/azure/iot/hub/module-identity#az_iot_hub_module_identity_delete) of in de Azure Portal. Volg de onderstaande stappen om het apparaat in de Azure Portal:

1. Navigeer naar uw IoT-hub en kies **IoT-apparaten** in de menuopties aan de linkerkant. 
2. In de eerste helft van dit artikel ziet u een apparaat met de [apparaatregistratie-id die u hebt gekozen.](#auto-provision-device-using-device-provisioning-service) U kunt ook elk ander apparaat kiezen dat u wilt verwijderen, zolang er een dubbel in Azure Digital Twins staat, zodat u kunt controleren of de dubbel automatisch wordt verwijderd nadat het apparaat is verwijderd.
3. Selecteer het apparaat en kies **Verwijderen.**

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/delete-device-twin.png" alt-text="Schermopname van de Azure Portal apparaattweeling van de IoT-apparaten te verwijderen." lightbox="media/how-to-provision-using-device-provisioning-service/delete-device-twin.png":::

Het kan enkele minuten duren voordat de wijzigingen worden doorgevoerd in Azure Digital Twins.

Gebruik de volgende [Azure Digital Twins CLI-opdracht](how-to-use-cli.md) om te controleren of de dubbel van het apparaat in Azure Digital Twins is verwijderd.

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id "<Device Registration ID>"
```

U ziet dat de dubbel van het apparaat niet meer kan worden gevonden in Azure Digital Twins-exemplaar.

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/show-retired-twin.png" alt-text="Schermopname van de Opdrachtvenster waarin wordt weergegeven dat de tweeling niet is gevonden." lightbox="media/how-to-provision-using-device-provisioning-service/show-retired-twin.png":::

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources die in dit artikel zijn gemaakt niet meer nodig hebt, volgt u deze stappen om ze te verwijderen.

Met de Azure Cloud Shell of lokale Azure CLI kunt u alle Azure-resources in een resourcegroep verwijderen met de [opdracht az group delete.](/cli/azure/group#az_group_delete) Hiermee verwijdert u de resourcegroep; het Azure Digital Twins-exemplaar; de IoT-hub en de hubapparaatregistratie; het Event Grid-onderwerp en de bijbehorende abonnementen; de Event Hubs-naamruimte en Azure Functions apps, inclusief bijbehorende resources zoals opslag.

> [!IMPORTANT]
> Het verwijderen van een resourcegroep kan niet ongedaan worden gemaakt. De resourcegroep en alle resources daarin worden permanent verwijderd. Zorg ervoor dat u niet per ongeluk de verkeerde resourcegroep of resources verwijdert. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

Verwijder vervolgens de voorbeeldmap van het project die u hebt gedownload van uw lokale computer.

## <a name="next-steps"></a>Volgende stappen

De digitale tweelingen die voor de apparaten zijn gemaakt, worden opgeslagen als een platte hiërarchie in Azure Digital Twins, maar ze kunnen worden verrijkt met modelinformatie en een hiërarchie met meerdere niveau's voor de organisatie. Lees voor meer informatie over dit concept:

* [*Concepten: Digitale tweelingen en de tweelinggrafiek*](concepts-twins-graph.md)

Zie voor meer informatie over het gebruik van HTTP-aanvragen met Azure-functies:

* [*Azure HTTP-aanvraagtrigger voor Azure Functions*](../azure-functions/functions-bindings-http-webhook-trigger.md)

U kunt aangepaste logica schrijven om deze informatie automatisch op te geven met behulp van het model en de grafiekgegevens die al zijn opgeslagen in Azure Digital Twins. Zie het volgende voor meer informatie over het beheren, upgraden en ophalen van informatie uit de tweelinggrafiek:

* [*How-to: Manage a digital twin (Een digitale tweeling beheren)*](how-to-manage-twin.md)
* [*How-to: Query's uitvoeren op de tweelinggrafiek*](how-to-query-graph.md)