---
title: Verbinding maken met Azure Synapse Link voor Azure Cosmos DB
description: Een Azure Cosmos DB-database verbinden met een Azure Synapse-werkruimte met behulp van Azure Synapse Link.
services: synapse-analytics
author: Rodrigossz
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 03/02/2021
ms.author: rosouz
ms.reviewer: jrasnick
ms.custom: cosmos-db
ms.openlocfilehash: 9a73ea697a48a5b2514f4701b8a24896e9a04c1f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105627690"
---
# <a name="connect-to-azure-synapse-link-for-azure-cosmos-db"></a>Verbinding maken met Azure Synapse Link voor Azure Cosmos DB

In dit artikel wordt beschreven hoe u toegang krijgt tot een Azure Cosmos DB-database vanuit Azure Synapse Analytics Studio met behulp van Azure Synapse Link.

## <a name="prerequisites"></a>Vereisten

Voordat u een Azure Cosmos DB-database verbindt met uw werkruimte, hebt u het volgende nodig:

* Een bestaande Azure Cosmos DB-database, of u kunt een nieuw account maken door de stappen te volgen in: [Quickstart: Een Azure Cosmos DB-account beheren](../../cosmos-db/how-to-manage-database-account.md).
* Een bestaande Azure Synapse-werkruimte, of u kunt een nieuwe werkruimte maken door de stappen te volgen in [Quickstart: Een Synapse-werkruimte maken](../quickstart-create-workspace.md).

## <a name="enable-synapse-link-on-an-azure-cosmos-db-database-account"></a>Synapse Link op een Azure Cosmos DB-database-account inschakelen

Als u grootschalige analyses wilt uitvoeren in Azure Cosmos DB zonder de prestaties van bewerkingen te beïnvloeden, wordt u aangeraden om Synapse Link voor Azure Cosmos DB in te schakelen. Synapse Link brengt HTAP-mogelijkheden naar een container en biedt ingebouwde ondersteuning in Azure Synapse.

## <a name="go-to-synapse-studio"></a>Ga naar Synapse Studio

Selecteer **Synapse Studio starten** vanuit uw Azure Synapse-werkruimte. Selecteer op de startpagina van Synapse Studio de optie **Gegevens**. U wordt nu naar Data Object Explorer geleid.

## <a name="connect-an-azure-cosmos-db-database-to-an-azure-synapse-workspace"></a>Een Azure Cosmos DB-database verbinden met een Azure Synapse-werkruimte

Het verbinden van een Azure Cosmos DB-database wordt uitgevoerd als een gekoppelde service. Met een gekoppelde service van Azure Cosmos DB kunt u bladeren in gegevens en deze verkennen, en lees- en schrijfbewerkingen vanuit Apache Spark voor Azure Synapse Analytics of SQL uitvoeren in Azure Cosmos DB.

Vanuit Data Object Explorer kunt u rechtstreeks verbinding maken met een Azure Cosmos DB-database door de volgende stappen te volgen:

1. Selecteer het pictogram **+** bij de optie **Gegevens**.
1. Selecteer **Verbinding maken met externe gegevens**.
1. Selecteer de API waarmee u verbinding wilt maken, bijvoorbeeld **SQL API** of **API voor MongoDB**.
1. Selecteer **Doorgaan**.
1. Gebruik een beschrijvende naam voor de gekoppelde service. De naam wordt weergegeven in Data Object Explorer en wordt tijdens Azure Synapse-uitvoeringen gebruikt om verbinding te maken met de database en containers.
1. Selecteer de **Azure Cosmos DB-accountnaam** en de **databasenaam**.
1. Als er geen regio is opgegeven, worden Azure Synapse-uitvoeringen gerouteerd naar de dichtstbijzijnde regio waar de analytische opslag is ingeschakeld (optioneel). U kunt de regio die u uw gebruikers wilt laten gebruiken om toegang te krijgen tot de analytische opslag van Azure Cosmos DB ook handmatig instellen. Selecteer **Aanvullende verbindingseigenschappen** en selecteer vervolgens **Nieuw**. Voer onder **Naam van eigenschap** **PreferredRegions** in. Stel de **waarde** in op de gewenste regio, bijvoorbeeld **WestUS2**. (Er zijn geen spaties tussen de woorden en het getal.)
1. Selecteer **Maken**.

Azure Cosmos DB-databases worden weergegeven op het tabblad **Gekoppeld** in het gedeelte **Azure Cosmos DB**. Met Azure Cosmos DB kunt u een container met HTAP onderscheiden van een alleen-OLTP-container, met behulp van de volgende pictogrammen:

**Alleen-OLTP-container**:

![Visualisatie waarmee het OLTP-containerpictogram wordt weergegeven.](../media/quickstart-connect-synapse-link-cosmosdb/oltp-container.png)

**Container met HTAP**:

![Visualisatie waarmee het HTAP-containerpictogram wordt weergegeven.](../media/quickstart-connect-synapse-link-cosmosdb/htap-container.png)

## <a name="quickly-interact-with-code-generated-actions"></a>Snel communiceren via met code gegenereerde acties

Als u met de rechtermuisknop op een container klikt, ziet u een lijst met gebaren die een Apache Spark- of SQL-uitvoering activeren. Schrijven naar een container gebeurt via de transactionele opslag van Azure Cosmos DB, en hierbij worden aanvraageenheden verbruikt.  

## <a name="next-steps"></a>Volgende stappen

* [Krijg inzicht in wat wordt ondersteund tussen Azure Synapse en Azure Cosmos DB](./concept-synapse-link-cosmos-db-support.md)
* [Persoonlijke eind punten configureren voor Azure Cosmos DB-analytische opslag](../../cosmos-db/analytical-store-private-endpoints.md)
* [Leer hoe u de analytische opslag doorzoekt met Spark](./how-to-query-analytical-store-spark.md)