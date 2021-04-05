---
title: Azure AD Identity Protection beleid
description: De drie beleids regels identificeren die zijn ingeschakeld met identiteits beveiliging
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28a9080ce878e262573adf0b3c79394079c09ca2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94835965"
---
# <a name="identity-protection-policies"></a>Identiteitsbeveiligingbeleid

Azure Active Directory Identity Protection bevat drie standaard beleidsregels die beheerders kunnen kiezen om in te scha kelen. Deze beleids regels zijn beperkt aanpassen, maar zijn van toepassing op de meeste organisaties. Met alle beleids regels kunt u uitzonde ring van gebruikers, zoals uw [beheerders accounts voor nood toegang of afbreek glazen](../roles/security-emergency-access.md), toestaan.

![Identiteitsbeveiligingbeleid](./media/concept-identity-protection-policies/identity-protection-policies.png)

## <a name="azure-ad-mfa-registration-policy"></a>Azure AD MFA-registratie beleid

Identiteits beveiliging kan organisaties helpen bij het implementeren van Azure AD Multi-Factor Authentication (MFA) met behulp van een beleid voor voorwaardelijke toegang dat registratie bij het aanmelden vereist. Het inschakelen van dit beleid is een uitstekende manier om ervoor te zorgen dat nieuwe gebruikers in uw organisatie op de eerste dag zijn geregistreerd voor MFA. Multi-factor Authentication is een van de methoden voor het automatisch door voeren van risico gebeurtenissen binnen de identiteits beveiliging. Met zelf herstel kunnen uw gebruikers zelf actie ondernemen om het volume van de helpdesk oproep te verminderen.

Meer informatie over Azure AD Multi-Factor Authentication vindt u in het artikel, [hoe het werkt: Azure AD-multi-factor Authentication](../authentication/concept-mfa-howitworks.md).

## <a name="sign-in-risk-policy"></a>Beleid voor aanmeldingsrisico's

Identiteits beveiliging analyseert signalen van elke aanmelding, zowel in realtime als offline en berekent een risico Score op basis van de kans dat de gebruiker zich niet heeft aangemeld. Beheerders kunnen een beslissing nemen op basis van dit signaal van een risico Score om de vereisten van de organisatie af te dwingen. Beheerders kunnen kiezen om de toegang te blok keren, toegang toe te staan of toegang toe te staan, maar multi-factor Authentication te vereisen.

Als risico wordt gedetecteerd, kunnen gebruikers multi-factor Authentication uitvoeren om zichzelf op te lossen en de Risk ante aanmeldings gebeurtenis te sluiten om onnodige ruis voor beheerders te voor komen.

> [!NOTE] 
> Gebruikers moeten eerder zijn geregistreerd voor Azure AD Multi-Factor Authentication voordat ze het aanmeldings risico beleid kunnen activeren.

### <a name="custom-conditional-access-policy"></a>Aangepast beleid voor voorwaardelijke toegang

Beheerders kunnen er ook voor kiezen om een aangepast beleid voor voorwaardelijke toegang te maken, inclusief aanmeldings risico als een toewijzings voorwaarde. Meer informatie over Risico's als een voor waarde in het beleid voor voorwaardelijke toegang vindt u in het artikel [voorwaardelijke toegang: voor waarden](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk)

![Beleid voor aangepaste aanmeldings Risico's voor voorwaardelijke toegang](./media/concept-identity-protection-policies/identity-protection-custom-sign-in-policy.png)

## <a name="user-risk-policy"></a>Beleid voor gebruikersrisico's

Identiteits beveiliging kan berekenen wat het beste is voor het gedrag van een gebruiker en hoe het kan worden gebruikt om beslissingen te baseren op hun risico. Gebruikers risico is een berekening van de waarschijnlijkheid dat er is geknoeid met een identiteit. Beheerders kunnen een beslissing nemen op basis van dit signaal van een risico Score om de vereisten van de organisatie af te dwingen. Beheerders kunnen ervoor kiezen om de toegang te blok keren, toegang toe te staan of toegang toe te staan, maar u moet een wacht woord wijzigen met behulp van de [selfservice voor wachtwoord herstel van Azure AD](../authentication/howto-sspr-deployment.md).

Als er een risico wordt gedetecteerd, kunnen gebruikers de selfservice voor wachtwoord herstel uitvoeren om zichzelf op te lossen en de gebruikers risico gebeurtenis te sluiten om onnodige ruis voor beheerders te voor komen.

> [!NOTE] 
> Gebruikers moeten eerder zijn geregistreerd voor selfservice voor wachtwoord herstel voordat ze het beleid voor gebruikers Risico's kunnen activeren.

## <a name="next-steps"></a>Volgende stappen

- [Selfservice voor wachtwoord herstel van Azure AD inschakelen](../authentication/howto-sspr-deployment.md)

- [Azure AD Multi-Factor Authentication inschakelen](../authentication/howto-mfa-getstarted.md)

- [Azure AD Multi-Factor Authentication-registratie beleid inschakelen](howto-identity-protection-configure-mfa-policy.md)

- [Aanmeldings-en gebruikers risico beleid inschakelen](howto-identity-protection-configure-risk-policies.md)
