---
title: Verificatie op basis van certificaten op iOS-Azure Active Directory
description: Meer informatie over de ondersteunde scenario's en de vereisten voor het configureren van verificatie op basis van certificaten voor Azure Active Directory in oplossingen met iOS-apparaten
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5f9b96fe9ee0781803bbbd86316e8783b60a6f1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96861320"
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>Op certificaten gebaseerde verificatie op iOS Azure Active Directory

Om de beveiliging te verbeteren, kunnen iOS-apparaten authenticatie op basis van certificaten (dit) gebruiken om te verifiëren bij Azure Active Directory (Azure AD) met behulp van een client certificaat op hun apparaat wanneer er verbinding wordt gemaakt met de volgende toepassingen of services:

* Mobiele Office-toepassingen, zoals micro soft Outlook en micro soft Word
* Exchange ActiveSync-clients (EAS)

Door certificaten te gebruiken, hoeft u geen combi natie van gebruikers naam en wacht woord in te voeren in bepaalde mail-en Microsoft Office toepassingen op uw mobiele apparaat.

In dit artikel vindt u informatie over de vereisten en de ondersteunde scenario's voor het configureren van dit op een iOS-apparaat. DIT voor iOS is beschikbaar in open bare Clouds van Azure, micro soft Government Cloud, Microsoft Cloud Duitsland en Microsoft Azure China 21Vianet.

## <a name="microsoft-mobile-applications-support"></a>Ondersteuning voor micro soft Mobile Applications

| Apps | Ondersteuning |
| --- | --- |
| App Azure Information Protection |![Vinkje voor ondersteuning voor deze toepassing][1] |
| Intune Bedrijfsportal |![Vinkje voor ondersteuning voor deze toepassing][1] |
| Microsoft Teams |![Vinkje voor ondersteuning voor deze toepassing][1] |
| Office (mobiel) |![Vinkje voor ondersteuning voor deze toepassing][1] |
| OneNote |![Vinkje voor ondersteuning voor deze toepassing][1] |
| OneDrive |![Vinkje voor ondersteuning voor deze toepassing][1] |
| Outlook |![Vinkje voor ondersteuning voor deze toepassing][1] |
| Power BI |![Vinkje voor ondersteuning voor deze toepassing][1] |
| Skype voor Bedrijven |![Vinkje voor ondersteuning voor deze toepassing][1] |
| Word/Excel/Power Point |![Vinkje voor ondersteuning voor deze toepassing][1] |
| Yammer |![Vinkje voor ondersteuning voor deze toepassing][1] |

## <a name="requirements"></a>Vereisten

Voor het gebruik van dit met iOS gelden de volgende vereisten en overwegingen:

* De versie van het besturings systeem van het apparaat moet iOS 9 of hoger zijn.
* Microsoft Authenticator is vereist voor Office-toepassingen op iOS.
* Een identiteits voorkeur moet worden gemaakt in de macOS-sleutel hanger die de verificatie-URL van de ADFS-server bevat. Zie [een identiteits voorkeur maken in sleutel hanger toegang op Mac](https://support.apple.com/guide/keychain-access/create-an-identity-preference-kyca6343b6c9/mac)voor meer informatie.

De volgende Active Directory Federation Services (ADFS)-vereisten en overwegingen zijn van toepassing:

* De ADFS-server moet zijn ingeschakeld voor verificatie van certificaten en het gebruik van Federated Authentication.
* Het certificaat moet gebruikmaken van uitgebreid sleutel gebruik (EKU) en de UPN van de gebruiker in de *alternatieve naam voor het onderwerp (NT principal name)* bevatten.

## <a name="configure-adfs"></a>ADFS configureren

Als u een client certificaat wilt intrekken voor Azure AD, moet het ADFS-token de volgende claims hebben. Azure AD voegt deze claims toe aan het vernieuwings token als deze beschikbaar zijn in het ADFS-token (of een ander SAML-token). Wanneer het vernieuwings token moet worden gevalideerd, wordt deze informatie gebruikt om de intrekking te controleren:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` -het serie nummer van uw client certificaat toevoegen
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` -de teken reeks voor de uitgever van uw client certificaat toevoegen

Als best practice moet u ook de ADFS-fout pagina's van uw organisatie bijwerken met de volgende informatie:

* De vereiste voor het installeren van de Microsoft Authenticator op iOS.
* Instructies voor het ophalen van een gebruikers certificaat.

Zie [de AD FS-aanmeldings pagina aanpassen](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn280950(v=ws.11))voor meer informatie.

## <a name="use-modern-authentication-with-office-apps"></a>Moderne verificatie gebruiken met Office-apps

Sommige Office-apps waarvoor moderne verificatie is ingeschakeld, `prompt=login` worden in hun aanvraag verzonden naar Azure AD. Azure AD vertaalt standaard `prompt=login` de aanvraag naar ADFS als `wauth=usernamepassworduri` (vraagt ADFS om u/P-auth te maken) en `wfresh=0` (vraagt ADFS om SSO-status te negeren en een nieuwe verificatie uit te voeren). Als u verificatie op basis van certificaten voor deze apps wilt inschakelen, wijzigt u het standaard gedrag van Azure AD.

Als u het standaard gedrag wilt bijwerken, stelt u de *PromptLoginBehavior* in uw federatieve domein instellingen in op *uitgeschakeld*. U kunt de [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings) -cmdlet gebruiken om deze taak uit te voeren, zoals wordt weer gegeven in het volgende voor beeld:

```powershell
Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled
```

## <a name="support-for-exchange-activesync-clients"></a>Ondersteuning voor Exchange ActiveSync-clients

Op iOS 9 of hoger wordt de systeem eigen iOS-e-mailclient ondersteund. Neem contact op met de ontwikkelaar van de toepassing om te bepalen of deze functie wordt ondersteund voor alle andere Exchange ActiveSync-toepassingen.

## <a name="next-steps"></a>Volgende stappen

Zie aan de [slag met verificatie op basis van certificaten](active-directory-certificate-based-authentication-get-started.md) voor instructies voor het configureren van verificatie op basis van certificaten in uw omgeving.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png
