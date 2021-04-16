---
title: Besturingselementen verlenen in beleid voor voorwaardelijke toegang - Azure Active Directory
description: Wat zijn toekenningsbesturingselementen in een beleid voor voorwaardelijke toegang van Azure AD?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: bce54bb845e3085d654e3980123ef5c8a856fd98
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530194"
---
# <a name="conditional-access-grant"></a>Voorwaardelijke toegang: Verlenen

Binnen een beleid voor voorwaardelijke toegang kan een beheerder gebruikmaken van toegangsbesturingselementen om de toegang tot resources te verlenen of te blokkeren.

![Beleid voor voorwaardelijke toegang met een besturingselement voor toekenning waarvoor meervoudige verificatie is vereist](./media/concept-conditional-access-grant/conditional-access-grant.png)

## <a name="block-access"></a>Toegang blokkeren

Met Blokkeren wordt rekening gehouden met toewijzingen en wordt de toegang geblokkeerd op basis van de beleidsconfiguratie voor voorwaardelijke toegang.

Blokkeren is een krachtig besturingselement dat moet worden gebruikt met de juiste kennis. Beleid met blokin instructies kan onbedoelde neveneffecten hebben. De juiste tests en validatie zijn essentieel voordat deze op schaal worden inschakelen. Beheerders moeten bij het aanbrengen van wijzigingen [gebruikmaken](concept-conditional-access-report-only.md) van hulpprogramma's zoals de rapportmodus voor voorwaardelijke toegang en het hulpprogramma [What If voorwaardelijke](what-if-tool.md) toegang.

## <a name="grant-access"></a>Toegang verlenen

Beheerders kunnen ervoor kiezen om een of meer besturingselementen af te dwingen bij het verlenen van toegang. Deze besturingselementen omvatten de volgende opties: 

- [Meervoudige verificatie vereisen (Azure AD Multi-Factor Authentication)](../authentication/concept-mfa-howitworks.md)
- [Vereisen dat het apparaat wordt gemarkeerd als compatibel (Microsoft Intune)](/intune/protect/device-compliance-get-started)
- [Hybride Azure AD-gekoppeld apparaat vereisen](../devices/concept-azure-ad-join-hybrid.md)
- [Goedgekeurde client-apps vereisen](app-based-conditional-access.md)
- [Beleid voor app-beveiliging vereisen](app-protection-based-conditional-access.md)
- [Wachtwoordwijziging vereisen](#require-password-change)

Wanneer beheerders ervoor kiezen om deze opties te combineren, kunnen ze de volgende methoden kiezen:

- Alle geselecteerde besturingselementen vereisen **(besturingselement EN** besturingselement)
- Een van de geselecteerde besturingselementen vereisen **(besturingselement OF)**

Voor voorwaardelijke toegang zijn standaard alle geselecteerde besturingselementen vereist.

### <a name="require-multi-factor-authentication"></a>Multi-Factor Authentication vereisen

Als u dit selectievakje incheckt, moeten gebruikers Azure AD Multi-Factor Authentication uitvoeren. Meer informatie over het implementeren van Azure AD Multi-Factor Authentication vindt u in het artikel [Een cloudgebaseerde Azure AD Multi-Factor Authentication-implementatie plannen.](../authentication/howto-mfa-getstarted.md)

[Windows Hello voor Bedrijven](/windows/security/identity-protection/hello-for-business/hello-overview) voldoet aan de vereiste voor meervoudige verificatie in het beleid voor voorwaardelijke toegang. 

### <a name="require-device-to-be-marked-as-compliant"></a>Vereisen dat het apparaat moet worden gemarkeerd als compatibel

Organisaties die een apparaat hebben geïmplementeerd Microsoft Intune de informatie die van hun apparaten wordt geretourneerd, gebruiken om apparaten te identificeren die voldoen aan specifieke nalevingsvereisten. Deze informatie over naleving van het beleid wordt doorgestuurd van Intune naar Azure AD, waar voorwaardelijke toegang beslissingen kan nemen om toegang tot resources te verlenen of te blokkeren. Zie het artikel Set rules on devices to allow access to resources in your organization using Intune (Regels instellen op apparaten om toegang tot resources in uw organisatie toe te staan met [behulp van Intune) voor meer informatie over nalevingsbeleid.](/intune/protect/device-compliance-get-started)

Een apparaat kan worden gemarkeerd als compatibel door Intune (voor elk besturingssysteem van het apparaat) of door een MDM-systeem van derden voor Windows 10 apparaten. Jamf Pro is het enige ondersteunde MDM-systeem van derden. Meer informatie over integratie vindt u in het artikel [Jamf Pro integreren met Intune voor naleving.](/intune/protect/conditional-access-integrate-jamf)

Apparaten moeten worden geregistreerd in Azure AD voordat ze als compatibel kunnen worden gemarkeerd. Meer informatie over apparaatregistratie vindt u in het artikel [Wat is een apparaat-id?](../devices/overview.md).

### <a name="require-hybrid-azure-ad-joined-device"></a>Hybride Azure AD-gekoppeld apparaat vereisen

Organisaties kunnen ervoor kiezen om de apparaat-id te gebruiken als onderdeel van hun beleid voor voorwaardelijke toegang. Organisaties kunnen vereisen dat apparaten zijn samengevoegd met hybride Azure AD met behulp van dit selectievakje. Zie het artikel Wat is een apparaat-id? voor meer informatie over [apparaat-id's.](../devices/overview.md)

Wanneer u de [OAuth-stroom met apparaatcode](../develop/v2-oauth2-device-code.md)gebruikt, wordt het vereisen van beheer van beheerde apparaatverlening of een apparaattoestandvoorwaarde niet ondersteund. Dit komt doordat het apparaat dat verificatie voert, de apparaattoestand niet kan verstrekken aan het apparaat dat een code verstrekt en de apparaattoestand in het token is vergrendeld voor het apparaat dat verificatie voert. Gebruik in plaats daarvan Het verlenen van meervoudige verificatie vereisen.

### <a name="require-approved-client-app"></a>Goedgekeurde client-apps vereisen

Organisaties kunnen vereisen dat een toegangspoging tot de geselecteerde cloud-apps wordt gedaan vanuit een goedgekeurde client-app. Deze goedgekeurde client-apps ondersteunen [Intune-beveiligingsbeleid](/intune/app-protection-policy) voor apps, onafhankelijk van een MDM-oplossing (Mobile Device Management).

Om gebruik te kunnen maken van dit toekenningsbeheer, vereist voorwaardelijke toegang dat het apparaat wordt geregistreerd in Azure Active Directory waarvoor het gebruik van een broker-app is vereist. De broker-app kan de Microsoft Authenticator voor iOS zijn, of de Microsoft Authenticator of de Microsoft-bedrijfsportal voor Android-apparaten. Als er geen broker-app op het apparaat is geïnstalleerd wanneer de gebruiker probeert te verifiëren, wordt de gebruiker omgeleid naar de juiste App Store om de vereiste broker-app te installeren.

Deze instelling is van toepassing op de volgende iOS- en Android-apps:

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
- Microsoft 365 Admin

**Opmerkingen**

- De goedgekeurde client-apps ondersteunen de Intune Mobile Application Management-functie.
- De **vereiste Goedgekeurde client-app** vereisen:
   - Ondersteunt alleen de platformvoorwaarde voor iOS en Android voor apparaten.
   - Er is een broker-app vereist om het apparaat te registreren. De broker-app kan de Microsoft Authenticator voor iOS zijn, of de Microsoft Authenticator of de Microsoft-bedrijfsportal voor Android-apparaten.
- Voorwaardelijke toegang kan geen Microsoft Edge in de InPrivate-modus een goedgekeurde client-app.
- Het gebruik van Azure AD toepassingsproxy om de mobiele Power BI-app verbinding te laten maken met on-premises Power BI Report Server wordt niet ondersteund met beleid voor voorwaardelijke toegang waarvoor de Microsoft Power BI-app als goedgekeurde client-app is vereist.

Zie het artikel How to: Require approved client apps for cloud app access with Conditional Access (Goedgekeurde client-apps vereisen voor toegang tot [cloud-apps met voorwaardelijke](app-based-conditional-access.md) toegang) voor configuratievoorbeelden.

### <a name="require-app-protection-policy"></a>Beleid voor app-beveiliging vereisen

In uw beleid voor voorwaardelijke toegang kunt u vereisen dat [er een Intune-beveiligingsbeleid](/intune/app-protection-policy) voor apps aanwezig is op de client-app voordat toegang beschikbaar is voor de geselecteerde cloud-apps. 

Om gebruik te kunnen maken van dit toekenningsbeheer, vereist voorwaardelijke toegang dat het apparaat wordt geregistreerd in Azure Active Directory waarvoor het gebruik van een broker-app is vereist. De broker-app kan de Microsoft-Authenticator voor iOS of de Microsoft-bedrijfsportal voor Android-apparaten zijn. Als er geen broker-app op het apparaat is geïnstalleerd wanneer de gebruiker probeert te verifiëren, wordt de gebruiker omgeleid naar de App Store om de broker-app te installeren.

Toepassingen moeten de **Intune-SDK** met **Beleidsgarantie** implementeren en voldoen aan bepaalde andere vereisten om deze instelling te ondersteunen. Ontwikkelaars die toepassingen met de Intune SDK implementeren, kunnen meer informatie vinden in de SDK-documentatie over deze vereisten.

De volgende client-apps zijn bevestigd om deze instelling te ondersteunen:

- Microsoft Cortana
- Microsoft Edge
- Microsoft Excel
- Microsoft-lijsten (iOS)
- Microsoft Office
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Word
- MultiLine voor Intune
- Nine Mail - E-& Agenda

> [!NOTE]
> Microsoft Teams, Microsoft Kaizala, Microsoft Skype voor Bedrijven en Microsoft Visio bieden geen ondersteuning voor de toekenning van **het beveiligingsbeleid voor apps** vereisen. Als u wilt dat deze apps werken, gebruikt u de toekenning **Goedgekeurde apps** vereisen uitsluitend. Het gebruik van de or-component tussen de twee toekenningen werkt niet voor deze drie toepassingen.

**Opmerkingen**

- Apps voor app-beveiligingsbeleid ondersteunen de Intune Mobile Application Management-functie met beleidsbeveiliging.
- De **beleidsvereisten voor app-beveiliging** vereisen:
    - Ondersteunt alleen de platformvoorwaarde voor iOS en Android voor apparaten.
    - Er is een broker-app vereist om het apparaat te registreren. In iOS is de broker-app Microsoft Authenticator en op Android is Intune-bedrijfsportal app.

Zie het artikel How [to: Require app protection policy and an approved client app access with Conditional Access](app-protection-based-conditional-access.md) (App-beveiligingsbeleid en een goedgekeurde client-app vereisen voor toegang tot cloud-apps met voorwaardelijke toegang) voor configuratievoorbeelden.

### <a name="require-password-change"></a>Wachtwoordwijziging vereisen 

Wanneer er gebruikersrisico's worden gedetecteerd, kunnen beheerders met behulp van de beleidsvoorwaarden voor gebruikersrisico's ervoor kiezen om de gebruiker het wachtwoord veilig te laten wijzigen met behulp van de selfservice voor wachtwoord opnieuw instellen van Azure AD. Als er een gebruikersrisico wordt gedetecteerd, kunnen gebruikers self-service voor wachtwoord opnieuw instellen om zichzelf te herstellen. Hierdoor wordt de gebeurtenis voor gebruikersrisico's gesloten om onnodige ruis voor beheerders te voorkomen. 

Wanneer een gebruiker wordt gevraagd om het wachtwoord te wijzigen, moet deze eerst meervoudige verificatie voltooien. U moet ervoor zorgen dat al uw gebruikers zich hebben geregistreerd voor meervoudige verificatie, zodat ze worden voorbereid voor het geval er risico's voor hun account worden gedetecteerd.  

> [!WARNING]
> Gebruikers moeten zich eerder hebben geregistreerd voor selfservice voor wachtwoord opnieuw instellen voordat ze het beleid voor gebruikersrisico's activeren. 

Er gelden enkele beperkingen wanneer u een beleid configureert met behulp van het besturingselement voor wachtwoordwijziging.  

1. Het beleid moet worden toegewezen aan 'alle cloud-apps'. Dit voorkomt dat een aanvaller een andere app kan gebruiken om het wachtwoord van de gebruiker te wijzigen en accountrisico's opnieuw in te stellen door zich gewoon aan te melden bij een andere app. 
1. Wachtwoordwijziging vereisen kan niet worden gebruikt met andere besturingselementen, zoals het vereisen van een compatibel apparaat.  
1. Het besturingselement voor wachtwoordwijziging kan alleen worden gebruikt met de voorwaarde voor gebruikers- en groepstoewijzing, de toewijzingsvoorwaarde van de cloud-app (die op alle moet worden ingesteld) en de voorwaarden voor gebruikersrisico's. 

### <a name="terms-of-use"></a>Gebruiksvoorwaarden

Als uw organisatie gebruiksvoorwaarden heeft gemaakt, zijn er mogelijk extra opties zichtbaar onder besturingselementen voor toekenning. Met deze opties kunnen beheerders bevestiging van de gebruiksvoorwaarden vereisen als voorwaarde voor toegang tot de resources die door het beleid worden beveiligd. Meer informatie over gebruiksvoorwaarden vindt u in het artikel [Azure Active Directory gebruiksvoorwaarden.](terms-of-use.md)

## <a name="next-steps"></a>Volgende stappen

- [Voorwaardelijke toegang: Sessiebesturingselementen](concept-conditional-access-session.md)

- [Algemeen beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)

- [Modus Alleen rapporteren](concept-conditional-access-report-only.md)
