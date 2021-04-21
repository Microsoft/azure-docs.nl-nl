---
title: Voorwaardelijke toegang - Gecombineerde beveiligingsgegevens - Azure Active Directory
description: Een aangepast beleid voor voorwaardelijke toegang maken voor registratie van beveiligingsgegevens
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 04/20/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 05aca853e2eba98d224131c98751b4e2f4200024
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765643"
---
# <a name="conditional-access-securing-security-info-registration"></a>Voorwaardelijke toegang: Veilige registratie van beveiligingsinformatie

Het beveiligen wanneer en hoe gebruikers zich registreren voor Azure AD Multi-Factor Authentication en selfservice voor wachtwoord opnieuw instellen is mogelijk met gebruikersacties in een beleid voor voorwaardelijke toegang. Deze functie is beschikbaar voor organisaties die de gecombineerde registratie [hebben ingeschakeld.](../authentication/concept-registration-mfa-sspr-combined.md) Met deze functionaliteit kunnen organisaties het registratieproces behandelen zoals elke toepassing in een beleid voor voorwaardelijke toegang en gebruikmaken van de volledige kracht van voorwaardelijke toegang om de ervaring te beveiligen. 

Sommige organisaties in het verleden hebben mogelijk vertrouwde netwerklocatie of apparaat naleving gebruikt om de registratie-ervaring te beveiligen. Met de toevoeging [van Tijdelijke toegangspas](../authentication/howto-authentication-temporary-access-pass.md) in Azure AD kunnen beheerders tijds beperkte referenties inrichten voor hun gebruikers, zodat ze zich vanaf elk apparaat of elke locatie kunnen registreren. Tijdelijke toegangspas referenties voldoen aan de vereisten voor voorwaardelijke toegang voor meervoudige verificatie.

## <a name="create-a-policy-to-secure-registration"></a>Een beleid maken om registratie te beveiligen

Het volgende beleid is van toepassing op de geselecteerde gebruikers die proberen te registreren met behulp van de gecombineerde registratie-ervaring. Het beleid vereist dat gebruikers meervoudige verificatie uitvoeren of Tijdelijke toegangspas gebruiken.

1. Blader in **Azure Portal** naar **Azure Active Directory**  >  **Security**  >  **Conditional Access**.
1. Selecteer **Nieuw beleid.**
1. Voer bij Naam een naam in voor dit beleid. Bijvoorbeeld Gecombineerde **registratie van beveiligingsgegevens met TAP**.
1. Selecteer **onder Toewijzingen** de **optie Gebruikers en groepen** en selecteer de gebruikers en groepen op wie u dit beleid wilt toepassen.
   1. Selecteer **onder Opnemen** de optie Alle **gebruikers.**

      > [!WARNING]
      > Gebruikers moeten zijn ingeschakeld voor de [gecombineerde registratie](../authentication/howto-registration-mfa-sspr-combined.md).

   1. Onder **Uitsluiten.**
      1. Selecteer **Alle gast- en externe gebruikers.**
      
         > [!NOTE]
         > Tijdelijke toegangspas werkt niet voor gastgebruikers.

      1. Selecteer **Gebruikers en groepen** en kies de accounts voor noodtoegang of break-glass van uw organisatie. 
1. Selecteer **onder Cloud-apps of -acties** **de optie Gebruikersacties** en **schakel Beveiligingsgegevens registreren in.**
1. Onder **Besturingselementen voor toegang**  >  **verleent u**.
   1. Selecteer **Toegang verlenen**.
   1. Selecteer **Meervoudige verificatie vereisen**.
   1. Klik op **Selecteren**.
1. Stel **Beleid inschakelen** in op **Aan**.
1. Selecteer vervolgens **Maken**.

Beheerders moeten nu referenties Tijdelijke toegangspas aan nieuwe gebruikers, zodat ze kunnen voldoen aan de vereisten voor meervoudige verificatie om zich te registreren. Stappen voor het uitvoeren van deze taak vindt u in de sectie [Een Tijdelijke toegangspas maken in de Azure AD-portal.](../authentication/howto-authentication-temporary-access-pass.md#create-a-temporary-access-pass)

Organisaties kunnen ervoor kiezen om andere toekenningsbesturingselementen te vereisen naast of in plaats van **Meervoudige verificatie vereisen** in stap 6b. Wanneer u meerdere besturingselementen selecteert, moet  u het juiste keuzerondje selecteren om alle **of** een van de geselecteerde besturingselementen te vereisen bij het maken van deze wijziging.

### <a name="guest-user-registration"></a>Registratie van gastgebruikers

Voor [gastgebruikers die](../external-identities/what-is-b2b.md) zich moeten registreren voor meervoudige verificatie in uw [](concept-conditional-access-conditions.md#locations) directory, kunt u de registratie van buiten vertrouwde netwerklocaties blokkeren met behulp van de volgende handleiding.

1. Blader in **Azure Portal** naar **Azure Active Directory**  >  **Security**  >  **Conditional Access**.
1. Selecteer **Nieuw beleid.**
1. Voer bij Naam een naam in voor dit beleid. Bijvoorbeeld gecombineerde **registratie van beveiligingsgegevens in vertrouwde netwerken.**
1. Selecteer **onder Toewijzingen** **de optie Gebruikers en groepen** en selecteer de gebruikers en groepen op wie u dit beleid wilt toepassen.
   1. Selecteer **onder Opnemen** de optie Alle **gastgebruikers en externe gebruikers.**
1. Selecteer **onder Cloud-apps of -acties** **de optie Gebruikersacties** en **schakel Beveiligingsgegevens registreren in.**
1. Onder **Voorwaarden**  >  **locaties**.
   1. Configureer **Ja.**
   1. Neem **elke locatie op.**
   1. Sluit **Alle vertrouwde locaties uit.**
   1. Selecteer **Done** op de blade Locaties.
   1. Selecteer **Done** op de blade Voorwaarden.
1. Onder **Besturingselementen voor toegang**  >  **verleent u**.
   1. Selecteer **Toegang blokkeren.**
   1. Klik vervolgens op **Selecteren**.
1. Stel **Beleid inschakelen** in op **Aan**.
1. Selecteer vervolgens **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

[Algemeen beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)

[Invloed bepalen met de rapportmodus Voorwaardelijke toegang](howto-conditional-access-insights-reporting.md)

[Aanmeldingsgedrag simuleren met behulp van het hulpprogramma What If voorwaardelijke toegang](troubleshoot-conditional-access-what-if.md)

[Vereisen dat gebruikers verificatiegegevens opnieuw bevestigen](../authentication/concept-sspr-howitworks.md#reconfirm-authentication-information)
