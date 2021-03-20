---
title: Ingebouwde notebook opdrachten en-functies gebruiken in Azure Cosmos DB C#-notitie blokken (preview-versie)
description: Meer informatie over het gebruik van ingebouwde opdrachten en functies voor het uitvoeren van algemene bewerkingen met behulp van de ingebouwde C#-notebooks van Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/19/2020
ms.author: dech
ms.custom: devx-track-python, devx-track-csharp
ms.openlocfilehash: 67fe71d8e2d6ab239989cb30e9bf5a1b4d731037
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93340472"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db-c-notebooks-preview"></a>Ingebouwde notebook opdrachten en-functies gebruiken in Azure Cosmos DB C#-notitie blokken (preview-versie)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Met ingebouwde Jupyter-notebooks in Azure Cosmos DB kunt u uw gegevens van de Azure Portal analyseren en visualiseren. In dit artikel wordt beschreven hoe u ingebouwde notebook opdrachten en-functies gebruikt voor veelvoorkomende bewerkingen in C#-notebooks.

## <a name="install-a-new-nuget-package"></a>Een nieuw NuGet-pakket installeren
Nadat u de ondersteuning voor notebooks voor uw Azure Cosmos-accounts hebt ingeschakeld, kunt u een nieuw notitie blok openen en een pakket installeren.

In een nieuwe code-cel voegt u de volgende code in en voert u deze uit, vervangt u door ``PackageToBeInstalled`` het gewenste NuGet-pakket en ``optionalVersion`` met een specifieke versie van het pakket, indien gewenst. 

```csharp
#r "nuget: PackageToBeInstalled, optionalVersion"
```

U kunt meerdere NuGet-pakketten in dezelfde cel installeren. Pakketten kunnen worden gebruikt vanuit elk notebook in de Azure Cosmos-account werkruimte. 

Op dit moment biedt de C#-notebook-werk ruimte geen ondersteuning voor recursieve omzetting van NuGet-pakketten. Als een NuGet-pakket afhankelijkheden heeft van andere NuGet-pakketten die momenteel niet zijn geïnstalleerd, moet u deze expliciet samen met het bovenliggende pakket raadplegen.

> [!TIP]
> Als uw notitie blok een aangepast pakket vereist, raden we u aan een cel aan uw notitie blok toe te voegen om het pakket te installeren en de eerste cel te maken. Dit verkleint de kans op conflicten met andere pakketten die standaard worden Azure Cosmos DB. Het is ook eenvoudig om de pakketten opnieuw te installeren als u [de werk ruimte opnieuw instelt](#reset-notebooks-workspace), waardoor alle pakketten worden verwijderd. 

## <a name="use-the-built-in-azure-cosmos-db-net-sdk"></a>De ingebouwde Azure Cosmos DB .NET SDK gebruiken
Versie 3 van de [Azure Cosmos db .NET SDK voor SQL API](https://github.com/Azure/azure-cosmos-dotnet-v3) is geïnstalleerd en opgenomen in de notebook-omgeving voor het Azure Cosmos-account.

Maak een instantie van ``CosmosClient`` om een SDK-bewerking uit te voeren. 

Bijvoorbeeld:

```csharp
// Include usings
using System;
using Microsoft.Azure.Cosmos; //namespace for Azure Cosmos DB .NET V3 SDK
using System.Collections;

// Initialize a new instance of CosmosClient using the built-in account endpoint and key parameters
CosmosClient cosmosClient = new CosmosClient(Cosmos.Endpoint, Cosmos.Key);

// Create a new database and container with 400 RU/s
Database database = await cosmosClient.CreateDatabaseIfNotExistsAsync("DatabaseName");
Container container = await database.CreateContainerIfNotExistsAsync("ContainerName", "/partitionKey", 400);
```
Zie de [.net v3 SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage)-voor beelden voor meer informatie. 

> [!IMPORTANT]
> De ingebouwde Azure Cosmos DB .NET SDK wordt alleen ondersteund voor SQL-(core) API-accounts. Voor andere Api's moet u [het relevante .net-stuur programma installeren](#install-a-new-nuget-package) dat overeenkomt met de API. 

## <a name="set-custom-options-using-cosmosclientoptions"></a>Aangepaste opties instellen met ``CosmosClientOptions``
Voor meer flexibiliteit kunt u de aangepaste eigenschap instellen ``CosmosClientOptions`` en deze door geven in uw ``CosmosClient`` exemplaar. U kunt deze eigenschap gebruiken voor het volgende:

- Stel een toepassings naam in het achtervoegsel van de gebruikers agent in om deze in elke aanvraag op te vragen.
- Stel de voorkeurs regio in die moet worden gebruikt bij het uitvoeren van bewerkingen voor de service.
- Stel een aangepast beleid voor opnieuw proberen in voor het afhandelen van frequentie-beperkte aanvragen.

Zie het artikel over de [COSMOSCLIENTOPTIONS API-verwijzing](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions) voor alle ondersteunde instellingen. Hier volgt een voor beeld waarin wordt uitgelegd hoe u de `cosmosClientOptions` eigenschap instelt:

```csharp
using Microsoft.Azure.Cosmos;

// Configure CosmosClientOptions
var cosmosClientOptions = new CosmosClientOptions
{
    ApplicationName = "ContosoNotebookAppName",
    ApplicationRegion = Regions.RegionName, // By default, this is the region you first created your account in
    MaxRetryAttemptsOnRateLimitedRequests = 9, // By default, this value is 9
};

var client = new CosmosClient(Cosmos.Endpoint, Cosmos.Key, cosmosClientOptions);
```

## <a name="access-the-account-endpoint-and-primary-key-variables"></a>Het account eindpunt en de primaire-sleutel variabelen openen
U hebt toegang tot het ingebouwde eind punt en de sleutel van het Azure Cosmos-account waar uw notebook zich bevindt.

```csharp
var key = Cosmos.Key;
var endpoint = Cosmos.Endpoint;
```

> [!IMPORTANT]
> De ``Cosmos.Key`` ``Cosmos.Endpoint`` variabelen en zijn alleen van toepassing op de SQL-API. Voor andere Api's zoekt u het eind punt en de sleutel in de **verbindings reeksen** of de Blade **sleutels** in uw Azure Cosmos-account.  

## <a name="print-console-output-in-c-code"></a>Uitvoer van de printer console in C#-code
In de C#-code kunt u de syntaxis display. AsMarkdown () met [teken reeks-interpolatie](/dotnet/csharp/language-reference/tokens/interpolated) gebruiken om de uitvoer van de console af te drukken die wordt weer gegeven wanneer u de cel uitvoert. 

Bijvoorbeeld: 

```csharp
// Print text in the output
Display.AsMarkdown($"Hello world!");

// Print a variable in the output
for (int i = 0; i < 5; i++) {
    Display.AsMarkdown($"Printing out variable: {i}");
}
```
> [!IMPORTANT]
> De syntaxis van console. WriteLine () wordt momenteel niet ondersteund in C#-notebooks. Gebruik display. AsMarkdown om de uitvoer van de console vanuit uw programma af te drukken. 

## <a name="use-built-in-nteract-data-explorer"></a>Ingebouwde nteract Data Explorer gebruiken
U kunt de ingebouwde [nteract Data Explorer](https://blog.nteract.io/designing-the-nteract-data-explorer-f4476d53f897) gebruiken om een verzameling items te filteren en te visualiseren. In een cel plaatst u de variabele die u wilt visualiseren in de laatste regel, die automatisch wordt weer gegeven in nteract wanneer u de cel uitvoert.

In het *GetingStarted_Csharp. ipynb* -voor beeld kunnen we de variabele afdrukken met het resultaat, de ``telemetryEvents`` . Zie de [GettingStarted_Csharp. ipynb-notebook](https://github.com/Azure-Samples/cosmos-notebooks/blob/master/CSharp_quickstarts/GettingStarted_CSharp.ipynb) voor het hele voor beeld. 

:::image type="content" source="media/use-notebook-features-and-commands/csharp-query-cell.png" alt-text="Cel csharp query":::

:::image type="content" source="media/use-notebook-features-and-commands/csharp-nteract-built-in-chart.png" alt-text="nteract Data Explorer":::

## <a name="use-built-in-dictionary-viewer"></a>Ingebouwde woordenlijst viewer gebruiken
U kunt de ingebouwde woordenlijst viewer gebruiken om een variabele weer te geven. In een cel plaatst u de variabele die u wilt visualiseren in de laatste regel, die automatisch wordt weer gegeven wanneer de cel wordt uitgevoerd.

:::image type="content" source="media/use-notebook-features-and-commands/csharp-built-in-dictionary-viewer.png" alt-text="Ingebouwde woordenlijst viewer":::

## <a name="upload-json-items-to-a-container"></a>JSON-items uploaden naar een container
U kunt de ``%%upload`` opdracht Magic gebruiken om gegevens van een JSON-bestand te uploaden naar een opgegeven Azure Cosmos-container. Gebruik de volgende opdracht om de items te uploaden:

```csharp
%%upload --databaseName {database_id} --containerName {container_id} --url {url_location_of_file}
```

- Vervang ``{database_id}`` en door ``{container_id}`` de naam van de data base en de container in uw Azure Cosmos-account. 
- Vervang door ``{url_location_of_file}`` de locatie van het JSON-bestand. Het bestand moet een matrix met geldige JSON-objecten zijn en moet toegankelijk zijn via het open bare Internet.

Bijvoorbeeld:

```csharp
%%upload --database databaseName --container containerName --url 
https://contoso.com/path/to/data.json
```
```
Documents successfully uploaded to ContainerName
Total number of documents imported : 2654
Total time taken : 00:00:38.1228087 hours
Total RUs consumed : 25022.58
```

Met de uitvoer statistieken kunt u de daad werkelijke RU/s berekenen die worden gebruikt voor het uploaden van de items. Als 25.000 RUs bijvoorbeeld meer dan 38 seconden is verbruikt, is de daad werkelijke RU/s 25.000 RUs/38 seconden = 658 RU/s.

## <a name="run-another-notebook-in-current-notebook"></a>Een ander notitie blok in het huidige notitie blok uitvoeren 
U kunt de ``%%run`` opdracht Magic gebruiken om een ander notitie blok in uw werk ruimte uit te voeren vanuit uw huidige notitie blok. Gebruik de syntaxis:

```csharp
%%run ./path/to/{notebookName}.ipynb
```
Vervang door ``{notebookName}`` de naam van het notitie blok dat u wilt uitvoeren. Het notitie blok moet zich in de huidige werk ruimte mijn notitie blokken bevinden. 

## <a name="reset-notebooks-workspace"></a>Werk ruimte notitie blokken opnieuw instellen
Als u de werk ruimte notitie blokken opnieuw wilt instellen op de standaard instellingen, selecteert u **werk ruimte opnieuw instellen** op de opdracht balk. Hiermee worden alle aangepaste geïnstalleerde pakketten verwijderd en wordt de Jupyter-server opnieuw gestart. Uw notitie blokken, bestanden en Azure Cosmos-resources worden niet beïnvloed.  

:::image type="content" source="media/use-notebook-features-and-commands/reset-workspace.png" alt-text="Werk ruimte notitie blokken opnieuw instellen":::

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de voor delen van [Azure Cosmos DB Jupyter-notebooks](cosmosdb-jupyter-notebooks.md)
- Meer informatie over de [Azure Cosmos db .NET SDK voor SQL API](https://github.com/Azure/azure-cosmos-dotnet-v3)
