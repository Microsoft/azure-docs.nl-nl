---
title: Verificatiemethoden en -functies - Azure Active Directory
description: Meer informatie over de verschillende verificatiemethoden en -functies die beschikbaar zijn in Azure Active Directory om aanmeldingsgebeurtenissen te verbeteren en beveiligen
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
ms.openlocfilehash: b4d69157f4544daad962cca15e53802e7b912399
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530428"
---
# <a name="what-authentication-and-verification-methods-are-available-in-azure-active-directory"></a>Welke authenticatie- en verificatiemethoden zijn er beschikbaar in Azure Active Directory?

Als onderdeel van de aanmeldingservaring voor accounts in Azure Active Directory (Azure AD) zijn er verschillende manieren waarop een gebruiker zichzelf kan verifiëren. Een gebruikersnaam en wachtwoord zijn de meest voorkomende manier waarop een gebruiker in het verleden referenties zou opgeven. Met moderne verificatie- en beveiligingsfuncties in Azure AD moet dat basiswachtwoord worden aangevuld of vervangen door veiligere verificatiemethoden.

![Tabel met de sterke punten en voorkeursverificatiemethoden in Azure AD](media/concept-authentication-methods/authentication-methods.png)

Verificatiemethoden zonder wachtwoord, zoals Windows Hello, FIDO2-beveiligingssleutels en de Microsoft Authenticator-app bieden de veiligste aanmeldingsgebeurtenissen.

Azure AD Multi-Factor Authentication (MFA) voegt extra beveiliging toe dan alleen het gebruik van een wachtwoord wanneer een gebruiker zich meldt. De gebruiker kan worden gevraagd om aanvullende vormen van verificatie, zoals om te reageren op een pushmelding, een code van een software- of hardware-token in te voeren of te reageren op een sms- of telefoongesprek.

Ter vereenvoudiging van de gebruikerservaring bij het invoeren en registreren voor zowel MFA als selfservice voor wachtwoord opnieuw instellen (SSPR), raden we u aan gecombineerde registratie van [beveiligingsgegevens in teschakelen.](howto-registration-mfa-sspr-combined.md) Voor tolerantie wordt u aangeraden dat gebruikers meerdere verificatiemethoden moeten registreren. Wanneer er geen methode beschikbaar is voor een gebruiker tijdens het aanmelden of SSPR, kan deze ervoor kiezen om te verifiëren met een andere methode. Zie Een flexibele strategie voor [toegangsbeheer](concept-resilient-controls.md)maken in Azure AD voor meer informatie.

Hier is een [video die](https://www.youtube.com/watch?v=LB2yj4HSptc&feature=youtu.be) we hebben gemaakt om u te helpen de beste verificatiemethode te kiezen om uw organisatie veilig te houden.

## <a name="authentication-method-strength-and-security"></a>Sterkte en beveiliging van verificatiemethode

Wanneer u functies zoals Azure AD Multi-Factor Authentication in uw organisatie implementeert, controleert u de beschikbare verificatiemethoden. Kies de methoden die voldoen aan of voldoen aan uw vereisten op het gebied van beveiliging, bruikbaarheid en beschikbaarheid. Gebruik waar mogelijk verificatiemethoden met het hoogste beveiligingsniveau.

De volgende tabel bevat een overzicht van de beveiligingsoverwegingen voor de beschikbare verificatiemethoden. Beschikbaarheid is een indicatie dat de gebruiker de verificatiemethode kan gebruiken, niet van de beschikbaarheid van de service in Azure AD:

| Verificatiemethode          | Beveiliging | Bruikbaarheid | Beschikbaarheid |
|--------------------------------|:--------:|:---------:|:------------:|
| Windows Hello voor Bedrijven     | Hoog     | Hoog      | Hoog         |
| Microsoft Authenticator-app    | Hoog     | Hoog      | Hoog         |
| FIDO2-beveiligingssleutel             | Hoog     | Hoog      | Hoog         |
| OATH-hardwaretokens (preview) | Gemiddeld   | Gemiddeld    | Hoog         |
| OATH-softwaretokens           | Gemiddeld   | Gemiddeld    | Hoog         |
| Sms                            | Normaal   | Hoog      | Gemiddeld       |
| Spraak                          | Gemiddeld   | Gemiddeld    | Gemiddeld       |
| Wachtwoord                       | Laag      | Hoog      | Hoog         |

Bekijk onze blogberichten voor de meest recente informatie over beveiliging:

- [Het is tijd om op te hangen bij telefoontransporten voor verificatie](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/it-s-time-to-hang-up-on-phone-transports-for-authentication/ba-p/1751752)
- [Beveiligingsproblemen met verificatie en aanvalsvectoren](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124)

> [!TIP]
> Voor flexibiliteit en bruikbaarheid raden we u aan de app Microsoft Authenticator gebruiken. Deze verificatiemethode biedt de beste gebruikerservaring en meerdere modi, zoals wachtwoordloos, MFA-pushmeldingen en OATH-codes.

## <a name="how-each-authentication-method-works"></a>Hoe elke verificatiemethode werkt

Sommige verificatiemethoden kunnen worden gebruikt als de primaire factor bij het aanmelden bij een toepassing of apparaat, zoals het gebruik van een FIDO2-beveiligingssleutel of een wachtwoord. Andere verificatiemethoden zijn alleen beschikbaar als secundaire factor wanneer u Azure AD Multi-Factor Authentication of SSPR gebruikt.

In de volgende tabel wordt beschreven wanneer een verificatiemethode kan worden gebruikt tijdens een aanmeldingsgebeurtenis:

| Methode                         | Primaire authenticatie | Secundaire verificatie  |
|--------------------------------|:----------------------:|:-------------------------:|
| Windows Hello voor Bedrijven     | Ja                    | MFA                       |
| Microsoft Authenticator-app    | Ja                    | MFA en SSPR              |
| FIDO2-beveiligingssleutel             | Ja                    | MFA                       |
| OATH-hardwaretokens (preview) | Nee                     | MFA                       |
| OATH-softwaretokens           | Nee                     | MFA                       |
| Sms                            | Ja                    | MFA en SSPR              |
| Spraakoproep                     | Nee                     | MFA en SSPR              |
| Wachtwoord                       | Ja                    |                           |

Al deze verificatiemethoden kunnen worden geconfigureerd in de Azure Portal en in toenemende mate gebruikmaken van [de Microsoft Graph REST API](/graph/api/resources/authenticationmethods-overview).

Zie de volgende afzonderlijke conceptuele artikelen voor meer informatie over hoe elke verificatiemethode werkt:

* [Windows Hello voor Bedrijven](/windows/security/identity-protection/hello-for-business/hello-overview)
* [Microsoft Authenticator-app](concept-authentication-authenticator-app.md)
* [FIDO2-beveiligingssleutel](concept-authentication-passwordless.md#fido2-security-keys)
* [OATH-hardwaretokens (preview)](concept-authentication-oath-tokens.md#oath-hardware-tokens-preview)
* [OATH-softwaretokens](concept-authentication-oath-tokens.md#oath-software-tokens)
* [Aanmelden en verificatie via](howto-authentication-sms-signin.md) [sms](concept-authentication-phone-options.md#mobile-phone-verification)
* [Verificatie van spraakoproepen](concept-authentication-phone-options.md)
* Wachtwoord

> [!NOTE]
> In Azure AD is een wachtwoord vaak een van de primaire verificatiemethoden. U kunt de verificatiemethode voor wachtwoorden niet uitschakelen. Als u een wachtwoord als primaire verificatiefactor gebruikt, verhoogt u de beveiliging van aanmeldingsgebeurtenissen met behulp van Azure AD Multi-Factor Authentication.

De volgende aanvullende verificatiemethoden kunnen in bepaalde scenario's worden gebruikt:

* [App-wachtwoorden:](howto-mfa-app-passwords.md) worden gebruikt voor oude toepassingen die geen ondersteuning bieden voor moderne verificatie en kunnen worden geconfigureerd voor Azure AD Multi-Factor Authentication per gebruiker.
* [Beveiligingsvragen:](concept-authentication-security-questions.md) alleen gebruikt voor SSPR
* [E-mailadres:](concept-sspr-howitworks.md#authentication-methods) alleen gebruikt voor SSPR

## <a name="next-steps"></a>Volgende stappen

Zie de [zelfstudie voor SSPR (self-service voor wachtwoordherstel)][tutorial-sspr] en [Azure AD Multi-Factor Authentication][tutorial-azure-mfa] om aan de slag te gaan.

Zie How [Azure AD self-service password reset works][concept-sspr](Hoe self-service voor wachtwoord opnieuw instellen in Azure AD werkt) voor meer informatie over SSPR-concepten.

Zie Hoe [Azure AD Multi-Factor Authentication][concept-mfa]werkt voor meer informatie over MFA-concepten.

Meer informatie over het configureren van verificatiemethoden met behulp [van Microsoft Graph REST API](/graph/api/resources/authenticationmethods-overview).

Zie Azure [AD Multi-Factor Authentication authentication method analysis with PowerShell (Azure AD Multi-Factor Authentication-verificatiemethodeanalyse met PowerShell)](/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/)als u wilt controleren welke verificatiemethoden in gebruik zijn.

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
