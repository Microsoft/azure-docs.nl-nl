---
title: bestand opnemen
description: bestand opnemen
services: cosmos-db
ms.custom: include file
ms.openlocfilehash: ad4445cbea6553a7a96299e1276dbe8f3816e166
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994541"
---
1. Meld u in een nieuw browservenster aan bij [Azure Portal](https://portal.azure.com/).

2. Selecteer **Een resource maken** in het menu aan de linkerkant.
   
   ![Een resource maken in Azure Portal](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-0.png)
   
3. Selecteer op de pagina **Nieuw** **Databases** > **Azure Cosmos DB**.
   
   ![Het deelvenster Databases in de Azure-portal](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)
   
3. Voer op de pagina **Azure Cosmos DB-account maken** de instellingen in voor het nieuwe Azure Cosmos DB-account. 
 
    Instelling|Waarde|Beschrijving
    ---|---|---
    Abonnement|Uw abonnement|Selecteer het Azure-abonnement dat u voor dit Azure Cosmos DB-account wilt gebruiken. 
    Resourcegroep|Nieuwe maken<br><br>Voer vervolgens dezelfde naam in als de Accountnaam|Selecteer **Nieuw maken**. Voer daarna een nieuwe resourcegroepnaam in voor het account. Gebruik dezelfde naam als uw Azure Cosmos DB-accountnaam om het uzelf gemakkelijk te maken. 
    Accountnaam|Voer een unieke naam in|Voer een unieke naam in om uw Azure Cosmos DB-account te identificeren. Uw account-URI wordt *mongo.cosmos.azure.com* dat aan uw unieke accountnaam wordt toegevoegd.<br><br>De accountnaam moet tussen de 3 en 31 tekens lang zijn en mag alleen kleine letters, cijfers en afbreekstreepjes bevatten.
    API|Azure Cosmos DB voor Mongo DB-API|De API bepaalt het type te maken account. Azure Cosmos DB heeft vijf API's: Core (SQL) voor documentdatabases, Gremlin voor grafiekdatabases, Azure Cosmos DB voor Mongo DB-API voor documentdatabases, Azure Table en Cassandra. Op dit moment moet u voor elke API een afzonderlijk account maken. <br><br>Selecteer **Azure Cosmos DB voor Mongo DB-API** omdat u in deze quickstart een verzameling maakt die met MongoDB werkt.<br><br>[Meer informatie over de Azure Cosmos DB voor MongoDB-API](../articles/cosmos-db/mongodb-introduction.md).|
    Locatie|Selecteer de regio het dichtst in de buurt van uw gebruikers|Selecteer een geografische locatie waar u het Azure Cosmos DB-account wilt hosten. Gebruik de locatie die zich het dichtst bij uw gebruikers bevindt, zodat ze de snelst mogelijke toegang tot de gegevens hebben.|
    Capaciteitsmodus|Ingerichte doorvoer of serverloos|Selecteer **Ingerichte doorvoer** om een account te maken in de modus [Ingerichte doorvoer](../articles/cosmos-db/set-throughput.md). Selecteer **Serverloos** om een account te maken in de modus [serverloos](../articles/cosmos-db/serverless.md).<br><br>**Opmerking**: Alleen MongoDB API-versie 3.6 wordt ondersteund door serverloze accounts. Als u 3.2 als versie kiest, wordt het account in de ingerichte doorvoermodus gedwongen.

    Selecteer **Beoordelen en maken**. U kunt de secties **Netwerk** en **Tags** overslaan. 

    ![De pagina Nieuw account voor Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-create-new-account.png)

4. Het duurt enkele minuten om het account te maken. Wacht tot de portal de pagina **Gefeliciteerd! Uw Azure Cosmos DB voor MongoDB-API-account is gemaakt** wordt weergegeven.

    ![Het deelvenster Meldingen in Azure Portal](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-account-created.png)
