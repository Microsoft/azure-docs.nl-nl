---
title: Verbind Qlik Sense om uw gegevens te Azure Cosmos DB en te visualiseren
description: In dit artikel worden de stappen beschreven die nodig zijn om Azure Cosmos DB te verbinden met Qlik Sense en uw gegevens te visualiseren.
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
author: SnehaGunda
ms.author: sngun
ms.topic: how-to
ms.date: 05/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 2401786a82b6a3e5bf6c6a893a8e7cd3656f3402
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95996951"
---
# <a name="connect-qlik-sense-to-azure-cosmos-db-and-visualize-your-data"></a>Verbind Qlik Sense om uw gegevens te Azure Cosmos DB en te visualiseren
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Qlik Sense is een hulp programma voor gegevens visualisatie waarmee gegevens uit verschillende bronnen worden gecombineerd in één weer gave. Qlik Sense indexeert elke mogelijke relatie in uw gegevens, zodat u direct inzicht kunt krijgen in de gegevens. U kunt Azure Cosmos DB gegevens visualiseren met behulp van Qlik Sense. In dit artikel worden de stappen beschreven die nodig zijn om Azure Cosmos DB te verbinden met Qlik Sense en uw gegevens te visualiseren. 

> [!NOTE]
> Het verbinden van Qlik Sense met Azure Cosmos DB wordt momenteel alleen ondersteund voor de SQL-API en de Azure Cosmos DB-API voor MongoDB-accounts.

U kunt Qlik Sense verbinden met Azure Cosmos DB:

* Cosmos DB SQL-API met behulp van de ODBC-Connector.

* De API van Azure Cosmos DB voor MongoDB met behulp van de Qlik Sense MongoDB-connector (momenteel als preview-versie).

* De API van Azure Cosmos DB voor MongoDB en SQL API met behulp van REST API-connector in Qlik Sense.

* Cosmos DB Mongo DB API met behulp van de gRPC-connector voor Qlik core.
In dit artikel worden de details van de verbinding met de Cosmos DB SQL-API beschreven met behulp van de ODBC-Connector.

In dit artikel worden de details van de verbinding met de Cosmos DB SQL-API beschreven met behulp van de ODBC-Connector.

## <a name="prerequisites"></a>Vereisten

Voordat u de instructies in dit artikel volgt, moet u ervoor zorgen dat u de volgende resources gereed hebt:

* Down load het [Qlik Sense-bureau blad](https://www.qlik.com/us/try-or-buy/download-qlik-sense) of stel Qlik Sense in Azure in door [het Qlik Sense Marketplace-item te installeren](https://azuremarketplace.microsoft.com/marketplace/apps/qlik.qlik-sense).

* Down load de [video game gegevens](https://www.kaggle.com/gregorut/videogamesales), deze voorbeeld gegevens hebben de CSV-indeling. U kunt deze gegevens in een Cosmos DB-account opslaan en deze visualiseren in Qlik Sense.

* Maak een Azure Cosmos DB SQL-API-account met behulp van de stappen in de sectie [een account maken](create-sql-api-dotnet.md#create-account) van het artikel Quick Start.

* [Een Data Base en verzameling maken](create-sql-api-java.md#add-a-container) : u kunt de doorvoer waarde van de verzameling instellen op 1000 ru/s. 

* Laad de verkoop gegevens van het voor beeld-video spel naar uw Cosmos DB-account. U kunt de gegevens importeren met Azure Cosmos DB hulp programma voor gegevens migratie, u kunt een [sequentieel](import-data.md#SQLSeqTarget) of een [bulksgewijze import](import-data.md#SQLBulkTarget) van gegevens uitvoeren. Het duurt ongeveer 3-5 minuten voordat de gegevens naar het Cosmos DB-account worden geïmporteerd.

* Down load, installeer en configureer het ODBC-stuur programma met behulp van de stappen in het artikel [verbinding maken met Cosmos DB met een ODBC-stuur programma](odbc-driver.md) . De video game gegevens zijn een eenvoudige gegevensset en u hoeft het schema niet te bewerken. gebruik gewoon het standaard schema voor verzamelings toewijzing.

## <a name="connect-qlik-sense-to-cosmos-db"></a>Qlik Sense verbinden met Cosmos DB

1. Open Qlik Sense en selecteer **nieuwe app maken**. Geef een naam op voor uw app en selecteer **maken**.

   :::image type="content" source="./media/visualize-qlik-sense/create-new-qlik-sense-app.png" alt-text="Een nieuwe Qlik Sense-app maken":::

2. Nadat de nieuwe app is gemaakt, selecteert u **app openen** en kiest u **gegevens toevoegen uit bestanden en andere bronnen**. 

3. Selecteer in de gegevens bronnen **ODBC** om het venster nieuwe verbinding instellen te openen. 

4. Schakel over naar de **gebruikers-DSN** en kies de ODBC-verbinding die u eerder hebt gemaakt. Geef een naam op voor de verbinding en selecteer **maken**. 

   :::image type="content" source="./media/visualize-qlik-sense/create-new-connection.png" alt-text="Een nieuwe verbinding maken":::

5. Nadat u de verbinding hebt gemaakt, kunt u de data base kiezen, verzamelen waar de video game gegevens zich bevinden en vervolgens een voor beeld bekijken.

   :::image type="content" source="./media/visualize-qlik-sense/choose-database-and-collection.png" alt-text="De data base en verzameling kiezen"::: 

6. Selecteer vervolgens **gegevens toevoegen** om de gegevens naar Qlik Sense te laden. Nadat u gegevens naar Qlik Sense hebt geladen, kunt u inzichten genereren en analyses uitvoeren op de gegevens. U kunt de inzichten gebruiken of uw eigen app bouwen met het verkennen van de omzet voor video games. In de volgende afbeelding ziet u 

   :::image type="content" source="./media/visualize-qlik-sense/visualize-data.png" alt-text="Gegevens visualiseren":::

### <a name="limitations-when-connecting-with-odbc"></a>Beperkingen bij het maken van verbinding met ODBC 

Cosmos DB is een schema-less gedistribueerde data base met stuur Programma's die zijn gemodelleerd rond ontwikkelaars behoeften. Het ODBC-stuur programma vereist een Data Base met schema voor het afleiden van kolommen, hun gegevens typen en andere eigenschappen. De normale SQL-query of de DML-syntaxis met relationele functionaliteit is niet van toepassing op Cosmos DB SQL-API, omdat SQL API geen ANSI SQL is. Om die reden worden de SQL-instructies die zijn uitgegeven via het ODBC-stuur programma, omgezet in Cosmos DB-specifieke SQL-syntaxis die geen equivalenten heeft voor alle constructs. Als u deze Vertaal problemen wilt voor komen, moet u een schema Toep assen bij het instellen van de ODBC-verbinding. In het artikel [Connect with ODBC driver](odbc-driver.md) vindt u suggesties en methoden om u te helpen bij het configureren van het schema. Zorg ervoor dat u deze toewijzing maakt voor elke data base/verzameling binnen het Cosmos DB-account.

## <a name="next-steps"></a>Volgende stappen

Als u een ander visualisatie programma gebruikt, zoals Power BI, kunt u er verbinding mee maken met behulp van de instructies in het volgende document:

* [Cosmos DB gegevens visualiseren met behulp van de Power BI-connector](powerbi-visualize.md)
