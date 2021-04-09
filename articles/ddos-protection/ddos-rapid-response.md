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
ms.openlocfilehash: 8e860bf47420f2b58c44df695da7761bcc2aa0ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100521778"
---
# <a name="azure-ddos-rapid-response"></a>Azure DDoS Rapid-antwoord

Tijdens een actieve toegang hebben Azure DDoS Protection standaard klanten toegang tot het DRR-team (DDoS Rapid Response), die kan helpen bij het onderzoeken van aanvallen tijdens een aanval en na een aanval.

## <a name="prerequisites"></a>Vereisten

- Voordat u de stappen in deze zelf studie kunt volt ooien, moet u eerst een [Azure DDoS Standard-beveiligings plan](manage-ddos-protection.md)maken.

## <a name="when-to-engage-drr"></a>Wanneer DRR wordt ingeschakeld

U moet DRR alleen benaderen als: 

- Tijdens een DDoS-aanval als u merkt dat de prestaties van de beveiligde resource ernstig gedegradeerd zijn of dat de bron niet beschikbaar is. 
- U denkt dat uw resource wordt aangevallen door een DDoS-aanval en dat DDoS Protection-service de aanval niet effectief verkleint.
- U bent bezig met het plannen van een viraal evenement waardoor uw netwerkverkeer aanzienlijk wordt verhoogd.
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
- Meer informatie over het [weer geven en configureren van DDoS Protection-telemetrie](telemetry.md).
- Meer informatie over het [weer geven en configureren van DDoS diagnostische logboek registratie](diagnostic-logging.md).
