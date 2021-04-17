---
title: Een Azure Static Web Apps
description: Meer informatie over het configureren van routes, het afdwingen van beveiligingsregels en algemene instellingen voor Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: cshoe
ms.openlocfilehash: 9494bcc9941491bbb82c6a948dce720cb9e51424
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502280"
---
# <a name="configure-azure-static-web-apps"></a>Een Azure Static Web Apps

Configuratie voor Azure Static Web Apps wordt gedefinieerd in destaticwebapp.config.jsin het _bestand,_ waarmee de volgende instellingen worden bepaald:

- Routering
- Verificatie
- Autorisatie
- Terugvalregels
- Overschrijvingen van HTTP-antwoorden
- Globale HTTP-headerdefinities
- Aangepaste MIME-typen

## <a name="file-location"></a>Bestandslocatie

De aanbevolen locatie voor de _staticwebapp.config.jsis_ in de map die is ingesteld als in `app_location` het [werkstroombestand](./github-actions-workflow.md). Het bestand kan echter op elke locatie in de broncodemap van uw toepassing worden geplaatst.

Zie het [voorbeeldconfiguratiebestand](#example-configuration-file) voor meer informatie.

> [!IMPORTANT]
> De [ _routes.jsin_ het bestand](./routes.md) wordt genegeerd als er eenstaticwebapp.config.js _op_ bestaat.

## <a name="routes"></a>Routes

Met routeregels kunt u het patroon definiëren van URL's die toegang tot uw toepassing op internet toestaan. Routes worden gedefinieerd als een matrix met routeringsregels. Zie het [voorbeeldconfiguratiebestand voor](#example-configuration-file) gebruiksvoorbeelden.

- Regels worden gedefinieerd in de `routes` matrix, zelfs als u slechts één route hebt.
- Regels worden uitgevoerd in de volgorde waarin ze worden weergegeven in de `routes` matrix.
- De evaluatie van regels stopt bij de eerste overeenkomst: routeringsregels worden niet aan elkaar vastgeketend.
- U hebt volledige controle over aangepaste rolnamen.
  - Er zijn enkele ingebouwde rolnamen die en [`anonymous`](./authentication-authorization.md) [`authenticated`](./authentication-authorization.md) bevatten.

De routering heeft aanzienlijke overlap met de concepten verificatie (de gebruiker identificeren) en autorisatie (het toewijzen van mogelijkheden aan de gebruiker). Lees de handleiding voor [verificatie en autorisatie](authentication-authorization.md) in dit artikel.

Het standaardbestand voor statische inhoud is _index.html-bestand._

## <a name="defining-routes"></a>Routes definiëren

Elke regel bestaat uit een routepatroon, samen met een of meer van de optionele regeleigenschappen. Routeregels worden gedefinieerd in de `routes` matrix. Zie het [voorbeeldconfiguratiebestand voor](#example-configuration-file) gebruiksvoorbeelden.

| Regel-eigenschap                       | Vereist | Standaardwaarde                        | Opmerking                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ----------------------------------- | -------- | ------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `route`                             | Ja      | n.v.t.                                  | Het routepatroon dat is aangevraagd door de aanroeper.<ul><li>[Jokertekens worden](#wildcards) ondersteund aan het einde van routepaden.<ul><li>De routebeheerder/ komt _bijvoorbeeld \* overeen_ met een route onder het _beheerderspad._</ul></ul>                                                                                                                                                                                                                                                                                                                                                                                                        |
| `rewrite`                           | Nee       | n.v.t.                                  | Hiermee definieert u het bestand of pad dat door de aanvraag wordt geretourneerd.<ul><li>Sluiten elkaar wederzijds uit tot een `redirect` regel<li>Bij het herschrijven van regels wordt de locatie van de browser niet gewijzigd.<li>Waarden moeten relatief zijn ten opzichte van de hoofdmap van de app</ul>                                                                                                                                                                                                                                                                                                                                                                                                      |
| `redirect`                          | Nee       | n.v.t.                                  | Hiermee definieert u het bestand of pad omleidingsdoel voor een aanvraag.<ul><li>Is wederzijds exclusief voor een `rewrite` regel.<li>Met omleidingsregels wordt de locatie van de browser gewijzigd.<li>De standaardreactiecode is [`302`](https://developer.mozilla.org/docs/Web/HTTP/Status/302) een (tijdelijke omleiding), maar u kunt overschrijven met een [`301`](https://developer.mozilla.org/docs/Web/HTTP/Status/301) (permanente omleiding).</ul>                                                                                                                                                                                                              |
| `allowedRoles`                      | Nee       | Anonieme                            | Hiermee definieert u een lijst met rolnamen die vereist zijn voor toegang tot een route. <ul><li>Geldige tekens zijn `a-z`, `A-Z`, `0-9` en `_`.<li>De ingebouwde rol, , is van toepassing op alle [`anonymous`](./authentication-authorization.md) niet-geautheticeerde gebruikers<li>De ingebouwde rol, [`authenticated`](./authentication-authorization.md) , is van toepassing op elke aangemelde gebruiker.<li>Gebruikers moeten deel uitmaken van ten minste één rol.<li>Rollen worden op _OR-basis gematcht._<ul><li>Als een gebruiker een van de vermelde rollen heeft, wordt toegang verleend.</ul><li>Afzonderlijke gebruikers worden aan rollen gekoppeld via [uitnodigingen.](authentication-authorization.md)</ul> |
| `headers`<a id="route-headers"></a> | Nee       | n.v.t.                                  | Set [HTTP-headers toegevoegd](https://developer.mozilla.org/docs/Web/HTTP/Headers) aan het antwoord. <ul><li>Routespecifieke headers overschrijven wanneer de routespecifieke header hetzelfde [`globalHeaders`](#global-headers) is als de globale header in het antwoord.<li>Als u een header wilt verwijderen, stelt u de waarde in op een lege tekenreeks.</ul>                                                                                                                                                                                                                                                                                          |
| `statusCode`                        | Nee       | `200`, `301` of `302` voor omleidingen | De [HTTP-statuscode](https://developer.mozilla.org/docs/Web/HTTP/Status) van het antwoord.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| `methods`                           | Nee       | Alle methoden                          | Lijst met aanvraagmethoden die overeenkomen met een route. Beschikbare methoden zijn: `GET` , , , , , , , , , `HEAD` en `POST` `PUT` `DELETE` `CONNECT` `OPTIONS` `TRACE` `PATCH` .                                                                                                                                                                                                                                                                                                                                                                                                                                                             |

Elke eigenschap heeft een specifiek doel in de aanvraag-/antwoordpijplijn.

| Doel                                        | Eigenschappen                                                                                   |
| ---------------------------------------------- | -------------------------------------------------------------------------------------------- |
| Routes matchen                                   | `route`, `methods`                                                                           |
| Autor nadat een route is gematcht             | `allowedRoles`                                                                               |
| Verwerken nadat een regel is gematcht en geautoriseerd | `rewrite` (wijzigt aanvraag) <br><br>`redirect`, `headers` , `statusCode` (wijzigt het antwoord) |

## <a name="securing-routes-with-roles"></a>Routes beveiligen met rollen

Routes worden beveiligd door een of meer rolnamen toe te voegen aan de matrix van een regel en gebruikers worden via uitnodigingen aan `allowedRoles` aangepaste [rollen gekoppeld.](./authentication-authorization.md) Zie het [voorbeeldconfiguratiebestand voor](#example-configuration-file) gebruiksvoorbeelden.

Standaard behoort elke gebruiker tot de ingebouwde rol en zijn alle aangemelde gebruikers `anonymous` lid van de `authenticated` rol.

Als u een route bijvoorbeeld wilt beperken tot alleen geverifieerde gebruikers, voegt u de ingebouwde rol `authenticated` toe aan de `allowedRoles` matrix.

```json
{
  "route": "/profile",
  "allowedRoles": ["authenticated"]
}
```

U kunt naar behoefte nieuwe rollen maken in de `allowedRoles` matrix. Als u een route wilt beperken tot alleen beheerders, kunt u uw eigen rol met de naam `administrator` definiëren in de `allowedRoles` matrix.

```json
{
  "route": "/admin",
  "allowedRoles": ["administrator"]
}
```

- U hebt volledige controle over rolnamen; er is geen lijst waaraan uw rollen moeten voldoen.
- Afzonderlijke gebruikers worden aan rollen gekoppeld via [uitnodigingen.](authentication-authorization.md)

## <a name="wildcards"></a>Jokertekens

Jokertekenregels komen overeen met alle aanvragen in een routepatroon, worden alleen ondersteund aan het einde van een pad en kunnen worden gefilterd op bestandsextensie. Zie het [voorbeeldconfiguratiebestand voor](#example-configuration-file) gebruiksvoorbeelden.

Als u bijvoorbeeld routes voor een agendatoepassing wilt implementeren, kunt u alle URL's die onder de kalenderroute _vallen,_ herschrijven om één bestand te kunnen gebruiken.

```json
{
  "route": "/calendar/*",
  "rewrite": "/calendar.html"
}
```

Het _calendar.html-bestand_ kan vervolgens routering aan de clientzijde gebruiken om een andere weergave te gebruiken voor URL-variaties zoals `/calendar/january/1` , en `/calendar/2020` `/calendar/overview` .

U kunt jokertekens filteren op bestandsextensie. Als u bijvoorbeeld een regel wilt toevoegen die alleen overeenkomt met HTML-bestanden in een bepaald pad, kunt u de volgende regel maken:

```json
{
  "route": "/articles/*.html",
  "headers": {
    "Cache-Control": "public, max-age=604800, immutable"
  }
}
```

Als u wilt filteren op meerdere bestandsextensies, moet u de opties tussen accolades opnemen, zoals wordt weergegeven in dit voorbeeld:

```json
{
  "route": "/images/thumbnails/*.{png,jpg,gif}",
  "headers": {
    "Cache-Control": "public, max-age=604800, immutable"
  }
}
```

Veelvoorkomende gebruiksgevallen voor routes met jokertekens zijn onder andere:

- Een specifiek bestand voor een volledig padpatroon bedienen
- Verschillende HTTP-methoden toewijzen aan een volledig padpatroon
- Verificatie- en autorisatieregels afdwingen
- Gespecialiseerde regels voor caching implementeren

## <a name="fallback-routes"></a>Terugvalroutes

Toepassingen met één pagina zijn vaak afhankelijk van routering aan de clientzijde. Met deze routeringsregels aan de clientzijde wordt de locatie van het browservenster bijgewerkt zonder dat er aanvragen naar de server worden gestuurd. Als u de pagina vernieuwt of rechtstreeks naar URL's navigeert die zijn gegenereerd door routeringsregels aan de clientzijde, is een terugvalroute aan de serverzijde vereist om de juiste HTML-pagina te kunnen gebruiken (doorgaans de _index.html_ voor uw app aan de clientzijde).

U kunt uw app configureren voor het gebruik van regels die een terugvalroute implementeren, zoals wordt weergegeven in het volgende voorbeeld, waarin een pad-jokerteken met bestandsfilter wordt gebruikt:

```json
{
  "navigationFallback": {
    "rewrite": "/index.html",
    "exclude": ["/images/*.{png,jpg,gif}", "/css/*"]
  }
}
```

De voorbeeldbestandsstructuur hieronder, de volgende resultaten zijn mogelijk met deze regel.

```files
├── images
│   ├── logo.png
│   ├── headshot.jpg
│   └── screenshot.gif
│
├── css
│   └── global.css
│
└── index.html
```

| Aanvragen voor...                                         | Retourneert...                                                                                                    | met de status... |
| ------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------- | ------------------ |
| _/about/_                                              | Het _bestand /index.html_                                                                                        | `200`              |
| _/images/logo.png_                                     | Het afbeeldingsbestand                                                                                                | `200`              |
| _/images/icon.svg_                                     | Het _bestand /index.html_ - omdat de _SVG-bestandsextensie_ niet wordt vermeld in het `/images/*.{png,jpg,gif}` filter | `200`              |
| _/images/unknown.png_                                  | Fout bestand niet gevonden                                                                                          | `404`              |
| _/css/unknown.css_                                     | Fout bestand niet gevonden                                                                                          | `404`              |
| _/css/global.css_                                      | Het stylesheet-bestand                                                                                           | `200`              |
| Elk ander bestand buiten de _mappen /images_ _of /css_ | Het _bestand /index.html_                                                                                        | `200`              |

## <a name="global-headers"></a>Globale headers

De sectie bevat een set HTTP-headers die worden toegepast op elk antwoord, tenzij deze worden overschrijven door een regel voor `globalHeaders` [een routeheader,](#route-headers) anders wordt de sameneening van zowel de headers van de route als de globale headers geretourneerd. [](https://developer.mozilla.org/docs/Web/HTTP/Headers)

Zie het [voorbeeldconfiguratiebestand voor](#example-configuration-file) gebruiksvoorbeelden.

Als u een header wilt verwijderen, stelt u de waarde in op een lege tekenreeks ( `""` ).

Enkele veelvoorkomende gebruiksgevallen voor globale headers zijn:

- Aangepaste regels voor opslaan in cache
- Beveiligingsbeleid afdwingen
- Instellingen voor codering

## <a name="response-overrides"></a>Antwoord-overschrijvingen

De `responseOverrides` sectie biedt de mogelijkheid om een aangepast antwoord te definiëren wanneer de server anders een foutcode retourneert. Zie het [voorbeeldconfiguratiebestand voor](#example-configuration-file) gebruiksvoorbeelden.

De volgende HTTP-codes zijn beschikbaar om te overschrijven:

| Statuscode                                                   | Betekenis      | Mogelijke oorzaak                                                                                                                                                                                                                                                                                     |
| ------------------------------------------------------------- | ------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [400](https://developer.mozilla.org/docs/Web/HTTP/Status/400) | Slechte aanvraag  | Ongeldige uitnodigingskoppeling                                                                                                                                                                                                                                                                            |
| [401](https://developer.mozilla.org/docs/Web/HTTP/Status/401) | Niet geautoriseerd | Aanvraag voor beperkte pagina's tijdens niet-geautheticeerde pagina's                                                                                                                                                                                                                                                  |
| [403](https://developer.mozilla.org/docs/Web/HTTP/Status/403) | Verboden    | <ul><li>De gebruiker is aangemeld, maar heeft niet de rollen die nodig zijn om de pagina weer te geven.<li>De gebruiker is aangemeld, maar de runtime kan de details van de gebruiker niet uit de identiteitsclaims halen.<li>Er zijn te veel gebruikers aangemeld bij de site met aangepaste rollen, waardoor de runtime de gebruiker niet kan aanmelden.</ul> |
| [404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) | Niet gevonden    | Bestand niet gevonden                                                                                                                                                                                                                                                                                     |

In de volgende voorbeeldconfiguratie wordt gedemonstreerd hoe u een foutcode overschrijven.

```json
{
  "responseOverrides": {
    "400": {
      "rewrite": "/invalid-invitation-error.html",
      "statusCode": 200
    },
    "401": {
      "statusCode": 302,
      "redirect": "/login"
    },
    "403": {
      "rewrite": "/custom-forbidden-page.html",
      "statusCode": 200
    },
    "404": {
      "rewrite": "/custom-404.html",
      "statusCode": 200
    }
  }
}
```

## <a name="example-configuration-file"></a>Voorbeeld van een configuratiebestand

```json
{
  "routes": [
    {
      "route": "/profile",
      "allowedRoles": ["authenticated"]
    },
    {
      "route": "/admin/*",
      "allowedRoles": ["administrator"]
    },
    {
      "route": "/images/*",
      "headers": {
        "cache-control": "must-revalidate, max-age=15770000"
      }
    },
    {
      "route": "/api/*",
      "methods": ["GET"],
      "allowedRoles": ["registeredusers"]
    },
    {
      "route": "/api/*",
      "methods": ["PUT", "POST", "PATCH", "DELETE"],
      "allowedRoles": ["administrator"]
    },
    {
      "route": "/api/*",
      "allowedRoles": ["authenticated"]
    },
    {
      "route": "/customers/contoso",
      "allowedRoles": ["administrator", "customers_contoso"]
    },
    {
      "route": "/login",
      "rewrite": "/.auth/login/github"
    },
    {
      "route": "/.auth/login/twitter",
      "statusCode": 404
    },
    {
      "route": "/logout",
      "redirect": "/.auth/logout"
    },
    {
      "route": "/calendar/*",
      "rewrite": "/calendar.html"
    },
    {
      "route": "/specials",
      "redirect": "/deals",
      "statusCode": 301
    }
  ],
  "navigationFallback": {
    "rewrite": "index.html",
    "exclude": ["/images/*.{png,jpg,gif}", "/css/*"]
  },
  "responseOverrides": {
    "400": {
      "rewrite": "/invalid-invitation-error.html"
    },
    "401": {
      "redirect": "/login",
      "statusCode": 302
    },
    "403": {
      "rewrite": "/custom-forbidden-page.html"
    },
    "404": {
      "rewrite": "/404.html"
    }
  },
  "globalHeaders": {
    "content-security-policy": "default-src https: 'unsafe-eval' 'unsafe-inline'; object-src 'none'"
  },
  "mimeTypes": {
    ".json": "text/json"
  }
}
```

Bekijk op basis van de bovenstaande configuratie de volgende scenario's.

| Aanvragen voor...                                                    | resulteert in...                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ----------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| _/profiel_                                                        | Geverifieerde gebruikers krijgen het _bestand /profile/index.html_ te zien. Niet-niet-geleide gebruikers worden omgeleid naar _/login._                                                                                                                                                                                                                                                                                                                              |
| _/admin/_                                                         | Geverifieerde gebruikers met de _beheerdersrol_ krijgen het _bestand /admin/index.html._ Geverifieerde gebruikers die niet de _beheerdersrol_ hebben, krijgen fout `403` <sup>1 te zien.</sup> Niet-niet-geleide gebruikers worden omgeleid naar _/login._                                                                                                                                                                                                          |
| _/logo.png_                                                       | De afbeelding wordt gebruikt met een aangepaste cacheregel waarbij de maximale leeftijd iets hoger is dan 182 dagen (15.770.000 seconden).                                                                                                                                                                                                                                                                                                                                   |
| _/api/admin_                                                      | `GET` aanvragen van geverifieerde gebruikers in de _rol registeredusers_ worden verzonden naar de API. Geverifieerde gebruikers die niet de _rol registeredusers hebben_ en niet-geverifieerde gebruikers krijgen een `401` foutmelding.<br/><br/>`POST`, `PUT` , en aanvragen van `PATCH` `DELETE` geverifieerde gebruikers in de _beheerdersrol_ worden verzonden naar de API. Geverifieerde gebruikers die niet de _beheerdersrol_ hebben en niet-geverifieerde gebruikers krijgen een `401` foutmelding te zien. |
| _/customers/contoso_                                              | Geverifieerde gebruikers die bij de _beheerder_ of _customers_contoso_ horen, krijgen het _bestand /customers/contoso/index.html._ Geverifieerde gebruikers die geen beheerder _of_ _customers_contoso_ hebben, krijgen fout `403` <sup>1 te zien.</sup> Niet-gebruikers worden omgeleid naar _/login._                                                                                                                            |
| _/login_                                                          | Niet-geverifieerde gebruikers worden op de proef gesteld om zich te verifiëren bij GitHub.                                                                                                                                                                                                                                                                                                                                                                             |
| _/.auth/login/twitter_                                            | Omdat autorisatie met Twitter is uitgeschakeld door de routeregel, wordt er een fout geretourneerd die terugvalt op het `404` _aanleveren van /index.html_ met een `200` statuscode.                                                                                                                                                                                                                                                                                     |
| _/logout_                                                         | Gebruikers worden afgemeld bij een verificatieprovider.                                                                                                                                                                                                                                                                                                                                                                                          |
| _/calendar/2021/01_                                               | De browser wordt aan het _/calendar.html-bestand_ weergegeven.                                                                                                                                                                                                                                                                                                                                                                                              |
| _/specials_                                                       | De browser wordt permanent omgeleid naar _/deals._                                                                                                                                                                                                                                                                                                                                                                                            |
| _/data.jsaan_                                                      | Het bestand dat wordt bediend met het `text/json` MIME-type.                                                                                                                                                                                                                                                                                                                                                                                               |
| _/about_, of een map die overeenkomt met routeringspatronen aan clientzijde | De _/index.html_ bestand wordt met een `200` statuscode.                                                                                                                                                                                                                                                                                                                                                                                    |
| Een niet-bestaand bestand in de _map /images/_                     | Een `404` fout.                                                                                                                                                                                                                                                                                                                                                                                                                                |

<sup>1</sup> U kunt een aangepaste foutpagina maken met behulp van een [regel voor het overschrijven van antwoorden.](#response-overrides)

## <a name="restrictions"></a>Beperkingen

De volgende beperkingen gelden voor destaticwebapps.config.js _in het_ bestand.

- Maximale bestandsgrootte is 100 KB
- Maximaal 50 verschillende rollen

Zie het [artikel Quota](quotas.md) voor algemene beperkingen en beperkingen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Verificatie en autorisatie instellen](authentication-authorization.md)
