---
title: Verificatie methoden en-functies-Azure Active Directory
description: Meer informatie over de verschillende verificatie methoden en-functies die beschikbaar zijn in Azure Active Directory om aanmeldings gebeurtenissen te verbeteren en te beveiligen
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: ee10aa7c461aca65f385c735f6e9aaa28af7f9e5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103471696"
---
# <a name="what-authentication-and-verification-methods-are-available-in-azure-active-directory"></a>Welke authenticatie- en verificatiemethoden zijn er beschikbaar in Azure Active Directory?

Als onderdeel van de aanmeldings ervaring voor accounts in Azure Active Directory (Azure AD), zijn er verschillende manieren waarop een gebruiker zichzelf kan verifiëren. Een gebruikers naam en wacht woord zijn de meest voorkomende manier waarop een gebruiker historische referenties zou opgeven. Met moderne verificatie-en beveiligings functies in azure AD moet het basis wachtwoord worden aangevuld of worden vervangen door veiligere verificatie methoden.

![Tabel met de sterke punten en voorkeurs verificatie methoden in azure AD](media/concept-authentication-methods/authentication-methods.png)

Verificatie methoden zonder wacht woord zoals Windows Hello, FIDO2-beveiligings sleutels en de Microsoft Authenticator-app bieden de veiligste aanmeldings gebeurtenissen.

Azure AD Multi-Factor Authentication (MFA) voegt extra beveiliging toe via het gebruik van een wacht woord wanneer een gebruiker zich aanmeldt. De gebruiker kan worden gevraagd om aanvullende verificatie vormen, zoals om te reageren op een push melding, een code van een software-of hardware-token in te voeren, of om te reageren op een SMS-of telefoon gesprek.

Voor het vereenvoudigen van de gebruikers ervaring en registratie voor MFA en self-service voor wachtwoord herstel (SSPR), raden we u aan om [gecombineerde beveiligings gegevens](howto-registration-mfa-sspr-combined.md)te registreren. Voor tolerantie raden wij aan dat gebruikers meerdere verificatie methoden moeten registreren. Wanneer een methode niet beschikbaar is voor een gebruiker tijdens het aanmelden of SSPR, kan deze een andere methode verifiëren. Zie [een flexibele toegangs beheer strategie maken in azure AD](concept-resilient-controls.md)voor meer informatie.

Hier volgt een [video](https://www.youtube.com/watch?v=LB2yj4HSptc&feature=youtu.be) die we hebben gemaakt om u te helpen bij het kiezen van de beste verificatie methode om uw organisatie veilig te houden.

## <a name="authentication-method-strength-and-security"></a>De sterkte en beveiliging van de verificatie methode

Wanneer u functies zoals Azure AD Multi-Factor Authentication in uw organisatie implementeert, controleert u de beschik bare verificatie methoden. Kies de methoden die voldoen aan of overschrijden uw vereisten op het gebied van beveiliging, bruikbaarheid en beschik baarheid. Gebruik waar mogelijk authenticatie methoden met het hoogste beveiligings niveau.

De volgende tabel bevat een overzicht van de beveiligings overwegingen voor de beschik bare verificatie methoden. Beschik baarheid is een indicatie dat de gebruiker de verificatie methode kan gebruiken, en niet van de beschik baarheid van de service in azure AD:

| Verificatiemethode          | Beveiliging | Bruikbaarheid | Beschikbaarheid |
|--------------------------------|:--------:|:---------:|:------------:|
| Windows Hello voor Bedrijven     | Hoog     | Hoog      | Hoog         |
| Microsoft Authenticator-app    | Hoog     | Hoog      | Hoog         |
| FIDO2-beveiligings sleutel             | Hoog     | Hoog      | Hoog         |
| OATH-hardware-tokens (preview-versie) | Gemiddeld   | Gemiddeld    | Hoog         |
| OATH-software tokens           | Gemiddeld   | Gemiddeld    | Hoog         |
| Sms                            | Normaal   | Hoog      | Gemiddeld       |
| Spraak                          | Gemiddeld   | Gemiddeld    | Gemiddeld       |
| Wachtwoord                       | Laag      | Hoog      | Hoog         |

Bekijk onze blog berichten voor de meest recente informatie over beveiliging:

- [Het is tijd om op telefoon transporten te hangen voor verificatie](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/it-s-time-to-hang-up-on-phone-transports-for-authentication/ba-p/1751752)
- [Verificatie beveiligings problemen en aanvals vectoren](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124)

> [!TIP]
> Voor flexibiliteit en bruikbaarheid raden we u aan de Microsoft Authenticator-app te gebruiken. Deze verificatie methode biedt de beste gebruikers ervaring en meerdere modi, zoals wacht woorden, MFA-push meldingen en OATH-codes.

## <a name="how-each-authentication-method-works"></a>Hoe een verificatie methode werkt

Sommige verificatie methoden kunnen worden gebruikt als de primaire factor wanneer u zich aanmeldt bij een toepassing of apparaat, zoals het gebruik van een FIDO2-beveiligings sleutel of een wacht woord. Andere verificatie methoden zijn alleen beschikbaar als secundaire factor wanneer u Azure AD Multi-Factor Authentication of SSPR gebruikt.

De volgende tabel geeft een overzicht van de wijze waarop een verificatie methode kan worden gebruikt tijdens een aanmeldings gebeurtenis:

| Methode                         | Primaire authenticatie | Secundaire verificatie  |
|--------------------------------|:----------------------:|:-------------------------:|
| Windows Hello voor Bedrijven     | Ja                    | MFA                       |
| Microsoft Authenticator-app    | Ja                    | MFA en SSPR              |
| FIDO2-beveiligings sleutel             | Ja                    | MFA                       |
| OATH-hardware-tokens (preview-versie) | Nee                     | MFA                       |
| OATH-software tokens           | Nee                     | MFA                       |
| Sms                            | Ja                    | MFA en SSPR              |
| Spraakoproep                     | Nee                     | MFA en SSPR              |
| Wachtwoord                       | Ja                    |                           |

Al deze verificatie methoden kunnen worden geconfigureerd in de Azure Portal en het gebruik van de [Microsoft Graph rest API bèta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta)wordt steeds vaker gebruikt.

Raadpleeg de volgende afzonderlijke conceptuele artikelen voor meer informatie over de werking van elke verificatie methode:

* [Windows hello voor bedrijven](/windows/security/identity-protection/hello-for-business/hello-overview)
* [Microsoft Authenticator-app](concept-authentication-authenticator-app.md)
* [FIDO2-beveiligings sleutel](concept-authentication-passwordless.md#fido2-security-keys)
* [OATH-hardware-tokens (preview-versie)](concept-authentication-oath-tokens.md#oath-hardware-tokens-preview)
* [OATH-software tokens](concept-authentication-oath-tokens.md#oath-software-tokens)
* [SMS-aanmelding](howto-authentication-sms-signin.md) en [verificatie](concept-authentication-phone-options.md#mobile-phone-verification)
* [Verificatie van spraak oproep](concept-authentication-phone-options.md)
* Wachtwoord

> [!NOTE]
> In azure AD is een wacht woord vaak een van de primaire verificatie methoden. U kunt de verificatie methode voor wacht woorden niet uitschakelen. Als u een wacht woord als primaire verificatie factor gebruikt, verhoogt u de beveiliging van aanmeldings gebeurtenissen met Azure AD Multi-Factor Authentication.

De volgende aanvullende verificatie methoden kunnen worden gebruikt in bepaalde scenario's:

* [App-wacht woorden](howto-mfa-app-passwords.md) : wordt gebruikt voor oude toepassingen die geen ondersteuning bieden voor moderne verificatie en kunnen worden geconfigureerd voor Azure AD-multi-factor Authentication per gebruiker.
* [Beveiligings vragen](concept-authentication-security-questions.md) : alleen gebruikt voor SSPR
* [E-mail adres](concept-sspr-howitworks.md#authentication-methods) : alleen gebruikt voor SSPR

## <a name="next-steps"></a>Volgende stappen

Zie de [zelfstudie voor SSPR (self-service voor wachtwoordherstel)][tutorial-sspr] en [Azure AD Multi-Factor Authentication][tutorial-azure-mfa] om aan de slag te gaan.

Zie [hoe Azure AD self-service password reset werkt][concept-sspr]voor meer informatie over SSPR-concepten.

Zie [hoe Azure AD multi-factor Authentication werkt][concept-mfa]voor meer informatie over MFA-concepten.

Meer informatie over het configureren van verificatie methoden met behulp van de [Microsoft Graph rest API bèta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).

Zie [Azure AD multi-factor authentication-verificatie methode analyse met Power shell](/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/)om te controleren welke verificatie methoden in gebruik zijn.

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
