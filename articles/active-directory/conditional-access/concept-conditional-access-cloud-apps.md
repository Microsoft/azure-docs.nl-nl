---
title: Cloud-apps of-acties in het beleid voor voorwaardelijke toegang-Azure Active Directory
description: Wat zijn Cloud-apps of acties in een beleid voor voorwaardelijke toegang van Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2895588a5a82ec2b6c69d33ff6cea39bbe3a0372
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103491993"
---
# <a name="conditional-access-cloud-apps-or-actions"></a>Voorwaardelijke toegang: Cloud-apps of-acties

Cloud-apps of-acties zijn een belang rijk signaal in een beleid voor voorwaardelijke toegang. Met beleid voor voorwaardelijke toegang kunnen beheerders besturings elementen aan specifieke toepassingen of acties toewijzen.

- Beheerders kunnen kiezen uit de lijst met toepassingen met ingebouwde micro soft-toepassingen en [Azure AD Integrated-toepassingen](../manage-apps/what-is-application-management.md) , waaronder galerie, niet-galerie en toepassingen die zijn gepubliceerd via [toepassings proxy](../manage-apps/what-is-application-proxy.md).
- Beheerders kunnen ervoor kiezen om beleid te definiëren dat niet is gebaseerd op een Cloud toepassing maar bij een gebruikers actie. De enige ondersteunde actie is het registreren van beveiligings gegevens (preview), waardoor voorwaardelijke toegang kan leiden tot het afdwingen van besturings elementen voor de [registratie van gecombineerde beveiligings gegevens](../authentication/howto-registration-mfa-sspr-combined.md).

![Beleid voor voorwaardelijke toegang definiëren en Cloud-apps opgeven](./media/concept-conditional-access-cloud-apps/conditional-access-cloud-apps-or-actions.png)

## <a name="microsoft-cloud-applications"></a>Micro soft-Cloud toepassingen

Veel van de bestaande micro soft-Cloud toepassingen zijn opgenomen in de lijst met toepassingen waaruit u kunt kiezen. 

Beheerders kunnen een beleid voor voorwaardelijke toegang toewijzen aan de volgende Cloud-apps van micro soft. Sommige apps, zoals Office 365 en Microsoft Azure beheer, bevatten meerdere verwante onderliggende apps of services. De volgende lijst is niet limitatief en kan worden gewijzigd.

- [Office 365](#office-365)
- Azure Analysis Services
- Azure DevOps
- [Azure SQL Database en Azure Synapse Analytics](../../azure-sql/database/conditional-access-configure.md)
- Dynamics CRM Online
- Micro soft Application Insights Analytics
- [Microsoft Azure Information Protection](/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- [Microsoft Azure-beheer](#microsoft-azure-management)
- Abonnements beheer Microsoft Azure
- Microsoft Cloud App Security
- Micro soft commerce tools Access Control-Portal
- Micro soft commerce tools-verificatie service
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- [Inschrijving Microsoft Intune](/intune/enrollment/multi-factor-authentication)
- Microsoft Planner
- Microsoft PowerApps
- Micro soft Search in Bing
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Exchange Online
- SharePoint
- Yammer
- Office Delve
- Office Sway
- Outlook-groepen
- Power BI Service
- Project Online
- Skype voor Bedrijven Online
- Virtueel particulier netwerk (VPN)
- Windows Defender ATP

### <a name="office-365"></a>Office 365

Microsoft 365 biedt productiviteits-en samenwerkings Services op basis van de Cloud, zoals Exchange, share point en micro soft teams. Microsoft 365 Cloud Services zijn nauw geïntegreerd om te zorgen voor soepele en samenwerkings ervaring. Deze integratie kan leiden tot Verwar ring bij het maken van beleid als sommige apps, zoals micro soft-teams, afhankelijk zijn van andere onderdelen, zoals share point of Exchange.

Met de Office 365-app kunnen deze services in één keer worden gericht. We raden u aan de nieuwe Office 365-app te gebruiken, in plaats van afzonderlijke Cloud-apps te richten om problemen met [service afhankelijkheden](service-dependencies.md)te voor komen. Gericht op deze groep toepassingen helpt problemen te voor komen die zich kunnen voordoen als gevolg van inconsistent beleid en afhankelijkheden.

Beheerders kunnen ervoor kiezen om specifieke apps uit te sluiten van het beleid als ze willen door de Office 365-app op te nemen en de specifieke apps van hun keuze in het beleid uit te sluiten.

Belangrijkste toepassingen die zijn opgenomen in de Office 365-client-app:

   - Microsoft Flow
   - Microsoft Forms
   - Microsoft Stream
   - Microsoft To-Do
   - Microsoft Teams
   - Exchange Online
   - SharePoint Online
   - Microsoft 365 Search Service
   - Yammer
   - Office Delve
   - Office Online
   - Office.com
   - OneDrive
   - PowerApps
   - Skype voor Bedrijven Online
   - Sway

### <a name="microsoft-azure-management"></a>Microsoft Azure-beheer

De Microsoft Azure-beheer toepassing bevat meerdere onderliggende services. 

   - Azure Portal
   - Azure Resource Manager provider
   - Klassieke implementatie model-Api's
   - Azure PowerShell
   - Azure CLI
   - Beheerders portal voor Visual Studio-abonnementen
   - Azure DevOps
   - Azure Data Factory Portal

> [!NOTE]
> De Microsoft Azure-beheer toepassing is van toepassing op Azure PowerShell, die de Azure Resource Manager API aanroept. Het is niet van toepassing op Azure AD Power shell, die Microsoft Graph aanroept.

## <a name="other-applications"></a>Andere toepassingen

Naast de micro soft-apps kunnen beheerders een door Azure AD geregistreerde toepassing toevoegen aan beleid voor voorwaardelijke toegang. Deze toepassingen kunnen bestaan uit: 

- Toepassingen die zijn gepubliceerd via [Azure AD-toepassingsproxy](../manage-apps/what-is-application-proxy.md)
- [Toepassingen die zijn toegevoegd vanuit de galerie](../manage-apps/add-application-portal.md)
- [Aangepaste toepassingen die niet voor komt in de galerie](../manage-apps/view-applications-portal.md)
- [Verouderde toepassingen die zijn gepubliceerd via app Delivery controllers en Networks](../manage-apps/secure-hybrid-access.md)
- Toepassingen die gebruikmaken [van op wacht woord gebaseerde eenmalige aanmelding](../manage-apps/configure-password-single-sign-on-non-gallery-applications.md)

> [!NOTE]
> Omdat met het beleid voor voorwaardelijke toegang de vereisten voor het openen van een service worden ingesteld, kunt u dit niet Toep assen op een client toepassing (Public/native). Andere woorden: het beleid wordt niet rechtstreeks ingesteld op een client toepassing (Public/native), maar wordt toegepast wanneer een client een service aanroept. Een beleid dat is ingesteld op share point service is bijvoorbeeld van toepassing op de clients die share point aanroept. Een beleid dat is ingesteld op Exchange, is van toepassing op de poging toegang te krijgen tot het e-mail bericht met de Outlook-client Dat is de reden waarom client-(open bare/systeem eigen) toepassingen niet beschikbaar zijn voor selectie in de optie Cloud-apps kiezen en voorwaardelijke toegang is niet beschikbaar in de toepassings instellingen voor de toepassing client (openbaar/systeem eigen) die is geregistreerd in uw Tenant. 

## <a name="user-actions"></a>Gebruikersacties

Gebruikers acties zijn taken die door een gebruiker kunnen worden uitgevoerd. Voorwaardelijke toegang ondersteunt momenteel twee gebruikers acties: 

- **Beveiligings gegevens registreren**: met deze gebruikers actie kan beleid voor voorwaardelijke toegang worden afgedwongen wanneer gebruikers die zijn ingeschakeld voor de gecombineerde registratie poging hun beveiligings gegevens te registreren. Meer informatie vindt u in het artikel, [registratie van gecombineerde beveiligings gegevens](../authentication/concept-registration-mfa-sspr-combined.md).

- **Apparaten registreren of toevoegen (preview)**: met deze gebruikers actie kunnen beheerders beleid voor voorwaardelijke toegang afdwingen wanneer gebruikers apparaten bij Azure AD [registreren](../devices/concept-azure-ad-register.md) of eraan [toevoegen](../devices/concept-azure-ad-join.md) . Er zijn twee belang rijke aandachtspunten met deze gebruikers actie: 
   - `Require multi-factor authentication` is het enige toegangs beheer dat beschikbaar is voor deze gebruikers actie en alle andere zijn uitgeschakeld. Deze beperking voor komt conflicten met toegangs controles die afhankelijk zijn van de registratie van Azure AD-apparaten of niet van toepassing zijn op Azure AD-apparaatregistratie. 
   - Wanneer een beleid voor voorwaardelijke toegang is ingeschakeld met deze gebruikers actie, moet u de apparaatinstellingen van **Azure Active Directory**  >  **apparaten** instellen  >    -  `Devices to be Azure AD joined or Azure AD registered require Multi-Factor Authentication` op **Nee**. Anders wordt het beleid voor voorwaardelijke toegang met deze gebruikers actie niet op de juiste wijze afgedwongen. Meer informatie over deze Apparaatinstellingen vindt [u in Apparaatinstellingen configureren](../device-management-azure-portal.md##configure-device-settings). Deze gebruikers actie biedt flexibiliteit om multi-factor Authentication te vereisen voor het registreren of samen voegen van apparaten voor specifieke gebruikers en groepen of voor waarden in plaats van een beleid op Tenant niveau te hebben in Apparaatinstellingen. 
   
## <a name="next-steps"></a>Volgende stappen

- [Voorwaardelijke toegang: voor waarden](concept-conditional-access-conditions.md)

- [Algemeen beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)
- [Client toepassings afhankelijkheden](service-dependencies.md)
