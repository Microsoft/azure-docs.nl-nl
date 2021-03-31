---
title: bestand opnemen
description: bestand opnemen
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 01/24/2020
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 941e2ee7b8eba957d970998324156c50a86439af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94504125"
---
1. Meld u in een nieuw browservenster aan bij [Azure Portal](https://portal.azure.com/).

2. Selecteer **Een resource maken** in het menu aan de linkerkant.
   
   :::image type="content" source="./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-0.png" alt-text="Een resource maken in de Azure-portal":::
   
3. Selecteer op de pagina **Nieuw** **Databases** > **Azure Cosmos DB**.
   
   :::image type="content" source="./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-1.png" alt-text="Het deelvenster Databases in de Azure-portal":::
   
3. Voer op de pagina **Azure Cosmos DB-account maken** de instellingen in voor het nieuwe Azure Cosmos DB-account. 
 
    Instelling|Waarde|Beschrijving
    ---|---|---
    Abonnement|Uw abonnement|Selecteer het Azure-abonnement dat u voor dit Azure Cosmos DB-account wilt gebruiken. 
    Resourcegroep|**Nieuwe maken** en vervolgens Accountnaam|Selecteer **Nieuw maken**. Voer daarna een nieuwe resourcegroepnaam in voor het account. Gebruik dezelfde naam als uw Azure Cosmos DB-accountnaam om het uzelf gemakkelijk te maken. 
    Accountnaam|Een unieke naam|Voer een unieke naam in om uw Azure Cosmos DB-account te identificeren.<br><br>De accountnaam moet tussen de 3 en 31 tekens lang zijn en mag alleen kleine letters, cijfers en afbreekstreepjes bevatten.
    API|Tabel|De API bepaalt het type te maken account. Azure Cosmos DB heeft vijf API's: Core(SQL) voor documentdatabases, Gremlin voor grafiekdatabases, MongoDB voor documentdatabases, Azure Table en Cassandra. U moet voor elke API een afzonderlijk account maken. <br><br>Selecteer **Azure Table**, omdat u in deze snelstartgids een tabel maakt die geschikt is voor de Table-API. <br><br>[Meer informatie over de Table-API](../articles/cosmos-db/table-introduction.md).|
    Locatie|De regio het dichtst bij uw gebruikers|Selecteer een geografische locatie waar u het Azure Cosmos DB-account wilt hosten. Gebruik de locatie die zich het dichtst bij uw gebruikers bevindt, zodat ze de snelst mogelijke toegang tot de gegevens hebben.
    Capaciteitsmodus|Ingerichte doorvoer of serverloos|Selecteer **Ingerichte doorvoer** om een account te maken in de modus [Ingerichte doorvoer](../articles/cosmos-db/set-throughput.md). Selecteer **Serverloos** om een account te maken in de modus [serverloos](../articles/cosmos-db/serverless.md).

    U kunt de opties **Geo-redundantie** en **Meerdere regio's schrijven** in **Uitschakelen** laten om extra kosten te voorkomen, en de secties **Netwerk** en **Tags** overslaan.

5. Selecteer **Beoordelen en maken**. Selecteer **Maken** om het account te maken nadat de validatie voltooid is. 
 
   :::image type="content" source="./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-create-new-account.png" alt-text="De pagina Nieuw account voor Azure Cosmos DB":::

6. Het duurt een paar minuten om het account te maken. Er wordt een bericht weergegeven waarin staat dat **Uw implementatie wordt uitgevoerd**. Wacht tot de implementatie is voltooid en selecteer **Ga naar resource**.

    :::image type="content" source="./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-account-created.png" alt-text="Het deelvenster Meldingen in de Azure-portal":::

