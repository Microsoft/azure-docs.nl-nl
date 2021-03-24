---
title: Telemetrie opnemen vanuit IoT Hub
titleSuffix: Azure Digital Twins
description: Zie telemetrie-berichten van apparaten inslikken van IoT Hub.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 9/15/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 2fd0d9d2b6e80d54bdd45b7a13fab7bfa33841c9
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889464"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>IoT Hub telemetrie opnemen in azure Digital Apparaatdubbels

Azure Digital Apparaatdubbels wordt gestuurd met gegevens van IoT-apparaten en andere bronnen. Een algemene bron voor de apparaatgegevens die in azure Digital Apparaatdubbels moet worden gebruikt, is [IOT hub](../iot-hub/about-iot-hub.md).

Het proces voor het opnemen van gegevens in azure Digital Apparaatdubbels is het instellen van een externe reken resource, zoals een functie die wordt gemaakt met behulp van [Azure functions](../azure-functions/functions-overview.md). De functie ontvangt de gegevens en maakt gebruik van de [DigitalTwins-api's](/rest/api/digital-twins/dataplane/twins) om de eigenschappen of telemetrie-gebeurtenissen op [digitale apparaatdubbels](concepts-twins-graph.md) dienovereenkomstig in te stellen. 

In dit document vindt u instructies voor het schrijven van een functie waarmee telemetrie kan worden opgenomen in IoT Hub.

## <a name="prerequisites"></a>Vereisten

Voordat u verder gaat met dit voor beeld, moet u de volgende resources instellen als vereisten:
* **Een IOT-hub**. Zie de sectie *een IOT hub maken* van [deze IOT hub Snelstartgids](../iot-hub/quickstart-send-telemetry-cli.md)voor instructies.
* **Een Azure Digital apparaatdubbels-exemplaar** dat telemetrie van uw apparaat ontvangt. Zie [*How to: een Azure Digital apparaatdubbels-exemplaar en-verificatie instellen*](./how-to-set-up-instance-portal.md)voor instructies.

### <a name="example-telemetry-scenario"></a>Voor beeld van telemetrie-scenario

In deze procedure wordt beschreven hoe u berichten van IoT Hub naar Azure Digital Apparaatdubbels verzendt met behulp van een functie in Azure. Er zijn veel mogelijke configuraties en overeenkomende strategieën die u kunt gebruiken voor het verzenden van berichten, maar het voor beeld voor dit artikel bevat de volgende onderdelen:
* Een thermo staat-apparaat in IoT Hub met een bekende apparaat-ID
* Een digitale dubbele om het apparaat te vertegenwoordigen met een overeenkomende ID

> [!NOTE]
> In dit voor beeld wordt een eenvoudige ID-overeenkomst gebruikt tussen de apparaat-ID en de bijbehorende digitale dubbele ID, maar het is mogelijk om meer geavanceerde toewijzingen van het apparaat aan de dubbele waarde toe te voegen (zoals bij een toewijzings tabel).

Wanneer een Tempe ratuur-telemetrie-gebeurtenis wordt verzonden door het Thermo staat-apparaat, verwerkt een functie de telemetrie en de eigenschap *Tempe ratuur* van het digitale dubbele moet worden bijgewerkt. Dit scenario wordt beschreven in een diagram hieronder:

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="Een diagram waarin een stroom diagram wordt weer gegeven. In de grafiek verzendt een IoT Hub apparaat een temperatuur telemetrie via IoT Hub naar een functie in azure, waarmee een temperatuur eigenschap wordt bijgewerkt op een dubbele in azure Digital Apparaatdubbels." border="false":::

## <a name="add-a-model-and-twin"></a>Een model en een dubbel toevoegen

In deze sectie gaat u een [digitale](concepts-twins-graph.md) Apparaatdubbels in azure configureren die het Thermo staats apparaat vertegenwoordigt. deze gegevens worden bijgewerkt met informatie uit IOT hub.

Als u een thermo staat wilt maken, moet u eerst het Thermo staats [model](concepts-models.md) uploaden naar uw exemplaar, waarin de eigenschappen van een thermo staat worden beschreven en later worden gebruikt voor het maken van de dubbele. 

Het model ziet er als volgt uit:
:::code language="json" source="~/digital-twins-docs-samples/models/Thermostat.json":::

Als u **dit model wilt uploaden naar uw apparaatdubbels-exemplaar**, voert u de volgende Azure cli-opdracht uit, die het bovenstaande model uploadt als inline JSON. U kunt de opdracht uitvoeren in [Azure Cloud shell](/cloud-shell/overview.md) in uw browser of op uw computer als u de CLI lokaal hebt [geïnstalleerd](/cli/azure/install-azure-cli).

```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```

Vervolgens moet u **een dubbele maken met behulp van dit model**. Gebruik de volgende opdracht om een thermo staat te maken met de naam **thermostat67** en stel 0,0 in als aanvankelijke temperatuur waarde.

```azurecli-interactive
az dt twin create --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{"Temperature": 0.0,}' --dt-name {digital_twins_instance_name}
```

>[!NOTE]
> Als u Cloud Shell in de Power shell-omgeving gebruikt, moet u mogelijk de aanhalings tekens in de inline JSON-velden voor hun waarden weglaten worden geparseerd. Dit zijn de opdrachten voor het uploaden van het model en het maken van de dubbele met deze wijziging:
>
> Model uploaden:
> ```azurecli-interactive
> az dt model create --models '{  \"@id\": \"dtmi:contosocom:DigitalTwins:Thermostat;1\",  \"@type\": \"Interface\",  \"@context\": \"dtmi:dtdl:context;2\",  \"contents\": [    {      \"@type\": \"Property\",      \"name\": \"Temperature\",      \"schema\": \"double\"    }  ]}' -n {digital_twins_instance_name}
> ```
>
> Maken van twee:
> ```azurecli-interactive
> az dt twin create --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{\"Temperature\": 0.0,}' --dt-name {digital_twins_instance_name}
> ```

Wanneer het dubbele is gemaakt, moet de CLI-uitvoer van de opdracht er ongeveer als volgt uitzien:
```json
{
  "$dtId": "thermostat67",
  "$etag": "W/\"0000000-9735-4f41-98d5-90d68e673e15\"",
  "$metadata": {
    "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
    "Temperature": {
      "ackCode": 200,
      "ackDescription": "Auto-Sync",
      "ackVersion": 1,
      "desiredValue": 0.0,
      "desiredVersion": 1
    }
  },
  "Temperature": 0.0
}
```

## <a name="create-a-function"></a>Een functie maken

In deze sectie maakt u een Azure-functie voor toegang tot Azure Digital Apparaatdubbels en het bijwerken van apparaatdubbels op basis van IoT-telemetriegegevens die worden ontvangen. Volg de onderstaande stappen om de functie te maken en te publiceren.

#### <a name="step-1-create-a-function-app-project"></a>Stap 1: een functie-app-project maken

Maak eerst een nieuw functie-app-project in Visual Studio. Zie de sectie [**een functie-app maken in Visual Studio**](how-to-create-azure-function.md#create-a-function-app-in-visual-studio) van het artikel *How to: een functie instellen voor het verwerken van gegevens* voor instructies over hoe u dit doet.

#### <a name="step-2-fill-in-function-code"></a>Stap 2: functie code invullen

Voeg de volgende pakketten toe aan uw project:
* [Azure. DigitalTwins. core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
* [Azure. Identity](https://www.nuget.org/packages/Azure.Identity/)
* [Micro soft. Azure. webjobs. Extensions. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid/)

Wijzig de naam van de voorbeeld functie *Function1. cs* die Visual Studio met het nieuwe project heeft gegenereerd in *IoTHubtoTwins. cs*. Vervang de code in het bestand door de volgende code:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/IoTHubToTwins.cs":::

Sla uw functie code op.

#### <a name="step-3-publish-the-function-app-to-azure"></a>Stap 3: de functie-app publiceren in azure

Publiceer het project naar een functie-app in Azure.

Zie de sectie [**de functie-app publiceren in azure**](how-to-create-azure-function.md#publish-the-function-app-to-azure) van het artikel *How to: een functie voor het verwerken van gegevens* voor meer informatie over hoe u dit doet.

#### <a name="step-4-configure-the-function-app"></a>Stap 4: de functie-app configureren

Wijs vervolgens **een Access-rol toe** voor de functie en **Configureer de toepassings instellingen** zodat deze toegang krijgen tot uw Azure Digital apparaatdubbels-exemplaar. Voor instructies over hoe u dit doet, zie de sectie [**beveiligings toegang instellen voor de functie-app**](how-to-create-azure-function.md#set-up-security-access-for-the-function-app) van het artikel *een functie voor het verwerken van gegevens* .

## <a name="connect-your-function-to-iot-hub"></a>Verbind uw functie met IoT Hub

In deze sectie stelt u uw functie in als een gebeurtenis bestemming voor de gegevens van de IoT hub-apparaat. Dit zorgt ervoor dat de gegevens van het Thermo staat-apparaat in IoT Hub worden verzonden naar de Azure-functie voor verwerking.

Navigeer in het [Azure Portal](https://portal.azure.com/)naar uw IOT hub-exemplaar dat u hebt gemaakt in de sectie [*vereisten*](#prerequisites) . Maak onder **gebeurtenissen** een abonnement voor uw functie.

:::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="Scherm afbeelding van de Azure Portal die het toevoegen van een gebeurtenis abonnement weergeeft.":::

Vul op de pagina **gebeurtenis abonnement maken** de velden als volgt in:
  1. Kies bij **naam** de gewenste naam voor het gebeurtenis abonnement.
  2. Kies _Event grid schema_ voor het **gebeurtenis schema**.
  3. Kies de gewenste naam voor de naam van het **systeem onderwerp**.
  1. **Als u wilt filteren op gebeurtenis typen**, kiest u het selectie vakje _telemetrie van apparaat_ en schakelt u andere gebeurtenis typen uit.
  1. Selecteer _Azure function_ voor het **type eind punt**.
  1. Gebruik voor **eind punt** de koppeling _Selecteer een eind punt_ om te kiezen welke Azure-functie voor het eind punt moet worden gebruikt.
    
:::image type="content" source="media/how-to-ingest-iot-hub-data/create-event-subscription.png" alt-text="Scherm afbeelding van de Azure Portal voor het maken van de details van het gebeurtenis abonnement":::

Op de pagina _Azure-functie selecteren_ die wordt geopend, controleert of vult u de onderstaande gegevens in.
 1. **Abonnement**: uw Azure-abonnement.
 2. **Resource groep**: de resource groep.
 3. **Functie-app**: de naam van uw functie-app.
 4. **Sleuf**: _productie_.
 5. **Functie**: Selecteer de functie van eerdere, *IoTHubtoTwins*, in de vervolg keuzelijst.

Sla uw gegevens op met de knop _selectie bevestigen_ .            
      
:::image type="content" source="media/how-to-ingest-iot-hub-data/select-azure-function.png" alt-text="Scherm opname van de Azure Portal om de functie te selecteren.":::

Selecteer de knop _maken_ om het gebeurtenis abonnement te maken.

## <a name="send-simulated-iot-data"></a>Gesimuleerde IoT-gegevens verzenden

Als u de nieuwe functie insluitingen wilt testen, gebruikt u de hand leiding van Device Simulator [*: verbinding maken met een end-to-end-oplossing*](./tutorial-end-to-end.md). Deze zelf studie wordt aangedreven door een voorbeeld project dat is geschreven in C#. De voorbeeld code bevindt zich hier: [Azure Digital apparaatdubbels end-to-end-voor beelden](/samples/azure-samples/digital-twins-samples/digital-twins-samples). U gebruikt het **DeviceSimulator** -project in die opslag plaats.

In de end-to-end zelf studie voert u de volgende stappen uit:
1. [*Het gesimuleerde apparaat bij IoT Hub registreren*](./tutorial-end-to-end.md#register-the-simulated-device-with-iot-hub)
2. [*De simulatie configureren en uitvoeren*](./tutorial-end-to-end.md#configure-and-run-the-simulation)

## <a name="validate-your-results"></a>Uw resultaten valideren

Bij het uitvoeren van de bovenstaande Device Simulator wordt de waarde voor de Tempe ratuur van uw digitale-dubbele waarden gewijzigd. Voer in de Azure CLI de volgende opdracht uit om de waarde voor de Tempe ratuur te bekijken.

```azurecli-interactive
az dt twin query -q "select * from digitaltwins" -n {digital_twins_instance_name}
```

De uitvoer moet een temperatuur waarde als volgt bevatten:

```json
{
  "result": [
    {
      "$dtId": "thermostat67",
      "$etag": "W/\"0000000-1e83-4f7f-b448-524371f64691\"",
      "$metadata": {
        "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
        "Temperature": {
          "ackCode": 200,
          "ackDescription": "Auto-Sync",
          "ackVersion": 1,
          "desiredValue": 69.75806974934324,
          "desiredVersion": 1
        }
      },
      "Temperature": 69.75806974934324
    }
  ]
}
```

Voer herhaaldelijk de query-opdracht uit om de gewijzigde waarde weer te geven.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het inkomen en uitkomen van gegevens met Azure Digital Apparaatdubbels:
* [*Concepten: integratie met andere services*](concepts-integration.md)
