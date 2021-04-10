---
title: Voorwaardelijke toegang-gecombineerde beveiligings informatie-Azure Active Directory
description: Een aangepast beleid voor voorwaardelijke toegang maken voor de registratie van beveiligings gegevens
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 03/24/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 711d4bdf2be2ad3158c12e4690a70fb83fe7a846
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105559499"
---
# <a name="conditional-access-securing-security-info-registration"></a>Voorwaardelijke toegang: Veilige registratie van beveiligingsinformatie

Beveiligen wanneer en hoe gebruikers zich registreren voor Azure AD Multi-Factor Authentication en self-service voor het opnieuw instellen van wacht woorden kunnen worden uitgevoerd met gebruikers acties in een beleid voor voorwaardelijke toegang. Deze functie is beschikbaar voor organisaties die de [gecombineerde registratie](../authentication/concept-registration-mfa-sspr-combined.md)hebben ingeschakeld. Met deze functionaliteit kunnen organisaties het registratie proces behandelen zoals elke toepassing in een beleid voor voorwaardelijke toegang en de volledige kracht van voorwaardelijke toegang gebruiken om de ervaring te beveiligen. 

Sommige organisaties in het verleden kunnen een vertrouwde netwerk locatie of apparaatcompatibiliteit gebruiken als middel om de registratie-ervaring te beveiligen. Door de [tijdelijke toegangs fase](../authentication/howto-authentication-temporary-access-pass.md) in azure AD toe te voegen, kunnen beheerders tijdgebonden referenties inrichten voor hun gebruikers, zodat ze zich kunnen registreren vanaf elk apparaat of elke locatie. Referenties voor het door geven van de tijdelijke toegang voldoen aan de vereisten voor voorwaardelijke toegang voor multi-factor Authentication.

## <a name="create-a-policy-to-secure-registration"></a>Een beleid maken om de registratie te beveiligen

Het volgende beleid is van toepassing op de geselecteerde gebruikers die zich willen registreren met de gecombineerde registratie-ervaring. Voor het beleid moeten gebruikers multi-factor Authentication uitvoeren of de tijdelijke referenties voor het door geven van toegang gebruiken.

1. Blader in het **Azure Portal** naar **Azure Active Directory**  >    >  **voorwaardelijke toegang** voor beveiliging.
1. Selecteer **Nieuw beleid**.
1. Voer bij naam een naam in voor dit beleid. Bijvoorbeeld **registratie van gecombineerde beveiligings gegevens met tikken**.
1. Onder **toewijzingen** selecteert u **gebruikers en groepen** en selecteert u de gebruikers en groepen waarop u dit beleid wilt Toep assen.
   1. Onder **insluiten** selecteert u **alle gebruikers**.

      > [!WARNING]
      > Gebruikers moeten zijn ingeschakeld voor de [gecombineerde registratie](../authentication/howto-registration-mfa-sspr-combined.md).

   1. Onder **uitsluiten**.
      1. Selecteer **alle gast-en externe gebruikers**.
      
         > [!NOTE]
         > De tijdelijke toegangs fase werkt niet voor gast gebruikers.

      1. Selecteer **gebruikers en groepen** en kies de accounts voor nood toegang of het afbreek glas van uw organisatie. 
1. Onder **Cloud-apps of-acties** selecteert u **gebruikers acties**, check **Security Information registreren**.
1. Onder **toegangs beheer**  >  **verlenen**.
   1. Selecteer **Toegang verlenen**.
   1. Selecteer **Meervoudige verificatie vereisen**.
   1. Klik op **Selecteren**.
1. Stel **Beleid inschakelen** in op **Aan**.
1. Selecteer vervolgens **Maken**.

Beheerders moeten nu referenties voor het door geven van de tijdelijke toegang verlenen aan nieuwe gebruikers, zodat ze kunnen voldoen aan de vereisten voor multi-factor Authentication om ze te registreren. De stappen voor het uitvoeren van deze taak vindt u in de sectie [tijdelijke toegang maken in de Azure AD-Portal](../authentication/howto-authentication-temporary-access-pass.md#create-a-temporary-access-pass-in-the-azure-ad-portal).

Organisaties kunnen ervoor kiezen om naast of in plaats van **multi-factor Authentication** bij stap 6b andere toekennings controles te vereisen. Wanneer u meerdere besturings elementen selecteert, selecteert u het juiste keuze rondje om **alle** of **een** van de geselecteerde besturings elementen in te stellen wanneer u deze wijziging aanbrengt.

### <a name="guest-user-registration"></a>Gebruikers registratie gast

Voor [gast gebruikers](../external-identities/what-is-b2b.md) die zich voor multi-factor Authentication in uw Directory moeten registreren, kunt u de registratie van buiten de [vertrouwde netwerk locaties](concept-conditional-access-conditions.md#locations) blok keren met behulp van de volgende hand leiding.

1. Blader in het **Azure Portal** naar **Azure Active Directory**  >    >  **voorwaardelijke toegang** voor beveiliging.
1. Selecteer **Nieuw beleid**.
1. Voer bij naam een naam in voor dit beleid. Bijvoorbeeld **registratie van gegevens over gecombineerde beveiliging op vertrouwde netwerken**.
1. Onder **toewijzingen** selecteert u **gebruikers en groepen** en selecteert u de gebruikers en groepen waarop u dit beleid wilt Toep assen.
   1. Onder **insluiten** selecteert u **alle gast en externe gebruikers**.
1. Onder **Cloud-apps of-acties** selecteert u **gebruikers acties**, check **Security Information registreren**.
1. Onder **voor waarden**  >  **locaties**.
   1. Configureer **Ja**.
   1. **Een wille keurige locatie** bevatten.
   1. **Alle vertrouwde locaties** uitsluiten.
   1. Selecteer **gereed** op de Blade locaties.
   1. Selecteer **gereed** op de Blade voor waarden.
1. Onder **toegangs beheer**  >  **verlenen**.
   1. Selecteer **toegang blok keren**.
   1. Klik vervolgens op **Selecteren**.
1. Stel **Beleid inschakelen** in op **Aan**.
1. Selecteer vervolgens **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

[Algemeen beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)

[Effect bepalen met de modus alleen rapport-alleen voor voorwaardelijke toegang](howto-conditional-access-insights-reporting.md)

[Aanmeld gedrag simuleren met het What If hulp programma voor voorwaardelijke toegang](troubleshoot-conditional-access-what-if.md)
