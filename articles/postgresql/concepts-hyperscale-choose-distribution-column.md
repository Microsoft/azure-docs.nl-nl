---
title: Kies distributie kolommen – grootschalige (Citus)-Azure Database for PostgreSQL
description: Meer informatie over het kiezen van distributie kolommen in algemene scenario's in Azure Database for PostgreSQL-grootschalige (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 129eff8c954c0c5469d3607e6ae16ce3202630ed
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91929333"
---
# <a name="choose-distribution-columns-in-azure-database-for-postgresql--hyperscale-citus"></a>Kies distributie kolommen in Azure Database for PostgreSQL – grootschalige (Citus)

De distributiekolom van elke tabel kiezen, is een van de belangrijkste modelleringsbeslissingen die u moet nemen. Azure Database for PostgreSQL – grootschalige (Citus) slaat rijen op in Shards op basis van de waarde van de kolom distributie kolommen.

De juiste keuze groepen hebben gerelateerde gegevens samen op dezelfde fysieke knoop punten, waardoor query's snel worden gemaakt en ondersteuning wordt toegevoegd voor alle SQL-functies. Een onjuiste keuze maakt het systeem langzaam en wordt niet alle SQL-functies op verschillende knoop punten ondersteund.

In dit artikel vindt u de kolom tips voor de twee meest voorkomende grootschalige (Citus).

### <a name="multi-tenant-apps"></a>Apps met meerdere tenants

De architectuur met meerdere tenants maakt gebruik van een hiërarchische indeling voor het distribueren van query's tussen knoop punten in de Server groep. De bovenkant van de gegevens hiërarchie staat bekend als de *Tenant-id* en moet worden opgeslagen in een kolom op elke tabel.

Grootschalige (Citus) inspecteert query's om te zien welke Tenant-ID ze hebben en vindt de overeenkomende tabel Shard. De query wordt doorgestuurd naar één worker-knoop punt dat de Shard bevat. Het uitvoeren van een query waarbij alle relevante gegevens op hetzelfde knoop punt worden geplaatst, wordt de locatie ' uplocation ' genoemd.

In het volgende diagram ziet u de co-locatie in het gegevens model met meerdere tenants. Het bevat twee tabellen, accounts en campagnes, die elk worden gedistribueerd door `account_id` . De gearceerde vakken vertegenwoordigen Shards. Groene Shards worden samen op één werk knooppunt opgeslagen en blauwe Shards worden opgeslagen op een ander worker-knoop punt. U ziet hoe een koppelings query tussen accounts en campagnes alle benodigde gegevens op één knoop punt heeft wanneer beide tabellen zijn beperkt tot dezelfde account \_ -id.

![Multi tenant-co-locatie](media/concepts-hyperscale-choosing-distribution-column/multi-tenant-colocation.png)

Als u dit ontwerp wilt Toep assen in uw eigen schema, identificeert u wat een Tenant in uw toepassing vormt. Algemene instanties zijn onder andere bedrijf, account, organisatie of klant. De naam van de kolom is bijvoorbeeld `company_id` of `customer_id` . Onderzoek elk van uw query's en vraag uzelf, zou het werken als er aanvullende WHERE-componenten zijn die alle tabellen met dezelfde Tenant-ID kunnen beperken.
Query's in het model met meerdere tenants zijn scoped voor een Tenant. Query's over de verkoop of de inventaris zijn bijvoorbeeld binnen een bepaald archief.

#### <a name="best-practices"></a>Aanbevolen procedures

-   **Gedistribueerde tabellen partitioneren op basis van een gemeen schappelijke Tenant \_ -id kolom.** In een SaaS-toepassing waarbij tenants bijvoorbeeld bedrijven zijn, is de Tenant \_ -id waarschijnlijk de bedrijfs \_ -id.
-   **Converteer kleine tabellen met meerdere tenants naar verwijzings tabellen.** Wanneer meerdere tenants een kleine tabel met gegevens delen, distribueer deze dan als een verwijzings tabel.
-   **Filter voor alle toepassings query's beperken op Tenant \_ -id.** Elke query moet informatie voor één Tenant tegelijk aanvragen.

Lees de [multi tenant-zelf studie](./tutorial-design-database-hyperscale-multi-tenant.md) voor een voor beeld van hoe u dit type toepassing kunt bouwen.

### <a name="real-time-apps"></a>Realtime-apps

De architectuur met meerdere tenants introduceert een hiërarchische structuur en maakt gebruik van gegevens verplaatsing voor het routeren van query's per Tenant. Real-time architecturen zijn daarentegen afhankelijk van specifieke distributie-eigenschappen van hun gegevens voor zeer parallelle verwerking.

We gebruiken ' Entiteits-ID ' als een term voor distributie kolommen in het real-time model. Typische entiteiten zijn gebruikers, hosts of apparaten.

Realtime query's vragen meestal naar numerieke aggregaten gegroepeerd op datum of categorie. Grootschalige (Citus) verzendt deze query's naar elke Shard voor gedeeltelijke resultaten en assembleert het definitieve antwoord op het coördinator knooppunt. Query's worden het snelst uitgevoerd wanneer het mogelijk is om een aantal knoop punten uit te voeren en wanneer er geen onevenredige hoeveelheid werk voor een knoop punt nodig is.

#### <a name="best-practices"></a>Aanbevolen procedures

-   **Kies een kolom met een hoge kardinaliteit als de kolom distributie.** Ter vergelijking is een status veld in een order tabel met de waarden nieuw, betaald en verzonden een slechte keuze kolom voor distributie. Hierbij wordt ervan uitgegaan dat er slechts enkele waarden zijn, die het aantal Shards beperken dat de gegevens kan bevatten, en het aantal knoop punten dat deze kan verwerken. Onder kolommen met een hoge kardinaliteit is het ook handig om deze kolommen te kiezen die veelvuldig worden gebruikt in Group-by-componenten of als lid-toets.
-   **Kies een kolom met een gelijkmatige verdeling.** Als u een tabel distribueert in een kolom die is schuingetrokken tot bepaalde algemene waarden, worden de gegevens in de tabel doorgaans verzameld in bepaalde Shards. De knoop punten die de Shards beëindigen, doen meer werk dan andere knoop punten.
-   **Feiten-en dimensie tabellen verdelen over de gemeen schappelijke kolommen.**
    Uw feiten tabel mag slechts één distributie sleutel hebben. Tabellen die worden toegevoegd aan een andere sleutel, worden niet naast de feiten tabel geplaatst. Kies één dimensie om te zoeken op basis van de frequentie waarmee deze is gekoppeld en de grootte van de rijen die u samenvoegt.
-   **Wijzig enkele dimensie tabellen in verwijzings tabellen.** Als een dimensie tabel niet kan worden gekoppeld aan de feiten tabel, kunt u de query prestaties verbeteren door kopieën van de dimensie tabel te distribueren naar alle knoop punten in de vorm van een verwijzings tabel.

Lees de [zelf studie over realtime-Dash boards](./tutorial-design-database-hyperscale-realtime.md) voor een voor beeld van hoe u dit type toepassing kunt bouwen.

### <a name="time-series-data"></a>Gegevens van de tijd reeks

In een werk belasting voor een time-serie worden de recente gegevens door toepassingen opgevraagd terwijl de oude gegevens worden gearchiveerd.

De meest voorkomende fout in informatie over het model leren van time-series in grootschalige (Citus) is het gebruik van de tijds tempel zelf als een distributie kolom. Een hash-distributie op basis van tijd verdeelt tijden in wille keurige volg orde in verschillende Shards in plaats van het bereik van tijd bij Shards te houden. Query's met een tijd in het algemeen verwijzen naar tijd, bijvoorbeeld de meest recente gegevens. Dit type hash-distributie leidt tot netwerk overhead.

#### <a name="best-practices"></a>Aanbevolen procedures

-   **Kies geen tijds tempel als de kolom distributie.** Kies een andere distributie kolom. Gebruik in een app met meerdere tenants de Tenant-ID of in een realtime-app de entiteit-ID gebruiken.
-   **Gebruik in plaats daarvan PostgreSQL-tabel partitionering voor tijd.** Gebruik tabel partities voor het opsplitsen van een grote tabel tijdgesorteerde gegevens in meerdere overgenomen tabellen met elke tabel die verschillende Peri Oden bevat. Als u een post gres-gepartitioneerde tabel in grootschalige (Citus) distribueert, wordt Shards gemaakt voor de overgenomen tabellen.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over hoe u met de co- [locatie](concepts-hyperscale-colocation.md) tussen gedistribueerde gegevens query's snel kunt uitvoeren.
