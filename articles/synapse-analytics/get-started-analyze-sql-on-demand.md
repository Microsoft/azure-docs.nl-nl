---
title: 'Zelfstudie: Aan de slag met het analyseren van gegevens met een serverloze SQL-pool'
description: In deze zelfstudie leert u hoe u gegevens kunt analyseren met serverloze SQL-pool, met behulp van gegevens die zich in Spark-databases bevinden.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 04/15/2021
ms.openlocfilehash: c6f2dfe0d4846227400ac9b3c7ac3e6ead8f0b57
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567550"
---
# <a name="analyze-data-with-a-serverless-sql-pool"></a>Gegevens analyseren met een serverloze SQL-pool

In deze zelfstudie leert u hoe u gegevens kunt analyseren met een serverloze SQL-pool. 

## <a name="the-built-in-serverless-sql-pool"></a>De ingebouwde serverloze SQL-pool

Met serverloze SQL-pools kunt u SQL gebruiken zonder dat u capaciteit moet reserveren. Facturering voor een serverloze SQL-pool is gebaseerd op de hoeveelheid gegevens die worden verwerkt om de query uit te voeren en niet het aantal knooppunten dat wordt gebruikt om de query uit te voeren.

Elke werkruimte wordt geleverd met een vooraf geconfigureerde serverloze **SQL-pool** met de naam Ingebouwd. 

## <a name="analyze-nyc-taxi-data-with-a-serverless-sql-pool"></a>Nyc Taxi-gegevens analyseren met een serverloze SQL-pool

1. Ga Synapse Studio naar de hub **Ontwikkelen**
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
1. Klik op **Run**. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Gegevens analyseren met een serverloze Spark-pool](get-started-analyze-spark.md)
