---
title: Inleiding tot Automation in azure Sentinel | Microsoft Docs
description: In dit artikel worden de mogelijkheden van Security Orchestration, Automation en Response (via) van Azure Sentinel geïntroduceerd en worden de via-onderdelen beschreven-Automation rules en playbooks.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/14/2021
ms.author: yelevin
ms.openlocfilehash: 54d7c997ce17c927a692e84f6094e3a08f707af7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609356"
---
# <a name="security-orchestration-automation-and-response-soar-in-azure-sentinel"></a>Security Orchestration, Automation en Response (via) in azure Sentinel

In dit artikel worden de mogelijkheden voor beveiliging Orchestration, Automation en Response (via) van Azure Sentinel beschreven en wordt getoond hoe het gebruik van Automation-regels en playbooks als reactie op beveiligings Risico's de effectiviteit van uw SOC verhoogt en u tijd en bronnen bespaart.

## <a name="azure-sentinel-as-a-soar-solution"></a>Azure Sentinel als een via-oplossing

### <a name="the-problem"></a>Het probleem

SIEM/SOC-teams zijn doorgaans regel matig overspoeld met beveiligings waarschuwingen en incidenten, zodat de volumes zo groot zijn dat beschik bare mede werkers worden overspoeld. Dit resulteert al te vaak in situaties waarin veel waarschuwingen worden genegeerd en veel incidenten niet worden onderzocht, waardoor de organisatie kwetsbaar is voor aanvallen die niet worden opgemerkt.

### <a name="the-solution"></a>De oplossing

Azure Sentinel, naast een Security Information and Event Management-systeem (SIEM), is ook een platform voor de beveiliging van orchestrator, Automation en Response (via). Een van de belangrijkste doelen is het automatiseren van terugkerende en voorspel bare verrijkingen, reacties en herstel taken die de verantwoordelijkheid zijn van uw Security Operations Center en personeel (SOC/SecOps), het vrijmaken van tijd en bronnen voor uitgebreid onderzoek van en het zoeken naar geavanceerde bedreigingen. Automation vergt een aantal verschillende formulieren in de Azure-Sentinel, vanuit Automation-regels die de automatisering van incident verwerking en-antwoorden centraal beheren, om te playbooks dat vooraf vastgestelde reeksen acties worden uitgevoerd om krachtige en flexibele geavanceerde automatisering te bieden aan uw bedreigings respons taken.

## <a name="automation-rules"></a>Regels voor Automation

Automatiserings regels zijn een nieuw concept in azure Sentinel. Met deze functie kunnen gebruikers de automatisering van incident verwerking centraal beheren. Naast de mogelijkheid om playbooks toe te wijzen aan incidenten (niet alleen op waarschuwingen zoals voorheen), kunt u met automatiserings regels ook de reacties voor meerdere analyse regels tegelijk automatiseren, automatisch tags toewijzen, toekennen of sluiten van incidenten zonder playbooks, en de volg orde bepalen van de acties die worden uitgevoerd. Met Automation-regels worden automatiserings gebruik in azure Sentinel gestroomlijnd en kunt u complexe werk stromen vereenvoudigen voor de indelings processen van uw incident.

Meer informatie vindt u in deze [volledige uitleg over Automation-regels](automate-incident-handling-with-automation-rules.md).

> [!IMPORTANT]
>
> - De functie voor het **automatiseren van regels** is momenteel beschikbaar als **Preview-versie**. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor aanvullende juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of op andere wijze nog niet beschikbaar zijn in algemene Beschik baarheid.

## <a name="playbooks"></a>Playbooks

Een Playbook is een verzameling van reactie-en herstel acties en logica die kan worden uitgevoerd vanuit Azure Sentinel als een routine. Een Playbook kan u helpen om uw bedreigings antwoord te automatiseren en te organiseren, het kan worden geïntegreerd met andere systemen zowel intern als extern, en kan worden ingesteld om automatisch te worden uitgevoerd als reactie op specifieke waarschuwingen of incidenten, wanneer dit wordt geactiveerd door een analyse regel of een automatiserings regel. Het kan ook hand matig op aanvraag worden uitgevoerd als reactie op waarschuwingen op de pagina incidenten.

Playbooks in azure Sentinel zijn gebaseerd op werk stromen die zijn ingebouwd in [Azure Logic apps](../logic-apps/logic-apps-overview.md), een Cloud service die u helpt bij het plannen, automatiseren en organiseren van taken en werk stromen tussen systemen binnen de hele onderneming. Dit betekent dat playbooks kan profiteren van alle kracht en aanpassings mogelijkheden van Logic Apps integratie-en indelings mogelijkheden en gebruiks vriendelijke ontwerp hulpprogramma's en de schaal baarheid, betrouw baarheid en service niveau van een Azure-service op laag 1.

Meer informatie vindt u in deze [volledige uitleg over playbooks](automate-responses-with-playbooks.md).

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe Azure Sentinel Automation gebruikt om uw SOC efficiënter en efficiënt te kunnen werken.

- Zie de [verwerking van incidenten automatiseren in azure Sentinel](automate-incident-handling-with-automation-rules.md)voor meer informatie over het verwerken van incidenten.
- Zie [bedreigings reacties automatiseren met playbooks in azure Sentinel](automate-responses-with-playbooks.md)voor meer informatie over geavanceerde opties voor automatisering.
- Voor hulp bij het implementeren van Automation-regels en playbooks raadpleegt u [zelf studie: Playbooks gebruiken voor het automatiseren van bedreigings reacties in azure Sentinel](tutorial-respond-threats-playbook.md).
