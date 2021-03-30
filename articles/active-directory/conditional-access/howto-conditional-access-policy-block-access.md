---
title: Voorwaardelijke toegang-blok toegang-Azure Active Directory
description: Een aangepast beleid voor voorwaardelijke toegang maken naar
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb,
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84e0801daa5bf83889be87987d440e377287b5ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92366187"
---
# <a name="conditional-access-block-access"></a>Voorwaardelijke toegang: toegang blok keren

Voor organisaties met een conservatieve Cloud migratie methode is het beleid voor het blok keren van gebruik een optie die kan worden gebruikt. 

> [!CAUTION]
> Een onjuiste configuratie van een blok beleid kan ertoe leiden dat organisaties worden uitgesloten van de Azure Portal.

Beleids regels zoals deze kunnen onbedoelde neven effecten hebben. Goed te testen en valideren zijn essentieel voordat ze worden ingeschakeld. Beheerders moeten hulpprogram ma's zoals de [modus alleen rapport-alleen voorwaardelijke toegang](concept-conditional-access-report-only.md) en [het What if-hulp programma in voorwaardelijke toegang](what-if-tool.md) gebruiken bij het aanbrengen van wijzigingen.

## <a name="user-exclusions"></a>Gebruikers uitsluitingen

Beleids regels voor voorwaardelijke toegang zijn krachtige hulp middelen. u wordt aangeraden de volgende accounts uit te sluiten van het beleid:

* **Nood toegang** of **afbreek glazen** om te voor komen dat accounts voor tenants worden vergrendeld. In het onwaarschijnlijke scenario zijn alle beheerders vergrendeld van uw Tenant. u kunt uw beheer account voor nood toegang gebruiken om u aan te melden bij de Tenant stappen nemen om de toegang te herstellen.
   * Meer informatie vindt u in het artikel [manage accounts voor nood toegang in azure AD](../roles/security-emergency-access.md).
* **Service accounts** en **service-principals**, zoals het Azure AD Connect Sync-account. Service accounts zijn niet-interactieve accounts die niet zijn gekoppeld aan een bepaalde gebruiker. Ze worden normaal gesp roken gebruikt door back-end-services die programmatische toegang tot toepassingen toestaan, maar worden ook gebruikt om u aan te melden bij systemen voor administratieve doel einden. Service accounts zoals deze moeten worden uitgesloten omdat MFA niet programmatisch kan worden voltooid. Aanroepen van service-principals worden niet geblokkeerd door voorwaardelijke toegang.
   * Als uw organisatie deze accounts in gebruik heeft in scripts of code, kunt u overwegen deze te vervangen door [beheerde identiteiten](../managed-identities-azure-resources/overview.md). Als tijdelijke oplossing kunt u deze specifieke accounts uitsluiten van het basislijn beleid.

## <a name="create-a-conditional-access-policy"></a>Beleid voor voorwaardelijke toegang maken

De volgende stappen helpen u bij het maken van beleid voor voorwaardelijke toegang om de toegang tot alle apps, met uitzonde ring van [Office 365](concept-conditional-access-cloud-apps.md#office-365) , te blok keren als gebruikers zich niet in een vertrouwd netwerk bevinden. Deze beleids regels worden in de [modus alleen rapport](howto-conditional-access-insights-reporting.md) gezet om te beginnen, zodat beheerders kunnen bepalen welke invloed ze hebben op bestaande gebruikers. Wanneer beheerders weten dat de beleids regels **van** toepassing zijn, kunnen ze overschakelen naar.

Het eerste beleid blokkeert de toegang tot alle apps, met uitzonde ring van Microsoft 365-toepassingen als dat niet op een vertrouwde locatie is.

1. Meld u aan bij de **Azure Portal** als globale beheerder, beveiligings beheerder of beheerder van de voorwaardelijke toegang.
1. Blader naar **Azure Active Directory**  >  **beveiligings**  >  **voorwaardelijke toegang**.
1. Selecteer **Nieuw beleid**.
1. Geef uw beleid een naam. Het is raadzaam dat organisaties een zinvolle norm maken voor de namen van hun beleid.
1. Onder **Toewijzingen** selecteert u **Gebruikers en groepen**.
   1. Onder **insluiten** selecteert u **alle gebruikers**.
   1. Onder **uitsluiten** selecteert u **gebruikers en groepen** en kiest u de accounts voor nood toegang of het afbreek glas van uw organisatie. 
   1. Selecteer **Gereed**.
1. Selecteer onder **Cloud-apps of acties** de volgende opties:
   1. Onder **insluiten** selecteert u **alle Cloud-apps**.
   1. Onder **uitsluiten** selecteert u **Office 365**, selecteert **u selecteren** en selecteert u **gereed**.
1. Onder **voor waarden**:
   1. Onder **voor waarden**  >  **locatie**.
      1. Stel **configureren** op **Ja** in
      1. Onder **insluiten** selecteert u **een wille keurige locatie**.
      1. Selecteer **alle vertrouwde locaties** onder **uitsluiten**.
      1. Selecteer **Gereed**.
   1. Stel **onder client-apps (preview)** **configureren** in op **Ja** en selecteer **gereed** en vervolgens **gereed**.
1. Onder **toegangs beheer**  >  **toekennen** selecteert u **toegang blok keren** en selecteert u **selecteren**.
1. Bevestig de instellingen en stel **beleid inschakelen** in op **alleen rapport**.
1. Selecteer **maken** om uw beleid in te stellen.

Hieronder wordt een tweede beleid gemaakt om multi-factor Authentication of een compatibel apparaat te vereisen voor gebruikers van Microsoft 365.

1. Selecteer **Nieuw beleid**.
1. Geef uw beleid een naam. Het is raadzaam dat organisaties een zinvolle norm maken voor de namen van hun beleid.
1. Onder **Toewijzingen** selecteert u **Gebruikers en groepen**.
   1. Onder **insluiten** selecteert u **alle gebruikers**.
   1. Onder **uitsluiten** selecteert u **gebruikers en groepen** en kiest u de accounts voor nood toegang of het afbreek glas van uw organisatie. 
   1. Selecteer **Gereed**.
1. Onder **Cloud-apps of acties**  >  , selecteert **u apps selecteren**, kiest u **Office 365** en selecteert u **selecteren** en **gereed**.
1. Onder **toegangs beheer**  >  **toekennen** selecteert u **toegang verlenen**.
   1. Selecteer **multi-factor Authentication vereisen** en **vereisen dat het apparaat moet worden gemarkeerd als compatibel** selectie **vakje** selecteren.
   1. Zorg ervoor dat **alle geselecteerde besturings elementen** zijn geselecteerd.
   1. Kies **Selecteren**.
1. Bevestig de instellingen en stel **beleid inschakelen** in op **alleen rapport**.
1. Selecteer **maken** om uw beleid in te stellen.

## <a name="next-steps"></a>Volgende stappen

[Algemeen beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)

[Effect bepalen met de modus alleen rapport-alleen voor voorwaardelijke toegang](howto-conditional-access-insights-reporting.md)

[Aanmeld gedrag simuleren met het What If hulp programma voor voorwaardelijke toegang](troubleshoot-conditional-access-what-if.md)
