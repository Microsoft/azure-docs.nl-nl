---
title: Identiteits beveiliging en B2B-gebruikers-Azure Active Directory
description: Identiteits beveiliging gebruiken met B2B-gebruikers hoe het werkt en bekende beperkingen
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9ec9e110c3a476c9096ae3e216c9780da0e0f49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88949193"
---
# <a name="identity-protection-and-b2b-users"></a>Identiteitsbeveiliging en B2B-gebruikers

Met Azure AD B2B-samen werking kunnen organisaties op risico gebaseerd beleid afdwingen voor B2B-gebruikers die gebruikmaken van identiteits beveiliging. Deze beleids regels worden op twee manieren geconfigureerd:

- Beheerders kunnen het ingebouwde beleid op risico gebaseerd op identiteits beveiliging configureren dat van toepassing is op alle apps die gast gebruikers bevatten.
- Beheerders kunnen hun beleids regels voor voorwaardelijke toegang configureren, met behulp van aanmeldings risico als voor waarde, die gast gebruikers bevat.

## <a name="how-is-risk-evaluated-for-b2b-collaboration-users"></a>Hoe wordt risico geëvalueerd voor B2B-samenwerkings gebruikers

De gebruikers Risico's voor B2B-samenwerkings gebruikers worden geëvalueerd in hun eigen basismap. Het realtime aanmeldings risico voor deze gebruikers wordt geëvalueerd op de Resource Directory wanneer ze proberen toegang te krijgen tot de resource.

## <a name="limitations-of-identity-protection-for-b2b-collaboration-users"></a>Beperkingen van identiteits beveiliging voor B2B-samenwerkings gebruikers

Er zijn beperkingen in de implementatie van identiteits beveiliging voor B2B-samenwerkings gebruikers in een resource directory, omdat hun identiteit in hun eigen basismap is bestaande. De belangrijkste beperkingen zijn als volgt:

- Als een gast gebruiker het beleid voor gebruikers Risico's voor identiteits beveiliging activeert om het wacht woord opnieuw in te stellen, **wordt deze geblokkeerd**. Dit blok wordt veroorzaakt door het onvermogen om wacht woorden opnieuw in te stellen in de resource directory.
- **Gast gebruikers worden niet weer gegeven in het rapport Risk ante gebruikers**. Dit verlies van zicht baarheid wordt veroorzaakt door de risico-evaluatie in de basismap van de B2B-gebruiker.
- Beheerders **kunnen een Risk ante B2B-samenwerkings gebruiker niet** uit de Resource Directory verwijderen of herstellen. Dit verlies van functionaliteit wordt veroorzaakt door beheerders in de map resource die geen toegang hebben tot de basismap van de B2B-gebruiker.

### <a name="why-cant-i-remediate-risky-b2b-collaboration-users-in-my-directory"></a>Waarom kan ik geen risico volle B2B-samenwerkings gebruikers in mijn map herstellen?

De risico-evaluatie en herstel voor B2B-gebruikers vindt plaats in hun basis directory. Als gevolg hiervan worden de gast gebruikers niet weer gegeven in het rapport Risk ante gebruikers in de resource directory en beheerders in de resource directory kan geen beveiligd wacht woord opnieuw instellen afdwingen voor deze gebruikers.

### <a name="what-do-i-do-if-a-b2b-collaboration-user-was-blocked-due-to-a-risk-based-policy-in-my-organization"></a>Wat moet ik doen als een B2B-samenwerkings gebruiker is geblokkeerd vanwege een op risico gebaseerd beleid in mijn organisatie?

Als een Risk ante B2B-gebruiker in uw Directory wordt geblokkeerd door uw beleid op basis van Risico's, moet de gebruiker dat risico herstellen in hun basismap. Gebruikers kunnen hun risico herstellen door een veilig wacht woord opnieuw in te stellen in de hoofdmap. Als er geen selfservice voor wachtwoord herstel is ingeschakeld in de basismap, moeten ze contact opnemen met de IT-afdeling van hun organisatie zodat een beheerder hun risico hand matig kan negeren of hun wacht woord opnieuw moet instellen.

### <a name="how-do-i-prevent-b2b-collaboration-users-from-being-impacted-by-risk-based-policies"></a>Hoe kan ik voor komen dat gebruikers van B2B-samen werking worden beïnvloed door op risico gebaseerd beleid?

Met uitzonde ring van B2B-gebruikers uit het op risico gebaseerd beleid voor voorwaardelijke toegang van uw organisatie kunnen B2B-gebruikers niet worden beïnvloed of geblokkeerd door hun risico-evaluatie. Als u deze B2B-gebruikers wilt uitsluiten, maakt u een groep in azure AD die alle gast gebruikers van uw organisatie bevat. Voeg deze groep vervolgens als uitsluiting toe voor het beleid voor de gebruikers Risico's en aanmeldings Risico's voor de ingebouwde identiteits beveiliging en de beleids regels voor voorwaardelijke toegang die gebruikmaken van aanmeldings risico als voor waarde.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen over Azure AD B2B-samen werking:

- [Wat is Azure AD B2B-samenwerking?](../external-identities/what-is-b2b.md)