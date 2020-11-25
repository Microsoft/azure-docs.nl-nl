---
ms.openlocfilehash: c400856546142353a7294a03fce6bbff1c258cc0
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95554184"
---
In Azure Active Directory (Azure AD) verwijst de term **app-inrichting** naar het automatisch maken van gebruikersidentiteiten en -rollen in de cloud-toepassingen ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) waartoe gebruikers toegang nodig hebben. Naast het maken van gebruikersidentiteiten omvat automatische inrichting het onderhoud en de verwijdering van gebruikersidentiteiten zoals gewijzigde status of rollen. Veelvoorkomende scenario's zijn onder andere het inrichten van een Azure AD-gebruiker in toepassingen als [Dropbox](../articles/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial.md), [Sales Force](../articles/active-directory/saas-apps/salesforce-provisioning-tutorial.md), [ServiceNow](../articles/active-directory/saas-apps/servicenow-provisioning-tutorial.md) en meer.

![Overzichtsdiagram van inrichting](./media/active-directory-app-provisioning/provisioning-overview.png)

Met deze functie kunt u:

- **Inrichting** automatiseren: Automatisch nieuwe accounts maken in de juiste systemen voor nieuwe personen wanneer ze deelnemen aan uw team of organisatie.
- **Inrichting automatiseren:** Schakel accounts automatisch uit in de juiste systemen wanneer mensen het team of de organisatie verlaten.
- **Gegevens synchroniseren tussen systemen:** Zorg ervoor dat de identiteiten in uw toepassingen en systemen up-to-date blijven op basis van wijzigingen in de map of uw HR-systeem.
- **Groepen inrichten:** Richt groepen in voor toepassingen die deze ondersteunen.
- **Toegang beheren:** Bewaak en controleer wie is ingericht in uw toepassingen.
- **Naadloos implementeren in brownfield-scenario's:** Vergelijk bestaande identiteiten tussen systemen en sta eenvoudige integratie toe, zelfs wanneer gebruikers al bestaan in het doelsysteem.
- **Uitgebreide aanpassing gebruiken:** Profiteer van aanpasbare toewijzingen van kenmerken, waarmee wordt gedefinieerd welke gebruikersgegevens van het bronsysteem naar het doelsysteem moeten stromen.
- **Krijg waarschuwingen voor kritieke gebeurtenissen:** De inrichtingsservice geeft waarschuwingen voor kritieke gebeurtenissen en maakt integratie van Log Analytics mogelijk. Hiermee kunt u aangepaste waarschuwingen definiëren voor de behoeften van uw bedrijf.

## <a name="benefits-of-automatic-provisioning"></a>Voordelen van automatische inrichting

Naarmate het aantal toepassingen dat in moderne organisaties wordt gebruikt groeit, moeten IT-beheerders voor toegangsbeheer op schaal zorgen. Met standaarden zoals Security Assertion Markup Language (SAML) of Open ID Connect (OIDC) kunnen beheerders snel eenmalige aanmelding (SSO) instellen, maar om toegang te kunnen krijgen moeten gebruikers ook worden ingericht in de toepassing. Bij veel beheerders betekent het inrichten dat elke week handmatig elk gebruikersaccount wordt gemaakt of CSV-bestanden moeten worden geüpload, maar deze processen zijn tijdrovend, duur en foutgevoelig. Oplossingen zoals SAML just-in-time (JIT) worden gebruikt voor het automatiseren van het inrichten, maar ondernemingen hebben ook een oplossing nodig om de inrichting van gebruikers ongedaan te maken wanneer ze het bedrijf verlaten of niet langer toegang tot bepaalde apps nodig hebben vanwege een rolwijziging.

Enkele veelvoorkomende motivaties voor het gebruik van automatische inrichting zijn:

- Maximale efficiëntie en nauwkeurigheid van inrichtingsprocessen.
- Besparen op kosten die zijn gekoppeld aan het hosten en onderhouden van op maat gemaakte inrichtingsoplossingen en -scripts.
- Beveiliging van uw organisatie door de identiteit van gebruikers direct te verwijderen uit belangrijke SaaS-apps wanneer ze de organisatie verlaten.
- Eenvoudig een groot aantal gebruikers importeren in een bepaalde SaaS-toepassing of -systeem.
- Beschikking over één set beleidsregels om te bepalen wie er wordt ingericht en wie zich kan aanmelden bij een app.

Het inrichten van Azure AD-gebruikers kan helpen bij het oplossen van deze uitdagingen. Als u meer wilt weten over hoe klanten gebruikersinrichting van Azure AD gebruiken, kunt u de [Casestudy van ASOS lezen](https://aka.ms/asoscasestudy). In onderstaande video vindt u een overzicht van gebruikersinrichting in Azure AD:

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Welke toepassingen en systemen kan ik gebruiken met automatische gebruikersinrichting van Azure AD?

Azure AD bevat vooraf geïntegreerde ondersteuning voor veel populaire SaaS-toepassingen en HR-systemen en algemene ondersteuning voor apps die specifieke onderdelen van de [SCIM 2.0-standaard](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010) implementeren.

* **Vooraf geïntegreerde toepassingen (SaaS-toepassingen uit de galerie)** . U kunt alle toepassingen vinden waarvoor Azure AD een vooraf geïntegreerde inrichtingsconnector ondersteunt in de [lijst met zelfstudies voor het inrichten van gebruikers in toepassingen](../articles/active-directory/saas-apps/tutorial-list.md). De vooraf geïntegreerde toepassingen die in de galerie worden weergegeven, gebruiken doorgaans op SCIM 2.0 gebaseerde gebruikersbeheer-API’s voor het inrichten. 

   ![Logo van Salesforce](./media/active-directory-app-provisioning/gallery-app-logos.png)

   Als u een nieuwe toepassing wilt aanvragen voor inrichting, kunt u [een aanvraag indienen voor integratie van uw app met onze app-galerie](../articles/active-directory/develop/v2-howto-app-gallery-listing.md). Voor een aanvraag voor gebruikersinrichting moet de toepassing een SCIM-compatibel eindpunt hebben. Vraag de leverancier van de toepassing de SCIM-standaard te volgen zodat de app snel kan worden geïmplementeerd op ons platform.

* **Toepassingen die ondersteuning bieden voor SCIM 2.0**. Zie [Een SCIM-eindpunt bouwen en gebruikersinrichting configureren](../articles/active-directory/app-provisioning/use-scim-to-provision-users-and-groups.md) voor meer informatie over het algemeen verbinden van toepassingen die gebruikmaken van SCIM 2.0-API’s voor gebruikersbeheer.

## <a name="what-is-system-for-cross-domain-identity-management-scim"></a>Wat is System for Cross-domain Identity Management (SCIM)?

Om te helpen bij het automatiseren van de inrichting of het ongedaan maken van de inrichting, maken apps gebruik van eigen gebruikers-API’s en groeps-API’s. Iedereen die wel eens heeft geprobeerd om gebruikers te beheren in meer dan één app, zal u echter vertellen dat elke app probeert dezelfde eenvoudige acties uit te voeren, zoals het maken of bijwerken van gebruikers, het toevoegen van gebruikers aan groepen of het ongedaan maken van de inrichting van gebruikers. Al deze eenvoudige acties worden net iets anders geïmplementeerd, met behulp van verschillende eindpuntpaden, verschillende methoden voor het opgeven van gebruikersgegevens en een ander schema om elk informatie-element weer te geven.

Om deze uitdagingen aan te pakken, biedt de SCIM-specificatie een gemeenschappelijk gebruikersschema om gebruikers te helpen bij het verplaatsen van gebruikers in, uit of rondom toepassingen. SCIM wordt de standaard voor het inrichten en, wanneer het wordt gebruikt in combinatie met federatiestandaarden zoals SAML of OpenID Connect, biedt het beheerders een end-to-end, op standaarden gebaseerde oplossing voor toegangsbeheer.

Zie [Een SCIM-eindpunt bouwen en gebruikersinrichting configureren](../articles/active-directory/app-provisioning/use-scim-to-provision-users-and-groups.md) voor gedetailleerde richtlijnen over het ontwikkelen van een SCIM-eindpunt voor het automatiseren van het inrichten en het ongedaan maken van de inrichting van gebruikers en groepen in een toepassing. Voor vooraf geïntegreerde toepassingen in de galerie (Slack, Azure Databricks, Snowflake, enz.) kunt u de documentatie voor ontwikkelaars overslaan en de zelfstudies gebruiken die u [hier](../articles/active-directory/saas-apps/tutorial-list.md) kunt vinden.

## <a name="manual-vs-automatic-provisioning"></a>Handmatige en automatische inrichting

Toepassingen in de Azure AD-galerie ondersteunen een van de twee inrichtingsmodi:

* **Handmatige** inrichting houdt in dat er nog geen automatische Azure AD-inrichtingsconnector is voor de app. Gebruikersaccounts moeten handmatig worden gemaakt, bijvoorbeeld door gebruikers rechtstreeks toe te voegen aan de beheerportal van de app of een spreadsheet met gegevens van een gebruikersaccount te uploaden. Raadpleeg de documentatie van de app of neem contact op met de app-ontwikkelaar om te bepalen welke mechanismen beschikbaar zijn.

* **Automatisch** houdt in dat er al een Azure AD-inrichtingsconnector is ontwikkeld voor deze toepassing. U moet de zelfstudie voor installatie volgen om het inrichten voor de toepassing in te stellen. App-zelfstudies vindt u in de [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](../articles/active-directory/saas-apps/tutorial-list.md).

In de Azure AD-galerie worden toepassingen die ondersteuning bieden voor automatische inrichting aangeduid met een **Inrichting**-pictogram. Schakel over naar de nieuwe preview-ervaring van de galerie om deze pictogrammen weer te geven (in de banner bovenaan de pagina **Een toepassingspagina toevoegen** selecteert u de koppeling met **Klik hier om de nieuwe en verbeterde app-galerie uit te proberen**).

![Inrichting-pictogram in de toepassingsgalerie](./media/active-directory-app-provisioning/browse-gallery.png)

De inrichtingsmodus die door een toepassing wordt ondersteund, wordt ook weergegeven op het tabblad **Inrichting** zodra u de toepassing hebt toegevoegd aan uw **Enterprise-apps**.

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Hoe kan ik automatische inrichting instellen voor een toepassing?

Voor vooraf geïntegreerde toepassingen die in de galerie worden vermeld, zijn stapsgewijze instructies beschikbaar voor het instellen van automatische inrichting. Zie de [lijst met zelfstudies voor geïntegreerde galerie-apps](../articles/active-directory/saas-apps/tutorial-list.md). De volgende video laat zien hoe u automatische gebruikersinrichting instelt voor SalesForce.

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

Voor andere toepassingen die SCIM 2.0 ondersteunen, volgt u de stappen in het artikel [Een SCIM-eindpunt bouwen en gebruikersinrichting configureren](../articles/active-directory/app-provisioning/use-scim-to-provision-users-and-groups.md).