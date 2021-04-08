---
title: Een Azure Cosmos DB-account naar een andere regio verplaatsen
description: Meer informatie over het verplaatsen van een Azure Cosmos DB-account naar een andere regio.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 09/12/2020
ms.author: mjbrown
ms.openlocfilehash: 29b5c5d3cf55cd11fe505c0d9ab9b894dc2ad267
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93342025"
---
# <a name="move-an-azure-cosmos-db-account-to-another-region"></a>Een Azure Cosmos DB-account naar een andere regio verplaatsen
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In dit artikel wordt beschreven hoe u het volgende kunt doen:

- Een regio verplaatsen waar gegevens worden gerepliceerd in Azure Cosmos DB.
- Migreer de meta gegevens en gegevens van het account (Azure Resource Manager) van de ene naar de andere regio.

## <a name="move-data-from-one-region-to-another"></a>Gegevens verplaatsen van de ene regio naar een andere

Azure Cosmos DB biedt standaard ondersteuning voor gegevens replicatie, zodat het verplaatsen van gegevens van de ene regio naar de andere eenvoudig is. U kunt dit doen met behulp van de Azure Portal, Azure PowerShell of de Azure CLI. Hiervoor moeten de volgende stappen worden uitgevoerd:

1. Voeg een nieuwe regio toe aan het account.

    Als u een nieuwe regio aan een Azure Cosmos DB account wilt toevoegen, raadpleegt u [regio's toevoegen/verwijderen aan een Azure Cosmos DB-account](how-to-manage-database-account.md#addremove-regions-from-your-database-account).

1. Voer een hand matige failover uit naar de nieuwe regio.

    Wanneer de regio die wordt verwijderd, momenteel de schrijf regio voor het account is, moet u een failover starten naar de nieuwe regio die u in de vorige stap hebt toegevoegd. Dit is een bewerking met een nul-uitval tijd. Als u een lees regio in een account met meerdere regio's wilt verplaatsen, kunt u deze stap overs Laan. 
    
    Zie [hand matige failover uitvoeren op een Azure Cosmos-account](how-to-manage-database-account.md#manual-failover)om een failover te starten.

1. De oorspronkelijke regio verwijderen.

    Als u een regio wilt verwijderen uit een Azure Cosmos DB account, raadpleegt u [regio's toevoegen/verwijderen uit uw Azure Cosmos DB-account](how-to-manage-database-account.md#addremove-regions-from-your-database-account).

## <a name="migrate-azure-cosmos-db-account-metadata"></a>Meta gegevens van Azure Cosmos DB-account migreren

Azure Cosmos DB biedt geen systeem eigen ondersteuning voor het migreren van meta gegevens van accounts vanuit de ene regio naar een andere. Als u de meta gegevens van het account en de klant gegevens van de ene naar de andere regio wilt migreren, moet u een nieuw account in de gewenste regio maken en de gegevens vervolgens hand matig kopiëren. 

Een bijna-nul-downtime-migratie voor de SQL API vereist het gebruik van de [wijzigings feed](change-feed.md) of een hulp programma dat deze gebruikt. Zie Opties voor het [migreren van uw on-premises of Cloud gegevens naar Azure Cosmos DB](cosmosdb-migrationchoices.md)als u de MONGODB-API, de Cassandra-API of een andere API migreert of meer informatie wilt over de opties voor het migreren van gegevens tussen accounts. 

De volgende stappen laten zien hoe u een Azure Cosmos DB-account voor de SQL-API en de bijbehorende gegevens van de ene regio naar een andere kunt migreren:

1. Maak een nieuw Azure Cosmos DB-account in de gewenste regio.

    Zie [een Azure Cosmos DB account maken](how-to-manage-database-account.md#create-an-account)voor meer informatie over het maken van een nieuw account via de Azure Portal, Power shell of de Azure cli.

1. Een nieuwe data base en container maken.

    Zie [een Azure Cosmos-container maken](how-to-create-container.md)als u een nieuwe data base en container wilt maken.

1. Gegevens migreren met behulp van het hulp programma voor de Azure Cosmos DB van Live gegevens migratie.

    Zie [Azure Cosmos DB hulp programma live data Migrator](https://github.com/Azure-Samples/azure-cosmosdb-live-data-migrator)om gegevens te migreren met een minimale downtime van nul.

1. De connection string van de toepassing bijwerken.

    Wanneer het hulp programma live data Migrator nog steeds wordt uitgevoerd, werkt u de verbindings gegevens bij in de nieuwe implementatie van uw toepassing. U kunt de eind punten en sleutels voor uw toepassing ophalen uit de Azure Portal.

    :::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-portal.png" alt-text="Toegangs beheer in de Azure Portal, waarbij u de NoSQL-database beveiliging kunt demonstreren.":::

1. Aanvragen omleiden naar de nieuwe toepassing.

    Nadat de nieuwe toepassing is verbonden met Azure Cosmos DB, kunt u client aanvragen omleiden naar uw nieuwe implementatie.

1. Verwijder alle resources die u niet meer nodig hebt.

    Met aanvragen die nu volledig worden omgeleid naar het nieuwe exemplaar, kunt u het oude Azure Cosmos DB-account en het hulp programma live data Migrator verwijderen.

## <a name="next-steps"></a>Volgende stappen

Lees de volgende artikelen voor meer informatie en voor beelden over het beheren van het Azure Cosmos-account en de data bases en containers:

* [Een Azure Cosmos-account beheren](how-to-manage-database-account.md)
* [Feed wijzigen in Azure Cosmos DB](change-feed.md)
