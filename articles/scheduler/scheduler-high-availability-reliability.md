---
title: Hoge beschikbaarheid en betrouwbaarheid
description: Meer informatie over hoge Beschik baarheid en betrouw baarheid in azure scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/16/2016
ms.openlocfilehash: 98a6672af7e74fdd0732f3ba03264d2f674eb44f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92368142"
---
# <a name="high-availability-and-reliability-for-azure-scheduler"></a>Hoge Beschik baarheid en betrouw baarheid voor Azure scheduler

> [!IMPORTANT]
> [Azure Logic apps](../logic-apps/logic-apps-overview.md) vervangt Azure scheduler, die buiten gebruik wordt [gesteld](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Als u wilt blijven werken met de taken die u in scheduler hebt ingesteld, moet u zo snel mogelijk [naar Azure Logic apps worden gemigreerd](../scheduler/migrate-from-scheduler-to-logic-apps.md) . 
>
> Scheduler is niet meer beschikbaar in de Azure Portal, maar de [rest API](/rest/api/scheduler) en [Azure scheduler Power shell-cmdlets](scheduler-powershell-reference.md) blijven op dit moment beschikbaar, zodat u uw taken en taak verzamelingen kunt beheren.

Azure Scheduler biedt zowel [hoge Beschik baarheid](/azure/architecture/framework/#resiliency) als betrouw baarheid voor uw taken. Zie [Sla voor scheduler](https://azure.microsoft.com/support/legal/sla/scheduler)voor meer informatie.

## <a name="high-availability"></a>Hoge beschikbaarheid

Azure scheduler is [Maxi maal beschikbaar] en maakt gebruik van geo-redundante service-implementatie en geo-regionale taak replicatie.

### <a name="geo-redundant-service-deployment"></a>Geo-redundante service-implementatie

Azure scheduler is beschikbaar in vrijwel [elke geografische regio die momenteel door Azure wordt ondersteund](https://azure.microsoft.com/global-infrastructure/regions/#services). Als een Azure-Data Center in een gehoste regio niet meer beschikbaar is, kunt u nog steeds Azure Scheduler gebruiken omdat de failover-functionaliteit van de service scheduler beschikbaar maakt vanuit een ander Data Center.

### <a name="geo-regional-job-replication"></a>Geo-regionale taak replicatie

Uw eigen taken in azure scheduler worden gerepliceerd tussen Azure-regio's. Als er een storing in één regio optreedt, wordt er een failover uitgevoerd van Azure scheduler en wordt ervoor verzekerd dat uw taak wordt gestart vanuit een ander Data Center in de gekoppelde geografische regio.

Als u bijvoorbeeld een taak in Zuid-Centraal VS maakt, repliceert Azure scheduler deze taak automatisch in Noord-Centraal vs. Als er een fout optreedt in Zuid-Centraal VS, voert Azure scheduler de taak uit in Noord-Centraal vs. 

![Geo-regionale taak replicatie](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image1.png)

Azure scheduler zorgt er ook voor dat uw gegevens binnen dezelfde, maar bredere geografische regio blijven, alleen als er een fout optreedt in Azure. U hoeft uw taken dus niet te dupliceren wanneer u alleen een hoge Beschik baarheid wilt. Azure Scheduler biedt automatisch hoge Beschik baarheid voor uw taken.

## <a name="reliability"></a>Betrouwbaarheid

Azure scheduler garandeert een eigen hoge Beschik baarheid, maar heeft een andere benadering voor door gebruikers gemaakte taken. Stel bijvoorbeeld dat uw taak een HTTP-eind punt aanroept dat niet beschikbaar is. Er wordt nog steeds geprobeerd om uw taak uit te voeren met Azure Scheduler door alternatieve manieren te geven voor het verwerken van fouten: 

* Beleid voor opnieuw proberen instellen.
* Alternatieve eind punten instellen.

<a name="retry-policies"></a>

### <a name="retry-policies"></a>Beleid voor opnieuw proberen

Met Azure Scheduler kunt u beleid voor opnieuw proberen instellen. Als een taak mislukt, wordt de taak door scheduler standaard vier keer per interval van 30 seconden herhaald. U kunt dit beleid voor opnieuw proberen agressief maken, bijvoorbeeld tien keer bij 30 seconden of minder agressief, bijvoorbeeld twee keer per dag.

Stel bijvoorbeeld dat u een wekelijkse taak maakt die een HTTP-eind punt aanroept. Als het HTTP-eind punt gedurende een paar uur niet beschikbaar is wanneer uw taak wordt uitgevoerd, wilt u mogelijk niet een andere week wachten totdat de taak opnieuw wordt uitgevoerd. dit gebeurt omdat het standaard beleid voor opnieuw proberen niet in dit geval werkt. Daarom wilt u mogelijk het standaard beleid voor opnieuw proberen te wijzigen zodat er een nieuwe poging wordt gedaan, bijvoorbeeld om de drie uur, in plaats van elke 30 seconden. 

Zie [retryPolicy](scheduler-concepts-terms.md#retrypolicy)voor meer informatie over het instellen van een beleid voor opnieuw proberen.

### <a name="alternate-endpoints"></a>Alternatieve eind punten

Als uw Azure scheduler-taak een eind punt aanroept dat onbereikbaar is, zelfs nadat het beleid voor opnieuw proberen is bereikt, gaat scheduler terug naar een ander eind punt dat dergelijke fouten kan verwerken. Als u dit eind punt instelt, wordt het eind punt door scheduler aangeroepen, waardoor uw eigen taken Maxi maal beschikbaar worden wanneer er fouten optreden.

Dit diagram laat bijvoorbeeld zien hoe scheduler het beleid voor opnieuw proberen volgt bij het aanroepen van een webservice in New York. Als de nieuwe pogingen mislukken, controleert Scheduler op een ander eind punt. Als het eind punt bestaat, begint scheduler met het verzenden van aanvragen naar het alternatieve eind punt. Hetzelfde beleid voor opnieuw proberen is van toepassing op zowel de oorspronkelijke actie als de alternatieve actie.

![Scheduler-gedrag met beleid voor opnieuw proberen en alternatief eind punt](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png)

Het actie type voor de alternatieve actie kan afwijken van de oorspronkelijke actie. Hoewel de oorspronkelijke actie bijvoorbeeld een HTTP-eind punt aanroept, kan de alternatieve actie fouten in het logboek registreren met behulp van een opslag wachtrij, Service Bus wachtrij of een actie van Service Bus onderwerp.

Zie [Error Action](scheduler-concepts-terms.md#error-action)voor meer informatie over het instellen van een alternatief eind punt.

## <a name="next-steps"></a>Volgende stappen

* [Concepten, terminologie en entiteitenhiërarchie](scheduler-concepts-terms.md)
* [Naslaginformatie over REST API van Azure Scheduler](/rest/api/scheduler)
* [Naslaginformatie over Azure Scheduler PowerShell-cmdlets](scheduler-powershell-reference.md)
* [Limieten, quota, standaardwaarden en foutcodes](scheduler-limits-defaults-errors.md)