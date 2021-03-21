---
title: 'Quickstart: Een Node.js MongoDB-app verbinden met Azure Cosmos DB'
description: In deze snelstartgids wordt beschreven hoe u een bestaande MongoDB-app die is geschreven in Node.js verbindt met Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 05/21/2019
ms.custom: seo-javascript-september2019, seo-javascript-october2019, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 97b26e4b32c7ec1b81eb6227ac29b0574bf088fc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101659967"
---
# <a name="quickstart-migrate-an-existing-mongodb-nodejs-web-app-to-azure-cosmos-db"></a>Quickstart: Een bestaande MongoDB Node.js-web-app migreren naar Azure Cosmos DB 
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](./mongodb-introduction.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

In deze quickstart maakt en beheert u een Azure Cosmos DB voor MongoDB API-account met behulp van de Azure Cloud Shell en met een MEAN-app (MongoDB, Express, Angular en Node.js) die is gekloond van GitHub. Azure Cosmos DB is een databaseservice met meerdere modellen waarmee u snel document-, tabel-, sleutelwaarde- en grafiekdatabases kunt maken en er query's op kunt uitvoeren met globale distributie en horizontale schaalmogelijkheden.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] Of [probeer Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) zonder Azure-abonnement. U kunt ook de [Azure Cosmos DB Emulator](https://aka.ms/cosmosdb-emulator) gebruiken met de verbindingsreeks `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true`.

- [Node.js](https://nodejs.org/) en u moet bekend zijn met de basisbegrippen van Node.js.

- [Git](https://git-scm.com/downloads).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Voor dit artikel is versie 2.0 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.


## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

Voer de volgende opdrachten uit om de voorbeeldopslagplaats te klonen. Deze voorbeeldopslagplaats bevat de standaard [MEAN.js](https://meanjs.org/)-toepassing.

1. Open een opdrachtprompt, maak een nieuwe map met de naam git-samples en sluit vervolgens de opdrachtprompt.

    ```bash
    mkdir "C:\git-samples"
    ```

2. Open een git-terminalvenster, bijvoorbeeld git bash, en gebruik de `cd`-opdracht om naar de nieuwe map te gaan voor het installeren van de voorbeeld-app.

    ```bash
    cd "C:\git-samples"
    ```

3. Voer de volgende opdracht uit om de voorbeeldopslagplaats te klonen. Deze opdracht maakt een kopie van de voorbeeld-app op uw computer. 

    ```bash
    git clone https://github.com/prashanthmadi/mean
    ```

## <a name="run-the-application"></a>De toepassing uitvoeren

Deze MongoDB-app die is geschreven in Node.js, maakt verbinding met uw Azure Cosmos DB-database, die MongoDB-client ondersteunt. Het is met andere woorden duidelijk voor de toepassing dat de gegevens worden opgeslagen in een Azure Cosmos DB-database.

Installeer de vereiste pakketten en start de toepassing.

```bash
cd mean
npm install
npm start
```
Er wordt een poging gedaan om de toepassing te verbinden met een MongoDB-bron, wat mislukt. Sluit de toepassing wanneer de uitvoer het volgende retourneert: [MongoError: connect ECONNREFUSED 127.0.0.1:27017].

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Als u een geïnstalleerde Azure CLI gebruikt, meldt u zich aan bij uw Azure-abonnement met de opdracht [az login](/cli/azure/reference-index#az-login) en volgt u de instructies op het scherm. U kunt deze stap overslaan als u de Azure Cloud Shell gebruikt.

```azurecli
az login 
``` 
   
## <a name="add-the-azure-cosmos-db-module"></a>De Azure Cosmos DB-module toevoegen

Als u van een geïnstalleerde Azure CLI gebruikmaakt, controleert u of het onderdeel `cosmosdb` al is geïnstalleerd door de opdracht `az` uit te voeren. Als `cosmosdb` in de lijst met basisopdrachten staat, gaat u verder met de volgende opdracht. U kunt deze stap overslaan als u de Azure Cloud Shell gebruikt.

Als `cosmosdb` niet in de lijst met basisopdrachten staat, installeert u [Azure CLI](/cli/azure/install-azure-cli) opnieuw.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een [resourcegroep](../azure-resource-manager/management/overview.md) met de opdracht [az group create](/cli/azure/group#az-group-create). Een Azure-resourcegroep is een logische container waarin Azure-resources, zoals web-apps, databases en opslagaccounts, worden geïmplementeerd en beheerd. 

In het volgende voorbeeld wordt een resourcegroep gemaakt in de regio Europa - west. Kies een unieke naam voor de resourcegroep.

Als u van Azure Cloud Shell gebruikmaakt, selecteert u **Uitproberen**, volgt u de aanwijzingen op het scherm om u aan te melden en kopieert u de opdracht naar de opdrachtprompt.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

## <a name="create-an-azure-cosmos-db-account"></a>Maak een Azure Cosmos DB-account

Maak een Cosmos-account met de opdracht [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create).

Vervang in de volgende opdracht waar u de plaatsaanduiding `<cosmosdb-name>` ziet staan, de accountnaam met uw unieke Cosmos-accountnaam. Deze unieke naam wordt gebruikt als onderdeel van uw Cosmos DB-eindpunt (`https://<cosmosdb-name>.documents.azure.com/`). De naam moet daarom uniek zijn binnen alle Cosmos-accounts in Azure. 

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

De parameter `--kind MongoDB` maakt MongoDB-clientverbindingen mogelijk.

Wanneer de Azure Cosmos DB-account wordt gemaakt toont de Azure CLI informatie die lijkt op het volgende voorbeeld. 

> [!NOTE]
> In dit voorbeeld wordt JSON gebruikt als de Azure CLI-uitvoerindeling. Dit is standaardindeling. Zie [Output formats for Azure CLI commands](/cli/azure/format-output-azure-cli) (Uitvoerindelingen voor Azure CLI-opdrachten) als u een andere uitvoerindeling wilt gebruiken.

```json
{
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb-name>.documents.azure.com:443/",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Document
DB/databaseAccounts/<cosmosdb-name>",
  "kind": "MongoDB",
  "location": "West Europe",
  "name": "<cosmosdb-name>",
  "readLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ],
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "writeLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ]
} 
```

## <a name="connect-your-nodejs-application-to-the-database"></a>Uw Node.js-toepassing verbinden met de database

In deze stap verbindt u uw MEAN.js-voorbeeldtoepassing met het Azure Cosmos DB-databaseaccount dat u zojuist hebt gemaakt. 

<a name="devconfig"></a>
## <a name="configure-the-connection-string-in-your-nodejs-application"></a>Configureer de verbindingsreeks in uw Node.js-toepassing

Open `config/env/local-development.js` in uw MEAN.js-opslagplaats.

Vervang de inhoud van dit bestand door de volgende code. Zorg ervoor dat u ook de twee `<cosmosdb-name>`-plaatsaanduidingen vervangt door de naam van uw Cosmos-account.

```javascript
'use strict';

module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb-name>:<primary_master_key>@<cosmosdb-name>.documents.azure.com:10255/mean-dev?ssl=true&sslverifycertificate=false'
  }
};
```

## <a name="retrieve-the-key"></a>De sleutel ophalen

U hebt de databasesleutel nodig om verbinding te kunnen maken met een Cosmos-database. Gebruik de opdracht [az cosmosdb keys list](/cli/azure/cosmosdb/keys#az-cosmosdb-keys-list) om de primaire sleutel op te halen.

```azurecli-interactive
az cosmosdb keys list --name <cosmosdb-name> --resource-group myResourceGroup --query "primaryMasterKey"
```

De Azure CLI voert informatie uit die lijkt op het volgende voorbeeld. 

```json
"RUayjYjixJDWG5xTqIiXjC..."
```

Kopieer de waarde van `primaryMasterKey`. Plak deze op de `<primary_master_key>` in `local-development.js`.

Sla uw wijzigingen op.

### <a name="run-the-application-again"></a>Voer de toepassing opnieuw uit.

Voer `npm start` opnieuw uit. 

```bash
npm start
```

Er verschijnt nu een consolebericht dat de ontwikkelomgeving gereed is voor gebruik. 

Ga naar `http://localhost:3000` in een browser. Selecteer **Registreren** in het bovenste menu en probeer twee fictieve gebruikers te maken. 

De MEAN.js-voorbeeldtoepassing slaat gebruikersgegevens op in de database. Als het lukt en MEAN.js automatisch aanmeldt met de gemaakte gebruiker, is uw Azure Cosmos DB-verbinding actief. 

:::image type="content" source="./media/create-mongodb-nodejs/mongodb-connect-success.png" alt-text="MEAN.js maakt een geslaagde verbinding met MongoDB":::

## <a name="view-data-in-data-explorer"></a>Gegevens bekijken in Data Explorer

Gegevens die zijn opgeslagen in een Cosmos-database kunnen via Azure Portal worden bekeken en er kunnen vanuit de portal query's op worden uitgevoerd.

Meld u aan bij de [Azure Portal](https://portal.azure.com) in uw webbrowser om de gebruikersgegevens die u in de vorige stap hebt gemaakt, te bekijken, query’s erop uit te voeren of andere taken ermee uit te voeren.

Voer **Azure Cosmos DB** in het bovenste zoekvak in. Wanneer uw Cosmos-accountblade wordt geopend, selecteert u uw Cosmos-account. Selecteer in het linker navigatiegedeelte **Data Explorer**. Vouw uw verzameling uit in het venster Verzamelingen. Dan kunt u de documenten in de verzameling zien, query’s op de gegevens uitvoeren en zelfs opgeslagen procedures, triggers en UDF’s maken en uitvoeren. 

:::image type="content" source="./media/create-mongodb-nodejs/cosmosdb-connect-mongodb-data-explorer.png" alt-text="Data Explorer in Azure Portal":::


## <a name="deploy-the-nodejs-application-to-azure"></a>De Node.js-toepassing implementeren naar Azure

In deze stap implementeert u uw Node.js-toepassing naar Cosmos DB.

U hebt wellicht opgemerkt dat het configuratiebestand dat u eerder had gewijzigd, bestemd is voor de ontwikkelomgeving (`/config/env/local-development.js`). Wanneer u uw toepassing implementeert naar App Service, wordt deze standaard in de productieomgeving uitgevoerd. U moet nu dus dezelfde wijziging toepassing op het respectieve configuratiebestand.

Open `config/env/production.js` in uw MEAN.js-opslagplaats.

Vervang in het object `db` de waarde van `uri` zoals getoond in het volgende voorbeeld. Zorg dat u de plaatsaanduidingen vervangt zoals eerder beschreven.

```javascript
'mongodb://<cosmosdb-name>:<primary_master_key>@<cosmosdb-name>.documents.azure.com:10255/mean?ssl=true&sslverifycertificate=false',
```

> [!NOTE] 
> De optie `ssl=true` is belangrijk vanwege de vereisten van Cosmos DB. Zie [Connection string requirements](connect-mongodb-account.md#connection-string-requirements) (Vereisten voor verbindingsreeksen) voor meer informatie.
>
>

Voer in de terminal al uw wijzigingen door in Git. U kunt beide opdrachten kopiëren om ze samen uit te voeren.

```bash
git add .
git commit -m "configured MongoDB connection string"
```
## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een Azure Cosmos DB MongoDB API-account maakt met behulp van Azure Cloud Shell en hoe u een MEAN.js-app kunt uitvoeren om gebruikers aan het account toe te voegen. Nu kunt u aanvullende gegevens in uw Azure Cosmos DB-account importeren.

> [!div class="nextstepaction"]
> [MongoDB-gegevens importeren in Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)