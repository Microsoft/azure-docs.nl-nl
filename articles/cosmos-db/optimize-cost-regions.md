---
title: Optimaliseer de kosten voor implementaties met meerdere regio's in Azure Cosmos DB
description: In dit artikel wordt uitgelegd hoe u de kosten van implementaties met meerdere regio's in Azure Cosmos DB kunt beheren.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2020
ms.openlocfilehash: a559a51feafa310a4645282dc6368f520fc6b972
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96459621"
---
# <a name="optimize-multi-region-cost-in-azure-cosmos-db"></a>Multiregionale kosten optimaliseren in Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

U kunt op elk gewenst moment regio's toevoegen aan en verwijderen uit uw Azure Cosmos-account. De door u geconfigureerde door Voer voor verschillende Azure Cosmos-data bases en containers is gereserveerd in elke regio die aan uw account is gekoppeld. Als de door Voer is ingericht per uur, dat wil zeggen de som van RU/s die is geconfigureerd voor alle data bases en containers voor uw Azure Cosmos `T` -account, en het aantal Azure-regio's dat aan uw database account is gekoppeld, is de `N` totale ingerichte door Voer voor uw Cosmos-account voor een gegeven uur gelijk aan `T x N RU/s` .

Ingerichte doorvoer met één schrijfregio kost $ 0,008/uur per 100 RU/s, en ingerichte doorvoer met meerdere schrijfregio's kost $ 0,016/per uur per 100 RU/s. Zie Azure Cosmos DB- [pagina met prijzen](https://azure.microsoft.com/pricing/details/cosmos-db/)voor meer informatie.

## <a name="costs-for-multiple-write-regions"></a>Kosten voor meerdere schrijf regio's

In een systeem met meerdere regio's schrijft het net beschik bare RUs voor schrijf bewerkingen tot `N` `N` een toename van het aantal schrijf regio's. In tegens telling tot de schrijf bewerkingen in één regio is elke regio nu schrijfbaar en ondersteunt deze conflict oplossing. Van het kosten plannings punt van de weer gave, voor het `M` wereld wijd uitvoeren van ru/s voor schrijf bewerkingen, moet u M inrichten `RUs` op een container-of database niveau. U kunt vervolgens zoveel regio's toevoegen als u wilt en deze gebruiken voor schrijf bewerkingen voor het uitvoeren `M` van de wereld wijde schrijf bewerkingen van ru.

### <a name="example"></a>Voorbeeld

Houd er rekening mee dat u een container in VS West hebt geconfigureerd voor schrijf bewerkingen met één regio, ingericht met de door Voer van 10.000 RU/s, waarbij 0,5 TB aan gegevens deze maand worden opgeslagen. We gaan ervan uit dat u een regio, VS-Oost, met dezelfde opslag en door voer toevoegt en u de mogelijkheid wilt geven om naar de containers in beide regio's te schrijven vanuit uw app. Uw nieuwe totale maandelijkse factuur (waarbij 730 uur per maand wordt uitgegaan) is als volgt:

|**Item**|**Gebruik (maandelijks)**|**Snelheid**|**Maandelijkse kosten**|
|----|----|----|----|
|Doorvoer factuur voor container in West-VS (enkele schrijf regio) |10.000 RU/s * 730 uur |$0,008 per 100 RU/s per uur |$584 |
|Doorvoer factuur voor container in 2 regio's-vs-West & VS-Oost (meerdere schrijf regio's) |2 * 10.000 RU/s * 730 uur |$0,016 per 100 RU/s per uur |$2.336 |
|Opslag factuur voor container in West-VS |0,5 TB (of 512 GB) |$0,25/GB |$128 |
|Opslag factuur voor container in 2 regio's-vs-West & VS-Oost |2 * 0,5 TB (of 1.024 GB) |$0,25/GB |$256 |

## <a name="improve-throughput-utilization-on-a-per-region-basis"></a>Het doorvoer gebruik per regio verbeteren

Als u een inefficiënt gebruik hebt, bijvoorbeeld een of meer ondergeschikte Lees regio's, kunt u stappen nemen om het maximum gebruik van het RUs in Lees regio's te maken met behulp van Change feed van het Lees gebied of door het item naar een ander secundair te verplaatsen als dit wordt gebruikt. U moet ervoor zorgen dat de ingerichte door Voer (RUs) in de schrijf regio het eerst wordt geoptimaliseerd. Schrijf bewerkingen zijn meer dan lees bewerkingen, tenzij zeer grote query's worden uitgevoerd, waardoor zelfs het gebruik erg lastig kan zijn. Over het algemeen kunt u de verbruikte door Voer in uw regio's bewaken en regio's op aanvraag toevoegen of verwijderen om uw lees-en schrijf doorvoer te schalen, zodat u zeker weet wat de gevolgen zijn van latentie voor apps die in dezelfde regio zijn geïmplementeerd.

## <a name="next-steps"></a>Volgende stappen

Daarna kunt u meer te weten komen over cost Optimization in Azure Cosmos DB met de volgende artikelen:

* Meer informatie over het [optimaliseren voor ontwikkeling en testen](optimize-dev-test.md)
* Meer informatie over [uw Azure Cosmos DB factuur](understand-your-bill.md)
* Meer informatie over het [optimaliseren van doorvoer kosten](optimize-cost-throughput.md)
* Meer informatie over het [optimaliseren van opslag kosten](optimize-cost-storage.md)
* Meer informatie over [het optimaliseren van de kosten van lees-en schrijf bewerkingen](optimize-cost-reads-writes.md)
* Meer informatie over [het optimaliseren van de kosten van query's](./optimize-cost-reads-writes.md)
