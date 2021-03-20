---
title: Verificatie protocollen in Azure Active Directory B2C | Microsoft Docs
description: Apps rechtstreeks bouwen met behulp van de protocollen die door Azure Active Directory B2C worden ondersteund.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ed393f721d4461ebadea41f8dad707d4881865cd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "78183902"
---
# <a name="azure-ad-b2c-authentication-protocols"></a>Azure AD B2C: verificatie protocollen
Azure Active Directory B2C (Azure AD B2C) biedt identiteit als een service voor uw apps door ondersteuning te bieden voor twee industrie standaard protocollen: OpenID Connect Connect en OAuth 2,0. De service voldoet aan standaarden, maar twee implementaties van deze protocollen kunnen subtiele verschillen hebben.

De informatie in deze hand leiding is handig als u code schrijft door rechtstreeks HTTP-aanvragen te verzenden en af te handelen, in plaats van een open-source-bibliotheek te gebruiken. We raden u aan deze pagina te lezen voordat u de details van elk specifiek protocol hebt bekeken. Maar als u al bekend bent met Azure AD B2C, kunt u rechtstreeks naar [de naslag handleiding voor het protocol](#protocols)gaan.

<!-- TODO: Need link to libraries above -->

## <a name="the-basics"></a>De basisbeginselen
Elke app die gebruikmaakt van Azure AD B2C, moet worden geregistreerd in uw B2C-map in de [Azure Portal](https://portal.azure.com). Tijdens het registratieproces van de app worden enkele waarden verzameld en toegewezen aan uw app:

* Een **toepassings-id** die de app op unieke wijze identificeert.
* Een **omleidings-URI** of **pakket-id** die kan worden gebruikt om antwoorden terug te sturen naar uw app.
* Een paar andere scenario-specifieke waarden. Meer informatie vindt u [in het registreren van uw toepassing](tutorial-register-applications.md).

Nadat u uw app hebt geregistreerd, communiceert deze met Azure Active Directory (Azure AD) door aanvragen te verzenden naar het eind punt:

```
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/authorize
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/token
```

In bijna alle OAuth en OpenID Connect Connect-stromen zijn vier partijen betrokken bij de Exchange:

![Diagram van de vier OAuth 2,0-rollen](./media/protocols-overview/protocols_roles.png)

* De **autorisatie server** is het Azure AD-eind punt. Alles dat betrekking heeft op gebruikers gegevens en-toegang, wordt veilig afgehandeld. Ook worden de vertrouwens relaties tussen de partijen in een stroom verwerkt. Het is verantwoordelijk voor het controleren van de identiteit van de gebruiker, het verlenen en intrekken van de toegang tot bronnen en het uitgeven van tokens. Het wordt ook wel de ID-provider genoemd.

* De **resource-eigenaar** is doorgaans de eind gebruiker. Het is de partij die eigenaar is van de gegevens en biedt de mogelijkheid om externe partijen toegang te geven tot die gegevens of resource.

* De **OAuth-client** is uw app. Het wordt geïdentificeerd aan de hand van de toepassings-ID. Het is doorgaans de partij waarmee eind gebruikers communiceren. Er worden ook tokens van de autorisatie server aangevraagd. De resource-eigenaar moet de client toestemming geven om toegang te krijgen tot de resource.

* De **resource server** is de locatie waar de resource of gegevens zich bevinden. Het vertrouwt de autorisatie server om de OAuth-client veilig te verifiëren en te autoriseren. Er worden ook toegangs tokens van Bearer gebruikt om ervoor te zorgen dat toegang tot een bron kan worden verleend.

## <a name="policies-and-user-flows"></a>Beleid en gebruikers stromen
Weliswaar, Azure AD B2C-beleid zijn de belangrijkste functies van de service. Azure AD B2C breidt de standaard OAuth 2,0-en OpenID Connect Connect-protocollen uit door beleid te introduceren. Hiermee kunnen Azure AD B2C veel meer worden uitgevoerd dan eenvoudige verificatie en autorisatie.

Om u te helpen bij het instellen van de meest voorkomende identiteits taken, bevat de Azure AD B2C Portal vooraf gedefinieerde, Configureer bare beleids regels met de naam **gebruikers stromen**. Gebruikers stromen bieden een volledige beschrijving van de ervaringen van consumenten-id's, waaronder het aanmelden, aanmelden en het bewerken van profielen. Gebruikers stromen kunnen worden gedefinieerd in een administratieve gebruikers interface. Ze kunnen worden uitgevoerd met behulp van een speciale query parameter in HTTP-verificatie aanvragen.

Beleids regels en gebruikers stromen zijn geen standaard functies van OAuth 2,0 en OpenID Connect Connect. Daarom moet u de tijd nemen om ze te begrijpen. Zie de referentie gids voor de [Azure AD B2C-gebruikers stroom](user-flow-overview.md)voor meer informatie.

## <a name="tokens"></a>Tokens
De Azure AD B2C-implementatie van OAuth 2,0 en OpenID Connect Connect maakt uitgebreid gebruik van Bearer-tokens, inclusief Bearer-tokens die worden weer gegeven als JSON-webtokens (JWTs). Een Bearer-token is een licht gewicht beveiligings token dat de ' Bearer ' toegang verleent tot een beveiligde bron.

De Bearer is een partij die het token kan presen teren. Azure AD moet eerst een partij verifiëren voordat deze een Bearer-token kan ontvangen. Maar als de vereiste stappen niet worden uitgevoerd om het token in trans missie en opslag te beveiligen, kan het worden onderschept en gebruikt door een onbedoelde partij.

Sommige beveiligings tokens hebben ingebouwde mechanismen waarmee wordt voor komen dat niet-geautoriseerde partijen deze gebruiken, maar Bearer-tokens hebben dit mechanisme niet. Ze moeten worden getransporteerd in een beveiligd kanaal, zoals een trans port Layer Security (HTTPS).

Als een Bearer-token wordt verzonden buiten een beveiligd kanaal, kan een kwaadwillende partij een man-in-the-middle-aanval gebruiken om het token te verkrijgen en dit te gebruiken om onbevoegde toegang te krijgen tot een beveiligde bron. Dezelfde beveiligings principes zijn van toepassing wanneer Bearer-tokens worden opgeslagen in de cache voor later gebruik. Zorg er altijd voor dat uw app Bearer-tokens op een veilige manier verzendt en opslaat.

Zie [RFC 6750 sectie 5](https://tools.ietf.org/html/rfc6750)(Engelstalig) voor meer informatie over de beveiligings overwegingen voor tokens.

Meer informatie over de verschillende typen tokens die in Azure AD B2C worden gebruikt, is beschikbaar in [de Azure AD-token verwijzing](tokens-overview.md).

## <a name="protocols"></a>Protocollen
Wanneer u klaar bent om een aantal voorbeeld aanvragen te bekijken, kunt u beginnen met een van de volgende zelf studies. Elk komt overeen met een bepaald verificatie scenario. Als u hulp nodig hebt bij het bepalen van de juiste stroom voor u, bekijkt [u de typen apps die u kunt samen stellen met behulp van Azure AD B2C](application-types.md).

* [Mobiele en systeem eigen toepassingen bouwen met behulp van OAuth 2,0](authorization-code-flow.md)
* [Web-apps bouwen met behulp van OpenID Connect Connect](openid-connect.md)
* [Apps met één pagina bouwen met de impliciete OAuth 2,0-stroom](implicit-flow-single-page-application.md)

