---
title: Onderliggend beheer van Azure AD-Cloud voor on-premises workloads-Azure
description: In dit onderwerp wordt het beheer van de Cloud voor on-premises workloads beschreven.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5374dc2ef4c736535b218d8cd67521e83a24a40b
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94836458"
---
# <a name="how-azure-ad-delivers-cloud-governed-management-for-on-premises-workloads"></a>Hoe Azure AD onderhevige beheer van de Cloud biedt voor on-premises workloads

Azure Active Directory (Azure AD) is een uitgebreide IDaaS-oplossing (Identity as a Service) die wordt gebruikt door miljoenen organisaties die alle aspecten van identiteit, Toegangs beheer en beveiliging omvatten. Azure AD bevat meer dan een miljard gebruikers identiteiten en helpt gebruikers bij het aanmelden en veilig openen van beide:

* Externe resources, zoals Microsoft 365, de Azure Portal en duizenden andere SaaS-toepassingen (Software-as-a-Service).
* Interne bronnen, zoals toepassingen op het bedrijfs netwerk en het intranet van een organisatie, samen met alle Cloud toepassingen die zijn ontwikkeld door die organisatie.

Organisaties kunnen Azure AD gebruiken als ze ' pure Cloud ' of ' hybride ' implementatie zijn als ze on-premises workloads hebben. Een hybride implementatie van Azure AD kan deel uitmaken van een strategie voor een organisatie om de IT-activa naar de cloud te migreren of om bestaande on-premises infra structuur naast nieuwe Cloud Services te blijven integreren.

In het verleden hebben hybride organisaties Azure AD gezien als een uitbrei ding van hun bestaande on-premises infra structuur. In deze implementaties zijn het on-premises identiteits beheer, Windows Server Active Directory of andere interne Directory systemen, de controle punten en gebruikers en groepen worden gesynchroniseerd van deze systemen naar een Cloud Directory, zoals Azure AD. Zodra deze identiteiten zich in de cloud bevinden, kunnen ze beschikbaar worden gesteld aan Microsoft 365, Azure en andere toepassingen.

![Identiteitslevenscyclus](media/cloud-governed-management-for-on-premises//image1.png)

Naarmate organisaties meer van hun IT-infra structuur met hun toepassingen naar de Cloud verplaatsen, zoeken we vaak naar de verbeterde beveiliging en vereenvoudigde beheer mogelijkheden van Identity Management as a service. De door de Cloud geleverde IDaaS-functies in azure AD versnellen de overgang naar het beheer van de Cloud door de oplossingen en mogelijkheden te bieden waarmee organisaties hun identiteits beheer snel kunnen overnemen en verplaatsen van traditionele on-premises systemen naar Azure AD, terwijl de bestaande en nieuwe toepassingen blijven ondersteunen.

Dit artikel bevat een overzicht van de strategie van micro soft voor hybride IDaaS en beschrijft hoe organisaties Azure AD kunnen gebruiken voor hun bestaande toepassingen.

## <a name="the-azure-ad-approach-to-cloud-governed-identity-management"></a>De Azure AD-benadering van de Cloud Identity Management

Als organisaties overstappen op de Cloud, hebben ze een garantie dat ze over de volledige omgeving beschikken over een betere beveiliging en meer inzicht in activiteiten, ondersteund door Automation en proactieve inzichten. "Door de **Cloud bestuurd beheer**" beschrijft hoe organisaties hun gebruikers, toepassingen, groepen en apparaten vanuit de Cloud beheren en regelen.

In deze moderne wereld moeten organisaties op schaal effectief kunnen beheren vanwege de verspreiding van SaaS-toepassingen en de toenemende rol van samen werking en externe identiteiten. Het nieuwe risico landschap van de Cloud betekent dat een organisatie meer responsief moet zijn: een schadelijke actor die een Cloud gebruiker in gevaar brengt, kan invloed hebben op de Cloud-en on-premises toepassingen.

Met name moeten hybride organisaties taken kunnen delegeren en automatiseren, waardoor deze hand matig werd uitgevoerd. Om taken te automatiseren, moeten ze Api's en processen hebben die de levens cyclus van de verschillende identiteits bronnen (gebruikers, groepen, toepassingen, apparaten) indelen, zodat ze het dagelijkse beheer van deze resources kunnen delegeren aan meer personen buiten IT-mede werkers. Azure AD biedt een oplossing voor deze vereisten via beheer van gebruikers accounts en systeem eigen verificatie voor gebruikers zonder dat hiervoor een on-premises identiteits infrastructuur nodig is. Het is niet mogelijk om on-premises infra structuur te bouwen voor organisaties die nieuwe community's van gebruikers hebben, zoals zakelijke partners, die niet afkomstig zijn van hun on-premises Directory, maar waarvan het toegangs beheer essentieel is om zakelijke resultaten te bereiken.

Bovendien is het beheer niet voltooid zonder governance---en governance in deze nieuwe wereld is een geïntegreerd onderdeel van het identiteits systeem in plaats van een invoeg toepassing. Identiteits beheer biedt organisaties de mogelijkheid om de identiteits-en toegangs levenscyclus van werk nemers, zakelijke partners en leveranciers en services en toepassingen te beheren.

Door Identity governance te integreren, is het eenvoudiger om de organisatie in staat te stellen om over te stappen op het beheer van de Cloud, zodat het kan worden geschaald, nieuwe uitdagingen met gasten kan worden geadresseerd en er diepere inzichten en automatisering worden geboden dan klanten met een on-premises infra structuur. Governance in deze nieuwe wereld betekent dat een organisatie transparantie, zicht baarheid en goede controles kan hebben op de toegang tot resources binnen de organisatie. Met Azure AD hebben beveiligings-en controle teams inzicht in wie heeft---en wie toegang moet hebben tot de resources in de organisatie (op welke apparaten), wat gebruikers met die toegang doen en of de organisatie de juiste besturings elementen heeft en gebruikt om de toegang te verwijderen of te beperken in overeenstemming met het bedrijfs-of regelgevend beleid.

Het nieuwe beheer model maakt organisaties met zowel SaaS-als LOB-toepassingen (line-of-Business), omdat ze eenvoudiger toegang tot deze toepassingen kunnen beheren en beveiligen. Door toepassingen met Azure AD te integreren, kunnen organisaties de toegang op consistente wijze in zowel Cloud-als on-premises identiteiten gebruiken en beheren. Het beheer van toepassings levenscyclus wordt geautomatiseerd en Azure AD biedt uitgebreide inzichten in het gebruik van toepassingen die niet eenvoudig kunnen worden behaald in on-premises identiteits beheer. Met de functies van Azure AD, Microsoft 365 groups en teams self-service kunnen organisaties eenvoudig groepen maken voor toegangs beheer en samen werking en gebruikers toevoegen aan of verwijderen uit de cloud om vereisten voor samen werking en toegangs beheer in te scha kelen.

Het selecteren van de juiste Azure AD-mogelijkheden voor het beheer van de Cloud is afhankelijk van de toepassingen die moeten worden gebruikt en hoe deze toepassingen worden geïntegreerd met Azure AD. In de volgende secties vindt u een overzicht van de benaderingen voor AD-geïntegreerde toepassingen en toepassingen die gebruikmaken van Federation-protocollen (bijvoorbeeld SAML, OAuth of OpenID Connect Connect).

## <a name="cloud-governed-management-for-ad-integrated-applications"></a>Onderliggend beheer in de Cloud voor AD-geïntegreerde toepassingen

Azure AD verbetert het beheer van on-premises Active Directory-geïntegreerde toepassingen van een organisatie via beveiligde externe toegang en voorwaardelijke toegang tot deze toepassingen. Daarnaast biedt Azure AD ook account levenscyclus beheer en referentie beheer voor de bestaande AD-accounts van de gebruiker, waaronder:

* **Veilige externe toegang en voorwaardelijke toegang voor on-premises toepassingen**

Voor veel organisaties is de eerste stap bij het beheren van de toegang vanuit de Cloud voor on-premises AD-geïntegreerde web-en extern bureau blad-toepassingen het implementeren van de [toepassings proxy](../manage-apps/application-proxy.md) voor deze toepassingen om externe toegang te bieden.

Na een eenmalige aanmelding bij Azure AD, hebben gebruikers toegang tot zowel Cloud-als on-premises toepassingen via een externe URL of een interne toepassings Portal. Toepassings proxy biedt bijvoorbeeld externe toegang en eenmalige aanmelding voor Extern bureaublad, share point en apps zoals tableau en Qlik, en LOB-toepassingen (line-of-Business). Daarnaast kan het beleid voor voorwaardelijke toegang bestaan uit het weer geven [van de gebruiks voorwaarden](../conditional-access/terms-of-use.md) en [ervoor zorgen dat de gebruiker ermee akkoord](../conditional-access/require-tou.md) gaat voordat ze toegang kunnen krijgen tot een toepassing.

![Architectuur van de app-proxy](media/cloud-governed-management-for-on-premises/image2.png)

* **Automatische levenscyclus beheer voor Active Directory accounts**

Identiteits beheer helpt organisaties bij het verkrijgen van een evenwicht tussen *productiviteit* ---hoe snel iemand toegang kan krijgen tot de resources die ze nodig hebben, bijvoorbeeld wanneer ze lid worden van de organisatie? ---en *beveiliging* ---hoe de toegang tot de tijd kan worden gewijzigd, bijvoorbeeld wanneer de dienstverband status van die persoon verandert? Het beheer van identiteits levenscyclus is de basis voor Identity governance, en effectief beheer op schaal vereist een modernisering van de infra structuur voor Identity Lifecycle Management voor toepassingen.

Voor veel organisaties is de identiteits levenscyclus voor werk nemers gekoppeld aan de weer gave van die gebruiker in een HCM-systeem (Human Capital Management). Voor organisaties die workday gebruiken als hun HCM systeem, kan Azure AD ervoor zorgen dat gebruikers accounts in AD [automatisch worden ingericht en van de inrichting van werk nemers in workday](../saas-apps/workday-inbound-tutorial.md)worden ongedaan gemaakt. Dit leidt ertoe dat de gebruikers productiviteit wordt verbeterd via Automation van Birthright-accounts en het risico kan worden beheerd door ervoor te zorgen dat de toegang van toepassingen automatisch wordt bijgewerkt wanneer een gebruiker de rollen wijzigt of de organisatie verlaat. Het werk [plan](https://aka.ms/WorkdayDeploymentPlan) voor het inrichten van gebruikers met werk dagen is een stapsgewijze hand leiding waarmee organisaties door de implementatie van de best practices van workday worden geleid naar Active Directory oplossing voor het inrichten van gebruikers in een proces van vijf stappen.

Azure AD Premium omvat ook Microsoft Identity Manager, waarmee u records kunt importeren uit andere on-premises HCM-systemen, waaronder SAP, Oracle eBusiness en Oracle People Soft.

Bij samenwerkings verbanden in bedrijfs-to-Business wordt steeds toegang verleend aan personen buiten uw organisatie. Met [Azure AD B2B](/azure/active-directory/b2b/) -samen werking kunnen organisaties hun toepassingen en services veilig delen met gast gebruikers en externe partners, terwijl ze de controle over hun eigen bedrijfs gegevens behouden.

Azure AD kan [automatisch accounts maken in AD voor gast gebruikers](../external-identities/hybrid-cloud-to-on-premises.md) als dat nodig is, waardoor zakelijke gasten toegang krijgen tot on-premises AD-geïntegreerde toepassingen zonder dat er nog een wacht woord nodig is. Organisaties kunnen [multi-factor Authentication-beleid (MFA) voor gast gebruikers](../external-identities/conditional-access.md)instellen, zodat er MFA-controles worden uitgevoerd tijdens de verificatie van de toepassings proxy. Alle [toegangs beoordelingen](../governance/manage-guest-access-with-access-reviews.md) die worden uitgevoerd op gebruikers van de Cloud, zijn ook van toepassing op on-premises gebruikers. Als de Cloud gebruiker bijvoorbeeld wordt verwijderd via het levenscyclus beheer beleid, wordt de lokale gebruiker ook verwijderd.

**Referentie beheer voor Active Directory accounts** Met de self-service voor wachtwoord herstel van Azure AD kunnen gebruikers die hun wacht woord hebben verg eten opnieuw worden geverifieerd en hun wacht woord opnieuw instellen, met de gewijzigde wacht woorden die zijn [geschreven naar on-premises Active Directory](../authentication/concept-sspr-writeback.md). Het proces voor het opnieuw instellen van het wacht woord kan ook gebruikmaken van de on-premises Active Directory wachtwoord beleid: wanneer een gebruiker het wacht woord opnieuw instelt, wordt gecontroleerd of het voldoet aan het beleid voor on-premises Active Directory voordat het wordt doorgevoerd in die map. De self-service voor het [opnieuw instellen van](../authentication/howto-sspr-deployment.md) het wacht woord voor wachtwoord herstel geeft een overzicht van de aanbevolen procedures voor het implementeren van selfservice voor wachtwoord herstel voor gebruikers via web-en Windows-geïntegreerde ervaringen.

![Azure AD SSPR-architectuur](media/cloud-governed-management-for-on-premises/image3.png)

Ten slotte, voor organisaties die gebruikers in staat stellen hun wacht woord te wijzigen, kan AD worden geconfigureerd voor gebruik van hetzelfde wachtwoord beleid als voor de organisatie in azure AD via de [Azure AD-functie voor wachtwoord beveiliging](../authentication/concept-password-ban-bad-on-premises.md), die momenteel beschikbaar is als open bare preview.

Wanneer een organisatie klaar is om een AD-geïntegreerde toepassing naar de cloud te verplaatsen door het besturings systeem dat als host fungeert voor de toepassing te verplaatsen naar Azure, biedt [Azure AD Domain Services](../../active-directory-domain-services/overview.md) AD-compatibele domein Services (zoals domein deelname, groeps beleid, LDAP en Kerberos/NTLM-verificatie). Azure AD Domain Services integreert met de bestaande Azure AD-Tenant van de organisatie, zodat gebruikers zich kunnen aanmelden met hun bedrijfs referenties. Daarnaast kunnen bestaande groepen en gebruikers accounts worden gebruikt voor het beveiligen van de toegang tot resources, zodat de on-premises resources van een soepeler kunnen worden verplaatst naar Azure-infrastructuur services.

![Azure AD Domain Services](media/cloud-governed-management-for-on-premises/image4.png)

## <a name="cloud-governed-management-for-on-premises-federation-based-applications"></a>Beheer van de Cloud voor on-premises Federatie toepassingen

Voor een organisatie die al gebruikmaakt van een on-premises ID-provider, kunt u met het verplaatsen van toepassingen naar Azure AD meer beveiligde toegang en een eenvoudiger beheer ervaring voor Federatie beheer. Met Azure AD kunt u gedetailleerdere toegangs controles per toepassing, waaronder Azure AD Multi-Factor Authentication, configureren met behulp van voorwaardelijke toegang van Azure AD. Azure AD biedt ondersteuning voor meer mogelijkheden, waaronder toepassingsspecifieke token handtekening certificaten en verval datums van Configureer bare certificaten. Met deze mogelijkheden, hulpprogram ma's en richt lijnen kunnen organisaties hun on-premises id-providers buiten gebruik stellen. Voor één voor beeld heeft micro soft de 17.987-toepassingen verplaatst van de interne Active Directory Federation Services (AD FS) van micro soft naar Azure AD.

![Ontwikkeling van Azure AD](media/cloud-governed-management-for-on-premises/image5.png)

Als u wilt beginnen met het migreren van federatieve toepassingen naar Azure AD als id-provider, raadpleegt u de https://aka.ms/migrateapps volgende koppelingen:

* Het White paper [over het migreren van uw toepassingen naar Azure Active Directory](https://aka.ms/migrateapps/whitepaper), die de voor delen van de migratie biedt en beschrijft hoe u de migratie plant in vier duidelijk beschreven fasen: detectie, classificatie, migratie en continu beheer. U wordt begeleid bij het bepalen van het proces en het verbreken van het project in gemakkelijk te gebruiken onderdelen. In het hele document vindt u koppelingen naar belang rijke bronnen die u op weg helpen.

* De oplossings handleiding [voor het migreren van toepassings verificatie van Active Directory Federation Services naar Azure Active Directory](../manage-apps/migrate-adfs-apps-to-azure.md) Verken in meer detail dezelfde vier fasen voor het plannen en uitvoeren van een toepassings migratie project. In deze hand leiding leert u hoe u deze fasen toepast op het specifieke doel van het verplaatsen van een toepassing van Active Directory Federation Services (AD FS) naar Azure AD.

* Het [Active Directory Federation Services gereedheids script voor migratie](https://aka.ms/migrateapps/adfstools) kan worden uitgevoerd op bestaande on-premises Active Directory Federation Services (AD FS)-servers om de gereedheid van toepassingen voor migratie naar Azure ad te bepalen.

## <a name="ongoing-access-management-across-cloud-and-on-premises-applications"></a>Doorlopend toegangs beheer voor alle Cloud-en on-premises toepassingen

Organisaties hebben een proces nodig voor het beheren van toegang die schaalbaar is. Gebruikers blijven toegangs rechten verzamelen en eindigen op het punt dat aanvankelijk voor hen is ingericht. Daarnaast moeten bedrijfs organisaties efficiënt kunnen schalen om het toegangs beleid en de controles voortdurend te ontwikkelen en af te dwingen.

Normaal gesproken worden goedkeuringsbeslissingen met betrekking tot de toegang door de IT-afdeling overgelaten aan besluitvormers van het bedrijf. Daarnaast kan de IT-afdeling de gebruikers hierbij betrekken. Gebruikers die toegang hebben tot vertrouwelijke klantgegevens in de marketingtoepassing van een bedrijf in Europa moeten bijvoorbeeld op de hoogte zijn van het bedrijfsbeleid. Gast gebruikers zijn mogelijk niet op de hoogte van de verwerkings vereisten voor gegevens in een organisatie waarvoor ze zijn uitgenodigd.

Organisaties kunnen het toegangs levenscyclus proces automatiseren via technologieën zoals [dynamische groepen](../enterprise-users/groups-dynamic-membership.md), gekoppeld aan het inrichten van gebruikers voor [SaaS-toepassingen](../saas-apps/tutorial-list.md)of [toepassingen die zijn geïntegreerd met het systeem voor Identity Management (scim](../app-provisioning/use-scim-to-provision-users-and-groups.md))-standaard. Organisaties kunnen ook bepalen welke [gast gebruikers toegang hebben tot on-premises toepassingen](../external-identities/hybrid-cloud-to-on-premises.md). Deze toegangsrechten kunnen vervolgens regelmatig worden geëvalueerd met behulp van herhaaldelijke [Azure AD-toegangsbeoordelingen](../governance/access-reviews-overview.md).

## <a name="future-directions"></a>Volgende richting

In hybride omgevingen is de strategie van micro soft om implementaties in te scha kelen waarbij de **Cloud het besturings vlak voor identiteits**-en on-premises directory's en andere identiteits systemen, zoals Active Directory en andere on-premises toepassingen, het doel is voor het inrichten van gebruikers met toegang. Deze strategie blijft de rechten, identiteiten en toegang tot de toepassingen en werk belastingen die hiervan afhankelijk zijn, blijven waarborgen. In deze eind toestand kunnen organisaties de productiviteit van eind gebruikers volledig vanuit de Cloud doen.

![Azure AD-architectuur](media/cloud-governed-management-for-on-premises/image6.png)

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over hoe u aan de slag kunt gaan de Azure AD-implementatie plannen, te vinden op <https://aka.ms/deploymentplans> . Ze bieden end-to-end hulp voor het implementeren van Azure Active Directory (Azure AD)-mogelijkheden. Elk plan bevat uitleg over de bedrijfs waarde, plannings overwegingen, ontwerp en operationele procedures die nodig zijn om algemene Azure AD-mogelijkheden uit te kunnen rollen. Micro soft werkt de implementatie plannen voortdurend bij met de best practices die zijn geleerd van klant implementaties en andere feedback wanneer we nieuwe mogelijkheden toevoegen voor het beheer vanuit de Cloud met Azure AD.