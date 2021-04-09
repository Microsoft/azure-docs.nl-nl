---
title: Opmerkingen voor ontwikkel aars voor aangepast beleid
titleSuffix: Azure AD B2C
description: Opmerkingen voor ontwikkel aars over het configureren en onderhouden van Azure AD B2C met aangepast beleid.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 05/19/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c175a6d225be268f27854b9ab63886892cf029fb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105557272"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>Opmerkingen voor ontwikkel aars voor aangepast beleid in Azure Active Directory B2C

Aangepaste beleids configuratie in Azure Active Directory B2C is nu algemeen beschikbaar. Deze configuratie methode is gericht op geavanceerde identiteits ontwikkelaars die complexe identiteits oplossingen bouwen. Aangepaste beleids regels maken de kracht van het Framework voor identiteits ervaring beschikbaar in Azure AD B2C tenants.
Ontwikkel aars van geavanceerde identiteiten die aangepaste beleids regels gebruiken, moeten plannen dat ze enige tijd investeren bij het volt ooien van instructies en het lezen van referentie documenten.

Hoewel de meeste aangepaste beleids opties beschikbaar zijn nu algemeen beschikbaar zijn, zijn er onderliggende mogelijkheden, zoals technische profiel typen en inhouds definitie-Api's die in verschillende fasen van de levens cyclus van de software voor komen. Er zijn nog veel meer. In de volgende tabel wordt het niveau van de beschik baarheid op een gedetailleerder niveau opgegeven.

## <a name="features-that-are-generally-available"></a>Functies die algemeen beschikbaar zijn

- Gebruik aangepaste beleids regels om gebruikers met aangepaste verificatie te schrijven en te uploaden.
    - Beschrijf de stapsgewijze stap van gebruikers door stap als uitwisselingen tussen claim providers.
    - Definieer voorwaardelijke vertakking in de reis van de gebruiker.
- Werk met REST API-services in uw aangepaste gebruikers Reiss voor verificatie.
- Communiceren met id-providers die compatibel zijn met het OpenIDConnect-protocol.
- Communiceren met id-providers die voldoen aan het SAML 2,0-protocol.

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Verantwoordelijkheden van de aangepaste beleids functie-ontwikkel aars instellen

Hand matige beleids configuratie verleent toegang op lager niveau tot het onderliggende platform van Azure AD B2C en resulteert in het maken van een uniek vertrouwens raamwerk. De vele mogelijke permutaties van aangepaste ID-providers, vertrouwens relaties, integraties met externe services en stap-voor-stap-werk stromen vereisen een methode om te ontwerpen en te configureren.

Ontwikkel aars die de aangepaste beleids functie set gebruiken, moeten voldoen aan de volgende richt lijnen:

- Vertrouwd raken met de configuratie taal van het aangepaste beleid en het beheer van sleutel/geheimen. Zie [TrustFrameworkPolicy](trustframeworkpolicy.md)voor meer informatie.
- Eigenaar worden van scenario's en aangepaste integraties. Documenteer uw werk en laat uw organisatie van uw live site op de hoogte.
- Voer methode testen uit.
- Volg de aanbevolen procedures voor software ontwikkeling en fase ring. Er wordt Mini maal één ontwikkel-en test omgeving aanbevolen.
- Blijf op de hoogte van nieuwe ontwikkelingen van de id-providers en services die u integreert met. U kunt bijvoorbeeld wijzigingen bijhouden in geheimen en de geplande en ongeplande wijzigingen in de service.
- Stel actieve bewaking in en bewaak de reactie snelheid van productie omgevingen. Zie [Azure Active Directory B2C: Logboeken verzamelen](analytics-with-application-insights.md)voor meer informatie over de integratie met Application Insights.
- Houd contact-e-mail adressen actueel in het Azure-abonnement en blijf op de hoogte van de e-mail van micro soft live site-team.
- Onderneem tijdige actie wanneer u dit door het team van micro soft live site hebt door gesteld.

## <a name="terms-for-features-in-public-preview"></a>Voor waarden voor functies in open bare preview

- We raden u aan de open bare preview-functies alleen te gebruiken voor evaluatie doeleinden.
- Service Level Agreements (Sla's) gelden niet voor de open bare preview-functies.
- Ondersteunings aanvragen voor open bare preview-functies kunnen worden ingediend via reguliere ondersteunings kanalen.

## <a name="features-by-stage-and-known-issues"></a>Functies per fase en bekende problemen

Aangepaste beleids mogelijkheden zijn constant ontwikkeld. De volgende tabel bevat een index van functies en beschik baarheid van onderdelen.


### <a name="protocols-and-authorization-flows"></a>Protocollen en autorisatie stromen

| Functie | Ontwikkeling | Preview | Algemene beschikbaarheid | Notities |
|-------- | :-----------: | :-------: | :--: | ----- |
| [OAuth2-autorisatie code](authorization-code-flow.md) |  |  | X |  |
| OAuth2-verificatie code met PKCE |  |  | X | [Open bare clients en toepassingen met één pagina](authorization-code-flow.md)  |
| [OAuth2 impliciete stroom](implicit-flow-single-page-application.md) |  |  | X |  |
| [Referenties voor wacht woord van OAuth2-resource-eigenaar](add-ropc-policy.md) |  | X |  |  |
| [OIDC verbinding maken](openid-connect.md) |  |  | X |  |
| [SAML2](saml-service-provider.md)  |  |  |X  | Bindingen na plaatsen en omleiden. |
| OAuth1 |  |  |  | Wordt niet ondersteund. |
| WSFED | X |  |  |  |

### <a name="identify-providers-federation"></a>Providers Federatie identificeren 

| Functie | Ontwikkeling | Preview | Algemene beschikbaarheid | Notities |
|-------- | :-----------: | :-------: | :--: | ----- |
| [OpenID Connect](openid-connect-technical-profile.md) |  |  | X | Bijvoorbeeld Google +.  |
| [OAuth2](oauth2-technical-profile.md) |  |  | X | Bijvoorbeeld Facebook.  |
| [OAuth1](oauth1-technical-profile.md) |  | X |  | Bijvoorbeeld Twitter. |
| [SAML2](identity-provider-generic-saml.md) |  |   | X | Bijvoorbeeld Sales Force, ADFS. |
| WSFED| X |  |  |  |


### <a name="rest-api-integration"></a>Integratie van REST API

| Functie | Ontwikkeling | Preview | Algemene beschikbaarheid | Notities |
|-------- | :-----------: | :-------: | :--: | ----- |
| [REST API met basis verificatie](secure-rest-api.md#http-basic-authentication) |  |  | X |  |
| [REST API met verificatie van client certificaat](secure-rest-api.md#https-client-certificate-authentication) |  |  | X |  |
| [REST API met OAuth2 Bearer-verificatie](secure-rest-api.md#oauth2-bearer-authentication) |  | X |  |  |

### <a name="component-support"></a>Onderdeel ondersteuning

| Functie | Ontwikkeling | Preview | Algemene beschikbaarheid | Notities |
| ------- | :-----------: | :-------: | :--: | ----- |
| [Verificatie op de telefoon factor](phone-factor-technical-profile.md) |  |  | X |  |
| [Azure AD MFA-verificatie](multi-factor-auth-technical-profile.md) |  | X |  |  |
| [Eenmalig wachtwoord](one-time-password-technical-profile.md) |  | X |  |  |
| [Azure Active Directory](active-directory-technical-profile.md) als lokale map |  |  | X |  |
| Azure-e-mail subsysteem voor verificatie via e-mail |  |  | X |  |
| [Aanbieders van e-mail services van derden](custom-email-mailjet.md) |  |X  |  |  |
| [Ondersteuning voor meerdere talen](localization.md)|  |  | X |  |
| [Validatie van predikaten](predicates.md) |  |  | X | Bijvoorbeeld wachtwoord complexiteit. |
| [Besturings elementen weer geven](display-controls.md) |  |X  |  |  |


### <a name="app-ief-integration"></a>App-IEF-integratie

| Functie | Ontwikkeling | Preview | Algemene beschikbaarheid | Notities |
| ------- | :-----------: | :-------: | :--: | ----- |
| Query teken reeks parameter `domain_hint` |  |  | X | Beschikbaar als claim kan worden door gegeven aan IDP. |
| Query teken reeks parameter `login_hint` |  |  | X | Beschikbaar als claim kan worden door gegeven aan IDP. |
| JSON invoegen in gebruikers traject via `client_assertion` | X |  |  | Wordt afgeschaft. |
| JSON invoegen in de gebruikers reis als `id_token_hint` |  | X |  | Go-Forward-benadering voor het door geven van JSON. |
| [ID-provider token door geven aan de toepassing](idp-pass-through-user-flow.md) |  | X |  | Bijvoorbeeld van Facebook naar app. |


### <a name="session-management"></a>Sessie beheer

| Functie | Ontwikkeling | Preview | Algemene beschikbaarheid | Notities |
| ------- | :-----------: | :-------: | :--: | ----- |
| [Standaard-SSO-sessie provider](custom-policy-reference-sso.md#defaultssosessionprovider) |  |  | X |  |
| [Externe aanmeldings sessie provider](custom-policy-reference-sso.md#externalloginssosessionprovider) |  |  | X |  |
| [SAML SSO-sessie provider](custom-policy-reference-sso.md#samlssosessionprovider) |  |  | X |  |
| [OAuthSSOSessionProvider](custom-policy-reference-sso.md#oauthssosessionprovider)  |  | X |  |  |
| [Eenmalige afmelding](session-behavior.md#sign-out)  |  | X |  |  |

### <a name="security"></a>Beveiliging

| Functie | Ontwikkeling | Preview | Algemene beschikbaarheid | Notities |
|-------- | :-----------: | :-------: | :--: | ----- |
| Beleids sleutels: genereren, hand matig, uploaden |  |  | X |  |
| Beleids sleutels-RSA/cert, geheimen |  |  | X |  |


### <a name="developer-interface"></a>Ontwikkelaars interface

| Functie | Ontwikkeling | Preview | Algemene beschikbaarheid | Notities |
| ------- | :-----------: | :-------: | :--: | ----- |
| Azure Portal-IEF UX |  |  | X |  |
| Beleid uploaden |  |  | X |  |
| [Gebruikers reis logboeken Application Insights](troubleshoot-with-application-insights.md) |  | X |  | Wordt gebruikt voor het oplossen van problemen tijdens de ontwikkeling.  |
| [Application Insights gebeurtenis logboeken](analytics-with-application-insights.md) |  | X |  | Wordt gebruikt voor het bewaken van de gebruikers stromen in de productie omgeving. |


## <a name="next-steps"></a>Volgende stappen

- Controleer de [Microsoft Graph bewerkingen die beschikbaar zijn voor Azure AD B2C](microsoft-graph-operations.md)
- Meer informatie over [aangepaste beleids regels en de verschillen met gebruikers stromen](custom-policy-overview.md).
