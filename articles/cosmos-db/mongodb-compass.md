---
title: Verbinding maken met Azure Cosmos DB met behulp van kompas
description: Leer hoe u MongoDB-kompas kunt gebruiken om gegevens op te slaan en te beheren in Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 06/05/2020
author: christopheranderson
ms.author: chrande
ms.openlocfilehash: 43bcd54955cb1a8aaf08785368faf13c14f8322c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94413051"
---
# <a name="use-mongodb-compass-to-connect-to-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB-kompas gebruiken om verbinding te maken met de API van Azure Cosmos DB voor MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

In deze zelf studie wordt gedemonstreerd hoe u [MongoDb kompas](https://www.mongodb.com/products/compass) kunt gebruiken bij het opslaan en/of beheren van gegevens in Cosmos db. We gebruiken de Azure Cosmos DB-API voor MongoDB voor deze procedure. Voor degenen die niet bekend zijn, is kompas een GUI voor MongoDB. Het wordt meestal gebruikt voor het visualiseren van uw gegevens, het uitvoeren van ad-hoc query's en het beheren van uw gegevens.

Cosmos DB is de wereld wijd gedistribueerde multi-model database service van micro soft. U kunt snel databases maken van documenten, sleutel/waarde-paren en grafieken en hier query's op uitvoeren. Deze databases genieten allemaal het voordeel van de globale distributie en horizontale schaalmogelijkheden die ten grondslag liggen aan Cosmos DB.

## <a name="pre-requisites"></a>Vereisten

Als u verbinding wilt maken met uw Cosmos DB-account met behulp van MongoDB-kompas, moet u het volgende doen:

* Een [kompas](https://www.mongodb.com/download-center/compass?jmp=hero) downloaden en installeren
* Informatie over uw Cosmos DB [Connection String](connect-mongodb-account.md)

## <a name="connect-to-cosmos-dbs-api-for-mongodb"></a>Verbinding maken met de API van Cosmos DB voor MongoDB

Als u uw Cosmos DB-account wilt verbinden met een kompas, kunt u de onderstaande stappen volgen:

1. Haal de verbindings gegevens op voor uw Cosmos-account dat is geconfigureerd met de API-MongoDB van Azure Cosmos DB met behulp van de instructies [hier](connect-mongodb-account.md).

    :::image type="content" source="./media/mongodb-compass/mongodb-compass-connection.png" alt-text="Scherm afbeelding van de Blade connection string":::

2. Klik op de knop met de tekst **kopiëren naar het klem bord** naast uw **primaire/secundaire Connection String** in Cosmos db. Als u op deze knop klikt, wordt uw hele connection string naar het klem bord gekopieerd.

    :::image type="content" source="./media/mongodb-compass/mongodb-connection-copy.png" alt-text="Scherm afbeelding van de knop kopiëren naar klem bord":::

3. Open kompas op het bureau blad of de computer en klik op **verbinding maken** en vervolgens **verbinding maken met...**.

4. Met kompas wordt automatisch een connection string in het klem bord gedetecteerd en wordt u gevraagd of u wilt gebruiken om verbinding te maken. Klik op **Ja** , zoals wordt weer gegeven in de onderstaande scherm afbeelding.

    :::image type="content" source="./media/mongodb-compass/mongodb-compass-detect.png" alt-text="Scherm afbeelding toont een dialoog venster waarin wordt uitgelegd dat uw klem bord een connection string bevat.":::

5. Wanneer u op **Ja** klikt in de bovenstaande stap, worden uw details van de Connection String automatisch ingevuld. Verwijder de waarde die automatisch is ingevuld in het veld **naam van replicaset** om ervoor te zorgen dat deze leeg blijft.

    :::image type="content" source="./media/mongodb-compass/mongodb-compass-replica.png" alt-text="Scherm afbeelding toont het tekstvak naam van replicaset.":::

6. Klik onder aan de pagina op **verbinding maken** . Uw Cosmos DB-account en data bases moeten nu worden weer gegeven in MongoDB-kompas.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [gebruik van Studio 3T](mongodb-mongochef.md) met de API voor MongoDB van Azure Cosmos DB.
- Verken [voorbeelden](mongodb-samples.md) van MongoDB met de API van Azure Cosmos DB voor MongoDB.
