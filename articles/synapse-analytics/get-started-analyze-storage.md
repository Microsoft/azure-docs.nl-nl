---
title: 'Zelfstudie: aan de slag met het analyseren van gegevens in opslagaccounts'
description: In deze zelfstudie leert u hoe u gegevens kunt analyseren die zich in een opslagaccount bevinden.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 71ba3d99ceee89464dafdf5bf4c16e70df146bef
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102426073"
---
# <a name="analyze-data-in-a-storage-account"></a>Gegevens analyseren in een opslagaccount

In deze zelfstudie leert u hoe u gegevens kunt analyseren die zich in een opslagaccount bevinden.

## <a name="overview"></a>Overzicht

Tot nu toe zijn scenario's beschreven waarin gegevens in databases staan die zijn opgenomen in de werkruimte. U leert nu werken met bestanden in opslagaccounts. In dit scenario wordt het primaire opslagaccount van de werkruimte en de container die we hebben opgegeven bij het maken van de werkruimte gebruikt.

* De naam van het opslagaccount: **contosolake**
* De naam van de container in het opslagaccount: **users**

### <a name="create-csv-and-parquet-files-in-your-storage-account"></a>CSV- en Parquet-bestanden maken in uw opslagaccount

Voer de volgende code uit in een notitie blok in een nieuwe cel met code. Er wordt een CSV-bestand en een Parquet-bestand gemaakt in het opslagaccount.

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats_csvformat")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats_parquetformat")
```

### <a name="analyze-data-in-a-storage-account"></a>Gegevens analyseren in een opslagaccount

U kunt de gegevens in uw werkruimte standaard ADLS Gen2 account analyseren of u kunt een ADLS Gen2-of Blob Storage-account koppelen aan uw werkruimte via '**Beheren**' >**Gekoppelde services**' > '**Nieuw**' (de onderstaande stappen verwijzen naar het primaire ADLS Gen2-account).

1. Ga in Synapse Studio naar de hub **Gegevens** en selecteer vervolgens **Gekoppeld**.
1. Ga naar **Azure data Lake Storage Gen2**  >  **myworkspace (Primary-contosolake)**.
1. Selecteer **users (Primary)** . De map **NYCTaxi** moet worden weergegeven. Hierin ziet u twee mappen, **PassengerCountStats_csvformat** en **PassengerCountStats_parquetformat**.
1. Open de map **PassengerCountStats_parquetformat**. U ziet nu een Parquet-bestand met een naam als `part-00000-2638e00c-0790-496b-a523-578da9a15019-c000.snappy.parquet`.
1. Klik met de rechter muisknop op **. Parquet**, selecteer **Nieuw notitie blok** en selecteer vervolgens **laden naar data frame**. Er wordt een nieuw notitie blok gemaakt met een cel als volgt:

    ```py
    %%pyspark
    df = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    display(df.limit(10))
    ```

1. Koppel aan de Spark-pool met de naam **Spark1**. Voer de cel uit.
1. Klik op terug naar de map **gebruikers** . Klik opnieuw met de rechter muisknop op het **. Parquet** -bestand en selecteer vervolgens **Nieuw SQL-script**  >  **Select Top 100 rows**. Er wordt een SQL-script als deze gemaakt:

    ```sql
    SELECT 
        TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [result]
    ```

    Controleer in het Script venster of het veld **verbinding maken** met de **ingebouwde** serverloze SQL-groep is ingesteld.

1. Voer het script uit.



## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Activiteiten organiseren met pijplijnen](get-started-pipelines.md)
