---
title: bestand opnemen
description: bestand opnemen
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 10/01/2020
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 30a7f3ae878cebcd1e58287fc59241651dac2bfd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "94503894"
---
1. Meld u in een nieuw browservenster aan bij [Azure Portal](https://portal.azure.com/).

2. Selecteer **Een resource maken** in het menu aan de linkerkant.
   
   ![Een resource maken in Azure Portal](./media/cosmos-db-create-dbaccount-cassandra/create-nosql-db-databases-json-tutorial-0.png)
   
3. Selecteer op de pagina **Nieuw** **Databases** > **Azure Cosmos DB**.
   
   ![Het deelvenster Databases in de Azure-portal](./media/cosmos-db-create-dbaccount-cassandra/create-nosql-db-databases-json-tutorial-1.png)
   
3. Voer op de pagina **Azure Cosmos DB-account maken** de instellingen in voor het nieuwe Azure Cosmos DB-account. 
 
    Instelling|Waarde|Beschrijving
    ---|---|---
    Abonnement|Uw abonnement|Selecteer het Azure-abonnement dat u voor dit Azure Cosmos DB-account wilt gebruiken. 
    Resourcegroep|Nieuwe maken<br><br>Voer vervolgens dezelfde naam in als de Accountnaam|Selecteer **Nieuw maken**. Voer daarna een nieuwe resourcegroepnaam in voor het account. Gebruik dezelfde naam als uw Azure Cosmos-accountnaam om het uzelf gemakkelijk te maken. 
    Accountnaam|Voer een unieke naam in|Voer een unieke naam in om uw Azure Cosmos DB-account te identificeren. Uw account-URI wordt *cassandra.cosmos.azure.com* dat aan uw unieke accountnaam wordt toegevoegd.<br><br>De accountnaam moet tussen de 3 en 31 tekens lang zijn en mag alleen kleine letters, cijfers en afbreekstreepjes bevatten.
    API|Cassandra|De API bepaalt het type te maken account. Azure Cosmos DB heeft vijf API's: Core(SQL) voor documentdatabases, Gremlin voor grafiekdatabases, MongoDB voor documentdatabases, Azure Table en Cassandra. U moet voor elke API een afzonderlijk account maken. <br><br>Selecteer **Cassandra**, omdat u in deze snelstartgids een tabel maakt die geschikt is voor de Cassandra-API. <br><br>[Meer informatie over de Cassandra-API](../articles/cosmos-db/cassandra-introduction.md).|
    Locatie|Selecteer de regio het dichtst in de buurt van uw gebruikers|Selecteer een geografische locatie waar u het Azure Cosmos DB-account wilt hosten. Gebruik de locatie die zich het dichtst bij uw gebruikers bevindt, zodat ze de snelst mogelijke toegang tot de gegevens hebben.
    Capaciteitsmodus|Ingerichte doorvoer of serverloos|Selecteer **Ingerichte doorvoer** om een account te maken in de modus [Ingerichte doorvoer](../articles/cosmos-db/set-throughput.md). Selecteer **Serverloos** om een account te maken in de modus [serverloos](../articles/cosmos-db/serverless.md).

    Selecteer **Beoordelen en maken**. U kunt de sectie **Netwerken**, **Back-up**, **Versleuteling** en **Tags** overslaan. 

    ![De pagina Nieuw account voor Azure Cosmos DB](./media/cosmos-db-create-dbaccount-cassandra/azure-cosmos-db-create-new-account.png)

4. Het duurt enkele minuten om het account te maken. Wacht tot in de portal de pagina **Gefeliciteerd! Uw Azure Cosmos DB-account is gemaakt** wordt weergegeven.

