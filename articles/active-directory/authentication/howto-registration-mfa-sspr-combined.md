---
title: Registratie van gecombineerde beveiligingsgegevens inschakelen - Azure Active Directory
description: Meer informatie over het vereenvoudigen van de eindgebruikerservaring met gecombineerde Azure AD Multi-Factor Authentication en registratie van selfservice voor wachtwoord opnieuw instellen.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 01/27/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6eba5ac4ed61847596e12f56544e6d07dca8075
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829571"
---
# <a name="enable-combined-security-information-registration-in-azure-active-directory"></a>Registratie van gecombineerde beveiligingsgegevens inschakelen in Azure Active Directory

Voordat de registratie werd gecombineerd, registreerden gebruikers afzonderlijke verificatiemethoden voor Azure AD Multi-Factor Authentication en selfservice voor wachtwoord opnieuw instellen (SSPR). Mensen waren in de war dat vergelijkbare methoden werden gebruikt voor Azure AD Multi-Factor Authentication en SSPR, maar dat ze zich moesten registreren voor beide functies. Met gecombineerde registratie kunnen gebruikers zich nu eenmaal registreren en profiteren van de voordelen van zowel Azure AD Multi-Factor Authentication als SSPR.

> [!NOTE]
> Vanaf 15 augustus 2020 worden alle nieuwe Azure AD-tenants automatisch ingeschakeld voor gecombineerde registratie. Tenants die na deze datum zijn gemaakt, kunnen de verouderde registratiewerkstromen niet meer gebruiken.

Zie Gecombineerde concepten voor registratie van beveiligingsgegevens om ervoor te zorgen dat u de functionaliteit en effecten begrijpt voordat u de nieuwe ervaring [inschakelen.](concept-registration-mfa-sspr-combined.md)

![Verbeterde ervaring bij registratie van beveiligingsgegevens](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

## <a name="enable-combined-registration"></a>Gecombineerde registratie inschakelen

Als u gecombineerde registratie wilt inschakelen, moet u deze stappen voltooien:

1. Meld u aan bij Azure Portal als een gebruikersbeheerder of globale beheerder.
2. Ga naar **Azure Active Directory**  >  **Gebruikersinstellingen**  >  **Preview-instellingen voor gebruikersfunctie beheren.**
3. Kies **onder Gebruikers kunnen de gecombineerde registratie-ervaring voor beveiligingsgegevens** gebruiken voor inschakelen voor een **geselecteerde** groep gebruikers of **voor Alle** gebruikers.

   ![De gecombineerde ervaring met beveiligingsgegevens voor gebruikers inschakelen](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info.png)

> [!NOTE]
> Nadat u gecombineerde registratie hebt ingeschakeld, kunnen gebruikers die hun telefoonnummer of mobiele app via de nieuwe ervaring registreren of bevestigen, ze gebruiken voor Azure AD Multi-Factor Authentication en SSPR, als deze methoden zijn ingeschakeld in het Azure AD Multi-Factor Authentication- en SSPR-beleid.
>
> Als u deze ervaring vervolgens uit schakelen, moeten gebruikers die naar de vorige SSPR-registratiepagina op gaan multi-factor authentication uitvoeren voordat `https://aka.ms/ssprsetup` ze toegang hebben tot de pagina.

Als u de  lijst site-naar-zonetoewijzing hebt geconfigureerd in Internet Explorer, moeten de volgende sites zich in dezelfde zone bevindt:

* *[https://login.microsoftonline.com](https://login.microsoftonline.com)*
* *[https://mysignins.microsoft.com](https://mysignins.microsoft.com)*
* *[https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)*

## <a name="conditional-access-policies-for-combined-registration"></a>Beleid voor voorwaardelijke toegang voor gecombineerde registratie

Als u wilt beveiligen wanneer en hoe gebruikers zich registreren voor Azure AD Multi-Factor Authentication en selfservice voor wachtwoord opnieuw instellen, kunt u gebruikersacties gebruiken in het beleid voor voorwaardelijke toegang. Deze functionaliteit kan worden ingeschakeld in organisaties die willen dat gebruikers zich registreren voor Azure AD Multi-Factor Authentication en SSPR vanaf een centrale locatie, zoals een vertrouwde netwerklocatie tijdens de onboarding van HR.

> [!NOTE]
> Dit beleid is alleen van toepassing wanneer een gebruiker toegang heeft tot een gecombineerde registratiepagina. Met dit beleid wordt MFA-inschrijving niet afgedwongen wanneer een gebruiker toegang heeft tot andere toepassingen.
>
> U kunt een MFA-registratiebeleid maken met [behulp van Azure Identity Protection - MFA-beleid configureren.](../identity-protection/howto-identity-protection-configure-mfa-policy.md)

Zie Wat is de locatievoorwaarde in Azure Active Directory Voorwaardelijke toegang? voor meer informatie over het maken van vertrouwde locaties [in voorwaardelijke toegang.](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Een beleid maken om registratie vanaf een vertrouwde locatie te vereisen

Voltooi de volgende stappen om een beleid te maken dat van toepassing is op alle geselecteerde gebruikers die proberen te registreren met behulp van de gecombineerde registratie-ervaring, en blokkeert de toegang tenzij ze verbinding maken vanaf een locatie die is gemarkeerd als vertrouwd netwerk:

1. Blader in **Azure Portal** naar **Azure Active Directory**  >  **Security**  >  **Conditional Access**.
1. Selecteer **+ Nieuw beleid.**
1. Voer een naam in voor dit beleid, zoals *Registratie van gecombineerde beveiligingsgegevens in vertrouwde netwerken.*
1. Onder **Toewijzingen** selecteert u **Gebruikers en groepen**. Kies de gebruikers en groepen op wie u dit beleid wilt toepassen en selecteer vervolgens **Done**.

   > [!WARNING]
   > Gebruikers moeten zijn ingeschakeld voor gecombineerde registratie.

1. Selecteer **onder Cloud-apps of -acties** **de optie Gebruikersacties.** Schakel **Beveiligingsgegevens registreren in** en selecteer vervolgens **Done.**

    ![Een beleid voor voorwaardelijke toegang maken om de registratie van beveiligingsgegevens te controleren](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. Configureer   >  **onder Locaties** voorwaarden de volgende opties:
   1. Configureer **Ja.**
   1. Neem **Elke locatie op.**
   1. Sluit **Alle vertrouwde locaties uit.**
1. Selecteer **Done** in *het venster Locations* en selecteer vervolgens **Done** in het *venster Conditions.*
1. Kies **onder**  >  **Toegangsbesturingselementen** Verlenen **de optie Toegang blokkeren** en selecteer **vervolgens**.
1. Stel **Beleid inschakelen** in op **Aan**.
1. Selecteer Maken om het beleid te **maken.**

## <a name="next-steps"></a>Volgende stappen

Zie Problemen met registratie van gecombineerde [beveiligingsgegevens](howto-registration-mfa-sspr-combined-troubleshoot.md) oplossen als u hulp nodig hebt of zie Wat is de locatievoorwaarde in Voorwaardelijke toegang [van Azure AD?](../conditional-access/location-condition.md)

Zodra gebruikers zijn ingeschakeld voor gecombineerde registratie, kunt u [selfservice voor](tutorial-enable-sspr.md) wachtwoord opnieuw instellen inschakelen en [Azure AD Multi-Factor Authentication inschakelen.](tutorial-enable-azure-mfa.md)

Als dat nodig is, leert u hoe u [gebruikers kunt dwingen om verificatiemethoden opnieuw te registreren.](howto-mfa-userdevicesettings.md#manage-user-authentication-options)
