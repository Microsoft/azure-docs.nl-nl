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
ms.date: 07/20/2020
ms.openlocfilehash: fabfdce72202f79e2ac5bad08d124df7ce2de542
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94592580"
---
# <a name="analyze-data-in-a-storage-account"></a>Gegevens analyseren in een opslagaccount

In deze zelfstudie leert u hoe u gegevens kunt analyseren die zich in een opslagaccount bevinden.

## <a name="overview"></a>Overzicht

Tot nu toe zijn scenario's beschreven waarin gegevens in databases staan die zijn opgenomen in de werkruimte. U leert nu werken met bestanden in opslagaccounts. In dit scenario wordt het primaire opslagaccount van de werkruimte en de container die we hebben opgegeven bij het maken van de werkruimte gebruikt.

* De naam van het opslagaccount: **contosolake**
* De naam van de container in het opslagaccount: **users**

### <a name="create-csv-and-parquet-files-in-your-storage-account"></a>CSV- en Parquet-bestanden maken in uw opslagaccount

Voer de volgende code uit in een notebook. Er wordt een CSV-bestand en een Parquet-bestand gemaakt in het opslagaccount.

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats_csvformat")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats_parquetformat")
```

### <a name="analyze-data-in-a-storage-account"></a>Gegevens analyseren in een opslagaccount

1. Ga in Synapse Studio naar de hub **Gegevens** en selecteer vervolgens **Gekoppeld**.
1. Navigeer naar **Opslagaccounts** > **myworkspace (Primary - contosolake)** .
1. Selecteer **users (Primary)** . De map **NYCTaxi** moet worden weergegeven. Hierin ziet u twee mappen, **PassengerCountStats_csvformat** en **PassengerCountStats_parquetformat**.
1. Open de map **PassengerCountStats_parquetformat**. U ziet nu een Parquet-bestand met een naam als `part-00000-2638e00c-0790-496b-a523-578da9a15019-c000.snappy.parquet`.
1. Klik met de rechtermuisknop op **.parquet** en selecteer vervolgens **Nieuwe notebook**. Er wordt een notebook gemaakt met een cel als deze:

    ```py
    %%pyspark
    data_path = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    data_path.show(100)
    ```

1. Voer de cel uit.
1. Klik met de rechtermuisknop op het Parquet-bestand en selecteer **Nieuw SQL-script** > **Eerste 100 rijen selecteren**. Er wordt een SQL-script als deze gemaakt:

    ```sql
    SELECT TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [r];
    ```

    In het scriptvenster wordt het veld **Verbinding maken met** ingesteld op **Serverloze SQL-pool**.

1. Voer het script uit.



## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Activiteiten organiseren met pijplijnen](get-started-pipelines.md)
