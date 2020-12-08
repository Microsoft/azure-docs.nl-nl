---
title: Azure Synapse Studio-notebooks
description: Deze zelfstudie biedt een overzicht van het maken van een Power BI-dashboard met behulp van Apache Spark en een Serverloze SQL-pool.
services: synapse-analytics
author: midesa
ms.author: midesa
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.date: 11/16/2020
ms.openlocfilehash: 791cab369dcbf9cab8d1256377cfee4a433c21b9
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96450896"
---
# <a name="tutorial-create-a-power-bi-report-using-apache-spark-and-azure-synapse-analytics"></a>Zelfstudie: Een Power BI-rapport maken met behulp van Apache Spark en Azure Synapse Analytics

Organisaties moeten vaak grote hoeveelheden gegevens verwerken voordat ze worden gebruikt voor belangrijke zakelijke belanghebbenden. In deze zelfstudie leert u hoe u de geïntegreerde ervaring in Azure Synapse Analytics kunt gebruiken om gegevens te verwerken met Apache Spark en later de gegevens voor eindgebruikers te gebruiken via Power BI en Serverloze SQL.

## <a name="before-you-begin"></a>Voordat u begint
- [Azure Synapse Analytics-werkruimte](../quickstart-create-workspace.md) met een ADLS Gen2-opslagaccount dat is geconfigureerd als de standaardopslag. 
- Power BI-werkruimte en Power BI Desktop voor het visualiseren van gegevens. Zie [een Power BI-werkruimte maken](https://docs.microsoft.com/power-bi/service-create-the-new-workspaces) en [Power BI desktop installeren](https://powerbi.microsoft.com/downloads/) voor meer informatie
- Gekoppelde service om uw Azure Synapse Analytics- en Power BI-werkruimten met elkaar te verbinden. Zie [koppeling naar een Power BI-werkruimte](../quickstart-power-bi.md) voor meer informatie
- Serverloze Apache Spark-pool in uw Synapse Analytics-werkruimte. Zie [een serverloze Apache Spark-pool maken ](../quickstart-create-apache-spark-pool-studio.md) voor meer informatie
  
## <a name="download-and-prepare-the-data"></a>De gegevens downloaden en voorbereiden
In dit voorbeeld gebruikt u Apache Spark om een analyse uit te voeren voor gegevens over fooien voor taxiritten in New York. De gegevens zijn beschikbaar via [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Deze subset van de gegevensset bevat informatie over taxiritten, waaronder informatie over elke rit, de begin- en eindtijd en locaties, de kosten, en andere interessante kenmerken.

1. Voer de volgende regels uit om een Spark-gegevensframe te maken door de code in een nieuwe cel te plakken. Hiermee haalt u de gegevens op via de Open Datasets-API. Het ophalen van deze gegevens genereert bijna 1,5 miljard rijen. In het volgende codevoorbeeld wordt gebruikgemaakt van start_date en end_date om een filter toe te passen waarmee één maand aan gegevens worden geretourneerd.
   
   ```python
    from azureml.opendatasets import NycTlcYellow
    from dateutil import parser
    from datetime import datetime

    end_date = parser.parse('2018-06-06')
    start_date = parser.parse('2018-05-01')
    nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
    filtered_df = nyc_tlc.to_spark_dataframe()
   ```
2. Met Apache Spark SQL maken we een database met de naam NycTlcTutorial. Deze database gaan we gebruiken om de resultaten van de gegevensverwerking op te slaan.
   ```python
   %%pyspark
    spark.sql("CREATE DATABASE IF NOT EXISTS NycTlcTutorial")
   ```
3. Vervolgens gebruiken we Spark dataframe-bewerkingen voor het verwerken van de gegevens. In de volgende code voeren we de volgende transformaties uit:
   1. Onnodige kolommen verwijderen.
   2. Filteren om uitschieters/onjuiste waarden te verwijderen.
   3. Het maken van nieuwe functies als ```tripTimeSecs``` en ```tipped``` voor extra analyse.
    ```python
    from pyspark.sql.functions import unix_timestamp, date_format, col, when

    taxi_df = filtered_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'rateCodeId', 'passengerCount'\
                                    , 'tripDistance', 'tpepPickupDateTime', 'tpepDropoffDateTime'\
                                    , date_format('tpepPickupDateTime', 'hh').alias('pickupHour')\
                                    , date_format('tpepPickupDateTime', 'EEEE').alias('weekdayString')\
                                    , (unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('tripTimeSecs')\
                                    , (when(col('tipAmount') > 0, 1).otherwise(0)).alias('tipped')
                                    )\
                            .filter((filtered_df.passengerCount > 0) & (filtered_df.passengerCount < 8)\
                                    & (filtered_df.tipAmount >= 0) & (filtered_df.tipAmount <= 25)\
                                    & (filtered_df.fareAmount >= 1) & (filtered_df.fareAmount <= 250)\
                                    & (filtered_df.tipAmount < filtered_df.fareAmount)\
                                    & (filtered_df.tripDistance > 0) & (filtered_df.tripDistance <= 100)\
                                    & (filtered_df.rateCodeId <= 5)
                                    & (filtered_df.paymentType.isin({"1", "2"})))
    ```
4. Ten slotte gaan we ons dataframe opslaan met behulp van de Apache Spark-methode ```saveAsTable```. Hierdoor kunt u later een query uitvoeren en verbinding maken met dezelfde tabel met behulp van serverloze SQL-pools.
  ```python
     taxi_df.write.mode("overwrite").saveAsTable("NycTlcTutorial.nyctaxi")
  ```
   
## <a name="query-data-using-serverless-sql-pools"></a>Query's uitvoeren op gegevens met serverloze SQL-pools
Met Azure Synapse Analytics kunnen de verschillende rekenengines voor de werkruimte databases en tabellen delen tussen de serverloze Apache Spark-pools en serverloze SQL-pools. Dit wordt mogelijk gemaakt via de Synapse-functie [beheren van gedeelde metagegevens](../metadata/overview.md). Als gevolg hiervan worden de door Apache Spark gemaakte databases en de door Parquet ondersteunde tabellen zichtbaar in de werkruimte-serverloze SQL-pool.

Een query uitvoeren op uw Apache Spark-tabel met behulp van een serverloze SQL-pool:
   1. Nadat u de Apache Spark-tabel hebt opgeslagen, gaat u naar het tabblad **Gegevens**.
   
   2. Zoek onder **Werkruimten** de Apache Spark-tabel die u zojuist hebt gemaakt en selecteer **Nieuw SQL-script** en vervolgens **Bovenste 100 rijen selecteren**. 
      
      :::image type="content" source="../spark/media/apache-spark-power-bi/query-spark-table-with-sql.png" alt-text="SQL-query." border="true":::

   3. U kunt uw query verder verfijnen of uw resultaten zelfs visualiseren met behulp van de SQL-grafiekopties.

## <a name="connect-to-power-bi"></a>Verbinding maken met Power BI
Daarna gaan we onze serverloze SQL-pool koppelen aan onze Power BI-werkruimte. Nadat u uw werkruimte hebt verbonden, kunt u zowel vanuit Azure Synapse Analytics als vanuit Power BI desktop rechtstreeks Power BI-rapporten maken.

>[!Note]
> Voordat u begint, moet u een gekoppelde service instellen op uw [Power BI-werkruimte](../quickstart-power-bi.md) en de [Power BI desktop](https://docs.microsoft.com/power-bi/service-create-the-new-workspaces) downloaden.  

Om onze serverloze SQL-pool te verbinden met onze Power BI-werkruimte:

1.  Ga naar het tabblad **Power BI-gegevenssets** en selecteer de optie **+ Nieuwe gegevensset**. Download het .pbids-bestand van de SQL Analytics-database die u als gegevensbron wilt gebruiken. 
   :::image type="content" source="../spark/media/apache-spark-power-bi/power-bi-datasets.png" alt-text="Power BI-gegevenssets." border="true":::

2.  Open het bestand met Power BI Desktop om een gegevensset te maken. Nadat u het bestand hebt geopend, maakt u verbinding met de SQL Server-database met behulp van de opties **Microsoft-account** en **Importeren**. 
   

## <a name="create-a-power-bi-report"></a>Een Power BI-rapport maken
1. Vanuit Power BI desktop kunt u nu een grafiek met **belangrijkste beïnvloeders** toevoegen aan uw rapport.
   
   1. Sleep de kolom gemiddelde *tipAmount* naar de as **Analyseren**.
   
   2. Sleep de kolommen **weekdayString**, gemiddelde **tripDistance** en gemiddelde **tripTimeSecs** naar de as **Uitleggen met**. 
   
   :::image type="content" source="../spark/media/apache-spark-power-bi/power-bi-desktop.png" alt-text="Power BI Desktop." border="true":::

2. Selecteer op het tabblad Home van Power BI desktop **Publiceren** en **Opslaan** van wijzigingen. Voer een bestandsnaam in en sla dit rapport op in de *NycTaxiTutorial-werkruimte*.
   
3. Daarnaast kunt u ook Power BI-visualisaties maken in uw Azure Synapse Analytics-werkruimte. Hiertoe gaat u naar het tabblad **Ontwikkelen** in de Azure Synapse-werkruimte en opent u het tabblad Power BI. Hier kunt u uw rapport selecteren en doorgaan met het bouwen van extra visualisaties. 
   
   :::image type="content" source="../spark/media/apache-spark-power-bi/power-bi-synapse.png" alt-text="Azure Synapse Analytics-werkruimte." border="true":::

Voor meer informatie over het maken van een gegevensset via serverloze SQL en verbinding maken met Power BI, kunt u deze zelfstudie bezoeken op [verbinding maken met Power BI desktop](../../synapse-analytics/sql/tutorial-connect-power-bi-desktop.md)

## <a name="next-steps"></a>Volgende stappen
Ga naar de volgende documenten en zelfstudies voor meer informatie over de mogelijkheden voor gegevensvisualisatie in Azure Synapse Analytics:
   - [Gegevens visualiseren met serverloze Apache Spark-pools](../spark/apache-spark-data-visualization-tutorial.md)
   - [Overzicht van gegevensvisualisatie met Apache Spark-pools](../spark/apache-spark-data-visualization.md)
