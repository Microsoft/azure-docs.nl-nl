---
title: bestand opnemen
description: bestand opnemen
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 11/19/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 48cc6b84fe88676a03d1bb6e0a8154c16e3ef618
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96007428"
---
Event Hubs daarentegen biedt streaming van berichten via een model op basis van gepartitioneerd gebruik, waarbij elke consumer slechts een specifieke subset of partitie van de berichtenstroom leest. Dit patroon maakt een horizontale schaal voor de verwerking van gebeurtenissen mogelijk en biedt andere stroomgerichte functies die niet beschikbaar zijn in wachtrijen en onderwerpen.

Een partitie is een geordende reeks gebeurtenissen die in een Event Hub wordt bewaard. Als er nieuwere gebeurtenissen plaatsvinden, worden deze toegevoegd aan het einde van deze reeks. Een partitie kan worden beschouwd als een 'doorvoerlogboek'.

![Diagram waarin de reeks gebeurtenissen van oud naar nieuw wordt weergegeven.](./media/event-hubs-partitions/partition.png)

Event Hubs bewaart gegevens voor een geconfigureerde bewaartijd die wordt toegepast op het niveau van alle partities in de Event Hub. Gebeurtenissen verlopen op basis van tijd. U kunt ze niet expliciet verwijderen. Omdat partities onafhankelijk zijn en hun eigen reeks gegevens bevatten, groeien ze vaak met verschillende snelheden.

![Event Hubs](./media/event-hubs-partitions/multiple-partitions.png)

Het aantal partities wordt opgegeven bij het maken en moet tussen 1 en 32 liggen. Het aantal partities kan niet worden gewijzigd. Houd bij het instellen van het aantal partities dus uw doelen op de lange termijn in gedachten. Partities zijn een mechanisme voor gegevensordening. Ze hebben betrekking op de mate van downstreamparallelheid die is vereist bij het gebruik van toepassingen. Het aantal partities in een Event Hub houdt rechtstreeks verband met het aantal verwachte gelijktijdige lezers. Neem contact op met het team van Event Hubs als u meer dan 32 partities wilt maken.

Het is raadzaam deze op het moment van maken in te stellen op de hoogste waarde: 32. Houd er rekening mee dat als u meer dan één partitie hebt, dit ertoe leidt dat gebeurtenissen in meerdere partities worden verzonden zonder de volgorde aan te houden, tenzij u verzenders zo configureert dat deze één partitie uit 32 verzenden. Maar daardoor zijn de overige 31 overbodig. In het eerste geval moet u gebeurtenissen lezen in alle 32 partities. In het laatste geval zijn er geen duidelijke extra kosten, los van de extra configuratie die u moet instellen in de gebeurtenisverwerkingshost.

Partities kunnen worden geïdentificeerd en rechtstreeks gegevens ontvangen, maar dit wordt niet aanbevolen. In plaats daarvan kunt u constructies op een hoger niveau gebruiken. Deze vindt u in de sectie [Gebeurtenisuitgever](../articles/event-hubs/event-hubs-features.md#event-publishers). 

Partities bestaan uit een reeks gebeurtenisgegevens. Deze bevatten de hoofdtekst van de gebeurtenis, een door de gebruiker gedefinieerde eigenschappenverzameling en metagegevens, zoals de offset in de partitie en het nummer in de stroomreeks.

We raden u aan het aantal doorvoereenheden en partities 1:1 op elkaar af te stemmen, zodat u een optimale schaal bereikt. Eén partitie heeft een gegarandeerde opname en uitvoer van maximaal één doorvoereenheid. Hoewel het mogelijk is om in een partitie meer doorvoer te behalen, worden de prestaties niet gegarandeerd. Daarom raden we ten zeerste aan dat het aantal partities in een Event Hub groter is dan of gelijk is aan het aantal doorvoereenheden.

Gezien de totale doorvoer die u nodig denkt te hebben, weet u hoeveel doorvoereenheden en het minimale aantal partities u nodig hebt, maar hoeveel partities moet u echt hebben? Kies het aantal partities op basis van de parallelle downstreamuitvoering die u wilt, evenals uw doorvoerbehoeften in de toekomst. Er worden geen kosten in rekening gebracht voor het aantal partities dat u binnen een Event Hub hebt.

Zie de artikelen [Programmeergids voor Event Hubs](../articles/event-hubs/event-hubs-programming-guide.md#partition-key) en [Beschikbaarheid en consistentie in Event Hubs](../articles/event-hubs/event-hubs-availability-and-consistency.md) voor meer informatie over partities en de verhouding tussen de beschikbaarheid en betrouwbaarheid.
