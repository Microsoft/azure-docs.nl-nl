---
title: Toepassingsverificatie migreren naar Azure Active Directory
description: In dit whitepaper worden de planning voor en de voordelen van het migreren van uw toepassingsverificatie naar Azure AD belijnd.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 02/05/2021
ms.author: iangithinji
ms.reviewer: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3458f358c12ef33a337e50066e83b6e59273ccf1
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376746"
---
# <a name="migrate-application-authentication-to-azure-active-directory"></a>Toepassingsverificatie migreren naar Azure Active Directory

## <a name="about-this-paper"></a>Over dit artikel

In dit whitepaper worden de planning voor en de voordelen van het migreren van uw toepassingsverificatie naar Azure AD belijnd. Het is ontworpen voor Azure-beheerders en identiteitsprofessionals.

Het proces wordt in vier fasen verdeeld, elk met gedetailleerde plannings- en exitcriteria, en is ontworpen om u te helpen uw migratiestrategie te plannen en te begrijpen hoe Azure AD-verificatie uw organisatiedoelen ondersteunt.

## <a name="introduction"></a>Introductie

Tegenwoordig vereist uw organisatie een groot aantal toepassingen (apps) voor gebruikers om werk gedaan te krijgen. Waarschijnlijk blijft u elke dag apps toevoegen, ontwikkelen of verwijderen. Gebruikers hebben toegang tot deze toepassingen vanaf een groot aantal zakelijke en persoonlijke apparaten en locaties. Ze openen apps op verschillende manieren, waaronder:

- via de startpagina of portal van een bedrijf

- door bladwijzers te maken in hun browsers

- via de URL van een leverancier voor SaaS-apps (Software as a Service)

- koppelingen die rechtstreeks naar de bureaubladen of mobiele apparaten van de gebruiker worden pushen via een MDM-/MAM-oplossing (Mobile Device/Application Management)

Uw toepassingen gebruiken waarschijnlijk de volgende typen verificatie:

- On-premises federatieoplossingen (zoals Active Directory Federation Services (ADFS) en Ping)

- Active Directory (zoals Kerberos-auth en Windows-Integrated Auth)

- Andere cloudoplossingen voor identiteits- en toegangsbeheer (IAM) (zoals Okta of Oracle)

- On-premises webinfrastructuur (zoals IIS en Apache)

- In de cloud gehoste infrastructuur (zoals Azure en AWS)

**Om ervoor te zorgen dat de gebruikers eenvoudig en veilig toegang hebben tot toepassingen, is het uw doel om één set toegangsbesturingselementen en beleidsregels in uw on-premises en cloudomgevingen te hebben.**

[Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) biedt een universeel identiteitsplatform dat uw mensen, partners en klanten één identiteit biedt voor toegang tot de toepassingen die ze willen en samenwerken vanaf elk platform en apparaat.

![Een diagram van Azure Active Directory connectiviteit](media/migrating-application-authentication-to-azure-active-directory-1.jpg)

Azure AD heeft [een volledige suite met mogelijkheden voor identiteitsbeheer.](../fundamentals/active-directory-whatis.md#which-features-work-in-azure-ad) Door uw app-verificatie en autorisatie te standaardiseren voor Azure AD, profiteert u van de voordelen die deze mogelijkheden bieden.

Meer migratieresources vindt u op [https://aka.ms/migrateapps](./migration-resources.md)

## <a name="benefits-of-migrating-app-authentication-to-azure-ad"></a>Voordelen van het migreren van app-verificatie naar Azure AD

Als u app-verificatie naar Azure AD verplaatst, kunt u risico's en kosten beheren, de productiviteit verhogen en voldoen aan de nalevings- en governancevereisten.

### <a name="manage-risk"></a>beheer risico's.

Voor het beveiligen van uw apps moet u een volledig overzicht hebben van alle risicofactoren. Als u uw apps naar Azure AD migreert, worden uw beveiligingsoplossingen geconsolideerd. Met deze kunt u het volgende doen:

- Verbeter beveiligde gebruikerstoegang tot toepassingen en bijbehorende bedrijfsgegevens met behulp van beleidsregels voor voorwaardelijke [toegang,](../conditional-access/overview.md) [Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)en realtime op risico gebaseerde [identiteitsbeveiligingstechnologieën.](../identity-protection/overview-identity-protection.md)

- Bevoorrechte gebruikerstoegang tot uw omgeving beveiligen met [Just-In-Time-beheerderstoegang.](../../azure-resource-manager/managed-applications/request-just-in-time-access.md)

- Gebruik het [multi-tenant, geografisch gedistribueerde ontwerp met hoge](https://cloudblogs.microsoft.com/enterprisemobility/2014/09/02/azure-ad-under-the-hood-of-our-geo-redundant-highly-available-distributed-cloud-directory/)beschikbaarheid van Azure AD voor uw meest kritieke bedrijfsbehoeften.

- Beveilig uw oudere toepassingen met een van onze [veilige hybride toegangspartnerintegraties](https://aka.ms/secure-hybrid-access) die u mogelijk al hebt geïmplementeerd.

### <a name="manage-cost"></a>Kosten beheren

Uw organisatie kan meerdere IAM-oplossingen (Identity Access Management) hebben. Migreren naar één Azure AD-infrastructuur is een kans om de afhankelijkheden van IAM-licenties (on-premises of in de cloud) en infrastructuurkosten te verminderen. In gevallen waarin u mogelijk al hebt betaald voor Azure AD via Microsoft 365-licenties, is er geen reden om de extra kosten van een andere IAM-oplossing te betalen.

**Met Azure AD kunt u de infrastructuurkosten verlagen door:**

- Veilige externe toegang bieden voor on-premises apps met [behulp van Azure AD toepassingsproxy.](./application-proxy.md)

- Koppel apps los van de on-prem-referentiebenadering in uw tenant door Azure AD in te stellen als de vertrouwde universele [id-provider.](../hybrid/plan-connect-user-signin.md#choosing-the-user-sign-in-method-for-your-organization)

### <a name="increase-productivity"></a>Productiviteit verhogen

Economie- en beveiligingsvoordelen stimuleren organisaties om Over te gaan op Azure AD, maar volledige acceptatie en naleving zijn waarschijnlijker als gebruikers er ook voordeel van hebben. Met Azure AD kunt u het volgende doen:

- Verbeter de ervaring [voor single Sign-On (SSO)](./what-is-single-sign-on.md) van eindgebruikers door naadloze en veilige toegang tot elke toepassing, vanaf elk apparaat en elke locatie.

- Gebruik selfservice-IAM-mogelijkheden, zoals [selfservice](../authentication/concept-sspr-howitworks.md) voor wachtwoord opnieuw instellen en [selfservicegroepsbeheer.](../enterprise-users/groups-self-service-management.md)

- Verminder de administratieve overhead door slechts één identiteit voor elke gebruiker te beheren in cloud- en on-premises omgevingen:

  - [Het inrichten van gebruikersaccounts](../app-provisioning/user-provisioning.md) automatiseren (in [de Azure AD-galerie](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)) op basis van Azure AD-identiteiten
  - Toegang tot al uw apps vanuit het deelvenster MyApps in [het Azure Portal ](https://portal.azure.com/)

- Ontwikkelaars in staat stellen om de toegang tot hun apps te beveiligen en de eindgebruikerservaring te verbeteren met behulp van [het Microsoft Identity Platform](../develop/v2-overview.md) met de Microsoft Authentication Library (MSAL).

- Geef uw partners toegang tot cloudbronnen met behulp [van Azure AD B2B-samenwerking.](../external-identities/what-is-b2b.md) Cloudbronnen verwijderen de overhead van het configureren van point-to-point-federatie met uw partners.

### <a name="address-compliance-and-governance"></a>Naleving en governance aanpakken

Zorg voor naleving van wettelijke vereisten door het afdwingen van beleid voor bedrijfstoegang en het bewaken van gebruikerstoegang tot toepassingen en bijbehorende gegevens met behulp van geïntegreerde controlehulpprogramma's en API's. Met Azure AD kunt u aanmeldingen van toepassingen bewaken via rapporten die gebruikmaken van [SIEM-hulpprogramma's (Security Incident and Event Monitoring).](../reports-monitoring/plan-monitoring-and-reporting.md) U kunt de rapporten openen vanuit de portal of API's en programmatisch controleren wie toegang heeft tot uw toepassingen en toegang tot inactieve gebruikers verwijderen via toegangsbeoordelingen.

## <a name="plan-your-migration-phases-and-project-strategy"></a>Uw migratiefasen en projectstrategie plannen

Wanneer technologieprojecten mislukken, wordt dit vaak veroorzaakt door niet-overeenkomende verwachtingen, de juiste belanghebbenden die niet worden betrokken of een gebrek aan communicatie. Zorg ervoor dat het project is geslaagd door het project zelf te plannen.

### <a name="the-phases-of-migration"></a>De fasen van de migratie

Voordat we de hulpprogramma's gaan gebruiken, moet u weten hoe u het migratieproces moet doorlopen. Via verschillende direct-to-customer workshops raden we de volgende vier fasen aan:

![Een diagram van de fasen van de migratie](media/migrating-application-authentication-to-azure-active-directory-2.jpg)

### <a name="assemble-the-project-team"></a>Het projectteam samenstellen

Toepassingsmigratie is een teaminspanning en u moet ervoor zorgen dat u alle essentiële posities hebt ingevuld. Ondersteuning van senior leidinggevenden is belangrijk. Zorg ervoor dat u de juiste set leidinggevenden, zakelijke besluitvormers en deskundigen (SME's) erbij betrekken.

Tijdens het migratieproject kan één persoon meerdere rollen vervullen, of meerdere personen vervullen elke rol, afhankelijk van de grootte en structuur van uw organisatie. Mogelijk bent u ook afhankelijk van andere teams die een belangrijke rol spelen in uw beveiligingslandschap.

De volgende tabel bevat de belangrijkste rollen en hun bijdragen:

| Rol          | Bijdragen                                              |
| ------------- | ---------------------------------------------------------- |
| **Projectmanager** | Projectvolley die verantwoordelijk is voor het begeleiden van het project, waaronder:<br /> - ondersteuning voor leidinggevenden krijgen<br /> - belanghebbenden binnen halen<br /> - Planningen, documentatie en communicatie beheren |
| **Identity Architect/Azure AD-app Administrator** | Ze zijn verantwoordelijk voor het volgende:<br /> - de oplossing ontwerpen in samenwerking met belanghebbenden<br /> - Documenteren van het ontwerp van de oplossing en operationele procedures voor de hand-off aan het operationele team<br /> - de preproductie- en productieomgevingen beheren |
| **On-premises AD-operations-team** | De organisatie die de verschillende on-premises identiteitsbronnen beheert, zoals AD-forests, LDAP-directorieën, HR-systemen, enzovoort.<br /> - hersteltaken uitvoeren die nodig zijn voordat u synchronisatie uitvoert<br /> - Geef de serviceaccounts op die vereist zijn voor synchronisatie<br /> - toegang bieden om federatie te configureren voor Azure AD |
| **IT-ondersteuningsmanager** | Een vertegenwoordiger van de IT-ondersteuningsorganisatie die vanuit het perspectief van de helpdesk input kan geven over de ondersteuning van deze wijziging. |
| **Beveiligingseigenaar**  | Een vertegenwoordiger van het beveiligingsteam die ervoor kan zorgen dat het plan voldoet aan de beveiligingsvereisten van uw organisatie. |
| **Technische eigenaren van toepassingen** | Bevat technische eigenaren van de apps en services die worden geïntegreerd met Azure AD. Ze bieden de identiteitskenmerken van de toepassingen die moeten worden gebruikt in het synchronisatieproces. Ze hebben meestal een relatie met CSV-vertegenwoordigers. |
| **Bedrijfseigenaren van toepassingen** | Representatieve collega's die vanuit het perspectief van een gebruiker invoer kunnen geven over de gebruikerservaring en het nut van deze wijziging en eigenaar zijn van het algehele zakelijke aspect van de toepassing, waaronder het beheren van toegang. |
| **Pilotgroep van gebruikers** | Gebruikers die testen als onderdeel van hun dagelijkse werk, de testervaring en feedback geven om de rest van de implementaties te begeleiden. |

### <a name="plan-communications"></a>De communicatie plannen

Effectieve zakelijke betrokkenheid en communicatie is de sleutel tot succes. Het is belangrijk om belanghebbenden en eindgebruikers de kans te geven om informatie te krijgen en op de hoogte te blijven van planningsupdates. Iedereen informeren over de waarde van de migratie, wat de verwachte tijdlijnen zijn en hoe u een tijdelijke bedrijfsonderbreking kunt plannen. Gebruik meerdere mogelijkheden, zoals sessies, e-mails, een-op-een-vergaderingen, banners en townhalls.

Op basis van de communicatiestrategie die u hebt gekozen voor de app, kunt u gebruikers eraan herinneren dat er downtime in behandeling is. U moet ook controleren of er geen recente wijzigingen of bedrijfseffecten zijn waarvoor de implementatie moet worden uitgesteld.

In de volgende tabel vindt u de minimaal voorgestelde communicatie om uw belanghebbenden op de hoogte te houden:

**Fasen en projectstrategie plannen:**

| Communicatie      | Doelgroep                                          |
| ------------------ | ------------------------------------------------- |
| Bewustzijn en bedrijfs-/technische waarde van project | Alle gebruikers behalve eindgebruikers |
| Verzoek om proef-apps | - App-bedrijfseigenaren<br />- Technische eigenaren van apps<br />- Architecten en identiteitsteam |

**Fase 1: Ontdekken en bereik:**

| Communicatie      | Doelgroep                                          |
| ------------------ | ------------------------------------------------- |
| - Verzoek om toepassingsgegevens<br />- Resultaat van bereikoefening | - Technische eigenaren van apps<br />- App-bedrijfseigenaren |

**Fase 2: apps classificeren en pilot plannen:**

| Communicatie      | Doelgroep                                          |
| ------------------ | ------------------------------------------------- |
| - Resultaat van classificaties en wat dat betekent voor migratieplanning<br />- Voorlopig migratieschema | - Technische eigenaren van apps<br /> - App-bedrijfseigenaren |

**Fase 3– Migratie en testen plannen:**

| Communicatie      | Doelgroep                                          |
| ------------------ | ------------------------------------------------- |
| - Resultaat van het testen van toepassingsmigratie | - Eigenaren van technische apps<br />- App-bedrijfseigenaren |
| - Melding dat de migratie binnenkort wordt gegeven en uitleg over de ervaringen van eindgebruikers.<br />- Uitvaltijd en volledige communicatie, inclusief wat ze nu moeten doen, feedback en hulp krijgen | - Eindgebruikers (en alle andere) |

**Fase 4: beheren en inzichten verkrijgen:**

| Communicatie      | Doelgroep                                          |
| ------------------ | ------------------------------------------------- |
| Beschikbare analyses en toegang tot | - Technische eigenaren van apps<br />- App-bedrijfseigenaren |

### <a name="migration-states-communication-dashboard"></a>Communicatiedashboard voor migratie states

Het communiceren van de algehele status van het migratieproject is van cruciaal belang, omdat dit de voortgang laat zien, en helpt app-eigenaren van wie de apps op de migratie zijn voorbereid om de overstap voor te bereiden. U kunt een eenvoudig dashboard maken met behulp van Power BI of andere rapportagehulpprogramma's om inzicht te bieden in de status van toepassingen tijdens de migratie.

De migratie-staten die u kunt gebruiken, zijn als volgt:

| Migratie-staten       | Actieplan                                   |
| ---------------------- | --------------------------------------------- |
| **Eerste aanvraag** | Zoek de app en neem contact op met de eigenaar voor meer informatie |
| **Evaluatie voltooid** | App-eigenaar evalueert de app-vereisten en retourneert de app-enquête</td>
| **Configuratie wordt uitgevoerd** | De benodigde wijzigingen ontwikkelen voor het beheren van verificatie met Azure AD |
| **Testconfiguratie geslaagd** | De wijzigingen evalueren en de app verifiëren aan de hand van de Azure AD-test-tenant in de testomgeving |
| **Productieconfiguratie geslaagd** | Wijzig de configuraties om te werken met de PRODUCTIE AD-tenant en evalueer de app-verificatie in de testomgeving |
| **Voltooien/aftekenen** | Implementeer de wijzigingen voor de app in de productieomgeving en voer de uit op de Azure AD-productie-tenant |

Dit zorgt ervoor dat app-eigenaren weten wat de planning voor appmigratie en -test is wanneer hun apps beschikbaar zijn voor migratie en wat de resultaten zijn van andere apps die al zijn gemigreerd. U kunt ook overwegen koppelingen naar uw bugtrackerdatabase op te geven zodat eigenaren problemen kunnen melden en bekijken voor apps die worden gemigreerd.

### <a name="best-practices"></a>Aanbevolen procedures

Hier volgen de succesverhalen van onze klanten en partners en voorgestelde best practices:

- [Vijf tips voor het verbeteren van](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Five-tips-to-improve-the-migration-process-to-Azure-Active/ba-p/445364) het migratieproces voor Azure Active Directory door Patriot Consulting, een lid van ons partnernetwerk dat zich richt op het veilig implementeren van Microsoft-cloudoplossingen.

- [Ontwikkel een strategie voor risicobeheer voor de](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Develop-a-risk-management-strategy-for-your-Azure-AD-application/ba-p/566488) migratie van uw Azure AD-toepassing door Edgile, een partner die zich richt op IAM- en risicobeheeroplossingen.

## <a name="phase-1-discover-and-scope-apps"></a>Fase 1: Apps ontdekken en het bereik ervan beheren

**Toepassingsdetectie en -analyse is een fundamentele oefening om u een goede start te geven.** Mogelijk weet u niet alles, dus wees voorbereid op de onbekende apps.

### <a name="find-your-apps"></a>Uw apps zoeken

Het eerste beslissingspunt in een toepassingsmigratie is welke apps moeten worden gemigreerd, welke moeten blijven en welke apps moeten worden afgeschaft. Er is altijd een kans om de apps die u niet in uw organisatie gaat gebruiken, af te bouwen. Er zijn verschillende manieren om apps in uw organisatie te vinden. **Zorg er tijdens het detecteren van apps voor dat u apps in ontwikkeling en geplande apps opeenvoegt. Gebruik Azure AD voor verificatie in alle toekomstige apps.**

**Gebruik Active Directory Federation Services (AD FS) om een juiste app-inventaris te verzamelen:**

- **Gebruik Azure AD Connect Health.** Als u een Azure AD Premium hebt, raden we [](../hybrid/how-to-connect-health-adfs.md) u aan om een Azure AD Connect Health om het app-gebruik in uw on-premises omgeving te analyseren. U kunt het [ADFS-toepassingsrapport](./migrate-adfs-application-activity.md) (preview) gebruiken om ADFS-toepassingen te ontdekken die kunnen worden gemigreerd en om de gereedheid te evalueren van de toepassing die moet worden gemigreerd. Nadat u de migratie hebt doorlopen, implementeert [u Cloud Discovery](/cloud-app-security/set-up-cloud-discovery) waarmee u schaduw-IT in uw organisatie continu kunt bewaken zodra u zich in de cloud hebt.

- **AD FS parseren van logboeken.** Als u geen Azure AD Premium hebt, raden we u aan om de hulpprogramma's voor app-migratie van ADFS naar Azure AD te gebruiken op basis van [PowerShell.](https://github.com/AzureAD/Deployment-Plans/tree/master/ADFS%20to%20AzureAD%20App%20Migration). Raadpleeg [Oplossingshandleiding:](./migrate-adfs-apps-to-azure.md)

[Apps migreren van Active Directory Federation Services (AD FS) naar Azure AD.](./migrate-adfs-apps-to-azure.md)

### <a name="using-other-identity-providers-idps"></a>Andere id-providers (IdP's) gebruiken

Voor andere id-providers (zoals Okta of Ping) kunt u hun hulpprogramma's gebruiken om de toepassingsinventaris te exporteren. U kunt overwegen om te kijken naar serviceprincipes die zijn geregistreerd in Active Directory en die overeenkomen met de web-apps in uw organisatie.

### <a name="using-cloud-discovery-tools"></a>Hulpprogramma's voor clouddetectie gebruiken

In de cloudomgeving hebt u uitgebreide zichtbaarheid, controle over het gegevensverkeer en geavanceerde analyses nodig om cyberbedreigingen in al uw cloudservices te vinden en bestrijden. U kunt de inventaris van uw cloud-apps verzamelen met behulp van de volgende hulpprogramma's:

- **Cloud Access Security Broker (CASB) :** een [CASB](/cloud-app-security/) werkt doorgaans samen met uw firewall om inzicht te bieden in het gebruik van cloudtoepassing van uw werknemers en helpt u uw bedrijfsgegevens te beschermen tegen cyberbeveiligingsbedreigingen. Het CASB-rapport kan u helpen bij het bepalen van de meest gebruikte apps in uw organisatie en de vroege doelen voor migratie naar Azure AD.

- **Cloud Discovery:** als u Cloud Discovery [configureert,](/cloud-app-security/set-up-cloud-discovery)krijgt u inzicht in het gebruik van cloud-apps en kunt u niet-geanctioneerde of schaduw-IT-apps ontdekken.

- **API's:** voor apps die zijn verbonden met de cloudinfrastructuur, kunt u de API's en hulpprogramma's op deze systemen gebruiken om te beginnen met het inventariseren van gehoste apps. In de Azure-omgeving:

  - Gebruik de [cmdlet Get-AzureWebsite](/powershell/module/servicemanagement/azure.service/get-azurewebsite) om informatie over Azure-websites op te halen.

  - Gebruik de [cmdlet Get-AzureRMWebApp](/powershell/module/azurerm.websites/get-azurermwebapp) om informatie over uw Azure-Web Apps.
D
  - U vindt alle apps die worden uitgevoerd op Microsoft IIS vanaf de Windows-opdrachtregel met [ behulp vanAppCmd.exe](/iis/get-started/getting-started-with-iis/getting-started-with-appcmdexe#working-with-sites-applications-virtual-directories-and-application-pools).

  - Gebruik [Toepassingen](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity) en [Service-principals](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#serviceprincipal-entity) om informatie op te halen over een app- en app-exemplaar in een directory in Azure AD.

### <a name="using-manual-processes"></a>Handmatige processen gebruiken

Zodra u de hierboven beschreven geautomatiseerde benaderingen hebt gebruikt, hebt u een goede basis voor uw toepassingen. U kunt echter overwegen het volgende te doen om ervoor te zorgen dat u een goede dekking hebt voor alle gebieden voor gebruikerstoegang:

- Neem contact op met de verschillende bedrijfseigenaren in uw organisatie om de toepassingen te vinden die in uw organisatie worden gebruikt.

- Voer een HTTP-inspectieprogramma uit op uw proxyserver of analyseer proxylogboeken om te zien waar verkeer vaak wordt gerouteerd.

- Bekijk weblogs van populaire bedrijfsportalsites om te zien welke koppelingen gebruikers het meest gebruiken.

- Contact op met leidinggevenden of andere belangrijke leden van het bedrijf om ervoor te zorgen dat u de bedrijfskritieke apps hebt behandeld.

### <a name="type-of-apps-to-migrate"></a>Type apps dat moet worden gemigreerd

Zodra u uw apps hebt gevonden, identificeert u de volgende typen apps in uw organisatie:

- Apps die al gebruikmaken van moderne verificatieprotocollen

- Apps die gebruikmaken van verouderde verificatieprotocollen die u wilt moderniseren

- Apps die gebruikmaken van verouderde verificatieprotocollen die u niet wilt moderniseren

- Nieuwe Line-Of-Business-apps (LoB)

### <a name="apps-that-use-modern-authentication-already"></a>Apps die al gebruikmaken van moderne verificatie

De al gemoderniseerde apps worden waarschijnlijk naar Azure AD verplaatst. Deze apps maken al gebruik van moderne verificatieprotocollen (zoals SAML of OpenID Connect) en kunnen opnieuw worden geconfigureerd voor verificatie met Azure AD.

Naast de opties in de [Azure AD-app-galerie kunnen](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) dit apps zijn die al bestaan in uw organisatie of apps van derden van een leverancier die geen deel uitmaakt van de Azure[AD-galerie (niet-galerietoepassingen).](./add-application-portal.md)

Verouderde apps die u wilt moderniseren

Voor verouderde apps die u wilt moderniseren, ontgrendelt de overstap naar Azure AD voor kernverificatie en autorisatie alle kracht en gegevensrijkheid die de [Microsoft Graph](https://developer.microsoft.com/graph/gallery/?filterBy=Samples,SDKs) en [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence?rtc=1) te bieden hebben.

We raden u aan de **verificatiestackcode** voor deze toepassingen bij te werken van het verouderde protocol (zoals Windows-Integrated-verificatie, beperkte Kerberos-delegering, verificatie op basis van HTTP-headers) naar een modern protocol (zoals SAML of OpenID Connect).

### <a name="legacy-apps-that-you-choose-not-to-modernize"></a>Verouderde apps die u niet wilt moderniseren

Voor bepaalde apps die gebruikmaken van verouderde verificatieprotocollen, is het soms niet de juiste manier om hun verificatie te moderniseren om zakelijke redenen. Dit zijn onder andere de volgende typen apps:

- Apps worden on-premises gehouden vanwege nalevings- of controleredenen.

- Apps die zijn verbonden met een on-premises identiteit of federatieprovider die u niet wilt wijzigen.

- Apps die zijn ontwikkeld met behulp van on-premises verificatiestandaarden die u niet wilt verplaatsen

Azure AD kan geweldige voordelen bieden voor deze verouderde apps, omdat u moderne azure AD-beveiligings- en governancefuncties zoals [](../governance/manage-user-access-with-access-reviews.md#create-and-perform-an-access-review) [Multi-Factor Authentication,](../authentication/concept-mfa-howitworks.md)voorwaardelijke [toegang,](../conditional-access/overview.md)identiteitsbeveiliging, [](./access-panel-manage-self-service-access.md)gedelegeerde toepassingstoegang en toegangsbeoordelingen voor deze apps kunt inschakelen zonder de app aan te raken. [](../identity-protection/index.yml)

Begin met het uitbreiden van deze apps naar de **cloud** met Azure AD [toepassingsproxy](./application-proxy-configure-single-sign-on-password-vaulting.md) met behulp van eenvoudige verificatiemethode (zoals Password Vaulting) om uw gebruikers snel te migreren of via onze partnerintegraties met controllers voor [toepassingslevering](https://azure.microsoft.com/services/active-directory/sso/secure-hybrid-access/) die u mogelijk al hebt geïmplementeerd.

### <a name="new-line-of-business-lob-apps"></a>Nieuwe Line-Of-Business-apps (LoB)

Meestal ontwikkelt u LoB-apps voor het in-house gebruik van uw organisatie. Als u nieuwe apps in de pijplijn hebt, raden we u aan het [Microsoft Identity Platform te](../develop/v2-overview.md) gebruiken om deze te OpenID Connect.

### <a name="apps-to-deprecate"></a>Apps die moeten worden afgeschaft

Apps zonder duidelijke eigenaren en duidelijke onderhoud en bewaking vormen een beveiligingsrisico voor uw organisatie. Overweeg toepassingen af te bouwen wanneer:

- hun **functionaliteit is zeer redundant bij** andere systemen • er is geen **bedrijfseigenaar**

- er is duidelijk **geen gebruik.**

We raden u aan **om toepassingen met een hoge impact, bedrijfskritieke toepassingen, niet af te bouwen.** In dergelijke gevallen kunt u samenwerken met bedrijfseigenaren om de juiste strategie te bepalen.

### <a name="exit-criteria"></a>Criteria voor afsluiten

U bent in deze fase geslaagd met:

- Een goed begrip van de systemen binnen het bereik van uw migratie (die u buiten gebruik kunt plaatsen nadat u bent overgetrokken naar Azure AD)

- Een lijst met apps die het volgende bevat:

  - Met welke systemen deze apps verbinding maken
  - Vanaf waar en op welke apparaten gebruikers toegang hebben tot deze apparaten
  - Of ze worden gemigreerd, afgeschaft of verbonden met [Azure AD Connect.](../hybrid/whatis-azure-ad-connect.md)

> [!NOTE]
> U kunt het werkblad voor [toepassingsdetectie](https://download.microsoft.com/download/2/8/3/283F995C-5169-43A0-B81D-B0ED539FB3DD/Application%20Discovery%20worksheet.xlsx) downloaden om de toepassingen vast te registreren die u wilt migreren naar Azure AD-verificatie en de toepassingen die u wilt verlaten maar beheren met behulp [van Azure AD Connect](../hybrid/whatis-azure-ad-connect.md).

## <a name="phase-2-classify-apps-and-plan-pilot"></a>Fase 2: Apps classificeren en pilot plannen

Het classificeren van de migratie van uw apps is een belangrijke oefening. Niet elke app hoeft tegelijkertijd te worden gemigreerd en overge stappen. Zodra u informatie over elk van de apps hebt verzameld, kunt u rationaliseren welke apps het eerst moeten worden gemigreerd en welke extra tijd kunnen duren.

### <a name="classify-in-scope-apps"></a>Apps binnen het bereik classificeren

Een manier om hier over na te denken, is langs de assen van bedrijfskritiekheid, gebruik en levensduur, die allemaal afhankelijk zijn van meerdere factoren.

### <a name="business-criticality"></a>Bedrijfskritiek

Bedrijfskritiek heeft voor elk bedrijf verschillende dimensies, maar de twee metingen die u moet overwegen, zijn functies en **functionaliteit** en **gebruikersprofielen.** Wijs apps met unieke functionaliteit toe aan een hogere waarde dan apps met redundante of verouderde functionaliteit.

![Een diagram van de spectrums van functies & functionaliteit en gebruikersprofielen](media/migrating-application-authentication-to-azure-active-directory-3.jpg)

### <a name="usage"></a>Gebruik

Toepassingen met **een hoog gebruiksaantal** moeten een hogere waarde krijgen dan apps met een laag gebruik. Wijs een hogere waarde toe aan apps met externe gebruikers, leidinggevenden of beveiligingsteamgebruikers. Voltooi deze evaluaties voor elke app in uw migratieportfolio.

![Een diagram van de spectrums van gebruikersvolume en gebruikers breedte](media/migrating-application-authentication-to-azure-active-directory-4.jpg)

Zodra u waarden hebt bepaald voor bedrijfskritiek en gebruik, kunt u de levensduur van de toepassing bepalen en een matrix met prioriteit maken. Zie een dergelijke matrix hieronder:

![Een driehoeksdiagram met de relaties tussen gebruik, verwachte levensduur en bedrijfskritiek](media/migrating-application-authentication-to-azure-active-directory5.jpg)

### <a name="prioritize-apps-for-migration"></a>Apps prioriteren voor migratie

U kunt ervoor kiezen om de app-migratie te starten met de apps met de laagste prioriteit of apps met de hoogste prioriteit op basis van de behoeften van uw organisatie.

In een scenario waarin u mogelijk geen ervaring hebt met het gebruik van Azure AD- en identiteitsservices, kunt u overwegen eerst uw **apps** met de laagste prioriteit naar Azure AD te verplaatsen. Dit minimaliseert de impact van uw bedrijf en u kunt een enorme impact opbouwen. Zodra u deze apps hebt verplaatst en het vertrouwen van de belanghebbende hebt opgedaan, kunt u doorgaan met het migreren van de andere apps.

Als er geen duidelijke prioriteit is, kunt u overwegen om eerst de apps in de [Azure AD](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) Gallery te verplaatsen en meerdere id-providers (ADFS of Okta) te ondersteunen, omdat deze eenvoudiger te integreren zijn. Waarschijnlijk zijn deze apps de apps met **de hoogste prioriteit** in uw organisatie. Om u te helpen uw SaaS-toepassingen te [](../saas-apps/tutorial-list.md) integreren met Azure AD, hebben we een verzameling zelfstudies ontwikkeld die u helpen bij het configureren.

Wanneer u een deadline hebt voor het migreren van de apps, neemt deze bucket met de hoogste prioriteit de belangrijkste workload over. U kunt uiteindelijk de apps met een lagere prioriteit selecteren, omdat deze de kosten niet wijzigen, ook al hebt u de deadline verplaatst. Zelfs als u de licentie moet verlengen, is dit voor een klein bedrag.

Naast deze classificatie en afhankelijk van de urgentie van uw migratie,  kunt u ook overwegen om een migratieschema op te zetten waarin app-eigenaren hun apps moeten migreren. Aan het einde van dit proces moet u een lijst met alle toepassingen in geprioriteerde buckets voor migratie hebben.

### <a name="document-your-apps"></a>Uw apps documenteren

Begin eerst met het verzamelen van belangrijke details over uw toepassingen. Het [werkblad Toepassingsdetectie](https://download.microsoft.com/download/2/8/3/283F995C-5169-43A0-B81D-B0ED539FB3DD/Application%20Discovery%20worksheet.xlsx)helpt u snel uw migratiebeslissingen te nemen en snel een aanbeveling te krijgen voor uw bedrijfsgroep.

Informatie die belangrijk is voor het nemen van uw migratiebeslissing omvat:

- **App-naam:** hoe wordt deze app voor het bedrijf genoemd?

- **App-type:** is het een SaaS-app van derden? Een aangepaste Line-Of-Business-web-app? Een API?

- **Bedrijfskritiek:** is het hoge belang ervan? Lage? Of ergens ertussenin?

- **Volume van gebruikerstoegang:** heeft iedereen toegang tot deze app of slechts een paar personen?

- **Geplande levensduur:** hoe lang is deze app beschikbaar? Minder dan zes maanden? Meer dan twee jaar?

- **Huidige id-provider:** wat is de primaire id-provider voor deze app? Of is het afhankelijk van lokale opslag?

- **Verificatiemethode:** wordt de app geverifieerd met behulp van open standaarden?

- **Of u van plan bent om de app-code bij** te werken: is de app onder geplande of actieve ontwikkeling?

- **Of u van plan bent om de app on-premises** te houden: wilt u de app op de lange termijn in uw datacenter houden?

- **Of de app afhankelijk is van andere apps of API's:** roept de app momenteel andere apps of API's aan?

- **Of de app zich in de Azure AD-galerie ,** is de app momenteel al geïntegreerd met de [Azure AD-galerie?](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)

Andere gegevens die u later kunnen helpen, maar die u niet direct hoeft te nemen, zijn onder andere:

- **App-URL:** waar kunnen gebruikers toegang krijgen tot de app?

- **App-beschrijving:** wat is een korte beschrijving van wat de app doet?

- **App-eigenaar:** wie in het bedrijf is de belangrijkste POC voor de app?

- **Algemene opmerkingen of opmerkingen:** alle andere algemene informatie over het eigendom van de app of het bedrijf

Nadat u uw toepassing hebt geclassificeerd en de details hebt gedocumenteerd, moet u ervoor zorgen dat de bedrijfseigenaar zich heeft gekocht bij uw geplande migratiestrategie.

### <a name="plan-a-pilot"></a>Een pilot plannen

De app(s) die u voor de test selecteert, moeten de belangrijkste identiteits- en beveiligingsvereisten van uw organisatie vertegenwoordigen en u moet duidelijk zijn gekocht van de eigenaren van de toepassing. De testomgevingen worden doorgaans uitgevoerd in een afzonderlijke testomgeving. Zie [best practices forpilot (Best practices voor luchtvaartpersoneel)](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) op de pagina met implementatieplannen.

**Vergeet uw externe partners niet.** Zorg ervoor dat ze deelnemen aan migratieplanningen en -tests. Zorg er ten slotte voor dat ze toegang hebben tot uw helpdesk als er problemen zijn die problemen veroorzaken.

### <a name="plan-for-limitations"></a>Beperkingen plannen

Hoewel sommige apps eenvoudig te migreren zijn, kan het langer duren voor andere apps worden gemigreerd vanwege meerdere servers of exemplaren. De migratie van SharePoint kan bijvoorbeeld langer duren vanwege aangepaste aanmeldingspagina's.

Veel leveranciers van SaaS-apps brengen kosten in rekening voor het wijzigen van de SSO-verbinding. Neem contact met hen op en plan dit.

Azure AD heeft [ook servicelimieten en -beperkingen](../enterprise-users/directory-service-limits-restrictions.md) die u moet kennen.

### <a name="app-owner-sign-off"></a>App-eigenaar aftekenen

Bedrijfskritieke en universeel gebruikte toepassingen hebben mogelijk een groep testgebruikers nodig om de app in de testfase te testen. Nadat u een app hebt getest in de preproductie- of testomgeving, moet u ervoor zorgen dat eigenaren van apps zich aftekenen op de prestaties vóór de migratie van de app en alle gebruikers voor productiegebruik van Azure AD voor verificatie.

### <a name="plan-the-security-posture"></a>De beveiligingsstatus plannen

Voordat u het migratieproces start, neemt u de tijd om volledig na te denken over de beveiligingsstatus die u wilt ontwikkelen voor uw bedrijfsidentiteitssysteem. Dit is gebaseerd op het verzamelen van deze waardevolle **gegevenssets: identiteiten, apparaten** en locaties die toegang hebben tot uw gegevens.

### <a name="identities-and-data"></a>Identiteiten en gegevens

De meeste organisaties hebben specifieke vereisten voor identiteiten en gegevensbeveiliging die variëren per branchesegment en per functie binnen organisaties. Raadpleeg [configuraties voor identiteits-](/microsoft-365/enterprise/microsoft-365-policies-configurations) en apparaattoegang voor onze aanbevelingen, waaronder een voorgeschreven set beleidsregels voor voorwaardelijke toegang [en](../conditional-access/overview.md) gerelateerde mogelijkheden.

U kunt deze informatie gebruiken om de toegang te beveiligen tot alle services die zijn geïntegreerd met Azure AD. Deze aanbevelingen zijn afgestemd op de Microsoft-beveiligingsscore en [de identiteitsscore in Azure AD.](../fundamentals/identity-secure-score.md) De score helpt bij het volgende:

- Objectief meten van het beveiligingspostuur van uw identiteit

- Plannen van verbeteringen aan de identiteitsbeveiliging

- Evalueren van het succes van uw verbeteringen

Dit helpt u ook bij het implementeren van de [vijf stappen voor het beveiligen van uw identiteitsinfrastructuur.](../../security/fundamentals/steps-secure-identity.md) Gebruik de richtlijnen als uitgangspunt voor uw organisatie en pas het beleid aan om te voldoen aan de specifieke vereisten van uw organisatie.

### <a name="who-is-accessing-your-data"></a>Wie heeft toegang tot uw gegevens?

Er zijn twee hoofdcategorieën van gebruikers van uw apps en resources die door Azure AD worden ondersteund:

- **Intern:** Werknemers, contractanten en leveranciers met accounts binnen uw id-provider. Hiervoor zijn mogelijk meer draaiingen met verschillende regels voor managers of leidinggevenden ten opzichte van andere werknemers nodig.

- **Extern:** Leveranciers, leveranciers, distributeurs of andere zakelijke partners die regelmatig met uw organisatie communiceren met [Azure AD B2B-samenwerking.](../external-identities/what-is-b2b.md)

U kunt groepen voor deze gebruikers definiëren en deze groepen op verschillende manieren vullen. U kunt ervoor kiezen dat een beheerder handmatig leden moet toevoegen aan een groep of u kunt selfservicegroepslidmaatschap inschakelen. Er kunnen regels worden ingesteld die automatisch leden toevoegen aan groepen op basis van de opgegeven criteria met behulp [van dynamische groepen.](../enterprise-users/groups-dynamic-membership.md)

Externe gebruikers kunnen ook verwijzen naar klanten. [Azure AD B2C](../../active-directory-b2c/overview.md)biedt een afzonderlijk product ondersteuning voor klantverificatie. Dit valt echter buiten het bereik van dit document.

### <a name="devicelocation-used-to-access-data"></a>Apparaat/locatie gebruikt voor toegang tot gegevens

Het apparaat en de locatie die een gebruiker gebruikt voor toegang tot een app zijn ook belangrijk. Apparaten die fysiek zijn verbonden met uw bedrijfsnetwerk zijn veiliger. Verbindingen van buiten het netwerk via VPN moeten mogelijk worden onderzocht.

![Een diagram met de relatie tussen gebruikerslocatie en gegevenstoegang](media/migrating-application-authentication-to-azure-active-directory-6.jpg)

Met deze aspecten van resource, gebruiker en apparaat in het achterhoofd, kunt u ervoor kiezen om de mogelijkheden voor [voorwaardelijke](../conditional-access/overview.md) toegang van Azure AD te gebruiken. Voorwaardelijke toegang gaat verder dan gebruikersmachtigingen: deze is gebaseerd op een combinatie van factoren, zoals de identiteit van een gebruiker of groep, het netwerk dat de gebruiker heeft verbonden, het apparaat en de toepassing die ze gebruiken en het type gegevens dat de gebruiker probeert te openen. De toegang die aan de gebruiker wordt verleend, wordt aangepast aan deze uitgebreidere set voorwaarden.

### <a name="exit-criteria"></a>Criteria voor afsluiten

U bent in deze fase geslaagd wanneer u:

- Uw apps kennen
  - De apps die u wilt migreren, volledig gedocumenteerd
  - Apps met prioriteit hebben op basis van bedrijfskritiekheid, gebruiksvolume en levensduur

- Apps hebben geselecteerd die voldoen aan uw vereisten voor een pilot

- Inkopen van bedrijfseigenaar op basis van uw prioriteit en strategie

- Inzicht in uw beveiligingsstatusbehoeften en hoe u deze implementeert

## <a name="phase-3-plan-migration-and-testing"></a>Fase 3: Migratie en testen plannen

Zodra u een bedrijfsinkoop hebt verkregen, bestaat de volgende stap uit het migreren van deze apps naar Azure AD-verificatie.

### <a name="migration-tools-and-guidance"></a>Hulpprogramma's en richtlijnen voor migratie

Gebruik de onderstaande hulpprogramma's en richtlijnen om de precieze stappen te volgen die nodig zijn om uw toepassingen naar Azure AD te migreren:

- **Algemene richtlijnen voor migratie:** gebruik het whitepaper, de hulpprogramma's, e-mailsjablonen en toepassingen in de Azure AD Apps Migration [Toolkit](./migration-resources.md) om uw apps te ontdekken, classificeren en migreren.

- **SaaS-toepassingen:** bekijk onze lijst met honderden zelfstudies voor [SaaS-apps](../saas-apps/tutorial-list.md) en het volledige [azure AD SSO-implementatieplan](https://aka.ms/ssodeploymentplan) om het end-to-end-proces te doorlopen.

- **Toepassingen die on-premises worden uitgevoerd:** leer alles over de [Azure AD-toepassingsproxy](./application-proxy.md) en gebruik het volledige [Azure AD toepassingsproxy implementatieplan](https://aka.ms/AppProxyDPDownload) om snel aan de haal te gaan.

- **Apps die u ontwikkelt:** lees onze stapsgewijs richtlijnen voor [integratie](../develop/quickstart-register-app.md) [en](../develop/quickstart-register-app.md) registratie.

Na de migratie kunt u ervoor kiezen om communicatie te verzenden naar de gebruikers over de geslaagde implementatie en hen eraan te herinneren dat ze nieuwe stappen moeten volgen.

### <a name="plan-testing"></a>Testen plannen

Tijdens het migratieproces heeft uw app mogelijk al een testomgeving die wordt gebruikt tijdens reguliere implementaties. U kunt deze omgeving blijven gebruiken voor migratietests. Als een testomgeving momenteel niet beschikbaar is, kunt u er mogelijk een instellen met behulp van Azure App Service of Azure Virtual Machines, afhankelijk van de architectuur van de toepassing. U kunt ervoor kiezen om een afzonderlijke Azure AD-testten tenant in te stellen voor gebruik bij het ontwikkelen van uw app-configuraties. Deze tenant start met een schone status en is niet geconfigureerd voor synchronisatie met een systeem.

U kunt elke app testen door u aan te melden met een testgebruiker en ervoor te zorgen dat alle functionaliteit hetzelfde is als vóór de migratie. Als u tijdens het testen bepaalt dat gebruikers hun [MFA-](/azure/active-directory/authentication/howto-mfa-userstates) of [SSPR-instellingen](../authentication/tutorial-enable-sspr.md)moeten bijwerken, of als u deze functionaliteit tijdens de migratie toevoegt, moet u dat toevoegen aan uw communicatieplan voor eindgebruikers. Zie [MFA-](https://aka.ms/mfatemplates) en [SSPR-communicatiesjablonen](https://aka.ms/ssprtemplates) voor eindgebruikers.

Nadat u de apps hebt gemigreerd, gaat u naar de [Azure Portal](https://aad.portal.azure.com/) om te testen of de migratie is geslaagd. Volg de onderstaande instructies:

- Selecteer **Bedrijfstoepassingen &gt; Alle toepassingen** en zoek uw app in de lijst.

- Selecteer **Gebruikers en groepen beheren &gt; om** ten minste één gebruiker of groep aan de app toe te wijzen.

- Selecteer **&gt; Voorwaardelijke toegang beheren.** Controleer uw lijst met beleidsregels en zorg ervoor dat u de toegang tot de toepassing niet blokkeert met een [beleid voor voorwaardelijke toegang.](../conditional-access/overview.md)

Controleer of eenmalige aanmelding goed werkt, afhankelijk van hoe u uw app configureert.

| Verificatietype      | Testen                                             |
| ------------------------ | --------------------------------------------------- |
| **OAuth/OpenID Connect** | Selecteer **Bedrijfstoepassingen &gt; Machtigingen** en zorg ervoor dat u hebt ingestemd met de toepassing die in uw organisatie moet worden gebruikt in de gebruikersinstellingen voor uw app. |
| **Op SAML gebaseerde SSO** | Gebruik de [knop TEST SAML Settings](./debug-saml-sso-issues.md) onder Single **Sign-On.** |
| **Eenmalige aanmelding op basis van een wachtwoord** | Download en installeer de [myApps-extensie voor veilig aanmelden.](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension) Met deze extensie kunt u alle cloud-apps van uw organisatie starten waarvoor u een SSO-proces moet gebruiken. |

| **[toepassingsproxy](./application-proxy.md)** | Zorg ervoor dat de connector wordt uitgevoerd en is toegewezen aan uw toepassing. Ga naar [toepassingsproxy gids voor probleemoplossing](./application-proxy-troubleshoot.md) voor meer hulp. |

### <a name="troubleshoot"></a>Problemen oplossen

Als u problemen hebt, raadpleegt u onze gids voor het oplossen van [problemen met](../app-provisioning/isv-automatic-provisioning-multi-tenant-apps.md) apps voor hulp. U kunt ook onze artikelen over probleemoplossing bekijken. Zie Problemen bij het aanmelden bij op SAML gebaseerde apps die zijn geconfigureerd voor [een aanmelding.](/troubleshoot/azure/active-directory/troubleshoot-sign-in-saml-based-apps)

### <a name="plan-rollback"></a>Terugdraaien plannen

Als uw migratie mislukt, is de beste strategie om terug te draaien en te testen. Dit zijn de stappen die u kunt ondernemen om migratieproblemen te verhelpen:

- **Maak schermopnamen** van de bestaande configuratie van uw app. U kunt teruggaan als u de app opnieuw moet configureren.

- U kunt ook overwegen koppelingen naar de verouderde verificatie **op te geven** als er problemen zijn met cloudverificatie.

- Voordat u de migratie voltooit, **moet u uw bestaande configuratie met** de eerdere id-provider niet wijzigen.

- Begin met het migreren van **de apps die ondersteuning bieden voor meerdere id-ps.** Als er iets fout gaat, kunt u altijd de configuratie van de gewenste IdP wijzigen.

- Zorg ervoor dat uw app-ervaring een **feedbackknop of** aanwijzers heeft naar problemen met **uw helpdesk.**

### <a name="exit-criteria"></a>Criteria voor afsluiten

In deze fase kunt u het volgende doen:

- Bepalen hoe elke app wordt gemigreerd

- De migratiehulpprogramma's gecontroleerd

- Uw testen gepland, inclusief testomgevingen en groepen

- Gepland terugdraaien

## <a name="phase-4-plan-management-and-insights"></a>Fase 4: Beheer en inzichten plannen

Zodra apps zijn gemigreerd, moet u ervoor zorgen dat:

- Gebruikers kunnen veilig toegang krijgen tot en beheren

- U kunt de juiste inzichten krijgen in het gebruik en de status van de app

We raden u aan de volgende acties uit te voeren die geschikt zijn voor uw organisatie.

### <a name="manage-your-users-app-access"></a>De toegang tot de app van uw gebruikers beheren

Nadat u de apps hebt gemigreerd, kunt u de gebruikerservaring op verschillende manieren verrijken

**Apps detecteerbaar maken**

**Wijs uw gebruiker** naar de [MyApps-portal-ervaring.](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension) Hier hebben ze toegang tot alle cloud-apps, apps die u beschikbaar maakt met behulp van [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md)en apps met behulp van [toepassingsproxy](./application-proxy.md) mits ze machtigingen hebben voor toegang tot deze apps.


U kunt uw gebruikers begeleiden bij het ontdekken van hun apps:

- Gebruik de [functie Bestaande een aanmelding om](./view-applications-portal.md) uw gebruikers te koppelen aan een **app**


- [Selfservicetoepassingstoegang tot een](./manage-self-service-access.md)app inschakelen en **gebruikers apps laten toevoegen die u cureren**

- [Toepassingen verbergen voor eindgebruikers (standaard Microsoft-apps](./hide-application-from-user-portal.md) of andere apps) om de apps die ze nodig hebben **beter vindbaar te maken**

### <a name="make-apps-accessible"></a>Apps toegankelijk maken

**Gebruikers toegang geven tot apps vanaf hun mobiele apparaten.** Gebruikers hebben toegang tot de MyApps-portal met een door Intune beheerde browser op hun [iOS](./hide-application-from-user-portal.md) 7.0- of hoger- of [Android-apparaten.](./hide-application-from-user-portal.md)

Gebruikers kunnen een door **Intune beheerde browser downloaden:**

- **Voor Android-apparaten,** uit de [Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.intune)

- **Voor Apple-apparaten**, via de [Apple App Store](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) of ze kunnen de Mijn apps mobiele app [voor iOS downloaden](https://apps.apple.com/us/app/my-apps-azure-active-directory/id824048653)

**Laat gebruikers hun apps openen vanuit een browserextensie.**

Gebruikers kunnen [de myApps-extensie](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab) voor beveiligde aanmelding downloaden in [Chrome,](https://chrome.google.com/webstore/detail/my-apps-secure-sign-in-ex/ggjhpefgjjfobnfoldnjipclpcfbgbhl) [FireFox](https://addons.mozilla.org/firefox/addon/access-panel-extension/) of [Microsoft Edge](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab) en apps direct vanuit hun browserbalk starten voor het volgende:

- **Zoek naar hun apps en hun meest recent gebruikte apps worden weergegeven**

- **Converteert automatisch interne URL's** die u hebt geconfigureerd in [toepassingsproxy](./application-proxy.md) naar de juiste externe URL's. Uw gebruikers kunnen nu werken met de koppelingen die ze kennen, waar ze ook zijn.

**Laat gebruikers hun apps openen vanuit Office.com.**

Gebruikers kunnen naar [Office.com](https://www.office.com/) om naar hun apps te zoeken en hun meest recent gebruikte **apps** voor hen te laten zien, op de plek waar ze werken.

### <a name="secure-app-access"></a>App-toegang beveiligen

Azure AD biedt een centrale toegangslocatie voor het beheren van uw gemigreerde apps. Ga naar de [Azure Portal](https://portal.azure.com/) schakel de volgende mogelijkheden in:

- **Gebruikerstoegang tot apps beveiligen.** Schakel [beleid voor voorwaardelijke toegang](../conditional-access/overview.md)of [Identiteitsbeveiliging](../identity-protection/overview-identity-protection.md)in om gebruikerstoegang tot toepassingen te beveiligen op basis van de apparaattoestand, locatie en meer.

- **Automatische inrichting.** Automatische inrichting [instellen van gebruikers met](../app-provisioning/user-provisioning.md) verschillende SaaS-apps van derden die gebruikers moeten openen. Naast het maken van gebruikersidentiteiten, omvat dit het onderhoud en de verwijdering van gebruikersidentiteiten wanneer de status of rollen veranderen.

- **Gebruikerstoegangsbeheer** **delegeren.** Schakel waar nodig selfservicetoepassingstoegang tot uw apps in en wijs een zakelijke goedkeurder toe om *toegang tot deze apps goed te keuren.* Selfservice [voor groepsbeheer gebruiken voor](../enterprise-users/groups-self-service-management.md)groepen die zijn toegewezen aan verzamelingen apps.

- **Beheerderstoegang delegeren.** directoryrol **gebruiken om** een beheerdersrol (zoals Toepassingsbeheerder, Cloud Toepassingsbeheerder of Toepassingsontwikkelaar) aan uw gebruiker toe te wijzen.

### <a name="audit-and-gain-insights-of-your-apps"></a>Uw apps controleren en er inzichten in verkrijgen

U kunt ook de [Azure Portal](https://portal.azure.com/) om al uw apps te controleren vanaf een centrale locatie,

- **Controleer uw app met** behulp van **Bedrijfstoepassingen, Controle of toegang tot dezelfde informatie uit de [Rapportage-API](../reports-monitoring/concept-reporting-api.md) van Azure AD om deze te integreren in uw favoriete hulpprogramma's.

- **Bekijk de machtigingen voor een app** met **bedrijfstoepassingen, machtigingen** voor apps met OAuth/OpenID Connect.

- **Aanmeldingsinzichten verkrijgen** met **Bedrijfstoepassingen, Aanmeldingen.** Toegang tot dezelfde informatie uit de [Rapportage-API van Azure AD.](../reports-monitoring/concept-reporting-api.md)

- **Het gebruik van uw app visualiseren vanuit** het [Azure AD Power BI-inhoudspakket](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

### <a name="exit-criteria"></a>Criteria voor afsluiten

U bent in deze fase geslaagd wanneer u:

- Beveiligde app-toegang bieden aan uw gebruikers

- Beheren om de gemigreerde apps te controleren en inzichten te verkrijgen

### <a name="do-even-more-with-deployment-plans"></a>Nog meer doen met implementatieplannen

Implementatieplannen helpen u bij de bedrijfswaarde, planning, implementatiestappen en het beheer van Azure AD-oplossingen, inclusief app-migratiescenario's. Ze brengen alles samen wat u nodig hebt om Te implementeren en waarde te verkrijgen uit de mogelijkheden van Azure AD. De implementatiehandleidingen bevatten inhoud zoals aanbevolen best practices van Microsoft, communicatie van eindgebruikers, planningshandleidingen, implementatiestappen, testgevallen en meer.

Er [zijn veel](../fundamentals/active-directory-deployment-plans.md) implementatieplannen beschikbaar voor uw gebruik en we maken er altijd meer!

### <a name="contact-support"></a>Contact opnemen met ondersteuning

Ga naar de volgende ondersteuningskoppelingen om een ondersteuningsticket te maken of bij te houden en de status te controleren.

- **Ondersteuning voor Azure:** U kunt een [Microsoft-ondersteuning](https://azure.microsoft.com/support) en een ticket openen voor elke Azure-omgeving

Probleem met identiteitsimplementatie, afhankelijk van uw Enterprise Agreement met Microsoft.

- **FastTrack:** als u Enterprise Mobility and Security (EMS) of Azure AD Premium-licenties hebt aangeschaft, komt u in aanmerking voor implementatieondersteuning van het [FastTrack-programma.](/enterprise-mobility-security/solutions/enterprise-mobility-fasttrack-program)

- **Betrek het Product Engineering-team:** Als u met miljoenen gebruikers aan een grote klantimplementatie werkt, hebt u recht op ondersteuning van het Microsoft-account-team of uw Cloud Oplossingsarchitect. Op basis van de complexiteit van de implementatie van het project kunt u rechtstreeks samenwerken met het [Azure Identity Product Engineering-team.](https://aad.portal.azure.com/#blade/Microsoft_Azure_Marketplace/MarketplaceOffersBlade/selectedMenuItemId/solutionProviders)

- **Azure AD Identity-blog:** Abonneer u op de [Azure AD Identity-blog](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/bg-p/Identity) om op de hoogte te blijven van alle meest recente productaankondigingen, diepgaande informatie en roadmapinformatie die rechtstreeks door het identity engineering-team wordt verstrekt.
