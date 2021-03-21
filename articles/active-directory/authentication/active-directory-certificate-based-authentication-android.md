---
title: Verificatie op basis van Android-certificaten-Azure Active Directory
description: Meer informatie over de ondersteunde scenario's en de vereisten voor het configureren van verificatie op basis van certificaten in oplossingen met Android-apparaten
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1001d5524fe99783cda4d5b77bdaceacc6791848
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96861370"
---
# <a name="azure-active-directory-certificate-based-authentication-on-android"></a>Verificatie op basis van certificaten op Android Azure Active Directory

Android-apparaten kunnen authenticatie op basis van certificaten (dit) gebruiken voor de verificatie van Azure Active Directory met behulp van een client certificaat op hun apparaat bij het maken van verbinding met:

* Mobiele Office-toepassingen, zoals micro soft Outlook en micro soft Word
* Exchange ActiveSync-clients (EAS)

Als u deze functie configureert, hoeft u geen combi natie van gebruikers naam en wacht woord op te geven in bepaalde mail-en Microsoft Office toepassingen op uw mobiele apparaat.

In dit onderwerp vindt u de vereisten en de ondersteunde scenario's voor het configureren van dit op een Android-apparaat voor gebruikers van tenants in Office 365 Enter prise, Business, education, Amerikaanse overheid, China en Duitsland plannen.

Deze functie is beschikbaar als preview-versie van Office 365 Amerikaanse overheids instellingen en federale plannen.

## <a name="microsoft-mobile-applications-support"></a>Ondersteuning voor micro soft Mobile Applications

| Apps | Ondersteuning |
| --- | --- |
| App Azure Information Protection |![Vinkje voor ondersteuning voor deze toepassing][1] |
| Intune Bedrijfsportal |![Vinkje voor ondersteuning voor deze toepassing][1] |
| Microsoft Teams |![Vinkje voor ondersteuning voor deze toepassing][1] |
| OneNote |![Vinkje voor ondersteuning voor deze toepassing][1] |
| OneDrive |![Vinkje voor ondersteuning voor deze toepassing][1] |
| Outlook |![Vinkje voor ondersteuning voor deze toepassing][1] |
| Power BI |![Vinkje voor ondersteuning voor deze toepassing][1] |
| Skype voor Bedrijven |![Vinkje voor ondersteuning voor deze toepassing][1] |
| Word/Excel/Power Point |![Vinkje voor ondersteuning voor deze toepassing][1] |
| Yammer |![Vinkje voor ondersteuning voor deze toepassing][1] |

### <a name="implementation-requirements"></a>Implementatie vereisten

De versie van het besturings systeem van het apparaat moet Android 5,0 (Lollipop) en hoger zijn.

Er moet een Federatie server worden geconfigureerd.

Als Azure Active Directory een client certificaat wilt intrekken, moet het ADFS-token de volgende claims hebben:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` (Het serie nummer van het client certificaat)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` (De teken reeks voor de verlener van het client certificaat)

Azure Active Directory voegt deze claims toe aan het vernieuwings token als deze beschikbaar zijn in het ADFS-token (of een ander SAML-token). Wanneer het vernieuwings token moet worden gevalideerd, wordt deze informatie gebruikt om het intrekken te controleren.

Als best practice moet u de ADFS-fout pagina's van uw organisatie bijwerken met de volgende gegevens:

* De vereiste voor het installeren van de Microsoft Authenticator op Android.
* Instructies voor het ophalen van een gebruikers certificaat.

Zie [de AD FS-aanmeldings pagina's aanpassen](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn280950(v=ws.11))voor meer informatie.

Sommige Office-apps (waarvoor moderne verificatie is ingeschakeld) verzenden '*prompt = login*' naar Azure AD in de aanvraag. Standaard vertaalt Azure AD '*prompt = login*' in de aanvraag bij ADFS als '*wauth = usernamepassworduri*' (vraagt ADFS om u/P-verificatie te doen) en '*wfresh = 0*' (vraagt ADFS de SSO-status te negeren en een nieuwe verificatie uit te voeren). Als u verificatie op basis van certificaten voor deze apps wilt inschakelen, moet u het standaard gedrag van Azure AD wijzigen. Stel de *PromptLoginBehavior* in uw federatieve domein instellingen in op *uitgeschakeld*.
U kunt de [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings) -cmdlet gebruiken om deze taak uit te voeren:

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`

## <a name="exchange-activesync-clients-support"></a>Ondersteuning voor Exchange ActiveSync-clients

Bepaalde Exchange ActiveSync-toepassingen op Android 5,0 (Lollipop) of hoger worden ondersteund. Neem contact op met de ontwikkelaar van de toepassing om te bepalen of uw e-mail toepassing ondersteuning biedt voor deze functie.

## <a name="next-steps"></a>Volgende stappen

Als u verificatie op basis van certificaten in uw omgeving wilt configureren, raadpleegt u aan de [slag met verificatie op basis van certificaten op Android](active-directory-certificate-based-authentication-get-started.md) voor instructies.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-android/ic195031.png
