---
title: Registratie van gecombineerde beveiligings gegevens inschakelen-Azure Active Directory
description: Meer informatie over hoe u de eindgebruikers ervaring kunt vereenvoudigen met gecombineerde Azure AD-Multi-Factor Authentication en de registratie van self-service voor het opnieuw instellen van wacht woorden.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 12/04/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29a577b50a561cb5b829e453c523e0bd18a70e1a
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96741674"
---
# <a name="enable-combined-security-information-registration-in-azure-active-directory"></a>Registratie van gecombineerde beveiligings gegevens inschakelen in Azure Active Directory

Vóór de gecombineerde registratie hebben gebruikers de verificatie methoden voor Azure AD Multi-Factor Authentication en self-service voor wachtwoord herstel (SSPR) afzonderlijk geregistreerd. Het is bekend dat er soort gelijke methoden werden gebruikt voor Azure AD Multi-Factor Authentication en SSPR, maar dat ze voor beide functies moesten registreren. Met gecombineerde registratie kunnen gebruikers zich eenmaal registreren en profiteren van de voor delen van Azure AD-Multi-Factor Authentication en SSPR.

> [!NOTE]
> Vanaf 15 augustus 2020 worden alle nieuwe Azure AD-tenants automatisch ingeschakeld voor gecombineerde registratie. Vanaf 14 december 2020 worden alle Azure AD-tenants met één gebruiker automatisch ingeschakeld voor gecombineerde registratie.

Raadpleeg de [informatie over het registreren van gecombineerde beveiligings gegevens](concept-registration-mfa-sspr-combined.md)om ervoor te zorgen dat u de functionaliteit en effecten begrijpt voordat u de nieuwe ervaring inschakelt.

![Verbeterde ervaring bij het registreren van gecombineerde beveiligings gegevens](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

## <a name="enable-combined-registration"></a>Gecombineerde registratie inschakelen

Voer de volgende stappen uit om gecombineerde registratie in te scha kelen:

1. Meld u aan bij de Azure Portal als gebruikers beheerder of globale beheerder.
2. Ga naar **Azure Active Directory**  >  **gebruikers instellingen**  >  **instellingen voor de preview-versie van gebruikers onderdelen beheren**.
3. Onder **gebruikers kunnen de functie voor registratie van gecombineerde beveiligings gegevens worden gebruikt**. Kies deze optie om in te scha kelen voor een **geselecteerde** groep gebruikers of voor **alle** gebruikers.

   ![De gecombineerde beveiligings informatie-ervaring voor gebruikers inschakelen](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info.png)

> [!NOTE]
> Nadat u gecombineerde registratie hebt ingeschakeld, kunnen gebruikers die hun telefoon nummer of mobiele app via de nieuwe ervaring registreren of bevestigen, ze gebruiken voor Azure AD Multi-Factor Authentication en SSPR als deze methoden zijn ingeschakeld in de Azure AD-Multi-Factor Authentication en SSPR-beleid.
>
> Als u deze ervaring vervolgens uitschakelt, zijn gebruikers die naar de vorige registratie pagina van SSPR op `https://aka.ms/ssprsetup` zijn verplicht om multi-factor Authentication uit te voeren voordat ze toegang krijgen tot de pagina.

Als u de *lijst site toewijzing met zone* hebt geconfigureerd in Internet Explorer, moeten de volgende sites zich in dezelfde zone bekomen:

* *[https://login.microsoftonline.com](https://login.microsoftonline.com)*
* *[https://mysignins.microsoft.com](https://mysignins.microsoft.com)*
* *[https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)*

## <a name="conditional-access-policies-for-combined-registration"></a>Beleid voor voorwaardelijke toegang voor gecombineerde registratie

Als u wilt beveiligen wanneer en hoe gebruikers zich registreren voor Azure AD Multi-Factor Authentication en self-service voor wachtwoord herstel, kunt u gebruikers acties gebruiken in het beleid voor voorwaardelijke toegang. Deze functionaliteit kan worden ingeschakeld in organisaties die gebruikers willen registreren voor Azure AD Multi-Factor Authentication en SSPR vanaf een centrale locatie, zoals een vertrouwde netwerk locatie tijdens HR-onboarding.

> [!NOTE]
> Dit beleid is alleen van toepassing wanneer een gebruiker toegang heeft tot een gecombineerde registratie pagina. Dit beleid dwingt geen MFA-inschrijving af wanneer een gebruiker toegang heeft tot andere toepassingen.
>
> U kunt een MFA-registratie beleid maken met behulp van [Azure Identity Protection-MFA-beleid configureren](../identity-protection/howto-identity-protection-configure-mfa-policy.md).

Zie [Wat is de locatie voorwaarde in azure Active Directory voorwaardelijke toegang?](../conditional-access/location-condition.md#named-locations) voor meer informatie over het maken van vertrouwde locaties in voorwaardelijke toegang.

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Een beleid maken om registratie van een vertrouwde locatie te vereisen

Voer de volgende stappen uit om een beleid te maken dat van toepassing is op alle geselecteerde gebruikers die zich willen registreren met behulp van de gecombineerde registratie-ervaring, en blokkeert de toegang tenzij ze verbinding maken vanaf een locatie die is gemarkeerd als vertrouwd netwerk:

1. Blader in het **Azure Portal** naar **Azure Active Directory**  >  **Security**  >  **voorwaardelijke toegang** voor beveiliging.
1. Selecteer **+ Nieuw beleid**.
1. Voer een naam in voor dit beleid, zoals de *registratie van gecombineerde beveiligings gegevens op vertrouwde netwerken*.
1. Onder **Toewijzingen** selecteert u **Gebruikers en groepen**. Kies de gebruikers en groepen waarop u dit beleid wilt Toep assen en selecteer vervolgens **gereed**.

   > [!WARNING]
   > Gebruikers moeten zijn ingeschakeld voor gecombineerde registratie.

1. Selecteer onder **Cloud-apps of acties** de optie **gebruikers acties**. Controleer **beveiligings gegevens registreren** en selecteer vervolgens **gereed**.

    ![Een beleid voor voorwaardelijke toegang maken om de registratie van beveiligings gegevens te beheren](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. Configureer onder **voor waarden**  >  **locaties** de volgende opties:
   1. Configureer **Ja**.
   1. **Een wille keurige locatie** bevatten.
   1. **Alle vertrouwde locaties** uitsluiten.
1. Selecteer **gereed** in het venster *locaties* en selecteer vervolgens **gereed** in het venster *voor waarden* .
1. Onder **toegangs beheer**  >  **toekennen** kiest u **toegang blok keren** en **selecteert** u vervolgens.
1. Stel **Beleid inschakelen** in op **Aan**.
1. Selecteer **maken** om het beleid te volt ooien.

## <a name="next-steps"></a>Volgende stappen

Als u hulp nodig hebt, raadpleegt u [problemen met het registreren van gecombineerde beveiligings gegevens oplossen](howto-registration-mfa-sspr-combined-troubleshoot.md) of meer informatie over [de locatie voorwaarde in azure AD voorwaardelijke toegang?](../conditional-access/location-condition.md)

Zodra gebruikers zijn ingeschakeld voor gecombineerde registratie, kunt u de [selfservice voor wachtwoord herstel inschakelen](tutorial-enable-sspr.md) en [Azure AD-multi-factor Authentication inschakelen](tutorial-enable-azure-mfa.md).

Als dat nodig is, leert u hoe u [gebruikers dwingt om verificatie methoden opnieuw te registreren](howto-mfa-userdevicesettings.md#manage-user-authentication-options).
