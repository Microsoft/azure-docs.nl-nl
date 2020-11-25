---
title: Flexibiliteit door middel van aanbevolen procedures voor ontwikkel aars met Azure AD B2C | Microsoft Docs
description: Flexibiliteit door middel van aanbevolen procedures voor ontwikkel aars in het identiteits-en toegangs beheer van klanten met Azure AD B2C
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecde474abf3c814b7c3afa4ae18d044868785cf5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919672"
---
# <a name="resilience-through-developer-best-practices"></a>Flexibiliteit door middel van aanbevolen procedures voor ontwikkel aars

In dit artikel delen we enkele informatie die is gebaseerd op onze ervaring van het werken met grote klanten. U kunt deze aanbevelingen overwegen in het ontwerp en de implementatie van uw services.

![Afbeelding toont onderdelen van ontwikkelaars ervaring](media/resilience-b2c-developer-best-practices/developer-best-practices-architecture.png)

## <a name="use-the-microsoft-authentication-library-msal"></a>De micro soft Authentication Library (MSAL) gebruiken

De [micro soft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview) en de [micro soft Identity Web authentication-bibliotheek voor ASP.net](https://docs.microsoft.com/azure/active-directory/develop/reference-v2-libraries) vereenvoudigen het verkrijgen, beheren, opslaan in de cache en het vernieuwen van de tokens die een toepassing nodig heeft. Deze bibliotheken zijn specifiek geoptimaliseerd ter ondersteuning van micro soft-identiteiten, waaronder functies die de toepassings tolerantie verbeteren.

Ontwikkel aars moeten de meest recente releases van MSAL aannemen en up-to-date blijven. Zie [hoe u de tolerantie van verificatie en autorisatie](resilience-app-development-overview.md) in uw toepassingen kunt verhogen. Vermijd het implementeren van uw eigen verificatie stack, waar mogelijk, en gebruik goed ingestelde bibliotheken.

## <a name="optimize-directory-reads-and-writes"></a>Lees-en schrijf bewerkingen van mappen optimaliseren

De Microsoft Azure AD B2C Directory service ondersteunt miljarden verificaties per dag. Het is ontworpen voor een hoge mate van Lees bewerkingen per seconde. Optimaliseer uw schrijf bewerkingen om afhankelijkheden te minimaliseren en de flexibiliteit te verg Roten.

### <a name="how-to-optimize-directory-reads-and-writes"></a>Lees-en schrijf bewerkingen van mappen optimaliseren

- **Vermijd het schrijven van functies voor de Directory bij het aanmelden**: nooit een schrijf bewerking uitvoeren op aanmelden zonder een voor waarde (if-component) in uw aangepaste beleids regels. Een use-case waarvoor schrijven is vereist voor een aanmelding, is [just-in-time migratie van gebruikers wachtwoorden](https://github.com/azure-ad-b2c/user-migration/tree/master/seamless-account-migration). Vermijd een scenario waarbij elke aanmelding moet worden geschreven.

  - De [voor waarden](https://docs.microsoft.com/azure/active-directory-b2c/userjourneys) in een gebruikers traject ziet er als volgt uit:

  ``
  <Precondition Type="ClaimEquals" ExecuteActionsIf="true"> 
  <Value>requiresMigration</Value>
  ...
  < Precondition/>
  ``
  - Bouw vastheid voor bot gestuurde [aanmeldings-ups door te integreren met een captcha-systeem](https://github.com/azure-ad-b2c/samples/tree/master/policies/captcha-integration).

  - Gebruik een voor [beeld van belasting testen](https://docs.microsoft.com/azure/active-directory-b2c/best-practices#testing) om registratie te simuleren en u aan te melden. 

- Meer **informatie over beperking**: de Directory implementeert regels voor toepassings-en Tenant niveau. Er zijn verdere frequentie limieten voor bewerkingen voor lezen/ophalen, schrijven/boeken, bijwerken/plaatsen en verwijderen/verwijderen en elke bewerking heeft verschillende limieten.

  - Een schrijf bewerking op het moment dat de aanmelding wordt verzonden, valt onder een POST voor nieuwe gebruikers of wordt opgeslagen voor bestaande gebruikers.

  - Een aangepast beleid voor het maken of bijwerken van een gebruiker bij elke aanmelding, kan mogelijk een limiet voor het aantal PUT-of POST frequentie op toepassings niveau hebben. Dezelfde limieten gelden bij het bijwerken van Directory-objecten via Azure AD of Microsoft Graph. Controleer ook de Lees bewerkingen zodat het aantal lees bewerkingen bij elke aanmelding tot het minimum wordt beperkt.

  - Schatting van de piek belasting om de frequentie van Directory schrijfopdrachten te voors pellen en te voor komen dat deze wordt beperkt. Schattingen van piek verkeer moeten schattingen bevatten voor acties zoals aanmelden, aanmelden en multi-factor Authentication (MFA). Zorg ervoor dat u zowel het Azure AD B2C systeem als uw toepassing test voor piek verkeer. Het is mogelijk dat Azure AD B2C de belasting kan verwerken zonder beperking, wanneer uw downstream-toepassingen of-services dat niet doen.

  - Begrijp en plan uw migratie tijdlijn. Wanneer u van plan bent gebruikers te migreren naar Azure AD B2C met behulp van Microsoft Graph, moet u rekening houden met de limieten van de toepassing en de Tenant om de benodigde tijd voor de migratie van gebruikers te berekenen. Als u de taak voor het maken van een gebruiker of script splitst met twee toepassingen, kunt u de limiet per toepassing gebruiken. Het zou nog steeds onder de drempel waarde per Tenant moeten blijven.

  - Inzicht in de effecten van uw migratie taak op andere toepassingen. Denk na over het live verkeer dat door andere toepassingen wordt bediend, om ervoor te zorgen dat u geen beperkingen opleveren op Tenant niveau en beroving voor uw Live-toepassing. Zie voor meer informatie de [Microsoft Graph beperkings richtlijnen](https://docs.microsoft.com/graph/throttling).
  
## <a name="extend-token-lifetimes"></a>Levens duur van token uitbreiden

Als de Azure AD B2C-verificatie service niet in staat is om nieuwe aanmeldingen en aanmeldingen te volt ooien, kunt u in een onwaarschijnlijke gebeurtenis de risico beperking bieden voor gebruikers die zijn aangemeld. Met [configuratie](https://docs.microsoft.com/azure/active-directory-b2c/configure-tokens)kunt u gebruikers die al zijn aangemeld, toestaan om de toepassing te blijven gebruiken zonder enige onderbreking totdat de gebruiker zich afmeldt bij de toepassing of wanneer de [sessie](https://docs.microsoft.com/azure/active-directory-b2c/session-behavior) is verlopen vanwege inactiviteit.

Uw bedrijfs vereisten en de gewenste eindgebruikers ervaring bepalen uw frequentie voor het vernieuwen van tokens voor zowel web-als single-page-toepassingen (SPAs).

### <a name="how-to-extend-token-lifetimes"></a>De levens duur van tokens uitbreiden

- **Webtoepassingen**: voor webtoepassingen waarbij het verificatie token is gevalideerd aan het begin van de aanmelding, is de toepassing afhankelijk van de sessie cookie, zodat de geldigheid van de sessie kan worden uitgebreid.

  - Gebruikers in staat stellen om aangemeld te blijven door gehoste sessie tijden te implementeren waarmee sessies op basis van gebruikers activiteit blijven worden vernieuwd. Als er sprake is van een onderbreking van de uitgifte van tokens op lange termijn, kunnen deze sessie tijden verder worden verhoogd als een eenmalige-configuratie voor de toepassing. De levens duur van de sessie beperken tot het Maxi maal toegestane aantal.

- **Spas**: een beveiligd-wachtwoord verificatie kan afhankelijk zijn van toegangs tokens om aanroepen naar de api's uit te voeren. Een beveiligd-wachtwoord verificatie gebruikt traditioneel de impliciete stroom die niet resulteert in een vernieuwings token. De beveiligd-wachtwoord verificatie kan een verborgen iframe gebruiken om nieuwe token aanvragen uit te voeren op het autorisatie-eind punt als de browser nog steeds een actieve sessie met de Azure AD B2C heeft. Voor SPAs zijn er enkele opties beschikbaar waarmee de gebruiker de toepassing kan blijven gebruiken.

  - Breid de geldigheids duur van het toegangs token uit om te voldoen aan uw bedrijfs vereisten.

  - Bouw uw toepassing om een API-gateway als verificatie proxy te gebruiken. In deze configuratie wordt de beveiligd-wachtwoord verificatie geladen zonder authenticatie en worden de API-aanroepen naar de API-gateway verzonden. De API-gateway stuurt de gebruiker via een aanmeldings proces met behulp van een [autorisatie code toekenning](https://oauth.net/2/grant-types/authorization-code/) op basis van een beleid en verifieert de gebruiker. Vervolgens wordt de verificatie sessie tussen de API-gateway en de client onderhouden met behulp van een verificatie cookie. De Api's worden verwerkt via de API-gateway met behulp van het token dat wordt verkregen door de API-gateway of een andere directe verificatie methode zoals certificaten, client referenties of API-sleutels.

  - [Migreer uw beveiligd-wachtwoord verificatie vanuit impliciete toekenning](https://developer.microsoft.com/identity/blogs/msal-js-2-0-supports-authorization-code-flow-is-now-generally-available/) naar [autorisatie code toekenning stroom](https://docs.microsoft.com/azure/active-directory-b2c/implicit-flow-single-page-application) met bewijs sleutel voor code Exchange (PKCE) en ondersteuning voor het gebruik van meerdere bronnen (CORS). Migreer uw toepassing van MSAL.js 1. x naar MSAL.js 2. x om de tolerantie van webtoepassingen te realiseren.

  - Voor mobiele toepassingen is het raadzaam om zowel de levens duur van het vernieuwen en het toegangs token uit te breiden.

- **Back-end-of micro service-toepassingen**: omdat een back-end-toepassing (daemon) niet-interactief is en zich niet in een gebruikers context bevindt, wordt de kandidaat van het dief stal van tokens aanzienlijk verminderd. Aanbeveling is een evenwicht tussen beveiliging en levens duur te halen en een lange levens duur van het token in te stellen.

## <a name="configure-single-sign-on"></a>Eenmalige aanmelding configureren

Met [eenmalige aanmelding (SSO)](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)kunnen gebruikers zich eenmaal aanmelden met één account en toegang krijgen tot meerdere toepassingen. De toepassing kan een web-, mobiele of een toepassing met één pagina zijn, ongeacht het platform of de domein naam. Wanneer de gebruiker zich voor het eerst aanmeldt bij een toepassing, wordt Azure AD B2C een [cookie op basis van cookies](https://docs.microsoft.com/azure/active-directory-b2c/session-overview).

Bij volgende verificatie aanvragen Azure AD B2C leest en valideert de cookie op basis van cookies en wordt een toegangs token uitgegeven zonder dat de gebruiker wordt gevraagd om zich opnieuw aan te melden. Als eenmalige aanmelding is geconfigureerd met een beperkt bereik in een beleid of toepassing, is voor latere toegang tot andere beleids regels en toepassingen nieuwe authenticatie vereist.

### <a name="how-to-configure-sso"></a>Eenmalige aanmelding configureren

[CONFIGUREER SSO](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso-quick-start) als Tenant-breed (standaard) zodat meerdere toepassingen en gebruikers stromen in uw Tenant dezelfde gebruikers sessie kunnen delen. Configuratie voor de hele Tenant biedt de meeste flexibiliteit voor nieuwe verificatie.  

## <a name="safe-deployment-practices"></a>Veilige implementatiemethoden

De meest voorkomende onderbrekingen van de service zijn de code-en configuratie wijzigingen. Acceptatie van doorlopende integratie en CICD-processen en hulpprogram ma's voor snelle implementatie op grote schaal en vermindert menselijke fouten tijdens het testen en implementeren naar productie. CICD voor fout reductie, efficiëntie en consistentie. [Azure-pijp lijnen](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/cicd-data-overview) is een voor beeld van CICD.

## <a name="web-application-firewall"></a>Web Application Firewall

Bescherm uw toepassingen tegen bekende beveiligings problemen, zoals DDoS-aanvallen (Distributed Denial of service), SQL-injecties, cross-site scripting, uitvoering van externe code en vele andere zoals beschreven in [OWASP Top 10](https://owasp.org/www-project-top-ten/). De implementatie van een Web Application firewall (WAF) kan worden beschermd tegen veelvoorkomende aanvallen en beveiligings problemen.

- Gebruik Azure [WAF](https://docs.microsoft.com/azure/web-application-firewall/overview). Dit biedt gecentraliseerde beveiliging tegen aanvallen.

- Gebruik WAF met Azure AD [Identity Protection en voorwaardelijke toegang om multi-laag beveiliging te bieden](https://docs.microsoft.com/azure/active-directory-b2c/conditional-access-identity-protection-overview) wanneer u Azure AD B2C gebruikt.  

## <a name="secrets-rotation"></a>Geheimen rouleren

Azure AD B2C maakt gebruik van geheimen voor toepassingen, Api's, beleids regels en versleuteling. De geheimen beveiligde authenticatie, externe interacties en opslag. Het National Institute of Standards and Technology (NIST) roept de tijds Panne aan waarin een specifieke sleutel wordt geautoriseerd voor gebruik door legitieme entiteiten van een cryptoperiod. Kies de juiste lengte van [cryptoperiod](https://csrc.nist.gov/publications/detail/sp/800-57-part-1/rev-5/final) om te voldoen aan de behoeften van uw bedrijf. Ontwikkel aars moeten de verval datum en de geheimen voor het verstrijken van het geheim hand matig instellen.

### <a name="how-to-implement-secret-rotation"></a>Een geheime draaiing implementeren

- Gebruik [beheerde identiteiten](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) voor ondersteunde bronnen om te verifiëren bij elke service die ondersteuning biedt voor Azure AD-verificatie. Wanneer u beheerde identiteiten gebruikt, kunt u resources automatisch beheren, met inbegrip van het draaien van referenties.

- Maak een inventarisatie van alle [sleutels en certificaten](https://docs.microsoft.com/azure/active-directory-b2c/policy-keys-overview) die in azure AD B2C zijn geconfigureerd. Deze lijst bevat waarschijnlijk sleutels die worden gebruikt in aangepaste beleids regels, [api's](https://docs.microsoft.com/azure/active-directory-b2c/secure-rest-api), token voor ondertekening-id's en certificaten voor SAML.

- Gebruik CICD om geheimen te laten verlopen binnen twee maanden vanaf het verwachte piek seizoen. De aanbevolen maximale cryptoperiod van persoonlijke sleutels die zijn gekoppeld aan een certificaat is één jaar.

- Proactief de toegangs referenties van de API bewaken en draaien, zoals wacht woorden en certificaten.

## <a name="test-rest-apis"></a>REST-Api's testen

In de context van tolerantie moet het testen van REST-Api's de verificatie omvatten: HTTP-codes, de reactie Payload, headers en prestaties. Testen mogen geen blije Path-tests omvatten, maar ook controleren of de API probleem scenario's correct verwerkt.

### <a name="how-to-test-apis"></a>Api's testen

We raden aan uw test plan [uitgebreide API-tests](https://docs.microsoft.com/azure/active-directory-b2c/best-practices#testing)op te nemen. Als u van plan bent om een aanstaande stroom te maken vanwege promotie-of vakantie verkeer, moet u de belasting testen met de nieuwe schattingen herzien. Voer belasting tests uit van uw Api's en Content Delivery Network (CDN) in een ontwikkel omgeving en niet in productie.

## <a name="next-steps"></a>Volgende stappen

- [Flexibiliteits bronnen voor Azure AD B2C-ontwikkel aars](resilience-b2c.md)
  - [Flexibele ervaring voor eind gebruikers](resilient-end-user-experience.md)
  - [Robuuste interfaces met externe processen](resilient-external-processes.md)
  - [Flexibiliteit door middel van bewaking en analyse](resilience-with-monitoring-alerting.md)
- [Flexibiliteit in uw verificatie-infra structuur maken](resilience-in-infrastructure.md)
- [Verhoog de flexibiliteit van verificatie en autorisatie in uw toepassingen](resilience-app-development-overview.md)
