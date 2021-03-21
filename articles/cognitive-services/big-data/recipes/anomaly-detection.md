---
title: 'Recept: voor speld onderhoud met de Cognitive Services voor Big Data'
titleSuffix: Azure Cognitive Services
description: Deze Quick Start laat zien hoe u een gedistribueerde anomalie detectie kunt uitvoeren met de Cognitive Services voor Big Data
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: how-to
ms.date: 07/06/2020
ms.author: marhamil
ms.custom: devx-track-python
ms.openlocfilehash: d2995f39bc61ae5bb87abafd674f411271e57ca2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94366278"
---
# <a name="recipe-predictive-maintenance-with-the-cognitive-services-for-big-data"></a>Recept: voor speld onderhoud met de Cognitive Services voor Big Data

Dit recept laat zien hoe u Azure Synapse Analytics en Cognitive Services op Apache Spark kunt gebruiken voor het voor speld onderhoud van IoT-apparaten. We volgen samen met het [CosmosDB-en Synapse-koppelings](https://github.com/Azure-Samples/cosmosdb-synapse-link-samples) voorbeeld. Om alles eenvoudig te maken, lezen we in dit recept de gegevens rechtstreeks vanuit een CSV-bestand in plaats van gestreamde gegevens via de CosmosDB-en Synapse-koppeling. We raden u aan om het voor beeld van de Synapse-koppeling te bekijken.

## <a name="hypothetical-scenario"></a>Hypothetisch scenario

Het hypothetische scenario is een energiebeheer, waarbij IoT-apparaten [stoom turbines](https://en.wikipedia.org/wiki/Steam_turbine)controleren. De verzameling IoTSignals heeft omwentelingen per minuut (RPM) en megawatts (MW) voor elke turbine. Signalen van stoom turbines worden geanalyseerd en afwijkende signalen worden gedetecteerd.

De gegevens kunnen in wille keurige frequentie uitbijten. In dat geval worden de waarden voor de RPM en de MW-uitvoer omlaag, voor de beveiliging van circuits. Het is de bedoeling dat de gegevens op hetzelfde moment worden weer gegeven, maar met verschillende signalen.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement - [Een gratis abonnement maken](https://azure.microsoft.com/free/cognitive-services)
* [Azure Synapse-werk ruimte](../../../synapse-analytics/quickstart-create-workspace.md) die is geconfigureerd met een [serverloze Apache Spark pool](../../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)

## <a name="setup"></a>Instellen

### <a name="create-an-anomaly-detector-resource"></a>Een Anomaly Detector-resource maken

Azure Cognitive Services worden vertegenwoordigd door Azure-resources waarop u zich abonneert. Een resource voor Translator maken met behulp van de [Azure Portal](../../cognitive-services-apis-create-account.md) of [Azure cli](../../cognitive-services-apis-create-account-cli.md). U kunt ook het volgende doen:

- Bekijk een bestaande resource in de  [Azure Portal](https://portal.azure.com/).

Noteer het eind punt en de sleutel voor deze resource. u hebt deze nodig in deze hand leiding.

## <a name="enter-your-service-keys"></a>Voer uw service sleutels in

Laten we beginnen door uw sleutel en locatie toe te voegen.

```python
service_key = None # Paste your anomaly detector key here
location = None # Paste your anomaly detector location here

assert (service_key is not None)
assert (location is not None)
```

## <a name="read-data-into-a-dataframe"></a>Gegevens in een data frame lezen

We gaan nu het IoTSignals-bestand lezen in een data frame. Open een nieuw notitie blok in uw Synapse-werk ruimte en maak een data frame uit het bestand.

```python
df_device_info = spark.read.csv("wasbs://publicwasb@mmlspark.blob.core.windows.net/iot/IoTSignals.csv", header=True, inferSchema=True)
```

### <a name="run-anomaly-detection-using-cognitive-services-on-spark"></a>Anomalie detectie uitvoeren met Cognitive Services op Spark

Het doel is instanties te vinden waarbij de signalen van IoT-apparaten afwijkende waarden uitvoeren, zodat we kunnen zien wanneer er iets mis gaat en het voor speld onderhoud zou doen. We gebruiken hiervoor anomalie detectie op Spark:

```python
from pyspark.sql.functions import col, struct
from mmlspark.cognitive import SimpleDetectAnomalies
from mmlspark.core.spark import FluentAPI

detector = (SimpleDetectAnomalies()
    .setSubscriptionKey(service_key)
    .setLocation(location)
    .setOutputCol("anomalies")
    .setGroupbyCol("grouping")
    .setSensitivity(95)
    .setGranularity("secondly"))

df_anomaly = (df_signals
    .where(col("unitSymbol") == 'RPM')
    .withColumn("timestamp", col("dateTime").cast("string"))
    .withColumn("value", col("measureValue").cast("double"))
    .withColumn("grouping", struct("deviceId"))
    .mlTransform(detector)).cache()

df_anomaly.createOrReplaceTempView('df_anomaly')
```

Laten we eens kijken naar de gegevens:

```python
df_anomaly.select("timestamp","value","deviceId","anomalies.isAnomaly").show(3)
```

Deze cel moet een resultaat opleveren dat er als volgt uitziet:

| tijdstempel           |   waarde | deviceId   | isAnomaly   |
|:--------------------|--------:|:-----------|:------------|
| 2020-05-01 18:33:51 |    3174 | dev-7      | Niet waar       |
| 2020-05-01 18:33:52 |    2976 | dev-7      | Niet waar       |
| 2020-05-01 18:33:53 |    2714 | dev-7      | Niet waar       |


 ## <a name="visualize-anomalies-for-one-of-the-devices"></a>Afwijkingen voor een van de apparaten visualiseren

IoTSignals.csv heeft signalen van meerdere IoT-apparaten. We richten zich op een specifiek apparaat en visualiseren uitvoer afwijkend van het apparaat.

```python
df_anomaly_single_device = spark.sql("""
select
  timestamp,
  measureValue,
  anomalies.expectedValue,
  anomalies.expectedValue + anomalies.upperMargin as expectedUpperValue,
  anomalies.expectedValue - anomalies.lowerMargin as expectedLowerValue,
  case when anomalies.isAnomaly=true then 1 else 0 end as isAnomaly
from
  df_anomaly
where deviceid = 'dev-1' and timestamp < '2020-04-29'
order by timestamp
limit 200""")
```

Nu we een data frame hebben gemaakt die de afwijkingen voor een bepaald apparaat vertegenwoordigt, kunnen we deze afwijkingen visualiseren:

```python
import matplotlib.pyplot as plt
from pyspark.sql.functions import col

adf = df_anomaly_single_device.toPandas()
adf_subset = df_anomaly_single_device.where(col("isAnomaly") == 1).toPandas()

plt.figure(figsize=(23,8))
plt.plot(adf['timestamp'],adf['expectedUpperValue'], color='darkred', linestyle='solid', linewidth=0.25, label='UpperMargin')
plt.plot(adf['timestamp'],adf['expectedValue'], color='darkgreen', linestyle='solid', linewidth=2, label='Expected Value')
plt.plot(adf['timestamp'],adf['measureValue'], 'b', color='royalblue', linestyle='dotted', linewidth=2, label='Actual')
plt.plot(adf['timestamp'],adf['expectedLowerValue'],  color='black', linestyle='solid', linewidth=0.25, label='Lower Margin')
plt.plot(adf_subset['timestamp'],adf_subset['measureValue'], 'ro', label = 'Anomaly')
plt.legend()
plt.title('RPM Anomalies with Confidence Intervals')
plt.show()
```

Als dit lukt, ziet uw uitvoer er als volgt uit:

![Afwijkende detector](../media/anomaly-output.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe u voorspellende onderhoud op schaal kunt uitvoeren met Azure Cognitive Services, Azure Synapse Analytics en Azure CosmosDB. Zie voor meer informatie het volledige voor beeld op [github](https://github.com/Azure-Samples/cosmosdb-synapse-link-samples).