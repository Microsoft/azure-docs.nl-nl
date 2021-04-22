---
title: Aanmelden zonder wachtwoord met de Microsoft Authenticator app - Azure Active Directory
description: Aanmelden zonder wachtwoord bij Azure AD inschakelen met behulp van de Microsoft Authenticator app
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/21/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: d586294f101c271f139867d0046576dc9a32f076
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861783"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app"></a>Aanmelden zonder wachtwoord inschakelen met de Microsoft Authenticator app 

De Microsoft Authenticator-app kan worden gebruikt om u aan te melden bij een Azure AD-account zonder een wachtwoord te gebruiken. Microsoft Authenticator maakt gebruik van verificatie op basis van sleutels om een gebruikersreferentie in te schakelen die is gekoppeld aan een apparaat, waarbij het apparaat gebruikmaakt van een pincode of biometrische gegevens. [Windows Hello voor Bedrijven](/windows/security/identity-protection/hello-for-business/hello-identity-verification) maakt gebruik van een vergelijkbare technologie.

Deze verificatietechnologie kan worden gebruikt op elk apparaatplatform, inclusief mobiel. Deze technologie kan ook worden gebruikt met elke app of website die is geïntegreerd met Microsoft-verificatiebibliotheken.

:::image type="content" border="false" source="./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png" alt-text="Voorbeeld van een aanmelding in een browser waarin de gebruiker wordt gevraagd om de aanmelding goed te keuren.":::

Personen die aanmelding via de telefoon hebben ingeschakeld vanuit de Microsoft Authenticator app zien een bericht waarin hen wordt gevraagd op een nummer in hun app te tikken. Er wordt geen gebruikersnaam of wachtwoord gevraagd. Om het aanmeldingsproces in de app te voltooien, moet een gebruiker de volgende acties uitvoeren:

1. Komt overeen met het getal.
2. Kies **Goedkeuren**.
3. Geef de pincode of biometrische gegevens op.

## <a name="prerequisites"></a>Vereisten

Als u aanmelding via een wachtwoordloze telefoon met de Microsoft Authenticator app wilt gebruiken, moet aan de volgende vereisten worden voldaan:

- Azure AD Multi-Factor Authentication, met pushmeldingen toegestaan als verificatiemethode.
- Nieuwste versie van Microsoft Authenticator geïnstalleerd op apparaten met iOS 8.0 of hoger of Android 6.0 of hoger.
- Het apparaat waarop de Microsoft Authenticator app is geïnstalleerd, moet binnen de Azure AD-tenant worden geregistreerd bij een afzonderlijke gebruiker. 

> [!NOTE]
> Als u aanmelding Microsoft Authenticator zonder wachtwoord hebt ingeschakeld met behulp van Azure AD PowerShell, is dit ingeschakeld voor uw hele directory. Als u het gebruik van deze nieuwe methode inschakelen, wordt het PowerShell-beleid. U kunt het beste inschakelen voor alle gebruikers in uw tenant via het nieuwe *menu* Verificatiemethoden, anders kunnen gebruikers die niet in het nieuwe beleid zijn niet langer aanmelden zonder wachtwoord.

## <a name="enable-passwordless-authentication-methods"></a>Verificatiemethoden zonder wachtwoord inschakelen

Als u verificatie zonder wachtwoord in Azure AD wilt gebruiken, moet u eerst de gecombineerde registratie-ervaring inschakelen en vervolgens gebruikers inschakelen voor de methode zonder wachtwoord.

### <a name="enable-the-combined-registration-experience"></a>De gecombineerde registratie-ervaring inschakelen

Registratiefuncties voor verificatiemethoden zonder wachtwoord zijn afhankelijk van de gecombineerde registratiefunctie. Als u wilt dat gebruikers de gecombineerde registratie zelf kunnen voltooien, volgt u de stappen om registratie [van gecombineerde beveiligingsgegevens in teschakelen.](howto-registration-mfa-sspr-combined.md)

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>Verificatiemethoden voor aanmelding via een telefoon zonder wachtwoord inschakelen

Met Azure AD kunt u kiezen welke verificatiemethoden tijdens het aanmeldingsproces kunnen worden gebruikt. Gebruikers registreren zich vervolgens voor de methoden die ze willen gebruiken.

Als u de verificatiemethode voor aanmelding via een telefoon zonder wachtwoord wilt inschakelen, moet u de volgende stappen voltooien:

1. Meld u aan bij [Azure Portal](https://portal.azure.com) account van *een globale* beheerder.
1. Zoek en selecteer *Azure Active Directory* en blader vervolgens naar   >  **Beleidsregels voor beveiligingsverificatiemethoden.**  >  
1. Kies **Microsoft Authenticator** volgende opties onder Microsoft Authenticator:
   1. **Inschakelen** : Ja of Nee
   1. **Doel:** alle gebruikers of Gebruikers selecteren
1. Elke toegevoegde groep of gebruiker is standaard ingeschakeld voor het gebruik van Microsoft Authenticator in zowel de modus voor wachtwoordloze als pushmeldingen ('Alle'-modus). Als u dit wilt wijzigen, voor elke rij:
   1. Blader naar **...**  >  **Configureer**.
   1. Voor **verificatiemodus:** alle, zonder wachtwoord of push
1. Selecteer Opslaan om het nieuwe beleid toe **te passen.**

## <a name="user-registration-and-management-of-microsoft-authenticator"></a>Gebruikersregistratie en -beheer van Microsoft Authenticator

Gebruikers registreren zichzelf voor de verificatiemethode zonder wachtwoord van Azure AD met behulp van de volgende stappen:

1. Blader naar [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo).
1. Meld u aan en voeg vervolgens de Authenticator-app toe door **Methode toevoegen > Authenticator-app** en vervolgens **Toevoegen.**
1. Volg de instructies voor het installeren en configureren van Microsoft Authenticator app op uw apparaat.
1. Selecteer **Done om** de Authenticator-configuratie te voltooien.
1. In **Microsoft Authenticator** kiest u **Aanmelding via telefoon inschakelen in** de vervolgkeuzelijst voor het geregistreerde account.
1. Volg de instructies in de app om de registratie van het account voor aanmelding via een telefoon zonder wachtwoord te voltooien.

Een organisatie kan haar gebruikers vragen zich aan te melden met hun telefoons, zonder een wachtwoord te gebruiken. Zie Aanmelden bij uw accounts met behulp van de Microsoft Authenticator app voor meer hulp bij het configureren van de Microsoft Authenticator-app en het [inschakelen van aanmelding via de telefoon.](../user-help/user-help-auth-app-sign-in.md)

> [!NOTE]
> Gebruikers die niet door het beleid zijn toegestaan om telefonische aanmelding te gebruiken, kunnen dit niet meer inschakelen in de Microsoft Authenticator app.

## <a name="sign-in-with-passwordless-credential"></a>Aanmelden met referenties zonder wachtwoord

Een gebruiker kan zich aanmelden zonder wachtwoord gaan gebruiken nadat alle volgende acties zijn voltooid:

- Een beheerder heeft de tenant van de gebruiker ingeschakeld.
- De gebruiker heeft haar app Microsoft Authenticator om aanmelding via de telefoon in te kunnenschakelen.

De eerste keer dat een gebruiker het aanmeldingsproces voor de telefoon start, voert de gebruiker de volgende stappen uit:

1. Voert haar naam in op de aanmeldingspagina.
2. Selecteert **Volgende.**
3. Selecteer indien nodig Andere **manieren om u aan te melden.**
4. Selecteert **Een aanvraag goedkeuren op mijn Microsoft Authenticator app**.

De gebruiker krijgt vervolgens een getal te zien. De app vraagt de gebruiker om zich te verifiëren door het juiste nummer te selecteren in plaats van door een wachtwoord in te voeren.

Nadat de gebruiker zich via een telefoon zonder wachtwoord heeft aanmelden, blijft de app de gebruiker door deze methode leiden. De gebruiker ziet echter de optie om een andere methode te kiezen.

:::image type="content" border="false" source="./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png" alt-text="Voorbeeld van een aanmelding in een browser met behulp van de Microsoft Authenticator app.":::

## <a name="known-issues"></a>Bekende problemen

De volgende bekende problemen bestaan.

### <a name="not-seeing-option-for-passwordless-phone-sign-in"></a>Optie voor aanmelden via telefoon zonder wachtwoord niet te zien

In één scenario kan een gebruiker een niet-beantwoorde verificatie via een telefoon zonder wachtwoord hebben die in behandeling is. Toch kan de gebruiker zich opnieuw proberen aan te melden. Als dit gebeurt, ziet de gebruiker mogelijk alleen de optie om een wachtwoord in te voeren.

Om dit scenario op te lossen, kunnen de volgende stappen worden gebruikt:

1. Open de app Microsoft Authenticator.
2. Reageren op meldingsprompts.

Vervolgens kan de gebruiker zich blijven aanmelden zonder wachtwoord.

### <a name="federated-accounts"></a>Federatief accounts

Wanneer een gebruiker referenties zonder wachtwoord heeft ingeschakeld, stopt het Azure AD-aanmeldingsproces met het gebruik van de \_ aanmeldingshint. Daarom versnelt het proces de gebruiker niet langer naar een federatief aanmeldingslocatie.

Deze logica voorkomt in het algemeen dat een gebruiker in een hybride tenant wordt omgeleid naar Active Directory Federated Services (AD FS) voor aanmeldingsverificatie. De gebruiker behoudt echter de optie om in plaats daarvan op **Uw wachtwoord gebruiken te klikken.**

### <a name="azure-mfa-server"></a>Azure MFA-server

Een eindgebruiker kan worden ingeschakeld voor Meervoudige verificatie (MFA) via een on-premises Azure MFA-server. De gebruiker kan nog steeds één aanmeldingsreferentie zonder wachtwoord maken en gebruiken.

Als de gebruiker probeert meerdere installaties (5+) van de Microsoft Authenticator-app bij te werken met de aanmeldingsreferenties voor de telefoon zonder wachtwoord, kan deze wijziging resulteren in een fout.

### <a name="device-registration"></a>Apparaatregistratie

Voordat u deze nieuwe sterke referentie kunt maken, zijn er vereisten. Een vereiste is dat het apparaat waarop de Microsoft Authenticator app is geïnstalleerd, binnen de Azure AD-tenant moet worden geregistreerd voor een afzonderlijke gebruiker.

Op dit moment kan een apparaat alleen worden geregistreerd in één tenant. Deze limiet betekent dat slechts één werk- of schoolaccount in de Microsoft Authenticator-app kan worden ingeschakeld voor telefonisch aanmelden.

> [!NOTE]
> Apparaatregistratie is niet hetzelfde als apparaatbeheer of MDM (Mobile Device Management). Apparaatregistratie koppelt alleen een apparaat-id en een gebruikers-id aan elkaar in de Azure AD-directory.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over Azure AD-verificatie en methoden zonder wachtwoord:

- [Meer informatie over hoe verificatie zonder wachtwoord werkt](concept-authentication-passwordless.md)
- [Meer informatie over apparaatregistratie](../devices/overview.md#getting-devices-in-azure-ad)
- [Meer informatie over Azure AD Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
