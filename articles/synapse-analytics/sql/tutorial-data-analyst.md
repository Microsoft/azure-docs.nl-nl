---
title: 'Zelfstudie: Een serverloze SQL-pool (preview) gebruiken voor het analyseren van Azure Open Datasets in Azure Synapse Studio (preview)'
description: Deze zelfstudie laat zien hoe u eenvoudig een verkennende gegevensanalyse kunt uitvoeren door verschillende Azure Open Datasets te combineren met behulp van een serverloze SQL-pool (preview) en de resultaten in Azure Synapse Studio kunt analyseren.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: cec16041edd10a57088df4ae9cfe0587906919eb
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93309286"
---
# <a name="tutorial-use-serverless-sql-pool-to-analyze-azure-open-datasets-and-visualize-the-results-in-azure-synapse-studio"></a>Zelfstudie: Gebruik een serverloze SQL-pool voor het analyseren van Azure Open Datasets en visualiseer resultaten in Azure Synapse Studio

In deze zelfstudie leert u hoe u verkennende gegevensanalyse kunt uitvoeren door verschillende Azure Open Datasets te combineren met behulp van een serverloze SQL-pool en hoe u de resultaten in Azure Synapse Studio kunt analyseren.

In het bijzonder gaat u de gegevensset [New York City (NYC) Taxi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) analyseren. Deze omvat:

- Datums en tijden voor ophalen en afzetten.
- Locaties voor ophalen en afzetten. 
- Reisafstanden.
- Gespecificeerde tarieven.
- Tarieftypen.
- Betalingswijzen. 
- Door de chauffeur gemelde passagiersaantallen.

## <a name="automatic-schema-inference"></a>Automatische schemadeductie

Omdat de gegevens zijn opgeslagen in de Parquet-bestandsindeling, is automatische schemadeductie beschikbaar. U kunt eenvoudig een query op de gegevens uitvoeren zonder dat u de gegevenstypen van alle kolommen in de bestanden hoeft weer te geven. U kunt ook het virtuele kolommechanisme en de functie bestandspad gebruiken om een bepaalde subset van bestanden te filteren.

Eerst bekijken we de gegevens van NYC Taxi door de volgende query uit te voeren:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
```

Het volgende fragment toont het resultaat voor de gegevens van NYC Taxi:

![Fragment met resultaten voor de gegevens van NYC Taxi](./media/tutorial-data-analyst/1.png)

Op dezelfde manier kunt u een query uitvoeren op de gegevensset Openbare feestdagen, met behulp van de volgende query:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
        FORMAT='PARQUET'
    ) AS [holidays]
```

Het volgende fragment toont het resultaat voor de gegevensset Openbare feestdagen:

![Fragment met resultaten voor de gegevensset Openbare feestdagen](./media/tutorial-data-analyst/2.png)

Tot slot kunnen we ook een query uitvoeren op de gegevensset Weer, met behulp van de volgende query:

```sql
SELECT
    TOP 100 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
```

Het volgende fragment toont het resultaat voor de gegevensset Weer:

![Fragment met resultaten voor de gegevensset Weer](./media/tutorial-data-analyst/3.png)

Meer informatie over de betekenis van de afzonderlijke kolommen vindt u in de beschrijvingen van de gegevenssets [NYC Taxi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/), [openbare feestdagen](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) en [Weergegevens](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/).

## <a name="time-series-seasonality-and-outlier-analysis"></a>Analyse van tijdreeks, seizoensgebondenheid en uitbijter

U kunt heel eenvoudig een samenvatting van het jaarlijks aantal taxiritten maken, met behulp van de volgende query:

```sql
SELECT
    YEAR(tpepPickupDateTime) AS current_year,
    COUNT(*) AS rides_per_year
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) >= '2009' AND nyc.filepath(1) <= '2019'
GROUP BY YEAR(tpepPickupDateTime)
ORDER BY 1 ASC
```

Het volgende fragment toont het resultaat voor het jaarlijkse aantal taxiritten:

![Fragment met resultaten voor de het jaarlijkse aantal taxiritten](./media/tutorial-data-analyst/4.png)

De gegevens kunnen worden gevisualiseerd in Synapse Studio door over te schakelen van de weergave **Tabel** naar de weergave **Grafiek**. U kunt kiezen uit verschillende grafiektypen, zoals **Gebied**, **Staaf**, **Kolom**, **Lijn**, **Cirkel** en **Spreiding**. In dit geval tekent u het **Kolomdiagram** met de kolom **Categorie** ingesteld op **current_year**:

![Kolomdiagram met het aantal ritten per jaar](./media/tutorial-data-analyst/5.png)

In deze visualisatie is duidelijk een trend zichtbaar van een afnemend aantal ritten door de jaren heen. Deze afname is waarschijnlijk te wijten aan de recent toegenomen populariteit van bedrijven die gedeelde ritten aanbieden.

> [!NOTE]
> Op het moment dat deze zelfstudie wordt geschreven, zijn de gegevens voor 2019 nog incompleet. Als gevolg hiervan is een enorme daling zichtbaar in het aantal ritten voor dat jaar.

We gaan de analyse nu op één jaar richten, bijvoorbeeld 2016. De volgende query retourneert het dagelijks aantal ritten gedurende dat jaar:

```sql
SELECT
    CAST([tpepPickupDateTime] AS DATE) AS [current_day],
    COUNT(*) as rides_per_day
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) = '2016'
GROUP BY CAST([tpepPickupDateTime] AS DATE)
ORDER BY 1 ASC
```

Het volgende fragment toont het resultaat voor deze query:

![Fragment met resultaten voor ritten in 2016](./media/tutorial-data-analyst/6.png)

Ook hier kunt u eenvoudig gegevens visualiseren door de **Kolomdiagram** te tekenen met de kolom **Categorie** ingesteld op **current_day** en de kolom **Legenda (reeks)** ingesteld op **rides_per_day**.

![Kolomdiagram met het dagelijkse aantal ritten voor 2016](./media/tutorial-data-analyst/7.png)

In het getekende diagram kunt u zien dat er sprake is van een wekelijks patroon, met zaterdagen als piekdag. Tijdens de zomermaanden zijn er minder taxiritten, vanwege de vakantie. U ziet ook aanzienlijke dalingen in het aantal taxiritten zonder duidelijk patroon wanneer en waarom ze optreden.

Laten we eens kijken of deze dalingen correleren met openbare feestdagen door ritten van NYC Taxi samen te voegen met de gegevensset Openbare feestdagen:

```sql
WITH taxi_rides AS
(
    SELECT
        CAST([tpepPickupDateTime] AS DATE) AS [current_day],
        COUNT(*) as rides_per_day
    FROM  
        OPENROWSET(
            BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT='PARQUET'
        ) AS [nyc]
    WHERE nyc.filepath(1) = '2016'
    GROUP BY CAST([tpepPickupDateTime] AS DATE)
),
public_holidays AS
(
    SELECT
        holidayname as holiday,
        date
    FROM
        OPENROWSET(
            BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
            FORMAT='PARQUET'
        ) AS [holidays]
    WHERE countryorregion = 'United States' AND YEAR(date) = 2016
)
SELECT
*
FROM taxi_rides t
LEFT OUTER JOIN public_holidays p on t.current_day = p.date
ORDER BY current_day ASC
```

![Visualisatie met resultaten voor de gegevensset met ritten van NYC Taxi en de gegevensset Openbare feestdagen](./media/tutorial-data-analyst/8.png)

Deze keer willen we het aantal taxiritten tijdens openbare feestdagen markeren. Hiervoor kiezen we **geen** voor de kolom **Categorie** en **rides_per_day** en **feestdag** als de kolommen **Legenda (reeks)** .

![Getekend diagram met aantal taxiritten tijdens openbare feestdagen](./media/tutorial-data-analyst/9.png)

In het getekende diagram kunt u zien dat het aantal taxiritten tijdens openbare feestdagen kleiner is. Er is nog steeds één onverklaarde grote daling op 23 januari. Laten we het weer in NYC op deze dag bekijken door een query uit te voeren op de gegevensset Weer:

```sql
SELECT
    AVG(windspeed) AS avg_windspeed,
    MIN(windspeed) AS min_windspeed,
    MAX(windspeed) AS max_windspeed,
    AVG(temperature) AS avg_temperature,
    MIN(temperature) AS min_temperature,
    MAX(temperature) AS max_temperature,
    AVG(sealvlpressure) AS avg_sealvlpressure,
    MIN(sealvlpressure) AS min_sealvlpressure,
    MAX(sealvlpressure) AS max_sealvlpressure,
    AVG(precipdepth) AS avg_precipdepth,
    MIN(precipdepth) AS min_precipdepth,
    MAX(precipdepth) AS max_precipdepth,
    AVG(snowdepth) AS avg_snowdepth,
    MIN(snowdepth) AS min_snowdepth,
    MAX(snowdepth) AS max_snowdepth
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
WHERE countryorregion = 'US' AND CAST([datetime] AS DATE) = '2016-01-23' AND stationname = 'JOHN F KENNEDY INTERNATIONAL AIRPORT'
```

![Visualisatie met resultaten voor de gegevensset Weer](./media/tutorial-data-analyst/10.png)

De resultaten van de query geven aan dat de daling van het aantal taxiritten plaatsvond vanwege het volgende:

- Er was die dag een storm in NYC, met zware sneeuwval (ongeveer 30 cm).
- Het was koud (temperatuur onder nul graden Celsius).
- Het waaide (~ 10 m/s).

In deze zelfstudie hebt u geleerd hoe een gegevensanalist snel verkennende gegevensanalyse kan uitvoeren, eenvoudig verschillende gegevenssets kan combineren met behulp van een serverloze SQL-pool, en de resultaten kan visualiseren met behulp van Azure Synapse Studio.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg [Een serverloze SQL-pool verbinden met Power BI Desktop en rapporten maken](tutorial-connect-power-bi-desktop.md) voor meer informatie over het verbinden van een serverloze SQL-pool met Power BI Desktop en het maken van rapporten.
 
