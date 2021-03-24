---
title: Toepassings verificatie migreren naar Azure Active Directory
description: In dit Witboek vindt u informatie over de planning voor en de voor delen van het migreren van uw toepassings verificatie naar Azure AD.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 02/05/2021
ms.author: kenwith
ms.reviewer: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e05a7af3f0b95470432b4fb9602e1b41da9f72f
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104952963"
---
# <a name="migrate-application-authentication-to-azure-active-directory"></a>Toepassings verificatie migreren naar Azure Active Directory

## <a name="about-this-paper"></a>Over dit artikel

In dit Witboek vindt u informatie over de planning voor en de voor delen van het migreren van uw toepassings verificatie naar Azure AD. Het is ontworpen voor Azure-beheerders en identiteits professionals.

Het proces in vier fasen opsplitsen, elk met gedetailleerde plannings-en afsluit criteria, is ontworpen om u te helpen uw migratie strategie te plannen en inzicht te krijgen in de manier waarop Azure AD-verificatie de doel stellingen van uw organisatie ondersteunt.

## <a name="introduction"></a>Introductie

Uw organisatie vereist vandaag nog een Slew toepassingen (apps) zodat gebruikers hun werk kunnen doen. U kunt apps elke dag waarschijnlijk nog steeds toevoegen, ontwikkelen of buiten gebruik stellen. Gebruikers hebben toegang tot deze toepassingen vanuit een enorme verscheidenheid aan bedrijfs-en persoonlijke apparaten en locaties. Ze openen apps op verschillende manieren, waaronder:

- via een start pagina of portal van het bedrijf

- door blad wijzers in hun browser

- via de URL van een leverancier voor SaaS-apps (Software as a Service)

- koppelingen die rechtstreeks naar de Desk tops of mobiele apparaten van de gebruiker worden gepusht via een oplossing voor het beheer van mobiele apparaten/toepassingen (MDM/MAM)

Uw toepassingen gebruiken waarschijnlijk de volgende typen verificatie:

- On-premises Federatie oplossingen (zoals Active Directory Federation Services (ADFS) en ping)

- Active Directory (zoals Kerberos auth en Windows-Integrated auth)

- Andere IAM-oplossingen (Identity and Access Management) van de Cloud (zoals Okta of Oracle)

- On-premises webinfrastructuur (zoals IIS en Apache)

- In de Cloud gehoste infra structuur (zoals Azure en AWS)

**Om ervoor te zorgen dat de gebruikers eenvoudig en veilig toegang kunnen krijgen tot toepassingen, is het doel om één set toegangs beheer en-beleid te hebben in uw on-premises en Cloud omgevingen.**

[Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) biedt een universeel identiteits platform waarmee uw gebruikers, partners en klanten één identiteit kunnen gebruiken om toegang te krijgen tot de gewenste toepassingen en om vanaf elk platform en apparaat te kunnen samen werken.

![Een diagram van Azure Active Directory connectiviteit](media/migrating-application-authentication-to-azure-active-directory-1.jpg)

Azure AD heeft een [volledige reeks mogelijkheden voor identiteits beheer](../fundamentals/active-directory-whatis.md#which-features-work-in-azure-ad). Door uw app-verificatie en-autorisatie te standaardiseren in azure AD, kunt u profiteren van de voor delen van deze mogelijkheden.

U kunt meer migratie resources vinden op [https://aka.ms/migrateapps](./migration-resources.md)

## <a name="benefits-of-migrating-app-authentication-to-azure-ad"></a>Voor delen van het migreren van app-verificatie naar Azure AD

Door app-verificatie naar Azure AD te verplaatsen, kunt u Risico's en kosten beheren, de productiviteit verhogen en voldoen aan de vereisten voor naleving en governance.

### <a name="manage-risk"></a>beheer risico's.

Voor het beveiligen van uw apps moet u een volledig overzicht van alle risico factoren hebben. Door uw apps naar Azure AD te migreren, worden uw beveiligings oplossingen geconsolideerd. Met deze oplossing kunt u:

- Verbeter de veilige gebruikers toegang tot toepassingen en gekoppelde Bedrijfs gegevens met behulp van het [beleid voor voorwaardelijke toegang](../conditional-access/overview.md), [multi-factor Authentication](../authentication/concept-mfa-howitworks.md)en op risico gebaseerde technologieën voor [identiteits bescherming](../identity-protection/overview-identity-protection.md) met realtime.

- De toegang van bevoegde gebruikers tot uw omgeving beschermen met [just-in-time](../../azure-resource-manager/managed-applications/request-just-in-time-access.md) beheerders toegang.

- Gebruik het [ontwerp met hoge Beschik baarheid, geografisch gedistribueerd,](https://cloudblogs.microsoft.com/enterprisemobility/2014/09/02/azure-ad-under-the-hood-of-our-geo-redundant-highly-available-distributed-cloud-directory/)voor uw belangrijkste zakelijke behoeften van Azure AD.

- Bescherm uw oudere toepassingen met een van onze [beveiligde hybride toegangs partner integraties](https://aka.ms/secure-hybrid-access) die u al hebt geïmplementeerd.

### <a name="manage-cost"></a>Kosten beheren

Uw organisatie heeft mogelijk meerdere IAM-oplossingen (Identity Access Management). Migratie naar een Azure AD-infra structuur is een kans om de afhankelijkheden van IAM-licenties (on-premises of in de Cloud) en infrastructuur kosten te verminderen. In gevallen waarin u mogelijk al hebt betaald voor Azure AD via Microsoft 365 licenties, is er geen reden om de toegevoegde kosten van een andere IAM-oplossing te betalen.

**Met Azure AD kunt u de infrastructuur kosten verlagen door:**

- Veilige externe toegang bieden tot on-premises apps met behulp van [Azure AD-toepassingsproxy](./application-proxy.md).

- Het loskoppelen van apps van de on-premises referentie benadering in uw Tenant door [Azure AD in te stellen als de vertrouwde universele-ID-provider](../hybrid/plan-connect-user-signin.md#choosing-the-user-sign-in-method-for-your-organization).

### <a name="increase-productivity"></a>Productiviteit verhogen

Organisaties en veiligheids voordelen dragen bij aan het verbeteren van Azure AD, maar de volledige acceptatie en naleving zijn waarschijnlijker als gebruikers er ook voor doen. Met Azure AD kunt u het volgende doen:

- De [eenmalige Sign-On (SSO)](./what-is-single-sign-on.md) van de eind gebruiker verbeteren via naadloze en veilige toegang tot elke toepassing, vanaf elk apparaat en elke locatie.

- Gebruik self-service IAM-mogelijkheden, zoals [self-service voor het opnieuw instellen van wacht woorden](../authentication/concept-sspr-howitworks.md) en [selfservice groeps beheer](../enterprise-users/groups-self-service-management.md).

- Verminder de administratieve overhead door slechts één identiteit te beheren voor elke gebruiker in de Cloud-en on-premises omgevingen:

  - Het inrichten van gebruikers accounts (in de [Azure AD-galerie](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)) [automatiseren](../app-provisioning/user-provisioning.md) op basis van Azure AD-identiteiten
  - Open al uw apps vanuit het paneel MyApps in de [Azure Portal ](https://portal.azure.com/)

- Ontwikkel aars in staat stellen om toegang tot hun apps te beveiligen en de gebruikers ervaring te verbeteren door gebruik te maken van het [micro soft-identiteits platform](../develop/v2-overview.md) met de micro soft Authentication Library (MSAL).

- Bied uw partners toegang tot cloud resources met behulp van [Azure AD B2B-samen werking](../external-identities/what-is-b2b.md). Cloud resources verwijderen de overhead van het configureren van Point-to-Point Federatie met uw partners.

### <a name="address-compliance-and-governance"></a>Naleving en beheer van adressen

Zorg ervoor dat u voldoet aan de wettelijke vereisten door het beleid voor bedrijfs toegang af te dwingen en de gebruikers toegang tot toepassingen en gekoppelde gegevens te controleren met behulp van geïntegreerde controle hulpprogramma's en Api's. Met Azure AD kunt u toepassings aanmeldingen bewaken via rapporten die gebruikmaken van [hulpprogram ma's voor beveiligings incidenten en Event monitoring (Siem)](../reports-monitoring/plan-monitoring-and-reporting.md). U kunt de rapporten openen vanuit de portal of Api's, en programmatisch controleren wie toegang heeft tot uw toepassingen en de toegang tot inactieve gebruikers verwijderen via toegangs Beoordelingen.

## <a name="plan-your-migration-phases-and-project-strategy"></a>Plan uw migratie fasen en project strategie

Wanneer technologie projecten mislukken, is dit vaak te wijten aan niet-overeenkomende verwachtingen, de juiste belanghebbenden die geen deel uitmaken of een gebrek aan communicatie. Zorg ervoor dat het project zelf wordt gepland.

### <a name="the-phases-of-migration"></a>De fase van de migratie

Voordat we aan de hulpprogram ma's gaan, moet u weten hoe u het migratie proces kunt bedenken. We raden u aan de volgende vier fasen uit te voeren via verschillende workshops voor direct naar de klant:

![Een diagram van de fase van de migratie](media/migrating-application-authentication-to-azure-active-directory-2.jpg)

### <a name="assemble-the-project-team"></a>Het project team samen stellen

Toepassings migratie is een team taak en u moet ervoor zorgen dat alle vitale posities zijn gevuld. De ondersteuning van toonaangevende bedrijfs leiders is belang rijk. Zorg ervoor dat u beschikt over de juiste set executive sponsors, besluit vormers van bedrijven en experts (MKB).

Tijdens het migratie project kan één persoon aan meerdere rollen voldoen of meerdere personen die aan elke rol voldoen, afhankelijk van de grootte en structuur van uw organisatie. Mogelijk hebt u ook een afhankelijkheid van andere teams die een belang rijke rol spelen in uw landschap beveiliging.

De volgende tabel bevat de belangrijkste rollen en hun bijdragen:

| Rol          | Bijdragen                                              |
| ------------- | ---------------------------------------------------------- |
| **Project Manager** | Project bewaart het project, met inbegrip van:<br /> -Executive-ondersteuning verkrijgen<br /> -meenemen in belanghebbenden<br /> -schema's, documentatie en communicatie beheren |
| **Identiteits architect/Azure AD-app beheerder** | Ze zijn verantwoordelijk voor het volgende:<br /> -de oplossing ontwerpen in samen werking met belanghebbenden<br /> -Documenteer het ontwerp van de oplossing en de operationele procedures voor het bewerkings team<br /> -de pre-productie-en productie omgevingen beheren |
| **On-premises AD Operations-team** | De organisatie die de verschillende on-premises identiteits bronnen beheert, zoals AD-forests, LDAP-directory's, HR-systemen, enzovoort.<br /> -herstel taken uitvoeren die nodig zijn voor de synchronisatie<br /> -De service accounts opgeven die vereist zijn voor synchronisatie<br /> -toegang bieden tot het configureren van Federatie naar Azure AD |
| **IT-ondersteunings Manager** | Een vertegenwoordiger van de IT-ondersteunings organisatie die in het perspectief van de Help Desk invoer kan bieden over de ondersteuning van deze wijziging. |
| **Eigenaar van beveiliging**  | Een vertegenwoordiger van het beveiligings team dat ervoor kan zorgen dat het plan voldoet aan de beveiligings vereisten van uw organisatie. |
| **Technische eigen aren van toepassingen** | Bevat technische eigen aren van de apps en services die worden geïntegreerd met Azure AD. Ze bieden de identiteits kenmerken van de toepassingen die in het synchronisatie proces moeten worden meegenomen. Ze hebben meestal een relatie met CSV-mede werkers. |
| **Zakelijke eigen aren van toepassingen** | Representatieve collega's die de gebruikers ervaring en het nut van deze wijziging in het oogpunt van een gebruiker kunnen invoeren en die eigenaar zijn van het algehele bedrijfs aspect van de toepassing, waaronder het beheren van toegang. |
| **Pilot groep gebruikers** | Gebruikers die zullen testen als onderdeel van hun dagelijkse werk, de test ervaring en feedback geven om de rest van de implementaties te begeleiden. |

### <a name="plan-communications"></a>De communicatie plannen

Effectief zakelijke betrokkenheid en communicatie is de sleutel tot succes. Het is belang rijk om belanghebbenden en eind gebruikers een taak te geven om informatie te verkrijgen en op de hoogte te blijven van het plannen van updates. Informeer iedereen over de waarde van de migratie, wat de verwachte tijd lijnen zijn en hoe u kunt plannen voor eventuele tijdelijke onderbrekingen van uw bedrijf. Gebruik meerdere mogelijkheden, zoals korte sessies, e-mails, een-op-een-vergaderingen, banners en townhalls.

Op basis van de communicatie strategie die u hebt gekozen voor de app, kunt u gebruikers van de in behandeling zijnde downtime eraan herinneren. U moet er ook voor zorgen dat er geen recente wijzigingen of zakelijke gevolgen zijn die vereisen dat de implementatie wordt uitgesteld.

In de volgende tabel ziet u de minimale voorgestelde communicatie om uw belanghebbenden op de hoogte te houden:

**Plan fasen en project strategie**:

| Communicatie      | Doelgroep                                          |
| ------------------ | ------------------------------------------------- |
| Bewustmaking en zakelijke/technische waarde van project | Alle behalve eind gebruikers |
| Aanvragen voor pilot-apps | -Zakelijke eigen aren van apps<br />-Technische eigen aren van apps<br />-Architecten en identiteits team |

**Fase 1-detectie en bereik**:

| Communicatie      | Doelgroep                                          |
| ------------------ | ------------------------------------------------- |
| -Aanvragen voor toepassings gegevens<br />-Resultaat van de oefening van het bereik | -Technische eigen aren van apps<br />-Zakelijke eigen aren van apps |

**Fase 2: apps classificeren en prototype plannen**:

| Communicatie      | Doelgroep                                          |
| ------------------ | ------------------------------------------------- |
| -Resultaat van classificaties en wat betekent dat voor het migratie schema<br />-Voorlopig migratie schema | -Technische eigen aren van apps<br /> -Zakelijke eigen aren van apps |

**Fase 3: migratie en tests plannen**:

| Communicatie      | Doelgroep                                          |
| ------------------ | ------------------------------------------------- |
| -Resultaat van testen van de toepassings migratie | -Technische eigen aren van apps<br />-Zakelijke eigen aren van apps |
| -Melding dat de migratie afkomstig is en een uitleg krijgt van de resulterende ervaring voor eind gebruikers.<br />-Downtime en volledige communicatie, met inbegrip van wat ze nu moeten doen, feedback en hulp krijgen | -Eind gebruikers (en alle andere) |

**Fase 4: beheer en verkrijg inzichten**:

| Communicatie      | Doelgroep                                          |
| ------------------ | ------------------------------------------------- |
| Beschik bare analyse en toegang | -Technische eigen aren van apps<br />-Zakelijke eigen aren van apps |

### <a name="migration-states-communication-dashboard"></a>Communicatie dashboard voor migratie status

Het communiceren van de algemene status van het migratie project is van cruciaal belang, omdat er voortgang wordt weer gegeven en app-eigen aren kunnen helpen bij de migratie om de overstap voor te bereiden. U kunt een eenvoudig dash board samen stellen met behulp van Power BI of andere hulpprogram ma's voor rapportage om inzicht te krijgen in de status van toepassingen tijdens de migratie.

De migratie statussen die u kunt gebruiken, zijn als volgt:

| Migratie statussen       | Actieplan                                   |
| ---------------------- | --------------------------------------------- |
| **Eerste aanvraag** | Zoek de app en neem contact op met de eigenaar voor meer informatie |
| **Evaluatie voltooid** | App-eigenaar evalueert de app-vereisten en retourneert de app-vragen lijst</td>
| **De configuratie wordt uitgevoerd** | De wijzigingen ontwikkelen die nodig zijn voor het beheren van de verificatie voor Azure AD |
| **Testen van configuratie geslaagd** | Evalueer de wijzigingen en authenticeer de app aan de hand van de Azure AD-Tenant testen in de test omgeving |
| **De productie configuratie is voltooid** | Wijzig de configuraties zodat deze overeenkomen met de productie-AD-Tenant en beoordeel de app-verificatie in de test omgeving |
| **Volt ooien/afmelden** | Implementeer de wijzigingen voor de app in de productie omgeving en voer deze uit op basis van de Azure AD-Tenant voor productie |

Dit zorgt ervoor dat app-eigen aren weten wat het migratie-en test schema van de app is wanneer hun apps zijn voor migratie en wat de resultaten zijn van andere apps die al zijn gemigreerd. U kunt ook overwegen koppelingen naar uw Data Base voor fout controle te bieden voor eigen aars om problemen op te lossen voor apps die worden gemigreerd.

### <a name="best-practices"></a>Aanbevolen procedures

Hieronder vindt u de succes verhalen van klanten en partners en aanbevolen procedures:

- [Vijf tips voor het verbeteren van het migratie proces Azure Active Directory](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Five-tips-to-improve-the-migration-process-to-Azure-Active/ba-p/445364) door Patriot Consulting, een lid van ons partner netwerk dat zich richt op de hulp van klanten om micro soft-cloud oplossingen veilig te implementeren.

- [Ontwikkel een strategie voor risico beheer voor uw Azure AD-toepassings migratie](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Develop-a-risk-management-strategy-for-your-Azure-AD-application/ba-p/566488) door Edgile, een partner die zich richt op de oplossingen voor iam en risico beheer.

## <a name="phase-1-discover-and-scope-apps"></a>Fase 1: apps detecteren en bereiken

**Toepassings detectie en-analyse is een fundamentele oefening om u een goed start te geven.** Mogelijk weet u niet alles dat u wilt voorbereiden op de onbekende apps.

### <a name="find-your-apps"></a>Uw apps zoeken

Het eerste beslissings punt in een toepassings migratie is welke apps moeten worden gemigreerd, wat eventueel moet blijven, en welke apps moeten worden overgezet. U kunt altijd de apps die u niet wilt gebruiken in uw organisatie opzeggen. Er zijn verschillende manieren om apps in uw organisatie te vinden. **Zorg ervoor dat u in-ontwikkeling en geplande apps opneemt tijdens het detecteren van apps. gebruik Azure AD voor verificatie in alle toekomstige apps.**

**Active Directory Federation Services (AD FS) gebruiken om een juiste app-inventaris te verzamelen:**

- **Gebruik Azure AD Connect Health.** Als u een Azure AD Premium licentie hebt, kunt u het beste [Azure AD Connect Health](../hybrid/how-to-connect-health-adfs.md) implementeren om het app-gebruik in uw on-premises omgeving te analyseren. U kunt het [AD FS-toepassings rapport](./migrate-adfs-application-activity.md) (preview) gebruiken voor het detecteren van ADFS-toepassingen die kunnen worden gemigreerd en evalueren van de gereedheid van de toepassing die moet worden gemigreerd. Nadat u de migratie hebt voltooid, implementeert u [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery) waarmee u de schaduw kopie in uw organisatie voortdurend kunt bewaken zodra u zich in de Cloud bevindt.

- **AD FS voor het parseren van Logboeken**. Als u geen Azure AD Premium-licenties hebt, kunt u het beste de AD FS-hulpprogram ma's voor het migratie programma ADFS gebruiken op basis van [Power shell.](https://github.com/AzureAD/Deployment-Plans/tree/master/ADFS%20to%20AzureAD%20App%20Migration) Raadpleeg de [hand leiding voor oplossingen](./migrate-adfs-apps-to-azure.md):

[Apps migreren van Active Directory Federation Services (AD FS) naar Azure AD.](./migrate-adfs-apps-to-azure.md)

### <a name="using-other-identity-providers-idps"></a>Andere id-providers (id) gebruiken

Voor andere id-providers (zoals Okta of ping) kunt u de bijbehorende hulpprogram ma's gebruiken om de toepassings voorraad te exporteren. U kunt overwegen om te kijken naar de service principes die zijn geregistreerd op Active Directory die overeenkomen met de web-apps in uw organisatie.

### <a name="using-cloud-discovery-tools"></a>Cloud Discovery-hulpprogram ma's gebruiken

In de cloud omgeving hebt u uitgebreide zicht baarheid nodig, de controle over gegevens reizen en geavanceerde analyses om Cyber bedreigingen te vinden en te bestrijden in al uw Cloud Services. U kunt uw Cloud-app-inventaris verzamelen met behulp van de volgende hulpprogram ma's:

- **Cloud Access Security Broker (CASB**): een [CASB](/cloud-app-security/) werkt normaal gesp roken naast uw firewall om inzicht te krijgen in het gebruik van de Cloud toepassing van uw werk nemers en helpt u bij het beveiligen van uw bedrijfs gegevens tegen Cyber beveiliging bedreigingen. Het CASB-rapport kan u helpen bij het bepalen van de meest gebruikte apps in uw organisatie en de vroege doelen die moeten worden gemigreerd naar Azure AD.

- **Cloud Discovery** -door [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery)te configureren, krijgt u inzicht in het gebruik van de Cloud-app en kunt u niet-goedgekeurde of schaduw it-apps detecteren.

- **Api's** : voor apps die zijn verbonden met de Cloud infrastructuur, kunt u de api's en hulpprogram ma's op deze systemen gebruiken om te beginnen met het maken van een inventaris van gehoste apps. In de Azure-omgeving:

  - Gebruik de cmdlet [Get-website](/powershell/module/servicemanagement/azure.service/get-azurewebsite) om informatie over Azure websites op te halen.

  - Gebruik de cmdlet [Get-AzureRMWebApp](/powershell/module/azurerm.websites/get-azurermwebapp) om informatie over uw Azure-web apps op te halen.
D
  - U kunt alle apps die worden uitgevoerd op micro soft IIS vinden via de Windows-opdracht regel met behulp van [AppCmd.exe](/iis/get-started/getting-started-with-iis/getting-started-with-appcmdexe#working-with-sites-applications-virtual-directories-and-application-pools).

  - Gebruik [toepassingen](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity) en [service-principals](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#serviceprincipal-entity) om informatie te krijgen over een app en een app-exemplaar in een directory in azure AD.

### <a name="using-manual-processes"></a>Hand matige processen gebruiken

Zodra u de hierboven beschreven automatische benaderingen hebt uitgevoerd, hebt u een goede greep voor uw toepassingen. U kunt echter het volgende doen om ervoor te zorgen dat u over voldoende dekking beschikt voor alle gebieden van gebruikers toegang:

- Neem contact op met de verschillende zakelijke eigen aren in uw organisatie om de toepassingen te vinden die in uw organisatie worden gebruikt.

- Voer een hulp programma voor HTTP-inspectie op uw proxy server uit of Analyseer proxy Logboeken om te zien waar verkeer doorgaans wordt gerouteerd.

- Lees de Weblogboeken van de populaire site van de bedrijfs portal om te zien welke koppelingen gebruikers het meest gebruiken.

- Neem contact op met leidinggevenden of andere belang rijke zakelijke leden om ervoor te zorgen dat u de bedrijfskritische apps hebt behandeld.

### <a name="type-of-apps-to-migrate"></a>Type apps dat moet worden gemigreerd

Wanneer u uw apps hebt gevonden, kunt u deze typen apps in uw organisatie identificeren:

- Apps die al moderne verificatie protocollen gebruiken

- Apps die gebruikmaken van verouderde verificatie protocollen die u kiest om te moderniseren

- Apps die gebruikmaken van verouderde verificatie protocollen die u niet wilt moderniseren

- Nieuwe LoB-apps (line-of-Business)

### <a name="apps-that-use-modern-authentication-already"></a>Apps die al gebruikmaken van moderne authenticatie

De al moderne apps zijn het meest waarschijnlijk om te worden verplaatst naar Azure AD. Deze apps maken al gebruik van moderne verificatie protocollen (zoals SAML of OpenID Connect Connect) en kunnen opnieuw worden geconfigureerd voor verificatie met Azure AD.

Naast de opties in de [Azure AD-App-galerie,](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) kunnen dit apps zijn die al bestaan in uw organisatie of apps van derden van een leverancier die geen deel uitmaakt van de Azure AD-galerie ([niet-galerie toepassingen)](./add-application-portal.md).

Verouderde apps die u wilt moderniseren

Voor verouderde apps die u wilt moderniseren, gaat u naar Azure AD voor Core-verificatie en-autorisatie ontgrendelt u alle kracht-en gegevens verrijkingen die de [Microsoft Graph](https://developer.microsoft.com/graph/gallery/?filterBy=Samples,SDKs) en [intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence?rtc=1) te bieden hebben.

U kunt **het beste de verificatie stack code** voor deze toepassingen bijwerken vanuit het verouderde Protocol (zoals Windows-Integrated verificatie, Kerberos-beperkte overdracht, op http-headers gebaseerde verificatie) naar een modern Protocol (zoals SAML of OpenID Connect Connect).

### <a name="legacy-apps-that-you-choose-not-to-modernize"></a>Verouderde apps die u niet wilt moderniseren

Voor bepaalde apps die gebruikmaken van verouderde verificatie protocollen, is het mogelijk dat de verificatie wordt gemodern en niet om zakelijke redenen. Dit zijn onder andere de volgende typen apps:

- Apps die on-premises worden bewaard voor naleving of controle redenen.

- Apps die zijn verbonden met een on-premises identiteit of Federation-provider die u niet wilt wijzigen.

- Apps die zijn ontwikkeld met behulp van on-premises verificatie normen waarvoor u geen plannen hebt om te verplaatsen

Azure AD kan profiteren van de voor delen van deze verouderde apps, omdat u Azure AD-beveiligings-en beheer functies zoals [multi-factor Authentication](../authentication/concept-mfa-howitworks.md), [voorwaardelijke toegang](../conditional-access/overview.md), [identiteits beveiliging](../identity-protection/index.yml), [gedelegeerde toegang tot toepassingen](./access-panel-manage-self-service-access.md)en [toegangs beoordelingen](../governance/manage-user-access-with-access-reviews.md#create-and-perform-an-access-review) voor deze apps kunt inschakelen zonder dat u de app helemaal hoeft aan te raken.

Begin door **deze apps uit te breiden naar de Cloud** [met behulp van](./application-proxy-configure-single-sign-on-password-vaulting.md) eenvoudige verificatie methoden (zoals wachtwoord kluizen), zodat uw gebruikers snel kunnen worden gemigreerd, of via onze [partner integraties](https://azure.microsoft.com/services/active-directory/sso/secure-hybrid-access/) met bezorgings controllers voor toepassingen die u mogelijk al hebt geïmplementeerd.

### <a name="new-line-of-business-lob-apps"></a>Nieuwe LoB-apps (line-of-Business)

U ontwikkelt doorgaans LoB-apps voor intern gebruik in uw organisatie. Als u nieuwe apps in de pijp lijn hebt, raden we u aan het [micro soft Identity-platform](../develop/v2-overview.md) te gebruiken voor het implementeren van OpenID Connect Connect.

### <a name="apps-to-deprecate"></a>Te afschaffing apps

Apps zonder duidelijke eigen aren en duidelijke onderhoud en bewaking geven een beveiligings risico voor uw organisatie. Overweeg het terugnemen van toepassingen wanneer:

- hun **functionaliteit is zeer redundant** met andere systemen • er is **geen bedrijfs eigenaar**

- Er is duidelijk **geen gebruik**.

We raden u **aan om essentiële bedrijfs kritieke toepassingen niet af te stoten**. In dergelijke gevallen kunt u samen werken met zakelijke eigen aars om de juiste strategie te bepalen.

### <a name="exit-criteria"></a>Afsluit criteria

U bent in deze fase geslaagd met:

- Een goed beeld van de systemen in het bereik voor uw migratie (die u buiten gebruik kunt stellen nadat u deze hebt verplaatst naar Azure AD)

- Een lijst met apps die het volgende bevatten:

  - Met welke systemen deze apps verbinding maken
  - Van waar en op welke apparaten gebruikers toegang hebben
  - Of ze worden gemigreerd, afgeschaft of verbonden met [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md).

> [!NOTE]
> U kunt het [werk blad toepassings detectie](https://download.microsoft.com/download/2/8/3/283F995C-5169-43A0-B81D-B0ED539FB3DD/Application%20Discovery%20worksheet.xlsx) downloaden om de toepassingen vast te leggen die u wilt migreren naar Azure AD-verificatie en die u wilt verlaten, maar wilt beheren met behulp van [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md).

## <a name="phase-2-classify-apps-and-plan-pilot"></a>Fase 2: apps classificeren en prototype plannen

Het classificeren van de migratie van uw apps is een belang rijke oefening. Niet elke app moet tegelijkertijd worden gemigreerd en overgezet. Zodra u informatie over elk van de apps hebt verzameld, kunt u overstappen welke apps het eerst moeten worden gemigreerd en wat tijd kan duren.

### <a name="classify-in-scope-apps"></a>Apps in het bereik classificeren

Een manier om dit te zien is langs de assen van kritiekheid, gebruik en levens duur van het bedrijf, die allemaal afhankelijk zijn van meerdere factoren.

### <a name="business-criticality"></a>Bedrijfskritiek

Bedrijfskritischeiteit kan voor elk bedrijf op verschillende dimensies worden uitgevoerd, maar de twee metingen die u moet overwegen, zijn **onderdelen en functionaliteit** en **gebruikers profielen**. Wijs apps met een unieke functionaliteit toe met een hogere punt waarde dan die met redundante of verouderde functionaliteit.

![Een diagram van het spectrum van functies & functionaliteit en gebruikers profielen](media/migrating-application-authentication-to-azure-active-directory-3.jpg)

### <a name="usage"></a>Gebruik

Toepassingen met een **hoog gebruiks nummer** moeten een hogere waarde krijgen dan apps met weinig gebruik. Wijs een hogere waarde toe aan apps met gebruikers van een extern, Executive of beveiligings team. Voor elke app in uw migratie portfolio voert u deze evaluaties uit.

![Een diagram van het spectrum van gebruikers volume en gebruikers breedte](media/migrating-application-authentication-to-azure-active-directory-4.jpg)

Wanneer u waarden hebt vastgesteld voor bedrijfs kritiekheid en gebruik, kunt u de **levens duur** van de toepassing bepalen en een matrix met prioriteit maken. Zie een van de volgende matrix:

![Een driehoek diagram met de relaties tussen gebruik, verwachte levens duur en bedrijfs kritiek](media/migrating-application-authentication-to-azure-active-directory5.jpg)

### <a name="prioritize-apps-for-migration"></a>Prioriteiten voor de migratie van apps bepalen

U kunt ervoor kiezen om de app-migratie te starten met de apps met de laagste prioriteit of door de apps met de hoogste prioriteit op basis van de behoeften van uw organisatie.

In een scenario waarin u mogelijk geen ervaring hebt met het gebruik van Azure AD en identiteits Services, kunt u overwegen om eerst uw apps met de **laagste prioriteit** naar Azure ad te verplaatsen. Zo wordt de impact van uw bedrijf tot een minimum beperkt en kunt u een momentum bouwen. Zodra u deze apps hebt verplaatst en het vertrouwen van de belanghebbende hebt opgedaan, kunt u door gaan met het migreren van de andere apps.

Als er geen duidelijke prioriteit is, kunt u overwegen om de apps die in de [Azure AD-galerie](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) zijn opgenomen eerst te verplaatsen en meerdere id-providers (ADFS of Okta) te ondersteunen, omdat ze gemakkelijker te integreren zijn. Waarschijnlijk zijn deze apps de apps met de **hoogste prioriteit** in uw organisatie. Voor het integreren van uw SaaS-toepassingen met Azure AD hebben we een verzameling [zelf studies](../saas-apps/tutorial-list.md) ontwikkeld waarmee u de configuratie kunt door lopen.

Wanneer u de apps hebt gemigreerd, zal deze app-Bucket met de hoogste prioriteit de grote werk belasting opvolgen. U kunt uiteindelijk de apps met een lagere prioriteit selecteren, omdat ze de kosten niet wijzigen, zelfs als u de deadline hebt verplaatst. Zelfs als u de licentie moet verlengen, geldt een kleine hoeveelheid.

Naast deze classificatie en afhankelijk van de urgentie van de migratie, kunt u ook overwegen om een **migratie schema** op te zetten waarbinnen app-eigen aren hun apps moeten migreren. Aan het einde van dit proces moet u een lijst met alle toepassingen in verzamelingen met prioriteit voor migratie hebben.

### <a name="document-your-apps"></a>Uw apps documenteren

Begin eerst met het verzamelen van belang rijke gegevens over uw toepassingen. Het [werk blad voor toepassings detectie](https://download.microsoft.com/download/2/8/3/283F995C-5169-43A0-B81D-B0ED539FB3DD/Application%20Discovery%20worksheet.xlsx)helpt u bij het snel maken van uw migratie beslissingen en een aanbeveling voor uw bedrijfs groep te krijgen.

Informatie die belang rijk is voor het nemen van uw migratie besluit:

- **App-naam** : wat is deze app bekend als bij het bedrijf?

- **App-type** – is het een SaaS-app van derden? Een aangepaste line-of-Business-Web-app? Een API?

- **Bedrijfskritischeiteit** : is de hoge mate van belang rijkheid? Gebrek? Of ergens anders?

- **Gebruikers toegangs volume** : heeft iedereen toegang tot deze app of slechts een paar personen?

- **Geplande levens duur** – hoe lang duurt deze app? Minder dan zes maanden? Meer dan twee jaar?

- **Huidige ID-provider** : wat is de primaire IDP voor deze app? Of is het afhankelijk van lokale opslag?

- **Verificatie methode** : de app wordt geverifieerd met open standaarden?

- **Of u van plan bent de app-code bij te werken** : de app onder geplande of actieve ontwikkeling?

- **Of u van plan bent de app on-premises te houden** – wilt u de app op de lange termijn van uw Data Center blijven?

- **Of de app afhankelijk is van andere apps of api's** – de app roept momenteel andere apps of api's aan?

- **Of de app zich in de Azure AD-galerie bevindt** , is de app momenteel al geïntegreerd met de [Azure AD-galerie](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)?

Andere gegevens die u later zullen helpen, maar die u niet hoeft te doen om een onmiddellijke migratie beslissing te nemen:

- **App-URL** – waar krijgen gebruikers toegang tot de app?

- **App-beschrijving** : wat is een korte beschrijving van wat de app doet?

- **Eigenaar** van de app: wie in het bedrijf is de belangrijkste haalbaarheids test voor de app?

- **Algemene opmerkingen of opmerkingen** : andere algemene informatie over de app of het eigendom van het bedrijf

Nadat u uw toepassing hebt geclassificeerd en de details hebt gedocumenteerd, moet u ervoor zorgen dat u de bedrijfs eigenaar kunt aanschaffen bij de strategie voor de geplande migratie.

### <a name="plan-a-pilot"></a>Een pilot plannen

De app (s) die u voor de pilot selecteert, moeten de sleutel identiteit en beveiligings vereisten van uw organisatie vertegenwoordigen en u moet een duidelijke aankoop van de toepassings eigenaren hebben. Pilots worden doorgaans uitgevoerd in een afzonderlijke test omgeving. Zie [Aanbevolen procedures voor Pilots](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) op de pagina implementatie plannen.

**Vergeet niet over uw externe partners.** Zorg ervoor dat ze deel nemen aan migratie planningen en testen. Ten slotte moet u ervoor zorgen dat ze toegang krijgen tot de Help Desk als er problemen zijn opgetreden.

### <a name="plan-for-limitations"></a>Beperkingen plannen

Hoewel sommige apps eenvoudig kunnen worden gemigreerd, kunnen anderen langer duren vanwege meerdere servers of instanties. Share point-migratie kan bijvoorbeeld langer duren vanwege aangepaste aanmeldings pagina's.

Veel SaaS-app-leveranciers brengen kosten in rekening voor het wijzigen van de SSO-verbinding. Neem contact met hen op en plan dit.

Azure AD heeft ook [service limieten en beperkingen](../enterprise-users/directory-service-limits-restrictions.md) waar u rekening mee moet houden.

### <a name="app-owner-sign-off"></a>Afmelding van de app-eigenaar

Bedrijfs kritieke en toepassingen die universeel worden gebruikt, hebben mogelijk een groep pilot gebruikers nodig om de app in de test fase te testen. Wanneer u een app hebt getest in de pre-productie-of test omgeving, moet u ervoor zorgen dat de zakelijke eigen aren van de app zich afmelden bij de prestaties voordat de app en alle gebruikers naar productie gebruik van Azure AD voor verificatie worden uitgevoerd.

### <a name="plan-the-security-posture"></a>De beveiligings postuur plannen

Voordat u het migratie proces start, neemt u de tijd om de beveiligings postuur die u wilt ontwikkelen voor uw bedrijfs identiteits systeem, volledig in overweging te nemen. Dit is gebaseerd op het verzamelen van deze waardevolle gegevens sets: **identiteiten, apparaten en locaties die toegang hebben tot uw gegevens.**

### <a name="identities-and-data"></a>Identiteiten en gegevens

De meeste organisaties hebben specifieke vereisten met betrekking tot identiteiten en gegevens bescherming die variëren per branche segment en taak functies binnen organisaties. Raadpleeg de [configuraties voor identiteits-en toegangs apparaten](/microsoft-365/enterprise/microsoft-365-policies-configurations) voor onze aanbevelingen, waaronder een vereiste set [beleids regels voor voorwaardelijke toegang](../conditional-access/overview.md) en gerelateerde mogelijkheden.

U kunt deze informatie gebruiken voor het beveiligen van de toegang tot alle services die zijn geïntegreerd met Azure AD. Deze aanbevelingen zijn afgestemd op de micro soft Secure Score en de [identiteits Score in azure AD](../fundamentals/identity-secure-score.md). De score helpt bij het volgende:

- Objectief meten van het beveiligingspostuur van uw identiteit

- Plannen van verbeteringen aan de identiteitsbeveiliging

- Evalueren van het succes van uw verbeteringen

Dit helpt u ook bij het implementeren van de [vijf stappen voor het beveiligen van uw identiteits infrastructuur](../../security/fundamentals/steps-secure-identity.md). Gebruik de richt lijnen als uitgangs punt voor uw organisatie en pas het beleid aan om te voldoen aan de specifieke vereisten van uw organisatie.

### <a name="who-is-accessing-your-data"></a>Wie heeft toegang tot uw gegevens?

Er zijn twee hoofd categorieën van gebruikers van uw apps en resources die door Azure AD worden ondersteund:

- **Intern:** Werk nemers, contract ANTEN en leveranciers die accounts hebben binnen uw ID-provider. Dit heeft mogelijk verdere draaiing nodig met verschillende regels voor managers of leiders en andere mede werkers.

- **Extern:** Leveranciers, leveranciers, distributeurs of andere zakelijke partners die communiceren met uw organisatie in het kader van een reguliere periode van het bedrijf met [Azure AD B2B-samen werking.](../external-identities/what-is-b2b.md)

U kunt groepen voor deze gebruikers definiëren en deze groepen op verschillende manieren vullen. U kunt ervoor kiezen om een beheerder in staat te stellen leden hand matig toe te voegen aan een groep of door selfservice-groepslid maatschap in te scha kelen. Er kunnen regels worden gemaakt waarmee leden automatisch worden toegevoegd aan groepen op basis van de opgegeven criteria met [dynamische groepen](../enterprise-users/groups-dynamic-membership.md).

Externe gebruikers verwijzen mogelijk ook naar klanten. [Azure AD B2C](../../active-directory-b2c/overview.md)biedt een afzonderlijk product ondersteuning voor klant verificatie. Het valt echter buiten het bereik van dit artikel.

### <a name="devicelocation-used-to-access-data"></a>Apparaat/locatie die wordt gebruikt voor toegang tot gegevens

Het apparaat en de locatie die een gebruiker gebruikt voor toegang tot een app zijn ook belang rijk. Apparaten die fysiek zijn verbonden met uw bedrijfs netwerk, zijn veiliger. Verbindingen van buiten het netwerk via VPN moeten mogelijk worden onderzocht.

![Een diagram van de relatie tussen de locatie van de gebruiker en gegevens toegang](media/migrating-application-authentication-to-azure-active-directory-6.jpg)

Met deze aspecten van resources, gebruikers en apparaten kunt u ervoor kiezen om [voorwaardelijke toegang tot Azure AD](../conditional-access/overview.md) te gebruiken. Voorwaardelijke toegang gaat verder dan gebruikers machtigingen: het is gebaseerd op een combi natie van factoren, zoals de identiteit van een gebruiker of groep, het netwerk waarmee de gebruiker is verbonden, het apparaat en de toepassing die ze gebruiken, en het type gegevens waartoe ze toegang hebben. De toegang die aan de gebruiker wordt verleend, wordt aangepast aan deze bredere set voor waarden.

### <a name="exit-criteria"></a>Afsluit criteria

U bent in deze fase geslaagd wanneer u:

- Ken uw apps
  - De apps die u wilt migreren volledig hebben gedocumenteerd
  - Apps met een prioriteit hebben op basis van de bedrijfs kritiek, het gebruiks volume en de levens duur

- Apps hebben geselecteerd die uw vereisten voor een pilot vertegenwoordigen

- Inkopen van zakelijke eigenaar voor uw prioriteit en strategie

- Inzicht in de behoeften van uw beveiligings postuur en hoe u deze implementeert

## <a name="phase-3-plan-migration-and-testing"></a>Fase 3: migratie en tests plannen

Zodra u de aanschaf van het bedrijf hebt verworven, is de volgende stap het migreren van deze apps naar Azure AD-verificatie.

### <a name="migration-tools-and-guidance"></a>Migratie hulpprogramma's en-richt lijnen

Gebruik de onderstaande hulpprogram ma's en richt lijnen om de stappen te volgen die nodig zijn om uw toepassingen te migreren naar Azure AD:

- **Algemene richt lijnen voor migratie** : gebruik het Witboek, hulpprogram ma's, e-mail sjablonen en sollicitatie vragenlijst in de [Azure AD Apps Migration Toolkit](./migration-resources.md) om uw apps te detecteren, classificeren en migreren.

- **SaaS-toepassingen** – Bekijk onze lijst met [honderden SaaS-app-zelf studies](../saas-apps/tutorial-list.md) en het volledige [implementatie plan voor Azure AD SSO](https://aka.ms/ssodeploymentplan) om het proces te door lopen.

- **Toepassingen die on-premises worden uitgevoerd** : meer informatie [over Azure AD-toepassingsproxy](./application-proxy.md) en het volledige [implementatie plan van Azure AD-toepassingsproxy](https://aka.ms/AppProxyDPDownload) gebruiken om snel aan de slag te gaan.

- **Apps die u ontwikkelt** – Lees onze stapsgewijze [integratie](../develop/quickstart-register-app.md) -en [registratie](../develop/quickstart-register-app.md) richtlijnen.

Na de migratie kunt u ervoor kiezen om communicatie te verzenden die de gebruikers van de geslaagde implementatie informeert en u eraan te herinneren dat er nieuwe stappen moeten worden uitgevoerd.

### <a name="plan-testing"></a>Tests plannen

Tijdens het proces van de migratie bevat uw app mogelijk al een test omgeving die wordt gebruikt tijdens reguliere implementaties. U kunt deze omgeving blijven gebruiken voor het testen van de migratie. Als er op dit moment geen test omgeving beschikbaar is, kunt u er mogelijk een instellen met behulp van Azure App Service of Azure Virtual Machines, afhankelijk van de architectuur van de toepassing. U kunt ervoor kiezen om een afzonderlijke test van Azure AD-Tenant in te stellen om te gebruiken tijdens het ontwikkelen van uw app-configuraties. Deze Tenant wordt in een schone status gestart en wordt niet geconfigureerd om te synchroniseren met een systeem.

U kunt elke app testen door u aan te melden met een test gebruiker en ervoor te zorgen dat alle functionaliteit hetzelfde is als vóór de migratie. Als u tijdens het testen bepaalt dat gebruikers hun [MFA](/azure/active-directory/authentication/howto-mfa-userstates) -of [SSPR](../authentication/tutorial-enable-sspr.md)-instellingen moeten bijwerken of als u deze functionaliteit tijdens de migratie toevoegt, moet u deze toevoegen aan uw communicatie plan voor eind gebruikers. Zie [MFA](https://aka.ms/mfatemplates) en [SSPR](https://aka.ms/ssprtemplates) -communicatie sjablonen voor eind gebruikers.

Nadat u de apps hebt gemigreerd, gaat u naar de [Azure Portal](https://aad.portal.azure.com/) om te testen of de migratie is geslaagd. Volg de onderstaande instructies:

- Selecteer **&gt; alle toepassingen in bedrijfs toepassingen** en zoek uw app in de lijst.

- Selecteer **&gt; gebruikers en groepen beheren** om ten minste één gebruiker of groep aan de app toe te wijzen.

- Selecteer **&gt; voorwaardelijke toegang beheren**. Controleer uw lijst met beleids regels en zorg ervoor dat u de toegang tot de toepassing niet blokkeert met een [beleid voor voorwaardelijke toegang](../conditional-access/overview.md).

Afhankelijk van hoe u uw app configureert, controleert u of SSO goed werkt.

| Verificatietype      | Testen                                             |
| ------------------------ | --------------------------------------------------- |
| **OAuth/OpenID Connect Connect** | Selecteer **zakelijke toepassings &gt; machtigingen** en zorg ervoor dat u hebt ingestemd met de toepassing die in uw organisatie moet worden gebruikt in de gebruikers instellingen voor uw app. |
| **Op SAML gebaseerde SSO** | U kunt de knop [SAML-instellingen testen](./debug-saml-sso-issues.md) vinden onder **eenmalige aanmelding.** |
| **Eenmalige aanmelding op basis van wacht woorden** | Down load en installeer de [MyApps Secure Sign-in extension](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension). Deze uitbrei ding helpt u bij het starten van de Cloud-apps van uw organisatie waarvoor u een SSO-proces moet gebruiken. |

| **[Toepassings proxy](./application-proxy.md)** | Zorg ervoor dat uw connector wordt uitgevoerd en aan uw toepassing is toegewezen. Ga naar de [probleemoplossings gids voor toepassings proxy](./application-proxy-troubleshoot.md) voor verdere ondersteuning. |

### <a name="troubleshoot"></a>Problemen oplossen

Als u problemen ondervindt, raadpleegt u onze [probleemoplossings gids voor apps](../app-provisioning/isv-automatic-provisioning-multi-tenant-apps.md) om hulp te krijgen. U kunt ook de artikelen over het oplossen van problemen bekijken, [problemen met het aanmelden bij op SAML gebaseerde, geconfigureerde apps voor eenmalige aanmelding](/troubleshoot/azure/active-directory/troubleshoot-sign-in-saml-based-apps).

### <a name="plan-rollback"></a>Plan terugdraai actie

Als de migratie mislukt, is de beste strategie om terug te draaien en te testen. Hier volgen de stappen die u kunt ondernemen om migratie problemen te verhelpen:

- **Maak scherm opnamen** van de bestaande configuratie van uw app. U kunt terugkijken als u de app opnieuw moet configureren.

- U kunt ook overwegen **koppelingen naar de verouderde verificatie op te geven**, als er problemen zijn met Cloud verificatie.

- Wijzig voordat u de migratie voltooit **niet uw bestaande configuratie** met de eerdere ID-provider.

- Begin met het migreren van **de apps die ondersteuning bieden voor meerdere id**. Als er iets fout gaat, kunt u altijd overschakelen naar de configuratie van de voorkeurs IdP.

- Zorg ervoor dat uw app-ervaring een **feedback knop** of aanwijzer naar uw **Help Desk** -problemen heeft.

### <a name="exit-criteria"></a>Afsluit criteria

U bent in deze fase geslaagd wanneer u het volgende hebt:

- Bepaald hoe elke app wordt gemigreerd

- De hulpprogram ma's voor migratie zijn gecontroleerd

- Uw tests plannen met inbegrip van test omgevingen en-groepen

- Geplande terugdraai actie

## <a name="phase-4-plan-management-and-insights"></a>Fase 4: beheer en inzichten plannen

Nadat de apps zijn gemigreerd, moet u het volgende controleren:

- Gebruikers kunnen veilig toegang krijgen en beheren

- U kunt de juiste inzichten verkrijgen in het gebruik en de status van apps

We raden u aan de volgende acties uit te voeren die van toepassing zijn voor uw organisatie.

### <a name="manage-your-users-app-access"></a>De toegang tot de app van uw gebruikers beheren

Zodra u de apps hebt gemigreerd, kunt u de gebruikers ervaring op verschillende manieren verrijken

**Apps kunnen worden gedetecteerd**

Ga naar de [MyApps](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)-Portal-ervaring van **uw gebruiker** . Hier hebben ze toegang tot alle apps op basis van de Cloud, apps die u beschikbaar maakt met behulp van [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md), en apps die [toepassings proxy](./application-proxy.md) gebruiken, op voor waarde dat ze machtigingen hebben voor toegang tot deze apps.


U kunt uw gebruikers helpen bij het detecteren van hun apps:

- De bestaande functie voor [eenmalige aanmelding](./view-applications-portal.md) gebruiken om **uw gebruikers aan elke app te koppelen**


- [Self-service toepassingen toegang](./manage-self-service-access.md)tot een app inschakelen en **gebruikers apps toevoegen die u aan de curator hebt**

- [Verberg toepassingen van eind gebruikers](./hide-application-from-user-portal.md) (standaard micro soft-apps of andere apps) om **ervoor te zorgen dat de apps die ze nodig** hebben, beter kunnen worden gedetecteerd

### <a name="make-apps-accessible"></a>Apps toegankelijk maken

**Gebruikers toegang bieden tot apps vanaf hun mobiele apparaten**. Gebruikers hebben toegang tot de MyApps-Portal met intune-Managed browser op hun [iOS](./hide-application-from-user-portal.md) 7,0-of later-of [Android](./hide-application-from-user-portal.md) -apparaten.

Gebruikers kunnen een door **intune beheerde browser** downloaden:

- **Voor Android-apparaten**, vanuit de [Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.intune)

- **Voor Apple-apparaten**, vanuit de [Apple App Store](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) of ze kunnen de [mobiele app mijn apps voor IOS](https://apps.apple.com/us/app/my-apps-azure-active-directory/id824048653) downloaden

**Gebruikers in staat stellen hun apps te openen vanuit een browser extensie.**

Gebruikers kunnen [de uitbrei ding voor beveiligde aanmelding van MyApps](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab) in [Chrome,](https://chrome.google.com/webstore/detail/my-apps-secure-sign-in-ex/ggjhpefgjjfobnfoldnjipclpcfbgbhl) [Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/) of [micro soft Edge](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab) downloaden en apps direct vanaf hun browser balk starten om het volgende te doen:

- **Zoeken naar hun apps en de meest recent gebruikte apps weer geven**

- **Interne url's** die u hebt geconfigureerd in de [toepassings proxy](./application-proxy.md) automatisch converteren naar de juiste externe url's. Uw gebruikers kunnen nu werken met de koppelingen die ze kennen, ongeacht waar ze zich bevinden.

**Laat gebruikers hun apps openen vanuit Office.com.**

Gebruikers kunnen naar [Office.com](https://www.office.com/) gaan om naar **hun apps te zoeken en hun meest recent gebruikte apps** voor hen weer te geven vanaf de locatie waar ze werkzaam zijn.

### <a name="secure-app-access"></a>Toegang tot de app beveiligen

Azure AD biedt een gecentraliseerde toegangs locatie voor het beheren van uw gemigreerde apps. Ga naar de [Azure Portal](https://portal.azure.com/) en schakel de volgende mogelijkheden in:

- **Gebruikers toegang tot apps beveiligen.** [Beleids regels voor voorwaardelijke toegang](../conditional-access/overview.md)of [identiteits beveiliging](../identity-protection/overview-identity-protection.md)inschakelen voor het beveiligen van gebruikers toegang tot toepassingen op basis van de status van het apparaat, de locatie en meer.

- **Automatische inrichting.** Stel [automatische inrichting in van gebruikers](../app-provisioning/user-provisioning.md) met verschillende SaaS-apps van derden waartoe gebruikers toegang moeten hebben. Naast het maken van gebruikers identiteiten, omvat het het onderhoud en de verwijdering van gebruikers identiteiten als status of rollen worden gewijzigd.

- **Beheer** van **gebruikers toegang delegeren** . Schakel, indien van toepassing, selfservice toegang in voor uw apps en *Wijs een zakelijke goed keurder toe om de toegang tot deze apps goed te keuren*. Gebruik [self-service groeps beheer](../enterprise-users/groups-self-service-management.md)voor groepen die zijn toegewezen aan verzamelingen apps.

- **Beheerders toegang delegeren.** Gebruik **Directory-rol** om een beheerdersrol (zoals toepassings beheerder, Cloud toepassings beheerder of toepassings ontwikkelaar) aan uw gebruiker toe te wijzen.

### <a name="audit-and-gain-insights-of-your-apps"></a>Controleren en inzicht krijgen in uw apps

U kunt ook de [Azure Portal](https://portal.azure.com/) gebruiken om al uw apps te controleren vanaf een centrale locatie,

- **Controleer uw app** met behulp van * * bedrijfs toepassingen, audit of toegang tot dezelfde informatie van de [Azure AD Reporting API](../reports-monitoring/concept-reporting-api.md) om te integreren in uw favoriete hulpprogram ma's.

- **De machtigingen voor een app weer geven** met behulp van **bedrijfs toepassingen, machtigingen** voor apps die gebruikmaken van OAuth/OpenID Connect Connect.

- **Meld u aan voor aanmelding** met **bedrijfs toepassingen, aanmeldingen**. Toegang tot dezelfde informatie van de [Azure AD Reporting-API.](../reports-monitoring/concept-reporting-api.md)

- Het **gebruik van uw app visualiseren** vanuit het [Azure AD Power bi-inhouds pakket](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

### <a name="exit-criteria"></a>Afsluit criteria

U bent in deze fase geslaagd wanneer u:

- Beveiligde app-toegang verlenen aan uw gebruikers

- Beheren om te controleren en inzicht te krijgen in de gemigreerde apps

### <a name="do-even-more-with-deployment-plans"></a>Doe nog meer met implementatie plannen

Implementatie plannen begeleiden u bij de bedrijfs waarde, planning, implementatie stappen en het beheer van Azure AD-oplossingen, waaronder scenario's voor het migreren van apps. Ze bieden alles wat u nodig hebt om te beginnen met de implementatie en de waarde van Azure AD-mogelijkheden te verkrijgen. De implementatie handleidingen bevatten inhoud zoals micro soft aanbevolen procedures, communicatie van eind gebruikers, plannings handleidingen, implementaties tappen en meer.

Er zijn veel [implementatie plannen](../fundamentals/active-directory-deployment-plans.md) beschikbaar voor uw gebruik en we maken er altijd meer uit.

### <a name="contact-support"></a>Contact opnemen met ondersteuning

Ga naar de volgende ondersteunings koppelingen om ondersteunings ticket te maken of bij te houden en de status te controleren.

- **Ondersteuning voor Azure:** U kunt [Microsoft ondersteuning](https://azure.microsoft.com/support) aanroepen en een ticket openen voor elke Azure-

Probleem met identiteits implementatie, afhankelijk van uw Enterprise Overeenkomst met micro soft.

- **FastTrack**: als u een Enter prise Mobility and Security (EMS) of Azure AD Premium licenties hebt aangeschaft, komt u in aanmerking voor implementatie ondersteuning van het [FastTrack-programma.](/enterprise-mobility-security/solutions/enterprise-mobility-fasttrack-program)

- **Het product technisch team** samen werken: Als u werkt met een grote klant implementatie met miljoenen gebruikers, hebt u recht op ondersteuning van het Microsoft-account-team of uw cloud oplossingen architect. Op basis van de complexiteit van de implementatie van het project kunt u rechtstreeks met het [team van Azure Identity product engineering werken.](https://aad.portal.azure.com/#blade/Microsoft_Azure_Marketplace/MarketplaceOffersBlade/selectedMenuItemId/solutionProviders)

- **Azure AD-identiteits blog:** Abonneer u op de [Azure AD-identiteits blog](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/bg-p/Identity) om op de hoogte te blijven van alle meest recente product aankondigingen, diep gaande Dives en kaart gegevens die rechtstreeks door het team van de identiteits techniek worden verstrekt.
