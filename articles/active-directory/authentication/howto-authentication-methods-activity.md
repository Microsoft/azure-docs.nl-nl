---
title: Activiteit verificatiemethoden - Azure Active Directory
description: Overzicht van de verificatiemethoden die gebruikers registreren om zich aan te melden en wachtwoorden opnieuw in te stellen.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/16/2021
ms.author: justinha
author: sopand
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 699ff88e4181dada5eacaa3f13469722cdf7ceaa
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530453"
---
# <a name="authentication-methods-activity"></a>Activiteit in verificatiemethoden 

Met het nieuwe activiteitsdashboard voor verificatiemethoden kunnen beheerders de registratie en het gebruik van verificatiemethoden binnen hun organisatie bewaken. Deze rapportagemogelijkheid biedt uw organisatie de mogelijkheid om te begrijpen welke methoden worden geregistreerd en hoe ze worden gebruikt.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="permissions-and-licenses"></a>Machtigingen en licenties

Ingebouwde en aangepaste rollen met de volgende machtigingen hebben toegang tot de blade Activiteit van verificatiemethoden en API's:

- Microsoft.directory/auditLogs/allProperties/read
- Microsoft.directory/signInReports/allProperties/read

De volgende rollen hebben de vereiste machtigingen:

- Rapportenlezer
- Beveiligingslezer
- Algemene lezer
- Beveiligingsoperator
- Beveiligingsbeheer
- Hoofdbeheerder

 Een Azure AD Premium P1- of P2-licentie is vereist voor toegang tot gebruik en inzichten. Informatie over Azure AD Multi-Factor Authentication en selfservice voor wachtwoord opnieuw instellen (SSPR) vindt u op de Azure Active Directory [de site met prijzen.](https://azure.microsoft.com/pricing/details/active-directory/)

## <a name="how-it-works"></a>Uitleg

Voor toegang tot het gebruik van de verificatiemethode en inzichten:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Klik **Azure Active Directory**  >  **op Activiteit**  >  **beveiligingsverificatiemethoden.**  >  
1. Het rapport heeft twee tabbladen: **Registratie** en **Gebruik.**

   ![Overzicht van de activiteit van verificatiemethoden](media/how-to-authentication-methods-usage-insights/registration-usage-tabs.png)

## <a name="registration-details"></a>Registratiegegevens

U kunt het tabblad [**Registratie openen**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade) om het aantal gebruikers weer te geven dat geschikt is voor meervoudige verificatie, wachtwoordloze verificatie en selfservice voor wachtwoord opnieuw instellen. 

Klik op een van de volgende opties om vooraf een lijst met gebruikersregistratiegegevens te filteren:

- **Gebruikers die azure Multi-Factor Authentication kunnen gebruiken,** geven de uitsplitsing weer van gebruikers die beide zijn:
  - Geregistreerd voor een sterke verificatiemethode 
  - Ingeschakeld door beleid om die methode te gebruiken voor MFA 
  
  Dit nummer geeft geen gebruikers weer die zijn geregistreerd voor MFA buiten Azure AD. 
- Gebruikers **die** verificatie zonder wachtwoord kunnen gebruiken, geven de uitsplitsing weer van gebruikers die zijn geregistreerd om zich zonder wachtwoord aan te melden met behulp van FIDO2, Windows Hello voor Bedrijven of wachtwoordloze telefonische aanmelding met de Microsoft Authenticator-app. 
- **Gebruikers die selfservice voor wachtwoord opnieuw instellen kunnen gebruiken,** geven de uitsplitsing weer van gebruikers die hun wachtwoord opnieuw kunnen instellen. Gebruikers kunnen hun wachtwoord opnieuw instellen als ze beide zijn:
  - Geregistreerd voor voldoende methoden om te voldoen aan het beleid van hun organisatie voor selfservice voor wachtwoord opnieuw instellen 
  - Ingeschakeld om het wachtwoord opnieuw in te stellen 

  ![Schermopname van gebruikers die zich kunnen registreren](media/how-to-authentication-methods-usage-insights/users-capable.png)

**Gebruikers die zijn geregistreerd via de verificatiemethode,** geven aan hoeveel gebruikers er zijn geregistreerd voor elke verificatiemethode. Klik op een verificatiemethode om te zien wie is geregistreerd voor die methode.

![Schermopname van geregistreerde gebruikers](media/how-to-authentication-methods-usage-insights/users-registered.png)

**Recente registratie op verificatiemethode laat** zien hoeveel registraties zijn geslaagd en mislukt, gesorteerd op verificatiemethode. Klik op een verificatiemethode om recente registratiegebeurtenissen voor die methode te bekijken.

![Schermopname van Onlangs geregistreerd](media/how-to-authentication-methods-usage-insights/recently-registered.png)

## <a name="usage-details"></a>Gebruiksgegevens

In **het gebruiksrapport** ziet u welke verificatiemethoden worden gebruikt om u aan te melden en wachtwoorden opnieuw in te stellen.

![Schermopname van de gebruikspagina](media/how-to-authentication-methods-usage-insights/usage-page.png)

**Aanmeldingen op** verificatievereiste toont het aantal geslaagde interactieve aanmeldingen van gebruikers dat vereist was voor enkelvoudige versus meervoudige verificatie in Azure AD. Aanmeldingen waarbij MFA is afgedwongen door een externe MFA-provider, worden niet opgenomen.

![Schermopname van aanmeldingen op verificatievereiste](media/how-to-authentication-methods-usage-insights/sign-ins-protected.png)

**Aanmeldingen per verificatiemethode toont** het aantal interactieve aanmeldingen van gebruikers (geslaagd en mislukt) per gebruikte verificatiemethode. Het bevat geen aanmeldingen waar aan de verificatievereiste is voldaan door een claim in het token.

![Schermopname van aanmeldingen per methode](media/how-to-authentication-methods-usage-insights/sign-ins-by-method.png)

**Het aantal keer dat het wachtwoord opnieuw** wordt ingesteld en het account wordt ontgrendeld, toont het aantal geslaagde wachtwoordwijzigingen en het opnieuw instellen van wachtwoorden (selfservice en door beheerder) gedurende een periode.

![Schermopname van opnieuw instellen en ontgrendelen](media/how-to-authentication-methods-usage-insights/password-changes.png)

**Wachtwoord opnieuw instellen op verificatiemethode toont** het aantal geslaagde en mislukte verificaties tijdens de stroom voor wachtwoord opnieuw instellen per verificatiemethode.

![Schermopname van Resets by method](media/how-to-authentication-methods-usage-insights/resets-by-method.png)

## <a name="user-registration-details"></a>Registratiegegevens van gebruikers 

Met behulp van de besturingselementen boven aan de lijst kunt u een gebruiker zoeken en de lijst met gebruikers filteren op basis van de weergegeven kolommen.

Het rapport met registratiegegevens bevat de volgende informatie voor elke gebruiker:

- User Principal Name
- Name
- Geschikt voor MFA (geschikt, niet mogelijk)
- Geschikt voor zonder wachtwoord (geschikt, niet mogelijk)
- SSPR geregistreerd (geregistreerd, niet geregistreerd)
- SSPR ingeschakeld (ingeschakeld, niet ingeschakeld)
- SSPR-geschikt (geschikt, niet mogelijk) 
- Geregistreerde methoden (e-mail, mobiele telefoon, alternatieve mobiele telefoon, telefoon op kantoor, Microsoft Authenticator-push, Software One Time-wachtwoordcode, FIDO2, beveiligingssleutel, beveiligingsvragen)

  ![Schermopname van details van gebruikersregistratie](media/how-to-authentication-methods-usage-insights/registration-details.png)

## <a name="registration-and-reset-events"></a>Registratie- en resetgebeurtenissen 

**Registratie- en resetgebeurtenissen** tonen registratie- en resetgebeurtenissen van de afgelopen 24 uur, de afgelopen zeven dagen of de afgelopen 30 dagen, waaronder:

- Datum
- Gebruikersnaam
- Gebruiker 
- Functie (registratie, opnieuw instellen)
- Gebruikte methode (app-melding, app-code, telefoongesprek, kantooroproep, alternatief mobiel gesprek, sms, e-mail, beveiligingsvragen)
- Status (geslaagd, mislukt)
- Reden voor fout (uitleg)

  ![Schermopname van registratie- en resetgebeurtenissen](media/how-to-authentication-methods-usage-insights/registration-and-reset-logs.png)

## <a name="limitations"></a>Beperkingen

- De gegevens in het rapport worden niet in realtime bijgewerkt en kunnen een latentie van enkele uren weerspiegelen.
- Tijdelijke toegangspas registraties worden niet weergegeven op het tabblad Registratie van het rapport, omdat ze slechts korte tijd geldig zijn.
- De **PhoneAppNotification-** of **PhoneAppOTP-methoden** die een gebruiker mogelijk heeft geconfigureerd, worden niet weergegeven in het dashboard. 

## <a name="next-steps"></a>Volgende stappen

- [Werken met de API voor verificatiemethoden voor gebruiksrapport](/graph/api/resources/authenticationmethods-usage-insights-overview)
- [Verificatiemethoden kiezen voor uw organisatie](concept-authentication-methods.md)
- [Gecombineerde registratie-ervaring](concept-registration-mfa-sspr-combined.md)
