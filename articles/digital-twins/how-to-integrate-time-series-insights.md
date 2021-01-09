---
title: Integreren met Azure Time Series Insights
titleSuffix: Azure Digital Twins
description: Zie gebeurtenis routes van Azure Digital Apparaatdubbels instellen op Azure Time Series Insights.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 7/14/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 22ee57592af838a236d75fa7f56a0c8e1ed89403
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98046521"
---
# <a name="integrate-azure-digital-twins-with-azure-time-series-insights"></a>Azure Digital Apparaatdubbels integreren met Azure Time Series Insights

In dit artikel leert u hoe u Azure Digital Apparaatdubbels integreert met behulp van [Azure time series Insights (TSI)](../time-series-insights/overview-what-is-tsi.md).

Met de oplossing die in dit artikel wordt beschreven, kunt u historische gegevens over uw IoT-oplossing verzamelen en analyseren. Azure Digital Apparaatdubbels is een uitstekende oplossing voor het inbrengen van gegevens in Time Series Insights, omdat u hiermee meerdere gegevensstreams kunt correleren en uw gegevens gestandaardiseerd kunt maken voordat u deze naar Time Series Insights verzendt. 

## <a name="prerequisites"></a>Vereisten

Voordat u een relatie met Time Series Insights kunt instellen, moet u een **Azure Digital apparaatdubbels-exemplaar** hebben. Dit exemplaar moet worden ingesteld met de mogelijkheid om digitale en dubbele informatie bij te werken op basis van gegevens, omdat u dubbele informatie een paar keer moet bijwerken om te zien dat de gegevens die in Time Series Insights worden bijgehouden. 

Als u deze instelling nog niet hebt ingesteld, kunt u deze maken met behulp van de Azure Digital Apparaatdubbels- [*zelf studie: verbinding maken met een end-to-end oplossing*](./tutorial-end-to-end.md). In deze zelf studie wordt u begeleid bij het instellen van een Azure Digital Apparaatdubbels-exemplaar dat werkt met een virtueel IoT-apparaat om digitale dubbele updates te activeren.

## <a name="solution-architecture"></a>Architectuur voor de oplossing

U gaat Time Series Insights toevoegen aan Azure Digital Apparaatdubbels via het onderstaande pad.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="Een weer gave van Azure-Services in een end-to-end-scenario, markeren Time Series Insights" lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>Een route maken en filteren op dubbele update meldingen

Azure Digital Apparaatdubbels-instanties kunnen [dubbele update gebeurtenissen](how-to-interpret-event-data.md) verzenden wanneer de status van een twee is bijgewerkt. In deze sectie gaat u een Azure Digital Apparaatdubbels- [**gebeurtenis route**](concepts-route-events.md) maken die deze update gebeurtenissen naar Azure [Event hubs](../event-hubs/event-hubs-about.md) stuurt voor verdere verwerking.

De zelf studie over Azure Digital Apparaatdubbels [*: verbinding maken met een end-to-end-oplossing*](./tutorial-end-to-end.md) door een scenario waarin een thermo meter wordt gebruikt om een temperatuur kenmerk bij te werken op een digitale dubbele locatie die een ruimte vertegenwoordigt. Dit patroon is afhankelijk van de dubbele updates, in plaats van het door sturen van telemetrie van een IoT-apparaat, die u de flexibiliteit geeft om de onderliggende gegevens bron te wijzigen zonder dat u uw Time Series Insights logica hoeft bij te werken.

1. Maak eerst een Event Hub-naam ruimte, die gebeurtenissen ontvangt van uw Azure Digital Apparaatdubbels-exemplaar. U kunt de onderstaande Azure CLI-instructies gebruiken of de Azure Portal: Quick Start [*: een event hub maken met behulp van Azure Portal*](../event-hubs/event-hubs-create.md).

    ```azurecli-interactive
    # Create an Event Hubs namespace. Specify a name for the Event Hubs namespace.
    az eventhubs namespace create --name <name for your Event Hubs namespace> --resource-group <resource group name> -l <region, for example: East US>
    ```

2. Maak een Event Hub binnen de naam ruimte.

    ```azurecli-interactive
    # Create an event hub to receive twin change events. Specify a name for the event hub. 
    az eventhubs eventhub create --name <name for your Twins event hub> --resource-group <resource group name> --namespace-name <Event Hubs namespace from above>
    ```

3. Maak een [autorisatie regel](/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest&preserve-view=true#az-eventhubs-eventhub-authorization-rule-create) met machtigingen voor verzenden en ontvangen.

    ```azurecli-interactive
    # Create an authorization rule. Specify a name for the rule.
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace from above> --eventhub-name <Twins event hub name from above> --name <name for your Twins auth rule>
    ```

4. Maak een Azure Digital Apparaatdubbels- [eind punt](concepts-route-events.md#create-an-endpoint) dat uw event hub koppelt aan uw Azure Digital apparaatdubbels-exemplaar.

    ```azurecli-interactive
    az dt endpoint create eventhub --endpoint-name <name for your Event Hubs endpoint> --eventhub-resource-group <resource group name> --eventhub-namespace <Event Hubs namespace from above> --eventhub <Twins event hub name from above> --eventhub-policy <Twins auth rule from above> -n <your Azure Digital Twins instance name>
    ```

5. Maak een [route](concepts-route-events.md#create-an-event-route) in azure Digital apparaatdubbels om dubbele update gebeurtenissen naar uw eind punt te verzenden. Met het filter in deze route worden alleen dubbele update berichten door gegeven aan uw eind punt.

    >[!NOTE]
    >Er is momenteel een **bekend probleem** in Cloud Shell dat deze opdrachtgroepen beïnvloedt: `az dt route`, `az dt model`, `az dt twin`.
    >
    >Om dit probleem op te lossen, kunt u `az login` in Cloud Shell uitvoeren voordat u de opdracht uitvoert, of kunt u de [lokale CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) gebruiken in plaats van Cloud Shell. Zie [*Problemen oplossen: Bekende problemen in Azure Digital Twins*](troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell) voor meer informatie hierover.

    ```azurecli-interactive
    az dt route create -n <your Azure Digital Twins instance name> --endpoint-name <Event Hub endpoint from above> --route-name <name for your route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

Voordat u doorgaat, noteert u uw *Event hubs naam ruimte* en *resource groep*, omdat u deze opnieuw gaat gebruiken om nog een event hub later in dit artikel te maken.

## <a name="create-an-azure-function"></a>Een Azure-functie maken 

Vervolgens maakt u een door Event Hubs geactiveerde functie binnen een functie-app. U kunt de functie-app die u in de end-to-end zelf studie hebt gemaakt, gebruiken ([*zelf studie: een end-to-end-oplossing verbinden*](./tutorial-end-to-end.md)) of uw eigen. 

Met deze functie worden deze dubbele update gebeurtenissen van hun oorspronkelijke formulier geconverteerd als JSON-patch-documenten naar JSON-objecten, die alleen bijgewerkte en toegevoegde waarden van uw apparaatdubbels bevatten.

Zie [*Azure Event hubs trigger voor Azure functions*](../azure-functions/functions-bindings-event-hubs-trigger.md)voor meer informatie over het gebruik van Event hubs met Azure functions.

Vervang de functie code door de volgende code in de gepubliceerde functie-app.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/updateTSI.cs":::

Hier verzendt de functie vervolgens de JSON-objecten die worden gemaakt naar een tweede Event Hub, waarmee u verbinding maakt met Time Series Insights.

Later gaat u ook enkele omgevings variabelen instellen die met deze functie worden gebruikt om verbinding te maken met uw eigen event hubs.

## <a name="send-telemetry-to-an-event-hub"></a>Telemetrie verzenden naar een Event Hub

U gaat nu een tweede Event Hub maken en uw functie zo configureren dat de uitvoer naar die Event Hub wordt gestreamd. Deze Event Hub wordt vervolgens verbonden met Time Series Insights.

### <a name="create-an-event-hub"></a>Een Event Hub maken

Als u de tweede Event Hub wilt maken, kunt u de onderstaande Azure CLI-instructies gebruiken of de Azure Portal: [*Snelstartgids: een event hub maken met Azure Portal*](../event-hubs/event-hubs-create.md).

1. Bereid uw *Event hubs* -naam ruimte en de naam van de *resource groep* voor vanaf eerder in dit artikel

2. Een nieuwe Event Hub maken
    ```azurecli-interactive
    # Create an event hub. Specify a name for the event hub. 
    az eventhubs eventhub create --name <name for your TSI event hub> --resource-group <resource group name from earlier> --namespace-name <Event Hubs namespace from earlier>
    ```
3. Een [autorisatie regel](/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest&preserve-view=true#az-eventhubs-eventhub-authorization-rule-create) maken met machtigingen voor verzenden en ontvangen
    ```azurecli-interactive
    # Create an authorization rule. Specify a name for the rule.
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace from earlier> --eventhub-name <TSI event hub name from above> --name <name for your TSI auth rule>
    ```

## <a name="configure-your-function"></a>Uw functie configureren

Vervolgens moet u de omgevings variabelen in uw functie-app van eerder instellen, met de verbindings reeksen voor de Event hubs die u hebt gemaakt.

### <a name="set-the-twins-event-hub-connection-string"></a>Stel de Apparaatdubbels-Event Hub in connection string

1. Haal de Apparaatdubbels- [Event Hub Connection String](../event-hubs/event-hubs-get-connection-string.md)op met behulp van de autorisatie regels die u hierboven hebt gemaakt voor de apparaatdubbels hub.

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <Twins event hub name from earlier> --name <Twins auth rule from earlier>
    ```

2. Gebruik de connection string die u krijgt als gevolg van het maken van een app-instelling in uw functie-app die uw connection string bevat:

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<Twins event hub connection string>" -g <resource group> -n <your App Service (function app) name>
    ```

### <a name="set-the-time-series-insights-event-hub-connection-string"></a>Stel de Time Series Insights Event Hub in connection string

1. De TSI [Event Hub Connection String](../event-hubs/event-hubs-get-connection-string.md)ophalen met behulp van de autorisatie regels die u hierboven hebt gemaakt voor de time series Insights hub:

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <TSI event hub name> --name <TSI auth rule>
    ```

2. Maak in uw functie-app een app-instelling die uw connection string bevat:

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<TSI event hub connection string>" -g <resource group> -n <your App Service (function app) name>
    ```

## <a name="create-and-connect-a-time-series-insights-instance"></a>Een Time Series Insights-exemplaar maken en verbinden

Vervolgens stelt u een Time Series Insights-exemplaar in om de gegevens van uw tweede Event Hub te ontvangen. Volg de onderstaande stappen en Zie [*zelf studie: een Azure time series Insights GEN2 payg-omgeving instellen*](../time-series-insights/tutorials-set-up-tsi-environment.md)voor meer informatie over dit proces.

1. Begin met het maken van een Time Series Insights resource in het Azure Portal. 
    1. Selecteer de prijs categorie **Gen2 (L1)** .
    2. U moet een **tijd reeks-id** voor deze omgeving kiezen. De tijd reeks-ID mag Maxi maal drie waarden zijn die u gaat gebruiken om te zoeken naar uw gegevens in Time Series Insights. Voor deze zelf studie kunt u **$dtId** gebruiken. Meer informatie over het selecteren van een ID-waarde in [*Best practices voor het kiezen van een time series-id*](../time-series-insights/how-to-select-tsid.md).
    
        :::image type="content" source="media/how-to-integrate-time-series-insights/create-twin-id.png" alt-text="De portal voor het maken van een Time Series Insights-omgeving. De prijs categorie Gen2 (L1) is geselecteerd en de eigenschaps naam van de tijd reeks-ID is $dtId" lightbox="media/how-to-integrate-time-series-insights/create-twin-id.png":::

2. Selecteer **volgende: gebeurtenis bron** en selecteer uw event hubs informatie hierboven. U moet ook een nieuwe Event Hubs Consumer groep maken.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/event-source-twins.png" alt-text="De portal voor het maken van een Time Series Insights-omgevings gebeurtenis bron. U maakt een gebeurtenis bron met de Event Hub informatie hierboven. U maakt ook een nieuwe consumenten groep." lightbox="media/how-to-integrate-time-series-insights/event-source-twins.png":::

## <a name="begin-sending-iot-data-to-azure-digital-twins"></a>Beginnen met het verzenden van IoT-gegevens naar Azure Digital Apparaatdubbels

Als u wilt beginnen met het verzenden van gegevens naar Time Series Insights, moet u beginnen met het bijwerken van de digitale twee eigenschappen in azure Digital Apparaatdubbels met het wijzigen van gegevens waarden. Gebruik de opdracht [AZ DT dubbele update](/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest&preserve-view=true#ext-azure-iot-az-dt-twin-update) .

Als u de end-to-end zelf studie gebruikt ([*zelf studie: een end-to-end oplossing verbinden*](tutorial-end-to-end.md)) om u te helpen bij het instellen van de omgeving, kunt u gesimuleerde IOT-gegevens gaan verzenden door het *DeviceSimulator* -project uit te voeren vanuit het voor beeld. De instructies zijn te vinden in de sectie [*simulatie configureren en uitvoeren*](tutorial-end-to-end.md#configure-and-run-the-simulation) van de zelf studie.

## <a name="visualize-your-data-in-time-series-insights"></a>Uw gegevens visualiseren in Time Series Insights

Gegevens moeten nu worden gestroomd naar uw Time Series Insights-exemplaar, die klaar zijn om te worden geanalyseerd. Volg de onderstaande stappen om de gegevens in te verkennen.

1. Open uw Time Series Insights-exemplaar in de [Azure Portal](https://portal.azure.com) (u kunt zoeken naar de naam van uw exemplaar in de zoek balk van de portal). Ga naar de *URL voor Time Series Insights Explorer* die in het exemplarenoverzicht wordt weergegeven.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="Selecteer de URL van Time Series Insights Explorer op het tabblad Overzicht van uw Time Series Insights omgeving":::

2. In de Explorer ziet u uw drie apparaatdubbels van Azure Digital Apparaatdubbels die aan de linkerkant worden weer gegeven. Selecteer _**thermostat67**_, selecteer **Tempe ratuur** en druk op **toevoegen**.

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="Selecteer * * thermostat67 * *, selecteer * * Tempe ratuur * * en druk op * * toevoegen * *":::

3. U ziet nu de oorspronkelijke temperatuur aflezingen van uw Thermo staat, zoals hieronder wordt weer gegeven. Diezelfde temperatuur meting wordt bijgewerkt voor *room21* en *floor1*, en u kunt deze gegevens stromen op elkaar visualiseren.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="De initiële temperatuur gegevens worden in een grafiek in de TSI-Verkenner gegrafeerd. Het is een regel met wille keurige waarden tussen 68 en 85":::

4. Als u toestaat dat de simulatie veel langer wordt uitgevoerd, ziet uw visualisatie er ongeveer als volgt uit:
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="De gegevens over de Tempe ratuur van elke dubbele kleur worden in drie parallelle lijnen van verschillende kleuren genoteerd.":::

## <a name="next-steps"></a>Volgende stappen

De digitale apparaatdubbels worden standaard opgeslagen als een platte hiërarchie in Time Series Insights, maar ze kunnen worden verrijkt met model gegevens en een hiërarchie met meerdere niveaus voor de organisatie. Lees voor meer informatie over dit proces: 

* [*Zelf studie: een model definiëren en Toep assen*](../time-series-insights/tutorials-set-up-tsi-environment.md#define-and-apply-a-model) 

U kunt aangepaste logica schrijven om deze informatie automatisch te verstrekken met behulp van het model en de grafiek gegevens die al zijn opgeslagen in azure Digital Apparaatdubbels. Zie de volgende verwijzingen voor meer informatie over het beheren, bijwerken en ophalen van gegevens uit de apparaatdubbels-grafiek:

* [*Instructies: een digitale twee richtings beheer beheren*](./how-to-manage-twin.md)
* [*Instructies: een query uitvoeren op de dubbele grafiek*](./how-to-query-graph.md)