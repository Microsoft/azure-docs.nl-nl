---
title: bestand opnemen
description: bestand opnemen
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 03/15/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 2cb203a00bb00767126f95e1fdc2f5aff8990f01
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103601271"
---
Event Hubs organiseert de volg orde van de gebeurtenissen die naar een Event Hub worden verzonden in een of meer partities. Als er nieuwere gebeurtenissen plaatsvinden, worden deze toegevoegd aan het einde van deze reeks. 

![Event Hubs](./media/event-hubs-partitions/multiple-partitions.png)

Een partitie kan worden beschouwd als een ' commit-logboek '. Partities bevatten gebeurtenis gegevens die de hoofd tekst van de gebeurtenis bevatten, een door de gebruiker gedefinieerde eigenschappen verzameling waarin de gebeurtenis is beschreven, meta gegevens, zoals de offset in de partitie, het nummer in de stroom reeks en de service-side time stamp waarop deze is geaccepteerd.

![Diagram waarin de reeks gebeurtenissen van oud naar nieuw wordt weergegeven.](./media/event-hubs-partitions/partition.png)

### <a name="advantages-of-using-partitions"></a>Voor delen van het gebruik van partities
Event Hubs is ontworpen om te helpen bij het verwerken van grote hoeveelheden gebeurtenissen. Partitioneren draagt hier op twee manieren aan bij:

- Hoewel Event Hubs een PaaS-service is, is er sprake van een fysieke realiteit en het onderhouden van een logboek waarbij de volg orde van gebeurtenissen wordt behouden, vereist dat deze gebeurtenissen samen in de onderliggende opslag en de replica's worden bewaard en dat resulteert in een doorvoer plafond voor een dergelijk logboek. Als u partitioneert, kunnen meerdere parallelle logboeken worden gebruikt voor dezelfde Event Hub en dus de beschik bare IO-doorvoer capaciteit van RAW vermenigvuldigen.
- Uw eigen toepassingen moeten de verwerking kunnen blijven gebruiken van het volume van de gebeurtenissen die naar een Event Hub worden verzonden. Dit kan complex zijn en vereist aanzienlijke, uitgeschaalde en parallelle verwerkingscapaciteit. De capaciteit van één proces voor het afhandelen van gebeurtenissen is beperkt, dus u hebt verschillende processen nodig. Partities zijn de manier waarop uw oplossing deze processen inschakelt en er nog steeds voor zorgt dat elke gebeurtenis een duidelijke verwerkings eigenaar heeft. 

### <a name="number-of-partitions"></a>Aantal partities
Het aantal partities wordt opgegeven bij het maken en moet voor Event Hubs Standard tussen 1 en 32 liggen. Het aantal partities kan in Event Hubs Dedicated maximaal 2000 partities per capaciteitseenheid zijn. 

We raden u aan om ten minste zoveel partities te kiezen als u verwacht nodig te hebben voor aanhoudende [doorvoereenheden (TU's)](../articles/event-hubs/event-hubs-faq.md#what-are-event-hubs-throughput-units) tijdens piekbelasting van uw toepassing voor die specifieke Event Hub. U moet er rekening mee houden dat één partitie een doorvoercapaciteit heeft van 1 TU (1 MB ingaand, 2 MB uitgaand). U kunt de schaal van uw TU's aanpassen aan uw naamruimte of de capaciteitseenheden van uw cluster, onafhankelijk van het aantal partities. Een Event Hub met 32 partities of een Event Hub met één partitie genereren exact dezelfde kosten als de naamruimte is ingesteld op een capaciteit van één TU. 

Het aantal partities van een Event Hub in een [toegewezen Event Hubs-cluster](../articles/event-hubs/event-hubs-dedicated-overview.md) kan worden [verhoogd](../articles/event-hubs/dynamically-add-partitions.md) nadat de Event Hub is gemaakt, maar de distributie van stromen over partities verandert als deze klaar is, omdat de toewijzing van partitiesleutels aan partities verandert. U kunt dus het beste proberen om dergelijke wijzigingen zoveel mogelijk te vermijden als de relatieve volgorde van gebeurtenissen in uw toepassing van belang is.

Het instellen van het aantal partities op de maximaal toegestane waarde is verleidelijk, maar u moet er altijd rekening mee houden dat uw gebeurtenissenstromen zo moeten worden gestructureerd dat u wel kunt profiteren van meerdere partities. Als u de volgorde van alle gebeurtenissen of voor een klein aantal substromen echt moet behouden, kunt u misschien niet profiteren van het gebruik van veel partities. Daarnaast maken veel partities de verwerkingszijde complexer. 


### <a name="mapping-of-events-to-partitions"></a>Toewijzing van gebeurtenissen aan partities
U kunt een partitiesleutel gebruiken om inkomende gebeurtenisgegevens toe te wijzen aan specifieke partities, zodat de gegevens kunnen worden geordend. De partitiesleutel is een door de afzender opgegeven waarde die aan een Event Hub wordt doorgegeven. De partitiesleutel wordt verwerkt door een statische hash-functie, die zorgt voor de partitietoewijzing. Als u bij het publiceren van een gebeurtenis geen partitiesleutel opgeeft, wordt er gebruikgemaakt van round robin-toewijzing.

De gebeurtenisuitgever is alleen op de hoogte van de partitiesleutel en niet van de partitie waarop de gebeurtenissen worden gepubliceerd. Deze ontkoppeling van sleutel en partitie schermt de afzender af, zodat deze niet te veel te weten hoeft te komen over de downstreamverwerking. Goede partitiesleutels zijn bijvoorbeeld een apparaatspecifieke of een gebruikersspecifieke identiteit, maar voor het groeperen van gerelateerde gebeurtenissen in dezelfde partitie kunnen ook andere kenmerken, zoals geografie, worden gebruikt.

Als u een partitiesleutel opgeeft, kunnen gerelateerde gebeurtenissen in dezelfde partitie worden bewaard, in dezelfde volgorde waarin ze zijn verzonden. De partitiesleutel is een tekenreeks die is afgeleid van uw toepassingscontext en identificeert de relatie tussen de gebeurtenissen. Een reeks gebeurtenissen die wordt geïdentificeerd door een partitiesleutel is een *stroom*. Een partitie is een multiplex-logboekopslag voor veel van zulke stromen. 

> [!NOTE]
> Hoewel u gebeurtenissen rechtstreeks naar partities kunt verzenden, wordt deze niet aanbevolen, met name als hoge Beschik baarheid belang rijk voor u is. Hiermee wordt de beschik baarheid van een Event Hub naar partitie niveau gedowngraded. Zie [Beschik baarheid en consistentie](../articles/event-hubs/event-hubs-availability-and-consistency.md)voor meer informatie.

