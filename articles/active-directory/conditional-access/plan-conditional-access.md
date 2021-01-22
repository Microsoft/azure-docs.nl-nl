---
title: Een Azure Active Directory implementatie van voorwaardelijke toegang plannen
description: Meer informatie over het ontwerpen van beleid voor voorwaardelijke toegang en het effectief implementeren van uw organisatie.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 10/16/2020
ms.author: baselden
author: BarbaraSelden
manager: daveba
ms.reviewer: joflore
ms.collection: M365-identity-device-management
ms.openlocfilehash: da21359b56b9e5b9d6e971032d06f8bae94efce0
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98682135"
---
# <a name="plan-a-conditional-access-deployment"></a>Een implementatie van voorwaardelijke toegang plannen

Het plannen van de implementatie van voorwaardelijke toegang is van cruciaal belang om de toegangs strategie van uw organisatie voor apps en resources te bereiken.

In een mobiele wereld hebben uw gebruikers overal toegang tot de resources van uw organisatie met behulp van verschillende apparaten en apps. Als gevolg hiervan is het niet langer voldoende om zich te richten op wie toegang heeft tot een resource. U moet ook rekening houden met de locatie van de gebruiker, het apparaat dat wordt gebruikt, de toegang tot de resource en meer. 

Azure Active Directory (Azure AD) voorwaardelijke toegang-analyses, zoals gebruiker, apparaat en locatie, voor het automatiseren van beslissingen en het afdwingen van beleid voor organisatie toegang voor de resource. U kunt beleid voor voorwaardelijke toegang gebruiken om toegangs beheer, zoals Multi-Factor Authentication (MFA), toe te passen. Met beleid voor voorwaardelijke toegang kunt u gebruikers vragen voor MFA wanneer dit nodig is voor de beveiliging, en de manier van gebruikers blijven gebruiken als ze niet nodig zijn.

![Overzicht van voorwaardelijke toegang](./media/plan-conditional-access/conditional-access-overview-how-it-works.png)

Micro soft biedt standaard voorwaardelijke beleids regels met de naam [beveiligings standaard instellingen](../fundamentals/concept-fundamentals-security-defaults.md) die een basis beveiligings niveau garanderen. Uw organisatie heeft echter mogelijk meer flexibiliteit nodig dan de standaard instellingen voor de beveiliging. U kunt voorwaardelijke toegang gebruiken voor het aanpassen van de standaard instellingen van de beveiliging met een meer granulariteit en voor het configureren van nieuwe beleids regels die voldoen aan uw vereisten.

## <a name="learn"></a>Learn

Voordat u begint, moet u weten hoe [voorwaardelijke toegang](overview.md) werkt en wanneer u deze moet gebruiken.

### <a name="benefits"></a>Voordelen

De voor delen van het implementeren van voorwaardelijke toegang zijn:

* Verhoog de productiviteit. Onderbreek alleen gebruikers met een aanmeldings voorwaarde als MFA wanneer een of meer signalen deze rechtvaardigen. Met beleids regels voor voorwaardelijke toegang kunt u bepalen wanneer gebruikers om een MFA wordt gevraagd, wanneer de toegang wordt geblokkeerd en wanneer ze een vertrouwd apparaat moeten gebruiken.

* Risico's beheren. Het automatiseren van risico analyse met beleids voorwaarden betekent dat Risk ante aanmeldingen eenmaal zijn geïdentificeerd en opgelost of geblokkeerd. Als u voorwaardelijke toegang koppelt met [identiteits beveiliging](../identity-protection/overview-identity-protection.md), die afwijkingen en verdachte gebeurtenissen detecteert, kunt u instellen wanneer toegang tot resources wordt geblokkeerd of gegatedd. 

* Naleving en beheer van adressen. Met voorwaardelijke toegang kunt u de toegang tot toepassingen controleren, gebruiks voorwaarden weer geven voor toestemming en toegang beperken op basis van nalevings beleid.

* Kosten beheren. Door toegangs beleid naar Azure AD te verplaatsen, vermindert u de afhankelijkheid van aangepaste of on-premises oplossingen voor voorwaardelijke toegang, en de kosten van de infra structuur.

### <a name="license-requirements"></a>Licentievereisten

Zie [licentie vereisten voor voorwaardelijke toegang](overview.md).

Als aanvullende functies vereist zijn, hebt u mogelijk ook gerelateerde licenties nodig. Zie [Azure Active Directory prijzen](https://azure.microsoft.com/pricing/details/active-directory/)voor meer informatie.

### <a name="prerequisites"></a>Vereisten

* Een werkende Azure AD-tenant waarvoor een Azure AD Premium- of proeflicentie is ingeschakeld. [Maak er gratis een](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) indien nodig.

* Een account met beheerders bevoegdheden voor voorwaardelijke toegang.

* Een niet-beheerder met een wachtwoord dat u kent, zoals testuser. Als u een gebruiker wilt maken, raadpleegt u [Snelstart: Nieuwe gebruikers toevoegen aan Azure Active Directory](../fundamentals/add-users-azure-active-directory.md) als een testgebruiker zonder beheerdersbevoegdheden een wachtwoord heeft dat u kent, en u een gebruiker moet maken.

* Een groep waarvan de niet-beheerders gebruiker lid is. Als u een groep wilt maken, raadpleegt u [een groep maken en leden toevoegen in azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md).

### <a name="training-resources"></a>Trainings bronnen

De volgende bronnen kunnen nuttig zijn als u meer informatie wilt over voorwaardelijke toegang:


#### <a name="videos"></a>Video's

* [Wat is voorwaardelijke toegang?](https://youtu.be/ffMAw2IVO7A)
* [Hoe kan ik voorwaardelijke toegang implementeren?](https://youtu.be/c_izIRNJNuk)
* [Hoe kan ik het beleid voor voorwaardelijke toegang implementeren voor eind gebruikers?](https://youtu.be/0_Fze7Zpyvc)
* [Voorwaardelijke toegang met apparaatbesturingen](https://youtu.be/NcONUf-jeS4)
* [Voorwaardelijke toegang met Azure AD MFA](https://youtu.be/Tbc-SU97G-w)
* [Voorwaardelijke toegang in Enterprise Mobility + Security](https://youtu.be/A7IrxAH87wc)


#### <a name="online-courses-on-pluralsight"></a>Online cursussen op PluralSight

* [Identiteits beheer ontwerpen in Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-identity-management-design)
* [Verificatie ontwerpen voor Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authentication-design)
* [Autorisatie ontwerpen voor Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authorization-design)

## <a name="plan-the-deployment-project"></a>Het implementatie project plannen

Houd rekening met de behoeften van uw organisatie terwijl u de strategie voor deze implementatie in uw omgeving bepaalt.

### <a name="engage-the-right-stakeholders"></a>De juiste belanghebbenden benaderen

Wanneer technologie projecten mislukken, worden ze doorgaans als gevolg van niet-overeenkomende verwachtingen voor impact, resultaten en verantwoordelijkheden. Als u deze problemen wilt voor komen, moet [u ervoor zorgen dat u de juiste belanghebbenden gebruikt](../fundamentals/active-directory-deployment-plans.md) en dat de project rollen duidelijk zijn.

### <a name="plan-communications"></a>De communicatie plannen

Communicatie is van cruciaal belang voor het slagen van een nieuwe service. Communiceer proactief met uw gebruikers hoe hun ervaring verandert, wanneer deze wordt gewijzigd, en hoe u ondersteuning krijgt als u problemen ondervindt.

### <a name="plan-a-pilot"></a>Een pilot plannen

Wanneer nieuwe beleids regels gereed zijn voor uw omgeving, implementeert u deze in fasen in de productie omgeving. Pas eerst een beleid toe op een kleine set gebruikers in een test omgeving en controleer of het beleid zich op de verwachte manier gedraagt. Bekijk [Aanbevolen procedures voor een pilot](../fundamentals/active-directory-deployment-plans.md).

> [!NOTE]
> Voor het implementeren van nieuwe beleids regels die niet specifiek zijn voor beheerders, sluit u alle beheerders uit. Dit zorgt ervoor dat beheerders nog steeds toegang hebben tot het beleid en wijzigingen kunnen aanbrengen of intrekken als er een grote invloed is op de impact. Valideer het beleid altijd met kleinere gebruikers groepen voordat u toepast op alle gebruikers.

## <a name="understand-conditional-access-policy-components"></a>De onderdelen van het beleid voor voorwaardelijke toegang begrijpen
Beleid voor voorwaardelijke toegang geldt als-then-instructies: als aan een toewijzing wordt voldaan, past u deze toegangs elementen toe.

Wanneer u beleid voor voorwaardelijke toegang configureert, worden de voor waarden *toewijzingen* genoemd. Met beleid voor voorwaardelijke toegang kunt u toegangs beheer op de apps van uw organisatie afdwingen op basis van bepaalde toewijzingen.


Zie [buil ding a Conditional Access Policy](concept-conditional-access-policies.md)voor meer informatie.

![het scherm beleid maken](media/plan-conditional-access/create-policy.png)

[Toewijzingen](concept-conditional-access-policies.md#assignments) definiëren de

* [Gebruikers en groepen](concept-conditional-access-users-groups.md) die door het beleid worden beïnvloed

* [Cloud-apps of-acties](concept-conditional-access-cloud-apps.md) waarop het beleid van toepassing is 

* [Voor waarden](concept-conditional-access-conditions.md) waaronder het beleid van toepassing is.

Instellingen voor [toegangs beheer](concept-conditional-access-policies.md) bepalen hoe een beleid wordt afgedwongen:

* Toegang tot Cloud-apps [verlenen of blok keren](concept-conditional-access-grant.md) .

* [Sessie besturings elementen](concept-conditional-access-session.md) maken beperkte ervaringen mogelijk binnen specifieke Cloud-apps.

### <a name="ask-the-right-questions-to-build-your-policies"></a>Vraag de juiste vragen om uw beleid te bouwen

Beleids regels beantwoorden vragen over wie toegang moet hebben tot uw resources, welke resources zij moeten gebruiken en onder welke voor waarden. Beleids regels kunnen worden ontworpen om toegang te verlenen of om toegang te blok keren. Zorg ervoor dat u de juiste vragen stelt over wat uw beleid probeert te verkrijgen. 

Documenteer de antwoorden op vragen voor elk beleid voordat u het bouwt. 

#### <a name="common-questions-about-assignments"></a>Veelgestelde vragen over toewijzingen

[Gebruikers en groepen](concept-conditional-access-users-groups.md)

* Welke gebruikers en groepen worden opgenomen in of uitgesloten van het beleid?

* Bevat dit beleid alle gebruikers, een specifieke groep gebruikers, Directory rollen of externe gebruikers?

[Cloud-apps of acties](concept-conditional-access-cloud-apps.md)

* Op welke toepassing (en) wordt het beleid toegepast?

* Welke gebruikers acties worden onderhevig aan dit beleid?

[Voorwaarden](concept-conditional-access-conditions.md)

* Welke platformen worden opgenomen in of uitgesloten van het beleid?

* Wat zijn de vertrouwde locaties van de organisatie?

* Welke locaties worden opgenomen in of uitgesloten van het beleid?

* Welke typen client-apps (browser, mobiel, desktop-clients, apps met verouderde verificatie methoden) worden opgenomen in of uitgesloten van het beleid?

* Hebt u beleids regels die de uitzonde ring met Azure AD gekoppelde apparaten of hybride Azure AD-apparaten uit het beleid kunnen verbreken? 

* Als u [identiteits beveiliging](../identity-protection/concept-identity-protection-risks.md)gebruikt, wilt u aanmeldings risico beveiliging opnemen?

#### <a name="common-questions-about-access-controls"></a>Veelgestelde vragen over toegangs beheer

[Verlenen of blok keren ](concept-conditional-access-grant.md) 

Wilt u toegang verlenen tot resources door een of meer van de volgende handelingen uit te voeren?

* MFA vereisen

* Vereisen dat het apparaat moet worden gemarkeerd als compatibel

* Hybride Azure AD-gekoppeld apparaat vereisen

* Goedgekeurde client-apps vereisen

* Beleid voor app-beveiliging vereisen

[Sessie beheer](concept-conditional-access-session.md)

Wilt u een van de volgende toegangs besturings elementen afdwingen voor Cloud-apps?

* Door apps afgedwongen machtigingen gebruiken

* App-beheer voor voorwaardelijke toegang gebruiken

* Aanmeldings frequentie afdwingen

* Permanente browser sessies gebruiken

### <a name="access-token-issuance"></a>Uitgifte van toegangs token

Het is belang rijk om te begrijpen hoe toegangs tokens worden uitgegeven. 

![Diagram voor het uitgeven van toegangs tokens](media/plan-conditional-access/CA-policy-token-issuance.png)

> [!NOTE]
> Als er geen toewijzing is vereist en er geen beleid voor voorwaardelijke toegang van kracht is, is het standaard gedrag om een toegangs token te verlenen. 

Denk bijvoorbeeld aan een beleid waarbij:

Als de gebruiker zich in groep 1 bevindt, dwingt u MFA af voor toegang tot app 1.

Als een gebruiker die niet in groep 1 is, probeert toegang te krijgen tot de voor waarde ' If ' als aan de app ' als ' wordt voldaan, en er een token is uitgegeven. Als u gebruikers buiten groep 1 wilt uitsluiten, moet u een afzonderlijk beleid voor het blok keren van alle andere gebruikers.

## <a name="follow-best-practices"></a>Aanbevolen procedures volgen

Het Framework voor voorwaardelijke toegang biedt een uitstekende configuratie flexibiliteit. Een fantastische flexibiliteit betekent echter ook dat u elk configuratie beleid zorgvuldig moet door nemen voordat het wordt vrijgegeven om ongewenste resultaten te voor komen.

### <a name="apply-conditional-access-policies-to-every-app"></a>Beleid voor voorwaardelijke toegang Toep assen op elke app

Toegangs tokens worden standaard verleend als voor de voor waarde van een voorwaardelijk toegangs beleid geen toegangs beheer wordt geactiveerd. Zorg ervoor dat voor elke app ten minste één beleid voor voorwaardelijke toegang is toegepast

> [!IMPORTANT]
> Wees voorzichtig met het gebruik van blok keren en alle apps in één beleid. Dit kan beheerders vergren delen buiten de Azure-beheer Portal en uitsluitingen kunnen niet worden geconfigureerd voor belang rijke eind punten, zoals Microsoft Graph.

### <a name="minimize-the-number-of-conditional-access-policies"></a>Minimaliseer het aantal beleids regels voor voorwaardelijke toegang

Het maken van een beleid voor elke app is niet efficiënt en leidt tot moeilijk beheer. In voorwaardelijke toegang worden alleen de eerste 195-beleids regels per gebruiker toegepast. We raden u aan uw apps te analyseren en ze te groeperen in toepassingen die dezelfde resource vereisten voor dezelfde gebruikers hebben. Als alle Microsoft 365-apps of alle HR-apps dezelfde vereisten voor dezelfde gebruikers hebben, kunt u bijvoorbeeld één beleid maken en alle apps toevoegen waarop dit van toepassing is. 

### <a name="set-up-emergency-access-accounts"></a>Accounts voor nood toegang instellen

Als u een beleid hebt geconfigureerd, kan het de organisaties van de Azure Portal vergren delen. Het beperken van de impact van de onbedoelde beheerder vergrendeling door twee of meer [accounts voor toegang tot een nood geval](../roles/security-emergency-access.md) in uw organisatie te maken.

* Maak een gebruikers account dat is toegewezen aan beleids beheer en uitgesloten is van al uw beleids regels.

### <a name="set-up-report-only-mode"></a>Modus alleen rapport instellen

Het kan lastig zijn om het aantal en de namen van de gebruikers te voors pellen die worden beïnvloed door gang bare implementatie-initiatieven zoals:

* verouderde verificatie blok keren
* MFA vereisen
* beleid voor aanmeldings Risico's implementeren

Met de [modus alleen rapport](concept-conditional-access-report-only.md) kunnen beheerders de impact van beleids regels voor voorwaardelijke toegang evalueren voordat ze in hun omgeving worden ingeschakeld.

Meer informatie over het [configureren van de modus alleen rapport voor een beleid voor voorwaardelijke toegang](howto-conditional-access-insights-reporting.md).

### <a name="plan-for-disruption"></a>Plan voor onderbreking

Als u afhankelijk bent van één toegangs beheer, zoals MFA of een netwerk locatie, bent u gevoelig voor toegang tot fouten als dat ene toegangs beheer niet beschikbaar is of niet goed is geconfigureerd. [Plan strategieën](../authentication/concept-resilient-controls.md) voor het nemen van uw organisatie om het risico van vergren deling tijdens onvoorziene onderbrekingen te verminderen.

### <a name="set-naming-standards-for-your-policies"></a>Naamgevings standaarden instellen voor uw beleid

De naamgevings standaard helpt u bij het vinden van beleids regels en het begrijpen van het doel ervan zonder ze te openen in de Azure-beheer Portal. U wordt aangeraden uw beleid een naam te geven voor het volgende:

* Een Volg nummer

* De Cloud-app (s) die van toepassing is op

* Het antwoord

* Wie is van toepassing op

* Wanneer dit van toepassing is (indien van toepassing)

![Scherm opname van de naamgevings standaarden voor beleids regels.](media/plan-conditional-access/11.png)

**Voor beeld**; Een beleid om MFA te vereisen voor marketing gebruikers die toegang hebben tot de Dynamics CRP-app vanuit externe netwerken kunnen het volgende zijn:

![Naamgevings standaard](media/plan-conditional-access/naming-example.png)

Een beschrijvende naam helpt u een overzicht te krijgen van de implementatie van voorwaardelijke toegang. Het Volg nummer is handig als u wilt verwijzen naar een beleid in een gesprek. Wanneer u bijvoorbeeld een beheerder op de telefoon spreekt, kunt u hen vragen om het beleid CA01 te openen om een probleem op te lossen.

#### <a name="naming-standards-for-emergency-access-controls"></a>Naamgevings regels voor toegangs beheer voor nood gevallen

Naast uw actieve beleid, implementeert u uitgeschakeld beleid dat fungeert als secundaire, [flexibele toegangs controles in storingen of nood scenario's](../authentication/concept-resilient-controls.md). De naamgevings standaard voor het beleid voor nood gevallen omvat:
* Schakel IN nood gevallen aan het begin IN om de naam onder het andere beleid te laten opvallen.

* De naam van de onderbreking moet van toepassing zijn op.

* Een Volg nummer van een volg orde om de beheerder te helpen weten in welke volg orde het order beleid moet worden ingeschakeld.

**Voorbeeld**

De volgende naam geeft aan dat dit beleid het eerste van vier beleids regels is om in te scha kelen als er sprake is van een MFA-onderbreking:

EM01-inschakelen IN nood gevallen: MFA-onderbreking [1/4]-Exchange share point: vereist hybride Azure AD-deelname voor VIP-gebruikers.

### <a name="exclude-countries-from-which-you-never-expect-a-sign-in"></a>Landen uitsluiten van waaruit u geen aanmelding verwacht.

Met Azure Active Directory kunt u [benoemde locaties](location-condition.md)maken. Maak een benoemde locatie die alle landen bevat van waaruit u nooit een aanmelding zou verwachten. Maak vervolgens een beleid voor alle apps die het aanmelden vanaf die benoemde locatie blok keren. **Zorg ervoor dat uw beheerders van dit beleid worden uitgesloten**.

### <a name="plan-your-policy-deployment"></a>De implementatie van uw beleid plannen

Wanneer nieuwe beleids regels gereed zijn voor uw omgeving, moet u elk beleid door nemen voordat u het uitbrengt om ongewenste resultaten te voor komen.

## <a name="common-policies"></a>Algemene beleidsregels

Bepaal bij het plannen van de oplossing voor het beleid voor voorwaardelijke toegang of u beleid moet maken om de volgende resultaten te verkrijgen.

* [MFA vereisen](#require-mfa)
* [Reageren op mogelijk gemanipuleerde accounts](#respond-to-potentially-compromised-accounts)
* [Beheerde apparaten vereisen](#require-managed-devices)
* [Goedgekeurde client toepassingen vereisen](#require-approved-client-apps)
* [Toegang blokkeren](#block-access)

### <a name="require-mfa"></a>MFA vereisen

Algemene use cases voor het vereisen van MFA-toegang:

* [Door beheerders](howto-conditional-access-policy-admin-mfa.md)

* [Naar specifieke apps](../authentication/tutorial-enable-azure-mfa.md)

* [Voor alle gebruikers](howto-conditional-access-policy-all-users-mfa.md)

* [Vanaf netwerk locaties vertrouwt u niet](untrusted-networks.md)

* [Voor Azure Management](howto-conditional-access-policy-azure-management.md)

### <a name="respond-to-potentially-compromised-accounts"></a>Reageren op mogelijk gemanipuleerde accounts

Met het beleid voor voorwaardelijke toegang kunt u automatische antwoorden op aanmeldingen implementeren door mogelijk verkraakte identiteiten. De waarschijnlijkheid dat een account wordt aangetast, wordt weer gegeven in de vorm van risico niveaus. Er zijn twee risico niveaus berekend door identiteits beveiliging: aanmeldings risico en gebruikers risico. De volgende drie standaard beleidsregels die kunnen worden ingeschakeld.

* [Vereisen dat alle gebruikers zich registreren voor MFA](howto-conditional-access-policy-risk.md)

* [Wachtwoord wijziging vereisen voor gebruikers met een hoog risico](howto-conditional-access-policy-risk.md)

* [MFA vereisen voor gebruikers met gemiddeld of hoog aanmeldings risico](howto-conditional-access-policy-risk.md)

### <a name="require-managed-devices"></a>Beheerde apparaten vereisen

De verspreiding van ondersteunde apparaten om toegang te krijgen tot uw Cloud bronnen helpt u bij het verbeteren van de productiviteit van uw gebruikers. Waarschijnlijk wilt u niet dat bepaalde resources in uw omgeving toegankelijk zijn voor apparaten met een onbekend beveiligings niveau. Voor deze bronnen [is vereist dat gebruikers alleen toegang tot deze resources hebben via een beheerd apparaat](require-managed-devices.md).

### <a name="require-approved-client-apps"></a>Goedgekeurde client-apps vereisen

Werk nemers gebruiken hun mobiele apparaten voor zowel privé-als werk taken. Voor BYOD-scenario's moet u bepalen of u het hele apparaat of alleen de gegevens erop wilt beheren. Als alleen gegevens en toegang wordt beheerd, kunt u [goedgekeurde Cloud-apps vereisen](app-based-conditional-access.md) die uw bedrijfs gegevens kunnen beveiligen. u kunt bijvoorbeeld vereisen dat e-mail berichten alleen toegankelijk zijn via Outlook Mobile en niet via een algemeen e-mail programma.

### <a name="block-access"></a>Toegang blokkeren

De optie om [alle toegang te blok keren](howto-conditional-access-policy-block-access.md) , is krachtig. Het kan bijvoorbeeld worden gebruikt wanneer u een app migreert naar Azure AD, maar niet gereed bent voor iedereen om zich nog aan te melden. Toegang blok keren: 

* Onderdrukt alle andere toewijzingen voor een gebruiker

* Biedt de kracht om uw hele organisatie te blok keren om u aan te melden bij uw Tenant

> [!IMPORTANT]
> Als u een beleid maakt om de toegang te blok keren voor alle gebruikers, moet u ervoor zorgen dat u geen accounts voor nood toegang sluit en kunt u alle beheerders uitsluiten van het beleid.

Andere veelvoorkomende scenario's waarin u de toegang tot uw gebruikers kunt blok keren:

* [Bepaalde netwerk locaties blok keren](howto-conditional-access-policy-location.md) voor toegang tot uw Cloud-apps. U kunt dit beleid gebruiken om bepaalde landen te blok keren waarvan u weet dat verkeer niet afkomstig mag zijn.

* Azure AD biedt ondersteuning voor verouderde verificatie. Verouderde verificatie biedt echter geen ondersteuning voor MFA en voor veel omgevingen is het niet nodig om identiteits beveiliging te verhelpen. In dit geval kunt u [apps blok keren die gebruikmaken van verouderde verificatie ](block-legacy-authentication.md) om toegang te krijgen tot uw Tenant bronnen.

## <a name="build-and-test-policies"></a>Beleid voor bouwen en testen

Zorg er in elke fase van de implementatie voor dat u evalueert dat de resultaten naar verwachting zijn. 

Wanneer nieuwe beleids regels gereed zijn, implementeert u deze in fasen in de productie omgeving:

* Bied interne wijzigings communicatie aan eind gebruikers aan.

* Begin met een kleine groep gebruikers en controleer of het beleid zich gedraagt zoals verwacht.

* Wanneer u een beleid uitbreidt om meer gebruikers op te nemen, moet u alle beheerders blijven uitsluiten. Met uitzonde ring van beheerders weet u zeker dat iemand nog steeds toegang tot een beleid heeft als een wijziging is vereist.

* Pas een beleid toe op alle gebruikers nadat het uitgebreid is getest. Zorg ervoor dat u ten minste één beheerders account hebt waarop een beleid niet van toepassing is.

### <a name="create-test-users"></a>Test gebruikers maken

Maak een set test gebruikers die de gebruikers in uw productie omgeving weer spie gelen. Door test gebruikers te maken, kunt u controleren of het beleid werkt zoals verwacht voordat u echte gebruikers beïnvloedt en mogelijk de toegang tot apps en resources verstoort.

Sommige organisaties hebben test tenants voor dit doel einde. Het kan echter lastig zijn om alle voor waarden en apps in een test Tenant opnieuw te maken om het resultaat van een beleid volledig te testen.

### <a name="create-a-test-plan"></a>Een test plan maken

Het test plan is belang rijk voor een vergelijking tussen de verwachte resultaten en de werkelijke resultaten. U moet altijd een verwachting hebben voordat u iets kunt testen. De volgende tabel geeft een overzicht van voor beelden van test cases. Pas de scenario's en verwachte resultaten aan op basis van de configuratie van uw beleid voor voorwaardelijke toegang.

| Beleid| Scenario| Verwacht resultaat |
| - | - | - |
| [MFA vereisen wanneer dat niet aan het werk is](untrusted-networks.md)| Geautoriseerde gebruiker meldt zich aan bij de app op een vertrouwde locatie/werk| Gebruiker wordt niet gevraagd om MFA |
| [MFA vereisen wanneer dat niet aan het werk is](untrusted-networks.md)| Geautoriseerde gebruiker meldt zich aan bij de app, maar niet op een vertrouwde locatie/werk| De gebruiker wordt gevraagd om te MFA en kan zich aanmelden |
| [MFA vereisen (voor beheerder)](../fundamentals/concept-fundamentals-security-defaults.md)| De globale beheerder meldt zich aan bij de app| Beheerder wordt gevraagd om te MFA |
| [Riskante aanmeldingen](../identity-protection/howto-identity-protection-configure-risk-policies.md)| Gebruiker meldt zich aan bij de app met een niet-goedgekeurde browser| Beheerder wordt gevraagd om te MFA |
| [Apparaatbeheer](require-managed-devices.md)| Geautoriseerde gebruiker probeert zich aan te melden bij een bevoegd apparaat| Toegang verleend |
| [Apparaatbeheer](require-managed-devices.md)| Geautoriseerde gebruiker probeert zich aan te melden vanaf een niet-geautoriseerd apparaat| Toegang geblokkeerd |
| [Wachtwoord wijziging voor Risk ante gebruikers](../identity-protection/howto-identity-protection-configure-risk-policies.md)| Geautoriseerde gebruiker meldt zich aan met de aangetaste referenties (aanmelden met een hoog risico)| De gebruiker wordt gevraagd om het wacht woord te wijzigen of de toegang wordt geblokkeerd op basis van uw beleid |


### <a name="configure-the-test-policy"></a>Het test beleid configureren

In de [Azure Portal](https://portal.azure.com/)configureert u beleid voor voorwaardelijke toegang onder Azure Active Directory > beveiliging > voorwaardelijke toegang.

Als u meer wilt weten over het maken van beleid voor voorwaardelijke toegang, raadpleegt u dit voor beeld: [beleid voor voorwaardelijke toegang om te vragen aan MFA wanneer een gebruiker zich aanmeldt bij de Azure Portal](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json). Deze Snelstartgids helpt u bij het volgende:

* Vertrouwd raken met de gebruikers interface

* Een eerste indruk krijgen van de werking van voorwaardelijke toegang

### <a name="enable-the-policy-in-report-only-mode"></a>Het beleid in de modus alleen rapport inschakelen

Als u de gevolgen van uw beleid wilt beoordelen, moet u eerst het beleid inschakelen in de [modus alleen rapport](concept-conditional-access-report-only.md). Alleen rapport beleidsregels worden geëvalueerd tijdens het aanmelden, maar granting Controls en sessie besturings elementen worden niet afgedwongen. Wanneer u het beleid opslaat in de modus alleen rapport, ziet u de invloed op real-time aanmeldingen in de aanmeldings Logboeken. Selecteer in de aanmeldings Logboeken een gebeurtenis en navigeer naar het tabblad alleen rapport om het resultaat van elk rapport beleid te bekijken.


![modus alleen rapport ](media/plan-conditional-access/report-only-mode.png)

Als u het beleid selecteert, kunt u ook zien hoe de toewijzingen en toegangs beheer van het beleid worden geëvalueerd met het scherm Details van beleid. Om een beleid toe te passen op een aanmelding, moet aan elk van de geconfigureerde toewijzingen worden voldaan. 

### <a name="understand-the-impact-of-your-policies-using-the-insights-and-reporting-workbook"></a>Inzicht in de gevolgen van uw beleid met behulp van de inzichten en de rapportage werkmap

U kunt de cumulatieve impact van uw beleid voor voorwaardelijke toegang bekijken in de werkmap inzichten en rapportage. U hebt een Azure Monitor-abonnement nodig om toegang te krijgen tot de werkmap en u moet [uw aanmeldings logboeken streamen naar een log Analytics-werk ruimte](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md). 

### <a name="simulate-sign-ins-using-the-what-if-tool"></a>Aanmeldingen simuleren met het hulp programma What-if

Een andere manier om uw beleid voor voorwaardelijke toegang te valideren, is met behulp van het [hulp programma What-if](troubleshoot-conditional-access-what-if.md), dat simuleert welk beleid van toepassing zou zijn op een gebruiker die zich aanmeldt onder hypothetische omstandigheden. Selecteer de aanmeldings kenmerken die u wilt testen (zoals de gebruiker, toepassing, het platform van het apparaat en de locatie) en bekijk welke beleids regels van toepassing zijn.

> [!NOTE] 
> Hoewel een gesimuleerde uitvoering u een goed idee krijgt van de impact van het beleid voor voorwaardelijke toegang, wordt een daad werkelijk uitgevoerde test niet vervangen.

### <a name="test-your-policy"></a>Uw beleid testen

Voer elke test in uw test plan uit met test gebruikers.

**Zorg ervoor dat u de uitsluitings criteria van een beleid test**. U kunt bijvoorbeeld een gebruiker of groep uitsluiten van een beleid waarvoor MFA vereist is. Test of de uitgesloten gebruikers om MFA wordt gevraagd, omdat de combi natie van andere beleids regels mogelijk MFA vereist voor die gebruikers.

### <a name="roll-back-policies"></a>Beleid terugdraaien

Gebruik een of meer van de volgende opties voor het geval u de zojuist geïmplementeerde beleids regels wilt herstellen:

* **Schakel het beleid uit.** Als u een beleid uitschakelt, wordt het niet toegepast wanneer een gebruiker zich probeert aan te melden. U kunt altijd terugkomen en het beleid inschakelen wanneer u het wilt gebruiken.

![beleids installatie kopie inschakelen](media/plan-conditional-access/enable-policy.png)

* **Een gebruiker of groep uitsluiten van een beleid.** Als een gebruiker geen toegang kan krijgen tot de app, kunt u ervoor kiezen om de gebruiker uit te sluiten van het beleid.

![gebruikers en groepen uitsluiten](media/plan-conditional-access/exclude-users-groups.png)

> [!NOTE]
>  Deze optie moet spaarzaam worden gebruikt, alleen in situaties waarin de gebruiker wordt vertrouwd. De gebruiker moet zo snel mogelijk worden toegevoegd aan het beleid of de groep.

* **Het beleid verwijderen.** Als het beleid niet meer nodig is, [verwijdert](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json) u het.

## <a name="manage-access-to-cloud-apps"></a>Toegang tot Cloud-apps beheren

Gebruik de volgende beheer opties om uw beleid voor voorwaardelijke toegang te bepalen en beheren:

![Scherm afbeelding toont de beheer opties voor C A-beleids regels, zoals benoemde locaties, aangepaste besturings elementen, Gebruiksvoorwaarden, V P N-connectiviteit en de geselecteerde klassieke beleids regels.](media/plan-conditional-access/manage-access.png)


### <a name="named-locations"></a>Benoemde locaties

De locatie voorwaarde van een beleid voor voorwaardelijke toegang stelt u in staat om instellingen voor toegangs beheer te koppelen aan de netwerk locaties van uw gebruikers. Met [benoemde locaties](location-condition.md)kunt u logische groeperingen van IP-adresbereiken of landen en regio's maken.

### <a name="custom-controls"></a>Aangepaste besturingselementen

[Aangepaste besturings elementen](controls.md) omleiden uw gebruikers naar een compatibele service om te voldoen aan de verificatie vereisten buiten Azure AD. Om aan dit besturings element te voldoen, wordt de browser van een gebruiker omgeleid naar de externe service, wordt elke vereiste verificatie uitgevoerd en vervolgens teruggeleid naar Azure AD. Azure AD verifieert de reactie en, als de gebruiker is geverifieerd of gevalideerd, wordt de gebruiker voortgezet in de stroom voor voorwaardelijke toegang.

### <a name="terms-of-use"></a>Gebruiksvoorwaarden

Voordat u bepaalde Cloud-apps in uw omgeving opent, kunt u toestemming van de gebruikers krijgen door hen de Gebruiksvoorwaarden (gebruiks voorwaarden) te accepteren. Volg deze [Snelstartgids om gebruiks voorwaarden te maken](require-tou.md).

## <a name="troubleshoot-conditional-access"></a>Problemen met voorwaardelijke toegang oplossen

Wanneer een gebruiker een probleem met een beleid voor voorwaardelijke toegang heeft, verzamelt u de volgende informatie om het oplossen van problemen te vergemakkelijken.

* Naam van gebruikers principe

* Weergave naam van gebruiker

* Naam van het besturings systeem

* Tijds tempel (geschatte is OK)

* Doel toepassing

* Type client toepassing (browser versus client)

* Correlatie-ID (dit is uniek voor het aanmelden)

Als de gebruiker een bericht met een koppeling meer details heeft ontvangen, kunnen ze de meeste van deze gegevens voor u verzamelen.

![Kan geen toegang krijgen tot het app-fout bericht](media/plan-conditional-access/cant-get-to-app.png)

Wanneer u de gegevens hebt verzameld, raadpleegt u de volgende bronnen:

* [Aanmeld problemen met voorwaardelijke toegang](troubleshoot-conditional-access.md) : inzicht in onverwachte aanmeldings resultaten die betrekking hebben op voorwaardelijke toegang met behulp van fout berichten en logboeken van Azure AD-aanmeldingen.

* [Gebruik het What-If-hulp programma](troubleshoot-conditional-access-what-if.md) om te begrijpen waarom een beleid is of niet is toegepast op een gebruiker in een specifieke omstandigheid of als een beleid van toepassing zou zijn op een bekende status.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over multi-factor Authentication](../authentication/concept-mfa-howitworks.md)

[Meer informatie over identiteits beveiliging](../identity-protection/overview-identity-protection.md)

[Beleid voor voorwaardelijke toegang beheren met Microsoft Graph-API](/graph/api/resources/conditionalaccesspolicy)