---
title: bestand opnemen
description: bestand opnemen
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 01/05/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: f6bd0c13d5cbad802613e2bdea8fd6002f4deea2
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102444347"
---
Met Event Hub worden reeksen van gebeurtenissen in een of meer partities georganiseerd. Als er nieuwere gebeurtenissen plaatsvinden, worden deze toegevoegd aan het einde van deze reeks. Een partitie kan worden beschouwd als een 'doorvoerlogboek'.

Partities bevatten gebeurtenisgegevens met de hoofdtekst van de gebeurtenis, een door de gebruiker gedefinieerde set eigenschappen waarin de gebeurtenis wordt beschreven en metagegevens, zoals de offset in de partitie, het nummer in de stroomreeks en de service-side timestamp van de acceptatie.

![Diagram waarin de reeks gebeurtenissen van oud naar nieuw wordt weergegeven.](./media/event-hubs-partitions/partition.png)

Event Hubs is ontworpen om te helpen bij het verwerken van grote hoeveelheden gebeurtenissen. Partitioneren draagt hier op twee manieren aan bij:

Ten eerste, hoewel Event Hubs een PaaS-service is, is er wel een onderliggende fysieke realiteit. Het bijhouden van een logboek waarbij de volgorde van gebeurtenissen wordt behouden, vereist dat deze gebeurtenissen samen in de bijbehorende opslag en replica's worden bewaard. Dit betekent dat er een doorvoerplafond is voor een dergelijk logboek. Partitioneren maakt het mogelijk om meerdere parallelle logboeken te gebruiken voor dezelfde Event Hub en zo de beschikbare doorvoercapaciteit voor onbewerkte IO-gegevens te vermenigvuldigen.

Ten tweede moeten uw eigen toepassingen de verwerking van het volume aan gebeurtenissen dat naar een Event Hub wordt verzonden bij kunnen houden. Dit kan complex zijn en vereist aanzienlijke, uitgeschaalde en parallelle verwerkingscapaciteit. De reden voor partities is dezelfde als hierboven: De capaciteit van één proces voor het afhandelen van gebeurtenissen is beperkt en daarom hebt u verschillende processen nodig. Partities zijn de manier waarop uw oplossing deze processen voedt en zorgen ervoor dat elke gebeurtenis een duidelijke verwerkingseigenaar heeft. 

Event Hubs bewaart gebeurtenissen voor een geconfigureerde bewaartijd die wordt toegepast op het niveau van alle partities. Gebeurtenissen worden automatisch verwijderd wanneer de retentieperiode is bereikt. Als u een retentieperiode van één dag opgeeft, wordt de gebeurtenis precies 24 uur nadat deze is geaccepteerd onbeschikbaar. U kunt gebeurtenissen niet expliciet verwijderen. 

De toegestane retentieperiode is maximaal zeven dagen voor Event Hubs Standard en maximaal 90 dagen voor Event Hubs Dedicated. Als u gebeurtenissen wilt archiveren na de toegestane retentieperiode, kunt u deze [automatisch laten opslaan in Azure Storage of Azure Data Lake door de functie Event Hubs Capture in te schakelen](../articles/event-hubs/event-hubs-capture-overview.md). Als u dergelijke diepe archieven wilt doorzoeken of analyseren, kunt u ze [gemakkelijk importeren in Azure Synapse](../articles/event-hubs/store-captured-data-data-warehouse.md) of andere vergelijkbare opslagplaatsen en analyseplatforms. 

De limiet op de retentieperiode voor Event Hubs is om te voorkomen dat grote hoeveelheden historische klantgegevens worden vastgelegd in een archief dat alleen wordt geïndexeerd per timestamp en alleen sequentiële toegang toestaat. De architectuurfilosofie is dat voor historische gegevens uitgebreidere indexering en directere toegang nodig is dan de real-time interface voor gebeurtenissen die Event Hubs of Kafka bieden. Engines voor gebeurtenissenstromen zijn niet geschikt om te fungeren als data lakes of als langetermijnarchieven voor gebeurtenisbronnen. 

Omdat partities onafhankelijk zijn en hun eigen reeks gegevens bevatten, groeien ze vaak met verschillende snelheden. In Event Hubs is dat geen bezwaar waarvoor administratieve interventies nodig zijn, zoals in Apache Kafka, maar ongelijke distributie leidt tot ongelijkmatige belasting van uw downstream-gebeurtenisverwerkers.

![Event Hubs](./media/event-hubs-partitions/multiple-partitions.png)

Het aantal partities wordt opgegeven bij het maken en moet voor Event Hubs Standard tussen 1 en 32 liggen. Het aantal partities kan in Event Hubs Dedicated maximaal 2000 partities per capaciteitseenheid zijn. 

We raden u aan om ten minste zoveel partities te kiezen als u verwacht nodig te hebben voor aanhoudende [doorvoereenheden (TU's)](../articles/event-hubs/event-hubs-faq.md#what-are-event-hubs-throughput-units) tijdens piekbelasting van uw toepassing voor die specifieke Event Hub. U moet er rekening mee houden dat één partitie een doorvoercapaciteit heeft van 1 TU (1 MB ingaand, 2 MB uitgaand). U kunt de schaal van uw TU's aanpassen aan uw naamruimte of de capaciteitseenheden van uw cluster, onafhankelijk van het aantal partities. Een Event Hub met 32 partities of een Event Hub met één partitie genereren exact dezelfde kosten als de naamruimte is ingesteld op een capaciteit van één TU. 

Toepassingen regelen de toewijzing van gebeurtenissen aan partities op een van de volgende drie manieren:

- Door de partitiesleutel op te geven die consistent is toegewezen (met behulp van een hash-functie) aan een van de beschikbare partities. 
- Door geen partitiesleutel op te geven, zodat een broker voor iedere gebeurtenis een willekeurige partitie kan kiezen.
- Door expliciet gebeurtenissen te verzenden naar een specifieke partitie.

Als u een partitiesleutel opgeeft, kunnen gerelateerde gebeurtenissen in dezelfde partitie worden bewaard, in dezelfde volgorde waarin ze zijn verzonden. De partitiesleutel is een tekenreeks die is afgeleid van uw toepassingscontext en identificeert de relatie tussen de gebeurtenissen.

Een reeks gebeurtenissen die wordt geïdentificeerd door een partitiesleutel is een *stroom*. Een partitie is een multiplex-logboekopslag voor veel van zulke stromen. 

Het aantal partities van een Event Hub in een [toegewezen Event Hubs-cluster](../articles/event-hubs/event-hubs-dedicated-overview.md) kan worden [verhoogd](../articles/event-hubs/dynamically-add-partitions.md) nadat de Event Hub is gemaakt, maar de distributie van stromen over partities verandert als deze klaar is, omdat de toewijzing van partitiesleutels aan partities verandert. U kunt dus het beste proberen om dergelijke wijzigingen zoveel mogelijk te vermijden als de relatieve volgorde van gebeurtenissen in uw toepassing van belang is.

Het instellen van het aantal partities op de maximaal toegestane waarde is verleidelijk, maar u moet er altijd rekening mee houden dat uw gebeurtenissenstromen zo moeten worden gestructureerd dat u wel kunt profiteren van meerdere partities. Als u de volgorde van alle gebeurtenissen of voor een klein aantal substromen echt moet behouden, kunt u misschien niet profiteren van het gebruik van veel partities. Daarnaast maken veel partities de verwerkingszijde complexer. 

Hoewel u rechtstreeks gebeurtenissen naar partities kunt sturen, wordt dit niet aanbevolen. In plaats daarvan kunt u constructies op een hoger niveau gebruiken. Deze vindt u in de sectie [Gebeurtenisuitgever](../articles/event-hubs/event-hubs-features.md#event-publishers). 

