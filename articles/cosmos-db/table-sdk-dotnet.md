---
title: Azure Cosmos DB Table-API .NET SDK & resources
description: Meer informatie over de Azure Cosmos DB Table-API voor .NET, inclusief release datums, pensioen datums en wijzigingen die zijn aangebracht tussen elke versie.
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/17/2018
ms.custom: devx-track-dotnet
ms.openlocfilehash: 4bea9f202d40090217219752a13ffde64b466140
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104597572"
---
# <a name="azure-cosmos-db-table-net-api-download-and-release-notes"></a>Azure Cosmos DB Table .NET API: Download-en release-opmerkingen
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

> [!div class="op_single_selector"]
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   | Koppelingen  |
|---|---|
|**SDK downloaden**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table)|
|**Snelstartgids**|[Azure Cosmos DB: een app bouwen met .NET en de Table-API](create-table-dotnet.md)|
|**Zelfstudie**|[Azure Cosmos DB: ontwikkelen met de tabel-API in .NET](tutorial-develop-table-dotnet.md)|
|**Huidig ondersteund framework**|[Microsoft .NET Framework 4.5.1](https://www.microsoft.com/en-us/download/details.aspx?id=40779)|

> [!IMPORTANT]
> De .NET Framework SDK [Microsoft.Azure.CosmosDB.Table ](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) bevindt zich in de onderhoudsmodus en wordt binnenkort beëindigd. Voer een upgrade uit naar de nieuwe .NET Standaard bibliotheek [Microsoft.Azure.Cosmos.table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) om door te gaan met de nieuwste functies die door de Table-API worden ondersteund.

> Als u tijdens de preview een tabel-API-account hebt gemaakt, moet u een [nieuw tabel-API-account](create-table-dotnet.md#create-a-database-account) maken om te kunnen werken met de algemeen beschikbare SDK’s voor tabel-API's.
>

## <a name="release-notes"></a>Releaseopmerkingen

### <a name="212"></a><a name="2.1.2"></a>omschreven

* Opgeloste fouten

### <a name="210"></a><a name="2.1.0"></a>2.1.0

* Opgeloste fouten

### <a name="200"></a><a name="2.0.0"></a>2.0.0

* Ondersteuning voor het schrijven van meerdere regio's toegevoegd
* Afhankelijkheden van NuGet-pakketten op Microsoft.Azure.DocumentDB, micro soft. OData. core, micro soft. OData. EDM, micro soft. Spatial

### <a name="113"></a><a name="1.1.3"></a>1.1.3

* Afhankelijkheden van NuGet-pakketten op micro soft. Azure. storage. common en Microsoft.Azure.DocumentDB.
* Fout oplossingen voor tabel-serialisatie wanneer JsonConvert. DefaultSettings worden geconfigureerd.

### <a name="111"></a><a name="1.1.1"></a>1.1.1

* Validatie toegevoegd voor onjuiste ETAGs in directe modus.
* Vaste LINQ-query fout in de gateway modus.
* Synchrone Api's worden nu uitgevoerd in de thread groep met SynchronizationContext.

### <a name="110"></a><a name="1.1.0"></a>1.1.0

* TableQueryMaxItemCount, TableQueryEnableScan, TableQueryMaxDegreeOfParallelism en TableQueryContinuationTokenLimitInKb toevoegen aan TableRequestOptions
* Opgeloste fouten

### <a name="100"></a><a name="1.0.0"></a>1.0.0

* Release van algemene Beschik baarheid

### <a name="090-preview"></a><a name="0.1.0-preview"></a>0.9.0-Preview

* Eerste preview-release

## <a name="release-and-retirement-dates"></a>Release-en pensioen datums

Micro soft biedt een kennisgeving van ten minste **twaalf maanden** voor het buiten gebruik stellen van een SDK om de overgang naar een nieuwere/ondersteunde versie te versoepelen.

De `Microsoft.Azure.CosmosDB.Table` bibliotheek is momenteel alleen beschikbaar voor .NET Framework en bevindt zich in de onderhouds modus en zal binnenkort worden afgeschaft. Nieuwe functies en functionaliteiten en Optima Lise ringen worden alleen toegevoegd aan de .NET Standard-bibliotheek [micro soft. Azure. Cosmos. table.](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)het wordt aanbevolen dat u een upgrade uitvoert naar [micro soft. Azure. Cosmos. table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table).

Het preview-pakket voor [WindowsAzure. Storage-PremiumTable](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable/0.1.0-preview) is afgeschaft. De SDK voor WindowsAzure. Storage-PremiumTable wordt op 15 november 2018 ingetrokken, op het moment dat de aanvragen voor de buiten gebruik gestelde SDK niet worden toegestaan.

| Versie | Releasedatum | Buitengebruikstellingsdatum |
| --- | --- | --- |
| [omschreven](#2.1.2) |16 september 2019| |
| [2.1.0](#2.1.0) |22 januari 2019|01 april 2020 |
| [2.0.0](#2.0.0) |26 september 2018|01 maart 2020 |
| [1.1.3](#1.1.3) |17 juli 2018|01 december 2019 |
| [1.1.1](#1.1.1) |26 maart 2018|01 december 2019 |
| [1.1.0](#1.1.0) |21 februari 2018|01 december 2019 |
| [1.0.0](#1.0.0) |15 november 2017|15 november 2019 |
| 0.9.0-Preview |11 november 2017 |11 november 2019 |

## <a name="troubleshooting"></a>Problemen oplossen

Als u de fout melding krijgt 

```
Unable to resolve dependency 'Microsoft.Azure.Storage.Common'. Source(s) used: 'nuget.org', 
'CliFallbackFolder', 'Microsoft Visual Studio Offline Packages', 'Microsoft Azure Service Fabric SDK'`
```

Wanneer u probeert het pakket micro soft. Azure. CosmosDB. table NuGet te gebruiken, hebt u twee opties om het probleem op te lossen:

* Gebruik de console pakket beheer om het pakket micro soft. Azure. CosmosDB. table en de bijbehorende afhankelijkheden te installeren. Hiertoe typt u het volgende in de Package Manager-console voor uw oplossing. 

    ```powershell
    Install-Package Microsoft.Azure.CosmosDB.Table -IncludePrerelease
    ```

    
* Gebruik uw favoriete NuGet-pakket beheer programma om het pakket micro soft. Azure. storage. common NuGet te installeren voordat u micro soft. Azure. CosmosDB. table installeert.

## <a name="faq"></a>Veelgestelde vragen

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zie ook

Zie [Inleiding tot Azure Cosmos DB Table-API](table-introduction.md)voor meer informatie over de Azure Cosmos db table-API. 
