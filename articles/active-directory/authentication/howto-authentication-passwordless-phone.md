---
title: Aanmelding zonder wacht woord met de app-Azure Active Directory Microsoft Authenticator
description: Aanmelding zonder wacht woord inschakelen voor Azure AD met behulp van de Microsoft Authenticator-app (preview)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/11/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 62dea67f139857befc7ca24b8b8a105241b5c949
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/06/2021
ms.locfileid: "99626152"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app-preview"></a>Aanmelding zonder wacht woord inschakelen met de Microsoft Authenticator-app (preview)

De Microsoft Authenticator-app kan worden gebruikt om u aan te melden bij een Azure AD-account zonder gebruik te maken van een wacht woord. Microsoft Authenticator maakt gebruik van verificatie op basis van een sleutel om een gebruikers referentie in te scha kelen die is gekoppeld aan een apparaat, waarbij het apparaat een pincode of biometrische gebruikt. [Windows hello voor bedrijven](/windows/security/identity-protection/hello-for-business/hello-identity-verification) maakt gebruik van een vergelijk bare technologie.

Deze verificatie technologie kan worden gebruikt op elk platform, met inbegrip van mobiele apparaten. Deze technologie kan ook worden gebruikt in combi natie met elke app of website die kan worden geïntegreerd met micro soft-verificatie bibliotheken.

:::image type="content" border="false" source="./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png" alt-text="Voor beeld van een browser aanmelding waarin de gebruiker wordt gevraagd om de aanmelding goed te keuren.":::

Personen die aanmelding via de telefoon in de Microsoft Authenticator-app hebben ingeschakeld, zien een bericht waarin ze wordt gevraagd een nummer in hun app te tikken. Er wordt geen gebruikers naam of wacht woord gevraagd. Om het aanmeldings proces in de app te volt ooien, moet een gebruiker vervolgens de volgende acties uitvoeren:

1. Overeenkomen met het nummer.
2. Kies **Goedkeuren**.
3. Geef hun pincode of biometrische waarde op.

## <a name="prerequisites"></a>Vereisten

Als u aanmelden zonder wacht woord met de app Microsoft Authenticator wilt gebruiken, moet aan de volgende vereisten worden voldaan:

- Azure AD Multi-Factor Authentication, waarbij push meldingen zijn toegestaan als verificatie methode.
- Nieuwste versie van Microsoft Authenticator geïnstalleerd op apparaten met iOS 8,0 of hoger, of Android 6,0 of hoger.

> [!NOTE]
> Als u Microsoft Authenticator aanmeldings voorbeeld zonder wacht woord hebt ingeschakeld met behulp van Azure AD Power shell, is deze ingeschakeld voor uw hele directory. Als u het gebruik van deze nieuwe methode inschakelt, wordt het Power shell-beleid vervangen. U wordt aangeraden voor alle gebruikers in uw Tenant in te scha kelen via het menu nieuwe *authenticatie methoden* , anders kunnen gebruikers die geen deel uitmaken van het nieuwe beleid zich niet meer aanmelden zonder een wacht woord.

## <a name="enable-passwordless-authentication-methods"></a>Verificatie methoden met een wacht woord inschakelen

Als u verificatie zonder wacht woord in azure AD wilt gebruiken, moet u eerst de ervaring voor gecombineerde registratie inschakelen en gebruikers inschakelen voor de methode voor wacht woord minder.

### <a name="enable-the-combined-registration-experience"></a>De gecombineerde registratie-ervaring inschakelen

Registratie functies voor verificatie methoden met een wacht woord zijn afhankelijk van de functie voor gecombineerde registratie. Als u wilt dat gebruikers de gecombineerde registratie zelf kunnen volt ooien, volgt u de stappen voor het inschakelen van de [registratie van gecombineerde beveiligings gegevens](howto-registration-mfa-sspr-combined.md).

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>Verificatie methoden met wacht woord-aanmeld authenticatie inschakelen

Met Azure AD kunt u kiezen welke verificatie methoden tijdens het aanmeldings proces kunnen worden gebruikt. Gebruikers registreren zich vervolgens voor de methoden die ze willen gebruiken.

Voer de volgende stappen uit om de verificatie methode in te scha kelen voor aanmelding zonder wacht woord.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met een *Algemeen beheerders* account.
1. Zoek en selecteer *Azure Active Directory* en blader vervolgens naar beleid voor **beveiligings**  >  **verificatie methoden**  >  .
1. Onder **Microsoft Authenticator (preview)** kiest u de volgende opties:
   1. **Inschakelen** -ja of Nee
   1. **Doel** -alle gebruikers of Selecteer gebruikers
1. Elke toegevoegde groep of gebruiker is standaard ingeschakeld voor het gebruik van Microsoft Authenticator in zowel de modus voor zowel wacht woorden als push meldingen (' any '). Als u dit wilt wijzigen, voor elke rij:
   1. Bladeren naar **...**  >  **Configureren**.
   1. Voor **verificatie modus** : wille keurig, wacht woord of push
1. Selecteer **Opslaan** om het nieuwe beleid toe te passen.

## <a name="user-registration-and-management-of-microsoft-authenticator"></a>Gebruikers registratie en het beheer van Microsoft Authenticator

Gebruikers registreren zich voor de verificatie methode met een wacht woord voor Azure AD door de volgende stappen uit te voeren:

1. Blader naar [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo).
1. Meld u aan en voeg de verificator-app toe door **methode toevoegen > verificator-app** te selecteren en vervolgens **toe te voegen**.
1. Volg de instructies voor het installeren en configureren van de Microsoft Authenticator-app op het apparaat.
1. Selecteer **gereed** om de configuratie van de verificator te volt ooien.
1. Kies in **Microsoft Authenticator** de optie **telefoon aanmelding inschakelen** in de vervolg keuzelijst voor het account dat is geregistreerd.
1. Volg de instructies in de app om de registratie van het account voor wacht woordloze aanmelding via de telefoon te volt ooien.

Een organisatie kan zijn gebruikers om zich aan te melden met hun telefoons zonder een wacht woord te gebruiken. Zie [Aanmelden bij uw accounts met behulp van de Microsoft Authenticator-app](../user-help/user-help-auth-app-sign-in.md)voor meer informatie over het configureren van de Microsoft Authenticator-app en het inschakelen van aanmelding via de telefoon.

> [!NOTE]
> Gebruikers die niet zijn toegestaan door het beleid voor het gebruik van aanmelding via de telefoon, kunnen dit niet meer inschakelen binnen de app Microsoft Authenticator.

## <a name="sign-in-with-passwordless-credential"></a>Aanmelden met een wacht woord zonder referenties

Een gebruiker kan aanmelden zonder wacht woord gebruiken nadat de volgende acties zijn voltooid:

- Een beheerder heeft de Tenant van de gebruiker ingeschakeld.
- De gebruiker heeft haar Microsoft Authenticator-app bijgewerkt om aanmelding via de telefoon in te scha kelen.

De eerste keer dat een gebruiker het aanmeldings proces voor de telefoon start, voert de gebruiker de volgende stappen uit:

1. De naam wordt ingevoerd op de aanmeldings pagina.
2. Selecteert **volgende**.
3. Als dat nodig is, selecteert **u andere manieren om u aan te melden**.
4. Hiermee selecteert u **een aanvraag goed keuren in mijn Microsoft Authenticator-app**.

De gebruiker krijgt vervolgens een getal. De app vraagt de gebruiker om te verifiëren door het juiste nummer te selecteren in plaats van een wacht woord in te voeren.

Nadat de gebruiker aanmelding zonder wacht woord heeft gebruikt, blijft de gebruiker door deze methode begeleid. De gebruiker ziet echter de optie om een andere methode te kiezen.

:::image type="content" border="false" source="./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png" alt-text="Voor beeld van een browser aanmelding met behulp van de app Microsoft Authenticator.":::

## <a name="known-issues"></a>Bekende problemen

De volgende bekende problemen bevinden zich in de huidige preview-versie.

### <a name="not-seeing-option-for-passwordless-phone-sign-in"></a>Optie niet weer geven voor aanmelding met wacht woordloos aanmelden

In één scenario kan een gebruiker een niet-beantwoorde aanmeld verificatie voor aanmelding via wacht woord hebben die in behandeling is. De gebruiker probeert zich mogelijk opnieuw aan te melden. Als dit gebeurt, ziet de gebruiker mogelijk alleen de optie om een wacht woord in te voeren.

De volgende stappen kunnen worden gebruikt om dit scenario op te lossen:

1. Open de app Microsoft Authenticator.
2. Reageer op eventuele meldings prompts.

Vervolgens kan de gebruiker het aanmelden zonder wacht woord blijven gebruiken.

### <a name="federated-accounts"></a>Federatieve accounts

Wanneer een gebruiker een wacht woordloze referentie heeft ingeschakeld, stopt het Azure AD-aanmeldings proces met de aanmeldings \_ Hint. Het proces versnelt daarom niet langer de gebruiker naar een federatieve aanmeldings locatie.

Deze logica voor komt in het algemeen dat een gebruiker in een hybride Tenant wordt doorgestuurd naar Active Directory Federated Services (AD FS) voor aanmeldings verificatie. De gebruiker behoudt echter de mogelijkheid **om in plaats daarvan uw wacht woord gebruiken** te klikken.

### <a name="azure-mfa-server"></a>Azure MFA-server

Een eind gebruiker kan worden ingeschakeld voor multi-factor Authentication (MFA) via een on-premises Azure MFA-server. De gebruiker kan nog steeds één wacht woord voor eenmalige aanmelding voor de telefoon maken en gebruiken.

Als de gebruiker probeert om meerdere installaties (5 +) van de Microsoft Authenticator-app te upgraden met de aanmeldings referenties voor een wacht woord, kan deze wijziging resulteren in een fout.

### <a name="device-registration"></a>Apparaatregistratie

Voordat u deze nieuwe sterke referentie kunt maken, zijn er vereisten. Een van de vereisten is dat het apparaat waarop de Microsoft Authenticator-app is geïnstalleerd, in de Azure AD-Tenant moet worden geregistreerd bij een afzonderlijke gebruiker.

Op dit moment kan een apparaat alleen worden geregistreerd in één Tenant. Deze limiet betekent dat er slechts één werk-of school account in de Microsoft Authenticator-app kan worden ingeschakeld voor aanmelding via de telefoon.

> [!NOTE]
> Apparaatregistratie is niet hetzelfde als Apparaatbeheer of Mobile Device Management (MDM). Apparaatregistratie koppelt alleen een apparaat-ID en gebruikers-ID samen in de Azure AD-Directory.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over Azure AD-verificatie en wacht woord-methoden:

- [Meer informatie over de werking van verificatie met een wacht woord](concept-authentication-passwordless.md)
- [Meer informatie over apparaatregistratie](../devices/overview.md#getting-devices-in-azure-ad)
- [Meer informatie over Azure AD Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
