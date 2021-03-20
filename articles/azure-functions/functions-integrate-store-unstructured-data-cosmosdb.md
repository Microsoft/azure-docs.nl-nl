---
title: Ongestructureerde gegevens opslaan met behulp van Azure Cosmos DB en Functions
description: Ongestructureerde gegevens opslaan met behulp van Azure Functions en Cosmos DB
ms.topic: quickstart
ms.date: 10/01/2020
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 5bc3895cb219338acde492b871dce806db70622b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91661156"
---
# <a name="store-unstructured-data-using-azure-functions-and-azure-cosmos-db"></a>Ongestructureerde gegevens opslaan met behulp van Azure Functions en Azure Cosmos DB

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) is een geweldige manier om ongestructureerde gegevens en JSON-gegevens op te slaan. Cosmos DB biedt, in combinatie met Azure Functions, een snelle en eenvoudige manier om gegevens op te slaan met veel minder code dan nodig is voor het opslaan van gegevens in een relationele database.

> [!NOTE]
> Op dit moment werken de Azure Cosmos DB-trigger, invoerbindingen en uitvoerbindingen alleen met SQL API- en Graph API-accounts.

In Azure Functions bieden invoer- en uitvoerbindingen een verklarende manier om verbinding te maken met externe servicegegevens vanuit uw functie. In dit artikel wordt uitgelegd hoe u een bestaande functie kunt bijwerken voor het toevoegen van een uitvoerbinding die niet-gestructureerde gegevens in een Azure Cosmos DB-document opslaat.

## <a name="prerequisites"></a>Vereisten

Vereisten voor het voltooien van deze zelfstudie:

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

## <a name="create-an-azure-cosmos-db-account"></a>Maak een Azure Cosmos DB-account

U moet een Azure Cosmos DB-account hebben dat gebruikmaakt van de SQL-API voordat u de uitvoerbinding maakt.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-an-output-binding"></a>Een uitvoerbinding toevoegen

1. Ga in de Azure-portal naar en selecteer de functie-app die u eerder hebt gemaakt.

1. Selecteer **Functies** en vervolgens de functie HttpTrigger.

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-select-http-function.png" alt-text="Selecteer uw HTTP-functie in de Azure-portal." border="true":::

1. Selecteer **Integratie** en **+ Uitvoer toevoegen**.

     :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-add-output-binding.png" alt-text="Een Cosmos DB-uitvoerbinding toevoegen" border="true":::

1. Gebruik de **Uitvoer maken**-instellingen zoals opgegeven in de tabel:

     :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-configure-cosmosdb-binding.png" alt-text="Azure Cosmos DB-uitvoerbinding configureren" border="true":::

    | Instelling      | Voorgestelde waarde  | Beschrijving                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **Bindingstype** | Azure Cosmos DB | De naam van het te selecteren bindingstype om de uitvoerbinding te maken naar Azure Cosmos DB. |
    | **Parameternaam van document** | taskDocument | Naam die in code verwijst naar het Cosmos DB-object. |
    | **Databasenaam** | taskDatabase | Naam van database waarin documenten worden opgeslagen. |
    | **Naam van verzameling** | taskCollection | Naam van de databaseverzameling. |
    | **Indien waar, worden de Cosmos-DB-database en -verzameling gemaakt** | Ja | De verzameling bestaat nog niet, dus moet u deze maken. |
    | **Cosmos DB-accountverbinding** | Nieuwe instelling | Selecteer **Nieuw**, kies **Azure Cosmos DB-account** , gevolgd door het **Database-account** dat u eerder hebt gemaakt en selecteer **OK**. Hiermee maakt u een toepassingsinstelling voor uw accountverbinding. Deze instelling wordt gebruikt door de binding om verbinding te maken met de database. |

1. Selecteer **OK** om de binding te maken.

## <a name="update-the-function-code"></a>De functiecode bijwerken

Vervang de bestaande functiecode door het volgende in de door u gekozen taal:

# <a name="c"></a>[C#](#tab/csharp)

Vervang de bestaande C#-functie door de volgende code:

```csharp
#r "Newtonsoft.Json"

using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

public static IActionResult Run(HttpRequest req, out object taskDocument, ILogger log)
{
    string name = req.Query["name"];
    string task = req.Query["task"];
    string duedate = req.Query["duedate"];

    // We need both name and task parameters.
    if (!string.IsNullOrEmpty(name) && !string.IsNullOrEmpty(task))
    {
        taskDocument = new
        {
            name,
            duedate,
            task
        };

        return (ActionResult)new OkResult();
    }
    else
    {
        taskDocument = null;
        return (ActionResult)new BadRequestResult();
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Vervang de bestaande JavaScript-functie door de volgende code:

```js
module.exports = async function (context, req) {

    // We need both name and task parameters.
    if (req.query.name && req.query.task) {

        // Set the output binding data from the query object.
        context.bindings.taskDocument = req.query;

        // Success.
        context.res = {
            status: 200
        };
    }
    else {
        context.res = {
            status: 400,
            body: "The query options 'name' and 'task' are required."
        };
    }
};
```
---

In dit codevoorbeeld worden de queryreeksen van de HTTP-aanvraag gelezen en toegewezen aan het velden in het `taskDocument`-object. De `taskDocument`-binding verzendt de objectgegevens van deze bindingsparameter voor opslag in de gebonden documentdatabase. De database wordt de eerste keer dat de functie wordt uitgevoerd gemaakt.

## <a name="test-the-function-and-database"></a>De functie en database testen

1. Selecteer **Testen/Uitvoeren**. Selecteer onder **Query** de optie **+ Parameter toevoegen** en voeg de volgende parameters toe aan de querytekenreeks:

    + `name`
    + `task`
    + `duedate`

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-test-function.png" alt-text="Test de functie." border="true":::


1. Selecteer **Uitvoeren** en controleer of een 200-status wordt geretourneerd.

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-test-function-output.png" alt-text="Schermopname toont de status van de HTTP-antwoordcode 200 gemarkeerd na het selecteren van Uitvoeren." border="true":::


1. Zoek en selecteer **Azure Cosmos DB** in de Azure-portal.

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-search-cosmos-db.png" alt-text="Zoeken naar de Cosmos DB-service" border="true":::

1. Kies uw Azure Cosmos DB-account en selecteer **Data Explorer**.

1. Vouw de **TaskCollection**-knooppunten uit, selecteer het nieuwe document en controleer of het document uw querytekenreekswaarden, samen met enkele aanvullende metagegevens, bevat.

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-data-explorer-check-document.png" alt-text="Controleer de tekenreekswaarden in uw document." border="true":::

U hebt een binding toegevoegd aan de HTTP-trigger waarmee niet-gestructureerde gegevens worden opgeslagen in een Azure Cosmos DB.

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Functions Cosmos DB bindings](functions-bindings-cosmosdb.md) (Bindingen tussen Azure Functions en Cosmos DB) voor meer informatie over de binding met een Cosmos DB database.

[!INCLUDE [functions-quickstart-next-steps](../../includes/functions-quickstart-next-steps-2.md)]
