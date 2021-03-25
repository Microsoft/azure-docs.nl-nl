---
title: Azure Functions verbinding maken met Azure Cosmos DB met behulp van Visual Studio code
description: Informatie over het verbinden van Azure Functions met een Azure Cosmos DB-account door een uitvoer binding toe te voegen aan uw Visual Studio code-project.
author: ThomasWeiss
ms.date: 03/23/2021
ms.topic: quickstart
ms.author: thweiss
zone_pivot_groups: programming-languages-set-functions-temp
ms.openlocfilehash: 0a0c63ee54699185bcd02104b1a3f4d0070ea808
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105023245"
---
# <a name="connect-azure-functions-to-azure-cosmos-db-using-visual-studio-code"></a>Azure Functions verbinding maken met Azure Cosmos DB met behulp van Visual Studio code

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

In dit artikel leest u hoe u met Visual Studio code verbinding maakt [Azure Cosmos DB](../cosmos-db/introduction.md) met de functie die u hebt gemaakt in het vorige artikel Snelstartgids. De uitvoer binding die u aan deze functie toevoegt, schrijft gegevens van de HTTP-aanvraag naar een JSON-document dat is opgeslagen in een Azure Cosmos DB container. 

::: zone pivot="programming-language-csharp"
Voordat u begint, moet u de Snelstartgids volt ooien [: een C#-functie maken in azure met Visual Studio code](create-first-function-vs-code-csharp.md). Als u de resources na voltooiing van dat artikel al had opgeruimd, doorloopt u de stappen voor het maken van de functie-app en de bijbehorende resources opnieuw in Azure.
::: zone-end
::: zone pivot="programming-language-javascript"  
Voordat u begint, moet u de [snelstartgids uitvoeren: een Java script-functie maken in azure met Visual Studio code](create-first-function-vs-code-node.md). Als u de resources na voltooiing van dat artikel al had opgeruimd, doorloopt u de stappen voor het maken van de functie-app en de bijbehorende resources opnieuw in Azure.  
::: zone-end

## <a name="configure-your-environment"></a>Uw omgeving configureren

Voordat u aan de slag gaat, moet u ervoor zorgen dat u de [Azure data base-extensie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb) voor Visual Studio code installeert.

## <a name="create-your-azure-cosmos-db-account"></a>Uw Azure Cosmos DB-account maken

> [!IMPORTANT]
> [Azure Cosmos DB serverloos](../cosmos-db/serverless.md) is nu beschikbaar als preview-versie. Deze modus op basis van verbruik maakt Azure Cosmos DB een krachtige optie voor serverloze workloads. Als u Azure Cosmos DB in de serverloze modus wilt gebruiken, kiest u **Server** zonder de **capaciteits modus** bij het maken van uw account.

1. Meld u in een nieuw browservenster aan bij [Azure Portal](https://portal.azure.com/).

2. Klik op **Een resource maken** > **Databases** > **Azure Cosmos DB**.
   
    :::image type="content" source="../../includes/media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png" alt-text="Het deelvenster Databases in Azure Portal" border="true":::

3. Voer op de pagina **Azure Cosmos DB account maken** de instellingen voor uw nieuwe Azure Cosmos DB-account in. 
 
    Instelling|Waarde|Beschrijving
    ---|---|---
    Abonnement|*Uw abonnement*|Kies het Azure-abonnement waar u uw functie-app in het [vorige artikel](./create-first-function-vs-code-csharp.md)hebt gemaakt.
    Resourcegroep|*De resource groep*|Kies de resource groep waar u de functie-app in het [vorige artikel](./create-first-function-vs-code-csharp.md)hebt gemaakt.
    Accountnaam|*Voer een unieke naam in*|Voer een unieke naam in om uw Azure Cosmos DB-account te identificeren.<br><br>De accountnaam moet tussen de 3 en 31 tekens lang zijn en mag alleen kleine letters, cijfers en afbreekstreepjes bevatten.
    API|Core (SQL)|Selecteer **kern (SQL)** om een document database te maken waarin u een query kunt uitvoeren met behulp van een SQL-syntaxis. Meer [informatie over de Azure Cosmos DB SQL-API](../cosmos-db/introduction.md).|
    Locatie|*Selecteer de regio die het dichtst bij uw locatie ligt*|Selecteer een geografische locatie waar u het Azure Cosmos DB-account wilt hosten. Gebruik de locatie die het dichtst bij u of uw gebruikers ligt om de snelste toegang tot uw gegevens te krijgen.
    Capaciteitsmodus|Serverloze of ingerichte door Voer|Selecteer **Serverloos** om een account te maken in de modus [serverloos](../cosmos-db/serverless.md). Selecteer **Ingerichte doorvoer** om een account te maken in de modus [Ingerichte doorvoer](../cosmos-db/set-throughput.md).<br><br>Kies **serverloos** als u aan de slag gaat met Azure Cosmos db.

4. Klik op **Controleren + maken**. U kunt de secties **Netwerk** en **Tags** overslaan. 

5. Lees de overzichtsinformatie en klik op **Maken**. 

6. Wacht tot de nieuwe Azure Cosmos DB zijn gemaakt en selecteer vervolgens **naar resource**.

    :::image type="content" source="../cosmos-db/media/create-cosmosdb-resources-portal/azure-cosmos-db-account-deployment-successful.png" alt-text="Het maken van het Azure Cosmos DB-account is voltooid" border="true":::

## <a name="create-an-azure-cosmos-db-database-and-container"></a>Een Azure Cosmos DB-Data Base en-container maken

Selecteer in uw Azure Cosmos DB-account **Data Explorer** en vervolgens **nieuwe container**. Maak een nieuwe Data Base *met de naam my-data base*, een nieuwe container met de naam *mijn-container* en kies `/id` als [partitie sleutel](../cosmos-db/partitioning-overview.md).

:::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/create-container.png" alt-text="Een nieuwe Azure Cosmos DB-container maken op basis van de Azure Portal" border="true":::

## <a name="update-your-function-app-settings"></a>De instellingen van uw functie-app bijwerken

In het [vorige Quick](./create-first-function-vs-code-csharp.md)start-artikel hebt u een functie-app in azure gemaakt. In dit artikel werkt u uw functie-app bij om JSON-documenten te schrijven in de Azure Cosmos DB-container die u hierboven hebt gemaakt. Als u verbinding wilt maken met uw Azure Cosmos DB-account, moet u de connection string toevoegen aan de app-instellingen. Vervolgens downloadt u de nieuwe instelling naar uw local.settings.jsin het bestand, zodat u verbinding kunt maken met uw Azure Cosmos DB account wanneer u lokaal wordt uitgevoerd.

1. Ga in Visual Studio code naar het Azure Cosmos DB account dat u zojuist hebt gemaakt. Klik met de rechter muisknop op de naam en selecteer **verbindings reeks kopiëren**.

    :::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/copy-connection-string.png" alt-text="Het Azure Cosmos DB connection string kopiëren" border="true":::

1. Druk op <kbd>F1</kbd> om het opdracht palet te openen, zoek de opdracht en voer deze uit `Azure Functions: Add New Setting...` .

1. Kies de functie-app die u in het vorige artikel hebt gemaakt. Geef de volgende informatie op bij de prompts:

    + **Voer de naam van de nieuwe app-instelling** in: type `CosmosDbConnectionString` .

    + **Voer een waarde in voor "CosmosDbConnectionString"**: plak de Connection String van uw Azure Cosmos DB account, zoals eerder is gekopieerd.

1. Druk nogmaals op <kbd>F1</kbd> om het opdracht palet te openen, zoek de opdracht en voer deze uit `Azure Functions: Download Remote Settings...` . 

1. Kies de functie-app die u in het vorige artikel hebt gemaakt. Selecteer **Ja op alle** om de bestaande lokale instellingen te overschrijven. 

## <a name="register-binding-extensions"></a>Binding-extensies registreren

Omdat u een Azure Cosmos DB-uitvoer binding gebruikt, moet u de bijbehorende extensie voor bindingen hebben geïnstalleerd voordat u het project uitvoert. 

::: zone pivot="programming-language-csharp"

Met uitzondering van HTTP- en timertriggers worden bindingen geïmplementeerd als uitbreidingspakketten. Voer de volgende [dotnet add package](/dotnet/core/tools/dotnet-add-package)-opdracht in het terminalvenster uit om het Storage-extensiepakket toe te voegen aan uw project.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.CosmosDB
```

::: zone-end

::: zone pivot="programming-language-javascript"

Uw project is geconfigureerd voor het gebruik van [uitbreidingsbundels](functions-bindings-register.md#extension-bundles), waarmee automatisch een vooraf gedefinieerde set uitbreidingspakketten wordt geïnstalleerd. 

Het gebruik van uitbreidingsbundels wordt ingeschakeld in het bestand host.json in de hoofdmap van het project, dat er als volgt uitziet:

:::code language="json" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/host.json":::

::: zone-end

U kunt nu de Azure Cosmos DB uitvoer binding toevoegen aan uw project.

## <a name="add-an-output-binding"></a>Een uitvoerbinding toevoegen

In functies moet voor elk type binding een `direction`, `type`en een unieke `name` worden gedefinieerd in het bestand function.json. De manier waarop u deze kenmerken definieert, is afhankelijk van de taal van uw functie-app.

::: zone pivot="programming-language-csharp"

In een bibliotheekproject van de C#-klasse worden de bindingen gedefinieerd als bindingseigenschappen in de functiemethode. Het bestand *function.json* dat wordt vereist door Functions, wordt automatisch gegenereerd op basis van deze kenmerken.

Open het projectbestand *HttpExample.cs* en voeg de volgende parameter toe aan de methodedefinitie `Run`:

```csharp
[CosmosDB(
    databaseName: "my-database",
    collectionName: "my-container",
    ConnectionStringSetting = "CosmosDbConnectionString")]IAsyncCollector<dynamic> documentsOut,
```

De `documentsOut` para meter is een IAsyncCollector <T> -type dat een verzameling JSON-documenten vertegenwoordigt die naar uw Azure Cosmos DB-container worden geschreven wanneer de functie is voltooid. Specifieke kenmerken bevat de naam van de container en de naam van de bovenliggende data base. De connection string voor uw Azure Cosmos DB-account wordt ingesteld door de `ConnectionStringSettingAttribute` .

De uitvoermethodedefinitie moet er nu als volgt uitzien:  

```csharp
[FunctionName("HttpExample")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
    [CosmosDB(
        databaseName: "my-database",
        collectionName: "my-container",
        ConnectionStringSetting = "CosmosDbConnectionString")]IAsyncCollector<dynamic> documentsOut,
    ILogger log)
```

::: zone-end

::: zone pivot="programming-language-javascript"

Bindingskenmerken worden rechtstreeks in het bestand function.json gedefinieerd. Afhankelijk van het type binding zijn er aanvullende eigenschappen vereist. De [Azure Cosmos DB uitvoer configuratie](./functions-bindings-cosmosdb-v2-output.md#configuration) beschrijft de velden die vereist zijn voor een Azure Cosmos DB-uitvoer binding. De extensie maakt het eenvoudig om bindingen toe te voegen aan het bestand function.json. 

Als u een binding wilt maken, klikt u met de rechtermuisknop (ctrl + klik op macOS) op het bestand `function.json` in uw HttpTrigger-map en kiest u **Binding toevoegen...** . Volg de aanwijzingen om de volgende bindingseigenschappen te definiëren voor de nieuwe binding:

| Vraag | Waarde | Beschrijving |
| -------- | ----- | ----------- |
| **Bindingsrichting selecteren** | `out` | De binding is een uitvoerbinding. |
| **Binding selecteren met richting ' uit '** | `Azure Cosmos DB` | De binding is een Azure Cosmos DB binding. |
| **De naam voor het identificeren van deze binding in uw code** | `outputDocument` | Naam die de bindingsparameter identificeert waar in uw code naar wordt verwezen. |
| **De Cosmos DB Data Base waar de gegevens worden geschreven** | `my-database` | De naam van de Azure Cosmos DB Data Base met de doel container. |
| **Data base verzameling waar gegevens worden geschreven** | `my-container` | De naam van de Azure Cosmos DB-container waar de JSON-documenten worden geschreven. |
| **Indien waar, worden de Cosmos-DB-database en -verzameling gemaakt** | `false` | De doel database en-container bestaan al. |
| **Selecteer de instelling in 'local.setting.json'** | `CosmosDbConnectionString` | De naam van een toepassings instelling die de connection string voor de Azure Cosmos DB-account bevat. |
| **Partitiesleutel (optioneel)** | *leeg laten* | Alleen vereist wanneer de uitvoer binding de container maakt. |
| **Verzamelingsdoorvoer (optioneel)** | *leeg laten* | Alleen vereist wanneer de uitvoer binding de container maakt. |

Er wordt een binding toegevoegd aan de matrix `bindings` in uw function.json, die er als volgt uit hoort te zien:

```json
{
    "type": "cosmosDB",
    "direction": "out",
    "name": "outputDocument",
    "databaseName": "my-database",
    "collectionName": "my-container",
    "createIfNotExists": "false",
    "connectionStringSetting": "CosmosDbConnectionString"
}
```

::: zone-end

## <a name="add-code-that-uses-the-output-binding"></a>Code toevoegen die gebruikmaakt van de uitvoerbinding

::: zone pivot="programming-language-csharp"  

Voeg code toe die gebruikmaakt van het `documentsOut` bindings object voor uitvoer om een JSON-document te maken. Voeg deze code toe voordat de methode wordt geretourneerd.

```csharp
if (!string.IsNullOrEmpty(name))
{
    // Add a JSON document to the output container.
    await documentsOut.AddAsync(new
    {
        // create a random ID
        id = System.Guid.NewGuid().ToString(),
        name = name
    });
}
```

Op dit moment moet uw functie er als volgt uit zien:

```csharp
[FunctionName("HttpExample")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
    [CosmosDB(
        databaseName: "my-database",
        collectionName: "my-container",
        ConnectionStringSetting = "CosmosDbConnectionString")]IAsyncCollector<dynamic> documentsOut,
    ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    if (!string.IsNullOrEmpty(name))
    {
        // Add a JSON document to the output container.
        await documentsOut.AddAsync(new
        {
            // create a random ID
            id = System.Guid.NewGuid().ToString(),
            name = name
        });
    }

    string responseMessage = string.IsNullOrEmpty(name)
        ? "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response."
        : $"Hello, {name}. This HTTP triggered function executed successfully.";

    return new OkObjectResult(responseMessage);
}
```

::: zone-end

::: zone pivot="programming-language-javascript"  

Voeg code toe die gebruikmaakt `outputDocument` van het bindings object voor uitvoer op `context.bindings` om een JSON-document te maken. Voeg deze code toe vóór de instructie `context.res`.

```javascript
if (name) {
    context.bindings.outputDocument = JSON.stringify({
        // create a random ID
        id: new Date().toISOString() + Math.random().toString().substr(2,8),
        name: name
    });
}
```

Op dit moment moet uw functie er als volgt uit zien:

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    const name = (req.query.name || (req.body && req.body.name));
    const responseMessage = name
        ? "Hello, " + name + ". This HTTP triggered function executed successfully."
        : "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.";

    if (name) {
        context.bindings.outputDocument = JSON.stringify({
            // create a random ID
            id: new Date().toISOString() + Math.random().toString().substr(2,8),
            name: name
        });
    }

    context.res = {
        // status: 200, /* Defaults to 200 */
        body: responseMessage
    };
}
```

::: zone-end  

## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

1. Net als in het vorige artikel drukt u op <kbd>F5</kbd> om het functie-app-project en de belangrijkste hulpprogram ma's te starten. 

1. Ga naar het gedeelte **functies van Azure:** als u kern hulpprogramma's uitvoert. Vouw onder **functies** **lokale project**  >  **functies** uit. Klik met de rechter muisknop (CTRL + klik op Mac) de `HttpExample` functie en kies **functie nu uitvoeren...**.

    :::image type="content" source="../../includes/media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="De functie nu uitvoeren vanuit Visual Studio code":::

1. In de **hoofd tekst** van de aanvraag ziet u de waarde van de aanvraag bericht hoofdtekst van `{ "name": "Azure" }` . Druk op ENTER om dit aanvraag bericht naar uw functie te verzenden.  
 
1. Nadat een antwoord is geretourneerd, drukt u op <kbd>CTRL + C</kbd> om de kern hulpprogramma's te stoppen.

### <a name="verify-that-a-json-document-has-been-created"></a>Controleren of een JSON-document is gemaakt

1. Ga op het Azure Portal terug naar uw Azure Cosmos DB-account en selecteer **Data Explorer**.

1. Vouw uw data base en container uit en selecteer **items** om de documenten weer te geven die in de container zijn gemaakt.

1. Controleer of er een nieuw JSON-document is gemaakt door de uitvoer binding.

    :::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/verify-output.png" alt-text="Controleren of er een nieuw document is gemaakt in de Azure Cosmos DB container" border="true":::

## <a name="redeploy-and-verify-the-updated-app"></a>De bijgewerkte app opnieuw implementeren en verifiëren

1. Druk in Visual Studio Code op F1 om het opdrachtenpalet te openen. In het opdrachtenpalet zoekt en selecteert u `Azure Functions: Deploy to function app...`.

1. Kies de functie-app die u in het eerste artikel hebt gemaakt. Omdat u uw project opnieuw implementeert voor dezelfde app, selecteert u **Implementeren** om de waarschuwing over het overschrijven van bestanden te negeren.

1. Nadat de implementatie is voltooid, kunt u opnieuw de functie **nu uitvoeren** gebruiken om de functie in azure te activeren.

1. [Controleer nogmaals de documenten die in de Azure Cosmos DB container zijn gemaakt](#verify-that-a-json-document-has-been-created) om te controleren of de uitvoer binding opnieuw een nieuw JSON-document genereert.

## <a name="clean-up-resources"></a>Resources opschonen

In Azure verwijzen *Resources* naar functie-apps, functies, opslagaccounts enzovoort. Deze zijn gegroepeerd in *resourcegroepen*. U kunt alle resources in een groep verwijderen door de groep zelf te verwijderen.

U hebt resources gemaakt om deze snelstartgidsen te voltooien. Deze resources kunnen bij u in rekening worden gebracht, afhankelijk van de [accountstatus](https://azure.microsoft.com/account/) en [serviceprijzen](https://azure.microsoft.com/pricing/). Als u de resources niet meer nodig hebt, kunt u ze als volgt verwijderen:

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]

## <a name="next-steps"></a>Volgende stappen

U hebt uw HTTP-geactiveerde functie bijgewerkt om JSON-documenten naar een Azure Cosmos DB-container te schrijven. Nu kunt u meer informatie vinden over het ontwikkelen van functies met Visual Studio Code:

+ [Azure Functions ontwikkelen met Visual Studio Code](functions-develop-vs-code.md)

+ [Azure Functions-triggers en -bindingen](functions-triggers-bindings.md).
::: zone pivot="programming-language-csharp"  
+ [Voorbeelden van complete Function-projecten in C#](/samples/browse/?products=azure-functions&languages=csharp).

+ [Naslaginformatie over Azure Functions C# voor ontwikkelaars](functions-dotnet-class-library.md)  
::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [Voorbeelden van complete Function-projecten in Javascript](/samples/browse/?products=azure-functions&languages=javascript).

+ [Ontwikkelaarshandleiding voor Azure Functions Javascript](functions-reference-node.md)  
::: zone-end  