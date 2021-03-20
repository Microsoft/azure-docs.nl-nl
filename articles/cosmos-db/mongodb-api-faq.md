---
title: Veelgestelde vragen over de API van de Azure Cosmos DB voor MongoDB
description: Krijg antwoorden op veelgestelde vragen over de API van de Azure Cosmos DB voor MongoDB
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: sngun
ms.openlocfilehash: 08899018d03209dab09f61d4dd74feceee03b246
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019005"
---
# <a name="frequently-asked-questions-about-the-azure-cosmos-dbs-api-for-mongodb"></a>Veelgestelde vragen over de API van de Azure Cosmos DB voor MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

De API van het Azure Cosmos DB voor MongoDB is een interprotocol Compatibility Layer waarmee toepassingen eenvoudig en transparant kunnen communiceren met de systeem eigen Azure Cosmos-data base-engine door gebruik te maken van bestaande Sdk's en stuur Programma's die door de community worden ondersteund voor MongoDB. Ontwikkel aars kunnen nu bestaande MongoDB-toolchains en-vaardig heden gebruiken om toepassingen te ontwikkelen die gebruikmaken van Azure Cosmos DB. Ontwikkel aars profiteren van de unieke mogelijkheden van Azure Cosmos DB, waaronder wereld wijde distributie met multi-regio write-replicatie, automatische indexering, onderhoud van back-ups, financieel ondersteunde service level agreements (Sla's), enzovoort.

## <a name="how-do-i-connect-to-my-database"></a>Hoe kan ik verbinding maken met mijn data base?

De snelste manier om verbinding te maken met een Cosmos-data base met de API van Azure Cosmos DB voor MongoDB, is naar de [Azure Portal](https://portal.azure.com). Ga naar uw account en klik vervolgens in het navigatie menu links op **snel starten**. Quick start is de beste manier om code fragmenten op te halen om verbinding te maken met uw data base.

Azure Cosmos DB dwingt strikte beveiligings vereisten en-standaarden af. Voor Azure Cosmos DB accounts is verificatie en beveiligde communicatie via TLS vereist. Zorg er dus voor dat u TLSv 1.2 gebruikt.

Zie [verbinding maken met uw Cosmos-data base met de API van Azure Cosmos DB voor MongoDb](connect-mongodb-account.md)voor meer informatie.

## <a name="error-codes-while-using-azure-cosmos-dbs-api-for-mongodb"></a>Fout codes tijdens het gebruik van de API van Azure Cosmos DB voor MongoDB?

Naast de algemene MongoDB-fout codes heeft de API van de Azure Cosmos DB voor MongoDB zijn eigen specifieke fout codes. Deze kunt u vinden in de [hand leiding](mongodb-troubleshoot.md)voor het oplossen van problemen.

## <a name="supported-drivers"></a>Ondersteunde Stuur Programma's

### <a name="is-the-simba-driver-for-mongodb-supported-for-use-with-azure-cosmos-dbs-api-for-mongodb"></a>Is het Simba-stuur programma voor MongoDB ondersteund voor gebruik met de API van Azure Cosmos DB voor MongoDB?

Ja, u kunt het Simba Mongo ODBC-stuur programma gebruiken met de API van Azure Cosmos DB voor MongoDB

## <a name="next-steps"></a>Volgende stappen

* [Een .NET-Web-app maken met behulp van de API van Azure Cosmos DB voor MongoDB](create-mongodb-dotnet.md)
* [Een console-app maken met Java en de MongoDB-API in Azure Cosmos DB](create-mongodb-java.md)
