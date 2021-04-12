---
title: Apparaten automatisch beheren met Device Provisioning Service
titleSuffix: Azure Digital Twins
description: Zie een geautomatiseerd proces voor het inrichten en buiten gebruik stellen van IoT-apparaten in azure Digital Apparaatdubbels met behulp van de Device Provisioning Service (DPS).
author: baanders
ms.author: baanders
ms.date: 3/21/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: ad1351b7c9a649a553ce54422b99a13c286437d6
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107292"
---
# <a name="auto-manage-devices-in-azure-digital-twins-using-device-provisioning-service-dps"></a>Apparaten in azure Digital Apparaatdubbels automatisch beheren met behulp van de Device Provisioning Service (DPS)

In dit artikel leert u hoe u Azure Digital Apparaatdubbels integreert met de [Device Provisioning Service (DPS)](../iot-dps/about-iot-dps.md).

Met de oplossing die in dit artikel wordt beschreven, kunt u het proces voor het **_inrichten_** en **_buiten gebruik_** stellen van IOT Hub apparaten in azure Digital apparaatdubbels met behulp van Device Provisioning Service automatiseren. 

Zie de [sectie *levens cyclus van apparaten*](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) in de documentatie van IOT hub Apparaatbeheer voor meer informatie over de fase van het _inrichten_ en de _buiten gebruiks telling_ en voor een beter begrip van de set algemene Apparaatbeheer die gemeen schappelijk zijn voor alle zakelijke IOT-projecten.

## <a name="prerequisites"></a>Vereisten

Voordat u het inrichten kunt instellen, moet u het volgende instellen:
* een **Azure Digital apparaatdubbels-exemplaar**. Volg de instructies in [*How-to: een instantie en authenticatie instellen*](how-to-set-up-instance-portal.md) om een Azure Digital apparaatdubbels-exemplaar te maken. Verzamel de **_hostnaam_** van het exemplaar in de Azure Portal ([instructies](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).
* een **IOT-hub**. Zie de sectie *een IOT hub maken* van deze [IOT hub Snelstartgids](../iot-hub/quickstart-send-telemetry-cli.md)voor instructies.
* een [**Azure-functie**](../azure-functions/functions-overview.md) waarmee digitale dubbele gegevens worden bijgewerkt op basis van IOT hub gegevens. Volg de instructies in [*How to: inslikken van IOT hub-gegevens*](how-to-ingest-iot-hub-data.md) om deze Azure-functie te maken. Verzamel de functie **_naam_** om deze in dit artikel te gebruiken.

In dit voor beeld wordt ook een **device Simulator** gebruikt die het inrichten met behulp van de Device Provisioning Service bevat. De Device Simulator bevindt zich hier: [Azure Digital apparaatdubbels-en IOT hub Integration](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/)-voor beeld. Down load het voorbeeld project op uw machine door te navigeren naar de voorbeeld koppeling en de knop *zip downloaden* te selecteren onder de titel. De gedownloade map uitpakken.

U moet [**Node.js**](https://nodejs.org/download) op uw computer zijn geïnstalleerd. De Device Simulator is gebaseerd op **Node.js**, versie 10.0. x of hoger.

## <a name="solution-architecture"></a>Architectuur voor de oplossing

In de onderstaande afbeelding ziet u de architectuur van deze oplossing met behulp van Azure Digital Apparaatdubbels met Device Provisioning Service. Het toont zowel de inrichting van het apparaat als de buiten gebruiks telling van de stroom.

:::image type="content" source="media/how-to-provision-using-dps/flows.png" alt-text="Diagram van het apparaat en verschillende Azure-Services in een end-to-end-scenario. Gegevens stromen terug en tussen een thermo staat-apparaat en DPS. Gegevens stromen ook van DPS naar IoT Hub en naar Azure Digital Apparaatdubbels via een Azure-functie met het label ' toewijzing '. Gegevens van een hand matige actie voor het verwijderen van een apparaat stromen via IoT Hub > Event Hubs > Azure Functions > Azure Digital Apparaatdubbels." lightbox="media/how-to-provision-using-dps/flows.png":::

Dit artikel is onderverdeeld in twee secties:
* [*Apparaat automatisch inrichten met Device Provisioning Service*](#auto-provision-device-using-device-provisioning-service)
* [*Apparaat automatisch buiten gebruik stellen met IoT Hub levenscyclus gebeurtenissen*](#auto-retire-device-using-iot-hub-lifecycle-events)

Zie de afzonderlijke secties verderop in dit artikel voor een diep gaande uitleg van elke stap in de architectuur.

## <a name="auto-provision-device-using-device-provisioning-service"></a>Apparaat automatisch inrichten met Device Provisioning Service

In deze sectie voegt u Device Provisioning Service toe aan Azure Digital Apparaatdubbels om apparaten automatisch in te richten via het onderstaande pad. Dit is een uittreksel van de volledige architectuur die [eerder](#solution-architecture)is weer gegeven.

:::image type="content" source="media/how-to-provision-using-dps/provision.png" alt-text="Diagram van inrichtings stroom: een fragment van het diagram van de oplossings architectuur, met getallen die secties van de stroom benoemen. Gegevens stromen terug en tussen een thermo staat-apparaat en DPS (1 voor Device > DPS en 5 voor DPS >-apparaat). Gegevens stromen ook van DPS naar IoT Hub (4) en naar Azure Digital Apparaatdubbels (3) via een Azure-functie met het label ' toewijzing ' (2)." lightbox="media/how-to-provision-using-dps/provision.png":::

Hier volgt een beschrijving van de proces stroom:
1. Apparaat neemt contact op met het DPS-eind punt, waarbij identificatie gegevens worden door gegeven om de identiteit ervan te bewijzen.
2. DPS valideert de identiteit van het apparaat door de registratie-ID en-sleutel te valideren op basis van de registratie lijst en een [Azure-functie](../azure-functions/functions-overview.md) aan te roepen om de toewijzing uit te voeren.
3. Met de functie Azure maakt u een nieuwe [dubbele](concepts-twins-graph.md) in azure Digital apparaatdubbels voor het apparaat. De dubbele waarde heeft dezelfde naam als de **registratie-id** van het apparaat.
4. Met DPS wordt het apparaat geregistreerd bij een IoT-hub en wordt de gewenste twee status van het apparaat ingevuld.
5. De IoT-hub retourneert informatie over de apparaat-ID en de gegevens van de IoT hub-verbinding met het apparaat. Het apparaat kan nu verbinding maken met de IoT-hub.

In de volgende secties worden de stappen beschreven voor het instellen van deze apparaat stroom voor automatische inrichting.

### <a name="create-a-device-provisioning-service"></a>Een Device Provisioning Service maken

Wanneer een nieuw apparaat wordt ingericht met behulp van Device Provisioning Service, kan een nieuwe twee voor dat apparaat worden gemaakt in azure Digital Apparaatdubbels met dezelfde naam als de registratie-ID.

Maak een Device Provisioning service-exemplaar dat wordt gebruikt voor het inrichten van IoT-apparaten. U kunt de onderstaande Azure CLI-instructies gebruiken of de Azure Portal: Quick Start [*: Stel de IOT hub Device Provisioning Service in met de Azure Portal*](../iot-dps/quick-setup-auto-provision.md).

Met de volgende Azure CLI-opdracht wordt een Device Provisioning-Service gemaakt. U moet een naam opgeven voor de Device Provisioning Service, de resource groep en de regio. Als u wilt zien welke regio's Device Provisioning Service ondersteunen, gaat u naar [*Azure-producten beschikbaar per regio*](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub).
De opdracht kan worden uitgevoerd in [Cloud shell](https://shell.azure.com)of lokaal als u de Azure cli [op uw computer hebt geïnstalleerd](/cli/azure/install-azure-cli).

```azurecli-interactive
az iot dps create --name <Device Provisioning Service name> --resource-group <resource group name> --location <region>
```

### <a name="add-a-function-to-use-with-device-provisioning-service"></a>Een functie toevoegen die wordt gebruikt met Device Provisioning Service

In het app-project van de functie dat u hebt gemaakt in de sectie [vereisten](#prerequisites) , maakt u een nieuwe functie voor gebruik met de Device Provisioning Service. Deze functie wordt gebruikt door de Device Provisioning Service in een [aangepast toewijzings beleid](../iot-dps/how-to-use-custom-allocation-policies.md) om een nieuw apparaat in te richten.

Begin met het openen van het functie-app-project in Visual Studio op uw computer en voer de onderstaande stappen uit.

#### <a name="step-1-add-a-new-function"></a>Stap 1: een nieuwe functie toevoegen 

Voeg een nieuwe functie van het type *http-trigger* toe aan het functie-app-project in Visual Studio.

:::image type="content" source="media/how-to-provision-using-dps/add-http-trigger-function-visual-studio.png" alt-text="Scherm opname van de weer gave Visual Studio voor het toevoegen van Azure-functie van het type http-trigger in uw functie-app-project." lightbox="media/how-to-provision-using-dps/add-http-trigger-function-visual-studio.png":::

#### <a name="step-2-fill-in-function-code"></a>Stap 2: functie code invullen

Voeg een nieuw NuGet-pakket toe aan het project: [micro soft. Azure. devices. provisioning. service](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/). Mogelijk moet u ook meer pakketten toevoegen aan uw project, als de pakketten die in de code worden gebruikt, niet al deel uitmaken van het project.

Plak in het zojuist gemaakte functie code bestand de volgende code, wijzig de naam van de functie in *DpsAdtAllocationFunc. cs* en sla het bestand op.

:::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DpsAdtAllocationFunc.cs":::

#### <a name="step-3-publish-the-function-app-to-azure"></a>Stap 3: de functie-app publiceren in azure

Publiceer het project met de functie *DpsAdtAllocationFunc. cs* in de functie-app in Azure.

[!INCLUDE [digital-twins-publish-and-configure-function-app.md](../../includes/digital-twins-publish-and-configure-function-app.md)]

### <a name="create-device-provisioning-enrollment"></a>Inschrijving voor Device Provisioning maken

Vervolgens moet u een registratie in Device Provisioning Service maken met behulp van een **aangepaste toewijzings functie**. Volg de instructies in de sectie [*de registratie maken*](../iot-dps/how-to-use-custom-allocation-policies.md#create-the-enrollment) van het artikel over het aangepaste toewijzings beleid in de documentatie van de Device Provisioning Service.

Zorg ervoor dat u de volgende opties selecteert om de registratie te koppelen aan de functie die u zojuist hebt gemaakt.

* **Selecteer hoe u apparaten aan hubs wilt toewijzen**: aangepast (Azure function gebruiken).
* **Selecteer de IOT-hubs waaraan deze groep kan worden toegewezen:** Kies de naam van uw IoT-hub of selecteer de knop *een nieuwe IOT hub koppelen* en kies uw IOT-hub in de vervolg keuzelijst.

Kies vervolgens de knop *een nieuwe functie selecteren* om uw functie-app te koppelen aan de registratie groep. Vul vervolgens de volgende waarden in:

* **Abonnement**: uw Azure-abonnement is automatisch ingevuld. Controleer of het het juiste abonnement is.
* **Functie-app**: Kies de naam van de functie-app.
* **Functie**: Kies DpsAdtAllocationFunc.

Sla uw gegevens op.                  

:::image type="content" source="media/how-to-provision-using-dps/link-enrollment-group-to-iot-hub-and-function-app.png" alt-text="Scherm opname van het venster Details van de groep voor douane registratie om aangepast te selecteren (Azure function gebruiken) en de naam van uw IoT-hub in de secties Selecteer hoe u apparaten aan hubs wilt toewijzen en selecteer de IoT-hubs waaraan deze groep kan worden toegewezen. Selecteer ook uw abonnement, functie-app in de vervolg keuzelijst en zorg ervoor dat u DpsAdtAllocationFunc selecteert." lightbox="media/how-to-provision-using-dps/link-enrollment-group-to-iot-hub-and-function-app.png":::

Nadat de inschrijving is gemaakt, wordt de **primaire sleutel** voor de registratie later gebruikt om de Device Simulator voor dit artikel te configureren.

### <a name="set-up-the-device-simulator"></a>De Device Simulator instellen

In dit voor beeld wordt een Device Simulator gebruikt die inrichting bevat met behulp van de Device Provisioning Service. De Device Simulator bevindt zich hier: [Azure Digital apparaatdubbels-en IOT hub Integration](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/)-voor beeld. Als u het voor beeld nog niet hebt gedownload, gaat u nu naar de voorbeeld koppeling en selecteert u de knop voor het downloaden van de *zip* onder de titel. De gedownloade map uitpakken.

#### <a name="upload-the-model"></a>Het model uploaden

De Device Simulator is een apparaat van het type thermo staat dat gebruikmaakt van het model met deze ID: `dtmi:contosocom:DigitalTwins:Thermostat;1` . U moet dit model uploaden naar Azure Digital Apparaatdubbels voordat u een twee van dit type kunt maken voor het apparaat.

[!INCLUDE [digital-twins-thermostat-model-upload.md](../../includes/digital-twins-thermostat-model-upload.md)]

Raadpleeg [*How to: Manage Models (modellen beheren*](how-to-manage-model.md#upload-models)) voor meer informatie over modellen.

#### <a name="configure-and-run-the-simulator"></a>De Simulator configureren en uitvoeren

Navigeer in uw opdracht venster naar het gedownloade voor beeld van *Azure Digital apparaatdubbels en IOT hub integratie* die u eerder hebt uitgepakt en klik vervolgens in de map *device-Simulator* . Installeer vervolgens de afhankelijkheden voor het project met behulp van de volgende opdracht:

```cmd
npm install
```

Kopieer vervolgens in de map Device Simulator het bestand. env. sjabloon naar een nieuw bestand met de naam. env en verzamel de volgende waarden om de instellingen in te vullen:

* PROVISIONING_IDSCOPE: als u deze waarde wilt ophalen, gaat u in de [Azure Portal](https://portal.azure.com/)naar uw Device Provisioning Service en selecteert u *overzicht* in het menu opties en zoekt u naar het *bereik veld-id*.

    :::image type="content" source="media/how-to-provision-using-dps/id-scope.png" alt-text="Scherm afbeelding van de Azure Portal weer gave van de overzichts pagina voor het inrichten van het apparaat om de ID-bereik waarde te kopiëren." lightbox="media/how-to-provision-using-dps/id-scope.png":::

* PROVISIONING_REGISTRATION_ID: u kunt een registratie-ID voor uw apparaat kiezen.
* ADT_MODEL_ID: `dtmi:contosocom:DigitalTwins:Thermostat;1`
* PROVISIONING_SYMMETRIC_KEY: dit is de primaire sleutel voor de registratie die u eerder hebt ingesteld. Als u deze waarde opnieuw wilt ophalen, gaat u naar uw Device Provisioning Service in de Azure Portal, selecteert u *inschrijvingen beheren*, selecteert u de registratie groep die u eerder hebt gemaakt en kopieert u de *primaire sleutel*.

    :::image type="content" source="media/how-to-provision-using-dps/sas-primary-key.png" alt-text="Scherm afbeelding van de Azure Portal weer gave van de pagina inschrijvingen voor Device Provisioning Service beheren voor het kopiëren van de waarde van de primaire SAS-sleutel." lightbox="media/how-to-provision-using-dps/sas-primary-key.png":::

Gebruik nu de bovenstaande waarden om de instellingen van het. env-bestand bij te werken.

```cmd
PROVISIONING_HOST = "global.azure-devices-provisioning.net"
PROVISIONING_IDSCOPE = "<Device Provisioning Service Scope ID>"
PROVISIONING_REGISTRATION_ID = "<Device Registration ID>"
ADT_MODEL_ID = "dtmi:contosocom:DigitalTwins:Thermostat;1"
PROVISIONING_SYMMETRIC_KEY = "<Device Provisioning Service enrollment primary SAS key>"
```

Sla het bestand op en sluit het.

### <a name="start-running-the-device-simulator"></a>Starten met het uitvoeren van de Device Simulator

Open in de map *device-Simulator* in het opdracht venster de Device Simulator met de volgende opdracht:

```cmd
node .\adt_custom_register.js
```

U ziet het apparaat dat wordt geregistreerd en verbonden met IoT Hub en vervolgens begint met het verzenden van berichten.
:::image type="content" source="media/how-to-provision-using-dps/output.png" alt-text="Scherm afbeelding van de Opdrachtvenster de apparaatregistratie weer geven en berichten verzendt" lightbox="media/how-to-provision-using-dps/output.png":::

### <a name="validate"></a>Valideren

Als gevolg van de stroom die u in dit artikel hebt ingesteld, wordt het apparaat automatisch geregistreerd in azure Digital Apparaatdubbels. Gebruik de volgende [Azure Digital APPARAATDUBBELS cli](how-to-use-cli.md) -opdracht om het dubbele apparaat te vinden in het Azure Digital apparaatdubbels-exemplaar dat u hebt gemaakt.

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id "<Device Registration ID>"
```

U ziet het dubbele apparaat dat wordt gevonden in het Azure Digital Apparaatdubbels-exemplaar.
:::image type="content" source="media/how-to-provision-using-dps/show-provisioned-twin.png" alt-text="Scherm afbeelding van de Opdrachtvenster met de nieuwe dubbele twee." lightbox="media/how-to-provision-using-dps/show-provisioned-twin.png":::

## <a name="auto-retire-device-using-iot-hub-lifecycle-events"></a>Apparaat automatisch buiten gebruik stellen met IoT Hub levenscyclus gebeurtenissen

In deze sectie gaat u IoT Hub levenscyclus gebeurtenissen toevoegen aan Azure Digital Apparaatdubbels om apparaten automatisch buiten gebruik te stellen via het onderstaande pad. Dit is een uittreksel van de volledige architectuur die [eerder](#solution-architecture)is weer gegeven.

:::image type="content" source="media/how-to-provision-using-dps/retire.png" alt-text="Diagram van de stroom van het apparaat buiten gebruik stellen: een fragment van het diagram van de oplossings architectuur, met getallen die secties van de stroom bevatten. Het Thermo staat-apparaat wordt weer gegeven zonder verbindingen met de Azure-Services in het diagram. Gegevens van een hand matige actie voor het verwijderen van een apparaat stromen via IoT Hub (1) > Event Hubs (2) > Azure Functions > Azure Digital Apparaatdubbels (3)." lightbox="media/how-to-provision-using-dps/retire.png":::

Hier volgt een beschrijving van de proces stroom:
1. Een extern of hand matig proces activeert het verwijderen van een apparaat in IoT Hub.
2. IoT Hub het apparaat verwijdert en een [levens cyclus](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) gebeurtenis van het apparaat genereert die wordt doorgestuurd naar een [Event hub](../event-hubs/event-hubs-about.md).
3. Een Azure-functie verwijdert het dubbele van het apparaat in azure Digital Apparaatdubbels.

In de volgende secties worden de stappen beschreven voor het instellen van deze apparaat stroom voor automatische buiten gebruik stellen.

### <a name="create-an-event-hub"></a>Een Event Hub maken

Vervolgens maakt u een Azure- [Event hub](../event-hubs/event-hubs-about.md) om IOT hub levenscyclus gebeurtenissen te ontvangen. 

Volg de stappen die worden beschreven in de Snelstartgids [*Create a Event hub*](../event-hubs/event-hubs-create.md) . Geef een naam op voor uw Event Hub *lifecycleevents*. U gebruikt deze Event Hub naam wanneer u IoT Hub route en een Azure-functie instelt in de volgende secties.

De onderstaande scherm afbeelding illustreert het maken van de Event Hub.
:::image type="content" source="media/how-to-provision-using-dps/create-event-hub-lifecycle-events.png" alt-text="Scherm opname van het Azure Portal venster voor het maken van een Event Hub met de naam lifecycleevents." lightbox="media/how-to-provision-using-dps/create-event-hub-lifecycle-events.png":::

#### <a name="create-sas-policy-for-your-event-hub"></a>SAS-beleid voor uw Event Hub maken

Vervolgens moet u een [SAS-beleid (Shared Access Signature)](../event-hubs/authorize-access-shared-access-signature.md) maken om de Event hub te configureren met uw functie-app.
Dit wilt doen
1. Ga naar de Event Hub die u zojuist hebt gemaakt in de Azure Portal en selecteer **beleid voor gedeelde toegang** in de menu opties aan de linkerkant.
2. Selecteer **Toevoegen**. In het venster *SAS-beleid toevoegen* dat wordt geopend, voert u een beleids naam van uw keuze in en schakelt u het selectie vakje *Luis teren* in.
3. Selecteer **Maken**.
    
:::image type="content" source="media/how-to-provision-using-dps/add-event-hub-sas-policy.png" alt-text="Scherm opname van de Azure Portal om een Event Hub SAS-beleid toe te voegen." lightbox="media/how-to-provision-using-dps/add-event-hub-sas-policy.png":::

#### <a name="configure-event-hub-with-function-app"></a>Event Hub configureren met functie-app

Vervolgens configureert u de Azure function-app die u hebt ingesteld in de sectie [vereisten](#prerequisites) om samen te werken met uw nieuwe event hub. U doet dit door een omgevings variabele in de functie-app in te stellen met de connection string van de Event Hub.

1. Open het beleid dat u zojuist hebt gemaakt en kopieer de **verbindings reeks-primaire sleutel** waarde.

    :::image type="content" source="media/how-to-provision-using-dps/event-hub-sas-policy-connection-string.png" alt-text="Scherm afbeelding van de Azure Portal om de connection string-primaire sleutel te kopiëren." lightbox="media/how-to-provision-using-dps/event-hub-sas-policy-connection-string.png":::

2. Voeg de connection string als een variabele toe aan de functie-app-instellingen met de volgende Azure CLI-opdracht. De opdracht kan worden uitgevoerd in [Cloud shell](https://shell.azure.com)of lokaal als u de Azure cli [op uw computer hebt geïnstalleerd](/cli/azure/install-azure-cli).

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EVENTHUB_CONNECTIONSTRING=<Event Hubs SAS connection string Listen>" -g <resource group> -n <your App Service (function app) name>
    ```

### <a name="add-a-function-to-retire-with-iot-hub-lifecycle-events"></a>Een functie toevoegen voor buiten gebruik stellen met IoT Hub levenscyclus gebeurtenissen

In uw functie-app-project dat u hebt gemaakt in de sectie [vereisten](#prerequisites) , maakt u een nieuwe functie om een bestaand apparaat buiten gebruik te stellen met IOT hub levenscyclus gebeurtenissen.

Zie [*IOT hub niet-telemetrie-gebeurtenissen*](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)voor meer informatie over levenscyclus gebeurtenissen. Zie [*Azure Event hubs trigger voor Azure functions*](../azure-functions/functions-bindings-event-hubs-trigger.md)voor meer informatie over het gebruik van Event hubs met Azure functions.

Begin met het openen van het functie-app-project in Visual Studio op uw computer en voer de onderstaande stappen uit.

#### <a name="step-1-add-a-new-function"></a>Stap 1: een nieuwe functie toevoegen
     
Een nieuwe functie van het type *Event hub-trigger* toevoegen aan het functie-app-project in Visual Studio.

:::image type="content" source="media/how-to-provision-using-dps/create-event-hub-trigger-function.png" alt-text="Scherm opname van het venster van Visual Studio voor het toevoegen van een Azure-functie van het type Event hub-trigger in uw functie-app-project." lightbox="media/how-to-provision-using-dps/create-event-hub-trigger-function.png":::

#### <a name="step-2-fill-in-function-code"></a>Stap 2: functie code invullen

Plak in het zojuist gemaakte functie code bestand de volgende code, wijzig de naam van de functie in `DeleteDeviceInTwinFunc.cs` en sla het bestand op.

:::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DeleteDeviceInTwinFunc.cs":::

#### <a name="step-3-publish-the-function-app-to-azure"></a>Stap 3: de functie-app publiceren in azure

Publiceer het project met de functie *DeleteDeviceInTwinFunc. cs* in de functie-app in Azure.

[!INCLUDE [digital-twins-publish-and-configure-function-app.md](../../includes/digital-twins-publish-and-configure-function-app.md)]

### <a name="create-an-iot-hub-route-for-lifecycle-events"></a>Een IoT Hub route maken voor levenscyclus gebeurtenissen

Nu gaat u een IoT Hub route instellen om levenscyclus gebeurtenissen van het apparaat te routeren. In dit geval luistert u specifiek naar het verwijderen van het apparaat, aangeduid door `if (opType == "deleteDeviceIdentity")` . Hiermee wordt de verwijdering van het digitale dubbele item geactiveerd en wordt de buiten gebruiks telling van een apparaat en de bijbehorende digitale dubbele items afgerond.

Eerst moet u een Event Hub-eind punt maken in uw IoT-hub. Vervolgens voegt u een route in IoT hub toe om levenscyclus gebeurtenissen naar dit Event Hub eind punt te verzenden.
Volg deze stappen om een Event Hub-eind punt te maken:

1. Navigeer in het [Azure Portal](https://portal.azure.com/)naar de IOT-hub die u hebt gemaakt in de sectie [vereisten](#prerequisites) en selecteer **bericht routering** in de menu opties aan de linkerkant.
2. Selecteer het tabblad **aangepaste eind punten** .
3. Selecteer **+ toevoegen** en kies **Event hubs** om een event hubs-type eind punt toe te voegen.

    :::image type="content" source="media/how-to-provision-using-dps/event-hub-custom-endpoint.png" alt-text="Scherm opname van het venster van Visual Studio om een Event Hub aangepast eind punt toe te voegen." lightbox="media/how-to-provision-using-dps/event-hub-custom-endpoint.png":::

4. In het venster *een event hub eind punt toevoegen* dat wordt geopend, kiest u de volgende waarden:
    * **Eindpunt naam**: Kies een naam voor het eind punt.
    * **Event hub-naam ruimte**: selecteer uw event hub naam ruimte in de vervolg keuzelijst.
    * **Event hub-instantie**: kies de Event hub naam die u in de vorige stap hebt gemaakt.
5. Selecteer **Maken**. Houd dit venster geopend om in de volgende stap een route toe te voegen.

    :::image type="content" source="media/how-to-provision-using-dps/add-event-hub-endpoint.png" alt-text="Scherm opname van het venster van Visual Studio om een Event Hub eind punt toe te voegen." lightbox="media/how-to-provision-using-dps/add-event-hub-endpoint.png":::

Vervolgens voegt u een route toe die verbinding maakt met het eind punt dat u in de bovenstaande stap hebt gemaakt, met een routerings query die de verwijderings gebeurtenissen verzendt. Volg deze stappen om een route te maken:

1. Navigeer naar het tabblad *routes* en selecteer **toevoegen** om een route toe te voegen.

    :::image type="content" source="media/how-to-provision-using-dps/add-message-route.png" alt-text="Scherm opname van het venster van Visual Studio om een route toe te voegen voor het verzenden van gebeurtenissen." lightbox="media/how-to-provision-using-dps/add-message-route.png":::

2. Kies op de pagina *een route toevoegen* die wordt geopend de volgende waarden:

   * **Naam**: Kies een naam voor de route. 
   * **Eind punt**: Kies het event hubs-eind punt dat u eerder hebt gemaakt in de vervolg keuzelijst.
   * **Gegevens bron**: Kies *levenscyclus gebeurtenissen* van het apparaat.
   * **Routerings query**: invoeren `opType='deleteDeviceIdentity'` . Met deze query worden de gebeurtenissen van de levens cyclus van het apparaat beperkt zodat alleen de verwijderings gebeurtenissen worden verzonden.

3. Selecteer **Opslaan**.

    :::image type="content" source="media/how-to-provision-using-dps/lifecycle-route.png" alt-text="Scherm opname van het Azure Portal venster om een route toe te voegen om levenscyclus gebeurtenissen te verzenden." lightbox="media/how-to-provision-using-dps/lifecycle-route.png":::

Zodra u deze stroom hebt door lopen, is alles zo ingesteld dat apparaten end-to-end buiten gebruik worden gesteld.

### <a name="validate"></a>Valideren

Als u het proces van het buiten gebruik wilt stellen, moet u het apparaat hand matig verwijderen uit IoT Hub.

U kunt dit doen met een [Azure cli-opdracht](/cli/azure/iot/hub/module-identity#az_iot_hub_module_identity_delete) of in de Azure Portal. Volg de onderstaande stappen om het apparaat te verwijderen in de Azure Portal:

1. Navigeer naar uw IoT-hub en kies **IOT-apparaten** in de menu opties aan de linkerkant. 
2. U ziet een apparaat met de registratie-ID van het apparaat dat u in de [eerste helft van dit artikel](#auto-provision-device-using-device-provisioning-service)hebt gekozen. U kunt ook elk ander apparaat selecteren dat u wilt verwijderen, zolang het een dubbele in azure Digital Apparaatdubbels is, zodat u kunt controleren of de dubbele waarde automatisch wordt verwijderd nadat het apparaat is verwijderd.
3. Selecteer het apparaat en kies **verwijderen**.

:::image type="content" source="media/how-to-provision-using-dps/delete-device-twin.png" alt-text="Scherm afbeelding van de Azure Portal om het apparaat te verwijderen, twee van de IoT-apparaten." lightbox="media/how-to-provision-using-dps/delete-device-twin.png":::

Het kan enkele minuten duren om de wijzigingen weer te geven die zijn weer gegeven in azure Digital Apparaatdubbels.

Gebruik de volgende [Azure Digital APPARAATDUBBELS cli](how-to-use-cli.md) -opdracht om te controleren of het dubbele apparaat in het Azure Digital apparaatdubbels-exemplaar is verwijderd.

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id "<Device Registration ID>"
```

U ziet dat de dubbele van het apparaat niet meer kan worden gevonden in het Azure Digital Apparaatdubbels-exemplaar.

:::image type="content" source="media/how-to-provision-using-dps/show-retired-twin.png" alt-text="Scherm afbeelding van de Opdrachtvenster met dubbele niet gevonden." lightbox="media/how-to-provision-using-dps/show-retired-twin.png":::

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources die in dit artikel zijn gemaakt niet meer nodig hebt, volgt u deze stappen om ze te verwijderen.

Met de Azure Cloud Shell of lokale Azure CLI kunt u alle Azure-resources in een resource groep verwijderen met de opdracht [AZ Group delete](/cli/azure/group#az-group-delete) . Hiermee verwijdert u de resource groep. het Azure Digital Apparaatdubbels-exemplaar; de IoT-hub en de registratie van het hub-apparaat; het event grid-onderwerp en de bijbehorende abonnementen; de Event hubs-naam ruimte en beide Azure Functions-apps, inclusief gekoppelde resources zoals opslag.

> [!IMPORTANT]
> Het verwijderen van een resourcegroep kan niet ongedaan worden gemaakt. De resourcegroep en alle resources daarin worden permanent verwijderd. Zorg ervoor dat u niet per ongeluk de verkeerde resourcegroep of resources verwijdert. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

Verwijder vervolgens de voor beeld-map van het project dat u hebt gedownload van uw lokale computer.

## <a name="next-steps"></a>Volgende stappen

De digitale apparaatdubbels die voor de apparaten zijn gemaakt, worden opgeslagen als een platte hiërarchie in azure Digital Apparaatdubbels, maar ze kunnen worden verrijkt met model gegevens en een hiërarchie met meerdere niveaus voor de organisatie. Lees voor meer informatie over dit concept:

* [*Concepten: Digital apparaatdubbels en het dubbele diagram*](concepts-twins-graph.md)

Zie voor meer informatie over het gebruik van HTTP-aanvragen met Azure functions:

* [*Azure HTTP-aanvraag trigger voor Azure Functions*](../azure-functions/functions-bindings-http-webhook-trigger.md)

U kunt aangepaste logica schrijven om deze informatie automatisch te verstrekken met behulp van het model en de grafiek gegevens die al zijn opgeslagen in azure Digital Apparaatdubbels. Zie het volgende voor meer informatie over het beheren, bijwerken en ophalen van gegevens uit de apparaatdubbels-grafiek:

* [*Instructies: een digitale twee richtings beheer beheren*](how-to-manage-twin.md)
* [*Instructies: een query uitvoeren op de dubbele grafiek*](how-to-query-graph.md)