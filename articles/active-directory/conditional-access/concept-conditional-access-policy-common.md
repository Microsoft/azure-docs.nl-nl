---
title: Algemene beleids regels voor voorwaardelijke toegang-Azure Active Directory
description: Veelgebruikte beleids regels voor voorwaardelijke toegang voor organisaties
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 07/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdaaed72b85bc3c0196a8897be311efa5fb714e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92366340"
---
# <a name="common-conditional-access-policies"></a>Algemeen beleid voor voorwaardelijke toegang

De [standaard instellingen voor beveiliging](../fundamentals/concept-fundamentals-security-defaults.md) zijn handig voor sommige organisaties, maar er zijn meer flexibiliteit nodig dan ze bieden. Veel organisaties hebben bijvoorbeeld de mogelijkheid om specifieke accounts uit te sluiten, zoals de accounts voor toegang in nood gevallen of het afbreken van een account van het beleid voor voorwaardelijke toegang waarvoor multi-factor Authentication is vereist. De algemene beleids regels waarnaar in dit artikel wordt verwezen, kunnen worden gebruikt voor deze organisaties.

![Beleid voor voorwaardelijke toegang in de Azure Portal](./media/concept-conditional-access-policy-common/conditional-access-policies-azure-ad-listing.png)

## <a name="emergency-access-accounts"></a>Accounts voor toegang in nood gevallen

Meer informatie over accounts voor toegang in nood gevallen en waarom ze belang rijk zijn, vindt u in de volgende artikelen: 

* [Accounts voor noodtoegang beheren in Azure AD](../roles/security-emergency-access.md)
* [Maak een flexibele toegangs beheer strategie met Azure Active Directory](../authentication/concept-resilient-controls.md)

## <a name="typical-policies-deployed-by-organizations"></a>Typische beleids regels die door organisaties worden geïmplementeerd

* [Verouderde verificatie blok keren](howto-conditional-access-policy-block-legacy.md)\*
* [MFA vereisen voor beheerders](howto-conditional-access-policy-admin-mfa.md)\*
* [MFA vereisen voor Azure-beheer](howto-conditional-access-policy-azure-management.md)\*
* [MFA vereisen voor alle gebruikers](howto-conditional-access-policy-all-users-mfa.md)\*

\* Deze vier beleids regels worden samen geconfigureerd, waarbij de functionaliteit is ingeschakeld door de [standaard instellingen voor beveiliging](../fundamentals/concept-fundamentals-security-defaults.md).

## <a name="additional-policies"></a>Extra beleids regels

* [Aan aanmeldings Risico's gebaseerde voorwaardelijke toegang (vereist Azure AD Premium P2)](howto-conditional-access-policy-risk.md)
* [Voorwaardelijke toegang op basis van gebruikers risico (vereist Azure AD Premium P2)](howto-conditional-access-policy-risk-user.md)
* [Vertrouwde locatie vereisen voor MFA-registratie](howto-conditional-access-policy-registration.md)
* [Toegang op locatie blok keren](howto-conditional-access-policy-location.md)
* [Compatibel apparaat vereisen](howto-conditional-access-policy-compliant-device.md)
* [Toegang blok keren met uitzonde ring van specifieke apps](howto-conditional-access-policy-block-access.md)

## <a name="next-steps"></a>Volgende stappen

- [Simuleer aanmeldings gedrag met het hulp programma voor voorwaardelijke toegang What If.](troubleshoot-conditional-access-what-if.md)

- [Gebruik de modus alleen rapport voor voorwaardelijke toegang om de impact van nieuwe beleids beslissingen te bepalen.](concept-conditional-access-report-only.md)
