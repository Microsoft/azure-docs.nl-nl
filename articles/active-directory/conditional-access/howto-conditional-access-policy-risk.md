---
title: Op Risico's gebaseerde voorwaardelijke toegang op basis van een aanmelding-Azure Active Directory
description: Maak beleid voor voorwaardelijke toegang met behulp van aanmeldings risico voor identiteits beveiliging
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 07/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: a09c4513206bea3462577ecba49b5d77b655b0e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91628261"
---
# <a name="conditional-access-sign-in-risk-based-conditional-access"></a>Voorwaardelijke toegang: op Risico's gebaseerde voorwaardelijke toegang op basis van een aanmelding

De meeste gebruikers vertonen normaal gedrag dat kan worden getraceerd. Wanneer ze buiten de norm hiervoor vallen, zou het riskant kunnen zijn om hen toe te staan zich zomaar aan te melden. Mogelijk wilt u die gebruiker blok keren of kan hij of zij vragen om multi-factor Authentication uit te voeren om te bewijzen dat ze in werkelijkheid zijn. 

Een aanmeldings risico duidt op de kans dat een bepaalde verificatie aanvraag niet is geautoriseerd door de eigenaar van de identiteit. Organisaties met Azure AD Premium P2-licenties kunnen beleids regels voor voorwaardelijke toegang maken met inbegrip van [Azure AD Identity Protection risico detecties](../identity-protection/concept-identity-protection-risks.md#sign-in-risk).

Er zijn twee locaties waar dit beleid kan worden toegewezen. Organisaties moeten een van de volgende opties kiezen om een beleid voor voorwaardelijke toegang op basis van een aanmelding in te scha kelen waarvoor een beveiligd wacht woord moet worden gewijzigd.

## <a name="enable-with-conditional-access-policy"></a>Inschakelen met beleid voor voorwaardelijke toegang

1. Meld u aan bij de **Azure Portal** als globale beheerder, beveiligings beheerder of beheerder van de voorwaardelijke toegang.
1. Blader naar **Azure Active Directory**  >  **beveiligings**  >  **voorwaardelijke toegang**.
1. Selecteer **Nieuw beleid**.
1. Geef uw beleid een naam. Het is raadzaam dat organisaties een zinvolle norm maken voor de namen van hun beleid.
1. Onder **Toewijzingen** selecteert u **Gebruikers en groepen**.
   1. Onder **insluiten** selecteert u **alle gebruikers**.
   1. Onder **uitsluiten** selecteert u **gebruikers en groepen** en kiest u de accounts voor nood toegang of het afbreek glas van uw organisatie. 
   1. Selecteer **Gereed**.
1. Onder **Cloud-apps of acties**  >  , selecteert u **alle Cloud-apps**.
1. Stel onder **voor waarden**  >  **aanmeldings risico** **configureren** in op **Ja**. Onder **Selecteer het risico niveau voor aanmelden wordt dit beleid van toepassing op** 
   1. Selecteer **hoog** en **gemiddeld**.
   1. Selecteer **Gereed**.
1. Onder **toegangs beheer**  >  **toekennen** selecteert u **toegang verlenen**, **multi-factor Authentication vereisen** en selecteert u **selecteren**.
1. Bevestig de instellingen en stel **beleid inschakelen** in **op aan**.
1. Selecteer **maken** om uw beleid in te stellen.

## <a name="enable-through-identity-protection"></a>Identiteits beveiliging inschakelen

1. Meld u aan bij **Azure Portal**.
1. Selecteer **alle services** en blader naar **Azure AD Identity Protection**.
1. Selecteer **beleid voor aanmeldings Risico's**.
1. Onder **toewijzingen** selecteert u **gebruikers**.
   1. Onder **insluiten** selecteert u **alle gebruikers**.
   1. Onder **uitsluiten** selecteert u **uitgesloten gebruikers selecteren**, kiest u de accounts voor nood toegang of het account van uw organisatie en selecteert u **selecteren**.
   1. Selecteer **Gereed**.
1. Selecteer onder **voor waarden** **aanmeldings risico** en kies vervolgens **medium en hoger**.
   1. Selecteer **selecteren** en vervolgens **gereed**.
1. Kies onder **Controls**  >  **Access** de optie **toegang toestaan** en selecteer vervolgens **multi-factor Authentication vereisen**.
   1. Kies **Selecteren**.
1. Stel **beleid afdwingen** in **op aan**.
1. Selecteer **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

[Algemeen beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)

[Voorwaardelijke gebruikerstoegang op basis van risico's](howto-conditional-access-policy-risk-user.md)

[Effect bepalen met de modus alleen rapport-alleen voor voorwaardelijke toegang](howto-conditional-access-insights-reporting.md)

[Aanmeld gedrag simuleren met het What If hulp programma voor voorwaardelijke toegang](troubleshoot-conditional-access-what-if.md)

[Wat is Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md)
