---
title: Consistentie niveaus toewijzen voor Azure Cosmos DB-API voor MongoDB
description: Consistentie niveaus toewijzen voor de Azure Cosmos DB-API voor MongoDB.
author: sivethe
ms.author: sivethe
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: e8ac3e376c8d67e82def3a57910707c6b1433912
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93333151"
---
# <a name="consistency-levels-for-azure-cosmos-db-and-the-api-for-mongodb"></a>Consistentie niveaus voor Azure Cosmos DB en de API voor MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

In tegens telling tot Azure Cosmos DB biedt de systeem eigen MongoDB niet precies gedefinieerde consistentie garanties. In plaats daarvan kunnen gebruikers met systeem eigen MongoDB de volgende consistentie garanties configureren: een schrijf probleem, een lees probleem en de isMaster-instructie-om de Lees bewerkingen naar primaire of secundaire replica's te sturen om het gewenste consistentie niveau te krijgen.

Wanneer u de API van Azure Cosmos DB gebruikt voor MongoDB, behandelt het MongoDB-stuur programma uw schrijf regio als de primaire replica en worden alle andere regio's een replica lezen. U kunt kiezen welke regio is gekoppeld aan uw Azure Cosmos-account als primaire replica.

> [!NOTE]
> Het standaard consistentie model voor Azure Cosmos DB is sessie. Session is een op de client gericht consistentie model dat niet systeem eigen wordt ondersteund door Cassandra of MongoDB. Zie [consistentie niveaus in azure Cosmos DB](consistency-levels.md) voor meer informatie over welk consistentie model moet worden gekozen.

Tijdens het gebruik van de API van Azure Cosmos DB voor MongoDB:

* De schrijf bezorgdheid wordt toegewezen aan het standaard consistentie niveau dat is geconfigureerd voor uw Azure Cosmos-account.

* Azure Cosmos DB wijst de Lees bezorgdheid die door het MongoDB-client stuur programma is opgegeven, dynamisch toe aan een van de Azure Cosmos DB consistentie niveaus die dynamisch zijn geconfigureerd voor een lees aanvraag.  

* U kunt aantekeningen toevoegen aan een specifieke regio die is gekoppeld aan uw Azure Cosmos-account als ' primair ' door de regio als de eerste Beschrijf bare regio te maken. 

## <a name="mapping-consistency-levels"></a>Consistentieniveaus toewijzen

In de volgende tabel ziet u hoe de systeem eigen MongoDB-schrijf-en lees problemen worden toegewezen aan de consistentie niveaus van Azure Cosmos wanneer u de API van Azure Cosmos DB gebruikt voor MongoDB:

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png" alt-text="Toewijzing van MongoDB-consistentie model" lightbox= "./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png":::

Als uw Azure Cosmos-account is geconfigureerd met een ander consistentie niveau dan de sterke consistentie, kunt u de kans ontdekken dat uw clients sterke en consistente Lees bewerkingen voor uw werk belastingen krijgen door te kijken naar de metrische gegevens van de *Probabilistically-gebonden veroudering* (PBS). Deze metriek wordt weer gegeven in de Azure Portal voor meer informatie, Zie [Probabilistically gebonden verouderde waarde (PBS) controleren](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

Probabilistic gebonden veroudering laten zien hoe uiteindelijk uw uiteindelijke consistentie is. Deze metrische gegevens bieden een inzicht in hoe vaak u een sterkere consistentie kunt krijgen dan het consistentie niveau dat u momenteel hebt geconfigureerd in uw Azure Cosmos-account. Met andere woorden, u kunt de waarschijnlijkheid (gemeten in milliseconden) voor het verkrijgen van sterk consistente Lees bewerkingen voor een combi natie van de regio's schrijven en lezen zien.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over globale distributie-en consistentie niveaus voor Azure Cosmos DB:

* [Overzicht van wereldwijde distributie](distribute-data-globally.md)
* [Overzicht van consistentie niveau](consistency-levels.md)
* [Hoge beschikbaarheid](high-availability.md)
