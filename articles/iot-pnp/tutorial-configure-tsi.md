---
title: Time Series Insights gebruiken om telemetrie van uw Azure IoT Plug and Play-apparaten op te slaan en te analyseren | Microsoft Docs
description: Stel een Time Series Insights-omgeving in en verbind de IoT-hub om telemetrie van uw IoT Plug en Play-apparaten te bekijken en te analyseren.
author: lyrana
ms.author: lyhughes
ms.date: 10/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: ad5c6f205fc832eb125e52b4135990fc58742e62
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96453238"
---
# <a name="preview-tutorial-create-and-connect-to-time-series-insights-gen2-to-store-visualize-and-analyze-iot-plug-and-play-device-telemetry"></a>Preview-zelfstudie: Time Series Insights Gen2 maken en er verbinding mee maken om telemetrie van IoT Plug and Play-apparaten op te slaan, te visualiseren en te analyseren

In deze zelfstudie leert u hoe u een [Azure TSI Gen2](../time-series-insights/overview-what-is-tsi.md)-omgeving (Time Series Insights) maakt en juist configureert voor integratie met uw IoT Plug en Play-oplossing. Gebruik TSI om tijdreeksgegevens te verzamelen, te verwerken, op te slaan, te visualiseren en er query's op uit te voeren op IoT-schaal (Internet of Things).

Eerst richt u een TSI-omgeving in en verbindt u de IoT-hub als een streaming-gebeurtenisbron. Vervolgens voert u modelsynchronisatie uit om het [Time Series-model](../time-series-insights/concepts-model-overview.md) van uw TSI-omgeving te maken, op basis van de [DTDL](https://github.com/Azure/opendigitaltwins-dtdl)-voorbeeldmodelbestanden (Digital Twins Definition Language) die u hebt gebruikt voor de temperatuurregelings- en thermostaatapparaten.

> [!NOTE]
> Deze integratie is in de preview-fase. Hoe DTDL-apparaatmodellen worden toegewezen aan het Time Series-model kan worden gewijzigd.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

U hebt nu het volgende:

* Een Azure IoT-hub.
* Een DPS-exemplaar dat is gekoppeld aan de IoT-hub, met een afzonderlijke apparaatinschrijving voor uw IoT Plug en Play-apparaat.
* Een verbinding met de IoT-hub vanaf een apparaat met één of meerdere onderdelen, waarop gesimuleerde gegevens worden gestreamd.

Om de vereiste van het lokaal installeren van de Azure CLI te vermijden, kunt u de Azure Cloud Shell gebruiken om de cloudservices in te stellen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-your-event-source"></a>Uw gebeurtenisbron voorbereiden

De IoT Hub die u eerder hebt gemaakt, wordt de [gebeurtenisbron](../time-series-insights/concepts-streaming-ingestion-event-sources.md) van de TSI-omgeving.

> [!IMPORTANT]
> Schakel eventuele bestaande IoT Hub-routes uit. Er treedt een bekend probleem op wanneer u een IoT-hub gebruikt als een TSI-gebeurtenisbron met [routering](../iot-hub/iot-hub-devguide-messages-d2c.md#routing-endpoints) geconfigureerd. Schakel alle routeringseindpunten tijdelijk uit. Wanneer de IoT-hub is verbonden met de TSI, kunt u deze weer inschakelen.

Maak een unieke consumentengroep in de IoT-hub waarvan TSI gebruik kan maken. Vervang `my-pnp-hub` door de naam van de IoT-hub die u eerder hebt gebruikt:

```azurecli-interactive
az iot hub consumer-group create --hub-name my-pnp-hub --name tsi-consumer-group
```

## <a name="choose-your-time-series-id"></a>Een tijdreeks-id kiezen

Wanneer u de TSI-omgeving inricht, moet u een *tijdreeks-id* selecteren. Het is van belang om de juiste tijdreeks-id te selecteren, omdat deze eigenschap onveranderbaar is en niet kan worden gewijzigd nadat deze is ingesteld. Een tijdreeks-id lijkt op een partitiesleutel voor een database. De tijdreeks-id fungeert als de primaire sleutel voor het Time Series-model. Raadpleeg [Best practices voor het kiezen van een tijdreeks-id](../time-series-insights/how-to-select-tsid.md) voor meer informatie.

Als IoT-Plug en Play-gebruiker geeft u als de tijdreeks-id een _samengestelde sleutel_ op, die bestaat uit `iothub-connection-device-id` en `dt-subject`. IoT-hub voegt deze systeemeigenschappen toe die respectievelijk uw IoT Plug en Play-apparaat-id en de namen van de apparaatonderdelen bevatten.

Zelfs als uw IoT Plug en Play-apparaatmodellen momenteel geen onderdelen gebruiken, moet u `dt-subject` opnemen als onderdeel van een samengestelde sleutel, zodat u ze in de toekomst kunt gebruiken. Omdat de tijdreeks-id onveranderbaar is, raadt Microsoft aan om deze optie in te schakelen voor het geval u deze in de toekomst nodig hebt.

> [!NOTE]
> De onderstaande voorbeelden zijn voor het apparaat **TemperatureController** met meerdere onderdelen, maar de concepten zijn hetzelfde voor het **thermostaat** apparaat zonder onderdelen.

## <a name="provision-your-tsi-environment"></a>De TSI-omgeving inrichten

In deze sectie wordt beschreven hoe u een Azure Time Series Insights Gen2-omgeving inricht.

De volgende opdracht:

* Maakt een Azure Storage-account voor de [koude opslag](../time-series-insights/concepts-storage.md#cold-store) van uw omgeving, ontworpen voor langetermijnretentie en analyse van historische gegevens.
  * Vervang `mytsicoldstore` door een unieke naam voor het koude opslagaccount.
* Maakt een Azure Time Series Insights Gen2-omgeving, inclusief warme opslag met een bewaarperiode van zeven dagen en koude opslag met een oneindige bewaarperiode.
  * Vervang `my-tsi-env` door een unieke naam voor de TSI-omgeving.
  * Vervang `my-pnp-resourcegroup` door de naam van de resourcegroep die u tijdens het instellen hebt gebruikt.
  * `iothub-connection-device-id, dt-subject` is de eigenschap Tijdreeks-id.

```azurecli-interactive
storage=mytsicoldstore
rg=my-pnp-resourcegroup
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv)
az timeseriesinsights environment longterm create --name my-tsi-env --resource-group $rg --time-series-id-properties iothub-connection-device-id, dt-subject --sku-name L1 --sku-capacity 1 --data-retention 7 --storage-account-name $storage --storage-management-key $key --location eastus2
```

Maak verbinding met de IoT Hub-gebeurtenisbron. Vervang `my-pnp-resourcegroup`, `my-pnp-hub` en `my-tsi-env` door de waarden die u hebt gekozen. Met de volgende opdracht wordt verwezen naar de consumentengroep voor TSI die u eerder hebt gemaakt:

```azurecli-interactive
rg=my-pnp-resourcegroup
iothub=my-pnp-hub
env=my-tsi-env
es_resource_id=$(az iot hub create -g $rg -n $iothub --query id --output tsv)
shared_access_key=$(az iot hub policy list -g $rg --hub-name $iothub --query "[?keyName=='service'].primaryKey" --output tsv)
az timeseriesinsights event-source iothub create -g $rg --environment-name $env -n iot-hub-event-source --consumer-group-name tsi-consumer-group  --key-name iothubowner --shared-access-key $shared_access_key --event-source-resource-id $es_resource_id
```

Ga in [Azure Portal](https://portal.azure.com) naar uw resourcegroep en selecteer de nieuwe Time Series Insights-omgeving. Ga naar de *URL voor Time Series Insights Explorer* die wordt weergegeven in het exemplarenoverzicht:

![Overzichtspagina van portal](./media/tutorial-configure-tsi/view-environment.png)

In de Explorer ziet u drie exemplaren:

* &lt;uw pnp-apparaat-id&gt;, thermostaat1
* &lt;uw pnp-apparaat-id&gt;, thermostaat2
* &lt;uw pnp-apparaat-id&gt;, `null`

> [!NOTE]
> De derde tag vertegenwoordigt telemetrie van **TemperatureController** zelf, zoals de werkset van het apparaatgeheugen. Omdat dit een eigenschap op het hoogste niveau is, is de waarde voor de onderdeelnaam null. In een latere stap werkt u deze bij naar een meer gebruiksvriendelijke naam.

![Explorer-weergave 1](./media/tutorial-configure-tsi/tsi-env-first-view.png)

## <a name="configure-model-translation"></a>Modelvertaling configureren

Vervolgens gaat u het DTDL-apparaatmodel vertalen naar het activamodel in Azure TSI (Time Series Insights). Het Time Series-model van TSI is een hulpmiddel voor semantische modellering om gegevens te contextualiseren in TSI. Het Time Series-model heeft drie kernonderdelen:

* [Time Series-modelexemplaren](../time-series-insights/concepts-model-overview.md#time-series-model-instances). Exemplaren zijn virtuele representaties van de tijdreeksen zelf. Exemplaren worden uniek geïdentificeerd met de tijdreeks-id.
* [Time Series-modelhiërarchieën](../time-series-insights/concepts-model-overview.md#time-series-model-hierarchies). Hiërarchieën organiseren exemplaren door eigenschapsnamen en hun relaties te specificeren.
* [Time Series-modeltypen](../time-series-insights/concepts-model-overview.md#time-series-model-types). Typen helpen u [variabelen](../time-series-insights/concepts-variables.md) of formules voor berekeningen te definiëren. Typen zijn gekoppeld aan een specifiek exemplaar.

### <a name="define-your-types"></a>Uw typen definiëren

U kunt beginnen met het opnemen van gegevens in Azure Time Series Insights Gen2 zonder vooraf een model te hebben gedefinieerd. Wanneer telemetrie aankomt, probeert TSI automatisch tijdreeksexemplaren op te lossen, op basis van de waarde(n) van de eigenschap Tijdreeks-id. Aan alle exemplaren wordt het *standaardtype* toegewezen. U moet handmatig een nieuw type maken om uw exemplaren juist te categoriseren. De volgende details laten de eenvoudigste methode zien om uw DTDL-apparaatmodellen te synchroniseren met de Time Series-modeltypen:

* De Digital Twin Model Identifier wordt de type-id.
* De naam van het type kan de modelnaam of de weergavenaam zijn.
* De modelbeschrijving wordt de beschrijving van het type.
* Er wordt minstens één typevariabele voor elke telemetrie gemaakt met een numeriek schema.
  * Er kunnen alleen numerieke gegevenstypen worden gebruikt voor variabelen, maar als een waarde wordt verzonden als een ander type dat kan worden geconverteerd, bijvoorbeeld `"0"`, kunt u een [conversiefunctie](/rest/api/time-series-insights/reference-time-series-expression-syntax.md#conversion-functions) gebruiken, zoals `toDouble`.
* De naam van de variabele kan de telemetrienaam of de weergavenaam zijn.
* Wanneer u de Time Series-expressie van de variabele definieert, raadpleegt u de naam van de verbonden telemetrie en het bijbehorende gegevenstype.

| DTDL JSON | Time Series-modeltype JSON | Voorbeeldwaarde |
|-----------|------------------|-------------|
| `@id` | `id` | `dtmi:com:example:TemperatureController;1` |
| `displayName`    | `name`   |   `Temperature Controller`  |
| `description`  |  `description`  |  `Device with two thermostats and remote reboot.` |  
|`contents` (matrix)| `variables` (object)  | Bekijk het voorbeeld hieronder

![DTDL naar Time Series-modeltype](./media/tutorial-configure-tsi/DTDL-to-TSM-Type.png)

> [!NOTE]
> Dit voorbeeld laat drie variabelen zien, maar elk type kan er maximaal 100 hebben. Verschillende variabelen kunnen naar dezelfde telemetriewaarde verwijzen om verschillende berekeningen uit te voeren. Voor de volledige lijst met filters, aggregaties en scalaire functies raadpleegt u [Syntaxis van Time Series-expressies voor Time Series Insights Gen2](/rest/api/time-series-insights/reference-time-series-expression-syntax.md).

Open een teksteditor en sla de volgende JSON op uw lokale station op:

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

Ga in Time Series Insights Explorer naar het tabblad **Model** door het modelpictogram aan de linkerkant te selecteren. Selecteer **Typen** en selecteer vervolgens **JSON uploaden**:

![Uploaden](./media/tutorial-configure-tsi/upload-type.png)

Selecteer **Bestand kiezen**, selecteer de JSON die u eerder hebt opgeslagen, en selecteer **Uploaden**

U ziet het zojuist gedefinieerde type **TemperatureController**.

### <a name="create-a-hierarchy"></a>Een hiërarchie maken

Maak een hiërarchie om de tags onder de bijbehorende bovenliggende **TemperatureController** te organiseren. Het volgende eenvoudige voorbeeld heeft één niveau, maar IoT-oplossingen hebben meestal vele niveaus voor nesten, om tags te contextualiseren in de bijbehorende fysieke en semantische positie binnen een organisatie.

Selecteer **Hiërarchieën** en selecteer **Hiërarchie toevoegen**. Voer *Apparaatvloot* in als de naam, en maak één niveau met de naam *Apparaatnaam*. Selecteer vervolgens **Opslaan**.

![Een hiërarchie toevoegen](./media/tutorial-configure-tsi/add-hierarchy.png)

### <a name="assign-your-instances-to-the-correct-type"></a>Uw exemplaren aan het juiste type toewijzen

Vervolgens wijzigt u het type van uw exemplaren en plaatst u ze in de hiërarchie.

Selecteer het tabblad **Exemplaren**, zoek het exemplaar dat de werkset van het apparaat vertegenwoordigt, en selecteer het pictogram **Bewerken** helemaal rechts:

![Exemplaren bewerken](./media/tutorial-configure-tsi/edit-instance.png)

Selecteer de vervolgkeuzelijst **Type** en selecteer **TemperatureController**. Voer *defaultComponent, <your device name>* in om de naam van het exemplaar bij te werken dat alle tags op het hoogste niveau vertegenwoordigt die zijn gekoppeld aan het apparaat.

![Exemplaartype wijzigen](./media/tutorial-configure-tsi/change-type.png)

Voordat u Opslaan selecteert, selecteert u het tabblad **Exemplaarvelden** en schakelt u het vakje **Apparaatvloot** in. Voer `<your device name> - Temp Controller` in om de telemetrie te groeperen, en selecteer vervolgens **Opslaan**.

![Toewijzen aan hiërarchie](./media/tutorial-configure-tsi/assign-to-hierarchy.png)

Herhaal de vorige stappen om aan de thermostaattags het juiste type en de juiste hiërarchie toe te wijzen.

## <a name="view-your-data"></a>Uw gegevens weergeven

Ga terug naar het grafiekdeelvenster en vouw **Apparaatvloot > uw apparaat** uit. Selecteer **thermostaat1** en selecteer de variabele **Temperatuur**. Selecteer vervolgens **Toevoegen** om de waarde in de grafiek te plaatsen. Doe hetzelfde voor **thermostat2** en de waarde **defaultComponent**-**werkset**.

![Exemplaartype voor thermostaat2 wijzigen](./media/tutorial-configure-tsi/charting-values.png)

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg [Azure Time Series Insights Explorer](../time-series-insights/concepts-ux-panels.md) voor meer informatie over de verschillende grafiekopties, waaronder intervalgroottes en y-asbesturingselementen.

* Raadpleeg het artikel [Time Series-model in Azure Time Series Insights Gen2](../time-series-insights/concepts-model-overview.md) voor een gedetailleerd overzicht van het Time Series-model van uw omgeving.

* Raadpleeg [Query-API's voor Azure Time Series Insights Gen2](/rest/api/time-series-insights/reference-query-apis.md) voor meer informatie over de query-API's en de syntaxis van Time Series-expressies.
