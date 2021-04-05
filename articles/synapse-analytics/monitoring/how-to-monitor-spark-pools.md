---
title: Apache Spark Pools bewaken in Synapse Studio
description: Meer informatie over het bewaken van uw Apache Spark Pools met behulp van Synapse Studio.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 8d95897e0c2d58b2a3955918be945800eed9ba56
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96466574"
---
# <a name="use-synapse-studio-to-monitor-your-apache-spark-pools"></a>Synapse Studio gebruiken om uw Apache Spark Pools te controleren

Met Azure Synapse Analytics kunt u Spark gebruiken om notitie blokken, taken en andere soorten toepassingen uit te voeren in Spark-Pools in uw werk ruimte.

In dit artikel wordt uitgelegd hoe u uw Apache Spark groepen kunt controleren, zodat u de status van uw Pools kunt blijven gebruiken, met inbegrip van het aantal vCores dat wordt gebruikt door verschillende gebruikers van de werk ruimte.

## <a name="access-spark-pools-list"></a>Lijst met Spark-groepen openen

Als u de lijst met Apache Spark groepen in uw werk ruimte wilt zien, opent u eerst [de Synapse Studio](https://web.azuresynapse.net/) en selecteert u uw werk ruimte.

![Aanmelden bij werk ruimte](./media/common/login-workspace.png)

Wanneer u uw werk ruimte hebt geopend, selecteert u de sectie **monitor** aan de linkerkant.

![Hub bewaken selecteren](./media/common/left-nav.png)

Selecteer **Apache Spark groepen** om de lijst met Apache Spark groepen weer te geven.

 ![Apache Spark groepen selecteren](./media/how-to-monitor-spark-pools/monitor-hub-nav-spark-pools.png)

## <a name="filter-your-spark-pools"></a>Uw Spark-Pools filteren

U kunt de lijst met Spark-groepen filteren op degene die u interesseren. Met de filters boven aan het scherm kunt u een veld opgeven waarop u wilt filteren.

U kunt bijvoorbeeld de weer gave filteren om alleen de Spark-Pools weer te geven met de naam ' dataprep ':

![Voorbeeld filter](./media/how-to-monitor-spark-pools/filter-example.png)

## <a name="view-details-about-a-specific-spark-pool"></a>Details over een specifieke Spark-groep weer geven

Als u de details van een van de Spark-Pools wilt weer geven, selecteert u de Spark-groep om de details weer te geven.

![Details van Apache Spark groep](./media/how-to-monitor-spark-pools/spark-pool-details.png)

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het bewaken van pijplijn uitvoeringen de [pipeline-uitvoeringen in Synapse Studio](how-to-monitor-pipeline-runs.md) -artikel. 

Zie voor meer informatie over het bewaken van Apache Spark toepassingen het artikel [Apache Spark toepassingen bewaken in Synapse Studio](how-to-monitor-spark-applications.md) .
