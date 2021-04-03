---
title: SQL-aanvragen bewaken in Synapse Studio
description: Meer informatie over het bewaken van uw SQL-aanvragen met behulp van Synapse Studio.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 7296fec91decaf1bd80829f9f3a743a518fbb7a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96466652"
---
# <a name="use-synapse-studio-to-monitor-your-sql-requests"></a>Synapse Studio gebruiken om uw SQL-aanvragen te bewaken

Met Synapse Studio kunt u SQL-scripts uitvoeren op de SQL-groepen in uw werk ruimte.

In dit artikel wordt uitgelegd hoe u uw SQL-aanvragen bewaken, zodat u de status van het uitvoeren van aanvragen in de gaten kunt houden en Details van historische aanvragen moet ontdekken.

## <a name="access-sql-requests-list"></a>Lijst met SQL-aanvragen voor toegang

Als u de lijst met SQL-aanvragen in uw werk ruimte wilt zien, opent u eerst [de Synapse Studio](https://web.azuresynapse.net/) en selecteert u uw werk ruimte.

![Aanmelden bij werk ruimte](./media/common/login-workspace.png)

Wanneer u uw werk ruimte hebt geopend, selecteert u de sectie **monitor** aan de linkerkant.

![Hub bewaken selecteren](./media/common/left-nav.png)

Selecteer **SQL-aanvragen** om de lijst met SQL-aanvragen weer te geven.

 ![SQL-aanvragen selecteren](./media/how-to-monitor-sql-requests/monitor-hub-nav-sql-requests.png)

## <a name="select-a-sql-pool"></a>Een SQL-groep selecteren

De geschiedenis van de SQL-aanvraag voor de ingebouwde serverloze SQL-groep wordt standaard weer gegeven in deze weer gave. U kunt een van uw specifieke SQL-groepen selecteren om de geschiedenis van de SQL-aanvraag van die groep te bekijken.

![SQL-groep selecteren](./media/how-to-monitor-sql-requests/select-pool.png)

## <a name="filter-your-sql-requests"></a>Uw SQL-aanvragen filteren

U kunt de lijst met SQL-aanvragen filteren op degene die u interesseren. Met de filters boven aan het scherm kunt u een veld opgeven waarop u wilt filteren.

U kunt bijvoorbeeld de weer gave filteren om alleen de SQL-aanvragen weer te geven die zijn ingediend door `maria@contoso.com` :

![Voorbeeld filter](./media/how-to-monitor-sql-requests/filter-example.png)

## <a name="view-details-about-a-specific-sql-request"></a>Details over een specifieke SQL-aanvraag weer geven

Als u de details van een van uw SQL-aanvragen wilt weer geven, opent u de SQL-aanvraag om naar de weer gave details te gaan. Voor de complexe aanvragen die worden uitgevoerd op specifieke SQL-groepen, kunt u de voortgang bewaken.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het bewaken van pijplijn uitvoeringen de [pipeline-uitvoeringen in Synapse Studio](how-to-monitor-pipeline-runs.md) -artikel. 

Zie voor meer informatie over het bewaken van Apache Spark toepassingen het artikel [Apache Spark toepassingen bewaken in Synapse Studio](how-to-monitor-spark-applications.md) .