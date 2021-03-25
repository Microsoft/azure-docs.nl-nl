---
title: 'Quickstart: Cassandra-API met Python - Azure Cosmos DB'
description: In deze snelstart ziet u hoe u de Apache Cassandra-API in Azure Cosmos DB gebruikt om een profieltoepassing te maken met Python.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.devlang: python
ms.topic: quickstart
ms.date: 08/13/2020
ms.custom: devx-track-python
ms.openlocfilehash: ea5a443caba25b7b5e6d843408c12e61a55b9e10
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105047436"
---
# <a name="quickstart-build-a-cassandra-app-with-python-sdk-and-azure-cosmos-db"></a>Quickstart: een Cassandra-app bouwen met de Python SDK en Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java v3](create-cassandra-java.md)
> * [Java v4](create-cassandra-java-v4.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

In deze quickstart maakt u een Azure Cosmos DB Cassandra-API-account en gebruikt u een Cassandra Python-app, die is gekloond uit GitHub, om een Cassandra-database en -container te maken. Met Azure Cosmos DB, een databaseservice met meerdere modellen, kunt u snel databases met documenten, tabellen, sleutelwaarden en grafieken maken en hier query's op uitvoeren. Deze databases hebben wereldwijde distributie en horizontale schaalmogelijkheden.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Of [probeer Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) zonder Azure-abonnement.
- [Python 2,7 of 3.6 +](https://www.python.org/downloads/).
- [Git](https://git-scm.com/downloads).
- [Python-stuurprogramma voor Apache Cassandra](https://github.com/datastax/python-driver).

## <a name="create-a-database-account"></a>Een databaseaccount maken

Voordat u een documentdatabase kunt maken, moet u een Cassandra-account maken met Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

We gaan nu een Cassandra API-app klonen vanuit GitHub, de verbindingsreeks instellen en de app uitvoeren. U zult zien hoe gemakkelijk het is om op een programmatische manier met gegevens te werken. 

1. Open een opdrachtprompt. Maak een nieuwe map met de naam `git-samples`. Sluit vervolgens de opdrachtprompt.

    ```bash
    md "C:\git-samples"
    ```

2. Open een git-terminalvenster, bijvoorbeeld git bash, en gebruik de `cd`-opdracht om naar de nieuwe map te gaan voor het installeren van de voorbeeld-app.

    ```bash
    cd "C:\git-samples"
    ```

3. Voer de volgende opdracht uit om de voorbeeldopslagplaats te klonen. Deze opdracht maakt een kopie van de voorbeeld-app op uw computer.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-python-getting-started.git
    ```

## <a name="review-the-code"></a>De code bekijken

Deze stap is optioneel. Als u wilt weten hoe de databaseresources met de code worden gemaakt, kunt u de volgende codefragmenten bekijken. De codefragmenten zijn alle afkomstig uit het bestand *pyquickstart.py*. Als u deze stap wilt overslaan, kunt u verdergaan naar [Uw verbindingsreeks bijwerken](#update-your-connection-string). 

* Het `cluster` wordt geïnitialiseerd met `contactPoint`- en `port`-gegevens die worden opgehaald uit Azure Portal. Het `cluster` maakt vervolgens verbinding met de Cassandra-API voor Azure Cosmos DB door gebruik te maken van de methode `connect()`. Een geautoriseerde verbinding wordt tot stand gebracht met behulp van de gebruikersnaam, het wachtwoord en het standaardcertificaat of een expliciet certificaat als u er een hebt opgegeven in het configuratiebestand.

  :::code language="python" source="~/cosmosdb-cassandra-python-sample/pyquickstart.py" id="authenticateAndConnect":::

* Er wordt een nieuwe keyspace gemaakt.

  :::code language="python" source="~/cosmosdb-cassandra-python-sample/pyquickstart.py" id="createKeyspace":::

* Er wordt een nieuwe tabel gemaakt.

  :::code language="python" source="~/cosmosdb-cassandra-python-sample/pyquickstart.py" id="createTable":::

* Sleutel/waarde-entiteiten worden ingevoegd.

  :::code language="python" source="~/cosmosdb-cassandra-python-sample/pyquickstart.py" id="insertData":::

* Voer een query uit om alle sleutelwaarden op te halen.

  :::code language="python" source="~/cosmosdb-cassandra-python-sample/pyquickstart.py" id="queryAllItems":::
    
* Voer een query uit om een sleutelwaarde op te halen.

  :::code language="python" source="~/cosmosdb-cassandra-python-sample/pyquickstart.py" id="queryByID":::

## <a name="update-your-connection-string"></a>Uw verbindingsreeks bijwerken

Ga nu terug naar Azure Portal om de verbindingsreeksinformatie op te halen en kopieer deze in de app. De verbindingsreeks stelt uw app in staat om te communiceren met de gehoste database.

1. Selecteer in uw Azure Cosmos DB-account in de [Azure-portal](https://portal.azure.com/) de optie **Verbindingsreeks**. 

1. Gebruik de knop :::image type="icon" source="./media/create-cassandra-python/copy.png"::: aan de rechterkant van het scherm om de bovenste waarde (het CONTACT POINT) te kopiëren. 

   :::image type="content" source="./media/create-cassandra-python/keys.png" alt-text="Een gebruikersnaam, wachtwoord en contactpunt voor toegang in Azure Portal weergeven en kopiëren, blade Verbindingsreeks":::

1. Open het bestand *config.py*. 

1. Plak de waarde CONTACT POINT vanuit de portal over `<FILLME>` op regel 10.

    Regel 10 moet nu ongeveer als volgt uitzien: 

    `'contactPoint': 'cosmos-db-quickstarts.cassandra.cosmosdb.azure.com:10350'`

1. Kopieer de waarde van USERNAME vanuit de portal en plak deze over `<FILLME>` op regel 6.

    Regel 6 moet nu ongeveer als volgt uitzien: 

    `'username': 'cosmos-db-quickstart',`
    
1. Kopieer de waarde van PASSWORD vanuit de portal en plak deze over `<FILLME>` op regel 8.

    Regel 8 moet nu ongeveer als volgt uitzien:

    `'password' = '2Ggkr662ifxz2Mg==`';`

1. Sla het bestand *config.py* op.
    
## <a name="use-the-x509-certificate"></a>Het X509-certificaat gebruiken

1. Download het Baltimore CyberTrust Root-certificaat lokaal van [https://cacert.omniroot.com/bc2025.crt](https://cacert.omniroot.com/bc2025.crt). Wijzig de naam van het bestand met de bestandsextensie *.cer*.

   Het certificaat heeft serienummer `02:00:00:b9` en SHA1-vingerafdruk `d4:de:20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74`.

2. Open *pyquickstart.py* en wijzig het `path\to\cert`, zodat dit naar uw nieuwe certificaat verwijst.

3. Sla *pyquickstart.py* op.

## <a name="run-the-python-app"></a>De Python-app uitvoeren

1. Gebruik de opdracht cd in de git-terminal om naar de map `azure-cosmos-db-cassandra-python-getting-started` te gaan. 

2. Voer de volgende opdrachten uit om de vereiste modules te installeren:

    ```python
    python -m pip install cassandra-driver
    python -m pip install prettytable
    python -m pip install requests
    python -m pip install pyopenssl
    ```

2. Voer de volgende opdracht uit om de Python-app te starten:

    ```
    python pyquickstart.py
    ```

3. Controleer of de resultaten op de opdrachtregel aan de verwachting voldoen.

    Druk op Ctrl+C om de uitvoering van het programma te stoppen en het consolevenster te sluiten. 

    :::image type="content" source="./media/create-cassandra-python/output.png" alt-text="De uitvoer weergeven en controleren":::
    
4. Open **Data Explorer** in de Azure-portal om deze nieuwe gegevens te bekijken, te wijzigen, een query erop uit te voeren of er iets anders mee te doen. 

    :::image type="content" source="./media/create-cassandra-python/data-explorer.png" alt-text="De gegevens weergeven in Data Explorer":::

## <a name="review-slas-in-the-azure-portal"></a>SLA’s bekijken in Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een Azure Cosmos DB-account maakt met Cassandra-API en een Cassandra Python-app uitvoert waarmee een Cassandra-database en -container wordt gemaakt. Nu kunt u aanvullende gegevens in uw Azure Cosmos DB-account importeren. 

> [!div class="nextstepaction"]
> [Cassandra-gegevens importeren in Azure Cosmos DB](cassandra-import-data.md)

