---
title: 'Voorwaardelijke toegang: MFA vereisen voor beheerders-Azure Active Directory'
description: Een aangepast beleid voor voorwaardelijke toegang maken zodat beheerders multi-factor Authentication kunnen uitvoeren
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 03/04/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 35178ecc9bc736bbaca3adc932022b15cc2fc956
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102632081"
---
# <a name="conditional-access-require-mfa-for-administrators"></a>Voorwaardelijke toegang: MFA vereisen voor beheerders

Accounts waaraan beheerders rechten zijn toegewezen, zijn gericht op aanvallers. Het vereisen van multi-factor Authentication (MFA) voor deze accounts is een eenvoudige manier om het risico te verkleinen dat deze accounts worden aangetast.

Micro soft raadt u aan MFA verplicht te stellen voor de volgende rollen ten minste:

* Verificatiebeheerder
* Factureringsbeheerder
* Beheerder van voorwaardelijke toegang
* Exchange-beheerder
* Globale beheerder
* Helpdesk beheerder
* Wachtwoordbeheerder
* Beheerder voor bevoorrechte rollen
* Beveiligingsbeheerder
* SharePoint-beheerder
* Gebruikersbeheerder

Organisaties kunnen ervoor kiezen om rollen op te nemen of uit te sluiten zoals ze passen.

## <a name="user-exclusions"></a>Gebruikers uitsluitingen

Beleids regels voor voorwaardelijke toegang zijn krachtige hulp middelen. u wordt aangeraden de volgende accounts uit te sluiten van het beleid:

* **Nood toegang** of **afbreek glazen** om te voor komen dat accounts voor tenants worden vergrendeld. In het onwaarschijnlijke scenario zijn alle beheerders vergrendeld van uw Tenant. u kunt uw beheer account voor nood toegang gebruiken om u aan te melden bij de Tenant stappen nemen om de toegang te herstellen.
   * Meer informatie vindt u in het artikel [manage accounts voor nood toegang in azure AD](../roles/security-emergency-access.md).
* **Service accounts** en **service-principals**, zoals het Azure AD Connect Sync-account. Service accounts zijn niet-interactieve accounts die niet zijn gekoppeld aan een bepaalde gebruiker. Ze worden normaal gesp roken gebruikt door back-end-services die programmatische toegang tot toepassingen toestaan, maar worden ook gebruikt om u aan te melden bij systemen voor administratieve doel einden. Service accounts zoals deze moeten worden uitgesloten omdat MFA niet programmatisch kan worden voltooid. Aanroepen van service-principals worden niet geblokkeerd door voorwaardelijke toegang.
   * Als uw organisatie deze accounts in gebruik heeft in scripts of code, kunt u overwegen deze te vervangen door [beheerde identiteiten](../managed-identities-azure-resources/overview.md). Als tijdelijke oplossing kunt u deze specifieke accounts uitsluiten van het basislijn beleid.

## <a name="create-a-conditional-access-policy"></a>Beleid voor voorwaardelijke toegang maken

De volgende stappen helpen u bij het maken van een beleid voor voorwaardelijke toegang om te vereisen dat aan deze toegewezen beheerders rollen multi-factor Authentication wordt uitgevoerd.

1. Meld u aan bij de **Azure Portal** als globale beheerder, beveiligings beheerder of beheerder van de voorwaardelijke toegang.
1. Blader naar **Azure Active Directory**  >  **beveiligings**  >  **voorwaardelijke toegang**.
1. Selecteer **Nieuw beleid**.
1. Geef uw beleid een naam. Het is raadzaam dat organisaties een zinvolle norm maken voor de namen van hun beleid.
1. Onder **toewijzingen** selecteert u **gebruikers en groepen**
   1. Onder **insluiten** selecteert u **Directory rollen** en kiest u ingebouwde rollen als:
      * Verificatiebeheerder
      * Factureringsbeheerder
      * Beheerder van voorwaardelijke toegang
      * Exchange-beheerder
      * Globale beheerder
      * Helpdesk beheerder
      * Wachtwoordbeheerder
      * Beveiligingsbeheerder
      * SharePoint-beheerder
      * Gebruikersbeheerder
   
      > [!WARNING]
      > Beleids regels voor voorwaardelijke toegang ondersteunen ingebouwde rollen. Beleids regels voor voorwaardelijke toegang worden niet afgedwongen voor andere typen rollen, inclusief [administratieve eenheid-scoped](../roles/admin-units-assign-roles.md) of [aangepaste rollen](../roles/custom-create.md).

   1. Onder **uitsluiten** selecteert u **gebruikers en groepen** en kiest u de accounts voor nood toegang of het afbreek glas van uw organisatie. 
   1. Selecteer **Gereed**.
1. Onder **Cloud-apps of acties**  >  , selecteert u **alle Cloud-apps** en selecteert u **gereed**.
1. Onder **toegangs beheer**  >  **toekennen** selecteert u **toegang verlenen**, **multi-factor Authentication vereisen** en selecteert u **selecteren**.
1. Bevestig de instellingen en stel **beleid inschakelen** in **op aan**.
1. Selecteer **maken** om uw beleid in te stellen.

## <a name="next-steps"></a>Volgende stappen

[Algemeen beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)

[Effect bepalen met de modus alleen rapport-alleen voor voorwaardelijke toegang](howto-conditional-access-insights-reporting.md)

[Aanmeld gedrag simuleren met het What If hulp programma voor voorwaardelijke toegang](troubleshoot-conditional-access-what-if.md)
