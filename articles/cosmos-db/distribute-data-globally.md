---
title: Gegevens globaal distribueren met Azure Cosmos DB
description: Meer informatie over het schalen van geo-replicatie, het schrijven van meerdere regio's en het herstellen van gegevens met behulp van globale data bases van Azure Cosmos DB, een wereld wijd gedistribueerde, multi-model database service.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/06/2021
ms.custom: seo-nov-2020
ms.openlocfilehash: 6bdb167990afeb5c1b6c68185f24a8f930287bed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102487896"
---
# <a name="distribute-your-data-globally-with-azure-cosmos-db"></a>Gegevens wereldwijd distribueren met Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Van toepassingen wordt tegenwoordig vereist dat ze zeer responsief en altijd online zijn. Voor het bereiken van lage latentie en hoge beschikbaarheid moeten de instanties van deze toepassingen worden geïmplementeerd in datacenters die zich dicht bij de gebruikers ervan bevinden. Deze toepassingen worden doorgaans geïmplementeerd in meerdere datacentrums en worden 'globaal gedistribueerd' genoemd. Globaal gedistribueerde toepassingen hebben een globaal gedistribueerde database nodig die overal ter wereld transparant gegevens kan repliceren om de toepassingen in staat te stellen te werken met een kopie van de gegevens die zich dicht bij de gebruikers bevindt. 

Azure Cosmos DB is een wereldwijd gedistribueerd databasesysteem waarmee u gegevens uit lokale replica's van uw database kunt lezen of schrijven. Azure Cosmos DB biedt op transparante wijze replica's van de gegevens in alle regio's die zijn gekoppeld aan uw Cosmos-account. Azure Cosmos DB is een wereld wijd gedistribueerde database service die is ontworpen om te voorzien in een lage latentie, elastische schaal baarheid van door Voer, goed gedefinieerde semantiek voor consistentie van gegevens en hoge Beschik baarheid. Kortom, als uw toepassing een snelle respons tijd overal ter wereld nodig heeft, als deze altijd online moet zijn, en u een onbeperkte en elastische schaal baarheid van door Voer en opslag nodig hebt, moet u uw toepassing bouwen op Azure Cosmos DB.

U kunt uw data bases zo configureren dat deze globaal worden gedistribueerd en beschikbaar zijn in een van de Azure-regio's. Als u de latentie wilt verlagen, plaatst u de gegevens dichtbij waar uw gebruikers zich bevinden. Het kiezen van de vereiste regio's is afhankelijk van het wereld wijde bereik van uw toepassing en waar uw gebruikers zich bevinden. Cosmos DB worden de gegevens op transparante wijze gerepliceerd naar alle regio's die zijn gekoppeld aan uw Cosmos-account. Het biedt één systeem kopie van uw wereld wijd gedistribueerde Azure Cosmos-data base en containers die uw toepassing lokaal kan lezen en ernaar kan schrijven.

Met Azure Cosmos DB kunt u op elk gewenst moment de regio's toevoegen of verwijderen die aan uw account zijn gekoppeld. Uw toepassing hoeft niet te worden onderbroken of opnieuw te worden geïmplementeerd om een regio toe te voegen of te verwijderen.

:::image type="content" source="./media/distribute-data-globally/deployment-topology.png" alt-text="Implementatie topologie met hoge Beschik baarheid" border="false":::

## <a name="key-benefits-of-global-distribution"></a>Belangrijkste voor delen van wereld wijde distributie

**Wereld wijd actieve apps bouwen.** Met zijn nieuwe Multi-Region writes-replicatie protocol ondersteunt elke regio zowel schrijf bewerkingen als Lees bewerkingen. De functie voor het schrijven van meerdere regio's biedt ook de volgende mogelijkheden:

- Onbeperkte elastische schrijf-en lees schaal baarheid.
- 99,999% Beschik baarheid voor lezen en schrijven over de hele wereld.
- Gegarandeerde lees-en schrijf bewerkingen in minder dan 10 milliseconden in het 99e percentiel.

Wanneer u regio's toevoegt aan en verwijdert uit uw Azure Cosmos-account, hoeft uw toepassing niet opnieuw te worden geïmplementeerd of gepauzeerd. deze is altijd Maxi maal beschikbaar.

**Bouw zeer snel reagerende apps.** Uw toepassing kan bijna realtime Lees-en schrijf bewerkingen uitvoeren op alle regio's die u voor uw data base hebt gekozen. Azure Cosmos DB beheert intern de gegevens replicatie tussen regio's met consistentie niveau van het niveau dat u hebt geselecteerd.

**Bouw Maxi maal beschik bare apps.** Het uitvoeren van een data base in meerdere regio's over de hele wereld verhoogt de beschik baarheid van een Data Base. Als een regio niet beschikbaar is, worden door andere regio's automatisch toepassings aanvragen afgehandeld. Azure Cosmos DB biedt een lees-en schrijf Beschik baarheid van 99,999% voor data bases met meerdere regio's.

**Onderhoud van bedrijfs continuïteit tijdens regionale storingen.** Azure Cosmos DB ondersteunt [automatische failover](how-to-manage-database-account.md#automatic-failover) tijdens een regionale storing. Tijdens een regionale onderbreking blijft Azure Cosmos DB de latentie, Beschik baarheid, consistentie en de door Voer van de voor uitgang blijven behouden. Om ervoor te zorgen dat uw volledige toepassing Maxi maal beschikbaar is, biedt Cosmos DB hand matige failover-API voor het simuleren van een regionale storing. Met deze API kunt u normale bedrijfs continuïteits oefeningen uitvoeren.

**Lees-en schrijf doorvoer wereld wijd schalen.** U kunt elke regio zo instellen dat deze kan worden beschrijfd en op elastische schaal worden gelezen en overal ter wereld wordt geschreven. De door Voer die door uw toepassing wordt geconfigureerd op een Azure Cosmos-data base of een container, wordt ingericht in alle regio's die zijn gekoppeld aan uw Azure Cosmos-account. De ingerichte door Voer wordt gegarandeerd door [financieel ondersteunde sla's](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/).

**U kunt kiezen uit verschillende goed gedefinieerde consistentie modellen.** Het Azure Cosmos DB-replicatie protocol biedt vijf goed gedefinieerde, praktische en intuïtieve consistentie modellen. Elk model heeft een verhouding tussen consistentie en prestaties. Gebruik deze consistentie modellen voor het gemak om wereld wijd gedistribueerde toepassingen te bouwen.

## <a name="next-steps"></a><a id="Next Steps"></a>Volgende stappen

Meer informatie over globale distributie vindt u in de volgende artikelen:

* [Wereldwijde distributie - achter de schermen](global-dist-under-the-hood.md)
* [Het schrijven van meerdere regio's in uw toepassingen configureren](how-to-multi-master.md)
* [Clients configureren voor multihoming](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Regio's toevoegen aan of verwijderen uit uw Azure Cosmos DB-account](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Een aangepast beleid voor conflict oplossing maken voor SQL-API-accounts](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
* [Programmeer bare consistentie modellen in Cosmos DB](consistency-levels.md)
* [Het juiste consistentie niveau voor uw toepassing kiezen](./consistency-levels.md)
* [Consistentie niveaus voor Azure Cosmos DB-Api's](./consistency-levels.md)
* [Beschik baarheid en prestaties voor diverse consistentie niveaus](./consistency-levels.md)