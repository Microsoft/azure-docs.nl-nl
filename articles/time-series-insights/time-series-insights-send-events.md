---
title: Gebeurtenissen verzenden naar een omgeving-Azure Time Series Insights | Microsoft Docs
description: Meer informatie over het configureren van een Event Hub, het uitvoeren van een voorbeeld toepassing en het verzenden van gebeurtenissen naar uw Azure Time Series Insights omgeving.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: 8ee2cc5473d83eafc2443f3aea7cab6a2e77727c
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/14/2021
ms.locfileid: "103463558"
---
# <a name="send-events-to-an-azure-time-series-insights-gen1-environment-by-using-an-event-hub"></a>Gebeurtenissen verzenden naar een Azure Time Series Insights gen1-omgeving met behulp van een Event Hub

> [!CAUTION]
> Dit is een Gen1-artikel.

In dit artikel wordt uitgelegd hoe u een Event Hub in azure Event Hubs maakt en configureert. Ook wordt beschreven hoe u een voorbeeld toepassing uitvoert om gebeurtenissen te pushen naar Azure Time Series Insights van Event Hubs. Als u een bestaande Event Hub hebt met gebeurtenissen in JSON-indeling, kunt u deze zelf studie overs Laan en uw omgeving bekijken in [Azure time series Insights](./tutorial-set-up-environment.md).

## <a name="configure-an-event-hub"></a>Een Event Hub configureren

1. Lees de [Event hubs documentatie](../event-hubs/index.yml)voor meer informatie over het maken van een event hub.
1. Zoek in het zoekvak naar **Event hubs**. Selecteer **Event hubs** in de weer gegeven lijst.
1. Selecteer uw Event Hub.
1. Wanneer u een Event Hub maakt, maakt u een Event Hub naam ruimte. Als u nog geen Event Hub in de naam ruimte hebt gemaakt, maakt u in het menu onder **entiteiten** een event hub.

    [![Lijst met Event hubs](media/send-events/tsi-connect-event-hub-namespace.png)](media/send-events/tsi-connect-event-hub-namespace.png#lightbox)

1. Nadat u een Event Hub hebt gemaakt, selecteert u deze in de lijst met Event hubs.
1. Selecteer in het menu onder **entiteiten** de optie **Event hubs**.
1. Selecteer de naam van de Event Hub om deze te configureren.
1. Selecteer onder **overzicht** de optie **consumenten groepen** en selecteer vervolgens **consumenten groep**.

    [![Een Consumer groep maken](media/send-events/add-event-hub-consumer-group.png)](media/send-events/add-event-hub-consumer-group.png#lightbox)

1. Zorg ervoor dat u een Consumer groep maakt die uitsluitend wordt gebruikt door de bron van de Azure Time Series Insights gebeurtenis.

    > [!IMPORTANT]
    > Zorg ervoor dat deze Consumer groep niet wordt gebruikt door een andere service, zoals een Azure Stream Analytics-taak of een andere Azure Time Series Insights omgeving. Als de Consumer groep wordt gebruikt door de andere services, worden Lees bewerkingen negatief beïnvloed voor deze omgeving en voor andere services. Als u **$default** als de Consumer groep gebruikt, kunnen andere lezers uw consumenten groep mogelijk hergebruiken.

1. Selecteer in het menu onder **instellingen** de optie **beleid voor gedeelde toegang** en selecteer vervolgens **toevoegen**.

    [![Selecteer beleid voor gedeelde toegang en selecteer vervolgens de knop toevoegen](media/send-events/add-shared-access-policy.png)](media/send-events/add-shared-access-policy.png#lightbox)

1. Maak in het deel venster **nieuw gedeeld toegangs beleid toevoegen** een gedeelde toegang met de naam **MySendPolicy**. U kunt dit beleid voor gedeelde toegang gebruiken voor het verzenden van gebeurtenissen in de C#-voor beelden verderop in dit artikel.

    [![Voer in het vak beleids naam MySendPolicy in](media/send-events/configure-shared-access-policy-confirm.png)](media/send-events/configure-shared-access-policy-confirm.png#lightbox)

1. Schakel onder **claim** het selectie vakje **verzenden** in.

## <a name="add-an-azure-time-series-insights-instance"></a>Een Azure Time Series Insights-exemplaar toevoegen

In Azure Time Series Insights Gen2 kunt u contextuele gegevens toevoegen aan inkomende telemetrie met behulp van het time series model (TSM). In TSM worden uw tags of signalen aangeduid als *instanties* en kunt u contextuele gegevens opslaan in *exemplaar velden.* De gegevens worden tijdens de query tijd gekoppeld met behulp van een **tijd reeks-id**. De **Time Series-id** voor het voorbeeld project Windmills dat later in dit artikel wordt gebruikt is `id` . Lees het overzicht van [Time Series model](./concepts-model-overview.md) voor meer informatie over het opslaan van gegevens in de exemplaar velden.

### <a name="create-an-azure-time-series-insights-event-source"></a>Een Azure Time Series Insights gebeurtenis bron maken

1. Als u nog geen gebeurtenis bron hebt gemaakt, voert u de stappen uit om [een gebeurtenis bron te maken](./how-to-ingest-data-event-hub.md).

1. Stel een waarde in voor `timeSeriesId` . Lees voor meer informatie over **tijd reeks-ID-** [modellen](./concepts-model-overview.md).

### <a name="push-events-to-windmills-sample"></a>Push gebeurtenissen naar Windmills-voor beeld

1. Zoek in de zoek balk naar **Event hubs**. Selecteer **Event hubs** in de weer gegeven lijst.

1. Selecteer uw Event Hub-exemplaar.

1. Ga naar het **beleid voor gedeelde toegang**  >  **MySendPolicy**. Kopieer de waarde voor **verbindings reeks-primaire sleutel**.

    [![Kopieer de waarde voor de primaire sleutel connection string](media/send-events/configure-sample-code-connection-string.png)](media/send-events/configure-sample-code-connection-string.png#lightbox)

1. Ga naar <https://tsiclientsample.azurewebsites.net/windFarmGen.html>. De URL maakt en voert gesimuleerde Windmill-apparaten uit.
1. Plak in het vak **Event hub-verbindings reeks** op de webpagina de Connection String die u hebt gekopieerd in het [invoer veld Windmill](#push-events-to-windmills-sample).

    [![Plak de primaire-sleutel connection string in het vak Verbindings reeks van de Event hub](media/send-events/configure-wind-mill-sim.png)](media/send-events/configure-wind-mill-sim.png#lightbox)

1. Selecteer **klikken om te starten**.

    > [!TIP]
    > De Windmill Simulator maakt ook JSON die u kunt gebruiken als een Payload met de [Azure time series INSIGHTS ga query-api's](/rest/api/time-series-insights/gen1-query).

    > [!NOTE]
    > De Simulator blijft gegevens verzenden totdat het tabblad browser is gesloten.

1. Ga terug naar uw Event Hub in de Azure Portal. Op de pagina **overzicht** worden de nieuwe gebeurtenissen weer gegeven die door de Event hub worden ontvangen.

    [![Een Event Hub overzichts pagina waarop de metrische gegevens voor de Event Hub worden weer gegeven](media/send-events/review-windmill-telemetry.png)](media/send-events/review-windmill-telemetry.png#lightbox)

## <a name="supported-json-shapes"></a>Ondersteunde JSON-vormen

### <a name="example-one"></a>Voor beeld 1

* **Invoer**: een eenvoudig JSON-object.

    ```JSON
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    }
    ```

* **Uitvoer**: één gebeurtenis.

    |id|tijdstempel|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|

### <a name="example-two"></a>Voor beeld twee

* **Invoer**: een JSON-matrix met twee JSON-objecten. Elk JSON-object wordt geconverteerd naar een gebeurtenis.

    ```JSON
    [
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z"
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z"
        }
    ]
    ```

* **Uitvoer**: twee gebeurtenissen.

    |id|tijdstempel|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|
    |device2|2016-01-08T01:17:00Z|

### <a name="example-three"></a>Voor beeld drie

* **Invoer**: een JSON-object met een geneste JSON-matrix met twee JSON-objecten.

    ```JSON
    {
        "location":"WestUs",
        "events":[
            {
                "id":"device1",
                "timestamp":"2016-01-08T01:08:00Z"
            },
            {
                "id":"device2",
                "timestamp":"2016-01-17T01:17:00Z"
            }
        ]
    }
    ```

* **Uitvoer**: twee gebeurtenissen. De **locatie** van de eigenschap wordt naar elke gebeurtenis gekopieerd.

    |location|events.id|events.timestamp|
    |--------|---------------|----------------------|
    |WestUs|device1|2016-01-08T01:08:00Z|
    |WestUs|device2|2016-01-08T01:17:00Z|

### <a name="example-four"></a>Voor beeld vier

* **Invoer**: een JSON-object met een geneste JSON-matrix met twee JSON-objecten. Deze invoer laat zien dat globale eigenschappen kunnen worden weer gegeven door het complexe JSON-object.

    ```JSON
    {
        "location":"WestUs",
        "manufacturer":{
            "name":"manufacturer1",
            "location":"EastUs"
        },
        "events":[
            {
                "id":"device1",
                "timestamp":"2016-01-08T01:08:00Z",
                "data":{
                    "type":"pressure",
                    "units":"psi",
                    "value":108.09
                }
            },
            {
                "id":"device2",
                "timestamp":"2016-01-17T01:17:00Z",
                "data":{
                    "type":"vibration",
                    "units":"abs G",
                    "value":217.09
                }
            }
        ]
    }
    ```

* **Uitvoer**: twee gebeurtenissen.

    |location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
    |---|---|---|---|---|---|---|---|
    |WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|druk|psi|108.09|
    |WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>Volgende stappen

* [Bekijk uw omgeving](https://insights.timeseries.azure.com) in de Azure time series Insights Explorer.

* Meer informatie over [IOT Hub-apparaten](../iot-hub/iot-hub-devguide-messages-construct.md)