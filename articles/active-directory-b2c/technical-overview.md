---
title: Technisch overzicht en functieoverzicht van Azure Active Directory B2C
description: Een uitvoerige bespreking van de functies en technologieën in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 05/28/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: fef192ab273a0e53958cda315932f91b427a5d7d
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97108380"
---
# <a name="technical-and-feature-overview-of-azure-active-directory-b2c"></a>Technisch overzicht en functieoverzicht van Azure Active Directory B2C

Dit artikel hoort bij [Wat is Azure Active Directory B2C?](overview.md) en biedt een meer uitgebreide inleiding van de service. Dit zijn de belangrijkste resources waarmee u samenwerkt in de service, de functies ervan en hoe u hiermee een volledig aangepaste identiteitservaring kunt bieden voor uw klanten in uw toepassingen.

## <a name="azure-ad-b2c-tenant"></a>Azure AD B2C-tenant

In Azure Active Directory B2C (Azure AD B2C) vertegenwoordigt een *tenant* uw organisatie en bestaat deze uit een directory, ook wel adreslijst genoemd. Elke Azure AD B2C-tenant is uniek en staat volledig los van andere Azure AD B2C-tenants. Een Azure AD B2C-tenant is iets anders dan een Azure Active Directory-tenant, die u misschien al hebt.

Dit zijn de primaire resources waarmee u werkt in een Azure AD B2C-tenant:

* **Directory-** : de *directory* is de locatie waar Azure AD B2C de referenties en profielgegevens van uw gebruikers opslaat, evenals de registraties van uw toepassingen.
* **Toepassingsregistraties**: u kunt uw web-, mobiele en systeemeigen toepassingen registreren bij Azure AD B2C om identiteitsbeheer in te schakelen. Dit geldt ook voor eventuele API's die u wilt beveiligen met Azure AD B2C.
* **Gebruikersstromen** en **aangepaste beleidsregels**: de ingebouwde (gebruikersstromen) en volledig aanpasbare (aangepaste beleidsregels) identiteitservaringen voor uw toepassingen.
  * Gebruik *gebruikersstromen* voor snelle configuratie en inschakeling van algemene identiteitstaken zoals registreren, aanmelden en profielbewerking.
  * Gebruik *aangepaste beleidsregels* om gebruikerservaringen niet alleen in te schakelen voor de algemene identiteitstaken, maar ook voor het bieden van ondersteuning voor complexe identiteitswerkstromen die uniek zijn voor uw organisatie, klanten, werknemers, partners en burgers.
* **Id-providers**: federatie-instellingen voor:
  * Id-providers voor *sociale netwerken* zoals Facebook, LinkedIn of Twitter die u wilt ondersteunen in uw toepassingen.
  * *Externe* id-providers die ondersteuning bieden voor standaardidentiteitsprotocollen zoals OAuth 2.0 en OpenID Connect.
  * *Lokale* accounts waarmee gebruikers zich kunnen registreren en kunnen aanmelden met een gebruikersnaam (of een e-mailadres of een andere id) en een wachtwoord.
* **Sleutels**: u kunt versleutelingssleutels toevoegen en beheren voor het ondertekenen en valideren van tokens, clientgeheimen, certificaten en wachtwoorden.

Een Azure AD B2C-tenant is de eerste resource die u moet maken om aan de slag te gaan met Azure AD B2C. Ga voor meer informatie naar [Zelfstudie: Een Azure Active Directory B2C-tenant maken](tutorial-create-tenant.md).

## <a name="accounts-in-azure-ad-b2c"></a>Accounts in Azure AD B2C

In Azure AD B2C wordt onderscheid gemaakt tussen verschillende typen gebruikersaccounts. Azure Active Directory, Azure Active Directory B2B en Azure Active Directory B2C delen deze accounttypen.

* **Werkaccount**: gebruikers met werkaccounts kunnen resources in een tenant beheren, en met de rol van beheerder kunnen ze tenants ook beheren. Gebruikers met werkaccounts kunnen nieuwe consumentenaccounts maken, wachtwoorden opnieuw instellen, accountsblokkeren/deblokkeren, en machtigingen instellen of een account toewijzen aan een beveiligingsgroep.
* **Gastaccount**: externe gebruikers die u als gast uitnodigt voor uw tenant. Een typisch scenario voor het uitnodigen van een gastgebruiker voor uw Azure AD B2C-tenant is voor het delen van beheerderstaken.
* **Consumentenaccount**: consumentenaccounts zijn de accounts die worden gemaakt in uw Azure AD B2C-directory wanneer gebruikers het registratieproject voltooien in een toepassing die u hebt geregistreerd in uw tenant.

![Pagina in Azure AD B2C voor gebruikersbeheer via de Azure-portal](media/technical-overview/portal-01-users.png)<br/>*Afbeelding: Directory van gebruikers van een Azure Active Directory B2C-tenant in de Azure-portal*

### <a name="consumer-accounts"></a>Consumentenaccounts

Met een *consumentenaccount* kunnen gebruikers zich aanmelden bij de toepassingen die u hebt beveiligd met Azure AD B2C. Gebruikers met consumentenaccounts hebben echter geen toegang tot Azure-resources, zoals de Azure-portal.

Een consumentenaccount kan worden gekoppeld aan deze identiteitstypen:

* **Lokale** identiteit, waarbij de gebruikersnaam en het wachtwoord lokaal worden opgeslagen in de Azure AD B2C-directory. We verwijzen vaak naar deze identiteiten als 'lokale accounts'.
* Identiteiten voor **sociale netwerken** of **ondernemingen**, waarbij de identiteit van de gebruiker wordt beheerd door een federatieve id-provider zoals Facebook, Microsoft, ADFS of Salesforce.

Een gebruiker met een consumentenaccount kan zich met verschillende identiteiten aanmelden, bijvoorbeeld gebruikersnaam, e-mailadres, werknemer-id of overheids-id. Eén account kan meerdere identiteiten hebben, zowel lokaal als sociaal.

![Identiteiten van consumentenaccounts](media/technical-overview/identities.png)<br/>*Afbeelding: Eén consumentenaccount met meerdere identiteiten in Azure AD B2C*

Met Azure AD B2C kunt u algemene kenmerken van profielen voor consumentenaccounts beheren, zoals de weergavenaam, de achternaam en de woonplaats. U kunt ook het Azure AD-schema uitbreiden om aanvullende informatie over uw gebruikers op te slaan. Denk hierbij aan hun land/regio, voorkeurstaal en voorkeuren zoals of ze zich willen abonneren op een nieuwsbrief of meervoudige verificatie willen inschakelen.

Meer informatie over de typen gebruikersaccounts in Azure AD B2C kunt u lezen in [Overzicht van gebruikersaccount in Azure Active Directory B2C](user-overview.md).

## <a name="external-identity-providers"></a>Externe id-providers

U kunt Azure AD B2C configureren zodat gebruikers zich kunnen aanmelden bij uw toepassing met referenties van externe id-providers voor sociale netwerken of voor ondernemingen (IdP). Azure AD B2C ondersteunt externe id-providers zoals Facebook, Microsoft-account, Google, Twitter en iedere andere id-provider die ondersteuning biedt voor OAuth 1.0, OAuth 2.0, OpenID Connect en SAML-protocollen.

![Externe id-providers](media/technical-overview/external-idps.png)

Met federatie van externe id-providers kunt u uw consumenten de mogelijkheid bieden om zich aan te melden met hun bestaande accounts voor sociale netwerken of onderneming, zonder dat u een nieuw account hoeft te maken voor alleen uw toepassing.

Op de registratie- of aanmeldingspagina wordt door Azure AD B2C een lijst weergegeven met externe id-providers waaruit de gebruiker kan kiezen om zich aan te melden. Zodra ze een van de externe id-providers hebben geselecteerd, worden ze omgeleid naar de website van de geselecteerde provider om daar het aanmeldingsproces te voltooien. Nadat de gebruiker zich heeft aangemeld, keert deze terug naar Azure AD B2C voor verificatie van het account in uw toepassing.

![Voorbeeld van mobiele aanmelding met een account voor sociale netwerken (Facebook)](media/technical-overview/external-idp.png)

Als u meer wilt weten over het toevoegen van id-providers in Azure AD B2C, gaat u naar [Zelfstudie: Id-providers toevoegen aan uw toepassingen in Azure Active Directory B2C](tutorial-add-identity-providers.md).

## <a name="identity-experiences-user-flows-or-custom-policies"></a>Identiteitservaringen: gebruikersstromen of aangepaste beleidsregels

Het uitbreidbare beleidsschema van Azure AD B2C is wat de service zo krachtig maakt. Beleidsregels beschrijven de identiteitservaringen van uw gebruikers, zoals registreren, aanmelden en profielbewerking.

In Azure AD B2C zijn er twee hoofdtrajecten die u kunt volgen om deze identiteitservaringen aan te bieden: gebruikersstromen en aangepaste beleidsregels.

* **Gebruikersstromen** zijn vooraf gedefinieerde, ingebouwde, configureerbare beleidsregels die we aanbieden, zodat u binnen een paar minuten ervaringen kunt maken voor registratie, aanmelden en profielbewerking.

* Via **aangepaste beleidsregels** kunt u uw eigen gebruikersbelevingen samenstellen voor scenario's met complexe identiteitservaringen.

Zowel gebruikersstromen als aangepaste beleidsregels maken gebruik van het *Identity Experience Framework*, de engine van Azure AD B2C voor het orkestreren van beleid.

### <a name="user-flow"></a>Gebruikersstroom

Om u te helpen snel de meest voorkomende identiteitstaken in te stellen, bevat de Azure-portal verschillende vooraf gedefinieerde en configureerbare beleidsregels die *gebruikersstromen* worden genoemd.

U kunt instellingen voor gebruikersstromen zoals deze configureren om het gedrag van identiteitservaringen in uw toepassingen te beheren:

* Accounttypen die worden gebruikt voor aanmelding, zoals accounts voor sociale netwerken zoals een Facebook-account of lokale accounts die gebruikmaken van een e-mail adres en wachtwoord voor aanmelding
* Kenmerken die moeten worden verzameld voor de consument, zoals voornaam, postcode of het land of de regio van vestiging
* Azure AD MFA (Multi-Factor Authentication)
* Aanpassing van de gebruikersinterface
* Een set claims in een token die door de toepassing wordt ontvangen nadat de gebruiker de gebruikersstroom heeft voltooid
* Sessiebeheer
* ...en meer.

De meeste gangbare identiteitsscenario's voor het merendeel van mobiele toepassingen, webtoepassingen en toepassingen met één pagina kunnen effectief worden gedefinieerd en geïmplementeerd met behulp van gebruikersstromen. Het wordt aangeraden de ingebouwde gebruikersstromen te gebruiken tenzij er sprake is van complexe scenario's voor gebruikersbelevingen waarvoor de volledige flexibiliteit van aangepaste beleidsregels vereist is.

Meer informatie over gebruikersstromen kunt u lezen in [Gebruikersstromen in Azure Active Directory B2C](user-flow-overview.md).

### <a name="custom-policy"></a>Aangepast beleid

Aangepaste beleidsregels bieden toegang tot de volledige kracht van de engine voor het orkestreren van beleid met de naam Identity Experience Framework (IEF). Met aangepaste beleidsregels kunt u IEF inzetten om bijna elke ervaring voor authenticatie, gebruikersregistratie of profielbewerking te bouwen die u zich maar kunt voorstellen.

Het Identity Experience Framework biedt u de mogelijkheid om gebruikersbelevingen te ontwikkelen met elke combinatie van stappen. Bijvoorbeeld:

* Federatie met andere id-providers
* Oplossingen voor meervoudige verificatie door eerste en externe partijen
* Verzamelen van gebruikersinvoer
* Integratie met externe systemen via communicatie met behulp van een REST-API

Elke op deze manier ontwikkelde gebruikersbeleving wordt gedefinieerd door een beleid en u kunt zo veel of zo weinig beleidsregels maken als nodig zijn om de beste gebruikerservaring voor uw organisatie te maken.

![Diagram van een voorbeeld van een complexe gebruikersbeleving dat mogelijk is gemaakt door het gebruik van IEF](media/technical-overview/custom-policy.png)

Een aangepast beleid bestaat uit een of meer XML-bestanden die in een hiërarchische keten naar elkaar verwijzen. De XML-elementen definiëren het claimschema, claimtransformaties, inhoudsdefinities, claimproviders, technische profielen, orkestratiestappen voor gebruikerstrajecten en andere aspecten van de identiteitservaring.

De krachtige flexibiliteit van aangepast beleidsregels komt van pas wanneer u complexe identiteitsscenario's moet maken. Ontwikkelaars die aangepaste beleidsregels configureren, moeten de vertrouwde relaties zeer zorgvuldig definiëren en rekening houden met zaken zoals eindpunten van metagegevens, exacte definities voor claimuitwisseling, en het configureren van geheimen, sleutels en certificaten die nodig zijn voor elke id-provider.

Meer informatie over aangepaste beleidsregels vindt u in [Aangepaste beleidsregels in Azure Active Directory B2C](custom-policy-overview.md).

## <a name="protocols-and-tokens"></a>Protocollen en tokens

- Voor toepassing ondersteunt Azure AD B2C de [OAuth 2.0](protocols-overview.md), [OpenID Connect](openid-connect.md) en [SAML-protocollen](connect-with-saml-service-providers.md) voor gebruikersbelevingen. De gebruikerservaring wordt via de toepassing gestart door verificatieaanvragen te verzenden naar Azure AD B2C. Het resultaat van een aanvraag bij Azure AD B2C is een beveiligingstoken, zoals een [id-token of toegangstoken](tokens-overview.md) of SAML-token. Met dit beveiligingstoken wordt de identiteit van de gebruiker gedefinieerd binnen de toepassing.

- Voor externe identiteiten ondersteunt Azure AD B2C federatie met elke id-provider op basis van OAuth 1.0, OAuth 2.0-, OpenID Connect en SAML.

In het volgende diagram ziet u hoe Azure AD B2C in dezelfde verificatiestroom kan communiceren met behulp van verschillende protocollen:

![Diagram van op OIDC gebaseerde client-app die federeert met SAML op basis van SAML](media/technical-overview/protocols.png)

1. De Relying Party-toepassing initieert een autorisatieaanvraag bij Azure AD B2C met behulp van OpenID Connect.
1. Wanneer een gebruiker van de toepassing zich wil aanmelden met een externe id-provider die het SAML-protocol gebruikt, roept Azure AD B2C het SAML-protocol aan om te communiceren met die id-provider.
1. Nadat de gebruiker de aanmelding met de externe id-provider heeft voltooid, retourneert Azure AD B2C het token naar de Relying Party-toepassing met behulp van OpenID Connect.

## <a name="application-integration"></a>Integratie van toepassingen

Wanneer een gebruiker zich wil aanmelden bij uw toepassing, of het nu gaat om een webtoepassing, een mobiele toepassing, een bureaubladtoepassing of een toepassing met één pagina (SPA), initieert de toepassing een autorisatieaanvraag naar een eindpunt van een gebruikersstroom of aangepast beleid. De gebruikersstroom of het aangepaste beleid definieert en bepaalt de gebruikerservaring. Wanneer de gebruikersstroom is voltooid, bijvoorbeeld de stroom voor *registratie of aanmelding*, wordt er door Azure AD B2C een token gegenereerd en wordt de gebruiker teruggeleid naar uw toepassing.

![Mobiele app met pijlen die de stroom tussen de app en de aanmeldingspagina van Azure AD B2C aangeven](media/technical-overview/app-integration.png)

Meerdere toepassingen kunnen dezelfde gebruikersstroom of hetzelfde aangepaste beleid gebruiken. Meerdere toepassingen kunnen dezelfde gebruikersstroom of hetzelfde aangepaste beleid gebruiken.

Als u zich bijvoorbeeld wilt aanmelden bij een toepassing, gebruikt de toepassing de gebruikersstroom *registreren of aanmelden*. Nadat de gebruiker zich heeft aangemeld, wil deze misschien zijn profiel bewerken, zodat de toepassing een andere autorisatieaanvraag initieert, deze keer met behulp van de gebruikersstroom *profiel bewerken*.

## <a name="seamless-user-experiences"></a>Naadloze gebruikerservaringen

In Azure AD B2C kunt u de identiteitservaringen van uw gebruikers samenstellen, zodat de pagina's die ze zien naadloos samenwerken met de look-and-feel van uw merk. U krijgt bijna volledige controle over de HTML- en CSS-inhoud die aan gebruikers wordt gepresenteerd wanneer ze de identiteitstrajecten van uw toepassing doorlopen. Deze flexibiliteit zorgt ervoor dat u consistentie voor uw merk en visuele consistentie kunt garanderen tussen uw toepassing en Azure AD B2C.

![Schermopnamen van aanmeldingspagina die is aangepast voor bepaald merk](media/technical-overview/seamless-ux.png)

Zie [Customize the user interface in Azure Active Directory B2C](customize-ui-with-html.md) (De gebruikersinterface aanpassen in Azure Active Directory B2C) voor meer informatie over het aanpassen van de UI.

## <a name="localization"></a>Lokalisatie

Taalaanpassing in Azure AD B2C maakt het mogelijk om verschillende talen aan te bieden, zodat u klanten in hun eigen taal kunt bereiken. Microsoft biedt de vertalingen voor 36 talen, maar u kunt ook uw eigen vertalingen gebruiken voor elke taal. Zelfs als uw ervaring is bedoeld voor slechts één taal, kunt u tekst op de pagina's aanpassen.

![Drie aanmeldingspagina's met tekst van de gebruikersinterface in verschillende talen](media/technical-overview/localization.png)

Meer informatie over lokalisatie vindt u in [Language customization in Azure Active Directory B2C](language-customization.md) (Taalaanpassing in Azure Active Directory B2C).

## <a name="add-your-own-business-logic"></a>Uw eigen bedrijfslogica toevoegen

Als u ervoor kiest om aangepast beleid te gebruiken, kunt u integreren met een REST-API in een gebruikersbeleving om uw eigen bedrijfslogica te integreren in de beleving. Zo kan Azure AD B2C gegevens uitwisselen met een RESTful-service om het volgende te doen:

* Aangepaste, gebruikersvriendelijke foutberichten weergeven.
* Gebruikersinvoer valideren om te voorkomen dat onjuist opgemaakte gegevens worden opgeslagen in uw adreslijst van gebruikers. U kunt bijvoorbeeld de gegevens wijzigen die door gebruikers worden ingevoerd, zodat de voornaam een hoofdletter krijgt als deze met allemaal kleine letters wordt ingevoerd.
* Gegevens van gebruikers verrijken door verder integratie met uw zakelijke LOB-toepassing.
* Met behulp van RESTful-aanroepen kunt u bijvoorbeeld pushmeldingen verzenden, bedrijfsdatabases bijwerken, een proces voor gebruikersmigratie uitvoeren, machtigingen beheren en databases controleren.

Loyaliteitsprogramma's zijn een ander scenario dat kan worden gerealiseerd door de ondersteuning van Azure AD B2C voor het aanroepen van REST-API's. Uw RESTful-service kan bijvoorbeeld het e-mailadres van een gebruiker ontvangen, een query uitvoeren op de klantendatabase en vervolgens het loyaliteitsnummer van de gebruiker retourneren aan Azure AD B2C. De geretourneerde gegevens kunnen worden opgeslagen in het adreslijstaccount van de gebruiker in Azure AD B2C en vervolgens worden geëvalueerd in volgende stappen in het beleid of worden opgenomen in het toegangstoken.

![LOB-integratie in een mobiele toepassing](media/technical-overview/lob-integration.png)

U kunt aan elke stap in de gebruikersbeleving die is gedefinieerd door een aangepast beleid, een aanroep van een REST-API toevoegen. U kunt bijvoorbeeld een REST-API aanroepen:

* Tijdens het aanmelden, net voordat Azure AD B2C de referenties valideert
* Direct na het aanmelden
* Voordat Azure AD B2C een nieuw account in de directory maakt
* Nadat Azure AD B2C een nieuw account in de directory maakt
* Voordat Azure AD B2C een toegangstoken uitgeeft

Als u wilt zien hoe u aangepaste beleidsregels gebruikt voor de integratie van een RESTful-API in Azure AD B2C, raadpleegt u [Integrate REST API claims exchanges in your Azure AD B2C custom policy](custom-policy-rest-api-intro.md) (Uitwisseling van REST API-claims integreren in aangepast beleid van Azure AD B2C).

## <a name="protect-customer-identities"></a>Identiteiten van klanten beveiligen

Azure AD B2C voldoet aan de vereisten voor beveiliging, privacy en andere verplichtingen die worden beschreven in het [Vertrouwenscentrum van Microsoft Azure](https://www.microsoft.com/trustcenter/cloudservices/azure).

Sessies worden gemodelleerd als versleutelde gegevens, waarbij de ontsleutelingssleutel alleen bekend is bij de beveiligingstokenservice van Azure AD B2C. Er wordt een sterk versleutelingsalgoritme, AES-192, gebruikt. Alle communicatiepaden worden beveiligd met TLS voor vertrouwelijkheid en integriteit. Onze beveiligingstokenservice maakt gebruik van een EV-certificaat (Extended Validation) voor TLS. Over het algemeen beperkt de beveiligingstokenservice de risico's van cross-site scriptingaanvallen (XSS) door niet-vertrouwde invoer niet weer te geven.

![Diagram van beveiligde gegevens onderweg en at rest](media/technical-overview/user-data.png)

### <a name="access-to-user-data"></a>Toegang tot gebruikersgegevens

Azure AD B2C-tenants hebben een groot aantal kenmerken gemeen met Azure Active Directory-tenants van bedrijven die worden gebruikt voor werknemers en partners. Voorbeelden van gemeenschappelijke aspecten zijn mechanismen voor het weergeven van beheerdersrollen, het toewijzen van rollen en het controleren van activiteiten.

U kunt rollen toewijzen om te bepalen wie bepaalde beheeracties kan uitvoeren in Azure AD B2C, waaronder:

* Alle aspecten van gebruikersstromen maken en beheren
* Het kenmerkschema dat beschikbaar is voor alle gebruikersstromen, maken en beheren
* Id-providers voor gebruik in directe federatie configureren
* Beleidsregels voor het vertrouwensframework in het Identity Experience Framework (aangepaste beleidsregels) maken en beheren
* Geheimen voor federatie en versleuteling in het Identity Experience Framework (aangepaste beleidsregels) beheren

Zie [Administrator role permissions in Azure Active Directory](../active-directory/roles/permissions-reference.md) (Machtigingen voor beheerdersrol in Azure Active Directory) voor meer informatie over Azure AD-rollen, waaronder ondersteuning voor de Azure AD B2C-beheerdersrol.

### <a name="multi-factor-authentication-mfa"></a>Meervoudige verificatie (MFA)

Meervoudige verificatie van Azure AD B2C helpt u bij het beveiligen van de toegang tot gegevens en toepassingen zonder dat uw gebruikers hier overdreven veel van merken. Er wordt extra beveiliging afgedwongen door een tweede vorm van verificatie te vereisen. Daarnaast wordt er sterke verificatie aangeboden met een keuze van verschillende gebruiksvriendelijke verificatiemethoden. Afhankelijk van de configuratiebeslissingen die u als beheerder neemt, moeten uw gebruikers al dan niet reageren op een verzoek van de functie voor meervoudige verificatie.

Zie [Meervoudige verificatie inschakelen in Azure Active Directory B2C](multi-factor-authentication.md) om te lezen hoe u meervoudige verificatie inschakelt in gebruikersstromen.

### <a name="smart-account-lockout"></a>Slimme accountvergrendeling

Om te voorkomen dat via brute-force aanvallen wachtwoorden worden geraden, maakt Azure AD B2C gebruik van een geavanceerde strategie om accounts te vergrendelen op basis van het IP-adres van de aanvraag, de ingevoerde wachtwoorden en verschillende andere factoren. De duur van de vergrendeling wordt automatisch verlengd op basis van het risico en het aantal pogingen.

![Slimme accountvergrendeling](media/technical-overview/smart-lockout1.png)

Zie [Manage threats to resources and data in Azure Active Directory B2C](threat-management.md) (Bedreigingen van resources en gegevens beheren in Azure Active Directory B2C) voor meer informatie over het beheren van instellingen voor wachtwoord beveiliging.

### <a name="password-complexity"></a>Wachtwoordcomplexiteit

Tijdens registratie of het opnieuw instellen van het wachtwoord, moeten uw gebruikers een wachtwoord opgeven dat voldoet aan de complexiteitsregels. Azure AD B2C dwingt standaard een sterk wachtwoordbeleid af. Azure AD B2C biedt ook configuratieopties voor het opgeven van de complexiteitsvereisten waaraan de wachtwoorden van uw klanten moeten voldoen.

U kunt vereisten voor wachtwoordcomplexiteit configureren in zowel [gebruikersstromen](password-complexity.md) als [aangepaste beleidsregels](password-complexity.md).

## <a name="auditing-and-logs"></a>Controle en logboeken

Azure AD B2C stelt auditlogboeken beschikbaar met activiteitsgegevens voor resources, uitgegeven tokens en beheerderstoegang. U kunt deze auditlogboeken gebruiken om een beeld te krijgen van de activiteiten op het platform en om problemen op te sporen. Kort nadat de activiteit heeft plaatsgevonden die de gebeurtenis heeft gegenereerd, zijn er vermeldingen beschikbaar in auditlogboeken.

In een auditlogboek dat beschikbaar is voor uw Azure AD B2C-tenant of voor een bepaalde gebruiker kunt u gegevens vinden zoals:

* Activiteiten met betrekking tot de autorisatie van een gebruiker om toegang te krijgen tot B2C-resources (bijvoorbeeld een beheerder die een lijst met B2C-beleidsregels raadpleegt)
* Activiteiten met betrekking tot adreslijstkenmerken die worden opgehaald op het moment dat een beheerder zich aanmeldt via de Azure-portal
* CRUD-bewerkingen (maken, lezen, bijwerken en verwijderen) op B2C-toepassingen
* CRUD-bewerkingen op sleutels die zijn opgeslagen in een B2C-sleutelcontainer
* CRUD-bewerkingen op B2C-resources (zoals beleidsregels en id-providers)
* Validatie van gebruikersreferenties en uitgifte van tokens

![Auditlogboek voor afzonderlijke gebruiker in de Azure-portal](media/technical-overview/audit-log.png)

Zie [Accessing Azure AD B2C audit logs](view-audit-logs.md) (Toegang tot auditlogboeken van Azure AD B2C) voor meer informatie over auditlogboeken.

### <a name="usage-insights"></a>Inzichten in gebruik

Met Azure AD B2C kunt u vaststellen wanneer mensen zich registreren of aanmelden bij uw web-app, waar uw gebruikers zich bevinden, en welke browsers en besturingssystemen ze gebruiken. Door Azure Application Insights te integreren in Azure AD B2C met behulp van aangepaste beleidsregels, kunt u inzicht krijgen in de manier waarop gebruikers zich registreren, zich aanmelden, hun wachtwoord opnieuw instellen of hun profiel bewerken. Aan de hand van dergelijke kennis kunt u gegevensgestuurde beslissingen nemen voor toekomstige ontwikkelcycli.

Meer informatie over gebruiksanalyse vindt u in [Track user behavior in Azure Active Directory B2C using Application Insights](analytics-with-application-insights.md) (Gebruikersgedrag bijhouden in Azure Active Directory B2C met behulp van Application Insights).

## <a name="next-steps"></a>Volgende stappen

Nu u meer inzicht hebt in de functies en technische aspecten van Azure Active Directory B2C, kunt u beginnen met onze [zelfstudie voor het maken van een Azure Active Directory B2C-tenant](tutorial-create-tenant.md).
