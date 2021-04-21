---
title: Integreren met Azure Time Series Insights
titleSuffix: Azure Digital Twins
description: Bekijk hoe u gebeurtenisroutes in kunt stellen van Azure Digital Twins naar Azure Time Series Insights.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 4/7/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: d89ee4c8e66ba4dda004fbd27e15b96ab13c642b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783769"
---
# <a name="integrate-azure-digital-twins-with-azure-time-series-insights"></a>Integratie Azure Digital Twins met Azure Time Series Insights

In dit artikel leert u hoe u uw Azure Digital Twins integreert [met Azure Time Series Insights (TSI).](../time-series-insights/overview-what-is-tsi.md)

Met de oplossing die in dit artikel wordt beschreven, kunt u historische gegevens over uw IoT-oplossing verzamelen en analyseren. Azure Digital Twins is zeer geschikt voor het verzenden van gegevens naar Time Series Insights, omdat u hiermee meerdere gegevensstreams kunt samenbrengen en uw gegevens uniform kunt maken voordat u deze naar Time Series Insights verzendt.

## <a name="prerequisites"></a>Vereisten

Voordat u een relatie met Time Series Insights kunt instellen, moet u de volgende resources instellen:
* Een **IoT-hub.** Zie de sectie Een IoT Hub [*maken*](../iot-hub/quickstart-send-telemetry-cli.md#create-an-iot-hub) van IoT Hub quickstart over *telemetrie* verzenden van de IoT Hub voor instructies.
* Een **Azure Digital Twins-exemplaar**.
Zie Instructies: [*Een Azure Digital Twins en verificatie voor instructies.*](./how-to-set-up-instance-portal.md)
* Een **model en een tweeling in het Azure Digital Twins-exemplaar**.
U moet de gegevens van de tweeling een paar keer bijwerken om te zien of de gegevens in de Time Series Insights. Zie de sectie [*Een model en*](how-to-ingest-iot-hub-data.md#add-a-model-and-twin) tweeling toevoegen in het artikel *Instructies: IoT-hub* opnemen voor instructies.

> [!TIP]
> In dit artikel worden de veranderende digital twin-waarden die worden bekeken in Time Series Insights voor het gemak handmatig bijgewerkt. Als u dit artikel echter wilt voltooien met live gesimuleerde gegevens, kunt u een Azure-functie instellen die digitale tweelingen bijwerkt op basis van IoT-telemetriegebeurtenissen van een gesimuleerd apparaat. Volg voor instructies Instructies: Gegevens opnemen [*IoT Hub,*](how-to-ingest-iot-hub-data.md)inclusief de laatste stappen voor het uitvoeren van de apparaatsimulator en valideren dat de gegevensstroom werkt.
>
> Zoek later nog een TIP om te laten zien waar u de apparaatsimulator kunt uitvoeren en laat uw Azure-functies de tweelingen automatisch bijwerken in plaats van handmatige opdrachten voor digitale tweelingupdates te verzenden.


## <a name="solution-architecture"></a>Architectuur voor de oplossing

U gaat een Time Series Insights aan Azure Digital Twins via het onderstaande pad.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="Diagram van Azure-services in een end-to-end-scenario, waarin de Time Series Insights." lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="create-event-hub-namespace"></a>Event hub-naamruimte maken

Voordat u de Event Hubs maakt, maakt u eerst een Event Hub-naamruimte die gebeurtenissen ontvangt van uw Azure Digital Twins exemplaar. U kunt de onderstaande Azure CLI-instructies gebruiken of de volgende [*Azure Portal: Quickstart: Een Event Hub*](../event-hubs/event-hubs-create.md)maken met behulp van Azure Portal . Ga naar Azure-producten beschikbaar per regio om te zien welke regio's ondersteuning bieden [*voor Event Hubs.*](https://azure.microsoft.com/global-infrastructure/services/?products=event-hubs)

```azurecli-interactive
az eventhubs namespace create --name <name-for-your-event-hubs-namespace> --resource-group <your-resource-group> -l <region>
```

> [!TIP]
> Als er een foutmelding wordt weergegeven, moet u ervoor zorgen dat de naam die u voor uw naamruimte hebt gekozen, voldoet aan de naamgevingsvereisten die worden beschreven in dit `BadRequest: The specified service namespace is invalid.` referentiedocument: [Naamruimte maken.](/rest/api/servicebus/create-namespace)

U gebruikt deze Event Hubs-naamruimte voor de twee Event Hubs die nodig zijn voor dit artikel:

  1. **Twins-hub** - Event Hub voor het ontvangen van wijzigingsgebeurtenissen van tweelingen
  2. **Time Series Hub:** Event Hub voor het streamen van gebeurtenissen naar Time Series Insights

De volgende secties helpen u bij het maken en configureren van deze hubs binnen uw Event Hub-naamruimte.

## <a name="create-twins-hub"></a>Tweelingenhub maken

De eerste Event Hub die u in dit artikel maakt, is **de twins-hub**. Deze Event Hub ontvangt dubbele wijzigingsgebeurtenissen van Azure Digital Twins.
Als u de twins-hub wilt instellen, moet u de volgende stappen in deze sectie voltooien:

1. De twins-hub maken
2. Een autorisatieregel maken om machtigingen voor de hub te bepalen
3. Maak een eindpunt in Azure Digital Twins die de autorisatieregel gebruikt voor toegang tot de hub
4. Maak een route in Azure Digital Twins die de gebeurtenis voor tweelingupdates naar het eindpunt en de verbonden twins-hub verzendt
5. De hub-connection string

Maak de **twins hub met** de volgende CLI-opdracht. Geef een naam op voor de tweelinghub.

```azurecli-interactive
az eventhubs eventhub create --name <name-for-your-twins-hub> --resource-group <your-resource-group> --namespace-name <your-event-hubs-namespace-from-above>
```

### <a name="create-twins-hub-authorization-rule"></a>Een hubautorisatieregel voor twins maken

Maak een [autorisatieregel](/cli/azure/eventhubs/eventhub/authorization-rule#az_eventhubs_eventhub_authorization_rule_create) met machtigingen voor verzenden en ontvangen. Geef een naam op voor de regel.

```azurecli-interactive
az eventhubs eventhub authorization-rule create --rights Listen Send --name <name-for-your-twins-hub-auth-rule> --resource-group <your-resource-group> --namespace-name <your-event-hubs-namespace-from-earlier> --eventhub-name <your-twins-hub-from-above>
```

### <a name="create-twins-hub-endpoint"></a>Tweelingen hub-eindpunt maken

Maak een [Azure Digital Twins-eindpunt dat](concepts-route-events.md#create-an-endpoint) uw Event Hub aan uw Azure Digital Twins koppelt. Geef een naam op voor het hub-eindpunt van uw tweeling.

```azurecli-interactive
az dt endpoint create eventhub -n <your-Azure-Digital-Twins-instance-name> --eventhub-resource-group <your-resource-group> --eventhub-namespace <your-event-hubs-namespace-from-earlier> --eventhub <your-twins-hub-name-from-above> --eventhub-policy <your-twins-hub-auth-rule-from-earlier> --endpoint-name <name-for-your-twins-hub-endpoint>
```

### <a name="create-twins-hub-event-route"></a>Twins Hub-gebeurtenisroute maken

Azure Digital Twins kunnen dubbele [updategebeurtenissen wanneer](how-to-interpret-event-data.md) de status van een tweeling wordt bijgewerkt. In deze sectie maakt u een Azure Digital Twins  gebeurtenisroute die deze updategebeurtenissen door stuurt naar de twins-hub voor verdere verwerking.

Maak een [route](concepts-route-events.md#create-an-event-route) in Azure Digital Twins van bovenstaande gegevens om dubbele updategebeurtenissen naar uw eindpunt te verzenden. Het filter in deze route staat alleen toe dat berichten van dubbele updates worden doorgegeven aan uw eindpunt. Geef een naam op voor de gebeurtenisroute van de tweelinghub.

```azurecli-interactive
az dt route create -n <your-Azure-Digital-Twins-instance-name> --endpoint-name <your-twins-hub-endpoint-from-above> --route-name <name-for-your-twins-hub-event-route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
```

### <a name="get-twins-hub-connection-string"></a>Twins Hub-connection string

Haal de [event hub-connection string van de](../event-hubs/event-hubs-get-connection-string.md)tweelingen op met behulp van de autorisatieregels die u hierboven hebt gemaakt voor de twins-hub.

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group <your-resource-group> --namespace-name <your-event-hubs-namespace-from-earlier> --eventhub-name <your-twins-hub-from-above> --name <your-twins-hub-auth-rule-from-earlier>
```
Noteer de waarde **primaryConnectionString uit het** resultaat om de instelling van de hub-app voor twins verderop in dit artikel te configureren.

## <a name="create-time-series-hub"></a>Time Series Hub maken

De tweede Event Hub die u in dit artikel maakt, is **de time series hub**. Dit is een Event Hub die de Azure Digital Twins naar Time Series Insights.
Als u de time series-hub wilt instellen, moet u deze stappen voltooien:

1. De Time Series-hub maken
2. Een autorisatieregel maken om machtigingen voor de hub te bepalen
3. De time series hub-connection string

Later, wanneer u het Time Series Insights maakt, verbindt u deze tijdreekshub als de gebeurtenisbron voor het Time Series Insights exemplaar.

Maak de **Time Series Hub met** behulp van de volgende opdracht. Geef een naam op voor de tijdreekshub.

```azurecli-interactive
 az eventhubs eventhub create --name <name-for-your-time-series-hub> --resource-group <your-resource-group> --namespace-name <your-event-hub-namespace-from-earlier>
```

### <a name="create-time-series-hub-authorization-rule"></a>Autorisatieregel voor time series hub maken

Maak een [autorisatieregel](/cli/azure/eventhubs/eventhub/authorization-rule#az_eventhubs_eventhub_authorization_rule_create) met machtigingen voor verzenden en ontvangen. Geef een naam op voor de tijdreekshub-auth-regel.

```azurecli-interactive
az eventhubs eventhub authorization-rule create --rights Listen Send --name <name-for-your-time-series-hub-auth-rule> --resource-group <your-resource-group> --namespace-name <your-event-hub-namespace-from-earlier> --eventhub-name <your-time-series-hub-name-from-above>
```

### <a name="get-time-series-hub-connection-string"></a>Time Series Hub-connection string

Haal de [Time Series Hub op connection string,](../event-hubs/event-hubs-get-connection-string.md)met behulp van de autorisatieregels die u hierboven hebt gemaakt voor de Time Series Hub:

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group <your-resource-group> --namespace-name <your-event-hub-namespace-from-earlier> --eventhub-name <your-time-series-hub-name-from-earlier> --name <your-time-series-hub-auth-rule-from-earlier>
```
Noteer de waarde **primaryConnectionString** uit het resultaat om de instelling voor de Time Series Hub-app verderop in dit artikel te configureren.

Noteer ook de volgende waarden om deze later te gebruiken om een Time Series Insights maken.
* Event hub-naamruimte
* Naam van time series-hub
* Tijdreekshub-auth-regel

## <a name="create-a-function"></a>Een functie maken

In deze sectie maakt u een Azure-functie die dubbele updategebeurtenissen van hun oorspronkelijke formulier converteert als JSON Patch-documenten naar JSON-objecten, die alleen bijgewerkte en toegevoegde waarden van uw tweelingen bevatten.

### <a name="step-1-create-function-app"></a>Stap 1: Functie-app maken

Maak eerst een nieuw functie-app-project in Visual Studio. Zie de sectie Een *functie-app* maken [**in Visual Studio**](how-to-create-azure-function.md#create-a-function-app-in-visual-studio) van het artikel Instructies: Een functie instellen voor het verwerken van gegevens voor instructies.

### <a name="step-2-add-a-new-function"></a>Stap 2: een nieuwe functie toevoegen

Maak een nieuwe Azure-functie met *de naam ProcessDTUpdatetoTSI.cs om* telemetriegebeurtenissen van apparaten bij te werken naar de Time Series Insights. Het functietype is **Event Hub-trigger.**

:::image type="content" source="media/how-to-integrate-time-series-insights/create-event-hub-trigger-function.png" alt-text="Schermopname van Visual Studio voor het maken van een nieuwe Azure-functie van het type Event Hub-trigger.":::

### <a name="step-3-fill-in-function-code"></a>Stap 3: functiecode invullen

Voeg de volgende pakketten toe aan uw project:
* [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/)
* [Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/)
* [Microsoft .NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/)

Vervang de code in *het bestand ProcessDTUpdatetoTSI.cs* door de volgende code:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/updateTSI.cs":::

Sla de functiecode op.

### <a name="step-4-publish-the-function-app-to-azure"></a>Stap 4: de functie-app publiceren naar Azure

Publiceer het project met *de functie ProcessDTUpdatetoTSI.cs* naar een functie-app in Azure.

Zie de sectie De *functie-app* publiceren naar [**Azure**](how-to-create-azure-function.md#publish-the-function-app-to-azure) in het artikel Instructies: Een functie instellen voor het verwerken van gegevens voor instructies.

Sla de naam van de functie-app op voor later gebruik om app-instellingen voor de twee Event Hubs te configureren.

### <a name="step-5-security-access-for-the-function-app"></a>Stap 5: Beveiligingstoegang voor de functie-app

Wijs **vervolgens een toegangsrol toe** voor de functie en **configureer** de toepassingsinstellingen zodat deze toegang heeft tot Azure Digital Twins-exemplaar. Zie de sectie Beveiligingstoegang instellen [](how-to-create-azure-function.md#set-up-security-access-for-the-function-app) voor de *functie-app* van het artikel Instructies: Een functie instellen voor het verwerken van gegevens voor instructies.

### <a name="step-6-configure-app-settings-for-the-two-event-hubs"></a>Stap 6: app-instellingen configureren voor de twee Event Hubs

Vervolgens voegt u omgevingsvariabelen toe aan de instellingen van de functie-app waarmee deze toegang kan krijgen tot de hub twins en de Time Series Hub.

Gebruik de waarde van twins hub **primaryConnectionString** die u eerder hebt opgeslagen om een app-instelling te maken in uw functie-app die de twins hub-connection string:

```azurecli-interactive
az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<your-twins-hub-primaryConnectionString>" -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

Gebruik de time series hub **primaryConnectionString-waarde** die u eerder hebt opgeslagen om een app-instelling te maken in uw functie-app die de time series-hub bevat connection string:

```azurecli-interactive
az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<your-time-series-hub-primaryConnectionString>" -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

## <a name="create-and-connect-a-time-series-insights-instance"></a>Een Time Series Insights-instantie maken en verbinden

In deze sectie stelt u een exemplaar van Time Series Insights om gegevens te ontvangen van uw time series-hub. Zie Zelfstudie: Een Azure Time Series Insights [*Gen2 PAYG-omgeving*](../time-series-insights/tutorial-set-up-environment.md)instellen voor meer informatie over dit proces. Volg de onderstaande stappen om een Time Series Insights-omgeving te maken.

1. Zoek in [Azure Portal](https://portal.azure.com)naar Time Series Insights *omgevingen* en selecteer de **knop** Toevoegen. Kies de volgende opties om de tijdreeksomgeving te maken.

    * **Abonnement:** kies uw abonnement.
        - **Resourcegroep:** kies uw resourcegroep.
    * **Omgevingsnaam:** geef een naam op voor uw tijdreeksomgeving.
    * **Locatie:** kies een locatie.
    * **Laag:** kies de **prijscategorie Gen2(L1).**
    * **Eigenschapsnaam:** voer **$dtId** in (meer informatie over het selecteren van een id-waarde in Best practices voor [*het kiezen van een tijdreeks-id).*](../time-series-insights/how-to-select-tsid.md)
    * **Naam van opslagaccount:** geef een naam op voor het opslagaccount.
    * **Warme opslag inschakelen:** laat dit veld ingesteld op *Ja.*

    U kunt de standaardwaarden voor andere eigenschappen op deze pagina behouden. Selecteer de **knop Volgende : Gebeurtenisbron >** selecteren.

    :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png" alt-text="Schermopname van de Azure Portal om een Time Series Insights maken. Selecteer uw abonnement, resourcegroep en locatie in de desbetreffende vervolgkeuzen en kies een naam voor uw omgeving." lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png":::
        
    :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png" alt-text="Schermopname van de Azure Portal om een Time Series Insights maken. De prijscategorie Gen2(L1) is geselecteerd en de eigenschapsnaam van de tijdreeks-id wordt $dtId." lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png":::

2. Kies op *het tabblad Gebeurtenisbron* de volgende velden:

   * **Een gebeurtenisbron maken** - Kies *Ja.*
   * **Brontype:** kies *Event Hub.*
   * **Naam:** geef een naam op voor uw gebeurtenisbron.
   * **Abonnement:** kies uw Azure-abonnement.
   * **Event Hub-naamruimte:** kies de naamruimte die u eerder in dit artikel hebt gemaakt.
   * **Event Hub-naam:** kies de **naam van de tijdreekshub** die u eerder in dit artikel hebt gemaakt.
   * **Naam van toegangsbeleid voor Event Hub:** kies de *tijdreekshub-auth-regel* die u eerder in dit artikel hebt gemaakt.
   * **Event Hub-consumentengroep:** selecteer *Nieuw* en geef een naam op voor uw Event Hub-consumentengroep. Selecteer vervolgens *Toevoegen.*
   * **Eigenschapsnaam:** laat dit veld leeg.
    
    Kies de **knop Beoordelen en maken** om alle details te bekijken. Selecteer vervolgens opnieuw de **knop Beoordelen en** maken om de tijdreeksomgeving te maken.

    :::image type="content" source="media/how-to-integrate-time-series-insights/create-tsi-environment-event-source.png" alt-text="Schermopname van de Azure Portal om een Time Series Insights maken. U maakt een gebeurtenisbron met de bovenstaande event hub-informatie. U maakt ook een nieuwe consumentengroep." lightbox="media/how-to-integrate-time-series-insights/create-tsi-environment-event-source.png":::

## <a name="send-iot-data-to-azure-digital-twins"></a>IoT-gegevens verzenden naar Azure Digital Twins

Als u gegevens naar Time Series Insights wilt verzenden, moet u beginnen met het bijwerken van de eigenschappen van de digitale tweeling in Azure Digital Twins gegevenswaarden te wijzigen.

Gebruik de volgende CLI-opdracht om de eigenschap *Temperatuur* bij te werken op de *tweeling thermostat67* die u hebt toegevoegd aan uw exemplaar in [de sectie Vereisten.](#prerequisites)

```azurecli-interactive
az dt twin update -n <your-azure-digital-twins-instance-name> --twin-id thermostat67 --json-patch '{"op":"replace", "path":"/Temperature", "value": 20.5}'
```

**Herhaal de opdracht nog minstens vier** keer met verschillende temperatuurwaarden om verschillende gegevenspunten te maken die later in de Time Series Insights waargenomen.

> [!TIP]
> Als u dit artikel wilt voltooien met live gesimuleerde gegevens in plaats van de digital twin-waarden [](#prerequisites) handmatig bij te werken, moet u eerst de TIP uit de sectie Vereisten voltooien om een Azure-functie in te stellen die tweelingen bijwerkt vanaf een gesimuleerd apparaat.
Daarna kunt u het apparaat nu uitvoeren om gesimuleerde gegevens te verzenden en uw digitale tweeling bij te werken via die gegevensstroom.

## <a name="visualize-your-data-in-time-series-insights"></a>Uw gegevens in Time Series Insights weergeven

Nu moeten de gegevens naar uw Time Series Insights worden gestroomd, klaar om te worden geanalyseerd. Volg de onderstaande stappen om de binnenkomende gegevens te verkennen.

1. Zoek in [Azure Portal](https://portal.azure.com)naar de naam van uw tijdreeksomgeving die u eerder hebt gemaakt. Selecteer overzicht in de menuopties aan de *linkerkant* om de URL Time Series Insights Explorer *bekijken.* Selecteer de URL om de temperatuurwijzigingen weer te geven die worden weergegeven in Time Series Insights omgeving.

    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="Schermopname van de Azure Portal de URL van Time Series Insights Explorer te selecteren op het tabblad Overzicht van Time Series Insights omgeving." lightbox="media/how-to-integrate-time-series-insights/view-environment.png":::

2. In de verkenner ziet u de tweelingen in het Azure Digital Twins-exemplaar aan de linkerkant. Selecteer de *tweeling thermostat67,* kies de eigenschap *Temperatuur* en klik op **Toevoegen.**

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="Schermopname van de Time Series Insights om thermostat67 te selecteren, selecteer de eigenschapstemperatuur en klik op Toevoegen." lightbox="media/how-to-integrate-time-series-insights/add-data.png":::

3. U ziet nu de initiële temperatuurmetingen van uw thermostaat, zoals hieronder wordt weergegeven. 

    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="Schermopname van de TSI-verkenner om de initiële temperatuurgegevens weer te geven. Het is een lijn met willekeurige waarden tussen 68 en 85" lightbox="media/how-to-integrate-time-series-insights/initial-data.png":::

Als u toestaat dat een simulatie veel langer wordt uitgevoerd, ziet uw visualisatie er als volgende uit:

:::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="Schermopname van de TSI-verkenner waarbij temperatuurgegevens voor elke tweeling worden weergegeven in drie parallelle lijnen met verschillende kleuren." lightbox="media/how-to-integrate-time-series-insights/day-data.png":::

## <a name="next-steps"></a>Volgende stappen

De digitale tweelingen worden standaard opgeslagen als een platte hiërarchie in Time Series Insights, maar ze kunnen worden verrijkt met modelinformatie en een hiërarchie met meerdere niveau's voor de organisatie. Lees het volgende voor meer informatie over dit proces: 

* [*Zelfstudie: Een model definiëren en toepassen*](../time-series-insights/tutorial-set-up-environment.md#define-and-apply-a-model) 

U kunt aangepaste logica schrijven om deze informatie automatisch op te geven met behulp van het model en de grafiekgegevens die al zijn opgeslagen in Azure Digital Twins. Zie de volgende verwijzingen voor meer informatie over het beheren, upgraden en ophalen van gegevens uit de tweelinggrafiek:

* [*How-to: Manage a digital twin (Een digitale tweeling beheren)*](./how-to-manage-twin.md)
* [*How-to: Query's uitvoeren op de tweelinggrafiek*](./how-to-query-graph.md)