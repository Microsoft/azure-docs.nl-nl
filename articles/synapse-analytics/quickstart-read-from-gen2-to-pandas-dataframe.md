---
title: 'Quick Start: gegevens lezen van ADLS Gen2 naar Panda data frame'
description: Lees gegevens van een Azure Data Lake Storage Gen2-account in een Panda data frame met behulp van python in Synapse Studio in azure Synapse Analytics.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: quickstart
ms.reviewer: jrasnick, garye, negust
ms.date: 03/23/2021
author: garyericson
ms.author: garye
ms.openlocfilehash: b7358c522cf12e7856496ad71fda393394e7ceab
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969867"
---
# <a name="quickstart-read-data-from-adls-gen2-to-pandas-dataframe-in-azure-synapse-analytics"></a>Quick Start: gegevens lezen van ADLS Gen2 naar Panda data frame in azure Synapse Analytics

In deze Quick Start leert u hoe u op eenvoudige wijze python kunt gebruiken om gegevens te lezen van een Azure Data Lake Storage (ADLS) Gen2 in een Panda data frame in azure Synapse Analytics.

Vanuit een Synapse Studio-notebook voert u de volgende handelingen uit:

- verbinding maken met een container in Data Lake Storage Gen2 die is gekoppeld aan uw Azure Synapse Analytics-werk ruimte
- de gegevens van een PySpark-notebook lezen met `spark.read.load`
- de gegevens converteren naar een Panda data frame met behulp van `.toPandas()`

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [Maak een gratis abonnement aan](https://azure.microsoft.com/free/).
- De Synapse Analytics-werk ruimte met Data Lake Storage Gen2 geconfigureerd als de standaard opslag: u moet de Inzender voor gegevens van de **opslag-BLOB** van het data Lake Storage Gen2 bestands systeem waarmee u samenwerkt. Zie [een Synapse-werk ruimte maken](get-started-create-workspace.md)voor meer informatie over het maken van een werk ruimte.
- Apache Spark pool in uw werk ruimte: Zie [een serverloze Apache Spark groep maken](get-started-analyze-spark.md#create-a-serverless-apache-spark-pool).

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="upload-sample-data-to-adls-gen2"></a>Voorbeeld gegevens uploaden naar ADLS Gen2

1. Maak in de Azure Portal een container in dezelfde Data Lake Storage Gen2 die wordt gebruikt door Synapse Studio. U kunt deze stap overs Laan als u het standaard gekoppelde opslag account wilt gebruiken in uw Azure Synapse Analytics-werk ruimte.

1. Klik in Synapse Studio op **gegevens**, selecteer het tabblad **gekoppeld** en selecteer de container onder **Azure data Lake Storage Gen2**.

1. Down load het voorbeeld bestand [RetailSales.csv](https://github.com/Azure-Samples/Synapse/blob/main/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/RetailData/RetailSales.csv) en upload het naar de container.

1. Selecteer het geüploade bestand, klik op **Eigenschappen** en kopieer de waarde van het **ABFSS** .

## <a name="read-data-from-adls-gen2-into-a-pandas-dataframe"></a>Gegevens van ADLS Gen2 lezen in een Panda data frame

1. Klik in het linkerdeel venster op **ontwikkelen**.

1. Klik **+** en selecteer ' notitie blok ' om een nieuw notitie blok te maken.

1. Selecteer in **koppelen aan** de Apache Spark groep. Als u er nog geen hebt, klikt u op **Apache Spark groep maken**.

1. Plak de volgende python-code in de cel notebook code en voeg het ABFSS-pad in dat u eerder hebt gekopieerd:

   ```python
   %%pyspark
   data_path = spark.read.load('<ABFSS Path to RetailSales.csv>', format='csv', header=True)
   data_path.show(10)
   
   print('Converting to Pandas.')
   
   pdf = data_path.toPandas()
   print(pdf)
   ```

1. Voer de cel uit.

Na een paar minuten moet de weer gegeven tekst er ongeveer als volgt uitzien.

```text
Command executed in 25s 324ms by gary on 03-23-2021 17:40:23.481 -07:00

Job execution Succeeded Spark 2 executors 8 cores

+-------+-----------+--------+-----------+-----------+-----+------------+--------------------+
|storeId|productCode|quantity|logQuantity|advertising|price|weekStarting|                  id|
+-------+-----------+--------+-----------+-----------+-----+------------+--------------------+
|      2| surface.go|     105|9.264828557|          1|  159|   6/15/2017|d6bd47a7-2ad6-4f0...|
|      2| surface.go|      80|8.987196821|          0|  269|   7/27/2017|64cc74c2-c7da-4e1...|
|      2| surface.go|      68|8.831711918|          1|  209|    8/3/2017|9a2d164b-5e44-44d...|
|      2| surface.go|      28|7.965545573|          0|  209|   8/10/2017|b8cd9987-1d5a-4f4...|
|      2| surface.go|      16|7.377758908|          0|  209|   8/24/2017|ac0ec099-e102-4bf...|
|      2| surface.go|     253| 10.1402973|          1|  189|   8/31/2017|3d22c002-b04c-409...|
|      2| surface.go|     107|9.282847063|          0|  189|    9/7/2017|b6e19699-d684-449...|
|      2| surface.go|      66|8.803273983|          0|  189|   9/14/2017|e89a5838-fb8f-413...|
|      2| surface.go|      65|8.793612072|          0|  179|   9/21/2017|c3278682-16c0-483...|
|      2| surface.go|      17|7.454719949|          0|  269|  10/12/2017|f40190c1-b2ed-46f...|
+-------+-----------+--------+-----------+-----------+-----+------------+--------------------+
only showing top 10 rows

Converting to Pandas.
      storeId  ...                                    id
0           2  ...  d6bd47a7-2ad6-4f0a-b8de-ed1386cae5ea
1           2  ...  64cc74c2-c7da-4e12-af64-c95bdf429934
2           2  ...  9a2d164b-5e44-44d7-9837-cf9ae6566c99
3           2  ...  b8cd9987-1d5a-4f4f-9346-719d73b1f7f0
4           2  ...  ac0ec099-e102-4bfc-9775-983b151dcd03
...       ...  ...                                   ...
28942     137  ...  6af00133-7015-415d-831b-ddf05bb5828c
28943     137  ...  1e0d3a21-ab43-49c4-89e2-49d202821807
28944     137  ...  5cc7e50a-6aa4-419b-a933-905a667aa2df
28945     137  ...  650ca506-7a4f-46f8-b2e1-e52ceffadf16
28946     137  ...  9bb216f6-04ec-4b61-9e68-34772b814c44

[28947 rows x 8 columns]
```

## <a name="next-steps"></a>Volgende stappen

- [Wat is Azure Synapse Analytics?](overview-what-is.md)
- [Aan de slag met Azure Synapse Analytics](get-started.md)
- [Een serverloze Apache Spark-pool maken](get-started-analyze-spark.md#create-a-serverless-apache-spark-pool)
