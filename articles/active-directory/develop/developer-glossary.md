---
title: Woorden lijst voor ontwikkel aars van micro soft Identity platform | Azure
description: Een lijst met voor waarden voor veelgebruikte concepten en functies voor ontwikkel aars van micro soft Identity platform.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jmprieur, saeeda, jesakowi, nacanuma
ms.openlocfilehash: 77a4e7389952b81df13dae929dc1aec664fcc0b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98755645"
---
# <a name="microsoft-identity-platform-developer-glossary"></a>Woorden lijst voor ontwikkel aars van micro soft Identity platform

Dit artikel bevat definities voor een aantal van de belangrijkste concepten en terminologie van ontwikkel aars, die handig zijn bij het ontwikkelen van toepassingen met behulp van micro soft Identity platform.

## <a name="access-token"></a>toegangs token

Een type [beveiligings token](#security-token) dat is uitgegeven door [een autorisatie server](#authorization-server)en wordt gebruikt door een [client toepassing](#client-application) om toegang te krijgen tot een [beveiligde bron server](#resource-server). Doorgaans in de vorm van een [JSON Web token (JWT)][JWT], wordt in het token de autorisatie van de [resource-eigenaar](#resource-owner)voor de client aangegeven voor een aangevraagd toegangs niveau. Het token bevat alle toepasselijke [claims](#claim) over het onderwerp, zodat de client toepassing het kan gebruiken als een vorm van referenties bij het openen van een bepaalde resource. Dit betekent ook dat de resource-eigenaar geen referenties voor de client beschikbaar moet maken.

Toegangs tokens zijn alleen geldig voor een korte periode en kunnen niet worden ingetrokken. Een autorisatie server kan ook een [vernieuwings token](#refresh-token) verlenen wanneer het toegangs token is uitgegeven. Vernieuwings tokens worden doorgaans alleen verstrekt aan vertrouwelijke client toepassingen.

Toegangs tokens worden ook wel ' gebruiker + app ' of ' alleen app ' genoemd, afhankelijk van de weer gegeven referenties. Wanneer een client toepassing bijvoorbeeld het volgende gebruikt:

* Autorisatie-autorisatie [code verleend](#authorization-grant), de eind gebruiker verifieert eerst als de resource-eigenaar, waarbij autorisatie wordt gedelegeerd aan de client om toegang te krijgen tot de resource. De client verifieert achteraf wanneer het toegangs token wordt verkregen. Het token kan soms specifiek worden aangeduid als een gebruikers-en app-token, aangezien het de gebruiker is die de client toepassing heeft geautoriseerd en de toepassing.
* ["Client referenties"-autorisatie verlenen](#authorization-grant), de client biedt de enige verificatie, werkt zonder de verificatie/autorisatie van de resource-eigenaar, zodat het token soms ook wel een ' app-only '-token kan worden genoemd.

Zie de naslag informatie voor het [micro soft Identity platform-token][AAD-Tokens-Claims] voor meer details.

## <a name="application-id-client-id"></a>toepassings-ID (client-ID)

De unieke id van Azure AD-problemen met een toepassings registratie waarmee een specifieke toepassing en de bijbehorende configuraties worden geïdentificeerd. Deze toepassings-ID ([client-id](https://tools.ietf.org/html/rfc6749#page-15)) wordt gebruikt bij het uitvoeren van verificatie aanvragen en wordt tijdens de ontwikkelings tijd aan de verificatie bibliotheken gegeven. De toepassings-ID (client-ID) is geen geheim.

## <a name="application-manifest"></a>toepassings manifest

Een functie van de [Azure Portal][AZURE-portal], die een JSON-weer gave van de identiteits configuratie van de toepassing produceert, die wordt gebruikt als een mechanisme voor het bijwerken van de bijbehorende [toepassing][Graph-App-Resource] en [ServicePrincipal][Graph-Sp-Resource] -entiteiten. Zie [het toepassings manifest van Azure Active Directory][AAD-App-Manifest] voor meer informatie.

## <a name="application-object"></a>toepassings object

Wanneer u een toepassing registreert of bijwerkt in de [Azure Portal][AZURE-portal], maakt/werkt de portal zowel een toepassings object als een bijbehorend [Service-Principal-object](#service-principal-object) voor die Tenant. Het toepassings object *definieert* de identiteits configuratie globaal van de toepassing (in alle tenants waar deze toegang tot heeft), waarbij een sjabloon wordt opgegeven van waaruit de bijbehorende service-principal-object (en) zijn *afgeleid* voor gebruik lokaal tijdens runtime (in een specifieke Tenant).

Zie [Application and Service Principal Objects][AAD-App-SP-Objects](Engelstalig) voor meer informatie.

## <a name="application-registration"></a>toepassingsregistratie

Als u wilt toestaan dat een toepassing kan worden geïntegreerd met de functies voor het beheren van identiteits-en toegangs beheer in azure AD, moet deze zijn geregistreerd bij een Azure AD- [Tenant](#tenant). Wanneer u uw toepassing registreert bij Azure AD, geeft u een identiteits configuratie voor uw toepassing op, zodat deze kan worden geïntegreerd met Azure AD en gebruikmaakt van functies zoals:

* Robuust beheer van één Sign-On met behulp van Azure AD Identity Management en [OpenID Connect Connect][OpenIDConnect] protocol-implementatie
* Brokered toegang tot [beveiligde bronnen](#resource-server) door [client toepassingen](#client-application)via OAuth 2,0- [autorisatie server](#authorization-server)
* [Toestemming raamwerk](#consent) voor het beheren van client toegang tot beveiligde bronnen, op basis van autorisatie van de resource-eigenaar.

Zie [toepassingen integreren met Azure Active Directory][AAD-Integrating-Apps] voor meer informatie.

## <a name="authentication"></a>verificatie

De handeling van een feestje om geldige referenties te verkrijgen, waarbij de basis vormt voor het maken van een beveiligingsprincipal die moet worden gebruikt voor identiteits-en toegangs beheer. Tijdens een [OAuth2-autorisatie verleent](#authorization-grant) de partij die de verificatie uitvoert, de rol van ofwel de [resource-eigenaar](#resource-owner) of [client toepassing](#client-application), afhankelijk van de gebruikte toekenning.

## <a name="authorization"></a>autorisatie

Het verlenen van een geverifieerde beveiligingsprincipal machtiging om iets te doen. Er zijn twee primaire gebruiksscenario's in het Azure AD-programmeermodel:

* Tijdens een OAuth2 voor autorisatie overdracht: wanneer de [resource-eigenaar](#resource-owner) autorisatie [toewijst](#authorization-grant) aan de [client toepassing](#client-application), waardoor de client toegang heeft tot de resources van de resource-eigenaar.
* Tijdens de toegang tot bronnen door de client: zoals geïmplementeerd door de [bron server](#resource-server), met behulp van de [claim](#claim) waarden die aanwezig zijn in het [toegangs token](#access-token) om op basis van de toegangs beheer beslissingen te nemen.

## <a name="authorization-code"></a>autorisatie code

Een korte levens ' token ' dat aan een [client toepassing](#client-application) door het [autorisatie-eind punt](#authorization-endpoint)wordt door gegeven als onderdeel van de stroom ' autorisatie code ', een van de vier OAuth2- [autorisatie subsidies](#authorization-grant). De code wordt geretourneerd naar de client toepassing als reactie op de verificatie van een [resource-eigenaar](#resource-owner), met de mede deling dat de resource-eigenaar toestemming heeft gegeven om toegang te krijgen tot de aangevraagde resources. Als onderdeel van de stroom wordt de code later ingewisseld voor een [toegangs token](#access-token).

## <a name="authorization-endpoint"></a>autorisatie-eind punt

Een van de eind punten die worden geïmplementeerd door de [autorisatie server](#authorization-server), die wordt gebruikt om te communiceren met de [resource-eigenaar](#resource-owner) om een [autorisatie](#authorization-grant) machtiging te bieden tijdens een OAuth2-autorisatie stroom. Afhankelijk van de gebruikte overdrachts stroom, kan de daad werkelijke verleende toekenning variëren, met inbegrip van een [autorisatie code](#authorization-code) of [beveiligings token](#security-token).

Zie de secties [autorisatie typen][OAuth2-AuthZ-Grant-Types] van de OAuth2-specificatie en het [autorisatie-eindpunt][OAuth2-AuthZ-Endpoint] en de [OpenIDConnect-specificatie][OpenIDConnect-AuthZ-Endpoint] voor meer informatie.

## <a name="authorization-grant"></a>autorisatie verlenen

Een referentie voor de [](#resource-owner) [autorisatie](#authorization) van de resource-eigenaar om toegang te krijgen tot de beveiligde bronnen die aan een [client toepassing](#client-application)worden verleend. Een client toepassing kan een van de [vier toekennings typen die door het OAuth2-autorisatie raamwerk worden gedefinieerd][OAuth2-AuthZ-Grant-Types] , gebruiken om een subsidie te verkrijgen, afhankelijk van het type/de vereisten van de client: "autorisatie code verlenen", "client referenties verlenen", "impliciete toekenning" en "wachtwoord referenties van de resource-eigenaar". De referentie die wordt geretourneerd naar de client is een [toegangs token](#access-token)of een [autorisatie code](#authorization-code) (later voor een toegangs token), afhankelijk van het type autorisatie toekenning dat wordt gebruikt.

## <a name="authorization-server"></a>autorisatie server

Zoals gedefinieerd in het [OAuth2-verificatie raamwerk][OAuth2-Role-Def], de server die verantwoordelijk is voor het verlenen van toegangs tokens aan de [client](#client-application) na verificatie van de [resource-eigenaar](#resource-owner) en het verkrijgen van de autorisatie. Een [client toepassing](#client-application) communiceert met de autorisatie server tijdens runtime via de [autorisatie](#authorization-endpoint) -en [token](#token-endpoint) -eind punten, in overeenstemming met de OAuth2-gedefinieerde [autorisatie toekenningen](#authorization-grant).

In het geval van micro soft Identity platform Application Integration, implementeert het micro soft Identity-platform de functie van de autorisatie server voor Azure AD-toepassingen en micro soft-service-Api's, bijvoorbeeld [Microsoft Graph api's][Microsoft-Graph].

## <a name="claim"></a>optreedt

Een [beveiligings token](#security-token) bevat claims, waarmee beweringen over één entiteit (zoals een [client toepassing](#client-application) of [resource-eigenaar](#resource-owner)) aan een andere entiteit (zoals de [resource server](#resource-server)) worden verstrekt. Claims zijn naam/waarde-paren die feiten over het onderwerp van de token door geven (bijvoorbeeld de beveiligingsprincipal die is geverifieerd door de [autorisatie server](#authorization-server)). De claims die aanwezig zijn in een gegeven token zijn afhankelijk van verschillende variabelen, zoals het type token, het type referentie dat wordt gebruikt voor het verifiëren van het onderwerp, de toepassings configuratie, enzovoort.

Zie de naslag informatie voor het [micro soft Identity platform-token][AAD-Tokens-Claims] voor meer details.

## <a name="client-application"></a>client toepassing

Zoals gedefinieerd door het [OAuth2-autorisatie raamwerk][OAuth2-Role-Def], een toepassing die beveiligde bron aanvragen maakt namens de [eigenaar](#resource-owner)van de resource. De term ' client ' impliceert geen specifieke kenmerken voor de hardware-implementatie (bijvoorbeeld of de toepassing wordt uitgevoerd op een server, een bureau blad of andere apparaten).

Een client toepassing vraagt [autorisatie](#authorization) van een resource-eigenaar om deel te nemen aan een [OAuth2-machtigings](#authorization-grant) stroom en heeft toegang tot api's/gegevens voor de naam van de bron eigenaar. Het OAuth2-autorisatie raamwerk [definieert twee typen clients][OAuth2-Client-Types]: ' vertrouwelijk ' en ' openbaar ', op basis van de mogelijkheid van de client om de vertrouwelijkheid van de referenties te hand haven. Toepassingen kunnen een [webclient (vertrouwelijk)](#web-client) implementeren die wordt uitgevoerd op een webserver, een [systeem eigen client (openbaar)](#native-client) die is geïnstalleerd op een apparaat of een [op een gebruiker gebaseerde client (openbaar)](#user-agent-based-client) die wordt uitgevoerd in de browser van een apparaat.

## <a name="consent"></a>vergunning

Het proces van een [resource-eigenaar](#resource-owner) die autorisatie verleent aan een [client toepassing](#client-application)om toegang te krijgen tot beveiligde bronnen onder specifieke [machtigingen](#permissions), namens de eigenaar van de resource. Afhankelijk van de machtigingen die door de client worden aangevraagd, wordt een beheerder of gebruiker gevraagd toestemming te geven om respectievelijk toegang tot hun organisatie/individuele gegevens toe te staan. In een scenario met [meerdere tenants](#multi-tenant-application) wordt de [Service-Principal](#service-principal-object) van de toepassing ook vastgelegd in de Tenant van de toestemmings gebruiker.

Zie het [instemming-Framework](consent-framework.md) voor meer informatie.

## <a name="id-token"></a>ID-token

Een [OpenID Connect Connect-][OpenIDConnect-ID-Token] [beveiligings token](#security-token) dat wordt verschaft door het [autorisatie-eind punt](#authorization-endpoint)van een [autorisatie server](#authorization-server) , dat [claims](#claim) bevat die betrekking hebben op de verificatie van de [resource-eigenaar](#resource-owner)van een eind gebruiker. Net als een toegangs token worden ID-tokens ook weer gegeven als een digitaal ondertekende [JSON Web token (JWT)][JWT]. In tegens telling tot een toegangs token worden claims van een ID-token niet gebruikt voor doel einden met betrekking tot toegang tot resources en specifiek toegangs beheer.

Zie de naslag informatie voor het [micro soft Identity platform-token][AAD-Tokens-Claims] voor meer details.

## <a name="microsoft-identity-platform"></a>Microsoft-identiteitsplatform

Het micro soft Identity-platform is een evolutie van de identiteits service en het ontwikkel platform van de Azure Active Directory (Azure AD). Met het Microsoft Identity Platform kunnen ontwikkelaars toepassingen maken waarbij gebruikers zich met alle Microsoft-identiteiten kunnen aanmelden en waarmee tokens worden opgehaald voor het aanroepen van Microsoft Graph, andere Microsoft-API's of API's die door ontwikkelaars zijn gemaakt. Het is een volledig uitgeruste platform dat bestaat uit een verificatie service, Bibliotheken, toepassings registratie en-configuratie, volledige documentatie voor ontwikkel aars, code voorbeelden en andere inhoud voor ontwikkel aars. Het Microsoft Identity Platform biedt ondersteuning voor standaardprotocollen als OAuth 2.0 en OpenID Connect.

## <a name="multi-tenant-application"></a>multi tenant-toepassing

Een klasse van de toepassing waarmee u zich kunt aanmelden en [toestemming](#consent) wilt geven door gebruikers die zijn ingericht in een Azure AD- [Tenant](#tenant), met inbegrip van andere tenants dan de locatie waar de client is geregistreerd. [Systeem eigen client](#native-client) toepassingen zijn standaard multi tenants, terwijl [web client](#web-client) en [web resource/API-](#resource-server) toepassingen de mogelijkheid hebben om één of meerdere tenants te selecteren. Als u daarentegen een webtoepassing die is geregistreerd als één Tenant, toestaat u alleen aanmeldingen van gebruikers accounts die zijn ingericht in dezelfde Tenant als de gebruiker waar de toepassing is geregistreerd.

Zie [een Azure AD-gebruiker aanmelden met behulp van het patroon voor multi tenant-toepassingen][AAD-Multi-Tenant-Overview] voor meer informatie.

## <a name="native-client"></a>systeem eigen client

Een type [client toepassing](#client-application) dat systeem eigen wordt geïnstalleerd op een apparaat. Omdat alle code op een apparaat wordt uitgevoerd, wordt dit beschouwd als een ' open bare ' client, omdat het niet mogelijk is om referenties persoonlijk of vertrouwelijk op te slaan. Zie [OAuth2-client typen en-profielen][OAuth2-Client-Types] voor meer informatie.

## <a name="permissions"></a>permissions

Een [client toepassing](#client-application) krijgt toegang tot een [bron server](#resource-server) door het declareren van machtigings aanvragen. Er zijn twee typen beschikbaar:

* ' Gedelegeerde ' machtigingen, waarmee toegang op basis van een [bereik](#scopes) wordt opgegeven met behulp van gedelegeerde autorisatie van de aangemelde [resource-eigenaar](#resource-owner), worden in runtime weer gegeven als [' scp ' claims](#claim) in het [toegangs token](#access-token)van de client.
* ' Toepassings machtigingen ', waarmee [op rollen gebaseerde](#roles) toegang met de referenties/identiteit van de client toepassing wordt opgegeven, worden in runtime weer gegeven als [' rollen ' claims](#claim) in het toegangs token van de client.

Ze hebben ook het Opper vlak tijdens het [toestemming](#consent) proces, waardoor de beheerder of de resource-eigenaar de mogelijkheid biedt de client toegang tot resources in hun Tenant toe te kennen of te weigeren.

Machtigings aanvragen worden geconfigureerd op de pagina **API-machtigingen** voor een toepassing in de [Azure Portal][AZURE-portal], door de gewenste ' gedelegeerde machtigingen ' en ' toepassings machtigingen ' te selecteren (hiervoor is het lidmaatschap van de rol van globale beheerder vereist). Omdat een [open bare client](#client-application) niet veilig referenties kan onderhouden, kan deze alleen gedelegeerde machtigingen aanvragen, terwijl een [vertrouwelijke client](#client-application) de mogelijkheid heeft om zowel gedelegeerde als toepassings machtigingen aan te vragen. Het [toepassings object](#application-object) van de client slaat de gedeclareerde machtigingen op in de [eigenschap requiredResourceAccess][Graph-App-Resource].

## <a name="refresh-token"></a>token vernieuwen

Een type [beveiligings token](#security-token) dat is uitgegeven door [een autorisatie server](#authorization-server)en wordt gebruikt door een [client toepassing](#client-application) om een nieuw [toegangs token](#access-token) aan te vragen voordat het toegangs token verloopt. Doorgaans in de vorm van een [JSON Web token (JWT)][JWT].

In tegens telling tot toegangs tokens kunnen vernieuwings tokens worden ingetrokken. Als een client toepassing probeert om een nieuw toegangs token aan te vragen met behulp van een vernieuwings token dat is ingetrokken, weigert de autorisatie server de aanvraag en wordt de client toepassing niet langer gemachtigd voor toegang tot de [bron server](#resource-server) namens de eigenaar van de [resource](#resource-owner).

## <a name="resource-owner"></a>resource-eigenaar

Zoals gedefinieerd door het [OAuth2-autorisatie raamwerk][OAuth2-Role-Def], kan een entiteit toegang tot een beveiligde bron verlenen. Wanneer de resource-eigenaar een persoon is, wordt deze een eind gebruiker genoemd. Wanneer een [client toepassing](#client-application) bijvoorbeeld toegang wil krijgen tot het postvak van een gebruiker via de [Microsoft Graph-API][Microsoft-Graph], is hiervoor toestemming vereist van de resource-eigenaar van het postvak.

## <a name="resource-server"></a>resource server

Zoals gedefinieerd door het [OAuth2-verificatie raamwerk][OAuth2-Role-Def], een server die beveiligde resources host, die beveiligde bron aanvragen kan accepteren en erop kan reageren door [client toepassingen](#client-application) die een [toegangs token](#access-token)aanbieden. Ook wel bekend als een beveiligde resource server of een resource toepassing.

Een resource server stelt Api's beschikbaar en dwingt de toegang tot de beveiligde resources af via [scopes](#scopes) en [rollen](#roles), met behulp van het OAuth 2,0-autorisatie raamwerk. Voor beelden hiervan zijn de [Microsoft Graph-API][Microsoft-Graph] die toegang biedt tot gegevens van Azure AD-tenants en de Microsoft 365-api's die toegang bieden tot informatie zoals e-mail en agenda.

Net als bij een client toepassing wordt de identiteits configuratie van de bron toepassing tot stand gebracht via [registratie](#application-registration) in een Azure AD-Tenant, waardoor zowel het toepassings-als Service-Principal-object wordt geleverd. Sommige door micro soft geleverde Api's, zoals de Microsoft Graph-API, hebben vooraf geregistreerde service-principals die tijdens het inrichten beschikbaar zijn gemaakt in alle tenants.

## <a name="roles"></a>rollen

Net als bij [bereiken](#scopes)bieden rollen een manier om de toegang tot de beveiligde bronnen van een [bron server](#resource-server) te regelen. Er zijn twee typen: de rol ' gebruiker ' implementeert toegangs beheer op basis van rollen voor gebruikers/groepen die toegang tot de resource nodig hebben, terwijl een toepassing wordt geïmplementeerd voor [client toepassingen](#client-application) die toegang nodig hebben.

Rollen zijn door de resource gedefinieerde teken reeksen (bijvoorbeeld ' fiatteur onkosten ', ' alleen-lezen ', ' Directory. ReadWrite. all '), beheerd in de [Azure Portal][AZURE-portal] via het [toepassings manifest](#application-manifest)van de resource en opgeslagen in de [eigenschap appRoles][Graph-Sp-Resource]van de resource. De Azure Portal wordt ook gebruikt om gebruikers toe te wijzen aan gebruikers rollen en client [toepassings machtigingen](#permissions) te configureren voor toegang tot een functie van de toepassing.

Zie [Graph API-machtigings bereik][Graph-Perm-Scopes]voor een gedetailleerde bespreking van de toepassings rollen die worden weer gegeven door de Microsoft Graph-API. Zie [Azure-roltoewijzingen toevoegen of verwijderen met behulp van de Azure Portal][AAD-RBAC]voor een stapsgewijze implementatie-voor beeld.

## <a name="scopes"></a>bereiken

Net als [rollen](#roles)bieden bereiken een manier waarop een [bron server](#resource-server) toegang tot de beveiligde bronnen kan beheren. Bereiken worden gebruikt voor het implementeren van toegangs beheer op [basis van scopes][OAuth2-Access-Token-Scopes] voor een [client toepassing](#client-application) waarvan de eigenaar toegang heeft gekregen tot de resource.

Bereiken zijn door resources gedefinieerde teken reeksen (bijvoorbeeld ' mail. read ', ' Directory. ReadWrite. all '), beheerd in de [Azure Portal][AZURE-portal] via het [toepassings manifest](#application-manifest)van de resource en opgeslagen in de [eigenschap oauth2Permissions][Graph-Sp-Resource]van de resource. De Azure Portal wordt ook gebruikt om de [gedelegeerde machtigingen](#permissions) van de client toepassing te configureren voor toegang tot een bereik.

Een best practice naam Conventie is het gebruik van een ' resource. Operation. CONSTRAINT '-indeling. Zie [Graph API-machtigings bereik][Graph-Perm-Scopes]voor een gedetailleerde bespreking van de bereiken die worden weer gegeven door Microsoft Graph-API. Zie [Microsoft 365 API permissions Reference][O365-Perm-Ref](Engelstalig) voor bereiken die worden weer gegeven door Microsoft 365 Services.

## <a name="security-token"></a>beveiligings token

Een ondertekend document met claims, zoals een OAuth2-token of SAML 2,0-bevestiging. Voor een OAuth2- [machtigings toekenning](#authorization-grant), een [toegangs token](#access-token) (OAuth2), een [vernieuwings token](#refresh-token)en een [id-token](https://openid.net/specs/openid-connect-core-1_0.html#IDToken) , zijn typen beveiligings tokens, die allemaal zijn geïmplementeerd als een [JSON Web token (JWT)][JWT].

## <a name="service-principal-object"></a>Service-Principal-object

Wanneer u een toepassing registreert of bijwerkt in de [Azure Portal][AZURE-portal], maakt/werkt de portal zowel een [toepassings object](#application-object) als een bijbehorend Service-Principal-object voor die Tenant. Het toepassings object *definieert* de identiteits configuratie globaal van de toepassing (in alle tenants waar de bijbehorende toepassing toegang is verleend) en is de sjabloon waaruit de bijbehorende service-principal-object (en) zijn *afgeleid* voor gebruik in runtime (in een specifieke Tenant).

Zie [Application and Service Principal Objects][AAD-App-SP-Objects](Engelstalig) voor meer informatie.

## <a name="sign-in"></a>aanmelden

Het proces van een [client toepassing](#client-application) die de verificatie van eind gebruikers initieert en gerelateerde status vastlegt, voor het verkrijgen van een [beveiligings token](#security-token) en het bereik van de toepassings sessie met die status. De status kan artefacten zoals gebruikers profiel gegevens en informatie die is afgeleid van token claims bevatten.

De aanmeldings functie van een toepassing wordt doorgaans gebruikt voor het implementeren van eenmalige aanmelding (SSO). Het kan ook worden voorafgegaan door een ' registratie functie ', zoals het toegangs punt voor een eind gebruiker om toegang te krijgen tot een toepassing (bij het eerste aanmelden). De registratie functie wordt gebruikt voor het verzamelen en persistent maken van aanvullende status die specifiek is voor de gebruiker en kan toestemming van de [gebruiker](#consent)vereisen.

## <a name="sign-out"></a>afmelden

Het proces waarbij een eind gebruiker wordt geverifieerd, de gebruikers status die is gekoppeld aan de sessie van de [client toepassing](#client-application) wordt ontkoppeld tijdens het [Aanmelden](#sign-in)

## <a name="tenant"></a>tenant

Een exemplaar van een Azure AD-Directory wordt een Azure AD-Tenant genoemd. Het biedt verschillende functies, waaronder:

* een register service voor geïntegreerde toepassingen
* verificatie van gebruikers accounts en geregistreerde toepassingen
* REST-eind punten die vereist zijn voor de ondersteuning van verschillende protocollen, waaronder OAuth2 en SAML, met inbegrip van het [autorisatie-eind](#authorization-endpoint)punt, [token eindpunt](#token-endpoint) en het ' algemene ' eind punt dat wordt gebruikt door [multi tenant-toepassingen](#multi-tenant-application).

Azure AD-tenants worden tijdens de registratie gemaakt/gekoppeld aan Azure-en Microsoft 365-abonnementen, met identiteits-& toegangs beheer functies voor het abonnement. Beheerders van Azure-abonnementen kunnen ook extra Azure AD-tenants maken via de Azure Portal. Zie [een Azure Active Directory-Tenant verkrijgen][AAD-How-To-Tenant] voor meer informatie over de verschillende manieren waarop u toegang kunt krijgen tot een Tenant. Zie [een Azure-abonnement koppelen aan of toevoegen aan uw Azure Active Directory-Tenant][AAD-How-Subscriptions-Assoc] voor meer informatie over de relatie tussen abonnementen en een Azure AD-Tenant, en voor instructies over het koppelen of toevoegen van een abonnement aan een Azure AD-Tenant.

## <a name="token-endpoint"></a>token eindpunt

Een van de eind punten die door de [autorisatie server](#authorization-server) worden geïmplementeerd ter ondersteuning van OAuth2- [autorisatie subsidies](#authorization-grant). Afhankelijk van de toekenning kan deze worden gebruikt voor het verkrijgen van een [toegangs token](#access-token) (en een gerelateerd ' vernieuwings token) aan een [client](#client-application)of [id-token](#id-token) bij gebruik met het [OpenID Connect Connect][OpenIDConnect] -protocol.

## <a name="user-agent-based-client"></a>Client op basis van een gebruikers agent

Een type [client toepassing](#client-application) dat code downloadt van een webserver en wordt uitgevoerd binnen een gebruikers agent (bijvoorbeeld een webbrowser), zoals een toepassing met één pagina (Spa). Omdat alle code op een apparaat wordt uitgevoerd, wordt dit beschouwd als een ' open bare ' client, omdat het niet mogelijk is om referenties persoonlijk of vertrouwelijk op te slaan. Zie [OAuth2-client typen en-profielen][OAuth2-Client-Types]voor meer informatie.

## <a name="user-principal"></a>gebruikers-principal

Net als bij de manier waarop een Service Principal-object wordt gebruikt om een toepassings exemplaar aan te duiden, is een User Principal-object een ander type beveiligings-principal dat een gebruiker vertegenwoordigt. Het [resource type][Graph-User-Resource] Microsoft Graph-gebruiker definieert het schema voor een gebruikers object, met inbegrip van aan de gebruiker gerelateerde eigenschappen, zoals de voor-en achternaam, de User Principal Name, het lidmaatschap van de Directory-rol, enzovoort. Dit biedt de configuratie van de gebruikers-id voor Azure AD om een gebruikers-principal tijdens runtime in te richten. De User Principal wordt gebruikt voor een geverifieerde gebruiker voor eenmalige aanmelding, het vastleggen van [toestemmings](#consent) overdracht, het maken van beslissingen voor toegangs beheer, enzovoort.

## <a name="web-client"></a>webclient

Een type [client toepassing](#client-application) dat alle code op een webserver uitvoert en kan functioneren als een ' vertrouwelijke ' client door de referenties op de server veilig op te slaan. Zie [OAuth2-client typen en-profielen][OAuth2-Client-Types]voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

De [micro soft Identity platform Developer's Guide][AAD-Dev-Guide] is de landings pagina die moet worden gebruikt voor alle ontwikkel-gerelateerde onderwerpen over micro soft Identity platform, inclusief een overzicht van [toepassings integratie][AAD-How-To-Integrate] en de basis principes van [micro soft Identity platform-verificatie en ondersteunde verificatie scenario's][AAD-Auth-Scenarios]. U kunt ook code voorbeelden & zelf studies vinden om snel aan de slag te gaan met [github](https://github.com/azure-samples?utf8=%E2%9C%93&q=active%20directory&type=&language=).

Gebruik de sectie volgende opmerkingen om feedback te geven en te helpen bij het verfijnen en vormen van deze inhoud, inclusief aanvragen voor nieuwe definities of het bijwerken van bestaande gegevens.

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]:reference-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[Graph-Perm-Scopes]: /graph/permissions-reference
[Graph-App-Resource]: /graph/api/resources/application
[Graph-Sp-Resource]: /graph/api/resources/serviceprincipal?view=graph-rest-beta&preserve-view=true
[Graph-User-Resource]: /graph/api/resources/user
[AAD-How-Subscriptions-Assoc]:../fundamentals/active-directory-how-subscriptions-associated-directory.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-How-To-Tenant]:quickstart-create-new-tenant.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Multi-Tenant-Overview]:howto-convert-app-to-be-multi-tenant.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]:access-tokens.md
[AZURE-portal]: https://portal.azure.com
[AAD-RBAC]: ../../role-based-access-control/role-assignments-portal.md
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[Microsoft-Graph]: https://developer.microsoft.com/graph
[O365-Perm-Ref]: /graph/permissions-reference
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Endpoint]: https://tools.ietf.org/html/rfc6749#section-3.1
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: https://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-AuthZ-Endpoint]: https://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint
[OpenIDConnect-ID-Token]: https://openid.net/specs/openid-connect-core-1_0.html#IDToken