---
title: Technische mogelijkheden voor beveiliging in Azure-Microsoft Azure
description: Inleiding tot beveiligings Services in azure waarmee u uw gegevens, resources en toepassingen in de cloud kunt beveiligen.
services: security
author: TerryLanfear
manager: rkarlin
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.date: 02/04/2021
ms.author: terrylan
ms.openlocfilehash: 54be25317717bcf98b9e296d5c9ed59643cd334e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105044750"
---
# <a name="azure-security-technical-capabilities"></a>Technische mogelijkheden van Azure-beveiliging
Dit artikel bevat een inleiding tot beveiligings Services in azure waarmee u uw gegevens, resources en toepassingen in de cloud kunt beveiligen en voldoen aan de beveiligings behoeften van uw bedrijf.

## <a name="azure-platform"></a>Azure-platform

[Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) is een Cloud platform dat bestaat uit infra structuur-en toepassings Services, met geïntegreerde gegevens Services en geavanceerde analyses, en hulpprogram ma's en services voor ontwikkel aars, die worden gehost in de open bare Cloud data centers van micro soft. Klanten gebruiken Azure voor veel verschillende capaciteiten en scenario's, van basis berekeningen, netwerken en opslag, tot mobiele en web-app-Services, op volledige Cloud scenario's zoals Internet of Things, en kunnen worden gebruikt met open-source technologieën en worden geïmplementeerd als hybride Cloud of worden gehost in het Data Center van een klant. Azure biedt Cloud technologie als bouw stenen waarmee bedrijven kosten kunnen besparen, snel kunnen worden geautomatiseerd en systemen proactief kunnen worden beheerd. Wanneer u IT-assets bouwt op of migreert naar een Cloud provider, bent u afhankelijk van de mogelijkheden van die organisatie om uw toepassingen en gegevens te beschermen met de services en de besturings elementen die ze bieden om de beveiliging van uw cloud-gebaseerde assets te beheren.

Microsoft Azure is de enige Cloud Computing provider die beschikt over een veilig, consistent toepassings platform en een infra structuur-as-a-service waarmee teams kunnen werken binnen hun verschillende Cloud vaardig heden en-niveaus van project complexiteit, met geïntegreerde gegevens Services en analyses waarmee informatie wordt gedetecteerd van gegevens waar deze zich bevinden, kunt u in zowel micro soft als niet-micro soft-platformen open frameworks en hulpprogram ma's, waarmee u de mogelijkheid biedt om de Cloud met on-premises te integreren en Azure-Cloud Services te implementeren in on-premises data centers. Als onderdeel van de micro soft-vertrouwde Cloud vertrouwen klanten op Azure voor een toonaangevende beveiliging, betrouw baarheid, naleving, privacy en het enorme netwerk van mensen, partners en processen om organisaties in de cloud te ondersteunen.

Met Microsoft Azure kunt u het volgende doen:

- Versnel innovatie met de Cloud.

- Power business-beslissingen & apps met inzichten.

- Bouw vrije en implementeer overal.

- Bescherm hun bedrijf.

## <a name="security-technical-capabilities-to-fulfill-your-responsibility"></a>Technische mogelijkheden voor beveiliging om aan uw verantwoordelijkheid te voldoen

Microsoft Azure biedt services die u helpen bij het voldoen aan uw behoeften op het gebied van beveiliging, privacy en naleving. De volgende afbeelding bevat een uitleg van de verschillende Azure-Services die beschikbaar zijn om een veilige en compatibele toepassings infrastructuur te maken op basis van industrie normen.

![Beschik bare technische mogelijkheden voor beveiliging-grote afbeelding](./media/technical-capabilities/azure-security-technical-capabilities-fig1.png)

## <a name="manage-and-control-identity-and-user-access"></a>Identiteits-en gebruikers toegang beheren en controleren

Met Azure kunt u zakelijke en persoonlijke gegevens beveiligen door gebruikers identiteiten en referenties te beheren en de toegang te beheren.

### <a name="azure-active-directory"></a>Azure Active Directory

Met de oplossingen voor identiteits-en toegangs beheer van micro soft kunt u de toegang tot toepassingen en resources in het bedrijfs centrum en in de Cloud beveiligen, waardoor er extra validatie niveaus, zoals multi-factor Authentication en beleid voor voorwaardelijke toegang. Het controleren van verdachte activiteiten via geavanceerde beveiligingsrapportage, controle en waarschuwingen, helpt om potentiële beveiligingsproblemen te verminderen. [Azure Active Directory Premium](../../active-directory/fundamentals/active-directory-whatis.md) biedt eenmalige aanmelding voor duizenden Cloud-apps en toegang tot web-apps die u on-premises uitvoert.

Voor delen van de beveiliging van Azure Active Directory (Azure AD) zijn de mogelijkheid om het volgende te doen:

- Maak en beheer één enkele identiteit voor elke gebruiker in uw hybride onderneming, zodat gebruikers, groepen en apparaten gesynchroniseerd blijven.

- Bieden van eenmalige aanmelding toegang tot uw toepassingen, waaronder duizenden vooraf geïntegreerde SaaS-apps.

- Toegangsbeveiliging tot toepassingen inschakelen door Multi-Factor Authentication op basis van regels af te dwingen voor zowel on-premises toepassingen als cloudtoepassingen.

- U kunt veilige externe toegang tot on-premises webtoepassingen inrichten via Azure AD-toepassingsproxy.

De [Azure Active Directory Portal](https://aad.portal.azure.com/) is beschikbaar als onderdeel van de Azure Portal. Vanuit dit dash board krijgt u een overzicht van de status van uw organisatie en kunt u eenvoudig de Directory, gebruikers of toegang tot de toepassing beheren.

![Azure Active Directory](./media/technical-capabilities/azure-security-technical-capabilities-fig2.png)

Hier volgen de belangrijkste mogelijkheden van Azure Identity Management:

- Eenmalige aanmelding

- Meervoudige verificatie

- Beveiligings bewaking, waarschuwingen en rapporten op basis van machine learning

- Identiteits-en toegangs beheer van consumenten

- Apparaatregistratie

- Privileged Identity Management

- Identiteitsbeveiliging

#### <a name="single-sign-on"></a>Eenmalige aanmelding

[Eenmalige aanmelding (SSO)](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) betekent dat u toegang hebt tot alle toepassingen en bronnen die u nodig hebt om zaken te doen, door zich slechts één keer aan te melden met één gebruikers account. Wanneer u bent aangemeld, hebt u toegang tot alle toepassingen die u nodig hebt zonder dat u een tweede keer hoeft te verifiëren (Typ bijvoorbeeld een wacht woord).

Veel organisaties zijn afhankelijk van SaaS-toepassingen (Software as a Service), zoals Microsoft 365, box en Sales Force voor productiviteit van eind gebruikers. In het verleden moesten IT-mede werkers in elke SaaS-toepassing afzonderlijke gebruikers accounts maken en bijwerken, en moeten gebruikers een wacht woord onthouden voor elke SaaS-toepassing.

[Azure AD breidt on-premises Active Directory uit in de Cloud](../../active-directory/manage-apps/what-is-single-sign-on.md), zodat gebruikers hun primaire organisatie-account kunnen gebruiken om zich niet alleen aan te melden bij hun apparaten in het domein en bedrijfs bronnen, maar ook voor alle web-en SaaS-toepassingen die nodig zijn voor hun taak.

Gebruikers hoeven niet alleen meerdere sets met gebruikers namen en wacht woorden te beheren, toepassings toegang kan automatisch worden ingericht of ongedaan gemaakt op basis van organisatie groepen en hun status als werk nemer. [Azure AD introduceert beveiligings-en toegangs beheer functies](../../active-directory/manage-apps/view-applications-portal.md) waarmee u de toegang van gebruikers tot de SaaS-toepassingen centraal kunt beheren.

#### <a name="multi-factor-authentication"></a>Meervoudige verificatie

[Azure AD multi-factor Authentication (MFA)](../../active-directory/authentication/concept-mfa-howitworks.md) is een authenticatie methode waarvoor het gebruik van meer dan één verificatie methode is vereist en waarmee een kritieke tweede beveiligingslaag wordt toegevoegd aan gebruikers aanmeldingen en trans acties. [MFA helpt](../../active-directory/authentication/concept-mfa-howitworks.md) de toegang tot gegevens en toepassingen te beschermen terwijl de vraag naar gebruikers wordt gevergaderd voor een eenvoudig aanmeldings proces. Het biedt sterke authenticatie via verschillende verificatie opties: telefonische oproep, tekst bericht of mobiele app-melding of verificatie code en OAuth-tokens van derden.

#### <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Beveiligings bewaking, waarschuwingen en rapporten op basis van machine learning

Beveiligings bewaking en waarschuwingen en rapporten op basis van machine learning waarmee inconsistente toegangs patronen worden geïdentificeerd, kunnen u helpen bij het beveiligen van uw bedrijf. U kunt de toegangs-en gebruiks rapporten van Azure Active Directory gebruiken om inzicht te krijgen in de integriteit en beveiliging van de directory van uw organisatie. Met deze informatie kan een Directory-beheerder beter bepalen waar mogelijke beveiligings Risico's zich kunnen voordoen, zodat ze adequaat kunnen plannen om deze Risico's te beperken.

In de Azure Portal of via de [Azure Active Directory-Portal](https://aad.portal.azure.com/)worden [rapporten](../../active-directory/reports-monitoring/overview-reports.md) op de volgende manieren gecategoriseerd:

- Afwijkende rapporten: bevatten aanmeldings gebeurtenissen die afwijkend zijn aangetroffen. Ons doel is om u op de hoogte te stellen van deze activiteit en u in staat te stellen te bepalen of een gebeurtenis verdacht is.

- Geïntegreerde toepassings rapporten: bieden inzicht in hoe Cloud toepassingen worden gebruikt in uw organisatie. Azure Active Directory biedt integratie met duizenden Cloud toepassingen.

- Fout rapporten: duiden op fouten die kunnen optreden bij het inrichten van accounts voor externe toepassingen.

- Gebruikersspecifieke rapporten – apparaat weer geven en activiteiten gegevens voor een specifieke gebruiker ondertekenen.

- Activiteiten logboeken: bevatten een record van alle gecontroleerde gebeurtenissen in de afgelopen 24 uur, in de afgelopen 7 dagen of de afgelopen 30 dagen, en groeps activiteiten wijzigen, wacht woord opnieuw instellen en registratie-activiteit.

#### <a name="consumer-identity-and-access-management"></a>Identiteits-en toegangs beheer van consumenten

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) is een Maxi maal beschik bare, wereld wijde service voor identiteits beheer voor consumenten toepassingen die worden geschaald naar honderden miljoenen identiteiten. De service kan worden geïntegreerd in zowel mobiele als webplatforms. Uw klanten kunnen zich bij al uw toepassingen aanmelden via verschillende aanpasbare methoden met hun bestaande sociale accounts of door nieuwe aanmeldingsgegevens te maken.

In het verleden hebben ontwikkel aars van toepassingen die [gebruikers wilden aanmelden en aanmelden](../../active-directory-b2c/overview.md) bij hun toepassingen, hun eigen code geschreven. Bovendien gebruikten ze on-premises databases of systemen om gebruikersnamen en wachtwoorden op te slaan. Azure Active Directory B2C biedt uw organisatie een betere manier om identiteits beheer van consumenten te integreren in toepassingen met behulp van een beveiligd, op standaarden gebaseerd platform en een grote set Extensible-beleids regels.

Wanneer u Azure Active Directory B2C gebruikt, kunnen uw consumenten zich registreren voor uw toepassingen met hun bestaande sociale accounts (Facebook, Google, Amazon, LinkedIn) of door nieuwe referenties te maken (e-mail adres en wacht woord of gebruikers naam en wacht woord).

#### <a name="device-registration"></a>Apparaatregistratie

[Registratie van Azure ad-apparaten](../../active-directory/devices/overview.md) is de basis voor op apparaten gebaseerde Scenario's voor [voorwaardelijke toegang](../../active-directory/devices/overview.md) . Wanneer een apparaat is geregistreerd, biedt Azure AD-apparaatregistratie het apparaat een identiteit die wordt gebruikt om het apparaat te verifiëren wanneer de gebruiker zich aanmeldt. Het geverifieerde apparaat en de kenmerken van het apparaat kunnen vervolgens worden gebruikt voor het afdwingen van beleid voor voorwaardelijke toegang voor toepassingen die worden gehost in de Cloud en on-premises.

In combi natie met een oplossing voor [Mobile Device Management (MDM)](https://www.microsoft.com/itshowcase/Article/Content/588/Mobile-device-management-at-Microsoft) , zoals intune, worden de kenmerken van het apparaat in azure Active Directory bijgewerkt met aanvullende informatie over het apparaat. Zo kunt u regels voor voorwaardelijke toegang maken waarmee de toegang wordt afgedwongen van apparaten om te voldoen aan uw normen voor beveiliging en naleving.

#### <a name="privileged-identity-management"></a>Privileged Identity Management

Met [Azure Active Directory (AD) privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md) kunt u uw bevoorrechte identiteiten en toegang tot resources in azure AD en andere micro soft-onlineservices zoals Microsoft 365 of Microsoft intune beheren, controleren en bewaken.

Soms moeten gebruikers bevoegde bewerkingen uitvoeren in azure of Microsoft 365 resources of andere SaaS-apps. Dit betekent vaak dat organisaties hun permanente privileged Access in azure AD moeten opgeven. Dit is een groeiend beveiligings risico voor resources die in de cloud worden gehost, omdat organisaties niet voldoende kunnen controleren wat gebruikers met hun beheerders bevoegdheden doen. Als een gebruikers account met bevoorrechte toegang is aangetast, kan dit ook van invloed zijn op de algehele beveiliging van de Cloud. Azure AD Privileged Identity Management helpt dit risico op te lossen.

Met Azure AD Privileged Identity Management kunt u het volgende doen:

- Zien welke gebruikers Azure AD-beheerders zijn

- Op aanvraag ' just-in-time ' beheerders toegang tot micro soft online services zoals Microsoft 365 en intune inschakelen

- Rapporten over beheerders toegangs geschiedenis en wijzigingen in beheerders toewijzingen ophalen

- Waarschuwingen ontvangen over toegang tot een bevoorrechte rol

#### <a name="identity-protection"></a>Identiteitsbeveiliging

[Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md) is een beveiligings service die een geconsolideerde weer gave biedt voor risico detecties en mogelijke beveiligings problemen die van invloed zijn op de identiteiten van uw organisatie. Identiteits beveiliging maakt gebruik van de bestaande Azure Active Directory afwijkingen detectie mogelijkheden (beschikbaar via de afwijkende activiteiten van Azure AD) en introduceert nieuwe typen risico detectie waarmee afwijkingen in realtime kunnen worden gedetecteerd.

## <a name="secure-resource-access"></a>Toegang tot beveiligde bronnen

Toegangs beheer in azure wordt gestart vanuit een facturerings perspectief. De eigenaar van een Azure-account, toegankelijk via de [Azure-Accountcentrum](https://account.windowsazure.com/subscriptions), is de account beheerder (AA). Abonnementen zijn een container voor facturering, maar ze fungeren ook als een beveiligings grens: elk abonnement heeft een service beheerder (SA) die Azure-resources in dit abonnement kan toevoegen, verwijderen en wijzigen met behulp van de Azure Portal. De standaard-SA van een nieuw abonnement is de AA, maar AA kan de SA in de Azure-accountcentrum wijzigen.

![Beveiligde toegang tot bronnen in azure](./media/technical-capabilities/azure-security-technical-capabilities-fig3.png)

Abonnementen hebben ook een koppeling met een map. De map definieert een aantal gebruikers. Deze kunnen gebruikers zijn van het werk of de school waarmee de Directory is gemaakt, of ze kunnen externe gebruikers (micro soft-accounts) zijn. Abonnementen zijn toegankelijk voor een subset van de Directory-gebruikers die zijn toegewezen als service beheerder (SA) of Co-Administrator (CA). de enige uitzonde ring hierop is dat micro soft-accounts (voorheen Windows Live ID), om verouderde redenen, kunnen worden toegewezen als SA of CA zonder dat ze in de Directory aanwezig zijn.

Beveiligings gerichte bedrijven moeten zich richten op het geven van werk nemers de exacte machtigingen die ze nodig hebben. Te veel machtigingen kunnen een account bloot stellen aan kwaadwillende personen. Te weinig machtigingen betekenen dat werk nemers hun werk niet efficiënt kunnen uitvoeren. Met [op rollen gebaseerd toegangs beheer van Azure (Azure RBAC)](../../role-based-access-control/overview.md) kunt u dit probleem oplossen door het bieden van nauw keurig toegang tot Azure.

![Beveiligde toegang tot bron](./media/technical-capabilities/azure-security-technical-capabilities-fig4.png)

Met op rollen gebaseerd toegangsbeheer van Azure kunt u taken scheiden binnen uw team en alleen de mate van toegang verlenen aan gebruikers die nodig is om de taken uit te voeren. In plaats van iedereen onbeperkte machtigingen in uw Azure-abonnement of-resources te geven, kunt u alleen bepaalde acties toestaan. Gebruik bijvoorbeeld Azure RBAC om een werk nemer virtuele machines in een abonnement te laten beheren, terwijl een andere SQL-data bases binnen hetzelfde abonnement kan beheren.

![Toegang tot beveiligde bronnen met behulp van Azure RBAC](./media/technical-capabilities/azure-security-technical-capabilities-fig5.png)

## <a name="data-security-and-encryption"></a>Gegevensbeveiliging en -versleuteling

Een van de sleutels voor gegevens beveiliging in de Cloud is een Accounting voor de mogelijke statussen waarin uw gegevens zich kunnen voordoen en welke besturings elementen beschikbaar zijn voor die status. Voor de best practices voor Azure Data Security en versleuteling gelden de aanbevelingen voor de statussen van de volgende gegevens.

- Inactief: Dit omvat alle opslag objecten, containers en typen van informatie die statisch op fysieke media aanwezig zijn, op de magnetische of optische schijf.
- Transit: wanneer gegevens worden overgebracht tussen onderdelen, locaties of Program ma's, zoals via het netwerk, via een service bus (van on-premises naar de Cloud en vice versa, waaronder hybride verbindingen zoals ExpressRoute), of tijdens een invoer-en uitvoer proces, wordt het gezien als bewegingen.

### <a name="encryption-at-rest"></a>Versleuteling 'at rest'

Versleuteling in rust wordt uitvoerig besproken in [Azure Data Encryption-at-rest](encryption-atrest.md).

### <a name="encryption-in-transit"></a>Versleuteling in-transit

Het beveiligen van gegevens in transit moet essentieel deel uitmaken van uw strategie voor gegevens beveiliging. Omdat de gegevens naar de andere locaties worden verplaatst, is de algemene aanbeveling dat u altijd SSL/TLS-protocollen gebruikt voor het uitwisselen van gegevens tussen verschillende locaties. In sommige gevallen wilt u mogelijk het volledige communicatie kanaal tussen uw on-premises en de Cloud infrastructuur isoleren met behulp van een virtueel particulier netwerk (VPN).

Als u gegevens wilt verplaatsen tussen uw on-premises infra structuur en Azure, moet u rekening houden met de juiste veiligheids maatregelen zoals HTTPS of VPN.

Gebruik [Azure site-to-site VPN](../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)voor organisaties die de toegang moeten beveiligen vanaf meerdere werk stations die zich on-premises naar Azure bevinden.

Gebruik [punt-naar-site-VPN](../../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md)voor organisaties die de toegang moeten beveiligen vanaf een werk station dat zich on-premises naar Azure bevindt.

Grotere gegevens sets kunnen worden verplaatst via een speciale WAN-verbinding met hoge snelheid, zoals [ExpressRoute](https://azure.microsoft.com/services/expressroute/). Als u ervoor kiest om ExpressRoute te gebruiken, kunt u de gegevens op toepassings niveau ook versleutelen met behulp van [SSL/TLS](https://web.archive.org/web/20150221085231/http://support.microsoft.com:80/kb/257591) of andere protocollen voor extra beveiliging.

Als u werkt met Azure Storage via de Azure Portal, worden alle trans acties uitgevoerd via HTTPS. [Opslag rest API](/rest/api/storageservices/) via https kan ook worden gebruikt om te communiceren met [Azure Storage](https://azure.microsoft.com/services/storage/) en [Azure SQL database](https://azure.microsoft.com/services/sql-database/).

Organisaties die gegevens in de overdracht niet kunnen beveiligen, zijn gevoeliger voor [man-in-the-middle-aanvallen](/previous-versions/office/skype-server-2010/gg195821(v=ocs.14)), [afluis teren](/previous-versions/office/skype-server-2010/gg195641(v=ocs.14))en het overnemen van sessies. Deze aanvallen kunnen de eerste stap zijn om toegang te krijgen tot vertrouwelijke gegevens.

Meer informatie over de Azure VPN-optie vindt u in het artikel [planning en ontwerp voor VPN gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="enforce-file-level-data-encryption"></a>Gegevens versleuteling op bestands niveau afdwingen

[Azure RMS](/azure/information-protection/what-is-azure-rms) maakt gebruik van beleids regels voor versleuteling, identiteit en verificatie om uw bestanden en e-mail te beveiligen. Azure RMS werkt op meerdere apparaten: telefoons, Tablets en Pc's door zowel binnen uw organisatie als buiten uw organisatie te beveiligen. Deze mogelijkheid is mogelijk omdat Azure RMS een beveiligings niveau toevoegt dat bij de gegevens blijft, zelfs wanneer het de grenzen van uw organisatie verlaat.

Wanneer u Azure RMS gebruikt om uw bestanden te beveiligen, gebruikt u gestandaardiseerde crypto grafie met volledige ondersteuning van [FIPS 140-2](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.140-2.pdf). Wanneer u Azure RMS gebruikt voor gegevens bescherming, hebt u de zekerheid dat de beveiliging bij het bestand blijft, zelfs als dit wordt gekopieerd naar een opslag die niet onder het beheer van de service valt, zoals een Cloud-opslag. Hetzelfde gebeurt voor bestanden die worden gedeeld via e-mail, het bestand wordt beveiligd als bijlage bij een e-mail bericht met instructies voor het openen van de beveiligde bijlage.
Bij het plannen van Azure RMS aanneming, raden we het volgende aan:

- Installeer de [app RMS sharing](/azure/information-protection/rms-client/sharing-app-windows). Deze app is geïntegreerd met Office-toepassingen door een Office-invoeg toepassing te installeren, zodat gebruikers bestanden eenvoudig rechtstreeks kunnen beveiligen.

- Toepassingen en services configureren ter ondersteuning van Azure RMS

- [Aangepaste sjablonen](/azure/information-protection/configure-policy-templates) maken die overeenkomen met uw bedrijfs vereisten. Bijvoorbeeld: een sjabloon voor de belangrijkste geheime gegevens die moeten worden toegepast in alle e-mails met de meeste geheime geheimen.

Organisaties die een zwakke [gegevens classificatie](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) en bestands beveiliging hebben, zijn mogelijk gevoeliger voor het lekken van gegevens. Zonder de juiste bestands beveiliging kunnen organisaties geen zakelijke inzichten verkrijgen, controleren op misbruik en schadelijke toegang tot bestanden voor komen.

> [!Note]
> Meer informatie over Azure RMS vindt u in het artikel [aan de slag met Azure Rights Management](/azure/information-protection/requirements).

## <a name="secure-your-application"></a>Uw toepassing beveiligen
Hoewel Azure verantwoordelijk is voor de beveiliging van de infra structuur en het platform waarop uw toepassing wordt uitgevoerd, is het uw verantwoordelijkheid om uw toepassing zelf te beveiligen. Met andere woorden, u moet uw toepassings code en-inhoud op een veilige manier ontwikkelen, implementeren en beheren. Als u dit niet doet, kan uw toepassings code of-inhoud nog steeds kwetsbaar zijn voor bedreigingen.

### <a name="web-application-firewall"></a>Web Application Firewall
[Web Application firewall (WAF)](../../web-application-firewall/ag/ag-overview.md) is een functie van [Application Gateway](../../application-gateway/overview.md) waarmee uw webtoepassingen gecentraliseerd worden beschermd tegen veelvoorkomende aanvallen en beveiligings problemen.

Web Application Firewall is gebaseerd op regels uit de [Core Rule Set 3.0 of 2.2.9 van OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project). Webtoepassingen zijn in toenemende mate het doel van aanvallen die gebruikmaken van veelvoorkomende bekende beveiligingsproblemen. Veelvoorkomende aanvallen zijn hierbij onder andere aanvallen met SQL-injecties en aanvallen via scripting op meerdere sites. Het kan een hele uitdaging zijn om dergelijke aanvallen in toepassingscode te voorkomen en dit kan tevens veel onderhoud, patching en controle vereisen op meerdere lagen van de toepassingstopologie. Een gecentraliseerde firewall voor webtoepassingen maakt het beveiligingsbeheer veel eenvoudiger en biedt toepassingsbeheerders meer veiligheid tegen bedreigingen of aanvallen. Een WAF-oplossing kan ook sneller reageren op een beveiligingsrisico door een patch voor een bekend beveiligingsprobleem toe te passen op een centrale locatie in plaats van elke afzonderlijke webtoepassing te beveiligen. Bestaande toepassingsgateways kunnen eenvoudig worden geconverteerd naar een toepassingsgateway met Web Application Firewall.

Hieronder ziet u enkele voorbeelden van veelvoorkomende beveiligingsproblemen waartegen Web Application Firewall bescherming biedt:

- Beveiliging tegen SQL-injecties

- Beveiliging tegen scripting op meerdere sites

- Beveiliging tegen veelvoorkomende aanvallen via internet, zoals opdrachtinjectie, het smokkelen van HTTP-aanvragen, het uitsplitsen van HTTP-antwoorden en aanvallen waarbij een extern bestand wordt ingesloten

- Beveiliging tegen schendingen van het HTTP-protocol

- Beveiliging tegen afwijkingen van het HTTP-protocol, zoals een gebruikersagent voor de host en Accept-headers die ontbreken

- Beveiliging tegen bots, crawlers en scanners

- Detectie van veelvoorkomende onjuiste configuraties van toepassingen (dat wil zeggen Apache, IIS, enzovoort)

> [!Note]
> Zie de volgende [kern regel sets](../../web-application-firewall/ag/ag-overview.md)voor een gedetailleerde lijst met regels en hun beveiligingen:

Azure biedt ook verschillende gebruiks vriendelijke functies waarmee u zowel binnenkomend als uitgaand verkeer voor uw app kunt beveiligen. Met Azure kunnen klanten hun toepassings code ook beveiligen door extern geboden functionaliteit te bieden om uw webtoepassing te scannen op beveiligings problemen.

- [Azure Active Directory authenticatie instellen voor uw app](https://azure.microsoft.com/blog/azure-websites-authentication-authorization/)

- [Verkeer naar uw app beveiligen door Transport Layer Security (TLS/SSL)-HTTPS in te scha kelen](../../app-service/configure-ssl-bindings.md)

  - [Alle binnenkomend verkeer via HTTPS-verbinding afdwingen](http://microsoftazurewebsitescheatsheet.info/)

  - [Strikte transport beveiliging (HSTS) inschakelen](http://microsoftazurewebsitescheatsheet.info/#enable-http-strict-transport-security-hsts)

- [De toegang tot uw app beperken op basis van het IP-adres van de client](http://microsoftazurewebsitescheatsheet.info/#filtering-traffic-by-ip)

- [Beperk de toegang tot uw app op basis van het gedrag van de client, aanvraag frequentie en gelijktijdigheid](http://microsoftazurewebsitescheatsheet.info/#dynamic-ip-restrictions)

- [Uw web-app-code scannen op beveiligings problemen met behulp van Tinfoil Security scanning](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)

- [Wederzijdse TLS-verificatie configureren om client certificaten te vereisen om verbinding te maken met uw web-app](../../app-service/app-service-web-configure-tls-mutual-auth.md)

- [Een client certificaat configureren voor gebruik vanuit uw app om veilig verbinding te maken met externe bronnen](https://azure.microsoft.com/blog/using-certificates-in-azure-websites-applications/)

- [Standaard server koppen verwijderen om te voor komen dat hulpprogram ma's uw app met een vinger afdruk](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)

- [Uw app veilig verbinden met resources in een particulier netwerk met behulp van punt-naar-site-VPN](../../app-service/web-sites-integrate-with-vnet.md)

- [Uw app veilig verbinden met resources in een particulier netwerk met behulp van Hybride verbindingen](../../app-service/app-service-hybrid-connections.md)

Azure App Service gebruikt dezelfde antimalware-oplossing die wordt gebruikt door Azure Cloud Services en Virtual Machines. Raadpleeg onze [malware-documentatie](antimalware.md)voor meer informatie over dit onderwerp.

## <a name="secure-your-network"></a>Uw netwerk beveiligen
Microsoft Azure bevat een robuuste netwerk infrastructuur ter ondersteuning van uw toepassings-en service connectiviteits vereisten. De netwerk verbinding is mogelijk tussen bronnen in azure, tussen on-premises en Azure gehoste resources, en naar en van Internet en Azure.

Met de [Azure-netwerk infrastructuur](/previous-versions/azure/virtual-machines/windows/infrastructure-example) kunt u Azure-resources veilig met elkaar verbinden met [virtuele netwerken (VNets)](../../virtual-network/virtual-networks-overview.md). Een VNet is een weer gave van uw eigen netwerk in de Cloud. Een VNet is een logische isolatie van het Azure-Cloud netwerk dat specifiek is voor uw abonnement. U kunt VNets verbinding maken met uw on-premises netwerken.

![Uw netwerk beveiligen (beveiligen)](./media/technical-capabilities/azure-security-technical-capabilities-fig6.png)

Als u toegangs beheer op basis van een netwerk niveau (op basis van IP-adres en TCP-of UDP-protocollen) nodig hebt, kunt u [netwerk beveiligings groepen](../../virtual-network/virtual-network-vnet-plan-design-arm.md)gebruiken. Een netwerk beveiligings groep (NSG) is een elementaire stateful pakket filtering firewall waarmee u de toegang kunt beheren op basis van een [5-tuple](https://www.techopedia.com/definition/28190/5-tuple).

Azure-netwerken bieden ondersteuning voor de mogelijkheid om het routerings gedrag voor netwerk verkeer op uw virtuele Azure-netwerken aan te passen. U kunt dit doen door door de [gebruiker gedefinieerde routes](../../virtual-network/virtual-networks-udr-overview.md) te configureren in Azure.

[Geforceerde tunneling](https://www.petri.com/azure-forced-tunneling) is een mechanisme dat u kunt gebruiken om ervoor te zorgen dat uw services geen verbinding met apparaten op Internet mogen initiëren.

Azure biedt ondersteuning voor speciale WAN-koppelings connectiviteit met uw on-premises netwerk en een Azure-Virtual Network met [ExpressRoute](../../expressroute/expressroute-introduction.md). De koppeling tussen Azure en uw site maakt gebruik van een speciale verbinding die niet via het open bare Internet gaat. Als uw Azure-toepassing wordt uitgevoerd in meerdere data centers, kunt u [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) gebruiken om aanvragen van gebruikers op intelligente wijze te routeren in verschillende exemplaren van de toepassing. U kunt ook verkeer routeren naar services die niet worden uitgevoerd in azure als ze toegankelijk zijn via internet.

Azure biedt ook ondersteuning voor persoonlijke en veilige verbindingen met uw PaaS-bronnen (bijvoorbeeld Azure Storage en SQL Database) vanuit uw Azure Virtual Network met de [persoonlijke Azure-koppeling](../../private-link/private-link-overview.md). PaaS-resource is toegewezen aan een [persoonlijk eind punt](../../private-link/private-endpoint-overview.md) in uw virtuele netwerk. De koppeling tussen het persoonlijke eind punt in uw virtuele netwerk en uw PaaS-resource maakt gebruik van het micro soft backbone-netwerk en gaat niet over op het open bare Internet. U hoeft uw service niet langer bloot te stellen aan het openbare internet. U kunt ook een persoonlijke Azure-koppeling gebruiken om toegang te krijgen tot Azure gehoste klanten-en partner services in uw virtuele netwerk.  Daarnaast kunt u met Azure private link uw eigen [persoonlijke koppelings service](../../private-link/private-link-service-overview.md) maken in uw virtuele netwerk en deze privé leveren aan uw klanten in hun virtuele netwerken. Het instellen en gebruiken van Azure Private Link is consistent voor Azure PaaS-services, services die eigendom zijn van klanten en gedeelde partnerservices.

## <a name="virtual-machine-security"></a>Beveiliging van virtuele machines

Met [Azure virtual machines](../../virtual-machines/index.yml) kunt u een breed scala aan computer oplossingen implementeren op een flexibele manier. Met ondersteuning voor micro soft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP en Azure BizTalk Services kunt u elke workload en elke taal implementeren op vrijwel elk besturings systeem.

Met Azure kunt u [antimalware-software](antimalware.md) van beveiligings leveranciers gebruiken, zoals micro soft, Symantec, Trend Micro en Kaspersky, om uw virtuele machines te beschermen tegen schadelijke bestanden, adware en andere bedreigingen.

Micro soft antimalware voor Azure Cloud Services en Virtual Machines is een real-time beschermings functie waarmee u virussen, spyware en andere schadelijke software kunt identificeren en verwijderen. Micro soft antimalware biedt Configureer bare waarschuwingen wanneer bekende schadelijke of ongewenste software probeert zichzelf te installeren of uit te voeren op uw Azure-systemen.

[Azure backup](../../backup/backup-overview.md) is een schaal bare oplossing die uw toepassings gegevens beveiligt met een kapitaal investering van nul en minimale bedrijfs kosten. Toepassings fouten kunnen uw gegevens beschadigen en menselijke fouten kunnen leiden tot fouten in uw toepassingen. Met Azure Backup worden uw virtuele machines met Windows en Linux beveiligd.

[Azure site Recovery](../../site-recovery/site-recovery-overview.md) helpt de replicatie, failover en herstel van werk belastingen en apps te organiseren, zodat deze beschikbaar zijn vanaf een secundaire locatie als uw primaire locatie uitvalt.

## <a name="ensure-compliance-cloud-services-due-diligence-checklist"></a>Zorg voor naleving: controle lijst voor Cloud Services met verval datum

Micro soft heeft [de controle lijst voor de Cloud Services](https://aka.ms/cloudchecklist.download) -overdenking ontwikkeld om organisaties te helpen om te zorgen dat de redenen zijn voor de overstap naar de Cloud. Het biedt een structuur voor een organisatie van elke omvang en type, persoonlijke bedrijven en organisaties uit de publieke sector, inclusief de overheid op alle niveaus en non-profit ondernemingen, om hun eigen prestaties, service, gegevens beheer en bedrijfs doelstellingen en-vereisten te identificeren. Hierdoor kunnen ze de aanbiedingen van andere Cloud serviceproviders vergelijken en uiteindelijk de basis vormen voor een Cloud service overeenkomst.

De controle lijst bevat een framework waarin component-by-component wordt uitgelijnd met een nieuwe internationale standaard voor Cloud service overeenkomsten, ISO/IEC 19086. Deze standaard biedt een uniforme set van overwegingen voor organisaties om hen te helpen bij het nemen van beslissingen over de acceptatie van de Cloud en het maken van een gemeen schappelijke grond voor het vergelijken van Cloud service-aanbiedingen.

Met de controle lijst wordt een grondige gecontroleerd verplaatst naar de Cloud, waardoor er gestructureerde richt lijnen en een consistente Herhaal bare benadering voor het kiezen van een Cloud serviceprovider worden geboden.

De implementatie van de Cloud is niet langer gewoon een technologisch besluit. Omdat de controle vereisten op elk aspect van een organisatie aanraken, dienen ze alle belang rijke interne besluit vormers te bijeenroepen: de CIO-en CISO en de juridische, risico beheer-, aankoop-en nalevings professionals. Dit verhoogt de efficiëntie van het besluitvormings proces en de grond beslissingen in geluids oorzaken, waardoor de kans op onvoorziene obstakels niet kan worden aangenomen.

Daarnaast is de controle lijst:

- Beschrijft belang rijke discussie onderwerpen voor besluit vormers aan het begin van het Cloud-acceptatie proces.

- Biedt uitgebreide zakelijke discussies over regels en de eigen doel stellingen van de organisatie voor privacy, persoonlijke gegevens en gegevens beveiliging.

- Helpt organisaties bij het identificeren van mogelijke problemen die van invloed kunnen zijn op een Cloud project.

- Biedt een consistente set vragen, met dezelfde voor waarden, definities, metrische gegevens en producten voor elke provider, om het proces van het vergelijken van aanbiedingen van andere Cloud serviceproviders te vereenvoudigen.

## <a name="azure-infrastructure-and-application-security-validation"></a>Azure-infra structuur en toepassings beveiligings validatie

[Azure Operational Security](operational-security.md) heeft betrekking op de services, besturings elementen en functies die beschikbaar zijn voor gebruikers voor het beveiligen van hun gegevens, toepassingen en andere assets in Microsoft Azure.

![beveiligings validatie (detectie)](./media/technical-capabilities/azure-security-technical-capabilities-fig7.png)

Azure Operational Security is gebaseerd op een Framework met de kennis die is opgedaan via een aantal mogelijkheden die uniek zijn voor micro soft, waaronder micro soft Security Development Lifecycle (SDL), het micro soft Security Response Center-programma en diepe kennis van de Cyber beveiliging Threat-landschap.

### <a name="microsoft-azure-monitor"></a>Microsoft Azure monitor

[Azure monitor](../../azure-monitor/index.yml) is de IT-beheer oplossing voor de hybride Cloud. Als u de bestaande System Center-implementatie gebruikt of uitbreidt, biedt Azure Monitor logboeken u de maximale flexibiliteit en controle voor het beheer van uw infra structuur op basis van de Cloud.

![Azure Monitor](./media/technical-capabilities/azure-security-technical-capabilities-fig8.png)

Met Azure Monitor kunt u elk exemplaar in elke Cloud beheren, met inbegrip van on-premises, azure, AWS, Windows Server, Linux, VMware en open stack, tegen lagere kosten dan concurrerende oplossingen. Azure Monitor is gebouwd voor de hele wereld en biedt een nieuwe benadering voor het beheren van uw onderneming die de snelste, rendabelste manier is om te voldoen aan nieuwe zakelijke uitdagingen en nieuwe werk belastingen, toepassingen en Cloud omgevingen te bieden.

### <a name="azure-monitor-logs"></a>Azure Monitor-logboeken

[Azure monitor logboeken](https://azure.microsoft.com/documentation/services/log-analytics) biedt bewakings Services door gegevens van beheerde resources te verzamelen in een centrale opslag plaats. Deze gegevens kunnen gebeurtenissen, prestatiegegevens en aangepaste gegevens omvatten die via de API worden geleverd. Na verzameling zijn de gegevens beschikbaar voor waarschuwingen, analyse en export.

![Azure Monitor-logboeken](./media/technical-capabilities/azure-security-technical-capabilities-fig9.png)

Met deze methode kunt u gegevens uit verschillende bronnen consolideren, zodat u gegevens uit uw Azure-Services kunt combi neren met uw bestaande on-premises omgeving. De methode maakt ook een duidelijk onderscheid tussen het verzamelen van gegevens en het bewerken hiervan. Zo zijn alle bewerkingen beschikbaar voor alle soorten gegevens.

### <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../../security-center/security-center-introduction.md) helpt u bij het detecteren, voorkomen van en reageren op bedreigingen dankzij een verhoogde zichtbaarheid van en controle over de beveiliging van uw Azure-resources. Het biedt geïntegreerde beveiligingsbewaking en beleidsbeheer voor uw Azure-abonnementen, helpt bedreigingen te detecteren die anders onopgemerkt zouden blijven, en werkt met een uitgebreid ecosysteem van beveiligingsoplossingen.

Security Center analyseert de beveiligingsstatus van uw Azure-resources om mogelijke beveiligingsproblemen op te sporen. Een lijst met aanbevelingen begeleidt u bij het configureren van benodigde besturingselementen.

Enkele voorbeelden:

- Inrichting van antimalware om schadelijke software te identificeren en te verwijderen

- Netwerk beveiligings groepen en-regels configureren om verkeer naar Vm's te beheren

- Inrichten van Web Application Firewalls om te beschermen tegen aanvallen die zijn gericht op uw webtoepassingen

- Implementatie van ontbrekende systeemupdates

- Aanpassing van besturingssysteemconfiguraties die niet overeenkomen met de aanbevolen basislijnen

Security Center verzamelt, analyseert en integreert automatisch logboekgegevens van uw Azure-resources, het netwerk en partneroplossingen zoals antimalwareprogramma's en firewalls. Wanneer er dreigingen worden gedetecteerd, wordt een beveiligingswaarschuwing gemaakt. Voorbeelden zijn detectie van:

- Gemanipuleerde Vm's die communiceren met bekende schadelijke IP-adressen

- Geavanceerde malware die is gedetecteerd met Windows Foutrapportage

- Beveiligings aanvallen tegen Vm's

- Beveiligingswaarschuwingen van geïntegreerde antimalwareprogramma's en firewalls

### <a name="azure-monitor"></a>Monitor voor Azure

[Azure monitor](../../azure-monitor/overview.md) biedt verwijzingen naar informatie over specifieke typen resources. Het biedt visualisatie, query's, route ring, waarschuwingen, automatisch schalen en automatisering op gegevens van de Azure-infra structuur (activiteiten logboek) en elke afzonderlijke Azure-resource (Diagnostische logboeken).

Cloud toepassingen zijn complex met veel bewegende onderdelen. Bewaking biedt gegevens om ervoor te zorgen dat uw toepassing in een goede staat actief blijft. Het helpt u ook om mogelijke problemen op te lossen of om Stave te oplossen.

![Diagram waarin wordt weer gegeven dat u bewakings gegevens kunt gebruiken om grondige inzichten over uw toepassing te krijgen.](./media/technical-capabilities/azure-security-technical-capabilities-fig10.png)
Daarnaast kunt u bewakings gegevens gebruiken om grondige inzichten over uw toepassing te krijgen. Deze kennis kan u helpen bij het verbeteren van de prestaties of het onderhoud van toepassingen, of het automatiseren van acties waarvoor anders hand matige interventie nodig zou zijn.

Het controleren van de netwerk beveiliging is essentieel voor het detecteren van problemen met het netwerk en het controleren van de naleving van uw IT-beveiligings beleid en het regulerende governance model. Met de weer gave beveiligings groep kunt u de geconfigureerde netwerk beveiligings groep en beveiligings regels, evenals de juiste beveiligings regels, ophalen. Met de lijst met toegepaste regels kunt u bepalen welke poorten het beveiligings risico van open en SS hebben.

### <a name="network-watcher"></a>Network Watcher

[Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) is een regionale service waarmee u voor waarden kunt controleren en diagnosticeren op netwerk niveau in, naar en Azure. U kunt de beschikbare diagnostische en visualisatiehulpprogramma's voor netwerken in Network Watcher gebruiken om uw netwerk in Azure te begrijpen, te analyseren en inzichten voor uw netwerk te verkrijgen. Deze service omvat pakket opname, volgende hop, IP-stroom controleren, beveiligings groep weer geven, NSG stroom Logboeken. Bewaking op scenario niveau biedt een end-to-end weer gave van netwerk bronnen in tegens telling tot afzonderlijke netwerk bron bewaking.

### <a name="storage-analytics"></a>Opslaganalyse

[Opslaganalyse](/rest/api/storageservices/fileservices/storage-analytics) kunt metrische gegevens opslaan die geaggregeerde trans actie-statistieken en capaciteitgegevens over aanvragen voor een opslag service bevatten. Trans acties worden gerapporteerd op het niveau van de API-bewerking en op het niveau van de opslag service, en de capaciteit wordt gerapporteerd op het niveau van de opslag service. Metrische gegevens kunnen worden gebruikt voor het analyseren van het gebruik van opslag Services, het diagnosticeren van problemen met aanvragen die zijn gedaan voor de opslag service en voor het verbeteren van de prestaties van toepassingen die gebruikmaken van een service.

### <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) is een UITBREID bare apm-service (Application Performance Management) voor webontwikkelaars op meerdere platforms. Hiermee kunt u uw livewebtoepassing controleren. Afwijkende prestaties worden automatisch gedetecteerd. Het bevat krachtige analyse hulpprogramma's waarmee u problemen kunt vaststellen en inzicht kunt krijgen in wat gebruikers met uw app doen. Het is bedoeld om u te helpen de prestaties en bruikbaarheid continu te verbeteren. Het werkt voor apps op een groot aantal verschillende platformen, waaronder .NET, Node.js en Java EE, lokaal gehost of in de Cloud. Het wordt geïntegreerd met uw DevOps-proces en heeft verbindings punten naar verschillende ontwikkel hulpprogramma's.

Met deze service kunt u het volgende controleren:

- **Aantal aanvragen, reactietijden en foutpercentages** - ga na welke pagina's het populairst zijn op welke tijdstippen van de dag en waar uw gebruikers zich bevinden. Ontdek welke pagina's het beste presteren. Als uw reactietijden en foutpercentages omhoog gaan wanneer er meer aanvragen binnenkomen, hebt u mogelijk te weinig resources.

- **Aantal afhankelijkheidsrelaties, reactietijden en foutpercentages** - controleer of externe services zorgen voor vertraging.

- **Uitzonderingen** - analyseer de cumulatieve statistische gegevens of kies specifieke gegevens en zoom in op de stack-trace en verwante aanvragen. Zowel server- als browseruitzonderingen worden gerapporteerd.

- **Paginaweergaven en de prestaties bij het laden van pagina’s** - deze gegevens worden gerapporteerd door de browsers van uw gebruikers.

- **Ajax-aanroepen van** webpagina's-tarieven, reactie tijden en fout percentages.

- **Aantal gebruikers en sessies.**

- **Prestatiemeteritems** van uw Windows- of Linux-servers, zoals die voor CPU-, geheugen- en netwerkgebruik.

- **Diagnostische gegevens van hosts** van Docker of Azure.

- **Diagnostische traceerlogboeken** van uw app - met behulp hiervan kunt u de samenhang vaststellen tussen traceergebeurtenissen en aanvragen.

- **Aangepaste gebeurtenissen en metrische gegevens** die u zelf schrijft in de client-of server code, voor het bijhouden van zakelijke gebeurtenissen, zoals verkochte items of spellen die zijn gewonnen.

De infrastructuur voor uw toepassing bestaat meestal uit veel onderdelen, zoals een virtuele machine, een opslagaccount en een virtueel netwerk, of een webtoepassing, database, databaseserver en services van derden. Deze onderdelen moet u niet zien als afzonderlijke entiteiten, maar als onderdelen die één entiteit vormen en aan elkaar zijn gerelateerd en afhankelijk zijn van elkaar. U implementeert, beheert en bewaakt deze onderdelen als groep. [Azure Resource Manager](../../azure-resource-manager/management/overview.md) kunt u met de resources in uw oplossing als groep gebruiken.

U kunt alle resources voor uw oplossing implementeren, bijwerken of verwijderen in een enkele, gecoördineerde bewerking. Voor implementatie gebruikt u een sjabloon. Deze sjabloon kan voor verschillende omgevingen worden gebruikt, zoals testen, faseren en productie. Resource Manager biedt beveiliging, controle en tagfuncties die u na de implementatie helpen bij het beheren van uw resources.

**De voordelen van Resource Manager**

Resource Manager biedt diverse voordelen:

- U kunt alle resources voor uw oplossing implementeren, beheren en bewaken als groep, in plaats van deze resources afzonderlijk te verwerken.

- U kunt gedurende de ontwikkelingscyclus uw oplossing herhaaldelijk implementeren en erop vertrouwen dat uw resources consistent worden geïmplementeerd.

- U kunt uw infrastructuur beheren via declaratieve sjablonen in plaats van scripts.

- U kunt de afhankelijkheden tussen resources definiëren, zodat deze in de juiste volg orde worden geïmplementeerd.

- U kunt toegangs beheer Toep assen op alle services in uw resource groep, omdat op rollen gebaseerd toegangs beheer op basis van Azure (Azure RBAC) systeem eigen is geïntegreerd in het beheer platform.

- U kunt tags toepassen op de resources om alle resources in uw abonnement op een logische manier te organiseren.

- U kunt de facturering van uw organisatie transparanter maken door te kijken naar de kosten voor een groep resources met dezelfde tag.

> [!Note]
> Resource Manager biedt een nieuwe manier om uw oplossingen te implementeren en te beheren. Zie [Resource Manager-implementatie en klassieke implementatie](../../azure-resource-manager/management/deployment-models.md)voor meer informatie over het gebruik van het eerdere implementatie model en over de wijzigingen.

## <a name="next-step"></a>Volgende stap

Het [Azure Security Bench Mark](../benchmarks/introduction.md) -programma bevat een verzameling beveiligings aanbevelingen die u kunt gebruiken om de services te beveiligen die u in azure gebruikt.