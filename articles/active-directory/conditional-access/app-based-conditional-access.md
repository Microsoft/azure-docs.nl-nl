---
title: Goedgekeurde client-apps met voorwaardelijke toegang-Azure Active Directory
description: Meer informatie over het vereisen van goedgekeurde client-apps voor toegang tot Cloud app met voorwaardelijke toegang in Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 03/04/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol, rosssmi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 122cc6a2be17cb35e77b638a60fc5fa4f035c0d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91266136"
---
# <a name="how-to-require-approved-client-apps-for-cloud-app-access-with-conditional-access"></a>Procedure: goedgekeurde client-apps vereisen voor toegang tot Cloud app met voorwaardelijke toegang

Gebruikers gebruiken regel matig hun mobiele apparaten voor zowel privé-als werk taken. Het is ook belang rijk om te voor komen dat uw mede werkers productief kunnen zijn, maar dat er geen gegevens verloren gaan. Met voorwaardelijke toegang kunnen organisaties de toegang beperken tot goedgekeurde (moderne authenticatie mogelijkheden) client-apps.

Dit artikel bevat twee scenario's voor het configureren van beleid voor voorwaardelijke toegang voor resources zoals Microsoft 365, Exchange Online en share point online.

- [Scenario 1: voor Microsoft 365-apps is een goedgekeurde client-app vereist](#scenario-1-microsoft-365-apps-require-an-approved-client-app)
- [Scenario 2: voor Exchange Online en share point online is een goedgekeurde client-app vereist](#scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app)

In voorwaardelijke toegang is deze functionaliteit bekend als het vereisen van een goedgekeurde client-app. Zie voor een lijst met goedgekeurde client-apps [goedgekeurde client-app-vereiste](concept-conditional-access-grant.md#require-approved-client-app).

> [!NOTE]
> Als u goedgekeurde client-Apps wilt vereisen voor iOS-en Android-apparaten, moeten deze apparaten eerst worden geregistreerd in azure AD.

## <a name="scenario-1-microsoft-365-apps-require-an-approved-client-app"></a>Scenario 1: voor Microsoft 365-apps is een goedgekeurde client-app vereist

In dit scenario heeft Contoso besloten dat gebruikers die mobiele apparaten gebruiken, toegang hebben tot alle Microsoft 365 Services zolang ze goedgekeurde client-apps gebruiken, zoals Outlook Mobile, OneDrive en micro soft teams. Al hun gebruikers aanmelden met Azure AD-referenties en hebben licenties toegewezen die Azure AD Premium P1 of P2 en Microsoft Intune bevatten.

Organisaties moeten de volgende drie stappen uitvoeren om het gebruik van een goedgekeurde client-app op mobiele apparaten te vereisen.

**Stap 1: beleid voor Android-en iOS-gebaseerde moderne authenticatie clients waarvoor het gebruik van een goedgekeurde client toepassing is vereist bij de toegang tot Exchange Online.**

1. Meld u aan bij de **Azure Portal** als globale beheerder, beveiligings beheerder of beheerder van de voorwaardelijke toegang.
1. Blader naar **Azure Active Directory**  >  **beveiligings**  >  **voorwaardelijke toegang**.
1. Selecteer **Nieuw beleid**.
1. Geef uw beleid een naam. Het is raadzaam dat organisaties een zinvolle norm maken voor de namen van hun beleid.
1. Onder **toewijzingen** selecteert u **gebruikers en groepen**
   1. Onder **opnemen** selecteert u **alle gebruikers** of de specifieke **gebruikers en groepen** waarop u dit beleid wilt Toep assen. 
   1. Selecteer **Gereed**.
1. Onder **Cloud-apps of acties**  >  , selecteert u **Office 365**.
1. Onder **voor waarden** selecteert u **apparaat platforms**.
   1. Stel **configureren** in op **Ja**.
   1. Voeg **Android** en **IOS** toe.
1. Onder **voor waarden** selecteert u **client-apps (preview-versie)**.
   1. Stel **configureren** in op **Ja**.
   1. Selecteer **Mobiele apps en bureaubladclients** en **Clients met moderne verificatie**.
1. Onder **toegangs beheer**  >  **toekennen** selecteert u **toegang verlenen**, **goedgekeurde client-app vereisen** en selecteert u **selecteren**.
1. Bevestig de instellingen en stel **beleid inschakelen** in **op aan**.
1. Selecteer **maken** om uw beleid te maken en in te scha kelen.

**Stap 2: een beleid voor voorwaardelijke toegang voor Azure AD configureren voor Exchange Online met ActiveSync (EAS)**

1. Blader naar **Azure Active Directory**  >  **beveiligings**  >  **voorwaardelijke toegang**.
1. Selecteer **Nieuw beleid**.
1. Geef uw beleid een naam. Het is raadzaam dat organisaties een zinvolle norm maken voor de namen van hun beleid.
1. Onder **toewijzingen** selecteert u **gebruikers en groepen**
   1. Onder **opnemen** selecteert u **alle gebruikers** of de specifieke **gebruikers en groepen** waarop u dit beleid wilt Toep assen. 
   1. Selecteer **Gereed**.
1.   >  Selecteer **Office 365 Exchange Online** onder Cloud-apps of-acties.
1. Onder **voor waarden**:
   1. **Client-apps (preview-versie)**:
      1. Stel **configureren** in op **Ja**.
      1. Selecteer **mobiele apps en desktop-clients** en **Exchange ActiveSync-clients**.
1. Onder **toegangs beheer**  >  **toekennen** selecteert u **toegang verlenen**, **goedgekeurde client-app vereisen** en selecteert u **selecteren**.
1. Bevestig de instellingen en stel **beleid inschakelen** in **op aan**.
1. Selecteer **maken** om uw beleid te maken en in te scha kelen.

**Stap 3: Configureer het beveiligings beleid van de intune-app voor iOS-en Android-client toepassingen.**

Raadpleeg het artikel het [maken en toewijzen van app-beveiligings beleid](/intune/apps/app-protection-policies)voor stappen voor het maken van app-beveiligings beleid voor Android en IOS. 

## <a name="scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app"></a>Scenario 2: voor Exchange Online en share point online is een goedgekeurde client-app vereist

In dit scenario heeft Contoso besloten dat gebruikers alleen toegang hebben tot e-mail en share point-gegevens op mobiele apparaten zolang ze een goedgekeurde client-app gebruiken, zoals Outlook Mobile. Al hun gebruikers aanmelden met Azure AD-referenties en hebben licenties toegewezen die Azure AD Premium P1 of P2 en Microsoft Intune bevatten.

Organisaties moeten de volgende drie stappen uitvoeren om het gebruik van een goedgekeurde client-app op mobiele apparaten en Exchange ActiveSync-clients te vereisen.

**Stap 1: beleid voor Android-en iOS-gebaseerde moderne authenticatie clients waarvoor het gebruik van een goedgekeurde client toepassing is vereist bij de toegang tot Exchange Online en share point online.**

1. Meld u aan bij de **Azure Portal** als globale beheerder, beveiligings beheerder of beheerder van de voorwaardelijke toegang.
1. Blader naar **Azure Active Directory**  >  **beveiligings**  >  **voorwaardelijke toegang**.
1. Selecteer **Nieuw beleid**.
1. Geef uw beleid een naam. Het is raadzaam dat organisaties een zinvolle norm maken voor de namen van hun beleid.
1. Onder **toewijzingen** selecteert u **gebruikers en groepen**
   1. Onder **opnemen** selecteert u **alle gebruikers** of de specifieke **gebruikers en groepen** waarop u dit beleid wilt Toep assen. 
   1. Selecteer **Gereed**.
1. Onder **Cloud-apps of-acties**  >  **gaat** u naar **Office 365 Exchange Online** en **Office 365 share point online**.
1. Onder **voor waarden** selecteert u **apparaat platforms**.
   1. Stel **configureren** in op **Ja**.
   1. Voeg **Android** en **IOS** toe.
1. Onder **voor waarden** selecteert u **client-apps (preview-versie)**.
   1. Stel **configureren** in op **Ja**.
   1. Selecteer **Mobiele apps en bureaubladclients** en **Clients met moderne verificatie**.
1. Onder **toegangs beheer**  >  **toekennen** selecteert u **toegang verlenen**, **goedgekeurde client-app vereisen** en selecteert u **selecteren**.
1. Bevestig de instellingen en stel **beleid inschakelen** in **op aan**.
1. Selecteer **maken** om uw beleid te maken en in te scha kelen.

**Stap 2: beleid voor Exchange ActiveSync-clients waarvoor het gebruik van een goedgekeurde client-app is vereist.**

1. Blader naar **Azure Active Directory**  >  **beveiligings**  >  **voorwaardelijke toegang**.
1. Selecteer **Nieuw beleid**.
1. Geef uw beleid een naam. Het is raadzaam dat organisaties een zinvolle norm maken voor de namen van hun beleid.
1. Onder **toewijzingen** selecteert u **gebruikers en groepen**
   1. Onder **opnemen** selecteert u **alle gebruikers** of de specifieke **gebruikers en groepen** waarop u dit beleid wilt Toep assen. 
   1. Selecteer **Gereed**.
1.   >  Selecteer **Office 365 Exchange Online** onder Cloud-apps of-acties.
1. Onder **voor waarden**:
   1. **Client-apps (preview-versie)**:
      1. Stel **configureren** in op **Ja**.
      1. Selecteer **mobiele apps en desktop-clients** en **Exchange ActiveSync-clients**.
1. Onder **toegangs beheer**  >  **toekennen** selecteert u **toegang verlenen**, **goedgekeurde client-app vereisen** en selecteert u **selecteren**.
1. Bevestig de instellingen en stel **beleid inschakelen** in **op aan**.
1. Selecteer **maken** om uw beleid te maken en in te scha kelen.

**Stap 3: Configureer het beveiligings beleid van de intune-app voor iOS-en Android-client toepassingen.**

Raadpleeg het artikel het [maken en toewijzen van app-beveiligings beleid](/intune/apps/app-protection-policies)voor stappen voor het maken van app-beveiligings beleid voor Android en IOS. 

## <a name="next-steps"></a>Volgende stappen

[Wat is voorwaardelijke toegang?](overview.md)

[Onderdelen voor voorwaardelijke toegang](concept-conditional-access-policies.md)

[Algemeen beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)
