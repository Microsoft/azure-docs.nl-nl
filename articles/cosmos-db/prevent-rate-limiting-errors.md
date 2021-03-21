---
title: Voor komen dat er fouten worden beperkt voor de Azure Cosmos DB-API voor MongoDB-bewerkingen.
description: Informatie over het voor komen van uw Azure Cosmos DB-API voor MongoDB-bewerkingen van het aantal fouten bij het beperken van de SSR (nieuwe poging tot server zijde).
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 01/13/2021
ms.author: gahllevy
ms.openlocfilehash: 1e9062b111c30efa90b98c4ebcee710b1d975a1d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99507927"
---
# <a name="prevent-rate-limiting-errors-for-azure-cosmos-db-api-for-mongodb-operations"></a>Fouten beperken voor de Azure Cosmos DB-API voor MongoDB-bewerkingen
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Azure Cosmos DB-API voor MongoDB-bewerkingen kan mislukken met snelheids beperkings fouten (16500/429) als ze de doorvoer limiet van een verzameling overschrijden (RUs). 

U kunt de SSR-functie (server side retry) inschakelen en deze bewerkingen automatisch door de server laten uitvoeren. De aanvragen worden opnieuw uitgevoerd na een korte vertraging voor alle verzamelingen in uw account. Deze functie is een handig alternatief voor het afhandelen van fouten beperken in de client toepassing.

## <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Navigeer naar uw Azure Cosmos DB-API voor MongoDB-account.

1. Ga naar het deel venster **functies** onder de sectie **instellingen** .

1. **Probeer het opnieuw**.

1. Klik op **inschakelen** om deze functie in te scha kelen voor alle verzamelingen in uw account.

:::image type="content" source="./media/prevent-rate-limiting-errors/portal-features-server-side-retry.png" alt-text="Scherm afbeelding van de functie nieuwe poging aan server zijde voor de Azure Cosmos DB-API voor MongoDB":::

## <a name="use-the-azure-cli"></a>Azure CLI gebruiken

1. Controleer of SSR al is ingeschakeld voor uw account:
```bash
az cosmosdb show --name accountname --resource-group resourcegroupname
```
2. **Inschakelen** SSR voor alle verzamelingen in uw database account. Het kan tot 15min duren voordat deze wijziging van kracht wordt.
```bash
az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableMongo DisableRateLimitingResponses
```
Met de volgende opdracht wordt SSR **uitgeschakeld** voor alle verzamelingen in uw database account door ' DisableRateLimitingResponses ' te verwijderen uit de lijst met mogelijkheden. Het kan tot 15min duren voordat deze wijziging van kracht wordt.
```bash
az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableMongo
```

## <a name="frequently-asked-questions"></a>Veelgestelde vragen
* Hoe worden aanvragen opnieuw geprobeerd?
    * Aanvragen worden doorlopend opnieuw geprobeerd (boven en na) tot een time-out van 60 seconden is bereikt. Als de time-out is bereikt, ontvangt de client een [ExceededTimeLimit-uitzonde ring (50)](mongodb-troubleshoot.md).
*  Hoe kan ik de effecten van SSR controleren?
    *  U kunt de frequentie beperkende fouten (429s) die opnieuw worden geprobeerd, in het deel venster met metrische gegevens van Cosmos DB weer geven. Houd er wel van uit dat deze fouten niet naar de client gaan wanneer SSR is ingeschakeld, omdat deze worden verwerkt en server zijde opnieuw wordt uitgevoerd. 
    *  U kunt zoeken naar logboek vermeldingen met ' estimatedDelayFromRateLimitingInMilliseconds ' in uw [Cosmos DB-bron logboeken](cosmosdb-monitor-resource-logs.md).
*  Heeft SSR invloed op het niveau van mijn consistentie?
    *  SSR heeft geen invloed op de consistentie van een aanvraag. Aanvragen worden opnieuw geprobeerd aan de server zijde als de frequentie beperkt is (met een 429-fout). 
*  Is SSR van invloed op elk type fout dat kan worden ontvangen van mijn client?
    *  Nee, SSR is alleen van invloed op frequentie beperkende fouten (429s) door de server opnieuw te proberen. Deze functie voor komt dat u de fout beperkende fouten in de client toepassing moet afhandelen. Alle [andere fouten](mongodb-troubleshoot.md) gaan naar de client. 

## <a name="next-steps"></a>Volgende stappen

Raadpleeg dit artikel voor meer informatie over het oplossen van veelvoorkomende fouten:

* [Veelvoorkomende problemen met de API van Azure Cosmos DB voor MongoDB oplossen](mongodb-troubleshoot.md)
