---
title: Indringings tests | Microsoft Docs
description: In dit artikel vindt u een overzicht van het test proces voor indringing en een pen testen voor uw app die wordt uitgevoerd in de Azure-infra structuur.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: terrylan
ms.openlocfilehash: 9f65133f622c71189f35a1533e278ca09ab02085
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550198"
---
# <a name="penetration-testing"></a>Penetratietesten

Een van de voor delen van het gebruik van Azure voor het testen en implementeren van toepassingen is dat u snel omgevingen kunt maken. U hoeft zich geen zorgen te maken over uw eigen on-premises hardware voor het aanvragen van opdrachten, voor het verkrijgen en ' rekken en stapelen '.

Het snel maken van omgevingen is geweldig, maar u moet er wel voor zorgen dat u uw normale beveiligings toelaat. Een van de dingen die u waarschijnlijk wilt doen, is de toepassingen die u in azure implementeert, door penetratie testen.

Er worden geen indringings tests van uw toepassing voor u uitgevoerd, maar we begrijpen dat u dat wilt en moet testen uitvoeren op uw eigen toepassingen. Het is een goed idee, omdat wanneer u de beveiliging van uw toepassingen verbetert, u het volledige Azure-ecosysteem beter kunt beveiligen.

Sinds 15 juni 2017 heeft Microsoft vooraf geen goedkeuring meer nodig om een penetratietest uit te voeren voor Azure-resources. Dit proces is alleen gerelateerd aan Microsoft Azure en is niet van toepassing op een andere Microsoft Cloud service.

>[!IMPORTANT]
>Hoewel het niet meer nodig is dat micro soft op de hoogte wordt gebracht van de test activiteiten van de pen, moeten de klanten nog steeds voldoen aan de [Microsoft Cloud gecombineerde Indringings test regels van engagement](https://technet.microsoft.com/mt784683).

U kunt onder andere de volgende standaard tests uitvoeren:

* Testen op uw eind punten om de [OWASP (open Web Application Security project) van de Top 10](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project) van beveiligings problemen op te slaan
* [Fuzz-testen](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) van uw eindpunten
* [Poortscans](https://en.wikipedia.org/wiki/Port_scanner) van uw eindpunten

Een soort pen test die u niet kunt uitvoeren, is een [DOS-aanval (Denial of service)](https://en.wikipedia.org/wiki/Denial-of-service_attack) . Deze test omvat het initiëren van een DoS-aanval zelf of het uitvoeren van gerelateerde tests die het type DoS-aanval kunnen bepalen, demonstreren of simuleren.

>[!Note]
>Micro soft heeft een samen werking met BreakingPoint-Cloud voor het bouwen van een interface waarmee u verkeer kunt genereren op basis van DDoS Protection open bare IP-adressen voor simulaties. Zie [testen door middel van simulaties](../../ddos-protection/test-through-simulations.md)voor meer informatie over de BreakingPoint-Cloud simulatie.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [regels voor het testen van Indringings](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=2)methoden.
