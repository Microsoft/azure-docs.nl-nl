---
title: SQL-groepen bewaken in Synapse Studio
description: Meer informatie over het bewaken van uw SQL-Pools met behulp van Synapse Studio.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 63b63526333435387c3ff5b5c9d5599ec851c1a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96466661"
---
# <a name="use-synapse-studio-to-monitor-your-sql-pools"></a>Synapse Studio gebruiken om uw SQL-groepen te bewaken

Met Synapse Studio kunt u SQL-scripts uitvoeren op de SQL-groepen in uw werk ruimte.

In dit artikel wordt uitgelegd hoe u uw SQL-groepen kunt bewaken, zodat u de status en activiteit van uw Pools in de gaten kunt houden.

## <a name="access-sql-pools-list"></a>Lijst met SQL-groepen voor toegang

Als u de lijst met SQL-groepen in uw werk ruimte wilt zien, opent u eerst [de Synapse Studio](https://web.azuresynapse.net/) en selecteert u uw werk ruimte.

![Aanmelden bij werk ruimte](./media/common/login-workspace.png)

Wanneer u uw werk ruimte hebt geopend, selecteert u de sectie **monitor** aan de linkerkant.

![Hub bewaken selecteren](./media/common/left-nav.png)

Selecteer **SQL-groepen** om de lijst met SQL-groepen weer te geven.

 ![SQL-groepen selecteren](./media/how-to-monitor-sql-pools/monitor-hub-nav-sql-pools.png)

## <a name="filter-your-sql-pools"></a>Uw SQL-groepen filteren

U kunt de lijst met SQL-groepen filteren op degene die u interesseren. Met de filters boven aan het scherm kunt u een veld opgeven waarop u wilt filteren.

U kunt bijvoorbeeld de weer gave filteren om alleen de SQL-groepen weer te geven met de naam ' salesrecords ':

![Voorbeeld filter](./media/how-to-monitor-sql-pools/filter-example.png)

## <a name="view-details-about-a-specific-sql-pool"></a>Details over een specifieke SQL-groep weer geven

Als u de details van een van uw SQL-groepen wilt weer geven, selecteert u de SQL-groep om de details weer te geven.

![Details van SQL-groep](./media/how-to-monitor-sql-pools/sql-pool-details.png)

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het bewaken van pijplijn uitvoeringen de [pipeline-uitvoeringen in Synapse Studio](how-to-monitor-pipeline-runs.md) -artikel. 

Zie het artikel [SQL-aanvragen bewaken in Synapse Studio](how-to-monitor-sql-requests.md) voor meer informatie over het bewaken van SQL-aanvragen.