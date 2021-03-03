---
title: Activiteit van verificatie methoden-Azure Active Directory
description: Overzicht van de verificatie methoden die door uw organisatie zijn geregistreerd en gebruikt om u aan te melden en wacht woord opnieuw in te stellen.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/22/2021
ms.author: justinha
author: sopand
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6566ee7e744411fc3b7005938f95181e5c5ec94d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101645138"
---
# <a name="authentication-methods-activity"></a>Activiteit in verificatiemethoden 

Met het nieuwe activiteiten dashboard voor verificatie methoden kunnen beheerders de registratie van de verificatie methode en het gebruik in hun organisatie bewaken. Deze rapportage mogelijkheid biedt uw organisatie de mogelijkheid om te begrijpen welke methoden worden geregistreerd en hoe ze worden gebruikt.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="permissions-and-licenses"></a>Machtigingen en licenties

De volgende rollen hebben toegang tot het gebruik en inzichten:

- Rapportenlezer
- Beveiligingslezer
- Beveiligingsbeheer
- Hoofdbeheerder

 Een Azure AD Premium P1-of P2-licentie is vereist voor toegang tot het gebruik en inzichten. De licentie gegevens voor Azure AD Multi-Factor Authentication en self-service voor het opnieuw instellen van wacht woorden (SSPR) zijn te vinden op de [Azure Active Directory-prijs site](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="how-it-works"></a>Uitleg

Voor toegang tot het gebruik van de verificatie methode en inzichten:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Klik op **Azure Active Directory**  >  **activiteit beveiligings**  >  **verificatie methoden**  >  .
1. Het rapport bevat twee tabbladen: **registratie** en **gebruik**.

   ![Overzicht van de activiteit verificatie methoden](media/how-to-authentication-methods-usage-insights/registration-usage-tabs.png)

## <a name="registration-details"></a>Registratie Details

U kunt het [**tabblad registratie**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade) openen om het aantal gebruikers weer te geven dat geschikt is voor multi-factor Authentication, passowordless-verificatie en selfservice voor het opnieuw instellen van wacht woorden. 

Klik op **gebruikers van Azure multi-factor Authentication**, **gebruikers met een wacht woordloze verificatie** of **gebruikers met een selfservice voor wachtwoord herstel** of op inzichten om een lijst met gebruikers registratie gegevens vooraf te filteren.

- **Gebruikers van azure multi-factor Authentication** tonen de uitsplitsing van gebruikers die kunnen worden MFA in azure AD. Gebruikers worden beschouwd als mogelijk als ze beide zijn geregistreerd voor een krachtige verificatie methode en worden ingeschakeld door beleid om deze methode te gebruiken voor het uitvoeren van MFA. Dit nummer weerspiegelt geen gebruikers die zijn geregistreerd voor MFA buiten Azure AD. 
- **Gebruikers die met een wacht woord kunnen worden geauthenticeerd** , tonen de uitsplitsing van gebruikers die zich kunnen aanmelden zonder wacht woord. Dit omvat gebruikers die zijn geregistreerd voor FIDO2, Windows hello voor bedrijven en aanmelding zonder wacht woord met de app Microsoft Authenticator. 
- **Gebruikers van wie de selfservice voor wachtwoord herstel is ingesteld,** tonen de uitsplitsing van de gebruikers die de selfservice voor wachtwoord herstel kunnen uitvoeren. Gebruikers worden beschouwd als SSPR als ze beide zijn geregistreerd voor voldoende methoden om te voldoen aan het SSPR-beleid van een organisatie en kunnen worden ingeschakeld om SSPR uit te voeren. 

  ![Scherm afbeelding van gebruikers die kunnen worden geregistreerd](media/how-to-authentication-methods-usage-insights/users-capable.png)

**Gebruikers die zijn geregistreerd door de verificatie methode** laten zien hoeveel gebruikers zijn geregistreerd voor elke verificatie methode. Klik op een verificatie methode om te zien welke gebruikers zijn geregistreerd voor die methode. 

![Scherm opname van geregistreerde gebruikers](media/how-to-authentication-methods-usage-insights/users-registered.png)

Met de **methode recente registratie per verificatie** wordt het aantal geslaagde en mislukte verificatie methode registraties per verificatie methode weer gegeven. Klik op een verificatie methode om recente registratie gebeurtenissen voor deze methode weer te geven.

![Scherm opname van recent geregistreerde](media/how-to-authentication-methods-usage-insights/recently-registered.png)

## <a name="usage-details"></a>Gebruiksgegevens

In het **gebruiks** rapport kunt u zien welke verificatie methoden gebruikers gebruiken om zich aan te melden en hun wacht woord opnieuw in te stellen.

![Scherm afbeelding van gebruiks pagina](media/how-to-authentication-methods-usage-insights/usage-page.png)

**Aanmeldingen op basis van de authenticatie vereisten** toont het aantal geslaagde gebruikers interactieve aanmeldingen dat is vereist voor het uitvoeren van één factor tegenover multi-factor Authentication in azure AD. Dit weerspiegelt geen aanmeldingen waarbij MFA is afgedwongen door een MFA-provider van derden.

![Scherm opname van de aanmeldingen op basis van de verificatie vereiste](media/how-to-authentication-methods-usage-insights/sign-ins-protected.png)

**Aanmeldingen op basis van de verificatie methode** toont het aantal gebruikers interactieve aanmeldingen (geslaagd en mislukt) op basis van de gebruikte verificatie methode. Het bevat geen aanmeldingen waarvoor aan de authenticatie vereiste is voldaan door een claim in het token.

![Scherm opname van de aanmeldingen per methode](media/how-to-authentication-methods-usage-insights/sign-ins-by-method.png)

Het **aantal wacht woord opnieuw instellen en account ontgrendelingen** toont het aantal geslaagde wachtwoord wijzigingen en het opnieuw instellen van wacht woorden (self-service en door de beheerder) in de loop van de tijd.

![Scherm opname van resetten en ontgrendelingen](media/how-to-authentication-methods-usage-insights/password-changes.png)

**Wacht woord opnieuw instellen op basis van de verificatie methode** toont het aantal geslaagde en mislukte authenticaties tijdens het wacht woord opnieuw instellen op basis van de verificatie methode.

![Scherm opname van reset by-methode](media/how-to-authentication-methods-usage-insights/resets-by-method.png)

## <a name="user-registration-details"></a>Details van gebruikers registratie 

Met de besturings elementen aan de bovenkant van de lijst kunt u zoeken naar een gebruiker en de lijst met gebruikers filteren op basis van de weer gegeven kolommen.

In het rapport registratie Details wordt de volgende informatie weer gegeven voor elke gebruiker:

- Principal-naam van gebruiker
- Name
- MFA mogelijk (mogelijk, niet mogelijk)
- Zonder wacht woord mogelijk (mogelijk, niet mogelijk)
- SSPR geregistreerd (geregistreerd, niet geregistreerd)
- SSPR ingeschakeld (ingeschakeld, niet ingeschakeld)
- SSPR mogelijk (mogelijk, niet mogelijk) 
- Geregistreerde methoden (e-mail adres, mobiele telefoon, alternatieve mobiele telefoon, zakelijke telefoon, Microsoft Authenticator push, software One time-wachtwoord code, FIDO2, beveiligings sleutel, beveiligings vragen)

  ![Scherm opname van Details van gebruikers registratie](media/how-to-authentication-methods-usage-insights/registration-details.png)

## <a name="registration-and-reset-events"></a>Registratie en opnieuw instellen van gebeurtenissen 

In **gebeurtenissen voor registratie en opnieuw instellen** worden de registratie-en reset gebeurtenissen van de afgelopen 24 uur, afgelopen zeven dagen of afgelopen 30 dagen weer gegeven, met inbegrip van:

- Datum
- Gebruikersnaam
- Gebruiker 
- Functie (registratie, opnieuw instellen)
- Gebruikte methode (app-melding, app-code, telefoon gesprek, kantoor oproep, alternatieve mobiele oproep, SMS, E-mail, beveiligings vragen)
- Status (geslaagd, mislukt)
- Reden voor fout (uitleg)

  ![Scherm afbeelding van gebruiks pagina](media/how-to-authentication-methods-usage-insights/registration-and-reset-logs.png)

## <a name="limitations"></a>Beperkingen

Registraties van tijdelijke toegang (tik) worden niet weer gegeven op het tabblad registratie van het rapport, omdat ze alleen geldig zijn voor korte tijd.

## <a name="next-steps"></a>Volgende stappen

- [Werken met de verificatie methoden gebruiks rapport-API](/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [Verificatie methoden kiezen voor uw organisatie](concept-authentication-methods.md)
- [Gecombineerde registratie-ervaring](concept-registration-mfa-sspr-combined.md)