---
title: Ondersteuning voor Azure-tabelopslag in Azure Cosmos DB
description: Leer hoe de tabel-API van Azure Cosmos DB en Azure-opslagtabellen samenwerken door hetzelfde tabelgegevensmodel en dezelfde bewerkingen te delen
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 01/08/2021
author: sakash279
ms.author: akshanka
ms.reviewer: sngun
ms.openlocfilehash: c2b2a9a03a654e8ccba58e62a1d017cd11db5abc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98050750"
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Ontwikkelen met de tabel-API van Azure Cosmos DB en Azure-tabelopslag
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

De tabel-API van Azure Cosmos DB en Azure-tabelopslag hebben hetzelfde gegevensmodel voor tabellen en bieden via de bijbehorende SDK’s dezelfde bewerkingen voor maken, verwijderen, bijwerken en uitvoeren van query’s.

> [!NOTE]
> De [modus met serverloze capaciteit](serverless.md) is nu beschikbaar in de Table-API van Azure Cosmos DB.

[!INCLUDE [storage-table-cosmos-comparison](../../includes/storage-table-cosmos-comparison.md)]

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Ontwikkelen met de tabel-API van Azure Cosmos DB

Op dit moment beschikt de [tabel-API van Azure Cosmos DB](table-introduction.md) over vier SDK’s voor ontwikkelen: 

* [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table): .NET SDK. Deze bibliotheek is gericht op .NET Standard en heeft dezelfde klassen en methodes als de openbare [Windows Azure Storage SDK](https://www.nuget.org/packages/WindowsAzure.Storage), maar biedt ook de mogelijkheid om verbinding te maken met Azure Cosmos DB-accounts met behulp van de Table-API. Gebruiker van de .NET Framework-bibliotheek [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table/) wordt aangeraden om een upgrade naar [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) uit te voeren, aangezien deze zich in de onderhoudsmodus bevindt en binnenkort buiten gebruik wordt gesteld.

* [Python SDK](table-sdk-python.md): De nieuwe Python SDK voor Azure Cosmos DB is de enige SDK die ondersteuning biedt voor Azure-tabelopslag in Python. Deze SDK is verbonden met zowel Azure-tabelopslag als met de tabel-API van Azure Cosmos DB.

* [Java SDK](table-sdk-java.md): Met deze Azure Storage-SDK kan verbinding worden gemaakt met Azure Cosmos DB-accounts met behulp van de tabel-API.

* [Node.js SDK](table-sdk-nodejs.md): Met deze Azure Storage-SDK kan verbinding worden gemaakt met Azure Cosmos DB-accounts met behulp van de tabel-API.


Aanvullende informatie over het werken met de tabel-API is beschikbaar in het artikel [Veelgestelde vragen: Ontwikkelen met de tabel-API](table-api-faq.md).

## <a name="developing-with-azure-table-storage"></a>Ontwikkelen met Azure-tabelopslag

Azure-tabelopslag beschikt over deze SDK’s voor ontwikkelen:

- De bibliotheken [Microsoft.Azure.Storage.Blob](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/), [Microsoft.Azure.Storage.File](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/), [Microsoft.Azure.Storage.Queue](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/), en [Microsoft.Azure.Storage.Common](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/) stellen u in staat om te werken met de service Azure-tabelopslag. Als u de tabel-API in Azure Cosmos DB gebruikt, kunt u in plaats hiervan de bibliotheek [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table/) gebruiken.
- [Python SDK](https://github.com/Azure/azure-cosmos-table-python). De Azure Cosmos DB Table SDK voor Python biedt ondersteuning voor de tabelopslagservice (omdat Azure-tabelopslag en de tabel-API van Cosmos DB dezelfde functies en functionaliteit delen, en in een poging om onze SDK-ontwikkelingsinspanningen te ontbinden, raden we aan deze SDK te gebruiken).
- [Azure Storage SDK voor Java](https://github.com/azure/azure-storage-java). Deze Azure Storage-SDK biedt een clientbibliotheek in Java voor het gebruik van Azure-tabelopslag.
- [Node.js SDK](https://github.com/Azure/azure-storage-node). Deze SDK biedt een Node.js-pakket en een JavaScript-clientbibliotheek die compatibel is met de browser, voor het gebruik van de opslagtabelservice.
- [AzureRmStorageTable PowerShell-module](https://www.powershellgallery.com/packages/AzureRmStorageTable). Deze PowerShell-module bevat cmdlets voor het werken met opslagtabellen.
- [Azure Storage-clientbibliotheek voor C++](https://github.com/Azure/azure-storage-cpp/). Met deze bibliotheek kunt u toepassingen bouwen in Azure Storage.
- [Clientbibliotheek voor Azure-opslagtabellen voor Ruby](https://github.com/azure/azure-storage-ruby/tree/master/table). Dit project biedt een Ruby-pakket waarmee u eenvoudig toegang kunt krijgen tot Azure-opslagtabelservices.
- [PHP-clientbibliotheek voor Azure-opslagtabellen](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table). Dit project biedt een PHP-clientbibliotheek waarmee u eenvoudig toegang kunt krijgen tot Azure-opslagtabelservices.


   





