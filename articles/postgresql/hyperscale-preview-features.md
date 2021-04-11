---
title: Preview-functies in Azure Database for PostgreSQL-grootschalige (Citus)
description: Bijgewerkte lijst met functies die momenteel als preview-versie beschikbaar zijn
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: overview
ms.date: 04/07/2021
ms.openlocfilehash: 775785e1b5130499d69b269e72f5c774e9e5f3f9
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107024062"
---
# <a name="preview-features-for-postgresql---hyperscale-citus"></a>Preview-functies voor PostgreSQL-grootschalige (Citus)

Azure Database for PostgreSQL-grootschalige (Citus) biedt previews voor niet-uitgebrachte functies. Preview-versies worden zonder service level agreement gegeven en worden niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.  Zie voor meer informatie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="features-currently-in-preview"></a>Functies die momenteel als preview-versie beschikbaar zijn

Dit zijn de functies die momenteel beschikbaar zijn voor de preview-versie:

* **[Kolom opslag](concepts-hyperscale-columnar.md)**.
  Sla de kolommen van de geselecteerde tabellen (in plaats van rijen) op de schijf opeenvolgend op. Ondersteunt compressie op schijf. Geschikt voor werk belastingen voor analyse en gegevens opslag.
* **[Postgresql 12 en 13](concepts-hyperscale-versions.md)**.
  Gebruik de meest recente versie van de data base in uw server groep.
* **[Basic-laag](concepts-hyperscale-tiers.md)**. Voer een server groep uit met alleen een coördinator knooppunt en geen worker-knoop punten. Een betaal bare manier om de eerste test en ontwikkeling uit te voeren en kleine productie workloads te verwerken.
* **[Replica's lezen](howto-hyperscale-read-replicas-portal.md)** (momenteel alleen regio). Eventuele wijzigingen die in de primaire server groep plaatsvinden, worden weer gegeven in de replica en de query's voor de replica veroorzaken geen extra belasting voor het origineel.
  Replica's zijn een handig hulp programma voor het verbeteren van de prestaties voor alleen-lezen workloads.
* **[Beheerde PgBouncer](concepts-hyperscale-limits.md#managed-pgbouncer-preview)**.
  Een verbindings groep waarmee meerdere clients tegelijk verbinding kunnen maken met de Server groep, terwijl het aantal actieve verbindingen wordt beperkt. Het voldoet aan verbindings aanvragen terwijl het coördinator knooppunt soepel blijft werken.
* **[PgAudit](concepts-hyperscale-audit.md)**. Biedt gedetailleerde controle logboek registratie van sessies en objecten via de standaard functie voor PostgreSQL-logboek registratie. Het produceert audit logboeken die nodig zijn voor het door geven van bepaalde overheids-, financiële of ISO-certificerings controles.

### <a name="available-regions-for-preview-features"></a>Beschik bare regio's voor preview-functies

De uitbrei ding pgAudit is beschikbaar in alle [regio's die worden ondersteund door grootschalige (Citus)](concepts-hyperscale-configuration-options.md#regions).
De andere preview-functies zijn alleen beschikbaar in **VS-Oost** .

## <a name="does-my-server-group-have-access-to-preview-features"></a>Heeft mijn server groep toegang tot Preview-functies?

Ga naar de pagina **overzicht** van de Server groep in de Azure Portal om te bepalen of er preview-functies zijn ingeschakeld voor de Server groep grootschalige (Citus).
Als u de eigenschap slaag **: Basic (preview)** of **tier: Standard (preview)** ziet, heeft de Server groep toegang tot de preview-functies.

### <a name="how-to-get-access"></a>Toegang krijgen

Wanneer u een nieuwe grootschalige-Server groep (Citus) maakt, schakelt u het selectie vakje **Preview-functies inschakelen in.**

## <a name="contact-us"></a>Contact opnemen

Laat het ons weten over uw ervaring met het gebruik van preview-functies door [Azure DB voor postgresql te vragen](mailto:AskAzureDBforPostgreSQL@service.microsoft.com).
(Dit e-mail adres is geen kanaal voor technische ondersteuning. Open een [ondersteunings aanvraag](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)voor technische problemen.)
