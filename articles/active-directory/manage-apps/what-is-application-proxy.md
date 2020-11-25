---
title: On-premises apps publiceren met Azure AD-toegangsproxy
description: Begrijp waarom u een toegangsproxy moet gebruiken voor het extern publiceren van on-premises webtoepassingen aan externe gebruikers. Meer informatie over de architectuur van een toegangsproxy, connectors, verificatiemethoden en beveiligingsvoordelen.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/31/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: bc3352dd57a76cbb0b38455adfa11987ec93781a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96003018"
---
# <a name="using-azure-ad-application-proxy-to-publish-on-premises-apps-for-remote-users"></a>Een toegangsproxy van Azure AD gebruiken om on-premises apps te publiceren voor externe gebruikers

Azure Active Directory (Azure AD) biedt veel mogelijkheden voor het beveiligen van gebruikers, apps en gegevens in de cloud en on-premises. Met name de functie Azure AD-toegangsproxy kan worden geïmplementeerd door IT-professionals die extern on-premises webtoepassingen willen publiceren. Externe gebruikers die toegang nodig hebben tot interne apps, kunnen deze op een veilige manier gebruiken.

De mogelijkheid om veilig toegang te krijgen tot interne apps buiten uw netwerk, is nog belangrijker op de moderne werk plek. Met scenario's zoals BYOD (Engelse afkorting voor ‘neem uw eigen apparaat mee’) en mobiele apparaten wordt van IT-professionals verwacht om te voldoen aan twee doeleinden:

* Eindgebruikers in staat stellen om overal en altijd productief te zijn
* De zakelijke bezittingen op elk moment te beveiligen

Veel organisaties geloven dat ze de controle hebben over en beschermd zijn als er resources zijn die zich binnen de grenzen van hun bedrijfsnetwerken bevinden. Maar in de huidige digitale werkplek is deze grens vergroot met beheerde mobiele apparaten en resources en services in de cloud. U moet nu de complexiteit beheren die gepaard gaat met het beschermen van de identiteit en gegevens van uw gebruikers die zijn opgeslagen op hun apparaten en apps.

Misschien maakt u al gebruik van Azure AD voor het beheren van gebruikers in de cloud die toegang moeten hebben tot Microsoft 365 en andere SaaS-toepassingen, evenals Web-apps die on-premises worden gehost. Als u Azure AD al hebt, kunt u deze gebruiken als één besturingsvlak om naadloze en veilige toegang tot uw on-premises toepassingen mogelijk te maken. Of misschien overweegt u nog steeds een overstap naar de cloud. Als dat het geval is, kunt u beginnen met de overstap naar de cloud door de toegangsproxy te implementeren en de eerste stap te nemen in het bouwen van een sterke identiteitsbasis.

De lijst hieronder illustreert een aantal van de dingen die u kunt inschakelen door app-proxy te implementeren in een hybride scenario:

* On-premises web-apps extern publiceren op een eenvoudige manier zonder een DMZ
* Ondersteuning voor eenmalige aanmelding (SSO) op apparaten, resources en apps in de cloud en on-premises
* Meervoudige verificatie ondersteunen voor apps in de Cloud en on-premises
* Maak snel gebruik van cloudfuncties met de beveiliging van de Microsoft Cloud
* Beheer van gebruikersaccounts centraliseren
* Beheer van identiteit en beveiliging centraliseren
* Toegang van gebruikers tot toepassingen automatisch toevoegen of verwijderen op basis van groepslidmaatschap

In dit artikel wordt uitgelegd hoe u met Azure AD en de toegangsproxy externe gebruikers een SSO-ervaring (eenmalige aanmelding) kunt geven. Gebruikers maken veilig verbinding met on-premises apps zonder VPN- of Dual-Home-servers en firewallregels. Dit artikel helpt u te begrijpen hoe de toegangsproxy de mogelijkheden en beveiligingsvoordelen van de cloud biedt voor uw on-premises webtoepassingen. Hierin worden ook de architectuur en topologieën beschreven die mogelijk zijn.

## <a name="remote-access-in-the-past"></a>Externe toegang in het verleden

Voorheen was het doel van uw besturingsvlak de bescherming van interne bronnen tegen aanvallers, terwijl de toegang door externe gebruikers is vergemakkelijkt in de DMZ of het perimeternetwerk. Maar de VPN-en omgekeerde proxy-oplossingen die zijn geïmplementeerd in het DMZ dat door externe clients wordt gebruikt voor toegang tot bedrijfsresources, zijn niet geschikt voor de cloudwereld. Ze lijden doorgaans aan de volgende nadelen:

* Hardwarekosten
* Beveiliging beheren (patches, poorten bewaken, enz.)
* Gebruikers aan de rand verifiëren
* Gebruikers verifiëren bij webservers in het perimeternetwerk
* VPN-toegang onderhouden voor externe gebruikers met de distributie en configuratie van VPN-clientsoftware. Houd ook rekening met servers die lid zijn van een domein in de DMZ, die kwetsbaar kunnen zijn voor aanvallen van buitenaf.

In de huidige wereld waar de cloud voorop staat is Azure AD het meest geschikt om te bepalen wie en wat in uw netwerk wordt weergegeven. Azure AD-toegangsproxy integreert met moderne verificatie en cloudtechnologieën, zoals SaaS-toepassingen en id-providers. Met deze integratie kunnen gebruikers overal toegang krijgen tot apps. Deze app is niet alleen geschikt voor de digitale werkplek van de huidige omgeving, maar is veiliger dan VPN- en omgekeerde proxy-oplossingen en eenvoudiger te implementeren. Externe gebruikers hebben dezelfde manier om toegang te krijgen tot uw on-premises toepassingen als voor micro soft en andere SaaS-apps die zijn geïntegreerd met Azure AD. U hoeft uw toepassingen niet te wijzigen of bij te werken om te kunnen werken met toegangsproxy. Bovendien hoeft u voor app-proxy geen inkomende verbindingen te openen via uw firewall. Met app-proxy kunt u deze eenvoudigweg instellen en hoeft u er niet meer aan te denken.

## <a name="the-future-of-remote-access"></a>De toekomst van externe toegang

In de huidige digitale werkplek werken gebruikers overal met meerdere apparaten en apps. De enige constante is de gebruikersidentiteit. Daarom is de eerste stap voor een veilig netwerk dat vandaag begint het gebruik van [Azure AD Identity Management](../../security/fundamentals/identity-management-overview.md)-mogelijkheden als uw beveiligingsbesturingsvlak. Een model dat gebruikmaakt van identiteit als uw besturingsvlak, bestaat doorgaans uit de volgende onderdelen:

* Een id-provider om gebruikers en gebruikersinformatie bij te houden.
* Apparaatmap voor het onderhouden van een lijst met apparaten die toegang hebben tot bedrijfsresources. Deze map bevat de bijbehorende apparaatgegevens (bijvoorbeeld type apparaat, integriteit enz.).
* De beleidsevaluatieservice om te bepalen of een gebruiker en apparaat voldoet aan het beleid dat is vastgelegd door beveiligingsbeheerders.
* De mogelijkheid om toegang tot resources van de organisatie toe te wijzen of te weigeren.

Met een toegangsproxy houdt Azure AD de gebruikers bij die toegang moeten hebben tot web-apps die on-premises en in de cloud zijn gepubliceerd. Het biedt een centraal beheerpunt voor die apps. Hoewel dit niet vereist is, is het raadzaam om ook voorwaardelijke toegang van Azure AD in te schakelen. Door voorwaarden te definiëren voor de manier waarop gebruikers zich kunnen verifiëren en toegang verkrijgen, zorgt u ervoor dat de juiste mensen toegang hebben tot toepassingen.

**Opmerking:** Het is belangrijk om te begrijpen dat een toepassingsproxy van Azure AD is bedoeld als een VPN- of omgekeerde proxyvervanging voor zwervende (of externe) gebruikers die toegang nodig hebben tot interne bronnen. Het is niet bedoeld voor interne gebruikers in het bedrijfsnetwerk. Interne gebruikers die onnodig toepassingsproxy gebruiken, kunnen onverwachte en ongewenste prestatieproblemen veroorzaken.

![Azure Active Directory en al uw apps](media/what-is-application-proxy/azure-ad-and-all-your-apps.png)

### <a name="an-overview-of-how-app-proxy-works"></a>Een overzicht van de werking van app-proxy

Een toepassingsproxy is een Azure AD-service die u configureert in het Azure-portal. U kunt hiermee een extern openbaar HTTP/HTTPS-URL-eindpunt publiceren in de Azure-cloud, die verbinding maakt met een interne toepassingsserver-URL in uw organisatie. Deze on-premises web-apps kunnen worden geïntegreerd met Azure AD ter ondersteuning van eenmalige aanmelding. Eind gebruikers hebben dan toegang tot on-premises web-apps op dezelfde manier als ze toegang hebben tot Microsoft 365 en andere SaaS-apps.

Onderdelen van deze functie omvatten de toepassingsproxy-service, die wordt uitgevoerd in de cloud, de connector van de toepassingsproxy, een lichtgewicht agent die wordt uitgevoerd op een on-premises server en Azure AD, de id-provider. Alle drie de onderdelen werken samen om de gebruiker te voorzien van een eenmalige aanmelding om toegang te krijgen tot on-premises webtoepassingen.

Nadat u zich hebt aangemeld, hebben externe gebruikers toegang tot on-premises webtoepassingen met behulp van een vertrouwde URL of [mijn apps](../user-help/my-apps-portal-end-user-access.md) vanaf hun desktop-of IOS/Mac-apparaten. Een app-proxy kan bijvoorbeeld externe toegang en eenmalige aanmelding bieden voor extern bureaublad, SharePoint-sites, Tableau, Qlik, Outlook op internet en LOB-toepassingen (line-of-business).

![Azure AD-toepassingsproxy en Qlik Sense](media/what-is-application-proxy/azure-ad-application-proxy-architecture.png)

### <a name="authentication"></a>Verificatie

Er zijn verschillende manieren om een toepassing te configureren voor eenmalige aanmelding en de methode die u selecteert, is afhankelijk van de verificatie die door uw toepassing wordt gebruikt. Een toepassingsproxy biedt ondersteuning voor de volgende typen toepassingen:

* Webtoepassingen
* Web-API's die u beschikbaar wilt maken voor uitgebreide toepassingen op verschillende apparaten
* Toepassingen die worden gehost achter een extern bureaublad-gateway
* Uitgebreide client-apps die zijn geïntegreerd met de[Microsoft Authentication Library (MSAL)](../develop/v2-overview.md)

App-proxy werkt met apps die gebruikmaken van het volgende systeemeigen verificatieprotocol:

* **[Geïntegreerde Windows-verificatie (IWA)](application-proxy-configure-single-sign-on-with-kcd.md).** Voor IWA gebruiken de toepassingsproxy-connectors Kerberos-beperkte overdracht (KCD) om gebruikers te verifiëren bij de Kerberos-toepassing.

De app-proxy biedt ook ondersteuning voor de volgende verificatieprotocollen met integratie van derden of in specifieke configuratiescenario's:

* [**Op headers gebaseerde verificatie**](/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-headers). Deze aanmeldingsmethode maakt gebruik van een verificatieservice van derden met de naam PingAccess en wordt gebruikt wanneer de toepassing headers gebruikt voor verificatie. In dit scenario wordt verificatie afgehandeld door PingAccess.
* [**Verificatie op basis van een formulier of wachtwoord**](application-proxy-configure-single-sign-on-password-vaulting.md). Met deze verificatiemethode melden gebruikers zich aan bij de toepassing met een gebruikersnaam en wachtwoord, bij de eerste keer dat ze deze gebruiken. Na de eerste aanmelding levert Azure AD de gebruikersnaam en het wachtwoord voor de toepassing. In dit scenario wordt verificatie verwerkt door Azure AD.
* [**SAML-verificatie**](application-proxy-configure-single-sign-on-on-premises-apps.md). Eenmalige aanmelding op basis van SAML wordt ondersteund voor toepassingen die gebruikmaken van de protocollen SAML 2.0 of WS-Federation. Met eenmalige aanmelding via SAML verifieert Azure AD bij de toepassing met behulp van het Azure AD-account van de gebruiker.

Zie [Kiezen voor eenmalige aanmeldingsmethode](sso-options.md#choosing-a-single-sign-on-method) voor meer informatie over ondersteunde methoden.

### <a name="security-benefits"></a>Beveiligingsvoordelen

De oplossing voor externe toegang die wordt aangeboden door de toepassingsproxy en Azure AD biedt ondersteuning voor verschillende beveiligingsvoordelen die klanten kunnen gebruiken, met inbegrip van:

* **Geverifieerde toegang**. Toepassingsproxy is het meest geschikt voor het publiceren van toepassingen met [verificatie vooraf](application-proxy-security.md#authenticated-access) om ervoor te zorgen dat alleen geverifieerde verbindingen in uw netwerk worden bereikt. Voor toepassingen die zijn gepubliceerd met verificatie vooraf, mag er geen verkeer worden doorgegeven aan uw on-premises omgeving via de app-proxyservice, zonder een geldig token. Verificatie vooraf, door de aard van de authenticatie, blokkeert een aanzienlijk aantal gerichte aanvallen, omdat alleen geverifieerde identiteiten toegang hebben tot de back-end-toepassing.
* **Voorwaardelijke toegang**. Geavanceerde beleidsregels kunnen worden toegepast voordat verbindingen met uw netwerk tot stand worden gebracht. Met voorwaardelijke toegang kunt u beperkingen definiëren voor het verkeer dat u toestaat uw back-end-toepassing te bereiken. U maakt beleidsregels die aanmeldingen beperken op basis van locatie, verificatiesterkte en gebruikersrisicoprofiel. Terwijl voorwaardelijke toegang zich ontwikkelt, worden er meer besturingselementen toegevoegd om extra beveiliging te bieden, zoals integratie met Microsoft Cloud App Security (MCAS). Met MCAS-integratie kunt u een on-premises toepassing voor [realtime-bewaking](application-proxy-integrate-with-microsoft-cloud-application-security.md) configureren door gebruik te maken van voorwaardelijke toegang om sessies in realtime te bewaken en te beheren op basis van een beleid voor voorwaardelijke toegang.
* **Verkeer beëindigen**. Al het verkeer naar de back-end-toepassing wordt beëindigd bij de toepassingsproxy-service in de cloud terwijl de sessie opnieuw tot stand wordt gebracht met de back-endserver. Deze verbindingsstrategie zorgt ervoor dat uw back-endservers niet worden blootgesteld aan direct HTTP-verkeer. Ze zijn beter beschermd tegen specifieke DoS-aanvallen (denial of service), omdat uw firewall niet wordt aangevallen.
* **Alle toegang is uitgaand**. De toepassingsproxy-connectors gebruiken alleen uitgaande verbindingen met de toegangsproxy-service in de cloud via de poorten 80 en 443. Zonder binnenkomende verbindingen is het niet nodig om firewallpoorten te openen voor binnenkomende verbindingen of onderdelen in de DMZ. Alle verbindingen zijn uitgaand en via een beveiligd kanaal.
* **Beveiligingsanalyse en intelligentie op basis van Machine Learning (ML).** Omdat het deel uitmaakt van Azure Active Directory, kan de toepassingsproxy gebruikmaken van [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md) (hiervoor is [Premium P2-licentie](https://azure.microsoft.com/pricing/details/active-directory/)vereist). Azure AD Identity Protection is een combinatie van beveiligingsinformatie voor machine learning met gegevensfeeds van de [Digital Crimes Unit van Microsoft](https://news.microsoft.com/stories/cybercrime/index.html) en [Microsoft Security Response Center](https://www.microsoft.com/msrc) om gekraakte accounts proactief te identificeren. Identiteitsbeveiliging biedt realtime-beveiliging van aanmeldingen met een hoog risico. Het houdt rekening met factoren zoals toegang vanaf geïnfecteerde apparaten, via anonieme netwerken, of op ongewone en onwaarschijnlijke locaties om het risicoprofiel van een sessie te vergroten. Dit risicoprofiel wordt gebruikt voor realtime-beveiliging. Veel van deze rapporten en gebeurtenissen zijn al beschikbaar via een API voor integratie met uw SIEM-systemen.

* **Externe toegang als een service**. U hoeft zich geen zorgen te maken over het onderhouden en patchen van on-premises servers om externe toegang in te schakelen. Een toepassingsproxy is een schaalservice op het internet van Microsoft, zodat u altijd de meest recente beveiligingspatches en upgrades krijgt. Niet-gepatchte software-accounts voor een groot aantal aanvallen. Volgens het Ministerie van Binnenlandse Veiligheid is [85 procent van de gerichte aanvallen te voorkomen](https://www.us-cert.gov/ncas/alerts/TA15-119A). Met dit servicemodel hoeft u niet meer het zware werk te doen bij het beheren van uw randservers of ze wanneer nodig te patchen.

* **Intune-integratie**. Met Intune wordt bedrijfsverkeer onafhankelijk van persoonlijk verkeer gerouteerd. Met een toepassingsproxy zorgt u ervoor dat het bedrijfsverkeer wordt geverifieerd. [Toepassingsproxy en de Intune Managed Browser](/intune/app-configuration-managed-browser#how-to-configure-application-proxy-settings-for-protected-browsers)-mogelijkheid kunnen ook samen worden gebruikt om externe gebruikers veilig toegang te bieden tot interne websites van iOS-en Android-apparaten.

### <a name="roadmap-to-the-cloud"></a>Roadmap naar de cloud

Een ander belangrijk voordeel van het implementeren van toepassingsproxy is Azure AD uitbreiden naar uw on-premises omgeving. De implementatie van de app-proxy is in feite een belangrijke stap bij het verplaatsen van uw organisatie en apps naar de cloud. Door over te stappen naar de Cloud en afstand te doen van on-premises verificatie, vermindert u de on-premisesfootprint en gebruikt u de mogelijkheden van Azure AD-identiteitsbeheer als uw besturingsvlak. Met minimale of geen updates voor bestaande toepassingen, hebt u toegang tot cloudmogelijkheden zoals eenmalige aanmelding, meervoudige verificatie en centraal beheer. Het installeren van de benodigde onderdelen voor een app-proxy is een eenvoudig proces om een RAS-Framework tot stand te brengen. En als u overstapt naar de cloud, hebt u toegang tot de nieuwste functies, updates en functionaliteit van Azure AD, zoals hoge beschikbaarheid en herstel na noodgevallen.

Zie het technisch document [Uw toepassingen migreren naar Azure Active Directory](https://aka.ms/migrateapps/whitepaper) voor meer informatie over het migreren van uw apps naar Azure AD.

## <a name="architecture"></a>Architectuur

In het volgende diagram ziet u hoe de Azure AD-verificatieservices en toepassingsproxy samenwerken om eenmalige aanmelding te bieden voor on-premises toepassingen voor eindgebruikers.

![Verificatiestroom van Azure AD-toepassingsproxy](media/what-is-application-proxy/azure-ad-application-proxy-authentication-flow.png)

1. Nadat de gebruiker toegang tot de toepassing heeft verkregen via een eindpunt, wordt de gebruiker omgeleid naar de aanmeldingspagina van Azure AD. Als u een beleid voor voorwaardelijke toegang hebt geconfigureerd, worden er op dit moment specifieke voorwaarden gecontroleerd om ervoor te zorgen dat u voldoet aan de beveiligingsvereisten van uw organisatie.
2. Na een geslaagde aanmelding stuurt Azure AD een token naar het clientapparaat van de gebruiker.
3. De client stuurt het token naar de toepassingsproxyservice, waarmee de user principal name (UPN) en de Security principal name (SPN) van het token worden opgehaald.
4. Toepassingsproxy stuurt de aanvraag door, die wordt opgehaald door de [connector](application-proxy-connectors.md) van toepassingsproxy.
5. De connector voert aanvullende verificatie uit namens de gebruiker (*optioneel, afhankelijk van de verificatiemethode*), verzoekt het interne eindpunt van de toepassingsserver en verzendt de aanvraag naar de on-premises toepassing.
6. Het antwoord van de toepassingsserver wordt via de connector naar de toepassingsproxyservice verzonden.
7. Het antwoord wordt verzonden vanuit de toepassingsproxyservice naar de gebruiker.

|**Onderdeel**|**Beschrijving**|
|:-|:-|
|Eindpunt|Het eind punt is een URL of [eind gebruikers Portal](end-user-experiences.md). Gebruikers kunnen toepassingen en buiten uw netwerk bereiken door toegang te krijgen tot een externe URL. Gebruikers in uw netwerk hebben toegang tot de toepassing via een URL of een portal voor eindgebruikers. Wanneer gebruikers naar een van deze eindpunten gaan, verifiëren ze zichzelf in Azure AD en worden ze vervolgens via de connector naar de on-premises toepassing gestuurd.|
|Azure AD|Azure AD voert de verificatie uit met behulp van de tenantmap die is opgeslagen in de cloud.|
|Service voor proxytoepassing|Deze toepassingsproxyservice wordt uitgevoerd in de cloud als onderdeel van Azure AD. Hiermee wordt het aanmeldingstoken van de gebruiker aan de toepassingsproxy-connector doorgegeven. Met de toepassingsproxy worden alle toegankelijke headers voor de aanvraag doorgestuurd en worden de headers volgens het bijbehorende protocol ingesteld op het IP-adres van de client. Als de inkomende aanvraag voor de proxy al die header heeft, wordt het client-IP-adres toegevoegd aan het einde van de door komma's gescheiden lijst die de waarde van de header is.|
|Connectors voor de toepassingsproxy|De connector is een lichtgewicht agent die wordt uitgevoerd op een Windows-server in uw netwerk. De connector beheert de communicatie tussen de toepassingsproxyservice in de cloud en de on-premises toepassing. De connector gebruikt alleen uitgaande verbindingen. U hoeft geen binnenkomende poorten te openen of iets in de DMZ te plaatsen. De connectors zijn staatloos en halen indien nodig gegevens uit de cloud. Zie [Azure AD-toepassingsproxy-connectors begrijpen](application-proxy-connectors.md)voor meer informatie over connectors, zoals hoe ze worden geladen en geverifieerd.|
|Active Directory (AD)|Active Directory wordt on-premises uitgevoerd om verificatie voor domeinaccounts uit te voeren. Wanneer eenmalige aanmelding is geconfigureerd, communiceert de connector met AD voor het uitvoeren van vereiste aanvullende verificatie.|
|On-premises toepassing|Ten slotte heeft de gebruiker toegang tot een on-premises toepassing.|

Azure AD-toepassingsproxy bestaat uit de cloudservice voor toepassingsproxy en een on-premises connector. De connector luistert naar aanvragen van de toepassingsproxyservice en verwerkt de verbindingen met de interne toepassingen. Het is belangrijk te weten dat alle communicatie plaatsvindt via TLS en altijd afkomstig is van de connector voor de toepassingsproxyservice. Dat wil zeggen dat de communicatie alleen uitgaand is. De connector gebruikt een clientcertificaat voor de verificatie van de toepassingsproxyservice voor alle aanroepen. De enige uitzondering op de verbindingsbeveiliging is de eerste installatiestap waarin het clientcertificaat is ingesteld. Zie de [Kijkje achter de schermen](application-proxy-security.md#under-the-hood) van de toepassingsproxy voor meer informatie.

### <a name="application-proxy-connectors"></a>Connectors voor de toepassingsproxy

[Connectoren voor toepassingsproxy’s](application-proxy-connectors.md) zijn lichtgewicht agents die on-premises worden geïmplementeerd en de uitgaande verbinding met de toepassingsproxyservice in de cloud faciliteren. De connectors moeten worden geïnstalleerd op een Windows-Server die toegang heeft tot de back-end-toepassing. Gebruikers maken verbinding met de cloudservice van de app-proxy waarmee hun verkeer via de connectors naar de apps wordt gerouteerd, zoals hieronder wordt weer gegeven.

![Netwerk verbindingen voor Azure AD-toepassingsproxy](media/what-is-application-proxy/azure-ad-application-proxy-network-connections.png)

Installatie en registratie tussen een connector en de app-proxy service worden als volgt uitgevoerd:

1. De IT-beheerder opent poorten 80 en 443 voor uitgaand verkeer en biedt toegang tot verschillende URL's die nodig zijn voor de connector, de app-proxyservice en Azure AD.
2. De beheerder meldt zich aan bij het Azure-portal en voert een uitvoerbaar bestand uit om de connector te installeren op een on-premises Windows-server.
3. De connector begint met "luisteren" naar de proxyservice van de app.
4. De beheerder voegt de on-premises toepassing toe aan Azure AD en configureert instellingen, zoals de URL's die gebruikers nodig hebben om verbinding te maken met hun apps.

Zie [Implementatie voor een Azure AD-webtoepassingsproxy plannen](application-proxy-deployment-plan.md) voor meer informatie.

Het is raadzaam om altijd meerdere connectors te implementeren voor redundantie en schaal. De connectors, in combinatie met de service, nemen alle taken met hoge beschikbaarheid op zich en kunnen dynamisch worden toegevoegd of verwijderd. Telkens wanneer een nieuwe aanvraag arriveert, wordt deze doorgestuurd naar een van de beschikbare connectors. Wanneer een connector wordt uitgevoerd, blijft deze actief wanneer deze verbinding maakt met de service. Als een connector tijdelijk niet beschikbaar is, reageert deze niet op dit verkeer. Ongebruikte connectors zijn gelabeld als inactief en worden verwijderd na 10 dagen van inactiviteit.

Connectors vragen de server ook om erachter te komen of er een nieuwere versie van de connector is. Hoewel u een handmatige update kunt uitvoeren, worden connectors automatisch bijgewerkt zolang de Connector Updater-service van de toepassingsproxy wordt uitgevoerd. Voor tenants met meerdere connectors, worden de automatische updates per één connector in elke groep uitgevoerd om uitvaltijd in uw omgeving te voorkomen.

**Opmerking**: U kunt de pagina [Versiegeschiedenis](application-proxy-release-version-history.md) van de toepassingsproxy controleren om een melding te ontvangen wanneer er updates zijn uitgebracht door u te abonneren op de RSS-feed.

Elke toepassingsproxy-connector wordt toegewezen aan een [connectorgroep](application-proxy-connector-groups.md). Connectors in dezelfde connectorgroep fungeren als één eenheid voor hoge beschikbaarheid en taakverdeling. U kunt nieuwe groepen maken, connectoren aan hen toewijzen in het Azure-portal en vervolgens specifieke connectors toewijzen voor specifieke toepassingen. Het wordt aanbevolen om ten minste twee connectors in elke connectorgroep te hebben voor hoge beschikbaarheid.

Connectorgroepen zijn handig wanneer u de volgende scenario's moet ondersteunen:

* Geografische apps publiceren
* Toepassingssegmentatie/-isolatie
* Publiceren van web-apps die in de cloud of on-premises worden uitgevoerd

Zie [Overwegingen voor de netwerktopologie bij het gebruik van Azure Active Directory-toepassingsproxy](application-proxy-network-topology.md) voor meer informatie over het kiezen van het installeren van uw connectors en het optimaliseren van uw netwerk.

## <a name="other-use-cases"></a>Andere gebruiksvoorbeelden

Tot nu toe hebben we gestreefd naar het gebruik van een toepassingsproxy om on-premises apps extern te publiceren, terwijl eenmalige aanmelding wordt ingeschakeld voor al uw cloud- en on-premises apps. Er zijn echter andere gebruiksvoorbeelden voor de app-proxy. Deze omvatten:

* **REST-API's veilig publiceren**. Wanneer u bedrijfslogica of API's uitvoert die on-premises of op virtuele machines in de cloud worden uitgevoerd, biedt de toepassingsproxy een openbaar eindpunt voor API-toegang. Met toegang tot een API-eindpunt kunt u verificatie en autorisatie beheren zonder dat er binnenkomende poorten zijn vereist. Het biedt extra beveiliging via Azure AD Premium-functies, zoals meervoudige verificatie en voorwaardelijke toegang op basis van apparaten voor bureaubladen, iOS, MAC en Android-apparaten met Intune. Zie voor meer informatie [Inschakelen van systeemeigen clienttoepassingen om te communiceren met proxy-toepassingen](application-proxy-configure-native-client-application.md) en [Een API te beveiligen met behulp van OAuth 2,0 met Azure Active Directory en API Management](../../api-management/api-management-howto-protect-backend-with-aad.md).
* **Externe bureaubladservices** **(RDS)** . Voor standaard-RDS-implementaties zijn open binnenkomende verbindingen vereist. De [RDS-implementatie met toepassingsproxy](application-proxy-integrate-with-remote-desktop-services.md) heeft echter een permanente uitgaande verbinding van de server waarop de connectorservice wordt uitgevoerd. Op deze manier kunt u meer toepassingen aan eindgebruikers aanbieden door on-premises toepassingen te publiceren via externe bureaubladservices. U kunt ook de kwetsbaarheid van de implementatie verminderen met een beperkt aantal verificaties in twee stappen en voorwaardelijke toegangscontroles voor RDS.
* **Toepassingen publiceren die verbinding maken met behulp van websockets**. Ondersteuning met [Qlik Sense](application-proxy-qlik.md) is in Openbare preview en wordt in de toekomst uitgebreid naar andere apps.
* **Schakel systeemeigen clienttoepassingen in om te communiceren met proxytoepassingen**. U kunt Azure AD-toepassingsproxy gebruiken om web-apps te publiceren, maar dit kan ook worden gebruikt voor het publiceren van [systeemeigen clienttoepassingen](application-proxy-configure-native-client-application.md) die zijn geconfigureerd met de Azure AD Authentication Library (ADAL). Systeemeigen client toepassingen verschillen van web-apps omdat ze zijn geïnstalleerd op een apparaat, terwijl web-apps via een browser worden geopend.

## <a name="conclusion"></a>Conclusie

De manier waarop we werken en de hulpprogramma's die we gebruiken, worden snel gewijzigd. Met meer werknemers die hun eigen apparaten gebruiken om te werken en het gebruik van de Software-as-a-Service (SaaS)-toepassingen, moeten de manier waarop organisaties hun gegevens kunnen beheren en beveiligen, moeten ook groeien. Bedrijven werken niet alleen meer binnen hun eigen muren, beschermd door een barrière die hun gebouw omsluit. Gegevens worden naar meer locaties dan ooit verplaatst, in zowel on-premises als in de cloudomgeving. Deze evolutie heeft bijgedragen tot meer productiviteit en mogelijkheden voor gebruikers om samen te werken, maar maakt het beveiligen van gevoelige gegevens ook moeilijker.

Of u nu Azure AD gebruikt voor het beheren van gebruikers in een hybride scenario of als u geïnteresseerd bent in het starten van uw reis naar de cloud, het implementeren van een Azure AD-toepassingsproxy kan u helpen de grootte van uw on-premises footprint te verkleinen door externe toegang als een service te bieden.

Organisaties moeten vandaag nog profiteren van app-proxy om te profiteren van de volgende voordelen:

* On-premises apps extern publiceren zonder de kosten voor het onderhouden van traditionele VPN- of andere on-premises oplossingen voor webpublicaties en DMZ-benadering
* Eenmalige aanmelding voor alle toepassingen, Microsoft 365 of andere SaaS-apps, met inbegrip van on-premises toepassingen
* Cloud schaal beveiliging waarbij Azure AD gebruikmaakt van Microsoft 365 telemetrie om onbevoegde toegang te voor komen
* Integratie van Intune om ervoor te zorgen dat bedrijfsverkeer wordt geverifieerd
* Centralisatie van gebruikersaccountbeheer
* Automatische updates om ervoor te zorgen dat u over de nieuwste beveiligingspatches beschikt
* Nieuwe functies als deze worden vrijgegeven; de meest recente ondersteuning voor eenmalige SAML-aanmelding en een nauwkeuriger beheer van toepassingscookies

## <a name="next-steps"></a>Volgende stappen

* Zie [Een implementatie van een Azure AD-toepassingsproxy plannen](application-proxy-deployment-plan.md) voor meer informatie over het plannen, gebruiken en beheren van de Azure AD-toepassingsproxy.
* Zie [Aan de slag met Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial) om een live-demo te plannen of een gratis proefversie van 90 dagen voor evaluatie te ontvangen.