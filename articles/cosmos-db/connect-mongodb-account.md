---
title: Een MongoDB-toepassing verbinden met Azure Cosmos DB
description: Meer informatie over hoe u een MongoDB-app kunt verbinden met Azure Cosmos DB door de connection string op te halen van Azure Portal
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/19/2020
ms.reviewer: sngun
adobe-target: true
adobe-target-activity: DocsExp-A/B-384740-MongoDB-2.8.2021
adobe-target-experience: Experience B
adobe-target-content: connect-mongodb-account-experimental.md
ms.openlocfilehash: d3e619559f1fb2500904aa7349aa131fd9b5fc00
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/09/2021
ms.locfileid: "99980776"
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Een MongoDB-toepassing verbinden met Azure Cosmos DB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Ontdek hoe u uw MongoDB-app met een Azure Cosmos DB kunt verbinden met behulp van een MongoDB-verbindingsreeks. U kunt vervolgens een Azure Cosmos-database gebruiken als het gegevensarchief voor uw MongoDB-app.

In deze zelfstudie kunt u op twee manieren gegevens van verbindingsreeksen ophalen:

- [De Quick](#get-the-mongodb-connection-string-by-using-the-quick-start)start-methode, voor gebruik met .net-, Node.js-, MongoDb-, Java-en python-Stuur Programma's
- [De aangepaste Connection String methode](#get-the-mongodb-connection-string-to-customize), voor gebruik met andere Stuur Programma's

## <a name="prerequisites"></a>Vereisten

- Een Azure-account. Als u geen Azure-account hebt, maakt u nu een [gratis Azure-account](https://azure.microsoft.com/free/) .
- Een Cosmos-account. Zie [een web-app bouwen met de API van Azure Cosmos DB voor MongoDb en .NET SDK](create-mongodb-dotnet.md)voor instructies.

## <a name="get-the-mongodb-connection-string-by-using-the-quick-start"></a>De MongoDB-connection string ophalen met behulp van snel starten

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)in een Internet browser.
2. Selecteer op de Blade **Azure Cosmos DB** de API.
3. Klik in het linkerdeel venster van de Blade account op **Quick Start**.
4. Kies uw platform (**.net**, **Node.js**, **MongoDb shell**, **Java**, **python**). Als uw stuur programma of hulp programma niet wordt weer gegeven, kunt u geen zorgen maken, we maken continu meer code fragmenten voor verbindings codes. Geef hieronder een opmerking over wat u wilt zien. Als u wilt weten hoe u uw eigen verbinding maakt, leest u [de Connection String gegevens van het account ophalen](#get-the-mongodb-connection-string-to-customize).
5. Kopieer en plak het code fragment in uw MongoDB-app.

    :::image type="content" source="./media/connect-mongodb-account/QuickStartBlade.png" alt-text="Blade snel starten":::

## <a name="get-the-mongodb-connection-string-to-customize"></a>De MongoDB-connection string ophalen om aan te passen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)in een Internet browser.
2. Selecteer op de Blade **Azure Cosmos DB** de API.
3. Klik in het linkerdeel venster van de Blade account op **verbindings reeks**.
4. De Blade **verbindings reeks** wordt geopend. Het bevat alle informatie die nodig is om verbinding te maken met het account met behulp van een stuur programma voor MongoDB, inclusief een geconstrueerde connection string.

   :::image type="content" source="./media/connect-mongodb-account/ConnectionStringBlade.png" alt-text="De blade Verbindingsreeks" lightbox= "./media/connect-mongodb-account/ConnectionStringBlade.png" :::

## <a name="connection-string-requirements"></a>Vereisten voor de verbindings reeks

> [!Important]
> Voor Azure Cosmos DB gelden strenge beveiligingsvereisten en -normen. Azure Cosmos DB-accounts vereisen verificatie en beveiligde communicatie via *TLS*.

Azure Cosmos DB ondersteunt de standaard MongoDB-connection string URI-indeling, met een aantal specifieke vereisten: Azure Cosmos DB accounts vereisen verificatie en beveiligde communicatie via TLS. De connection string indeling is dus:

`mongodb://username:password@host:port/[database]?ssl=true`

De waarden van deze teken reeks zijn beschikbaar op de Blade **verbindingsteken reeks** die eerder is weer gegeven:

* Gebruikers naam (vereist): Cosmos-account naam.
* Wacht woord (vereist): Cosmos-account wachtwoord.
* Host (vereist): FQDN van het Cosmos-account.
* Poort (vereist): 10255.
* Data Base (optioneel): de data base die door de verbinding wordt gebruikt. Als er geen data base wordt gegeven, is de standaard database ' test '.
* SSL = True (vereist)

Bekijk bijvoorbeeld het account dat wordt weer gegeven op de Blade **verbindings reeks** . Een geldig connection string is:

`mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true`

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [gebruik van Studio 3T](mongodb-mongochef.md) met de API voor MongoDB van Azure Cosmos DB.
- Meer informatie over het [gebruik van Robo 3T](mongodb-robomongo.md) met de API voor MongoDB van Azure Cosmos DB.
- Verken [voorbeelden](mongodb-samples.md) van MongoDB met de API van Azure Cosmos DB voor MongoDB.
