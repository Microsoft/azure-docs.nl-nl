---
title: 'Quickstart: Azure Cosmos DB-resources maken vanuit de Azure-portal'
description: In deze quickstart kunt u zien hoe u een Azure Cosmos-database, -container en -items kunt maken met de Azure-portal.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 11/10/2020
ms.openlocfilehash: 041b930a0c2fa82727216b4ca0c8367387d0d801
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100367249"
---
# <a name="quickstart-create-an-azure-cosmos-account-database-container-and-items-from-the-azure-portal"></a>Quickstart: Een Azure Cosmos-account, -database, -container en -items maken vanuit de Azure-portal
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Azure-portal](create-cosmosdb-resources-portal.md)
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

Azure Cosmos DB is de wereldwijd gedistribueerde multimodel-databaseservice van Microsoft. Met Azure Cosmos DB kunt u snel databases maken van sleutel/waarde-paren, documenten en grafieken en hier query’s op uitvoeren. Deze databases hebben allemaal het voordeel van de globale distributie en horizontale schaalmogelijkheden die ten grondslag liggen aan Azure Cosmos DB. 

In deze quickstart ziet u hoe u met de Microsoft Azure-portal een [SQL API](./introduction.md)-account van Azure Cosmos DB, een documentdatabase en een container kunt maken en gegevens kunt toevoegen aan de container. 

## <a name="prerequisites"></a>Vereisten

Een Azure-abonnement of gratis Azure Cosmos DB-proefaccount
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-an-azure-cosmos-db-account"></a>Maak een Azure Cosmos DB-account

Ga naar de [Azure-portal](https://portal.azure.com/) om een Azure Cosmos DB-account aan te maken. Zoek en selecteer **Azure Cosmos DB**.

   :::image type="content" source="./media/create-cosmosdb-resources-portal/find-nosql-cosmosdb-marketplace.png" alt-text="Het deelvenster Databases in Azure Portal":::

1. Selecteer **Toevoegen**.
1. Voer op de pagina **Azure Cosmos DB-account maken** de basisinstellingen in voor het nieuwe Azure Cosmos-account. 

    |Instelling|Waarde|Beschrijving |
    |---|---|---|
    |Abonnement|Abonnementsnaam|Selecteer het Azure-abonnement dat u voor dit Azure Cosmos-account wilt gebruiken. |
    |Resourcegroep|Naam van de resourcegroep|Selecteer een resourcegroep of selecteer **Nieuwe maken** en voer vervolgens een unieke naam in voor de nieuwe resourcegroep. |
    |Accountnaam|Een unieke naam|Voer een naam in om uw Azure Cosmos-account te identificeren. Gebruik een unieke naam omdat *documents.azure.com* is toegevoegd aan de naam die u hebt opgegeven om uw URI te maken.<br><br>De naam mag alleen kleine letters, cijfers en het koppelteken (-) bevatten. De naam moet tussen de 3 en 31 tekens lang zijn.|
    |API|Het type account dat moet worden gemaakt|Selecteer **Core(SQL)** om een documentdatabase en query's aan te maken met SQL-syntaxis. <br><br>De API bepaalt het type te maken account. Azure Cosmos DB heeft vijf API's: Core (SQL) en MongoDB voor documentgegevens, Gremlin voor grafiekgegevens, Azure Table en Cassandra. Op dit moment moet u voor elke API een afzonderlijk account maken. <br><br>[Meer informatie over de SQL-API](introduction.md).|
    |Capaciteitsmodus|Ingerichte doorvoer of serverloos|Selecteer **Ingerichte doorvoer** om een account te maken in de modus [Ingerichte doorvoer](set-throughput.md). Selecteer **Serverloos** om een account te maken in de modus [serverloos](serverless.md).|
    |Niveaukorting op gratis laag van Azure Cosmos DB toepassen|Toepassen of niet toepassen|Met de gratis laag van Azure Cosmos DB ontvangt u de eerste 400 RU/s en 5 GB aan opslagruimte gratis in een account. Meer informatie over de [gratis laag](https://azure.microsoft.com/pricing/details/cosmos-db/).|
    |Locatie|De regio het dichtst bij uw gebruikers|Selecteer een geografische locatie waar u het Azure Cosmos DB-account wilt hosten. Gebruik de locatie die zich het dichtst bij uw gebruikers bevindt, zodat ze de snelst mogelijke toegang tot de gegevens hebben.|
    |Accounttype|Productie of niet-productie|Selecteer **Productie** als het account wordt gebruikt voor een productie-werkbelasting. Selecteer **Niet-productie** als het account wordt gebruikt voor niet-productie, zoals ontwikkeling, testing, QA of fasering. Dit is een resourcetag-instelling in Azure die de portal-ervaring afstemt, maar geen invloed heeft op het onderliggende Azure Cosmos DB-account. U kunt deze waarde op elk gewenst moment wijzigen.|
    |Geografische redundantie|In- of uitschakelen|Schakel globale distributie voor uw account in of uit door uw regio te koppelen met een koppelingsregio. U kunt later meer regio's aan uw account toevoegen.|
    |Schrijfbewerkingen in meerdere regio's|In- of uitschakelen|Dankzij de mogelijkheid voor schrijfbewerkingen in meerdere regio's kunt over de hele wereld profiteren van de ingerichte doorvoer voor uw databases en containers.|
    |Beschikbaarheidszones|In- of uitschakelen|Met beschikbaarheidszones kunt u de beschikbaarheid en tolerantie van uw toepassing verder verbeteren.|

> [!NOTE]
> U kunt per Azure-abonnement maximaal één gratis laag voor het Azure Cosmos DB-account hebben, en u moet zich aanmelden wanneer u het account maakt. Als u de optie voor het toepassen van de korting voor gratis lagen niet ziet, betekent dit dat er al een ander account in het abonnement is ingeschakeld met een gratis laag.

> [!NOTE]
> De volgende opties zijn niet beschikbaar als u **Serverloos** als **Capaciteitsmodus** selecteert:
> - Korting voor gratis lagen toepassen
> - Geografische redundantie
> - Schrijven voor meerdere regio's
   
   :::image type="content" source="./media/create-cosmosdb-resources-portal/azure-cosmos-db-create-new-account-detail-2.png" alt-text="De pagina Nieuw account voor Azure Cosmos DB":::

1. Selecteer **Controleren + maken**. U kunt de secties **Netwerk** en **Tags** overslaan.

1. Controleer de accountinstellingen en selecteer vervolgens **Maken**. Het duurt een paar minuten om het account te maken. Wacht tot de portal-pagina **Uw implementatie is voltooid** weergeeft. 

    :::image type="content" source="./media/create-cosmosdb-resources-portal/azure-cosmos-db-account-deployment-successful.png" alt-text="Het deelvenster Meldingen in Azure Portal":::

1. Selecteer **Ga naar resource** om naar de Azure Cosmos DB-accountpagina te gaan. 

    :::image type="content" source="./media/create-cosmosdb-resources-portal/azure-cosmos-db-account-quickstart-pane.png" alt-text="De Azure Cosmos DB-accountpagina":::

<a id="create-container-database"></a>
## <a name="add-a-database-and-a-container"></a>Een database en een container toevoegen 

U kunt Data Explorer in de Azure-portal gebruiken om een database en een container te maken. 

1.  Selecteer **Data Explorer** op de linkernavigatiebalk op uw accountpagina van Azure Cosmos DB en selecteer **Nieuwe container**. 
    
    Mogelijk moet u naar rechts schuiven om het venster **Container toevoegen** te zien.
    
    :::image type="content" source="./media/create-sql-api-dotnet/azure-cosmosdb-data-explorer-dotnet.png" alt-text="Data Explorer in Azure Portal, het deelvenster Container toevoegen":::
    
1.  Geef in het deelvenster **Container toevoegen** de instellingen voor de nieuwe container op.
    
    |Instelling|Voorgestelde waarde|Beschrijving
    |---|---|---|
    |**Database-id**|Takenlijst|Voer *Takenlijst* in als de naam voor de nieuwe database. Databasenamen moeten tussen de 1 en 255 tekens zijn en mogen geen `/, \\, #, ?` bevatten en mogen niet eindigen met een spatie. Schakel de optie **Doorvoer voor databases inrichten** in, zodat u de doorvoer die is ingericht voor de database, kunt delen in alle containers in de database. Met deze optie bespaart u bovendien op de kosten. |
    |**Doorvoer**|400|Wijzig de doorvoer in 400 aanvraageenheden per seconde (RU/s). U kunt de doorvoer later opschalen als u de latentie wilt beperken.<br><br>**Opmerking**: Deze instelling is niet beschikbaar bij het maken van een nieuwe container in een serverloos account.| 
    |**Container-id**|Items|Voer *Items* in als de naam voor de nieuwe container. Voor id's van containers gelden dezelfde tekenvereisten als voor databasenamen.|
    |**Partitiesleutel**| /category| In het voorbeeld dat in dit artikel wordt beschreven, wordt */category* als de partitiesleutel gebruikt.|

    
    Voeg geen **unieke sleutels** toe voor dit voorbeeld. Met unieke sleutels kunt u een laag van gegevensintegriteit toevoegen aan de database door de uniekheid van een of meer waarden per partitiesleutel te garanderen. Zie [Unieke sleutels in Azure Cosmos DB](unique-keys.md) voor meer informatie.
    
1.  Selecteer **OK**. In Data Explorer worden de nieuwe database en container weergegeven die u hebt gemaakt.

## <a name="add-data-to-your-database"></a>Gegevens toevoegen aan uw database

Voeg gegevens aan uw nieuwe database toe met behulp van Data Explorer.

1. Vouw in **Data Explorer** de database **Takenlijst** uit en vouw de container **Items** uit. Selecteer vervolgens **Items** en selecteer **Nieuw item**. 
   
   :::image type="content" source="./media/create-sql-api-dotnet/azure-cosmosdb-new-document.png" alt-text="Nieuwe documenten maken in Data Explorer in de Azure-portal":::
   
1. Voeg de volgende structuur toe aan het document aan de rechterkant van het deelvenster **Documenten**:

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

1. Selecteer **Opslaan**.
   
   :::image type="content" source="./media/create-sql-api-dotnet/azure-cosmosdb-save-document.png" alt-text="JSON-gegevens kopiëren en Opslaan selecteren in Data Explorer in de Azure-portal":::
   
1. Selecteer opnieuw **Nieuw document**, maak nog een document en sla dit op met een unieke `id` en andere eigenschappen en waarden die u wilt instellen. De documenten kunnen elke gewenste structuur hebben, omdat in Azure Cosmos DB uw gegevens geen schema krijgen opgelegd.

## <a name="query-your-data"></a>Uw gegevens opvragen

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)] 

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

Als u alleen de database wilt verwijderen en het Azure Cosmos-account in de toekomst wilt gebruiken, kunt u de database verwijderen door de volgende stappen uit te voeren:

* Ga naar uw Azure Cosmos-account.
* Open **Data Explorer**, klik met de rechtermuisknop op de database die u wilt verwijderen en selecteer **Database verwijderen**.
* Voer de database-id/databasenaam in om de verwijderbewerking te bevestigen. 

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een Azure Cosmos DB-account maakt en een database en container maakt met Data Explorer. Nu kunt u aanvullende gegevens in uw Azure Cosmos DB-account importeren. 

> [!div class="nextstepaction"]
> [Gegevens importeren in Azure Cosmos DB](import-data.md)