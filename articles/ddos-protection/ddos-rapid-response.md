---
title: Azure DDoS Rapid-antwoord
description: Leer hoe u DDoS-experts kunt benaderen tijdens een actieve aanval op gespecialiseerde ondersteuning.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: yitoh
ms.openlocfilehash: 3655b117b641a6179bd6539aaf3f75af07f21a1a
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94992366"
---
# <a name="azure-ddos-rapid-response"></a>Azure DDoS Rapid-antwoord

Tijdens een actieve toegang hebben Azure DDoS Protection standaard klanten toegang tot het DRR-team (DDoS Rapid Response), die u kan helpen bij het onderzoeken van aanvallen tijdens een aanval en na een aanvals analyse.

## <a name="prerequisites"></a>Vereisten

- Voordat u de stappen in deze zelf studie kunt volt ooien, moet u eerst een [Azure DDoS Standard-beveiligings plan](manage-ddos-protection.md)maken.

## <a name="when-to-engage-drr"></a>Wanneer DRR wordt ingeschakeld

U moet DRR alleen benaderen als: 

- Tijdens een DDoS-aanval als u merkt dat de prestaties van de beveiligde resource ernstig gedegradeerd zijn of dat de bron niet beschikbaar is. Bekijk stap 2 hierboven over het configureren van monitors om de beschik baarheid en prestatie problemen van resources te detecteren.
- U denkt dat uw resource zich onder DDoS-aanval bevindt, maar DDoS Protection Service de aanval niet effectief verkleint.
- U bent bezig met het plannen van een evenement waardoor uw netwerk verkeer aanzienlijk wordt verbeterd.
- Voor aanvallen met een belang rijke bedrijfs impact.

## <a name="engage-drr-during-an-active-attack"></a>DRR tijdens een actieve aanval benaderen

1. Kies bij Azure Portal tijdens het maken van een nieuwe ondersteunings aanvraag het **probleem type** technisch.
2. Kies **service** als **DDoS-beveiliging**.
3. Kies een resource in de vervolg keuzelijst resource. _U moet een DDoS-abonnement selecteren dat is gekoppeld aan het virtuele netwerk dat wordt beveiligd door DDoS Protection Standard om DRR te benaderen._

    ![Resource kiezen](./media/ddos-rapid-response/choose-resource.png)

4. Op de volgende **probleem** pagina selecteert u de **Ernst** als een kritieke impact en **probleem type** als ' onder aanval '.

    ![PSeverity en probleem type](./media/ddos-rapid-response/severity-and-problem-type.png)

5. Voer aanvullende details uit en verzend de ondersteunings aanvraag.

DRR volgt het Azure Rapid Response-ondersteunings model. Raadpleeg het [ondersteunings bereik en de reactie tijd](https://azure.microsoft.com/en-us/support/plans/response/) voor meer informatie over snelle reacties.

Lees de [DDoS Protection Standard-documentatie](./ddos-protection-overview.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [testen met simulaties](test-through-simulations.md).
- Meer informatie over het [weer geven en configureren van DDoS Protection-telemetrie](telemetry-monitoring-alerting.md).
- Meer informatie over het [configureren van DDoS voor risico beperking van rapporten en stroom logboeken](reports-and-flow-logs.md).