---
title: bestand opnemen
description: bestand opnemen
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 01/27/2020
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 736ab630b035ae1a668332eb5ab5aa9530ef0e88
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95972419"
---
1. Meld u in een nieuw browservenster aan bij [Azure Portal](https://portal.azure.com/).

2. Selecteer **Een resource maken** in het menu aan de linkerkant.
   
   ![Een resource maken in Azure Portal](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-0.png)
   
3. Selecteer op de pagina **Nieuw** **Databases** > **Azure Cosmos DB**.
   
   ![Het deelvenster Databases in de Azure-portal](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-1.png)
   
   > [!TIP]
   > Als u Azure Cosmos DB niet in de lijst ziet, typt u het in het zoekvak bovenaan de pagina en drukt u op de _Enter_-toets. 

3. Voer op de pagina **Azure Cosmos DB-account maken** de instellingen in voor het nieuwe Azure Cosmos DB-account. 
 
    Instelling|Waarde|Beschrijving
    ---|---|---
    Abonnement|Uw abonnement|Selecteer het Azure-abonnement dat u voor dit Azure Cosmos DB-account wilt gebruiken. 
    Resourcegroep|Nieuwe maken<br><br>Voer vervolgens dezelfde naam in als de Accountnaam|Selecteer **Nieuw maken**. Voer daarna een nieuwe resourcegroepnaam in voor het account. Gebruik dezelfde naam als uw Azure Cosmos DB-accountnaam om het uzelf gemakkelijk te maken. 
    Accountnaam|Voer een unieke naam in|Voer een unieke naam in om uw Azure Cosmos DB-account te identificeren. Uw account-URI wordt *gremlin.azure.com* toegevoegd aan uw unieke accountnaam.<br><br>De accountnaam moet tussen de 3 en 31 tekens lang zijn en mag alleen kleine letters, cijfers en afbreekstreepjes bevatten.
    API|Gremlin (grafiek)|De API bepaalt het type te maken account. Azure Cosmos DB heeft vijf API's: Core(SQL) voor documentdatabases, Gremlin voor grafiekdatabases, MongoDB voor documentdatabases, Azure Table en Cassandra. U moet voor elke API een afzonderlijk account maken. <br><br>Selecteer **Gremlin (grafiek)** , omdat u in deze snelstartgids een tabel maakt die geschikt is voor de Gremlin-API. <br><br>[Meer informatie over de Gremlin API](../articles/cosmos-db/graph-introduction.md).|
    Locatie|Selecteer de regio het dichtst in de buurt van uw gebruikers|Selecteer een geografische locatie waar u het Azure Cosmos DB-account wilt hosten. Gebruik de locatie die zich het dichtst bij uw gebruikers bevindt, zodat ze de snelst mogelijke toegang tot de gegevens hebben.
    Capaciteitsmodus|Ingerichte doorvoer of serverloos|Selecteer **Ingerichte doorvoer** om een account te maken in de modus [Ingerichte doorvoer](../articles/cosmos-db/set-throughput.md). Selecteer **Serverloos** om een account te maken in de modus [serverloos](../articles/cosmos-db/serverless.md).

    Selecteer **Beoordelen en maken**. U kunt de secties **Netwerk** en **Tags** overslaan. 

    ![De pagina Nieuw account voor Azure Cosmos DB](./media/cosmos-db-create-dbaccount-graph/azure-cosmos-db-create-new-account.png)

4. Het duurt enkele minuten om het account te maken. Wacht tot de portal de pagina **Gefeliciteerd! Uw Azure Cosmos DB-account is gemaakt** weergeeft.
   
   ![Pagina Azure Cosmos DB-account aangemaakt](./media/cosmos-db-create-dbaccount-graph/azure-cosmos-db-graph-created.png)

