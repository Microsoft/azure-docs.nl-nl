---
title: Gegevens visualiseren met Apache Spark
description: Uitgebreide gegevens visualisaties maken met behulp van Apache Spark en Azure Synapse Analytics-notebooks
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 10/20/2020
ms.author: midesa
ms.openlocfilehash: 8768b8f8c7bf70b184971abc6ce27e2193823dea
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121546"
---
# <a name="analyze-data-with-apache-spark"></a>Gegevens analyseren met Apache Spark

In deze zelf studie leert u hoe u experimentele gegevens analyse kunt uitvoeren met behulp van Azure open gegevens sets en Apache Spark en vervolgens de resultaten in een Azure Synapse Studio-notebook kunt visualiseren.

In het bijzonder zullen we de [taxi-gegevensset van New York (NYC)](https://azure.microsoft.com/en-us/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) analyseren. De gegevens zijn beschikbaar via Azure Open Datasets. Deze subset van de gegevensset bevat informatie over gele taxi reizen, inclusief informatie over elke reis, de begin-en eind tijd en-locaties, de kosten en andere interessante kenmerken.
  
## <a name="before-you-begin"></a>Voordat u begint
- Maak een Apache Spark groep door de [zelf studie een Apache Spark groep maken](../articles/../quickstart-create-apache-spark-pool-studio.md) te volgen 

## <a name="download-and-prepare-the-data"></a>De gegevens downloaden en voorbereiden
1. Maak een notebook met behulp van de PySpark-kernel. Zie [een notitie blok maken](../quickstart-apache-spark-notebook.md#create-a-notebook)voor instructies. 
   
> [!Note]
> 
> Vanwege de PySpark-kernel hoeft u niet expliciet contexten te maken. De Spark-context wordt automatisch voor u gemaakt wanneer u de eerste codecel uitvoert.
>

2. In deze zelf studie gaan we verschillende bibliotheken gebruiken om ons te helpen bij het visualiseren van de gegevensset. Om deze analyse uit te voeren, moeten de volgende bibliotheken worden geïmporteerd: 

```python
import matplotlib.pyplot as plt
import seaborn as sns
import pandas as pd
```

3. Omdat de onbewerkte gegevens de Parquet-indeling hebben, kunt u de Spark-context gebruiken om het bestand rechtstreeks in het geheugen te plaatsen als een gegevensframe. Maak een Spark-gegevensframe door de gegevens op te halen via de Open Datasets-API. Hier gebruiken we het Spark data frame- *schema op Lees* eigenschappen om de gegevens typen en het schema af te leiden.

```python
from azureml.opendatasets import NycTlcYellow
from datetime import datetime
from dateutil import parser

end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
df = nyc_tlc.to_spark_dataframe()
```

4. Zodra de gegevens zijn gelezen, willen we een aantal initiële filtering uitvoeren om de gegevensset op te schonen. We kunnen overbodige kolommen verwijderen en aanvullende kolommen toevoegen waarmee belang rijke informatie wordt geëxtraheerd. Daarnaast worden ook afwijkingen in de gegevensset gefilterd.

```python
# Filter the dataset 
from pyspark.sql.functions import *

filtered_df = df.select('vendorID', 'passengerCount', 'tripDistance','paymentType', 'fareAmount', 'tipAmount'\
                                , date_format('tpepPickupDateTime', 'hh').alias('hour_of_day')\
                                , dayofweek('tpepPickupDateTime').alias('day_of_week')\
                                , dayofmonth(col('tpepPickupDateTime')).alias('day_of_month'))\
                            .filter((df.passengerCount > 0)\
                                & (df.tipAmount >= 0)\
                                & (df.fareAmount >= 1) & (df.fareAmount <= 250)\
                                & (df.tripDistance > 0) & (df.tripDistance <= 200))

filtered_df.createOrReplaceTempView("taxi_dataset")
```

## <a name="analyze-data"></a>Gegevens analyseren
 Als gegevens analist hebt u een breed scala aan hulpprogram ma's die u kunt gebruiken om inzichten uit de gegevens te halen. In dit gedeelte van de zelf studie gaan we een aantal nuttige hulpprogram ma's door nemen die beschikbaar zijn in azure Synapse Analytics-notebooks. In deze analyse willen we inzicht krijgen in de factoren die een hogere taxi tips leveren voor de geselecteerde periode.

###  <a name="apache-spark-sql-magic"></a>Apache Spark SQL Magic 
Eerst gaan we experimentele gegevens analyse uitvoeren door Apache Spark SQL-en Magic-opdrachten met de Synapse-notebook. Zodra we onze query hebben uitgevoerd, zullen we de resultaten visualiseren met behulp van de ingebouwde ```chart options``` mogelijkheid. 

1. Maak in uw notitie blok een nieuwe cel en kopieer de onderstaande code. Met deze query willen we weten hoe de gemiddelde fooien zijn gewijzigd gedurende een periode die we hebben geselecteerd. Met deze query kunnen we ook andere nuttige inzichten identificeren, waaronder het minimale/maximale fooien bedrag per dag en het gemiddelde ritbedrag bedrag.
   
```sql
%%sql
SELECT 
    day_of_month
    , MIN(tipAmount) AS minTipAmount
    , MAX(tipAmount) AS maxTipAmount
    , AVG(tipAmount) AS avgTipAmount
    , AVG(fareAmount) as fareAmount
FROM taxi_dataset 
GROUP BY day_of_month
ORDER BY day_of_month ASC
```

2. Zodra de uitvoering van de query is voltooid, kunnen we de resultaten visualiseren door over te scha kelen naar de **grafiek weergave**. In dit voor beeld maken we een **lijn diagram** door het ```day_of_month``` veld als de **sleutel** en ```avgTipAmount``` als de **waarde** op te geven. Zodra u de selecties hebt gemaakt, klikt u op **Toep assen** om uw grafiek te vernieuwen. 
   
## <a name="visualize-data"></a>Gegevens visualiseren
Naast de ingebouwde opties voor notitieblok grafieken kunt u ook populaire open-source bibliotheken gebruiken om uw eigen visualisaties te maken. In de volgende voor beelden gebruiken we Seaborn en matplotlib, die vaak worden gebruikt voor de visualisatie van gegevens. 

> [!Note]
> 
> Elke Azure Synapse Analytics-Apache Spark groep bevat standaard een set veelgebruikte en standaard bibliotheken. U kunt de volledige lijst met bibliotheken weer geven in [Azure Synapse runtime](../spark/apache-spark-version-support.md). documentatie. Daarnaast kunt u [een bibliotheek](../spark/apache-spark-azure-portal-add-libraries.md) op een van de Spark-Pools installeren om derden of lokaal gemaakte code beschikbaar te maken voor uw toepassingen.
>

1. Om de ontwikkeling eenvoudiger en goedkoperer te maken, wordt de gegevensset ook voor beelden weer geven. We gebruiken de ingebouwde Apache Spark sampling-mogelijkheid. Daarnaast is voor zowel Seaborn als matplotlib een Panda data frame-of numpy-matrix vereist. Om een Panda data frame te verkrijgen, gebruiken we de ```toPandas()``` opdracht om onze data frame te converteren.

```python
# To make development easier, faster and less expensive down sample for now
sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)

# The charting package needs a Pandas dataframe or numpy array do the conversion
sampled_taxi_pd_df = sampled_taxi_df.toPandas()
```

1. Eerst willen we de verdeling van tips in onze gegevensset begrijpen. We gebruiken matplotlib om een histogram te maken met de verdeling van het aantal fooien en aantallen. Op basis van de distributie kunnen we zien dat tips worden schuingetrokken tot bedragen die kleiner zijn dan of gelijk zijn aan $10.

```python
# Look at tips by count histogram using Matplotlib

ax1 = sampled_taxi_pd_df['tipAmount'].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()
```

![Histogram met tips](./media/apache-spark-machine-learning-mllib-notebook/histogram.png)

1. We willen nu inzicht krijgen in de relatie tussen de tips voor een bepaalde reis en de dag van de week. We gebruiken Seaborn om een Boxplot te maken waarin de trends voor elke dag van de week worden samenvatten. 

```python
# View the distribution of tips by day of week using Seaborn
ax = sns.boxplot(x="day_of_week", y="tipAmount",data=sampled_taxi_pd_df, showfliers = False)
ax.set_title('Tip amount distribution per day')
ax.set_xlabel('Day of Week')
ax.set_ylabel('Tip Amount ($)')
plt.show()

```
![Distributie van fooien per dag](./media/apache-spark-data-viz/data-analyst-tutorial-per-day.png)

4. Nu kan een andere hypo these zijn dat er sprake is van een positieve relatie tussen het aantal reizigers en het totale bedrag van de taxi tip. Als u deze relatie wilt controleren, voert u de volgende code uit om een Boxplot te genereren waarin de verdeling van tips voor elk aantal reizigers wordt weer geven. 
   
```python
# How many passengers tipped by various amounts 
ax2 = sampled_taxi_pd_df.boxplot(column=['tipAmount'], by=['passengerCount'])
ax2.set_title('Tip amount by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
ax2.set_ylim(0,30)
plt.suptitle('')
plt.show()
```
![Kader whisker tekenen](./media/apache-spark-machine-learning-mllib-notebook/box-whisker-plot.png)

1. Ten slotte willen we weten wat de relatie is tussen het fooien bedrag van het ritbedrag bedrag. Op basis van de resultaten kunnen we zien dat er verschillende waarnemingen zijn waarbij mensen geen tip hebben. Er wordt echter ook een positieve relatie weer geven tussen de totale kosten voor ritbedrag en fooien.
   
```python
# Look at the relationship between fare and tip amounts

ax = sampled_taxi_pd_df.plot(kind='scatter', x= 'fareAmount', y = 'tipAmount', c='blue', alpha = 0.10, s=2.5*(sampled_taxi_pd_df['passengerCount']))
ax.set_title('Tip amount by Fare amount')
ax.set_xlabel('Fare Amount ($)')
ax.set_ylabel('Tip Amount ($)')
plt.axis([-2, 80, -2, 20])
plt.suptitle('')
plt.show()
```
![Spreidings plot](./media/apache-spark-machine-learning-mllib-notebook/scatter.png)

## <a name="shut-down-the-spark-instance"></a>Het Spark-exemplaar afsluiten

Wanneer u klaar bent met uitvoeren van de toepassing, sluit u de notebook af om de resources vrij te geven door het tabblad af te sluiten of **Sessie beëindigen** te selecteren in het statuspaneel onderaan de notebook.

## <a name="see-also"></a>Zie ook

- [Overzicht: Apache Spark in Azure Synapse Analytics](apache-spark-overview.md)
- [Een machine learning model bouwen met Apache SparkML](../spark/apache-spark-machine-learning-mllib-notebook.md)

## <a name="next-steps"></a>Volgende stappen

- [Azure Synapse Analytics](../index.yml)
- [Officiële documentatie voor Apache Spark](https://spark.apache.org/docs/latest/)