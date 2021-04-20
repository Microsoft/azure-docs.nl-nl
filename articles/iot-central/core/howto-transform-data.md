---
title: Gegevens voor Azure IoT Central | Microsoft Docs
description: IoT-apparaten verzenden gegevens in verschillende indelingen die u mogelijk moet transformeren. In dit artikel wordt beschreven hoe u gegevens zowel onderweg IoT Central als onderweg kunt transformeren. In de scenario's die worden beschreven IoT Edge en Azure Functions.
author: dominicbetts
ms.author: dobett
ms.date: 04/09/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 6032300bd203db78e8cd147cf79300d6dcd9b1dc
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751684"
---
# <a name="transform-data-for-iot-central"></a>Gegevens voor IoT Central

*Dit onderwerp is van toepassing op oplossingsbouwers.*

IoT-apparaten verzenden gegevens in verschillende indelingen. Als u de apparaatgegevens wilt gebruiken met IoT Central toepassing, moet u mogelijk een transformatie gebruiken om:

- Maak de indeling van de gegevens compatibel met uw IoT Central toepassing.
- Eenheden converteren.
- Nieuwe metrische gegevens berekenen.
- Verrijk de gegevens uit andere bronnen.

In dit artikel wordt beschreven hoe u apparaatgegevens buiten de IoT Central bij ingress of egress.

In het volgende diagram ziet u drie routes voor gegevens die transformaties bevatten:

:::image type="content" source="media/howto-transform-data/transform-data.png" alt-text="Samenvatting van routes voor gegevenstransformatie, zowel in- als uitgaat" border="false":::

In de volgende tabel ziet u drie voorbeeldtransformatietypen:

| Transformatie | Beschrijving | Voorbeeld  | Opmerkingen |
|------------------------|-------------|----------|-------|
| Berichtindeling         | Converteren naar of bewerken van JSON-berichten. | CSV naar JSON  | Bij ingress. IoT Central accepteert alleen waarde-JSON-berichten. Zie Nettoladingen [voor telemetrie, eigenschappen en](concepts-telemetry-properties-commands.md)opdrachten voor meer informatie. |
| Berekeningen           | Wiskundige functies die [Azure Functions](../../azure-functions/index.yml) kunnen worden uitgevoerd. | Eenheidsconversie van Fahrenheit naar Celsius.  | Transformeer met behulp van het egress-patroon om te profiteren van schaalbaar apparaatingressie via directe verbinding met IoT Central. Door de gegevens te transformeren, kunt u IoT Central functies zoals visualisaties en taken gebruiken. |
| Berichtverrijking     | Verrijkingen van externe gegevensbronnen die niet worden gevonden in apparaateigenschappen of telemetrie. Zie [IoT-gegevens](howto-export-data.md) exporteren naar cloudbestemmingen met behulp van gegevensexport voor meer informatie over interne verrijkingen | Weerinformatie toevoegen aan berichten met behulp van locatiegegevens van apparaten. | Transformeer met behulp van het egress-patroon om te profiteren van schaalbaar apparaatingressie via directe verbinding met IoT Central. |

## <a name="prerequisites"></a>Vereisten

U hebt een actief Azure-abonnement nodig om de stappen in dit artikel uit te voeren. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

Als u de oplossing wilt instellen, hebt u een IoT Central nodig. Zie Een IoT Central maken voor meer informatie over [het maken Azure IoT Central toepassing.](quick-deploy-iot-central.md)

## <a name="data-transformation-at-ingress"></a>Gegevenstransformatie bij ingress

Er zijn twee opties om apparaatgegevens bij ingress te transformeren:

- **IoT Edge:** gebruik een IoT Edge-module om gegevens van downstreamapparaten te transformeren voordat u de gegevens naar uw IoT Central verzenden.

- **IoT Central** apparaatbrug: de [IoT Central-apparaatbrug](howto-build-iotc-device-bridge.md) verbindt andere IoT-apparaatwolken, zoals Sigfox, Particle en The Things Network, met IoT Central. De apparaatbrug maakt gebruik van een Azure-functie voor het doorsturen van de gegevens en u kunt de functie aanpassen om de apparaatgegevens te transformeren.

### <a name="use-iot-edge-to-transform-device-data"></a>Gebruik IoT Edge om apparaatgegevens te transformeren

:::image type="content" source="media/howto-transform-data/transform-ingress.png" alt-text="Gegevenstransformatie bij ingress met behulp van IoT Edge" border="false":::

In dit scenario transformeert een IoT Edge de gegevens van downstreamapparaten voordat deze worden doorgestuurd naar IoT Central toepassing. Op hoog niveau zijn de stappen voor het configureren van dit scenario:

1. **Een IoT Edge** instellen: installeer en inrichten van een IoT Edge-apparaat als gateway en verbind de gateway met uw IoT Central toepassing.

1. **Sluit het downstreamapparaat aan op IoT Edge apparaat:** Verbind downstreamapparaten met het IoT Edge apparaat en inrichten voor uw IoT Central toepassing.

1. **Apparaatgegevens transformeren in IoT Edge:** Maak een IoT Edge om de gegevens te transformeren. Implementeer de module op het IoT Edge gatewayapparaat dat de getransformeerde apparaatgegevens doorsturen naar uw IoT Central toepassing.

1. **Controleren:** gegevens verzenden van een downstreamapparaat naar de gateway en controleren of de getransformeerde apparaatgegevens uw IoT Central bereiken.

In het voorbeeld dat in de volgende secties wordt beschreven, verzendt het downstreamapparaat CSV-gegevens in de volgende indeling naar IoT Edge gatewayapparaat:

```csv
"<temperature >, <pressure>, <humidity>"
```

U wilt een module IoT Edge gebruiken om de gegevens te transformeren naar de volgende JSON-indeling voordat deze naar de IoT Central:

```json
{
  "device": {
      "deviceId": "<downstream-deviceid>"
  },
  "measurements": {
    "temp": <temperature>,
    "pressure": <pressure>,
    "humidity": <humidity>,
  }
}
```

De volgende stappen laten zien hoe u dit scenario in kunt stellen en configureren:

### <a name="build-the-custom-module"></a>De aangepaste module bouwen

In dit scenario voert het IoT Edge een aangepaste module uit die de gegevens van het downstreamapparaat transformeert. Voordat u het IoT Edge implementeert en configureert, moet u het volgende doen:

- Bouw de aangepaste module.
- Voeg de aangepaste module toe aan een containerregister.

De IoT Edge downloadt aangepaste modules uit een containerregister, zoals een Azure-containerregister of Docker Hub. De [Azure Cloud Shell](../../cloud-shell/overview.md) bevat alle hulpprogramma's die u nodig hebt om een containerregister te maken, de module te bouwen en de module te uploaden naar het register:

Een containerregister maken:

1. Open de [Azure Cloud Shell](https://shell.azure.com/) en meld u aan bij uw Azure-abonnement.

1. Voer de volgende opdrachten uit om een Azure-containerregister te maken:

    ```azurecli
    REGISTRY_NAME="{your unique container registry name}"
    az group create --name ingress-scenario --location eastus
    az acr create -n $REGISTRY_NAME -g ingress-scenario --sku Standard --admin-enabled true
    az acr credential show -n $REGISTRY_NAME
    ```

    Noteer de waarden `username` en . U gebruikt deze `password` later.

De aangepaste module bouwen in de [Azure Cloud Shell](https://shell.azure.com/):

1. [Navigeer in Azure Cloud Shell](https://shell.azure.com/)naar een geschikte map.
1. Voer de volgende opdracht uit om de GitHub-opslagplaats met de broncode van de module te klonen:

    ```azurecli
    git clone https://github.com/iot-for-all/iot-central-transform-with-iot-edge
    ```

1. Als u de aangepaste module wilt bouwen, voert u de volgende opdrachten uit in Azure Cloud Shell:

    ```azurecli
    cd iot-central-transform-with-iot-edge/custommodule/transformmodule
    az acr build --registry $REGISTRY_NAME --image transformmodule:0.0.1-amd64 -f Dockerfile.amd64 .
    ```

    Het uitvoeren van de vorige opdrachten kan enkele minuten duren.

### <a name="set-up-an-iot-edge-device"></a>Een apparaat IoT Edge instellen

In dit scenario wordt een IoT Edge gatewayapparaat gebruikt om de gegevens van downstreamapparaten te transformeren. In deze sectie wordt beschreven hoe u IoT Central voor de gateway en downstreamapparaten in uw IoT Central maken. IoT Edge gebruiken een implementatiemanifest om hun modules te configureren.

Voor het maken van een apparaatsjabloon voor het downstreamapparaat wordt in dit scenario een eenvoudig thermostaatapparaatmodel gebruikt:

1. Download het [apparaatmodel voor het thermostaatapparaat](https://raw.githubusercontent.com/Azure/iot-plugandplay-models/main/dtmi/com/example/thermostat-2.json) naar uw lokale computer.

1. Meld u aan bij IoT Central toepassing en navigeer naar de **pagina Apparaatsjablonen.**

1. Selecteer **+ Nieuw,** selecteer **IoT-apparaat** en selecteer **Volgende: Aanpassen.**

1. Voer *Thermostaat* in als de sjabloonnaam en selecteer **Volgende: Controleren.** Selecteer vervolgens **Maken**.

1. Selecteer **Een model importeren** en importeer dethermostat-2.jshet *bestand* dat u eerder hebt gedownload.

1. Selecteer **Publiceren om** de nieuwe apparaatsjabloon te publiceren.

Een apparaatsjabloon maken voor het IoT Edge gatewayapparaat:

1. Sla een kopie van het implementatiemanifest op uw lokale ontwikkelmachine op: [moduledeployment.jsop](https://raw.githubusercontent.com/iot-for-all/iot-central-transform-with-iot-edge/main/edgemodule/moduledeployment.json).

1. Open uw lokale kopie van het *moduledeployment.jsmanifestbestand* in een teksteditor.

1. Zoek de sectie en vervang de tijdelijke aanduidingen door de waarden die u hebt noteren bij het maken van het `registryCredentials` Azure-containerregister. De `address` waarde ziet er als `<username>.azurecr.io` uit.

1. Zoek de `settings` sectie voor de `transformmodule` . Vervang `<acr or docker repo>` door dezelfde waarde die u in de vorige stap hebt `address` gebruikt. Sla de wijzigingen op.

1. Ga in IoT Central toepassing naar de pagina **Apparaatsjablonen.**

1. Selecteer **+ Nieuw,** selecteer **Azure IoT Edge** en selecteer **vervolgens Volgende: Aanpassen.**

1. Voer *IoT Edge gatewayapparaat in* als de naam van de apparaatsjabloon. Selecteer **Dit is een gatewayapparaat.** Selecteer **Bladeren om** het bestandmoduledeployment.jsnaar *het* distributiemanifestbestand te uploaden dat u eerder hebt bewerkt.

1. Wanneer het implementatiemanifest is gevalideerd, selecteert **u Volgende: Controleren** en selecteert u **vervolgens Maken.**

1. Selecteer **onder Model** de optie **Relaties**. Selecteer **+ Relatie toevoegen**. Voer *Downstreamapparaat in* als weergavenaam en selecteer **Thermostaat** als doel. Selecteer **Opslaan**.

1. Selecteer **Publiceren om** de apparaatsjabloon te publiceren.

U hebt nu twee apparaatsjablonen in uw IoT Central toepassing. De **IoT Edge gatewayapparaatsjabloon** en de **thermostaatsjabloon** als downstreamapparaat.

Een gatewayapparaat registreren in IoT Central:

1. Ga in IoT Central toepassing naar de **pagina** Apparaten.

1. Selecteer **IoT Edge gatewayapparaat en** selecteer Een apparaat **maken.** Voer *IoT Edge gatewayapparaat* in als de apparaatnaam, voer *gateway-01* in als de apparaat-id en zorg ervoor dat IoT Edge **gatewayapparaat** is geselecteerd als de apparaatsjabloon. Selecteer **Maken**.

1. Klik in de lijst met apparaten op het **IoT Edge gatewayapparaat** en selecteer vervolgens **Verbinding maken.**

1. Noteer de waarden voor **ID-bereik,** **Apparaat-id** en Primaire sleutel voor het **IoT Edge gatewayapparaat.**  U gebruikt deze later.

Een downstreamapparaat registreren in IoT Central:

1. Navigeer in IoT Central toepassing naar de **pagina** Apparaten.

1. Selecteer **Thermostaat** en selecteer **Een apparaat maken.** Voer *Thermostaat* in als de apparaatnaam, voer *downstream-01* in als de apparaat-id en zorg ervoor dat **Thermostaat** is geselecteerd als apparaatsjabloon. Selecteer **Maken**.

1. Selecteer de Thermostaat in de lijst **met** apparaten en selecteer vervolgens Koppelen **aan gateway.** Selecteer de **IoT Edge gatewayapparaatsjabloon** en het **IoT Edge gatewayapparaat.** Selecteer **Koppelen.**

1. Klik in de lijst met apparaten op de **Thermostaat** en selecteer vervolgens **Verbinding maken.**

1. Noteer de waarden voor **ID-bereik,** **Apparaat-id** en **Primaire** sleutel voor het **thermostaatapparaat.** U gebruikt deze later.

### <a name="deploy-the-gateway-and-downstream-devices"></a>De gateway en downstreamapparaten implementeren

Voor het gemak wordt in dit artikel gebruikgemaakt van virtuele Azure-machines om de gateway en downstreamapparaten uit te voeren. Als u de twee virtuele Azure-machines wilt maken, selecteert u de knop Implementeren in **Azure** hieronder en gebruikt u de informatie in de volgende tabel om het formulier Aangepaste **implementatie in te** vullen:

| Veld | Waarde |
| ----- | ----- |
| Resourcegroep | `ingress-scenario` |
| Gateway voor DNS-label voorvoegsel | Een unieke DNS-naam voor deze machine, zoals `<your name>edgegateway` |
| Downstream DNS-label voorvoegsel | Een unieke DNS-naam voor deze machine, zoals `<your name>downstream` |
| Bereik-ID | Het id-bereik dat u eerder hebt noteren |
| Apparaat-id IoT Edge Gateway | `gateway-01` |
| Apparaatsleutel IoT Edge gateway | De waarde van de primaire sleutel die u eerder hebt noteren |
| Verificatietype | Wachtwoord |
| Beheerderswachtwoord of -sleutel | Uw wachtwoordkeuze voor het **AzureUser-account** op beide virtuele machines. |

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fiot-central-docs-samples%2Fmaster%2Ftransparent-gateway%2FDeployGatewayVMs.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png" alt="Deploy to Azure button" />
</a>

Selecteer **Controleren en maken** en vervolgens **Maken**. Het duurt enkele minuten om de virtuele machines in de resourcegroep voor het **ingress-scenario** te maken.

Controleren of het IoT Edge correct wordt uitgevoerd:

1. Open uw IoT Central toepassing. Navigeer vervolgens **naar IoT Edge gatewayapparaat** in de lijst met apparaten op **de pagina** Apparaten.

1. Selecteer het **tabblad Modules** en controleer de status van de drie modules. Het duurt enkele minuten voordat de IoT Edge wordt opstart in de virtuele machine. Wanneer deze is gestart, is de status van de drie modules **Wordt uitgevoerd.** Als de IoT Edge runtime niet start, zie [Problemen met uw IoT Edge oplossen.](../../iot-edge/troubleshoot.md)

Om uw IoT Edge als gateway te laten functioneren, heeft het enkele certificaten nodig om de identiteit te bewijzen voor downstreamapparaten. In dit artikel wordt gebruikgemaakt van democertificaten. Gebruik certificaten van uw certificeringsinstantie in een productieomgeving.

De democertificaten genereren en deze installeren op uw gatewayapparaat:

1. Gebruik SSH om verbinding te maken met de virtuele machine van uw gatewayapparaat en u aan te melden. U vindt de DNS-naam voor deze virtuele machine in de Azure Portal. Navigeer naar de virtuele machine **edgegateway** in de **resourcegroep voor het ingress-scenario.**

    > [!TIP]
    > Mogelijk moet u poort 22 openen voor SSH-toegang op beide virtuele machines voordat u SSH kunt gebruiken om verbinding te maken vanaf uw lokale computer of de Azure Cloud Shell.

1. Voer de volgende opdrachten uit om de opslagplaats IoT Edge klonen en uw democertificaten te genereren:

    ```bash
    # Clone the repo
    cd ~
    git clone https://github.com/Azure/iotedge.git

    # Generate the demo certificates
    mkdir certs
    cd certs
    cp ~/iotedge/tools/CACertificates/*.cnf .
    cp ~/iotedge/tools/CACertificates/certGen.sh .
    ./certGen.sh create_root_and_intermediate
    ./certGen.sh create_edge_device_ca_certificate "mycacert"
    ```

    Nadat u de vorige opdrachten hebt uitgevoerd, zijn de volgende bestanden klaar voor gebruik in de volgende stappen:

    - *~/certs/certs/azure-iot-test-only.root.ca.cert.pem:* het basis-CA-certificaat dat wordt gebruikt om alle andere democertificaten te maken voor het testen van een IoT Edge scenario.
    - *~/certs/certs/iot-edge-device-mycacert-full-chain.cert.pem:* een CA-certificaat voor apparaten waarnaar wordt verwezen vanuit het *bestand config.yaml.* In een gatewayscenario is dit CA-certificaat de manier waarop IoT Edge apparaat de identiteit verifieert naar downstreamapparaten.
    - *~/certs/private/iot-edge-device-mycacert.key.pem:* de persoonlijke sleutel die is gekoppeld aan het CA-certificaat van het apparaat.

    Zie Democertificaten maken om de apparaatfuncties IoT Edge te testen voor meer informatie [over deze democertificaten.](../../iot-edge/how-to-create-test-certificates.md)

1. Open het *bestand config.yaml* in een teksteditor. Bijvoorbeeld:

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Zoek de `Certificate settings` instellingen. U kunt de certificaatinstellingen als volgt uitcommenter en wijzigen:

    ```text
    certificates:
      device_ca_cert: "file:///home/AzureUser/certs/certs/iot-edge-device-ca-mycacert-full-chain.cert.pem"
      device_ca_pk: "file:///home/AzureUser/certs/private/iot-edge-device-ca-mycacert.key.pem"
      trusted_ca_certs: "file:///home/AzureUser/certs/certs/azure-iot-test-only.root.ca.cert.pem"
    ```

    In het bovenstaande voorbeeld wordt ervan uitgenomen dat u bent aangemeld als **AzureUser** en een apparaat-CA hebt gemaakt met de naam 'mycacert'.

1. Sla de wijzigingen op en start de IoT Edge runtime:

    ```bash
    sudo systemctl restart iotedge
    ```

Als de IoT Edge runtime wordt gestart na uw wijzigingen, verandert de status van de $edgeAgent en **$edgeHub** modules in **Wordt uitgevoerd.**  U kunt deze statuswaarden bekijken op de **pagina Modules** voor uw gatewayapparaat in IoT Central.

Als de runtime niet start, controleert u de wijzigingen die u hebt aangebracht in *config.yaml* en raadpleegt u Problemen met uw [IoT Edge oplossen.](../../iot-edge/troubleshoot.md)

### <a name="connect-downstream-device-to-iot-edge-device"></a>Downstreamapparaat verbinden met IoT Edge apparaat

Een downstreamapparaat verbinden met het IoT Edge gatewayapparaat:

1. Gebruik SSH om verbinding te maken met de virtuele machine van uw downstreamapparaat en u aan te melden. U vindt de DNS-naam voor deze virtuele machine in de Azure Portal. Navigeer naar de virtuele machine **leafdevice** in de **resourcegroep voor het ingress-scenario.**

    > [!TIP]
    > Mogelijk moet u poort 22 openen voor SSH-toegang op beide virtuele machines voordat u SSH kunt gebruiken om verbinding te maken vanaf uw lokale computer of de Azure Cloud Shell.

1. Voer de volgende opdracht uit om de GitHub-opslagplaats te klonen met de broncode voor het voorbeeld-downstreamapparaat:

    ```bash
    cd ~
    git clone https://github.com/iot-for-all/iot-central-transform-with-iot-edge
    ```

1. Voer de volgende opdrachten uit om het vereiste certificaat van het gatewayapparaat te `scp` kopiëren. Met `scp` deze opdracht wordt de hostnaam gebruikt om de virtuele `edgegateway` gatewaymachine te identificeren. U wordt gevraagd om uw wachtwoord:

    ```bash
    cd ~/iot-central-transform-with-iot-edge
    scp AzureUser@edgegateway:/home/AzureUser/certs/certs/azure-iot-test-only.root.ca.cert.pem leafdevice/certs
    ```

1. Navigeer *naar de map leafdevice* en installeer de vereiste pakketten. Voer vervolgens de `build` scripts en uit om het apparaat in `start` terichten en te verbinden met de gateway:

    ```bash
    cd ~/iot-central-transform-with-iot-edge/leafdevice
    sudo apt update
    sudo apt install nodejs npm node-typescript
    npm install
    npm run-script build
    npm run-script start
    ```

1. Voer de apparaat-id, bereik-id en SAS-sleutel in voor het downstreamapparaat dat u eerder hebt gemaakt. Voer in als `edgegateway` hostnaam. De uitvoer van de opdracht ziet er als volgende uit:

    ```output
    Registering device downstream-01 with scope 0ne00284FD9
    Registered device downstream-01.
    Connecting device downstream-01
    Connected device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    ```

### <a name="verify"></a>Verifiëren

Als u wilt controleren of het scenario wordt uitgevoerd, gaat u **naar IoT Edge gatewayapparaat** in IoT Central:

:::image type="content" source="media/howto-transform-data/transformed-data.png" alt-text="Schermopname met getransformeerde gegevens op de pagina apparaten.":::

- Selecteer **Modules**. Controleer of de drie IoT Edge modules **$edgeAgent**, **$edgeHub** **en transformmodule** worden uitgevoerd.
- Selecteer **downstreamapparaten en** controleer of het downstreamapparaat is ingericht.
- Selecteer **Onbewerkte gegevens.** De telemetriegegevens in de **kolom Niet-gemodelleerde gegevens** zien er als volgende uit:

    ```json
    {"device":{"deviceId":"downstream-01"},"measurements":{"temperature":85.21208,"pressure":59.97321,"humidity":77.718124,"scale":"farenheit"}}
    ```

Omdat het IoT Edge apparaat de gegevens van het downstreamapparaat transformeert, wordt de telemetrie gekoppeld aan het gatewayapparaat in IoT Central. Als u de telemetrie wilt visualiseren, maakt u een nieuwe versie van **IoT Edge-gatewayapparaatsjabloon** met definities voor de telemetrietypen.

## <a name="data-transformation-at-egress"></a>Gegevenstransformatie bij egress

U kunt uw apparaten verbinden met IoT Central, de apparaatgegevens exporteren naar een berekeningsen engine om deze te transformeren en de getransformeerde gegevens vervolgens terugsturen naar IoT Central voor apparaatbeheer en -analyse. Bijvoorbeeld:

- Uw apparaten verzenden locatiegegevens naar IoT Central.
- IoT Central exporteert de gegevens naar een berekeningsen engine die de locatiegegevens verbetert met weersinformatie.
- De berekeningsen engine stuurt de verbeterde gegevens terug naar IoT Central.

U kunt de apparaatbrug [IoT Central gebruiken](https://github.com/Azure/iotc-device-bridge) als berekeningsent engine om gegevens te transformeren die zijn geëxporteerd uit IoT Central.

Een voordeel van het transformeren van gegevens bij het verzenden is dat uw apparaten rechtstreeks verbinding maken met IoT Central, waardoor u eenvoudig opdrachten naar apparaten kunt verzenden of apparaateigenschappen kunt bijwerken. Met deze methode kunt u echter meer berichten gebruiken dan uw maandelijkse toewijzing en de kosten voor het gebruik van Azure IoT Central.

### <a name="use-the-iot-central-device-bridge-to-transform-device-data"></a>De apparaatbrug IoT Central apparaat te gebruiken om apparaatgegevens te transformeren

:::image type="content" source="media/howto-transform-data/transform-egress.png" alt-text="Gegevenstransformatie bij een IoT Edge" border="false":::

In dit scenario transformeert een berekeningsen engine apparaatgegevens die zijn geëxporteerd uit IoT Central voordat deze worden terugsturen naar uw IoT Central toepassing. Op hoog niveau zijn de stappen voor het configureren van dit scenario:

1. **De berekeningsen engine instellen:** Maak een IoT Central apparaatbrug om te fungeren als een berekeningsent engine voor gegevenstransformatie.

1. **Apparaatgegevens in de apparaatbrug transformeren:** Transformeer gegevens in de apparaatbrug door de functiecode van de apparaatbrug te wijzigen voor uw use-case voor gegevenstransformatie.

1. **Schakel de gegevensstroom van IoT Central naar de apparaatbrug in:** Exporteert de gegevens van IoT Central apparaatbrug voor transformatie. Vervolgens worden de getransformeerde gegevens doorgestuurd naar IoT Central. Wanneer u de gegevensexport maakt, gebruikt u filters voor bericht-eigenschappen om alleen niet-omgevormde gegevens te exporteren.

1. **Controleren:** verbind uw apparaat met IoT Central app en controleer op onbewerkte apparaatgegevens en getransformeerde gegevens in IoT Central.

<!-- To Do - doesn't the device send JSON data? -->
In het voorbeeld dat in de volgende secties wordt beschreven, verzendt het apparaat CSV-gegevens in de volgende indeling naar IoT Edge gatewayapparaat:

```csv
"<temperature in degrees C>, <humidity>, <latitude>, <longitude>"
```

U gebruikt de apparaatbrug om de apparaatgegevens te transformeren door:

- Het wijzigen van de temperatuureenheid van de temperatuurgrade in fahrenheit.
- De apparaatgegevens verrijken met weersgegevens die zijn verzameld uit [de Open Weather-service](https://openweathermap.org/) voor de waarden voor breedtegraad en lengtegraad.

De apparaatbrug verzendt de getransformeerde gegevens vervolgens naar IoT Central in de volgende indeling:

```json
{
  "temp": <temperature in degrees F>,
  "humidity": <humidity>,
  "lat": <latitude>,
  "lon": <logitude>,
  "weather": {
    "weather_temp": <temperature at lat/lon>,
    "weather_humidity": <humidity at lat/lon>,
    "weather_pressure": <pressure at lat/lon>,
    "weather_windspeed": <wind speed at lat/lon>,
    "weather_clouds": <cloud cover at lat/lon>,
    "weather_uvi": <UVI at lat/lon>
  }
}
```

De volgende stappen laten zien hoe u dit scenario in kunt stellen en configureren:

### <a name="retrieve-your-iot-central-connection-settings"></a>Uw verbindingsinstellingen voor IoT Central ophalen

Voordat u dit scenario in stelt, moet u enkele verbindingsinstellingen van uw IoT Central toepassing:

1. Meld u aan bij uw IoT Central toepassing.

1. **Navigeer naar Beheer > Apparaatverbinding**.

1. Noteer het **id-bereik**. U gebruikt deze waarde later.

1. Selecteer de **registratiegroep SaS-IoT-Devices.** Noteer de primaire sleutel van shared access signature. U gebruikt deze waarde later.

### <a name="set-up-a-compute-engine"></a>Een berekeningsen engine instellen

In dit scenario wordt dezelfde implementatie Azure Functions gebruikt als de IoT Central apparaatbrug. Als u de apparaatbrug wilt implementeren, selecteert u de knop Implementeren in **Azure** hieronder en gebruikt u de informatie in de volgende tabel om het formulier Aangepaste **implementatie in te** vullen:

| Veld | Waarde |
| ----- | ----- |
| Resourcegroep | Maak een nieuwe resourcegroep met de naam `egress-scenario` |
| Regio | Selecteer de regio het dichtst bij u in de buurt. |
| Bereik-ID | Gebruik het **id-bereik** dat u eerder hebt noteren. |
| IoT Central SAS-sleutel | Gebruik de primaire sleutel voor shared access signature voor de **registratiegroep SaS-IoT-Devices.** U hebt deze waarde eerder al noteert. |

[ ![ Implementeer in Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fiotc-device-bridge%2Fmaster%2Fazuredeploy.json).

Selecteer **Controleren en maken** en vervolgens **Maken**. Het duurt enkele minuten om de Azure-functie en gerelateerde resources te maken in de **resourcegroep voor het scenario van het egress-scenario.**

### <a name="transform-device-data-in-the-device-bridge"></a>Apparaatgegevens transformeren in de apparaatbrug

De apparaatbrug configureren om de geëxporteerde apparaatgegevens te transformeren:

1. Vraag een API-sleutel voor de toepassing op via de Weather-service. Een account is gratis met beperkt gebruik van de service. Als u een API-sleutel voor een toepassing wilt maken, maakt u een account in [Weather-service Portal openen](https://openweathermap.org/) en volgt u de instructies. Later gebruikt u de api-sleutel Voor weer openen.

1. Navigeer Azure Portal naar Functie-app in de **resourcegroep egress-scenario.**

1. Selecteer in het linkernavigatievenster in **Ontwikkelprogramma's** **de App Service-editor (preview)**.

1. Selecteer **&rarr; Go** om de **pagina** App Service-editor openen. Breng de volgende wijzigingen aan:

    1. Open het *bestand wwwroot/IoTCIntegration/index.js.* Vervang alle code in dit bestand door de code in [index.js](https://raw.githubusercontent.com/iot-for-all/iot-central-compute/main/Azure_function/index.js).

    1. Werk in de *nieuweindex.js* het `openWeatherAppId` variabelebestand bij met de API-sleutel Weer openen die u eerder hebt verkregen.

        ```javascript
        const openWeatherAppId = '<Your Open Weather API Key>'
        ```

    1. Voeg een bericht-eigenschap toe aan de gegevens die door de functie worden verzonden om IoT Central. IoT Central gebruikt deze eigenschap om te voorkomen dat de getransformeerde gegevens worden geëxporteerd. Als u deze wijziging wilt maken, opent u *het bestand wwwroot/IoTCIntegration/lib/engine.js.* Zoek de volgende code:

        ```javascript
        if (timestamp) {
          message.properties.add('iothub-creation-time-utc', timestamp);
        }
        ```

        Voeg de volgende code toe net na de code in het vorige codefragment:

        ```javascript
        // add a message property that we can look for in data export to not re-compute computed telemetry
        message.properties.add('computed', true);
        ```

        Ter referentie kunt u een voltooid voorbeeld van hetengine.js[weergeven.](https://raw.githubusercontent.com/iot-for-all/iot-central-compute/main/Azure_function/lib/engine.js)

1. Selecteer in **App Service-editor** de optie **Console** in het linkernavigatievenster. Voer de volgende opdrachten uit om de vereiste pakketten te installeren:

    ```bash
    cd IoTCIntegration
    npm install
    ```

    Het kan enkele minuten duren voor deze opdracht is uitgevoerd.

1. Ga terug naar de **pagina Overzicht van Azure-functies** en start de functie opnieuw op:

    :::image type="content" source="media/howto-transform-data/azure-function.png" alt-text="De functie opnieuw starten":::

1. Selecteer **Functies** in het linkernavigatievenster. Selecteer vervolgens **IoTCIntegration.** Selecteer **Code + Testen.**

1. Noteer de functie-URL. U hebt deze waarde later nodig:

    :::image type="content" source="media/howto-transform-data/get-function-url.png" alt-text="De functie-URL op te halen":::

### <a name="enable-data-flow-from-iot-central-to-the-device-bridge"></a>Gegevensstroom van IoT Central naar de apparaatbrug inschakelen

In deze sectie wordt beschreven hoe u de toepassing Azure IoT Central instellen.

Sla eerst het [apparaatmodelbestand](https://raw.githubusercontent.com/iot-for-all/iot-central-compute/main/model.json) op uw lokale computer op.

Als u een apparaatsjabloon wilt toevoegen aan IoT Central toepassing, navigeert u naar IoT Central toepassing en gaat u vervolgens als volgende te werk:

1. Meld u aan bij IoT Central toepassing en navigeer naar de **pagina Apparaatsjablonen.**

1. Selecteer **+ Nieuw,** selecteer **IoT-apparaat,** selecteer **Volgende: Aanpassen** en voer *Rekenmodel in* als sjabloonnaam. Selecteer **Volgende: Review**. Selecteer vervolgens **Maken**.

1. Selecteer **Een model importeren en** blader naar demodel.jsbestand *dat* u eerder hebt gedownload.

1. Nadat het model is geïmporteerd, selecteert u **Publiceren om** de apparaatsjabloon **rekenmodel** te publiceren.

De gegevensexport instellen om gegevens naar uw Device Bridge te verzenden:

1. Selecteer in IoT Central toepassing **gegevensexport.**

1. Selecteer **+ Nieuwe bestemming om** een bestemming te maken voor gebruik met de apparaatbrug. Roep de doelfunctie *Compute aan* bij **Doeltype** en selecteer **Webhook.** Voor de Callback-URL selecteert u Plakken in de functie-URL die u eerder hebt noteren. Laat **autorisatie** op **Geen verificatie.**

1. Sla de wijzigingen op.

1. Selecteer + **Nieuwe export en maak** een gegevensexport met de naam *Rekenexport.*

1. Voeg een filter toe om alleen apparaatgegevens te exporteren voor de apparaatsjabloon die u gebruikt. Selecteer **+ Filteren,** selecteer het item **Apparaatsjabloon,** selecteer de operator **Is** gelijk aan en selecteer de apparaatsjabloon **Rekenmodel** die u zojuist hebt gemaakt.

1. Voeg een berichtfilter toe om onderscheid te maken tussen getransformeerde en niet-getransformeerde gegevens. Met dit filter wordt voorkomen dat getransformeerde waarden terug worden verzonden naar de apparaatbrug. Selecteer **het filter + Bericht-eigenschap** en voer de berekende naamwaarde in en selecteer vervolgens de operator Bestaat **niet.**  De `computed` tekenreeks wordt gebruikt als trefwoord in de voorbeeldcode van de apparaatbrug.

1. Selecteer voor de bestemming het doel van **de Compute-functie** dat u eerder hebt gemaakt.

1. Sla de wijzigingen op. Na een minuut of zo wordt **de exportstatus** als In **orde weergeven.**

### <a name="verify"></a>Verifiëren

Het voorbeeldapparaat dat u gebruikt om het scenario te testen, wordt geschreven in Node.js. Zorg ervoor dat u Node.js NPM hebt geïnstalleerd op uw lokale computer. Als u deze vereisten niet wilt installeren, gebruikt u de Azure Cloud Shell[vooraf](https://shell.azure.com/) geïnstalleerd.

Een voorbeeldapparaat uitvoeren dat het scenario test:

1. Kloon de GitHub-opslagplaats met de voorbeeldcode en voer de volgende opdracht uit:

    ```bash
    git clone https://github.com/iot-for-all/iot-central-compute
    ```

1. Als u het voorbeeldapparaat wilt verbinden met IoT Central toepassing, bewerkt u de verbindingsinstellingen in het bestand *iot-central-compute/device/device.js.* Vervang de bereik-id en groeps-SAS-sleutel door de waarden die u eerder hebt noteren:

    ```javascript
    // These values need to be filled in from your Azure IoT Central application
    //
    const scopeId = "<IoT Central Scope Id value>";
    const groupSasKey = "<IoT Central Group SAS key>";
    //
    ```

    Sla de wijzigingen op.

1. Gebruik de volgende opdrachten om de vereiste pakketten te installeren en het apparaat uit te voeren:

    ```bash
    cd ~/iot-central-compute/device
    npm install
    node device.js
    ```

1. Het resultaat van deze opdracht ziet eruit als de volgende uitvoer:

    ```output
    registration succeeded
    assigned hub=iotc-2bd611b0....azure-devices.net
    deviceId=computeDevice
    Client connected
    send status: MessageEnqueued [{"data":"33.23, 69.09, 30.7213, -61.1192"}]
    send status: MessageEnqueued [{"data":"2.43, 75.86, -2.6358, 162.935"}]
    send status: MessageEnqueued [{"data":"6.19, 76.55, -14.3538, -82.314"}]
    send status: MessageEnqueued [{"data":"33.26, 48.01, 71.9172, 48.6606"}]
    send status: MessageEnqueued [{"data":"40.5, 36.41, 14.6043, 14.079"}]
    ```

1. Navigeer IoT Central uw toepassing naar het apparaat met de **naam computeDevice**. In de **weergave Onbewerkte** gegevens zijn twee verschillende telemetriestromen die ongeveer om de vijf seconden worden weergeven. De stroom met niet-gemodelleerde gegevens is de oorspronkelijke telemetrie. De stroom met gemodelleerde gegevens zijn de gegevens die door de functie zijn getransformeerd:

    :::image type="content" source="media/howto-transform-data/egress-telemetry.png" alt-text="Schermopname van de oorspronkelijke en getransformeerde onbewerkte gegevens.":::

## <a name="clean-up-resources"></a>Resources opschonen

Als u de Azure-resources die u hebt gemaakt tijdens het volgen van de stappen in deze handleiding niet meer nodig hebt, verwijdert u de [resourcegroepen in de Azure Portal](https://portal.azure.com/?r=1#blade/HubsExtension/BrowseResourceGroups).

De twee resourcegroepen die u in deze handleiding hebt gebruikt, zijn **ingress-scenario** en **egress-scenario.**

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over de verschillende opties voor het transformeren van apparaatgegevens voor IoT Central, zowel bij in- als uittreding. Het artikel bevatte walkthroughs voor twee specifieke scenario's:

- Gebruik een IoT Edge-module om gegevens van downstreamapparaten te transformeren voordat de gegevens naar uw IoT Central verzonden.
- Gebruik Azure Functions om gegevens buiten de IoT Central. In dit scenario gebruikt IoT Central een gegevensexport om binnenkomende gegevens te verzenden naar een Azure-functie die moet worden getransformeerd. De functie stuurt de getransformeerde gegevens terug naar uw IoT Central toepassing.

Nu u hebt geleerd hoe u apparaatgegevens buiten uw Azure IoT Central-toepassing kunt transformeren, kunt u leren Hoe u analyse gebruikt om apparaatgegevens te analyseren [in IoT Central.](howto-create-analytics.md)
