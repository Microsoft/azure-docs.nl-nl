---
title: Door query opgenomen telemetriegegevens
description: In dit artikel wordt beschreven hoe u opgenomen telemetriegegevens opvraagt.
author: sunasing
ms.topic: article
ms.date: 03/11/2020
ms.author: sunasing
ms.openlocfilehash: b9067e2f78c8098d4a21263ac89caf03da631274
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92677492"
---
# <a name="query-ingested-telemetry-data"></a>Door query opgenomen telemetriegegevens

In dit artikel wordt beschreven hoe u een query kunt uitvoeren op opgenomen sensor gegevens van Azure FarmBeats.

Het opnemen van gegevens uit Internet of Things IoT-resources zoals apparaten en Sens oren is een veelvoorkomend scenario in FarmBeats. U maakt meta gegevens voor apparaten en Sens oren en vervolgens neemt u de historische data op in een canonieke notatie van FarmBeats. Zodra de sensor gegevens beschikbaar zijn op FarmBeats Datahub, kunnen we een query uitvoeren op dezelfde voor het genereren van met actie bare inzichten of build-modellen.

## <a name="before-you-begin"></a>Voordat u begint

Voordat u verder gaat met dit artikel, moet u ervoor zorgen dat u FarmBeats en geconsumeerde telemetrie-sensor gegevens van uw IoT-apparaten hebt geïnstalleerd naar FarmBeats.

Als u telemetrie-sensor gegevens wilt opnemen, gaat u naar [historische telemetriegegevens gegevens](ingest-historical-telemetry-data-in-azure-farmbeats.md)

Voordat u doorgaat, moet u er ook voor zorgen dat u bekend bent met FarmBeats REST-Api's wanneer u een query uitvoert op opgenomen telemetrie met behulp van de Api's. Zie [FARMBEATS rest api's](rest-api-in-azure-farmbeats.md)(Engelstalig) voor meer informatie over FarmBeats-api's. **Zorg ervoor dat u API-aanvragen kunt maken naar uw FarmBeats Datahub-eind punt**.

## <a name="query-ingested-sensor-telemetry-data"></a>Gegevensgestuurde telemetrie-sensor gegevens opvragen

Er zijn twee manieren om toegang te krijgen tot telemetriegegevens en gegevens op te vragen vanuit FarmBeats:

- API en
- Time Series Insights (TSI).

### <a name="query-using-rest-api"></a>Query's uitvoeren met REST API

Volg de stappen om een query uit te voeren op de gegevensgestuurde sensor-telemetrie met behulp van FarmBeats REST-Api's:

1. Identificeer de sensor waarin u bent geïnteresseerd. U kunt dit doen door een GET-aanvraag te maken op/sensor-API.

> [!NOTE]
> De **id** en de **sensorModelId** van het object voor de geïnteresseerde sensor.

2. Maak een GET/{id} op de/SensorModel-API voor de **sensorModelId** zoals vermeld in stap 1. Het sensor model bevat alle meta gegevens en Details over de opgenomen telemetrie van de sensor. De meting van de **sensor** in het **sensor model** object heeft bijvoorbeeld informatie over welke metingen de sensor verzendt en in welke typen en eenheden. Bijvoorbeeld:

  ```json
  {
      "name": "moist_soil_last <name of the sensor measure - this is what we will receive as part of the queried telemetry data>",
      "dataType": "Double <Data Type - eg. Double>",
      "type": "SoilMoisture <Type of measure eg. temperature, soil moisture etc.>",
      "unit": "Percentage <Unit of measure eg. Celsius, Percentage etc.>",
      "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation>",
      "description": "<Description of the measure>"
  }
  ```
Noteer de reactie van de aanroep GET/{id} voor het sensor model.

3. Een POST-aanroep uitvoeren op/Telemetry-API met de volgende invoer lading

  ```json
  {
    "sensorId": "<id of the sensor as noted in step 1>",
    "searchSpan": {
      "from": "<desired start timestamp in ISO 8601 format; default is UTC>",
      "to": "<desired end timestamp in ISO 8601 format; default is UTC>"
    },
    "filter": {
      "tsx": "string"
    },
    "projectedProperties": [
      {
        "additionalProp1": "string",
        "additionalProp2": "string",
        "additionalProp3": "string"
      }
    ]
  }
  ```
4. Het antwoord van de/Telemetry-API ziet er ongeveer als volgt uit:

  ```json
  {
    "timestamps": [
      "2020-XX-XXT07:30:00Z",
      "2020-XX-XXT07:45:00Z"
    ],
    "properties": [
      {
        "values": [
          "<id of the sensor>",
          "<id of the sensor>"
        ],
        "name": "Id",
        "type": "String"
      },
      {
        "values": [
          2.1,
          2.2
        ],
        "name": "moist_soil_last <name of the SensorMeasure as defined in the SensorModel object>",
        "type": "Double <Data Type of the value - eg. Double>"
      }
    ]
  }
  ```
In het bovenstaande voor beeld geeft de query op sensor-telemetrie gegevens voor twee tijds tempels samen met de naam van de meting (' moist_soil_last ') en de waarden van de gerapporteerde telemetrie in de twee tijds tempels. U moet verwijzen naar het bijbehorende sensor model (zoals beschreven in stap 2) om het type en de eenheid van de gerapporteerde waarden te interpreteren.

### <a name="query-using-azure-time-series-insights-tsi"></a>Query's uitvoeren met behulp van Azure Time Series Insights (TSI)

FarmBeats maakt gebruik van [Azure time series Insights (TSI)](https://azure.microsoft.com/services/time-series-insights/) om gegevens op IOT-schaal te opnemen, op te slaan, op te vragen en te visualiseren, en gegevens die zeer gevoelig en geoptimaliseerd voor time series zijn.

Telemetriegegevens worden ontvangen op een EventHub en vervolgens verwerkt en gepusht naar een TSI-omgeving binnen FarmBeats-resource groep. Gegevens kunnen vervolgens rechtstreeks vanuit de TSI worden opgevraagd. Raadpleeg de [TSI-documentatie](../../time-series-insights/time-series-insights-explorer.md) voor meer informatie.

Volg de stappen voor het visualiseren van gegevens op TSI:

1. Ga naar **Azure Portal**  >  **FarmBeats DataHub resource groep** > Selecteer **Time Series Insights** omgeving (TSI-xxxx) > **beleid voor gegevens toegang**. Voeg een gebruiker toe met toegang voor lezer of Inzender.
2. Ga naar de pagina **overzicht** van **Time Series Insights** omgeving (TSI-xxxx) en selecteer de **URL van Time Series Insights Explorer**. U kunt nu de opgenomen telemetrie visualiseren.

Naast het opslaan, doorzoeken en visualisatie van telemetrie, biedt TSI ook integratie met een Power BI dash board. Zie [hier]( https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

U hebt nu sensor gegevens opgevraagd van uw Azure FarmBeats-exemplaar. Nu leert u hoe u [kaarten](generate-maps-in-azure-farmbeats.md#generate-maps) voor uw Farms kunt genereren.