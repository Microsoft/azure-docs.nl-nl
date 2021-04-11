---
title: Voer een upgrade uit voor de Mongo-versie van de API van uw Azure Cosmos DB voor het MongoDB-account
description: De MongoDB wire-protocol versie voor de API van uw bestaande Azure Cosmos DB voor MongoDB-accounts naadloos bijwerken
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/19/2021
ms.author: chrande
ms.openlocfilehash: 8865a16c2840b65f432de679c6dd63b285b1f760
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104771797"
---
# <a name="upgrade-the-api-version-of-your-azure-cosmos-db-api-for-mongodb-account"></a>Upgrade de API-versie van uw Azure Cosmos DB-API voor het MongoDB-account
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

In dit artikel wordt beschreven hoe u een upgrade uitvoert van de API-versie van de API van uw Azure Cosmos DB voor het MongoDB-account. Nadat u de upgrade hebt uitgevoerd, kunt u de nieuwste functionaliteit gebruiken in de API van Azure Cosmos DB voor MongoDB. Het upgrade proces onderbreekt niet de beschik baarheid van uw account en gebruikt geen RU/s of verkleint de capaciteit van de Data Base op elk gewenst moment. Dit proces heeft geen invloed op bestaande gegevens of indexen. 

Wanneer u een upgrade uitvoert naar een nieuwe API-versie, begint u met het ontwikkelen/testen van werk belastingen voordat u productie werkbelastingen bijwerkt. Het is belang rijk dat u uw clients bijwerkt naar een versie die compatibel is met de API-versie waarnaar u een upgrade uitvoert voordat u de Azure Cosmos DB-API voor MongoDB-account upgradet.

>[!Note]
> Op dit moment kunnen alleen in aanmerking komende accounts met de server versie 3,2 worden bijgewerkt naar versie 3,6 of 4,0. Als uw account de optie upgrade niet weergeeft, moet u [een ondersteunings ticket indienen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="upgrading-to-40-or-36"></a>Upgraden naar 4.0 of 3.6

### <a name="benefits-of-upgrading-to-version-40"></a>Voordelen van een upgrade naar versie 4.0

Hieronder vindt u de nieuwe functies van versie 4,0:
- Ondersteuning voor multi-document transacties in unsharded-verzamelingen.
- Nieuwe aggregatieoperatoren
- Verbeterde scanprestaties
- Snellere, efficiëntere opslag

### <a name="benefits-of-upgrading-to-version-36"></a>Voordelen van het uitvoeren van een upgrade naar versie 3.6

Hieronder vindt u de nieuwe functies van versie 3,6:
- Verbeterde prestaties en stabiliteit
- Ondersteuning voor nieuwe database-opdrachten
- Standaard ondersteuning voor aggregatiepijplijn en nieuwe aggregatiefasen
- Ondersteuning voor wijzigingsstromen
- Ondersteuning voor samengestelde indexen
- Ondersteuning voor meerdere partities voor de volgende bewerkingen: bijwerken, verwijderen, tellen en sorteren
- Verbeterde prestaties voor de volgende aggregatiebewerkingen: $count, $skip, $limit en $group
- Het indexeren van jokertekens wordt voortaan ondersteund

### <a name="changes-from-version-32"></a>Wijzigingen sinds versie 3.2

- Standaard is de functie [Nieuwe poging aan serverzijde (SSR)](prevent-rate-limiting-errors.md) ingeschakeld, zodat aanvragen van de clienttoepassing geen 16500-fouten retourneren. In plaats daarvan worden aanvragen hervat totdat ze zijn voltooid of de time-out van zestig seconden bereiken.
- De time-out per aanvraag is ingesteld op zestig seconden.
- Voor MongoDB-verzamelingen die voor de versie met het nieuwe Wire-protocol zijn gemaakt, is standaard alleen de eigenschap `_id` geïndexeerd.

### <a name="action-required-when-upgrading-from-32"></a>Actie vereist bij het upgraden van 3.2

Wanneer u een upgrade uitvoert van 3.2, wordt het achtervoegsel van het eindpunt van het databaseaccount bijgewerkt naar de volgende indeling:

```
<your_database_account_name>.mongo.cosmos.azure.com
```

Als u een upgrade uitvoert van versie 3,2, moet u het bestaande eind punt vervangen in uw toepassingen en stuur Programma's die verbinding maken met dit database account. **Alleen verbindingen die het nieuwe eindpunt gebruiken, hebben toegang tot de functies in de nieuwe API-versie**. Het vorige 3.2-eindpunt moet het achtervoegsel `.documents.azure.com` bevatten.

>[!Note]
> Dit eind punt kan een kleine verschillen hebben als uw account is gemaakt in een soevereine, overheids of beperkte Azure-Cloud.

## <a name="how-to-upgrade"></a>Upgrade uitvoeren

1. Meld u aan bij de [Azure Portal.](https://portal.azure.com/)

1. Navigeer naar uw Azure Cosmos DB-API voor MongoDB-account. Open het deel venster **overzicht** en controleer of de huidige **Server versie** 3,2 of 3,6 is.

    :::image type="content" source="./media/mongodb-version-upgrade/check-current-version.png" alt-text="Controleer de huidige versie van uw MongoDB-account uit het Azure Portal." border="true":::

1. Open het deel venster vanuit het menu links `Features` . Dit deel venster toont de functies voor het account niveau die beschikbaar zijn voor uw database account.

1. Selecteer rij `Upgrade MongoDB server version`. Als u deze optie niet ziet, komt uw account mogelijk niet in aanmerking voor deze upgrade. Als dit het geval is, moet u [een ondersteunings ticket](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) indienen.

    :::image type="content" source="./media/mongodb-version-upgrade/upgrade-server-version.png" alt-text="Open de Blade functies en voer een upgrade uit voor uw account." border="true":::

1. Bekijk de informatie die over de upgrade wordt weergegeven. Selecteer `Set server version to 4.0` (of 3,6 afhankelijk van uw huidige versie).

    :::image type="content" source="./media/mongodb-version-upgrade/select-upgrade.png" alt-text="Bekijk de richt lijnen voor de upgrade en selecteer upgrade." border="true":::

1. Nadat u de upgrade hebt gestart, wordt het menu **functie** grijs weer gegeven en wordt de status ingesteld op *in behandeling*. Het duurt ongeveer 15 minuten om de upgrade te volt ooien. Dit proces heeft geen invloed op de bestaande functionaliteit of bewerkingen van uw database account. Nadat de update is voltooid, wordt in de status van de **MongoDb-Server versie** de bijgewerkte versie weer gegeven. [Neem contact op met de ondersteuning](https://azure.microsoft.com/en-us/support/create-ticket/) als er een probleem is opgetreden bij het verwerken van uw aanvraag.

1. Hier volgen enkele aandachtspunten na de upgrade van uw account:

    1. Als u een upgrade hebt uitgevoerd vanaf 3,2, gaat u terug naar het deel venster **overzicht** en kopieert u de nieuwe connection string om in uw toepassing te gebruiken. De oude verbindingsreeks waarop versie 3.2 wordt uitgevoerd, wordt niet onderbroken. Voor een consistente ervaring moeten al uw toepassingen gebruikmaken van het nieuwe eindpunt.

    1. Als u een upgrade hebt uitgevoerd van versie 3.6, wordt uw bestaande verbindingsreeks bijgewerkt naar de opgegeven versie en moet deze nog steeds worden gebruikt.

## <a name="how-to-downgrade"></a>Downgrade uitvoeren

U kunt uw account ook downgradeen van 4,0 naar 3,6 via dezelfde stappen in de sectie How to upgrade.

Als u een upgrade hebt uitgevoerd van 3,2 naar (4,0 of 3,6) en u wilt terugvallen op 3,2, kunt u gewoon terugschakelen naar het gebruik van uw vorige (3,2) connection string met de host `accountname.documents.azure.com` die actief is op versie 3,2 van het na de upgrade uitvoeren.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de ondersteunde en niet-ondersteunde [functies van MongoDB-versie 4.0](mongodb-feature-support-40.md).
- Meer informatie over de ondersteunde en niet-ondersteunde [functies van MongoDB-versie 3.6](mongodb-feature-support-36.md).
- Zie [Mongo-functies van versie 3.6](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/) voor meer informatie
