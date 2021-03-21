---
title: Robo 3T gebruiken gebruiken om verbinding te maken met Azure Cosmos DB
description: Meer informatie over hoe u verbinding maakt met Azure Cosmos DB met behulp van Robo 3T gebruiken en de API van Azure Cosmos DB voor MongoDB
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/23/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: e096de5eea9bf5bbd47b976976192e62eb6aa33e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93096407"
---
# <a name="use-robo-3t-with-azure-cosmos-dbs-api-for-mongodb"></a>Robo 3T gebruiken met de Azure Cosmos DB-API voor MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Als u verbinding wilt maken met een Cosmos-account via Robo 3T gebruiken, moet u het volgende doen:

* Down load en Installeer [Robo 3T gebruiken](https://robomongo.org/)
* Informatie over uw Cosmos DB [Connection String](connect-mongodb-account.md)

> [!NOTE]
> Momenteel worden Robo 3T gebruiken v 1.2 en lagere versies ondersteund met de API van Cosmos DB voor MongoDB.

## <a name="connect-using-robo-3t"></a>Verbinding maken via Robo 3T

Voer de volgende stappen uit om uw Cosmos-account toe te voegen aan de Robo 3T gebruiken-verbindings beheer:

1. Haal de verbindings gegevens op voor uw Cosmos-account dat is geconfigureerd met de API-MongoDB van Azure Cosmos DB met behulp van de instructies [hier](connect-mongodb-account.md).

    :::image type="content" source="./media/mongodb-robomongo/connectionstringblade.png" alt-text="Scherm afbeelding van de Blade connection string":::
2. Voer de toepassing *Robomongo* uit.

3. Klik op de knop verbinding onder **bestand** om uw verbindingen te beheren. Klik vervolgens op **maken** in het venster **MongoDb-verbindingen** , waarmee het venster **Verbindings instellingen** wordt geopend.

4. Kies in het venster **Verbindings instellingen** een naam. Zoek vervolgens de **host** en **poort** uit uw verbindings gegevens in stap 1 en geef deze op in respectievelijk **adres** en **poort**.

    :::image type="content" source="./media/mongodb-robomongo/manageconnections.png" alt-text="Scherm afbeelding van de Robomongo-verbindingen beheren":::
5. Klik op het tabblad **verificatie** op **verificatie uitvoeren**. Voer vervolgens uw data base (standaard *beheerder*), **gebruikers naam** en **wacht woord** in.
De **gebruikers naam** en het **wacht woord** kunnen worden gevonden in de verbindings gegevens in stap 1.

    :::image type="content" source="./media/mongodb-robomongo/authentication.png" alt-text="Scherm afbeelding van het tabblad Robomongo-verificatie":::
6. Schakel op het tabblad **SSL** het selectie vakje **SSL-protocol gebruiken** in en wijzig de **verificatie methode** in een **zelfondertekend certificaat**.

    :::image type="content" source="./media/mongodb-robomongo/SSL.png" alt-text="Scherm afbeelding van het Robomongo SSL-tabblad":::
7. Klik ten slotte op **testen** om te controleren of u verbinding kunt maken en **Sla** het op.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [gebruik van Studio 3T](mongodb-mongochef.md) met de API voor MongoDB van Azure Cosmos DB.
- Verken [voorbeelden](mongodb-samples.md) van MongoDB met de API van Azure Cosmos DB voor MongoDB.
