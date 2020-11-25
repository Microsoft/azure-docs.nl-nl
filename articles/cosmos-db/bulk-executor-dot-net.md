---
title: Bulksgewijs uitvoeren van de .NET-bibliotheek in Azure Cosmos DB gebruiken voor bulksgewijze import-en update bewerkingen
description: Bulksgewijs importeren en de Azure Cosmos DB documenten bijwerken met de bulk-uitvoerder .NET-bibliotheek.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: how-to
ms.date: 03/23/2020
ms.author: ramkris
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 6f6994717ff4c730fb27bd26c40d199fb198e528
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "96019953"
---
# <a name="use-the-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db"></a>Gebruik de bulk-uitvoerder .NET-bibliotheek voor het uitvoeren van bulk bewerkingen in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!NOTE]
> Deze bulk-uitvoerder bibliotheek die in dit artikel wordt beschreven, wordt onderhouden voor toepassingen die gebruikmaken van de .NET SDK 2. x-versie. Voor nieuwe toepassingen kunt u gebruikmaken van de **bulk ondersteuning** die rechtstreeks beschikbaar is in de [.NET SDK versie 3. x.](tutorial-sql-api-dotnet-bulk-import.md) hiervoor is geen externe bibliotheek vereist. 

> Gebruik de stappen in de [migratie handleiding](how-to-migrate-from-bulk-executor-library.md) om uw toepassing te migreren als u momenteel gebruikmaakt van de bibliotheek voor bulk-uitvoerder en wilt migreren naar bulk ondersteuning in de nieuwere SDK.

Deze zelfstudie biedt instructies voor het gebruik van de BulkExecutor-bibliotheek voor .NET om documenten te importeren en bij te werken in een Azure Cosmos-container. Zie het artikel overzicht van de bulk-uitvoerder [bibliotheek](bulk-executor-overview.md) voor meer informatie over de bibliotheek voor bulk doorvoer. In deze zelf studie wordt een voor beeld van een .NET-toepassing weer geven waarmee wille keurig gegenereerde documenten bulksgewijs worden geïmporteerd in een Azure Cosmos-container. Na het importeren ziet u hoe u de geïmporteerde gegevens bulksgewijs kunt bijwerken door het opgeven van patches als bewerkingen die moeten worden uitgevoerd op specifieke document velden.

De bibliotheek voor bulk-uitvoering wordt momenteel alleen ondersteund door de Azure Cosmos DB SQL API-en Gremlin-API-accounts. In dit artikel wordt beschreven hoe u de bulk-uitvoerder .NET-bibliotheek met SQL API-accounts gebruikt. Zie [bulksgewijze bewerkingen uitvoeren in de Gremlin-API voor Azure Cosmos DB](bulk-executor-graph-dotnet.md)voor meer informatie over het gebruik van de bulk-uitvoerder .net-bibliotheek met GREMLIN-API-accounts.

## <a name="prerequisites"></a>Vereisten

* Als Visual Studio 2019 nog niet is geïnstalleerd, kunt u de [Visual studio 2019 Community Edition](https://www.visualstudio.com/downloads/)downloaden en gebruiken. Zorg ervoor dat u ' Azure Development ' inschakelt tijdens de installatie van Visual Studio.

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) aan voordat u begint.

* U kunt [Azure Cosmos DB gratis uitproberen](https://azure.microsoft.com/try/cosmosdb/) zonder Azure-abonnement, gratis en toezeg gingen. U kunt ook de Azure Cosmos DB- [emulator](./local-emulator.md) gebruiken met het `https://localhost:8081` eind punt. De primaire sleutel wordt gegeven in [Aanvragen verifiëren](local-emulator.md#authenticate-requests).

* Maak een Azure Cosmos DB SQL-API-account met behulp van de stappen in het gedeelte [Database account maken](create-sql-api-dotnet.md#create-account) van het artikel .net quick start.

## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

We gaan nu naar het werken met code door een .NET-voorbeeld toepassing te downloaden van GitHub. Met deze toepassing worden bulk bewerkingen uitgevoerd op de gegevens die zijn opgeslagen in uw Azure Cosmos-account. Als u de toepassing wilt klonen, opent u een opdracht prompt, navigeert u naar de map waarnaar u deze wilt kopiëren en voert u de volgende opdracht uit:

```bash
git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started.git
```

De gekloonde opslag plaats bevat twee voor beelden van ' BulkImportSample ' en ' BulkUpdateSample '. U kunt een van de voorbeeld toepassingen openen, de verbindings reeksen in App.config bestand bijwerken met de verbindings reeksen van uw Azure Cosmos DB-account, de oplossing bouwen en deze uitvoeren.

De toepassing ' BulkImportSample ' genereert wille keurige documenten en importeert deze bulksgewijs in uw Azure Cosmos-account. Met de toepassing ' BulkUpdateSample ' worden de geïmporteerde documenten bulksgewijs bijgewerkt door patches op te geven als bewerkingen die moeten worden uitgevoerd op specifieke document velden. In de volgende secties controleert u de code in elk van deze voor beelden van apps.

## <a name="bulk-import-data-to-an-azure-cosmos-account"></a>Gegevens bulksgewijs importeren naar een Azure Cosmos-account

1. Ga naar de map ' BulkImportSample ' en open het bestand ' BulkImportSample. SLN '.  

2. De verbindings reeksen van de Azure Cosmos DB worden opgehaald uit het App.config-bestand, zoals wordt weer gegeven in de volgende code:  

   ```csharp
   private static readonly string EndpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
   private static readonly string AuthorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];
   private static readonly string DatabaseName = ConfigurationManager.AppSettings["DatabaseName"];
   private static readonly string CollectionName = ConfigurationManager.AppSettings["CollectionName"];
   private static readonly int CollectionThroughput = int.Parse(ConfigurationManager.AppSettings["CollectionThroughput"]);
   ```

   De functie voor bulk import maakt een nieuwe data base en een container met de database naam, container naam en de doorvoer waarden die zijn opgegeven in het App.config bestand.

3. De volgende keer dat het object DocumentClient wordt geïnitialiseerd met de directe TCP-verbindings modus:  

   ```csharp
   ConnectionPolicy connectionPolicy = new ConnectionPolicy
   {
      ConnectionMode = ConnectionMode.Direct,
      ConnectionProtocol = Protocol.Tcp
   };
   DocumentClient client = new DocumentClient(new Uri(endpointUrl),authorizationKey,
   connectionPolicy)
   ```

4. Het BulkExecutor-object wordt geïnitialiseerd met een hoge waarde voor nieuwe pogingen voor wacht tijd en vertraagde aanvragen. En ze worden vervolgens ingesteld op 0 om de levens duur van de congestie controle door te geven aan BulkExecutor.  

   ```csharp
   // Set retry options high during initialization (default values).
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 30;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 9;

   IBulkExecutor bulkExecutor = new BulkExecutor(client, dataCollection);
   await bulkExecutor.InitializeAsync();

   // Set retries to 0 to pass complete control to bulk executor.
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 0;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 0;
   ```

5. De toepassing roept de BulkImportAsync-API aan. De .NET-bibliotheek biedt twee overbelasting van de API voor bulk import: een die een lijst met geserialiseerde JSON-documenten accepteert en de andere die een lijst met gedeserialiseerd POCO-documenten accepteert. Raadpleeg de [API-documentatie](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkimportasync?view=azure-dotnet&preserve-view=true)voor meer informatie over de definities van elk van deze overbelaste methoden.

   ```csharp
   BulkImportResponse bulkImportResponse = await bulkExecutor.BulkImportAsync(
     documents: documentsToImportInBatch,
     enableUpsert: true,
     disableAutomaticIdGeneration: true,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```
   **De methode BulkImportAsync accepteert de volgende para meters:**
   
   |**Parameter**  |**Beschrijving** |
   |---------|---------|
   |enableUpsert    |   Een markering voor het inschakelen van upsert-bewerkingen op de documenten. Als er al een document met de opgegeven ID bestaat, wordt het bijgewerkt. Deze waarde is standaard ingesteld op ONWAAR.      |
   |disableAutomaticIdGeneration    |    Een markering om het automatisch genereren van ID uit te scha kelen. Deze waarde is standaard ingesteld op True.     |
   |maxConcurrencyPerPartitionKeyRange    | De maximale graad van gelijktijdigheid per partitie sleutel bereik, ingesteld op NULL zorgt ervoor dat de tape wisselaar een standaard waarde van 20 gebruikt. |
   |maxInMemorySortingBatchSize     |  Het maximum aantal documenten dat wordt opgehaald uit de document-enumerator, die wordt door gegeven aan de API-aanroep in elke fase. Voor de sorteer fase in het geheugen die plaatsvindt voordat bulksgewijs wordt geïmporteerd, wordt door het instellen van deze para meter op null de standaard minimum waarde (Documents. Count, 1000000) gebruikt.       |
   |cancellationToken    |    Het annulerings token om de bulkimportbewerking zonder problemen af te sluiten.     |

   **Definitie van antwoord object voor bulk import** Het resultaat van de API-aanroep voor Bulk Import bevat de volgende kenmerken:

   |**Parameter**  |**Beschrijving**  |
   |---------|---------|
   |NumberOfDocumentsImported (lang)   |  Het totale aantal documenten dat is geïmporteerd uit de totale hoeveelheid documenten die is geleverd aan de API-aanroep voor bulk import.       |
   |TotalRequestUnitsConsumed (double)   |   Het totale aantal aanvraag eenheden (RU) dat wordt gebruikt door de API-aanroep voor bulk import.      |
   |TotalTimeTaken (time span)    |   De totale tijd die nodig is voor de API-aanroep voor bulk import om de uitvoering te volt ooien.      |
   |BadInputDocuments (lijst \<object> )   |     De lijst met documenten met onjuiste indeling die niet met succes zijn geïmporteerd in de API-aanroep voor bulk import. Los de geretourneerde documenten op en voer de import opnieuw uit. Documenten met een onjuiste indeling bevatten documenten waarvan de ID-waarde geen teken reeks is (null of een ander gegevens type wordt als ongeldig beschouwd).    |

## <a name="bulk-update-data-in-your-azure-cosmos-account"></a>Gegevens bulksgewijs bijwerken in uw Azure Cosmos-account

U kunt bestaande documenten bijwerken met behulp van de BulkUpdateAsync-API. In dit voor beeld stelt u het `Name` veld in op een nieuwe waarde en verwijdert u het `Description` veld uit de bestaande documenten. Raadpleeg de [API-documentatie](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet&preserve-view=true)voor de volledige set met ondersteunde update bewerkingen.

1. Ga naar de map ' BulkUpdateSample ' en open het bestand ' BulkUpdateSample. SLN '.  

2. Definieer de update-items samen met de bijbehorende veld Update bewerkingen. In dit voor beeld gaat u gebruiken `SetUpdateOperation` om het veld bij te werken `Name` en `UnsetUpdateOperation` het `Description` veld uit alle documenten te verwijderen. U kunt ook andere bewerkingen uitvoeren, zoals het ophogen van een document veld met een specifieke waarde, het pushen van specifieke waarden naar een matrix veld of het verwijderen van een specifieke waarde uit een matrix veld. Raadpleeg de [API-documentatie](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet&preserve-view=true)voor meer informatie over de verschillende methoden van de API voor bulksgewijs bijwerken.

   ```csharp
   SetUpdateOperation<string> nameUpdate = new SetUpdateOperation<string>("Name", "UpdatedDoc");
   UnsetUpdateOperation descriptionUpdate = new UnsetUpdateOperation("description");

   List<UpdateOperation> updateOperations = new List<UpdateOperation>();
   updateOperations.Add(nameUpdate);
   updateOperations.Add(descriptionUpdate);

   List<UpdateItem> updateItems = new List<UpdateItem>();
   for (int i = 0; i < 10; i++)
   {
    updateItems.Add(new UpdateItem(i.ToString(), i.ToString(), updateOperations));
   }
   ```

3. De toepassing roept de BulkUpdateAsync-API aan. Raadpleeg de [API-documentatie](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.ibulkexecutor.bulkupdateasync?view=azure-dotnet&preserve-view=true)voor meer informatie over de definitie van de BulkUpdateAsync-methode.  

   ```csharp
   BulkUpdateResponse bulkUpdateResponse = await bulkExecutor.BulkUpdateAsync(
     updateItems: updateItems,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```  
   **De methode BulkUpdateAsync accepteert de volgende para meters:**

   |**Parameter**  |**Beschrijving** |
   |---------|---------|
   |maxConcurrencyPerPartitionKeyRange    |   De maximale graad van gelijktijdigheid per partitie sleutel bereik. als deze para meter wordt ingesteld op NULL, wordt de tape wisselaar gebruikt voor het gebruik van de standaard waarde (20).   |
   |maxInMemorySortingBatchSize    |    Het maximum aantal update-items dat is opgehaald uit de enumerator voor update-items die zijn door gegeven aan de API-aanroep in elke fase. Voor de sorteer fase in het geheugen die vóór het bulksgewijs bijwerken plaatsvindt, wordt door het instellen van deze para meter op null de standaard minimum waarde (updateItems. Count, 1000000) gebruikt.     |
   | cancellationToken|Het annulerings token om de bewerking voor bulksgewijs bijwerken zonder problemen af te sluiten. |

   **Definitie van antwoord object voor bulk update** Het resultaat van de API-aanroep voor bulk updates bevat de volgende kenmerken:

   |**Parameter**  |**Beschrijving** |
   |---------|---------|
   |NumberOfDocumentsUpdated (lang)    |   Het aantal documenten dat is bijgewerkt met de totale hoeveelheid documenten die is geleverd aan de API-aanroep voor bulksgewijs bijwerken.      |
   |TotalRequestUnitsConsumed (double)   |    Het totale aantal aanvraag eenheden (RUs) dat wordt gebruikt door de API-aanroep voor bulk updates.    |
   |TotalTimeTaken (time span)   | De totale tijd die nodig is voor de API-aanroep voor bulk updates om de uitvoering te volt ooien. |
    
## <a name="performance-tips"></a>Tips voor prestaties 

Houd rekening met de volgende punten voor betere prestaties bij gebruik van de bibliotheek voor bulk-uitvoerder:

* Voor de beste prestaties voert u uw toepassing uit vanaf een virtuele machine van Azure die zich in dezelfde regio bevindt als de schrijf regio van uw Azure Cosmos-account.  

* Het is raadzaam om één `BulkExecutor` object voor de hele toepassing te instantiëren binnen één virtuele machine die overeenkomt met een specifieke Azure Cosmos-container.  

* Omdat één bulk bewerking van een API een groot deel van de CPU-en netwerk-i/o's van de client computer verbruikt (dit gebeurt door het uitvoeren van meerdere taken intern). Vermijd het starten van meerdere gelijktijdige taken binnen uw toepassings proces waarmee API-aanroepen voor bulk bewerkingen worden uitgevoerd. Als één bulk bewerking van een API-aanroep die wordt uitgevoerd op één virtuele machine, niet in staat is de gehele door Voer van de container te gebruiken (als de door Voer van de container > 1.000.000 RU/s) is, kunt u het beste afzonderlijke virtuele machines maken voor het gelijktijdig uitvoeren van de API-aanroepen voor bulk bewerkingen.  

* Zorg ervoor dat de `InitializeAsync()` methode wordt aangeroepen na het instantiëren van een BulkExecutor-object om de partitie toewijzing van de doel Cosmos-container op te halen.  

* Zorg ervoor dat **gcServer** is ingeschakeld voor betere prestaties In de App.Config van uw toepassing.
  ```xml  
  <runtime>
    <gcServer enabled="true" />
  </runtime>
  ```
* De bibliotheek verzendt traceringen die kunnen worden verzameld in een logboek bestand of in de-console. Als u beide wilt inschakelen, voegt u de volgende code toe aan het App.Config-bestand van uw toepassing.

  ```xml
  <system.diagnostics>
    <trace autoflush="false" indentsize="4">
      <listeners>
        <add name="logListener" type="System.Diagnostics.TextWriterTraceListener" initializeData="application.log" />
        <add name="consoleListener" type="System.Diagnostics.ConsoleTraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
  ```

## <a name="next-steps"></a>Volgende stappen

* Voor meer informatie over de details van het NuGet-pakket en de release opmerkingen raadpleegt u de informatie over de bulk-invoerder- [SDK](sql-api-sdk-bulk-executor-dot-net.md).
