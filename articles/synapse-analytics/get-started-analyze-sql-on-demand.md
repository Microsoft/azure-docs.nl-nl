---
title: 'Zelf studie: aan de slag met het analyseren van gegevens met een serverloze SQL-groep'
description: In deze zelfstudie leert u hoe u gegevens kunt analyseren met serverloze SQL-pool, met behulp van gegevens die zich in Spark-databases bevinden.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: c8a0ad3eb578280ea29d94dfbb5e74d873568541
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259639"
---
# <a name="analyze-data-with-a-serverless-sql-pool"></a>Gegevens analyseren met een serverloze SQL-groep

In deze zelf studie leert u hoe u gegevens kunt analyseren met serverloze SQL-pool. 

## <a name="the-built-in-serverless-sql-pool"></a>De ingebouwde serverloze SQL-pool

Met serverloze SQL-groepen kunt u SQL gebruiken zonder dat hiervoor capaciteit hoeft te worden gereserveerd. Facturering voor een serverloze SQL-groep is gebaseerd op de hoeveelheid gegevens die wordt verwerkt om de query uit te voeren en niet het aantal knoop punten dat wordt gebruikt om de query uit te voeren.

Elke werk ruimte wordt geleverd met een vooraf geconfigureerde, serverloze SQL-pool met de naam **ingebouwde**. 

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-serverless-sql-pool"></a>NYC Taxi-gegevens analyseren in blob-opslag met een serverloze SQL-pool

In deze sectie gebruikt u een serverloze SQL-groep voor het analyseren van NYC taxi-gegevens in een Azure Blob Storage-account.

1. Ga in Synapse Studio naar de **ontwikkelende** hub
1. Maak een nieuw SQL-script.
1. Plak de volgende code in het script.

    ```
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
                BULK 'https://contosolake.dfs.core.windows.net/users/NYCTripSmall.parquet',
            FORMAT='PARQUET'
        ) AS [result]
    ```
1. Klik op **Uitvoeren**

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Gegevens analyseren met een serverloze Spark-pool](get-started-analyze-spark.md)
