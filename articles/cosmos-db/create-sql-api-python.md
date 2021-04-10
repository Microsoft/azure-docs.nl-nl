---
title: 'Quickstart: een Python-app maken met behulp van een SQL API-account van Azure Cosmos DB'
description: Is een Python-codevoorbeeld dat u kunt gebruiken om verbinding te maken met en gegevens op te vragen uit de SQL API van Azure Cosmos DB
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: quickstart
ms.date: 09/22/2020
ms.author: anfeldma
ms.custom:
- seodec18
- seo-javascript-september2019
- seo-python-october2019
- devx-track-python
ms.openlocfilehash: fee0591622c1ee07b6e954b3cadc208a300ab6a5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104798779"
---
# <a name="quickstart-build-a-python-application-using-an-azure-cosmos-db-sql-api-account"></a>Quickstart: Een Python-app maken met behulp van een SQL API-account van Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK v4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

In deze snelstart maakt en beheert u een SQL API-account van Azure Cosmos DB via de Azure-portal en via Visual Studio Code met een Python-app die is gekloond van GitHub. Met Azure Cosmos DB, een databaseservice met meerdere modellen, kunt u snel databases met documenten, tabellen, sleutelwaarden en grafieken maken en hier query's op uitvoeren. Deze databases hebben wereldwijde distributie en horizontale schaalmogelijkheden.

## <a name="prerequisites"></a>Vereisten

- Een Cosmos DB-account. U hebt de volgende opties:
    * Met een actief Azure-abonnement:
        * [Maak een gratis Azure-account](https://azure.microsoft.com/free) of gebruik uw bestaande abonnement 
        * [Maandelijkse tegoeden Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers)
        * [Gratis laag in Azure Cosmos DB](./optimize-dev-test.md#azure-cosmos-db-free-tier)
    * Zonder een actief Azure-abonnement:
        * [Probeer Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/), een testomgeving gedurende 30 dagen.
        * [Azure Cosmos DB Emulator](https://aka.ms/cosmosdb-emulator) 
- [Python 2,7 of 3.6 +](https://www.python.org/downloads/), met het `python` uitvoer bare bestand in uw `PATH` .
- [Visual Studio Code](https://code.visualstudio.com/).
- De [Python-extensie voor Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python#overview).
- [Git](https://www.git-scm.com/downloads). 
- [Azure Cosmos DB SQL API SDK voor Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)

## <a name="create-a-database-account"></a>Een databaseaccount maken

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Een container toevoegen

U kunt nu het hulpprogramma Data Explorer in de Azure-portal gebruiken om een database en een container te maken. 

1. Selecteer **Data Explorer** > **Nieuwe container**. 
    
    Uiterst rechts wordt het gedeelte **Container toevoegen** weergegeven. Mogelijk moet u naar rechts scrollen om het te kunnen zien.

    :::image type="content" source="./media/create-sql-api-python/azure-cosmosdb-data-explorer.png" alt-text="Data Explorer in Azure Portal, het deelvenster Container toevoegen":::

2. Geef op de pagina **Container toevoegen** de instellingen voor de nieuwe container op.

    |Instelling|Voorgestelde waarde|Beschrijving
    |---|---|---|
    |**Database-id**|Taken|Voer *Taken* in als de naam voor de nieuwe database. Databasenamen moeten tussen de 1 en 255 tekens zijn en mogen geen `/, \\, #, ?` bevatten en mogen niet eindigen met een spatie. Schakel de optie **Doorvoer voor databases inrichten** in, zodat u de doorvoer die is ingericht voor de database, kunt delen in alle containers in de database. Met deze optie bespaart u bovendien op de kosten. |
    |**Doorvoer**|400|Wijzig de doorvoer in 400 aanvraageenheden per seconde (RU/s). U kunt de doorvoer later opschalen als u de latentie wilt beperken.| 
    |**Container-id**|Items|Voer *Items* in als de naam voor de nieuwe container. Voor id's van containers gelden dezelfde tekenvereisten als voor databasenamen.|
    |**Partitiesleutel**| /category| In het voorbeeld dat in dit artikel wordt beschreven, wordt */category* als de partitiesleutel gebruikt.|
    
    Naast de voornoemde instellingen kunt u indien gewenst **unieke sleutels** voor de container toevoegen. In dit voorbeeld laten we het veld leeg. Unieke sleutels bieden ontwikkelaars de mogelijkheid om een gegevensintegriteitslaag aan de database toe te voegen. Door een beleid met unieke sleutels te maken als u een container maakt, zorgt u ervoor dat een of meer waarden per partitiesleutel uniek zijn. Raadpleeg het artikel [Unique keys in Azure Cosmos DB](unique-keys.md) (Unieke sleutels in Azure Cosmos DB) voor meer informatie.
    
    Selecteer **OK**. In Data Explorer worden de nieuwe database en container weergegeven.

## <a name="add-sample-data"></a>Voorbeeldgegevens toevoegen

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Uw gegevens opvragen

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

We gaan nu een SQL API-app klonen vanuit GitHub, de verbindingsreeks instellen en de app uitvoeren. Deze snelstart maakt gebruik van versie 4 van de [Python SDK](https://pypi.org/project/azure-cosmos/#history).

1. Open een opdrachtprompt, maak een nieuwe map met de naam git-samples en sluit vervolgens de opdrachtprompt.

    ```cmd
    md "git-samples"
    ```
   Als u een Bash-prompt gebruikt, moet u in plaats daarvan de volgende opdracht gebruiken:

   ```bash
   mkdir "git-samples"
   ```

2. Open een git-terminalvenster, bijvoorbeeld git bash, en gebruik de `cd`-opdracht om naar de nieuwe map te gaan voor het installeren van de voorbeeld-app.

    ```bash
    cd "git-samples"
    ```

3. Voer de volgende opdracht uit om de voorbeeldopslagplaats te klonen. Deze opdracht maakt een kopie van de voorbeeld-app op uw computer. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-python-getting-started.git
    ```  

## <a name="update-your-connection-string"></a>Uw verbindingsreeks bijwerken

Ga nu terug naar Azure Portal om de verbindingsreeksinformatie op te halen en kopieer deze in de app.

1. Selecteer in uw Azure Cosmos DB-account in de [Azure-portal](https://portal.azure.com/) de optie **Sleutels** in het linkernavigatiegedeelte. Gebruik in de volgende stap de kopieerknoppen aan de rechterkant van het scherm om de **URI** en **Primaire sleutel** in het bestand *cosmos_get_started.py* te kopiëren.

    :::image type="content" source="./media/create-sql-api-dotnet/access-key-and-uri-in-keys-settings-in-the-azure-portal.png" alt-text="Een toegangssleutel en URI in de sleutelinstellingen in Azure Portal ophalen":::

2. Open het bestand *cosmos_get_started.py* in *\git-samples\azure-cosmos-db-python-getting-started* in Visual Studio Code.

3. Kopieer uw **URI**-waarde via de portal (met de kopieerknop) en geef deze als waarde aan de **eindpunt** variabele in *cosmos_get_started.py*. 

    `endpoint = 'https://FILLME.documents.azure.com',`

4. Kopieer vervolgens de waarde van uw **PRIMAIRE SLEUTEL** via de portal en geef deze als waarde aan de **sleutel** in *cosmos_get_started.py*. U hebt uw app nu bijgewerkt met alle informatie die nodig is voor de communicatie met Azure Cosmos DB. 

    `key = 'FILLME'`

5. Sla het bestand *cosmos_get_started.py* op.

## <a name="review-the-code"></a>De code bekijken

Deze stap is optioneel. Lees meer over de databaseresources die zijn gemaakt in code of sla dit over en ga naar [Uw verbindingsreeks bijwerken](#update-your-connection-string).

De volgende codefragmenten zijn allemaal afkomstig uit het bestand *cosmos_get_started.py*.

* De CosmosClient wordt geïnitialiseerd. Zorg ervoor dat u de waarden voor 'Eindpunt' en 'sleutel' bijwerkt zoals is beschreven in het gedeelte [Uw verbindingsreeks bijwerken](#update-your-connection-string). 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_cosmos_client)]

* Er wordt een nieuwe database gemaakt.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_database_if_not_exists)]

* Er wordt een nieuwe container gemaakt, met 400 RU/s aan [ingerichte doorvoer](request-units.md). We kiezen `lastName` als de [partitiesleutel](partitioning-overview.md#choose-partitionkey), waarmee we efficiënte query's kunnen uitvoeren op deze eigenschap. 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_container_if_not_exists)]

* Bepaalde items worden toegevoegd aan de container. Containers zijn een verzameling van items (JSON-documenten) die een gevarieerd schema kunnen hebben. De helpermethoden ```get_[name]_family_item``` retourneren weergaven van een familie die als JSON-documenten in Azure Cosmos DB zijn opgeslagen.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_item)]

* Puntleesbewerkingen (zoekacties voor sleutelwaarden) worden uitgevoerd met behulp van de `read_item`-methode. De [RU-kosten](request-units.md) van elke bewerking worden afgedrukt.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=read_item)]

* Een query wordt uitgevoerd met behulp van de SQL-querysyntaxis. Omdat we de partitiesleutelwaarden van ```lastName``` in de component WHERE gebruiken, wordt deze query op efficiënte wijze door Azure Cosmos DB naar de relevante partities gerouteerd, waardoor de prestaties worden verbeterd.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=query_items)]
   
## <a name="run-the-app"></a>De app uitvoeren

1. Selecteer **View** > **Command Palette** in Visual Studio Code. 

2. Voer na de prompt **Python in: Selecteer Interpreter** in en selecteer de te gebruiken Python-versie.

    De voettekst in Visual Studio Code wordt bijgewerkt om de geselecteerde interpreter aan te geven. 

3. Selecteer **View** > **Integrated Terminal** om de in Visual Studio Code geïntegreerde terminal te openen.

4. Controleer of u zich in het venster van de geïntegreerde terminal in de map *azure-cosmos-db-python-getting-started* bevindt. Zo niet, voer dan de volgende opdracht uit om naar de voorbeeldmap over te schakelen. 

    ```cmd
    cd "\git-samples\azure-cosmos-db-python-getting-started"`
    ```

5. Voer de volgende opdracht uit om het azure-cosmos-pakket te installeren. 

    ```python
    pip install --pre azure-cosmos
    ```

    Als u een foutmelding krijgt over geweigerde toegang bij het installeren van azure-cosmos, dient u [VS Code als beheerder uit te voeren](https://stackoverflow.com/questions/37700536/visual-studio-code-terminal-how-to-run-a-command-with-administrator-rights).

6. Voer de volgende opdracht uit om het voorbeeld uit te voeren en nieuwe documenten in Azure Cosmos DB te maken en op te slaan.

    ```python
    python cosmos_get_started.py
    ```

7. Als u wilt bevestigen dat de nieuwe items zijn gemaakt en opgeslagen, selecteert u **Data Explorer** > **AzureSampleFamilyDatabase** > **Items** in de Azure-portal. Bekijk de items die zijn gemaakt. Hier is bijvoorbeeld een voorbeeld van een JSON-document voor de familie Andersen:
   
   ```json
   {
       "id": "Andersen-1569479288379",
       "lastName": "Andersen",
       "district": "WA5",
       "parents": [
           {
               "familyName": null,
               "firstName": "Thomas"
           },
           {
               "familyName": null,
               "firstName": "Mary Kay"
           }
       ],
       "children": null,
       "address": {
           "state": "WA",
           "county": "King",
           "city": "Seattle"
       },
       "registered": true,
       "_rid": "8K5qAIYtZXeBhB4AAAAAAA==",
       "_self": "dbs/8K5qAA==/colls/8K5qAIYtZXc=/docs/8K5qAIYtZXeBhB4AAAAAAA==/",
       "_etag": "\"a3004d78-0000-0800-0000-5d8c5a780000\"",
       "_attachments": "attachments/",
       "_ts": 1569479288
   }
   ```

## <a name="review-slas-in-the-azure-portal"></a>SLA’s bekijken in Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u geleerd hoe u een Azure Cosmos DB-account maakt, een container maakt met Data Explorer en een Python-app in Visual Studio Code uitvoert. Nu kunt u aanvullende gegevens in uw Azure Cosmos DB-account importeren. 

> [!div class="nextstepaction"]
> [Gegevens importeren in Azure Cosmos DB voor de SQL API](import-data.md)
