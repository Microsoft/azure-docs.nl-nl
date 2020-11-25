---
title: Een Python Flask-web-app maken met de API van Azure Cosmos DB voor MongoDB
description: Biedt een voorbeeld van Python Flask-code dat u kunt gebruiken om verbinding te maken met de Azure Cosmos DB-API's voor MongoDB en er query's op uit te voeren.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: python
ms.topic: quickstart
ms.date: 12/26/2018
ms.custom: devx-track-python
ms.openlocfilehash: 58f22a335f4c619a6348e9e127e60f5a79f658b2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994543"
---
# <a name="quickstart-build-a-python-app-using-azure-cosmos-dbs-api-for-mongodb"></a>Quickstart: Een Python-app maken met de API van Azure Cosmos DB voor MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

In deze quickstart gebruikt u een Azure Cosmos DB voor MongoDB-API-account of het Azure Cosmos DB Emulator-account om een Python Flask To Do-web-app uit te voeren die is gekloond uit GitHub. Met Azure Cosmos DB, een databaseservice met meerdere modellen, kunt u snel databases met documenten, tabellen, sleutelwaarden en grafieken maken en hier query's op uitvoeren. Deze databases hebben wereldwijde distributie en horizontale schaalmogelijkheden.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Of [probeer Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) zonder Azure-abonnement. Of u kunt de [Azure Cosmos DB Emulator](local-emulator.md) gebruiken. 
- [Python 3.6+](https://www.python.org/downloads/)
- [Visual Studio Code](https://code.visualstudio.com/Download) met de [Python-extensie](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python).

## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

We gaan nu een Flask MongoDB-app klonen vanaf GitHub, de verbindingsreeks instellen en de app uitvoeren. U zult zien hoe gemakkelijk het is om op een programmatische manier met gegevens te werken.

1. Open een opdrachtprompt, maak een nieuwe map met de naam git-samples en sluit vervolgens de opdrachtprompt.

    ```bash
    md "C:\git-samples"
    ```

2. Open een git-terminalvenster, bijvoorbeeld git bash, en gebruik de `cd`-opdracht om naar de nieuwe map te gaan voor het installeren van de voorbeeld-app.

    ```bash
    cd "C:\git-samples"
    ```

3. Voer de volgende opdracht uit om de voorbeeldopslagplaats te klonen. Deze opdracht maakt een kopie van de voorbeeld-app op uw computer.

    ```bash
    git clone https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample.git
    ```
3. Voer de volgende opdracht uit om de Python-modules te installeren.

    ```bash 
    pip install -r .\requirements.txt
    ```
4. Open de map in Visual Studio Code.

## <a name="review-the-code"></a>De code bekijken

Deze stap is optioneel. Als u wilt weten hoe de databaseresources in de code worden gemaakt, kunt u de volgende codefragmenten bekijken. Sla dit anders over en ga naar [De web-app uitvoeren](#run-the-web-app). 

De volgende codefragmenten zijn allemaal afkomstig uit het bestand *app.py* en gebruiken de verbindingsreeks voor de lokale Azure Cosmos DB Emulator. Het wachtwoord moet worden opgesplitst, zoals hieronder wordt weergegeven, omdat de slashes anders niet kunnen worden geparseerd.

* Initialiseer de MongoDB-client, haal de database op en doorloop de verificatie.

    ```python
    client = MongoClient("mongodb://127.0.0.1:10250/?ssl=true") #host uri
    db = client.test    #Select the database
    db.authenticate(name="localhost",password='C2y6yDjf5' + r'/R' + '+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw' + r'/Jw==')
    ```

* Haal de verzameling op of maak er een als er nog geen is.

    ```python
    todos = db.todo #Select the collection
    ```

* De app maken

    ```Python
    app = Flask(__name__)
    title = "TODO with Flask"
    heading = "ToDo Reminder"
    ```
    
## <a name="run-the-web-app"></a>De web-app uitvoeren

1. Controleer of de Azure Cosmos DB-emulator wordt uitgevoerd.

2. Open een terminalvenster en ga met `cd` naar de map waarin de app wordt opgeslagen.

3. Stel vervolgens de omgevingsvariabele voor de Flask-app in met `set FLASK_APP=app.py`, `$env:FLASK_APP = app.py` voor PowerShell-editors, of `export FLASK_APP=app.py` als u een Mac gebruikt. 

4. Voer de app uit met `flask run` en blader naar *http:\//127.0.0.1:5000/* .

5. Voeg taken toe en verwijder ze, en kijk hoe ze zijn toegevoegd en gewijzigd in de verzameling.

## <a name="create-a-database-account"></a>Een databaseaccount maken

Als u wilt testen hoe de code met een actief Azure Cosmos DB-account werkt, gaat u naar de Azure-portal om een account te maken.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>Uw verbindingsreeks bijwerken

Als u de code wilt testen in het liveaccount van Azure Cosmos DB, haalt u de verbindingsreeksgegevens op. Kopieer deze vervolgens in de app.

1. Selecteer in uw Azure Cosmos DB-account in de Azure-portal, in de linkernavigatie, de optie **Verbindingsreeks**. Selecteer vervolgens **Sleutels voor lezen/schrijven**. Gebruik de kopieerknoppen aan de rechterkant van het scherm om gebruikersnaam, verbindingsreeks en wachtwoord te kopiëren. 

2. Open het bestand *app.py* in de hoofdmap.

3. Kopieer de waarde van **gebruikersnaam** van de portal (gebruik hiervoor de kopieerknop) en maak hiervan de waarde van **gebruikersnaam** in het bestand *app.py*.

4. Kopieer de waarde van **verbindingsreeks** van de portal en maak hiervan de waarde van de **MongoClient** in het bestand *app.py*.

5. Kopieer ten slotte de waarde van **wachtwoord** van de portal en maak hiervan de waarde van **wachtwoord** in het bestand *app.py*.

U hebt uw app nu bijgewerkt met alle informatie die nodig is voor de communicatie met Azure Cosmos DB. U kunt deze nu net als voorheen uitvoeren.

## <a name="deploy-to-azure"></a>Implementeren in Azure

Als u deze app wilt implementeren, kunt u een nieuwe web-app maken in Azure en continue implementatie inschakelen met een fork van deze GitHub-opslagplaats. Volg deze [zelfstudie](../app-service/deploy-continuous-deployment.md) voor het instellen van continue implementatie met GitHub in Azure.

Wanneer u Azure implementeert, moet u de sleutels van uw toepassing verwijderen en controleren of de onderstaande sectie als opmerking is opgenomen:

```python
    client = MongoClient(os.getenv("MONGOURL"))
    db = client.test    #Select the database
    db.authenticate(name=os.getenv("MONGO_USERNAME"),password=os.getenv("MONGO_PASSWORD"))
```

Vervolgens moet u uw MONGOURL, MONGO_PASSWORD en MONGO_USERNAME toevoegen aan de toepassingsinstellingen. In deze [zelfstudie](../app-service/configure-common.md#configure-app-settings) vindt u meer informatie over toepassingsinstellingen in Azure Web Apps.

Als u van deze opslagplaats geen fork wilt maken, kunt u ook de knop **Implementeren naar Azure** hieronder selecteren. Vervolgens gaat u naar Azure en stelt u daar de toepassingsinstellingen in op basis van de gegevens van uw Azure Cosmos DB-account.

<a href="https://deploy.azure.com/?repository=https://github.com/heatherbshapiro/To-Do-List---Flask-MongoDB-Example" target="_blank">
<img src="https://azuredeploy.net/deploybutton.png" alt="Click to Deploy to Azure">
</a>

> [!NOTE]
> Als u van plan bent uw code in GitHub of andere opties voor bronbeheer op te slaan, moet u uw verbindingsreeksen uit de code verwijderen. Ze kunnen ook worden ingesteld met toepassingsinstellingen voor de web-app.

## <a name="review-slas-in-the-azure-portal"></a>SLA’s bekijken in Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een Azure Cosmos DB voor MongoDB-API-account maakt en het Azure Cosmos DB Emulator-account gebruikt om een Python Flask To Do-web-app uit te voeren die is gekloond uit GitHub. Nu kunt u aanvullende gegevens in uw Azure Cosmos DB-account importeren. 

> [!div class="nextstepaction"]
> [MongoDB-gegevens importeren in Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%252fazure%252fcosmos-db%252ftoc.json%253ftoc%253d%252fazure%252fcosmos-db%252ftoc.json)