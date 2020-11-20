---
title: Geografisch nood herstel in Azure Event Grid | Microsoft Docs
description: Hierin wordt beschreven hoe Azure Event Grid geo-nood herstel (GeoDR) automatisch ondersteunt.
ms.topic: conceptual
ms.date: 11/19/2020
ms.openlocfilehash: 10beaf0ae25f3ed9b7bcda5961a89494b18b84d9
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94980845"
---
# <a name="server-side-geo-disaster-recovery-in-azure-event-grid"></a>Geografisch nood herstel aan de server zijde in Azure Event Grid
Event Grid hebt nu een automatische geo-nood herstel (GeoDR) van meta gegevens, niet alleen voor nieuwe, maar alle bestaande domeinen, onderwerpen en gebeurtenis abonnementen. Als een hele Azure-regio uitvalt, worden er al met de meta gegevens van de infra structuur van uw gebeurtenis een gepaarde regio gesynchroniseerd met Event Grid. Uw nieuwe evenementen worden opnieuw stromen zonder tussen komst van u. 

Herstel na nood gevallen wordt gemeten met twee metrische gegevens:

- Beoogd herstel punt (RPO): de minuten of uren aan gegevens die mogelijk verloren zijn gegaan.
- Beoogde herstel tijd (RTO): het aantal minuten of uren dat de service niet beschikbaar is.

De automatische failover van Event Grid heeft verschillende Rpo's en Rto's voor uw meta gegevens (gebeurtenis abonnementen, enzovoort) en gegevens (gebeurtenissen). Als u een andere specificatie van de volgende items nodig hebt, kunt u uw eigen [failover van de client implementeren met behulp van de onderwerp Health-api's](custom-disaster-recovery.md).

## <a name="recovery-point-objective-rpo"></a>Recovery Point Objective (RPO)
- **RPO van meta gegevens**: nul minuten. Telkens wanneer een resource wordt gemaakt in Event Grid, wordt deze direct gerepliceerd tussen regio's. Wanneer een failover optreedt, gaan er geen meta gegevens verloren.
- **Data RPO**: als uw systeem in orde is en op het moment van de regionale failover op het bestaande verkeer is, is de RPO voor gebeurtenissen ongeveer 5 minuten.

## <a name="recovery-time-objective-rto"></a>Beoogde hersteltijd (RTO)
- **Meta gegevens RTO**: Hoewel dit doorgaans veel sneller plaatsvindt, binnen 60 minuten, begint Event grid met het accepteren van aanroepen voor maken/bijwerken/verwijderen voor onderwerpen en abonnementen.
- **Data RTO**: zoals meta gegevens, gebeurt dit doorgaans veel sneller, maar binnen 60 minuten gaat Event grid het accepteren van nieuw verkeer na een regionale failover.

> [!NOTE]
> De kosten voor de GeoDR van meta gegevens op Event Grid zijn: $0.


## <a name="next-steps"></a>Volgende stappen
Zie [# uw eigen herstel na nood gevallen maken voor aangepaste onderwerpen in Event grid voor](custom-disaster-recovery.md) meer informatie over het implementeren van een failover-logica aan de client zijde.
