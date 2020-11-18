---
title: Risico beleid-Azure Active Directory Identity Protection
description: Risico beleid inschakelen en configureren in Azure Active Directory Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 366d68be1a7f115980973015e363da6095876754
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94835914"
---
# <a name="how-to-configure-and-enable-risk-policies"></a>Procedure: risico beleid configureren en inschakelen

Zoals we hebben geleerd in het vorige artikel, hebben [beleids regels voor identiteits beveiliging](concept-identity-protection-policies.md) twee risico beleidsregels die we in onze Directory kunnen inschakelen. 

- Beleid voor aanmeldingsrisico's
- Beleid voor gebruikersrisico's

![Pagina beveiligings overzicht voor het inschakelen van beleid voor gebruikers-en aanmeldings Risico's](./media/howto-identity-protection-configure-risk-policies/identity-protection-security-overview.png)

Beide beleids regels werken samen om de reactie op risico detecties in uw omgeving te automatiseren en gebruikers in staat te stellen zichzelf te herstellen wanneer risico wordt gedetecteerd. 

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]

## <a name="prerequisites"></a>Vereisten 

Als uw organisatie gebruikers wilt toestaan zichzelf op te lossen wanneer er Risico's worden gedetecteerd, moeten gebruikers worden geregistreerd voor selfservice voor het opnieuw instellen van wacht woorden en Azure AD-Multi-Factor Authentication. U wordt aangeraden [de gecombineerde beveiligings informatie registratie in te scha kelen](../authentication/howto-registration-mfa-sspr-combined.md) voor de beste ervaring. Door gebruikers toe te staan zichzelf op te lossen, worden ze sneller teruggebracht naar een productieve status zonder tussen komst van de beheerder. Beheerders kunnen deze gebeurtenissen nog steeds zien en ze na het feit onderzoeken. 

## <a name="choosing-acceptable-risk-levels"></a>Acceptabele risico niveaus kiezen

Organisaties moeten het risico niveau bepalen dat ze nodig hebben om de gebruikers ervaring en beveiligings postuur te accepteren. 

De aanbeveling van micro soft is de drempel waarde voor het gebruikers risico beleid in te stellen op **hoog** en het beleid voor aanmeldings risico op **gemiddeld en hoger**.

Het kiezen van een **hoge** drempel waarde vermindert het aantal keren dat een beleid wordt geactiveerd en minimaliseert de gevolgen voor gebruikers. Er worden echter **weinig** en **gemiddeld** risico detecties uit het beleid uitgesloten, waardoor een aanvaller niet kan worden misbruikt van een aangetast identiteit. Als u een **lage** drempel waarde selecteert, worden extra gebruikers onderbrekingen geïntroduceerd, maar wordt de beveiligings postuur verbeterd.

## <a name="exclusions"></a>Uitsluitingen

Met alle beleids regels kunt u uitzonde ring van gebruikers, zoals uw [beheerders accounts voor nood toegang of afbreek glazen](../roles/security-emergency-access.md), toestaan. Organisaties kunnen bepalen dat andere accounts moeten worden uitgesloten van specifieke beleids regels op basis van de manier waarop de accounts worden gebruikt. Alle uitsluitingen moeten regel matig worden gecontroleerd om te zien of ze nog steeds van toepassing zijn.

Geconfigureerde vertrouwde [netwerk locaties](../conditional-access/location-condition.md) worden door de identiteits beveiliging in enkele risico detecties gebruikt om fout-positieven te verminderen.

## <a name="enable-policies"></a>Beleid inschakelen

Voer de volgende stappen uit om het risico beleid voor gebruikers Risico's en-aanmelding in te scha kelen.

1. Navigeer naar [Azure Portal](https://portal.azure.com).
1. Blader naar **Azure Active Directory**  >  **beveiligings**  >  **identiteits beveiliging**  >  **Overview**.
1. Selecteer **beleid voor gebruikers Risico's**.
   1. Onder **toewijzingen**
      1. **Gebruikers** : Kies **alle gebruikers** of **Selecteer individuen en groepen** als u de implementatie wilt beperken.
         1. Optioneel kunt u ervoor kiezen om gebruikers uit te sluiten van het beleid.
      1. **Voor waarden**  -  **Gebruikers risico** De aanbeveling van micro soft is om deze optie in te stellen op **hoog**.
   1. Onder **besturings elementen**
      1. **Toegang** : aanbeveling van micro soft is **toegang toe te staan** en **wachtwoord wijziging te vereisen**.
   1. **Beleid**  -  afdwingen **Op**
   1. **Opslaan** : met deze actie keert u terug naar de pagina **overzicht** .
1. Selecteer **beleid voor aanmeldings Risico's**.
   1. Onder **toewijzingen**
      1. **Gebruikers** : Kies **alle gebruikers** of **Selecteer individuen en groepen** als u de implementatie wilt beperken.
         1. Optioneel kunt u ervoor kiezen om gebruikers uit te sluiten van het beleid.
      1. **Voor waarden**  -  **Aanmeldings risico** De aanbeveling van micro soft is om deze optie in te stellen op **gemiddeld en hoger**.
   1. Onder **besturings elementen**
      1. **Toegang** -aanbeveling van micro soft is om **toegang toe te staan** en **multi-factor Authentication te vereisen**.
   1. **Beleid**  -  afdwingen **Op**
   1. **Opslaan**

## <a name="next-steps"></a>Volgende stappen

- [Azure AD Multi-Factor Authentication-registratie beleid inschakelen](howto-identity-protection-configure-mfa-policy.md)

- [What is risk](concept-identity-protection-risks.md) (Wat is een risico?)

- [Risicodetectie onderzoeken](howto-identity-protection-investigate-risk.md)

- [Risicodetectie simuleren](howto-identity-protection-simulate-risk.md)
