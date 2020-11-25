---
title: Besturings elementen toekennen in het beleid voor voorwaardelijke toegang-Azure Active Directory
description: Wat zijn besturings elementen verlenen in een beleid voor voorwaardelijke toegang van Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8915ae349e33390ce5bb687d7c07a21665a0258
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96029401"
---
# <a name="conditional-access-grant"></a>Voorwaardelijke toegang: verlenen

Binnen een beleid voor voorwaardelijke toegang kan een beheerder gebruik maken van toegangs beheer om toegang tot resources toe te kennen of te blok keren.

![Beleid voor voorwaardelijke toegang met een granting Control waarvoor multi-factor Authentication is vereist](./media/concept-conditional-access-grant/conditional-access-grant.png)

## <a name="block-access"></a>Toegang blokkeren

De blok kering houdt rekening met eventuele toewijzingen en voor komt toegang op basis van de configuratie van het beleid voor voorwaardelijke toegang.

Blok is een krachtig besturings element dat moet worden beheerd met de juiste kennis. Beleids regels met blok-instructies kunnen onbedoelde neven effecten hebben. Goed te testen en valideren zijn essentieel voordat u op schaal schakelt. Beheerders moeten hulpprogram ma's zoals de [modus alleen rapport-alleen voorwaardelijke toegang](concept-conditional-access-report-only.md) en [het What if-hulp programma in voorwaardelijke toegang](what-if-tool.md) gebruiken bij het aanbrengen van wijzigingen.

## <a name="grant-access"></a>Toegang verlenen

Beheerders kunnen ervoor kiezen om een of meer besturings elementen af te dwingen bij het verlenen van toegang. Deze besturings elementen bevatten de volgende opties: 

- [Multi-factor Authentication (Azure AD Multi-Factor Authentication) vereisen](../authentication/concept-mfa-howitworks.md)
- [Vereisen dat het apparaat wordt gemarkeerd als compatibel (Microsoft Intune)](/intune/protect/device-compliance-get-started)
- [Hybride Azure AD-aangesloten apparaat vereisen](../devices/concept-azure-ad-join-hybrid.md)
- [Goedgekeurde client-apps vereisen](app-based-conditional-access.md)
- [Beleid voor app-beveiliging vereisen](app-protection-based-conditional-access.md)
- [Wachtwoord wijziging vereisen](#require-password-change)

Wanneer beheerders ervoor kiezen deze opties te combi neren, kunnen ze de volgende methoden kiezen:

- Alle geselecteerde besturings elementen vereisen (besturings element **en** besturings element)
- Een van de geselecteerde besturings elementen vereisen (besturings element **of** besturings element)

Voor voorwaardelijke toegang is standaard alle geselecteerde besturings elementen vereist.

### <a name="require-multi-factor-authentication"></a>Multi-Factor Authentication vereisen

Als u dit selectie vakje inschakelt, moeten gebruikers Azure AD-Multi-Factor Authentication uitvoeren. Meer informatie over het implementeren van Azure AD-Multi-Factor Authentication vindt u in het artikel [planning van een Azure AD-multi-factor Authentication implementatie](../authentication/howto-mfa-getstarted.md)in de Cloud.

### <a name="require-device-to-be-marked-as-compliant"></a>Vereisen dat het apparaat moet worden gemarkeerd als compatibel

Organisaties die Microsoft Intune hebben geïmplementeerd, kunnen de informatie gebruiken die wordt geretourneerd door hun apparaten om apparaten te identificeren die aan specifieke nalevings vereisten voldoen. Deze informatie over beleids naleving wordt vanuit intune doorgestuurd naar Azure AD, waarbij voorwaardelijke toegang kan besluiten om toegang tot resources toe te kennen of te blok keren. Zie voor meer informatie over nalevings beleid het artikel [regels instellen op apparaten om toegang tot resources in uw organisatie in te stellen met intune](/intune/protect/device-compliance-get-started).

Een apparaat kan worden gemarkeerd als compatibel door intune (voor elk besturings systeem van een apparaat) of door een MDM-systeem van derden voor Windows 10-apparaten. Jamf Pro is het enige ondersteunde MDM-systeem van derden. Meer informatie over integratie vindt u in het artikel, [Integreer Jamf Pro met intune voor naleving](/intune/protect/conditional-access-integrate-jamf).

Apparaten moeten worden geregistreerd in azure AD voordat ze kunnen worden gemarkeerd als compatibel. Meer informatie over apparaatregistratie vindt u in het artikel, [wat een apparaat-id is](../devices/overview.md).

### <a name="require-hybrid-azure-ad-joined-device"></a>Hybride Azure AD-aangesloten apparaat vereisen

Organisaties kunnen ervoor kiezen om de apparaat-id te gebruiken als onderdeel van het beleid voor voorwaardelijke toegang. Organisaties kunnen vereisen dat apparaten hybride Azure AD zijn toegevoegd met dit selectie vakje. Zie het artikel [Wat is een apparaat-id?](../devices/overview.md)voor meer informatie over apparaat-id's.

Wanneer u de [code van de OAuth-stroom](../develop/v2-oauth2-device-code.md)van het apparaat gebruikt, wordt het beleid voor het toekennen van beheerde apparaten of de status van een apparaat niet ondersteund. Dit komt doordat het apparaat dat de verificatie uitvoert, de apparaats status niet kan verstrekken aan het apparaat dat een code levert en de apparaatstatus in het token is vergrendeld op het apparaat dat de verificatie uitvoert. Gebruik in plaats daarvan het besturings element multi-factor Authentication-toekenning vereisen.

### <a name="require-approved-client-app"></a>Goedgekeurde client-apps vereisen

Organisaties kunnen vereisen dat een toegangs poging tot de geselecteerde Cloud-apps moet worden uitgevoerd vanuit een goedgekeurde client-app. Deze goedgekeurde client-apps ondersteunen het [intune-beveiligings beleid voor apps](/intune/app-protection-policy) onafhankelijk van een MDM-oplossing (Mobile-Device Management).

Voor voorwaardelijke toegang moet het apparaat worden geregistreerd in Azure Active Directory waarvoor een Broker-app moet worden gebruikt om gebruik te kunnen maken van deze machtigings controle. De Broker-app kan de Microsoft Authenticator voor iOS of de Microsoft Authenticator of micro soft-bedrijfs portal voor Android-apparaten zijn. Als een Broker-app niet is geïnstalleerd op het apparaat wanneer de gebruiker zich probeert te verifiëren, wordt de gebruiker omgeleid naar de juiste App Store om de vereiste Broker-app te installeren.

Deze instelling is van toepassing op de volgende iOS-en Android-apps:

- Microsoft Azure Information Protection
- Microsoft Bookings
- Microsoft Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Power Automate
- Microsoft Invoicing
- Microsoft Kaizala
- Microsoft Launcher
- Microsoft Office
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft PowerApps
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype voor Bedrijven
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Microsoft To-Do
- Microsoft Visio
- Microsoft Word
- Microsoft Yammer
- Microsoft Whiteboard

**Opmerkingen**

- De goedgekeurde client-apps ondersteunen de functie intune-Mobile Application Management.
- De vereiste **goedgekeurde client-app vereisen** :
   - Biedt alleen ondersteuning voor de voor waarde iOS en Android voor Device platform.
   - Er is een Broker-app vereist om het apparaat te registreren. De Broker-app kan de Microsoft Authenticator voor iOS of de Microsoft Authenticator of micro soft-bedrijfs portal voor Android-apparaten zijn.
- Voorwaardelijke toegang kan niet worden beschouwd als micro soft Edge in de InPrivate-modus van een goedgekeurde client-app.

Zie het artikel [How to: goedgekeurde client-apps vereisen voor toegang tot Cloud app met voorwaardelijke toegang](app-based-conditional-access.md) voor configuratie voorbeelden.

### <a name="require-app-protection-policy"></a>Beleid voor app-beveiliging vereisen

In het beleid voor voorwaardelijke toegang kunt u vereisen dat er een [intune-app-beveiligings beleid](/intune/app-protection-policy) aanwezig is op de client-app voordat toegang beschikbaar is voor de geselecteerde Cloud-apps. 

Voor voorwaardelijke toegang moet het apparaat worden geregistreerd in Azure Active Directory waarvoor een Broker-app moet worden gebruikt om gebruik te kunnen maken van deze machtigings controle. De broker-app kan de Microsoft-Authenticator voor iOS of de Microsoft-bedrijfsportal voor Android-apparaten zijn. Als een Broker-app niet is geïnstalleerd op het apparaat wanneer de gebruiker zich probeert te verifiëren, wordt de gebruiker omgeleid naar de App Store om de Broker-app te installeren.

Deze instelling is van toepassing op de volgende client-apps:

- Microsoft Cortana
- Microsoft Edge
- Microsoft Excel
- Microsoft Office
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Word
- Meerdere lijnen voor intune
- 9 e-mail adres & agenda

> [!NOTE]
> Micro soft Kaizala, micro soft Skype voor bedrijven en micro soft Visio bieden geen ondersteuning voor de toekenning van het **beveiligings beleid voor apps vereisen** . Als u wilt dat deze apps werken, moet u alleen de toekenning **goedgekeurde apps vereisen** . Het gebruik van de or-component tussen de twee subsidies werkt niet voor deze drie toepassingen.

**Opmerkingen**

- Apps voor het app-beveiligings beleid ondersteunen de functie intune-Mobile Application Management met beleids beveiliging.
- De vereisten voor het **beveiligings beleid voor apps vereisen** :
    - Biedt alleen ondersteuning voor de voor waarde iOS en Android voor Device platform.
    - Er is een Broker-app vereist om het apparaat te registreren. Op iOS is de Broker-app Microsoft Authenticator en op Android, Intune-bedrijfsportal app.

Zie het artikel [procedure: app-beveiligings beleid en een goedgekeurde client-app vereisen voor toegang tot Cloud-apps met voorwaardelijke toegang](app-protection-based-conditional-access.md) voor configuratie voorbeelden.

### <a name="require-password-change"></a>Wachtwoord wijziging vereisen 

Wanneer gebruikers risico wordt gedetecteerd met behulp van de beleids voorwaarden voor gebruikers Risico's, kunnen beheerders ervoor kiezen de gebruiker het wacht woord veilig te wijzigen met behulp van de selfservice voor wachtwoord herstel van Azure AD. Als gebruikers risico wordt gedetecteerd, kunnen gebruikers een self-service voor het opnieuw instellen van een wacht woord op zichzelf herstellen, waarna de risico gebeurtenis voor gebruikers wordt gesloten om onnodig ruis voor beheerders te voor komen. 

Wanneer een gebruiker wordt gevraagd het wacht woord te wijzigen, moeten ze eerst multi-factor Authentication volt ooien. U wilt ervoor zorgen dat al uw gebruikers zijn geregistreerd voor multi-factor Authentication, zodat ze worden voor bereid als er een risico wordt gedetecteerd voor hun account.  

> [!WARNING]
> Gebruikers moeten eerder zijn geregistreerd voor selfservice voor wachtwoord herstel voordat ze het beleid voor gebruikers Risico's kunnen activeren. 

Er bestaat een aantal beperkingen wanneer u een beleid configureert met behulp van het besturings element voor wachtwoord wijziging.  

1. Het beleid moet worden toegewezen aan alle Cloud-apps. Zo voor komt u dat een kwaadwillende persoon een andere app gebruikt om het wacht woord van de gebruiker te wijzigen en het account risico opnieuw in te stellen door zich eenvoudigweg aan te melden bij een andere app. 
1. Vereisen dat wachtwoord wijziging niet kan worden gebruikt met andere besturings elementen, zoals het vereisen van een compatibel apparaat.  
1. Het besturings element voor wachtwoord wijziging kan alleen worden gebruikt met de voor waarde voor de gebruikers-en groeps toewijzing, de voor waarde voor de Cloud app-toewijzing (die moet worden ingesteld op alle) en de risico voorwaarden van de gebruiker. 

### <a name="terms-of-use"></a>Gebruiksvoorwaarden

Als uw organisatie gebruiks voorwaarden heeft gemaakt, kunnen er aanvullende opties worden weer gegeven onder besturings elementen verlenen. Met deze opties kunnen beheerders bevestigings voorwaarden vereisen als voor waarde voor het openen van de bronnen die door het beleid worden beveiligd. Meer informatie over de gebruiks voorwaarden vindt u in het artikel [Azure Active Directory gebruiks voorwaarden](terms-of-use.md).

## <a name="next-steps"></a>Volgende stappen

- [Voorwaardelijke toegang: sessie besturings elementen](concept-conditional-access-session.md)

- [Algemeen beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)

- [Modus Alleen rapporteren](concept-conditional-access-report-only.md)
