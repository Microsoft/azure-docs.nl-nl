---
title: Voor komen dat er fouten worden beperkt voor de Azure Cosmos DB-API voor MongoDB-bewerkingen.
description: Informatie over het voor komen van uw Azure Cosmos DB-API voor MongoDB-bewerkingen van het aantal fouten bij het beperken van de SSR (nieuwe poging tot server zijde).
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 01/13/2021
ms.author: gahllevy
ms.openlocfilehash: 73c2aba3028f42621f241bd8f295e83e0ef96e68
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540543"
---
# <a name="prevent-rate-limiting-errors-for-azure-cosmos-db-api-for-mongodb-operations"></a>Fouten beperken voor de Azure Cosmos DB-API voor MongoDB-bewerkingen
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Azure Cosmos DB-API voor MongoDB-bewerkingen kan mislukken met snelheids beperkings fouten (16500/429) als ze de doorvoer limiet van een verzameling overschrijden (RUs). 

U kunt de SSR-functie (server side retry) inschakelen en deze bewerkingen automatisch door de server laten uitvoeren. De aanvragen worden opnieuw uitgevoerd na een korte vertraging voor alle verzamelingen in uw account. Deze functie is een handig alternatief voor het afhandelen van fouten beperken in de client toepassing.


## <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Navigeer naar uw Azure Cosmos DB-API voor MongoDB-account.

1. Ga naar het deel venster **functies** onder de sectie **instellingen** .

1. **Probeer het opnieuw**.

1. Klik op **inschakelen** om deze functie in te scha kelen voor alle verzamelingen in uw account.

:::image type="content" source="./media/prevent-rate-limiting-errors/portal-features-server-side-retry.png" alt-text="Scherm afbeelding van de functie nieuwe poging aan server zijde voor de Azure Cosmos DB-API voor MongoDB":::


## <a name="next-steps"></a>Volgende stappen

Raadpleeg dit artikel voor meer informatie over het oplossen van veelvoorkomende fouten:

* [Veelvoorkomende problemen met de API van Azure Cosmos DB voor MongoDB oplossen](mongodb-troubleshoot.md)
