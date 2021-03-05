---
title: 'Zelf studie: Azure Time Series Insights gebruiken voor het opslaan en analyseren van uw Azure IoT-Plug en Play apparaat-telemetrie'
description: 'Zelf studie: een Time Series Insights omgeving instellen en uw IoT-hub verbinden om telemetrie te bekijken en te analyseren op uw IoT Plug en Play-apparaten.'
author: lyrana
ms.author: lyhughes
ms.date: 10/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 55fa10cce038c83f0758a9537a916e2dca7e13f9
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181683"
---
# <a name="tutorial-create-and-configure-a-time-series-insights-gen2-environment"></a>Zelf studie: een Time Series Insights Gen2-omgeving maken en configureren

In deze zelfstudie leert u hoe u een [Time Series Insights Gen2](../time-series-insights/overview-what-is-tsi.md)-omgeving maakt en configureert voor integratie met uw IoT Plug en Play-oplossing. Gebruik Time Series Insights om tijdreeksgegevens te verzamelen, te verwerken, op te slaan, te visualiseren en er query's op uit te voeren op de schaal van IoT (Internet of Things).

In deze zelfstudie hebt u

> [!div class="checklist"]
> * Richt een Time Series Insights omgeving in en verbind uw IoT-hub als een streaming-gebeurtenis bron.
> * Werk met model synchronisatie om uw [Time Series-model](../time-series-insights/concepts-model-overview.md)te ontwerpen.
> * Gebruik de [DTDL-voorbeeld model bestanden (Digital Apparaatdubbels Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl) die u hebt gebruikt voor de temperatuur regelaar en de Thermo staat-apparaten.

> [!NOTE]
> Deze integratie tussen Time Series Insights en IoT-Plug en Play is in preview. De manier waarop DTDL-apparaatmodellen worden toegewezen aan het Time Series-model van Time Series Insights, kan wijzigen. 

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

U hebt nu het volgende:

* Een Azure IoT-hub.
* Een DPS-instantie (Device Provisioning Service) die aan uw IoT-hub is gekoppeld. De DPS-instantie moet een afzonderlijke apparaatinschrijving voor uw IoT Plug en Play-apparaat hebben.
* Een verbinding met de IoT-hub vanaf een apparaat met één of meerdere onderdelen, waarop gesimuleerde gegevens worden gestreamd.

Om de vereiste van het lokaal installeren van de Azure CLI te vermijden, kunt u Azure Cloud Shell gebruiken om de cloudservices in te stellen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-your-event-source"></a>Uw gebeurtenisbron voorbereiden

De IoT Hub die u eerder hebt gemaakt, wordt de [gebeurtenisbron](../time-series-insights/concepts-streaming-ingestion-event-sources.md) van de Time Series Insights-omgeving.

> [!IMPORTANT]
> Schakel eventuele bestaande IoT Hub-routes uit. Er is een bekend probleem dat optreedt als er een IoT-hub wordt gebruikt waarop [routering](../iot-hub/iot-hub-devguide-messages-d2c.md#routing-endpoints) is geconfigureerd. Schakel alle routeringseindpunten tijdelijk uit. Wanneer uw IoT-hub is verbonden met Time Series Insights, kunt u de routeringseindpunten weer inschakelen.

Maak op uw IoT-hub een unieke consumentengroep die door Time Series Insights kan worden geconsumeerd. Vervang in het volgende voorbeeld `my-pnp-hub` door de naam van de IoT-hub die u eerder hebt gebruikt.

```azurecli-interactive
az iot hub consumer-group create --hub-name my-pnp-hub --name tsi-consumer-group
```

## <a name="choose-a-time-series-id"></a>Een Time Series-id kiezen

Wanneer u de Time Series Insights-omgeving inricht, moet u een *tijdreeks-id* selecteren. Het is belangrijk dat u de juiste tijdreeks-id selecteert. Deze eigenschap is onveranderbaar en kan niet worden gewijzigd nadat deze is ingesteld. Een tijdreeks-id lijkt op een partitiesleutel voor een database. De tijdreeks-id fungeert als de primaire sleutel voor het Time Series-model. Raadpleeg [Best practices voor het kiezen van een tijdreeks-id](../time-series-insights/how-to-select-tsid.md) voor meer informatie.

Als IoT-Plug en Play-gebruiker geeft u als de tijdreeks-id een _samengestelde sleutel_ op, die bestaat uit `iothub-connection-device-id` en `dt-subject`. De IoT-hub voegt deze systeemeigenschappen toe die respectievelijk uw IoT Plug en Play-apparaat-id en de namen van de apparaatonderdelen bevatten.

Zelfs als uw IoT Plug en Play-apparaatmodellen momenteel geen onderdelen gebruiken, moet u `dt-subject` opnemen als onderdeel van een samengestelde sleutel, zodat u in de toekomst onderdelen kunt gebruiken. Omdat de tijdreeks-id onveranderbaar is, raadt Microsoft aan om deze optie in te schakelen voor het geval u deze in de toekomst nodig hebt.

> [!NOTE]
> De voorbeelden in dit artikel gelden voor het `TemperatureController`-apparaat met meerdere onderdelen. Maar de concepten zijn hetzelfde voor het `Thermostat`-apparaat zonder onderdelen.

## <a name="provision-your-time-series-insights-environment"></a>Uw Time Series Insights-omgeving inrichten

In deze sectie wordt beschreven hoe u een Azure Time Series Insights Gen2-omgeving inricht.

Voer de volgende opdracht uit om:

* Een Azure Storage-account te maken als de [koude opslag](../time-series-insights/concepts-storage.md#cold-store) van uw omgeving. Dit account is ontworpen voor langetermijnretentie en voor het analyseren van historische gegevens.
  * Vervang in uw code `mytsicoldstore` door een unieke naam voor het koude-opslagaccount.
* Een Azure Time Series Insights Gen2-omgeving maken. De omgeving wordt gemaakt met semi-dynamische opslag met een retentieperiode van zeven dagen. Het koude-opslagaccount wordt toegevoegd voor oneindige retentie.
  * Vervang in uw code `my-tsi-env` door een unieke naam voor uw Time Series Insights-omgeving.
  * Vervang in uw code `my-pnp-resourcegroup` door de naam van de resourcegroep die u tijdens het instellen hebt gebruikt.
  * Uw eigenschap Tijdreeks-id is `iothub-connection-device-id, dt-subject`.

```azurecli-interactive
storage=mytsicoldstore
rg=my-pnp-resourcegroup
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv)
az tsi environment gen2 create --name "my-tsi-env" --location eastus2 --resource-group $rg --sku name="L1" capacity=1 --time-series-id-properties name=iothub-connection-device-id type=String --time-series-id-properties name=dt-subject type=String --warm-store-configuration data-retention=P7D --storage-configuration account-name=$storage management-key=$key
```

Maak verbinding met de IoT Hub-gebeurtenisbron. Vervang `my-pnp-resourcegroup`, `my-pnp-hub` en `my-tsi-env` door de waarden die u hebt gekozen. Met de volgende opdracht wordt verwezen naar de consumentengroep voor Time Series Insights die u eerder hebt gemaakt:

```azurecli-interactive
rg=my-pnp-resourcegroup
iothub=my-pnp-hub
env=my-tsi-env
es_resource_id=$(az iot hub create -g $rg -n $iothub --query id --output tsv)
shared_access_key=$(az iot hub policy list -g $rg --hub-name $iothub --query "[?keyName=='service'].primaryKey" --output tsv)
az tsi event-source iothub create --event-source-name iot-hub-event-source --environment-name $env --resource-group $rg --location eastus2 --consumer-group-name tsi-consumer-group --key-name iothubowner --shared-access-key $shared_access_key --event-source-resource-id $es_resource_id --iot-hub-name $iothub
```

Ga in [Azure Portal](https://portal.azure.com) naar uw resourcegroep en selecteer vervolgens de nieuwe Time Series Insights-omgeving. Ga naar de **URL voor Time Series Insights Explorer** die wordt weergegeven in het instantieoverzicht:

![Schermopname van de overzichtspagina in de portal.](./media/tutorial-configure-tsi/view-environment.png)

In Explorer ziet u drie instanties:

* &lt;uw pnp-apparaat-id&gt;, thermostaat1
* &lt;uw pnp-apparaat-id&gt;, thermostaat2
* &lt;uw pnp-apparaat-id&gt;, `null`

> [!NOTE]
> De derde tag vertegenwoordigt telemetrie van `TemperatureController` zelf, zoals de werkset van het apparaatgeheugen. Omdat dit een eigenschap op het hoogste niveau is, is de waarde voor de onderdeelnaam null. In een latere stap wijzigt u deze naam in een meer gebruikersvriendelijke naam.

![Schermopname met drie instanties in Explorer.](./media/tutorial-configure-tsi/tsi-env-first-view.png)

## <a name="configure-model-translation"></a>Modelvertaling configureren

Vervolgens gaat u het DTDL-apparaatmodel vertalen naar het assetmodel in Azure Time Series Insights. Het Time Series-model van Time Series Insights is een hulpmiddel voor semantische modellering om gegevens te contextualiseren. Het model heeft drie kernonderdelen:

* [Time Series-modelinstanties](../time-series-insights/concepts-model-overview.md#time-series-model-instances) zijn virtuele representaties van de tijdreeksen zelf. Exemplaren worden uniek geïdentificeerd met de tijdreeks-id.
* [Time Series-modelhiërarchieën](../time-series-insights/concepts-model-overview.md#time-series-model-hierarchies) organiseren instanties door eigenschapsnamen en hun relaties te specificeren.
* [Time Series-modeltypen](../time-series-insights/concepts-variables.md) helpen u [variabelen](../time-series-insights/concepts-model-overview.md#time-series-model-types) of formules voor berekeningen te definiëren. Typen zijn gekoppeld aan een specifiek exemplaar.

### <a name="define-your-types"></a>Uw typen definiëren

U kunt beginnen met het opnemen van gegevens in Azure Time Series Insights Gen2 zonder vooraf een model te hebben gedefinieerd. Wanneer telemetrie aankomt, probeert Time Series Insights automatisch tijdreeksexemplaren op te lossen, op basis van de waarden van de eigenschap Tijdreeks-id. Aan alle exemplaren wordt het *standaardtype* toegewezen. U moet handmatig een nieuw type maken om uw exemplaren juist te categoriseren. 

De volgende details bieden een overzicht van de eenvoudigste methode om uw DTDL-apparaatmodellen te synchroniseren met de Time Series-modeltypen:

* De Digital Twin Model Identifier wordt de type-id.
* De naam van het type kan de modelnaam of de weergavenaam zijn.
* De modelbeschrijving wordt de beschrijving van het type.
* Er wordt ten minste één type-variabele gemaakt voor elk telemetriegegeven dat een numeriek schema heeft.
  * Er kunnen alleen numerieke gegevenstypen worden gebruikt voor variabelen, maar als een waarde wordt verzonden als een ander type dat kan worden geconverteerd, bijvoorbeeld `"0"`, kunt u een [conversiefunctie](/rest/api/time-series-insights/reference-time-series-expression-syntax#conversion-functions) gebruiken, zoals `toDouble`.
* De naam van de variabele kan de telemetrienaam of de weergavenaam zijn.
* Wanneer u de Time Series-expressievariabele definieert, raadpleegt u de naam van de verbonden telemetrie en het bijbehorende telemetriegegevenstype.

| DTDL JSON | Time Series-modeltype JSON | Voorbeeldwaarde |
|-----------|------------------|-------------|
| `@id` | `id` | `dtmi:com:example:TemperatureController;1` |
| `displayName`    | `name`   |   `Temperature Controller`  |
| `description`  |  `description`  |  `Device with two thermostats and remote reboot.` |  
|`contents` (matrix)| `variables` (object)  | Zie het volgende voorbeeld

![Schermopname met D T D L naar Time Series-modeltype.](./media/tutorial-configure-tsi/DTDL-to-TSM-Type.png)

> [!NOTE]
> Dit voorbeeld laat drie variabelen zien, maar elk type kan tot wel 100 variabelen hebben. Verschillende variabelen kunnen naar dezelfde telemetriewaarde verwijzen om verschillende berekeningen uit te voeren. Zie [Syntaxis van Time Series-expressies voor Time Series Insights Gen2](/rest/api/time-series-insights/reference-time-series-expression-syntax) voor de volledige lijst met filters, aggregaties en scalaire functies.

Open een teksteditor en sla de volgende JSON op uw lokale station op.

```JSON
{
  "put": [
    {
      "id": "dtmi:com:example:TemperatureController;1",
      "name": "Temperature Controller",
      "description": "Device with two thermostats and remote reboot.",
      "variables": {
        "workingSet": {
          "kind": "numeric",
          "value": {
            "tsx": "coalesce($event.workingSet.Long, toLong($event.workingSet.Double))"
          }, 
          "aggregation": {
            "tsx": "avg($value)"
          }
        },
        "temperature": {
          "kind": "numeric",
          "value": {
            "tsx": "coalesce($event.temperature.Long, toLong($event.temperature.Double))"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        },
        "eventCount": {
          "kind": "aggregate",
          "aggregation": {
            "tsx": "count()"
          }
        }
      }
    }
  ]
}
```

Selecteer in Time Series Insights Explorer het modelpictogram aan de linkerkant om het tabblad **Model** te openen. Selecteer **Typen** en selecteer vervolgens **JSON uploaden**:

![Schermopname van het uploaden van de JSON.](./media/tutorial-configure-tsi/upload-type.png)

Selecteer **Bestand kiezen**, selecteer de JSON die u eerder hebt opgeslagen en selecteer vervolgens **Uploaden**.

U ziet het zojuist gedefinieerde type **TemperatureController**.

### <a name="create-a-hierarchy"></a>Een hiërarchie maken

Maak een hiërarchie om de tags onder de bijbehorende bovenliggende `TemperatureController` te organiseren. Het volgende eenvoudige voorbeeld heeft één niveau, maar IoT-oplossingen hebben meestal vele niveaus voor nesten, om tags te contextualiseren in de bijbehorende fysieke en semantische positie binnen een organisatie.

Selecteer **Hiërarchieën** en selecteer vervolgens **Hiërarchie toevoegen**. Voer *Apparaatvloot* in als naam. Maak één niveau met de naam *Apparaatnaam*. Selecteer vervolgens **Opslaan**.

![Schermopname van het toevoegen van een hiërarchie.](./media/tutorial-configure-tsi/add-hierarchy.png)

### <a name="assign-your-instances-to-the-correct-type"></a>Uw exemplaren aan het juiste type toewijzen

Vervolgens wijzigt u het type van uw exemplaren en plaatst u ze in de hiërarchie.

Selecteer het tabblad **Instanties**. Zoek de instantie die de werkset van het apparaat vertegenwoordigt en selecteer vervolgens het pictogram **Bewerken** helemaal rechts.

![Schermopname van het bewerken van een instantie.](./media/tutorial-configure-tsi/edit-instance.png)

Open de vervolgkeuzelijst **Type** en selecteer vervolgens **Temperatuurregeling**. Voer *defaultComponent, <your device name>* in om de naam van het exemplaar bij te werken dat alle tags op het hoogste niveau vertegenwoordigt die zijn gekoppeld aan het apparaat.

![Schermopname van het wijzigen van een instantietype.](./media/tutorial-configure-tsi/change-type.png)

Voordat u **Opslaan** selecteert, selecteert u eerst het tabblad **Instantievelden** en selecteert u vervolgens **Apparaatvloot**. U kunt de telemetrie groeperen door *\<your device name> - Temperatuurregeling* in te voeren. Selecteer vervolgens **Opslaan**.

![Schermopname die laat zien hoe een instantie aan een hiërarchie kan worden toegewezen](./media/tutorial-configure-tsi/assign-to-hierarchy.png)

Herhaal de vorige stappen om aan de thermostaattags het juiste type en de juiste hiërarchie toe te wijzen.

## <a name="view-your-data"></a>Uw gegevens weergeven

Ga terug naar het grafiekdeelvenster en vouw **Apparaatvloot** > uw apparaat uit. Selecteer **thermostaat1** en selecteer de variabele **Temperatuur**. Selecteer vervolgens **Toevoegen** om de waarde in de grafiek te plaatsen. Doe hetzelfde voor **thermostat2** en de waarde **defaultComponent**-**werkset**.

![Schermopname van het wijzigen van een instantietype voor thermostaat2.](./media/tutorial-configure-tsi/charting-values.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [iot-pnp-clean-resources](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> Raadpleeg [Azure Time Series Insights Explorer](../time-series-insights/concepts-ux-panels.md) voor meer informatie over de verschillende grafiekopties, waaronder intervalgroottes en y-asbesturingselementen.
