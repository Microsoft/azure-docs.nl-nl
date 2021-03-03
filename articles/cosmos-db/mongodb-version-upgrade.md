---
title: Voer een upgrade uit voor de Mongo-versie van de API van uw Azure Cosmos DB voor het MongoDB-account
description: De MongoDB wire-protocol versie voor de API van uw bestaande Azure Cosmos DB voor MongoDB-accounts naadloos bijwerken
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/02/2021
ms.author: chrande
ms.openlocfilehash: 1818838a68c2712336a3515b2a82b5fdd518d237
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661168"
---
# <a name="upgrade-the-api-version-of-your-azure-cosmos-db-api-for-mongodb-account"></a>Upgrade de API-versie van uw Azure Cosmos DB-API voor het MongoDB-account
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

In dit artikel wordt beschreven hoe u een upgrade uitvoert van de API-versie van de API van uw Azure Cosmos DB voor het MongoDB-account. Nadat u de upgrade hebt uitgevoerd, kunt u de nieuwste functionaliteit gebruiken in de API van Azure Cosmos DB voor MongoDB. Het upgrade proces onderbreekt niet de beschik baarheid van uw account en gebruikt geen RU/s of verkleint de capaciteit van de Data Base op elk gewenst moment. Dit proces heeft geen invloed op bestaande gegevens of indexen. 

Wanneer u een upgrade uitvoert naar een nieuwe API-versie, begint u met het ontwikkelen/testen van werk belastingen voordat u productie werkbelastingen bijwerkt. Het is belang rijk dat u uw clients bijwerkt naar een versie die compatibel is met de API-versie waarnaar u een upgrade uitvoert voordat u de Azure Cosmos DB-API voor MongoDB-account upgradet.

>[!Note]
> Op dit moment kunnen alleen in aanmerking komende accounts met de server versie 3,2 worden bijgewerkt naar versie 3,6 of 4,0. Als uw account de optie upgrade niet weergeeft, moet u [een ondersteunings ticket indienen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="upgrading-to-40-or-36"></a>Upgraden naar 4,0 of 3,6

### <a name="benefits-of-upgrading-to-version-40"></a>Voor delen van het uitvoeren van een upgrade naar versie 4,0

Hieronder vindt u de nieuwe functies van versie 4,0:
- Ondersteuning voor multi-document transacties in unsharded-verzamelingen.
- Nieuwe aggregatie operatoren
- Verbeterde scan prestaties
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

### <a name="changes-from-version-32"></a>Wijzigingen van versie 3,2

- Standaard is de functie [opnieuw proberen aan de server zijde (SSR)](prevent-rate-limiting-errors.md) ingeschakeld, zodat aanvragen van de client toepassing geen 16500-fouten retour neren. In plaats daarvan worden aanvragen hervat totdat ze zijn voltooid of de 60 seconden time-out hebben bereikt.
- De time-out per aanvraag is ingesteld op zestig seconden.
- Voor MongoDB-verzamelingen die voor de versie met het nieuwe Wire-protocol zijn gemaakt, is standaard alleen de eigenschap `_id` geïndexeerd.

### <a name="action-required-when-upgrading-from-32"></a>Actie vereist bij het upgraden van 3,2

Wanneer u een upgrade uitvoert van 3,2, wordt het achtervoegsel van het eind punt van het database account bijgewerkt naar de volgende indeling:

```
<your_database_account_name>.mongo.cosmos.azure.com
```

Als u een upgrade uitvoert van versie 3,2, moet u het bestaande eind punt vervangen in uw toepassingen en stuur Programma's die verbinding maken met dit database account. **Alleen verbindingen die het nieuwe eind punt gebruiken, hebben toegang tot de functies in de nieuwe API-versie**. Het vorige 3,2-eind punt moet het achtervoegsel bevatten `.documents.azure.com` .

>[!Note]
> Dit eind punt kan een kleine verschillen hebben als uw account is gemaakt in een soevereine, overheids of beperkte Azure-Cloud.

## <a name="how-to-upgrade"></a>Een upgrade uitvoeren

1. Ga naar de Azure Portal en ga naar de Azure Cosmos DB-API voor de Blade overzicht van MongoDB-account. Controleer of uw huidige server versie u verwacht.

    :::image type="content" source="./media/mongodb-version-upgrade/1.png" alt-text="Overzicht van Azure Portal met MongoDB-account" border="false":::

2. Selecteer de Blade in de opties aan de linkerkant `Features` . Hiermee worden de functies van het account niveau onthuld die beschikbaar zijn voor uw database account.

    :::image type="content" source="./media/mongodb-version-upgrade/2.png" alt-text="Azure Portal met MongoDB-account overzicht met de Blade functies gemarkeerd" border="false":::

3. Klik op de `Upgrade Mongo server version` rij. Als u deze optie niet ziet, komt uw account mogelijk niet in aanmerking voor deze upgrade. Als dit het geval is, moet u [een ondersteunings ticket](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) indienen.

    :::image type="content" source="./media/mongodb-version-upgrade/3.png" alt-text="De Blade functies met opties." border="false":::

4. Bekijk de informatie die wordt weer gegeven over de upgrade. Klik op aan zodra `Enable` u klaar bent om het proces te starten.

    :::image type="content" source="./media/mongodb-version-upgrade/4.png" alt-text="Uitgebreide upgrade richtlijnen." border="false":::

5. Nadat het proces is gestart, `Features` wordt in het menu de status van de upgrade weer gegeven. De status gaat van `Pending` naar naar `In Progress` `Upgraded` . Dit proces heeft geen invloed op de bestaande functionaliteit of bewerkingen van het database account.

    :::image type="content" source="./media/mongodb-version-upgrade/5.png" alt-text="Upgrade status na het initiëren." border="false":::

6. Zodra de upgrade is voltooid, wordt de status weer gegeven als `Upgraded` . Klik hierop om meer te weten te komen over de volgende stappen en acties die u moet uitvoeren om het proces te volt ooien. [Neem contact op met de ondersteuning](https://azure.microsoft.com/en-us/support/create-ticket/) als er een probleem is opgetreden bij het verwerken van uw aanvraag.

    :::image type="content" source="./media/mongodb-version-upgrade/6.png" alt-text="Bijgewerkte account status." border="false":::

7. 
    1. Als u een upgrade hebt uitgevoerd vanaf 3,2, gaat u terug naar de `Overview` Blade en kopieert u de nieuwe connection string om in uw toepassing te gebruiken. De oude connection string met 3,2 wordt niet onderbroken. Voor een consistente ervaring moeten al uw toepassingen het nieuwe eind punt gebruiken.
    2. Als u een upgrade hebt uitgevoerd vanaf 3,6, wordt uw bestaande connection string bijgewerkt naar de opgegeven versie en moet deze blijven worden gebruikt.

    :::image type="content" source="./media/mongodb-version-upgrade/7.png" alt-text="Nieuwe blade overzicht." border="false":::


## <a name="how-to-downgrade"></a>Downgrade
U kunt uw account ook downgradeen van 4,0 naar 3,6 via dezelfde stappen in de sectie How to upgrade. 

Als u een upgrade hebt uitgevoerd van 3,2 naar (4,0 of 3,6) en u wilt terugvallen op 3,2, kunt u gewoon terugschakelen naar het gebruik van uw vorige (3,2) connection string met de host `accountname.documents.azure.com` die actief is op versie 3,2 van het na de upgrade uitvoeren.


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de ondersteunde en niet-ondersteunde [functies van MongoDb versie 4,0](mongodb-feature-support-40.md).
- Meer informatie over de ondersteunde en niet-ondersteunde [functies van MongoDb versie 3,6](mongodb-feature-support-36.md).
- Zie [Mongo-functies van versie 3.6](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/) voor meer informatie
