---
title: Vijf stappen voor het integreren van al uw apps met Azure AD
description: In deze hand leiding wordt uitgelegd hoe u al uw toepassingen integreert met Azure AD. In elke stap wordt de waarde uitgelegd en vindt u koppelingen naar bronnen waarin de technische details worden uitgelegd.
services: active-directory
author: knicholasa
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: nichola
ms.openlocfilehash: 7fd9e504448d55b4a2ef8c10b4ba1176cb2e3402
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96172632"
---
# <a name="five-steps-for-integrating-all-your-apps-with-azure-ad"></a>Vijf stappen voor het integreren van al uw apps met Azure AD

Azure Active Directory (Azure AD) is de micro soft cloud-gebaseerde service voor identiteits-en toegangs beheer. Azure AD biedt beveiligde authenticatie-en autorisatie oplossingen zodat klanten, partners en werk nemers toegang hebben tot de toepassingen die ze nodig hebben. Met Azure AD, [voorwaardelijke toegang](../conditional-access/overview.md), [multi-factor Authentication](../authentication/concept-mfa-howitworks.md), [eenmalige aanmelding](../hybrid/how-to-connect-sso.md)en [automatische gebruikers inrichting](../app-provisioning/user-provisioning.md) , kunt u eenvoudig en veilig identiteits-en toegangs beheer maken.

Als uw bedrijf een Microsoft 365-abonnement heeft, gebruikt u waarschijnlijk [al](/office365/enterprise/about-office-365-identity) Azure AD. Azure AD kan echter worden gebruikt voor al uw toepassingen en door [uw toepassings beheer te centraliseren](../manage-apps/common-scenarios.md) , kunt u dezelfde functies, hulpprogram ma's en beleids regels voor identiteits beheer gebruiken in uw volledige app-Port Folio. Dit biedt een uniforme oplossing waarmee de beveiliging wordt verbeterd, de kosten worden verminderd, de productiviteit wordt verhoogd en u ervoor kunt zorgen dat u ermee instemt. En u krijgt externe toegang tot on-premises apps.

In deze hand leiding wordt uitgelegd hoe u al uw toepassingen integreert met Azure AD. In elke stap wordt de waarde uitgelegd en vindt u koppelingen naar bronnen waarin de technische details worden uitgelegd. We presen teren deze stappen in een volg orde die wij raden. U kunt echter naar een deel van het proces springen om aan de slag te gaan met de meeste waarde voor u.

Andere bronnen in dit onderwerp, met inbegrip van diep gaande bedrijfs processen, die u kunt vinden in onze [bronnen voor het migreren van toepassingen naar Azure Active Directory](../manage-apps/migration-resources.md) -pagina.

## <a name="1-use-azure-ad-for-new-applications"></a>1. Azure AD gebruiken voor nieuwe toepassingen

Richt u eerst op nieuw verworven toepassingen. Als uw bedrijf begint met het gebruik van een nieuwe toepassing, voegt u deze direct toe [aan uw Azure AD-Tenant](../manage-apps/add-application-portal.md) . Stel een bedrijfs beleid in zodat het toevoegen van nieuwe apps aan Azure AD de standaard procedure in uw organisatie is. Dit is mini maal storend op bestaande bedrijfs processen en stelt u in staat om de waarde die u ontvangt van het integreren van apps te onderzoeken en te bewijzen zonder de manier te wijzigen waarop mensen nu zaken doen in uw omgeving.

Azure Active Directory (Azure AD) bevat een galerie met duizenden vooraf geïntegreerde toepassingen, zodat u snel aan de slag kunt gaan. U kunt [een galerie-app toevoegen aan uw Azure AD-organisatie](../manage-apps/add-application-portal.md) met stapsgewijze [zelf studies](../saas-apps/tutorial-list.md) voor de integratie met populaire apps, zoals:

- [ServiceNow](../saas-apps/servicenow-tutorial.md)
- [Workday](../saas-apps/workday-tutorial.md)
- [Salesforce](../saas-apps/salesforce-tutorial.md)
- [AWS](../saas-apps/amazon-web-service-tutorial.md)
- [Slack](../saas-apps/slack-tutorial.md)

Daarnaast kunt u [toepassingen integreren die zich niet in de galerie](../manage-apps/view-applications-portal.md)bevindt, inclusief alle toepassingen die al in uw organisatie bestaan, of een toepassing van derden die al deel uitmaakt van de Azure AD-galerie. Als dat niet het geval is, kunt u [uw app ook toevoegen aan de galerie](../develop/v2-howto-app-gallery-listing.md) .

Ten slotte kunt u ook de apps integreren die u zelf ontwikkelt. Dit wordt behandeld in stap 5 van deze hand leiding.

## <a name="2-determine-existing-application-usage-and-prioritize-work"></a>2. bepalen van het bestaande gebruik van de toepassing en prioriteiten werk

Vervolgens ontdekken we de werk nemers van de toepassingen die vaak gebruikmaken van en bepalen we uw werk om ze te integreren met Azure AD.

U kunt beginnen met de [hulpprogram ma's voor Cloud detectie](/cloud-app-security/tutorial-shadow-it) van Microsoft Cloud app Security om de schaduw kopie te detecteren en te beheren in uw netwerk (dat wil zeggen: apps die niet worden beheerd door de IT-afdeling). U kunt [micro soft Defender Advanced Threat Protection (ATP) gebruiken](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) om het detectie proces te vereenvoudigen en uit te breiden.

Daarnaast kunt u het [rapport AD FS toepassings activiteit](../manage-apps/migrate-adfs-application-activity.md) in de Azure Portal gebruiken om alle AD FS-apps in uw organisatie te detecteren, het aantal unieke gebruikers dat zich heeft aangemeld en de compatibiliteit voor het integreren met Azure AD.

Zodra u uw bestaande landschap hebt gevonden, moet u [een plan maken](../manage-apps/migration-resources.md) en prioriteiten toekennen aan de hoogste prioriteits-apps die u wilt integreren. Enkele voor beelden van vragen die u kunt stellen om dit proces te begeleiden zijn:

- Welke apps worden het meest gebruikt?
- Wat zijn de riskiest?
- Welke apps worden in de toekomst buiten gebruik gesteld, waardoor een verhuizing onnodig is?
- Welke apps moeten on-premises blijven en kunnen niet worden verplaatst naar de Cloud?

U ziet de grootste voor delen en kosten besparingen wanneer al uw apps zijn geïntegreerd en u niet meer vertrouwt op meerdere identiteits oplossingen. U ondervindt echter eenvoudiger identiteits beheer en verbeterde beveiliging wanneer u stapsgewijze naar dit doel verplaatst. U wilt deze tijd gebruiken om de prioriteit van uw werk te bepalen en te beslissen wat zinvol is voor uw situatie.

## <a name="3-integrate-apps-that-rely-on-other-identity-providers"></a>3. apps integreren die afhankelijk zijn van andere id-providers

Tijdens het detectie proces hebt u mogelijk toepassingen gevonden die niet worden bijgehouden door de IT-afdeling, waardoor uw gegevens en resources kwetsbaar blijven. Mogelijk hebt u ook toepassingen waarvoor alternatieve identiteits oplossingen worden gebruikt, waaronder Active Directory Federation Services (ADFS) of andere id-providers. Bedenk hoe u uw identiteits-en toegangs beheer kunt consolideren om geld te besparen en de beveiliging te verbeteren. Het aantal identiteits oplossingen dat u hebt, beperken:

- Bespaar geld door de nood zaak van on-premises gebruikers inrichten en verificatie te elimineren, evenals licentie kosten die zijn betaald aan andere Cloud-id-providers voor dezelfde service.
- Verminder de administratieve overhead en schakel betere beveiliging in met minder redundantie in uw proces voor identiteits-en toegangs beheer.
- Stel werk nemers in staat om toegang te krijgen tot alle toepassingen die ze nodig hebben via de [MyApps-Portal](../manage-apps/access-panel-collections.md).
- Verbeter de intelligentie van de services voor [identiteits beveiliging](../identity-protection/overview-identity-protection.md) van Azure AD, zoals voorwaardelijke toegang, door de hoeveelheid gegevens die wordt opgehaald uit uw app-gebruik te verhogen en de voor delen ervan uit te breiden naar de nieuwe toegevoegde apps.

We hebben hulp gepubliceerd voor het beheer van het bedrijfs proces van het integreren van apps met Azure AD, met inbegrip van een [poster](https://aka.ms/AppOnePager) en [presentatie](https://aka.ms/AppGuideline) die u kunt gebruiken om de eigen aren van zakelijke en toepassingen op de hoogte te stellen en geïnteresseerd te zijn. U kunt deze voor beelden met uw eigen huis stijl wijzigen en deze publiceren naar uw organisatie via uw bedrijfs portal, nieuws brief of ander medium wanneer u dit proces gaat volt ooien.

Een goed uitgangs punt om te beginnen is door het gebruik van Active Directory Federation Services (ADFS) te evalueren. Veel organisaties gebruiken ADFS voor verificatie met SaaS-apps, aangepaste line-of-Business-Apps en Microsoft 365-en Azure AD-apps:

![Diagram bevat on-premises apps, line-of-Business-Apps, SaaS-apps en, via Azure AD, Office 365 alle verbindingen met stippel lijnen in Active Directory en AD FS.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-1.png)

U kunt deze configuratie bijwerken door [ADFS te vervangen door Azure AD als centrum](../manage-apps/migrate-adfs-apps-to-azure.md) voor uw oplossing voor identiteits beheer. Dit maakt het mogelijk om u aan te melden voor elke app die uw werk nemers willen gebruiken en maakt het gemakkelijk voor werk nemers om alle zakelijke toepassingen te vinden die ze nodig hebben via de [MyApps-Portal](../user-help/my-apps-portal-end-user-access.md), naast de andere voor delen hierboven.

![Diagram toont on-premises apps via Active Directory en AD FS, line-of-Business-Apps, SaaS-apps en Office 365 alle verbindingen met stippel lijnen in Azure Active Directory.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-2.png)

Als Azure AD de centrale ID-provider wordt, kunt u mogelijk volledig overschakelen van ADFS, in plaats van een federatieve oplossing te gebruiken. Apps waarvoor ADFS AD FS eerder is gebruikt, kunnen nu alleen Azure AD gebruiken.

![Diagram bevat on-premises, line-of-Business-Apps, SaaS-apps en Office 365 alle verbindingen met stippel lijnen in Azure Active Directory. Active Directory en AD FS is niet aanwezig.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-3.png)

U kunt ook apps migreren die gebruikmaken van een andere Cloud provider op basis van een id naar Azure AD. Uw organisatie heeft mogelijk meerdere IAM-oplossingen (Identity Access Management). Migratie naar een Azure AD-infra structuur is een kans om de afhankelijkheden van IAM-licenties (on-premises of in de Cloud) en infrastructuur kosten te verminderen. In gevallen waarin u mogelijk al hebt betaald voor Azure AD via M365-licenties, is er geen reden om de toegevoegde kosten van een andere IAM-oplossing te betalen.

## <a name="4-integrate-on-premises-applications"></a>4. on-premises toepassingen integreren

Traditioneel blijven toepassingen beveiligd door alleen toegang toe te staan tijdens de verbinding met het bedrijfs netwerk. In een steeds meer verbonden wereld willen we echter toegang verlenen tot apps voor klanten, partners en/of werk nemers, ongeacht waar ze zich in de wereld bevinden. [Azure AD-toepassingsproxy](../manage-apps/what-is-application-proxy.md) (AppProxy) is een functie van Azure AD die uw bestaande on-premises apps verbindt met Azure AD en waarvoor u geen edge-servers of andere extra infra structuur nodig hebt.

![In een diagram wordt de toepassings proxy service in actie weer gegeven. Een gebruiker heeft toegang tot " https://sales.contoso.com " en hun aanvraag wordt omgeleid via " https://sales-contoso.msappproxy.net " in azure Active Directory naar het on-premises adres " http://sales "](./media/five-steps-to-full-application-integration-with-azure-ad\app-proxy.png)

U kunt [zelf studie gebruiken: Voeg een lokale toepassing toe voor externe toegang via toepassings proxy in azure Active Directory](../manage-apps/application-proxy-add-on-premises-application.md) om toepassings proxy in te scha kelen en een on-premises toepassing toe te voegen aan uw Azure AD-Tenant.

Daarnaast kunt u bezorgings controllers voor toepassingen integreren, zoals F5 BIG-IP APM of Zscaler private Access. Als u deze integreert met Azure AD, krijgt u het moderne verificatie-en identiteits beheer van Azure AD naast het verkeer beheer en de beveiligings functies van het partner product. Deze oplossing wordt aangeroepen om [hybride toegang te krijgen](../manage-apps/secure-hybrid-access.md). Als u vandaag nog een van de volgende services gebruikt, hebben we zelf studies die u stapsgewijs door lopen hoe ze kunnen worden geïntegreerd met Azure AD.

- [Akamai Enter prise Application Access (EAA)](../saas-apps/akamai-tutorial.md)
- [Citrix Application Delivery controller (ADC)](../saas-apps/citrix-netscaler-tutorial.md) (voorheen bekend als Citrix NetScaler)
- [F5 BIG-IP APM](../saas-apps/headerf5-tutorial.md)
- [Zscaler Private Access (ZPA)](../saas-apps/zscalerprivateaccess-tutorial.md)

## <a name="5-integrate-apps-your-developers-build"></a>5. apps integreren die uw ontwikkel aars bouwen

Voor apps die in uw bedrijf zijn gebouwd, kunnen uw ontwikkel aars het [micro soft-identiteits platform](../develop/index.yml) gebruiken voor het implementeren van verificatie en autorisatie. Toepassingen die zijn geïntegreerd met het platform, worden [geregistreerd met Azure AD](../develop/quickstart-register-app.md) en worden op dezelfde manier beheerd als andere apps in uw port Folio.

Ontwikkel aars kunnen het platform gebruiken voor apps die intern worden gebruikt en klant gerichte apps, en er zijn andere voor delen van het gebruik van het platform. [Micro soft-verificatie bibliotheken (MSAL)](../develop/msal-overview.md), die deel uitmaken van het platform, stelt ontwikkel aars in staat om moderne ervaringen in te scha kelen, zoals multi-factor Authentication en het gebruik van beveiligings sleutels voor toegang tot hun apps zonder dat ze zelf een implementatie nodig hebben. Daarnaast hebben apps die zijn geïntegreerd met het micro soft-identiteits platform toegang tot [Microsoft Graph](../develop/microsoft-graph-intro.md) -een uniform API-eind punt dat de Microsoft 365 gegevens bevat waarmee de patronen van productiviteit, identiteit en beveiliging in een organisatie worden beschreven. Ontwikkel aars kunnen deze informatie gebruiken om functies te implementeren die de productiviteit van uw gebruikers verg Roten. Bijvoorbeeld door de personen te identificeren die de gebruiker heeft gecommuniceerd met recent en deze te halen in de gebruikers interface van de app.

We hebben een [video serie](https://www.youtube.com/watch?v=zjezqZPPOfc&amp;list=PLLasX02E8BPBxGouWlJV-u-XZWOc2RkiX) met een uitgebreide inleiding tot het platform, evenals een [groot aantal code voorbeelden](../develop/sample-v2-code.md) in ondersteunde talen en platformen.

## <a name="next-steps"></a>Volgende stappen

- [Resources voor het migreren van toepassingen naar Azure Active Directory](../manage-apps/migration-resources.md)