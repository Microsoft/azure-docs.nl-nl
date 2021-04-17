---
title: Uw Oracle-database herstellen op azure BareMetal-infrastructuur
description: Meer informatie over het herstellen van uw Oracle-database op de Azure BareMetal-infrastructuur.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/15/2021
ms.openlocfilehash: fc7464474d01314a52a77e0f28b1df160a9f42a0
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590243"
---
# <a name="recover-your-oracle-database-on-azure-baremetal-infrastructure"></a>Uw Oracle-database herstellen op azure BareMetal-infrastructuur

Hoewel geen enkele technologie bescherming biedt tegen alle foutscenario's, biedt het combineren van functies databasebeheerders de mogelijkheid om hun database in vrijwel elke situatie te herstellen.

## <a name="causes-of-database-failure"></a>Oorzaken van databasefout

Databasefouten kunnen om verschillende redenen optreden, maar vallen doorgaans onder verschillende categorieën:

- Fouten bij gegevensmanipulatie.
- Verlies van online logboeken voor opnieuw doen.
- Verlies van databasebeheerbestanden.
- Verlies van databasegegevensbestanden.
- Beschadiging van fysieke gegevens.

## <a name="choose-your-method-of-recovery"></a>Uw herstelmethode kiezen

Het type herstel is afhankelijk van het type fout. Stel dat een object is uitgevallen of dat de gegevens onjuist zijn gewijzigd. De snelste oplossing is dan meestal het uitvoeren van een flashback-databasebewerking. In andere gevallen kan het herstellen via een Azure NetApp Files mogelijk het herstel bieden dat u wilt. De beslissingsstructuur in de volgende afbeelding vertegenwoordigt veelvoorkomende fout- en herstelscenario's als alle hierboven beschreven opties voor gegevensbeveiliging zijn geïmplementeerd.

:::image type="content" source="media/oracle-high-availability/db-recovery-decision-tree.png" alt-text="Diagram van de beslissingsstructuur voor databaseherstel." lightbox="media/oracle-high-availability/db-recovery-decision-tree.png":::

Houd er rekening mee dat deze voorbeeldbeslissingsstructuur alleen wordt bekeken vanuit de lens van een databasebeheerder. Elke implementatie kan verschillende vereisten hebben die de volgorde van de keuzes kunnen wijzigen. Het uitvoeren van een databaserolschakelaar naar een andere regio via Data Guard kan bijvoorbeeld een negatief effect hebben op de prestaties van de toepassing. Dit kan de herstelmethode voor momentopnamen een lagere RTO geven. Om ervoor te zorgen dat aan de RTO/RPO-vereisten wordt voldaan, raden we u aan deze bewerkingen uit te voeren en gedocumenteerde procedures te maken om deze uit te voeren wanneer dat nodig is.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over BareMetal Infrastructure:

- [Wat is BareMetal Infrastructure in Azure?](../../concepts-baremetal-infrastructure-overview.md)
- [BareMetal Infrastructure-instanties verbinden in Azure](../../connect-baremetal-infrastructure.md)
