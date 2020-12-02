---
title: Indringings tests | Microsoft Docs
description: In dit artikel vindt u een overzicht van het test proces voor indringing en een pen testen voor uw app die wordt uitgevoerd in de Azure-infra structuur.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2020
ms.author: terrylan
ms.openlocfilehash: af61b6ee1e69d175f47170df30f75832033d61d5
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96489714"
---
# <a name="penetration-testing"></a>Penetratietesten

Een van de voor delen van het gebruik van Azure voor het testen en implementeren van toepassingen is dat u snel omgevingen kunt maken. U hoeft zich geen zorgen te maken over uw eigen on-premises hardware voor het aanvragen van opdrachten, voor het verkrijgen en ' rekken en stapelen '.

Het snel maken van omgevingen is geweldig, maar u moet er wel voor zorgen dat u uw normale beveiligings toelaat. Een van de dingen die u waarschijnlijk wilt doen, is de toepassingen die u in azure implementeert, door penetratie testen.

Mogelijk weet u dat micro soft de [Azure-omgeving indringings tests](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)uitvoert. Deze test helpt Azure-verbeteringen te stimuleren.

Er worden geen indringings tests van uw toepassing voor u uitgevoerd, maar we begrijpen dat u dat wilt en moet testen uitvoeren op uw eigen toepassingen. Het is een goed idee, omdat wanneer u de beveiliging van uw toepassingen verbetert, u het volledige Azure-ecosysteem beter kunt beveiligen.

Vanaf 15 juni 2017 heeft micro soft niet langer vooraf-goed keuring nodig voor het uitvoeren van een indringings test op Azure-resources. Dit proces is alleen gerelateerd aan Microsoft Azure en is niet van toepassing op een andere Microsoft Cloud service.

>[!IMPORTANT]
>Hoewel het niet meer nodig is dat micro soft op de hoogte wordt gebracht van de test activiteiten van de pen, moeten de klanten nog steeds voldoen aan de [Microsoft Cloud gecombineerde Indringings test regels van engagement](https://technet.microsoft.com/mt784683).

U kunt onder andere de volgende standaard tests uitvoeren:

* Testen op uw eind punten om de [OWASP (open Web Application Security project) van de Top 10](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project) van beveiligings problemen op te slaan
* [Fuzzy tests](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) van uw eind punten
* [Poort scans](https://en.wikipedia.org/wiki/Port_scanner) van uw eind punten

Een soort pen test die u niet kunt uitvoeren, is een [DOS-aanval (Denial of service)](https://en.wikipedia.org/wiki/Denial-of-service_attack) . Deze test omvat het initiëren van een DoS-aanval zelf of het uitvoeren van gerelateerde tests die het type DoS-aanval kunnen bepalen, demonstreren of simuleren.

>[!Note]
>Micro soft heeft een samen werking met BreakingPoint-Cloud voor het bouwen van een interface waarmee u verkeer kunt genereren op basis van DDoS Protection open bare IP-adressen voor simulaties. Zie [Validate DDoS Detection](../../ddos-protection/manage-ddos-protection.md#validate-and-test)(Engelstalig) voor meer informatie over de Cloud simulatie van het onderbrekings punt.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [regels voor het testen van Indringings](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=2)methoden.