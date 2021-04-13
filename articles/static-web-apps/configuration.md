---
title: Statische Azure-Web Apps configureren
description: Meer informatie over het configureren van routes, het afdwingen van beveiligings regels en algemene instellingen voor statische Azure-Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: cshoe
ms.openlocfilehash: 3ecd38b725307c7a3d75787795130c5106de85a7
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312243"
---
# <a name="configure-azure-static-web-apps"></a>Statische Azure-Web Apps configureren

De configuratie voor de statische Web Apps van Azure wordt gedefinieerd in de _staticwebapp.config.jsvoor_ het bestand, waarmee de volgende instellingen worden beheerd:

- Routering
- Verificatie
- Autorisatie
- Terugval regels
- Overschrijvingen HTTP-antwoorden
- Algemene HTTP-header definities
- Aangepaste MIME-typen

## <a name="file-location"></a>Bestands locatie

De aanbevolen locatie voor de _staticwebapp.config.jsop_ is in de map die is ingesteld als de `app_location` in het [werk stroom bestand](./github-actions-workflow.md). Het bestand kan echter op elke locatie in de map van de bron code van de toepassing worden geplaatst.

Zie het [configuratie](#example-configuration-file) bestand voor het voor beeld voor meer informatie.

> [!IMPORTANT]
> De [ _staticwebapp.config.jsin_ het bestand](./routes.md) wordt genegeerd als er een _staticwebapp.config.jsop_ bestaat.

## <a name="routes"></a>Routes

Met routerings regels kunt u het patroon van Url's definiëren waarmee u toegang tot het web kunt krijgen voor uw toepassing. Routes worden gedefinieerd als een matrix met routerings regels. Zie het [voorbeeld configuratie bestand](#example-configuration-file) voor gebruiks voorbeelden.

- Regels worden gedefinieerd in de `routes` matrix, zelfs als u slechts één route hebt.
- Regels worden uitgevoerd in de volg orde zoals ze worden weer gegeven in de `routes` matrix.
- De regel evaluatie stopt bij de eerste overeenkomst: de routerings regels worden niet aan elkaar gekoppeld.
- U hebt volledige controle over de namen van aangepaste rollen.
  - Er zijn enkele ingebouwde rolnaams die zijn opgenomen in [`anonymous`](./authentication-authorization.md) en [`authenticated`](./authentication-authorization.md) .

De route ring heeft betrekking op een aanzienlijke overlap ping met verificatie (het identificeren van de gebruiker) en autorisatie (het toewijzen van mogelijkheden aan de gebruiker). Lees de hand leiding voor [verificatie en autorisatie](authentication-authorization.md) samen met dit artikel.

Het standaard bestand voor statische inhoud is het _index.html_ -bestand.

## <a name="defining-routes"></a>Routes definiëren

Elke regel bestaat uit een route patroon, samen met een of meer van de optionele regel eigenschappen. Route regels worden gedefinieerd in de `routes` matrix. Zie het [voorbeeld configuratie bestand](#example-configuration-file) voor gebruiks voorbeelden.

| Regel eigenschap                       | Vereist | Standaardwaarde                        | Opmerking                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ----------------------------------- | -------- | ------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `route`                             | Yes      | n.v.t.                                  | Het route patroon dat is aangevraagd door de aanroeper.<ul><li>[Joker tekens](#wildcards) worden aan het einde van route paden ondersteund.<ul><li>De routerings _beheerder/ \*_ komt bijvoorbeeld overeen met een wille keurige route onder het pad van de _beheerder_ .</ul></ul>                                                                                                                                                                                                                                                                                                                                                                                                        |
| `rewrite`                           | No       | n.v.t.                                  | Hiermee wordt het bestand of het pad gedefinieerd dat door de aanvraag wordt geretourneerd.<ul><li>Sluiten elkaar wederzijds uit voor een `redirect` regel<li>Herschrijf regels hebben geen invloed op de locatie van de browser.<li>De waarden moeten relatief zijn ten opzichte van de hoofdmap van de app</ul>                                                                                                                                                                                                                                                                                                                                                                                                      |
| `redirect`                          | No       | n.v.t.                                  | Hiermee wordt het bestand of pad voor het omleiden van paden voor een aanvraag gedefinieerd.<ul><li>Sluiten elkaar wederzijds uit voor een `rewrite` regel.<li>Omleidings regels wijzigen de locatie van de browser.<li>Standaard respons code is een [`302`](https://developer.mozilla.org/docs/Web/HTTP/Status/302) (tijdelijke omleiding), maar u kunt overschrijven met een [`301`](https://developer.mozilla.org/docs/Web/HTTP/Status/301) (permanente omleiding).</ul>                                                                                                                                                                                                              |
| `allowedRoles`                      | No       | toegang                            | Hiermee wordt een lijst met namen van rollen gedefinieerd die zijn vereist voor toegang tot een route. <ul><li>Geldige tekens zijn `a-z`, `A-Z`, `0-9` en `_`.<li>De ingebouwde rol, [`anonymous`](./authentication-authorization.md) , is van toepassing op alle niet-geverifieerde gebruikers<li>De ingebouwde rol, [`authenticated`](./authentication-authorization.md) , is van toepassing op elke aangemelde gebruiker.<li>Gebruikers moeten deel uitmaken van ten minste één rol.<li>Rollen worden _op basis van_ elkaar vergeleken.<ul><li>Als een gebruiker zich in een van de vermelde rollen bevindt, wordt de toegang verleend.</ul><li>Afzonderlijke gebruikers zijn gekoppeld aan rollen via [uitnodigingen](authentication-authorization.md).</ul> |
| `headers`<a id="route-headers"></a> | No       | n.v.t.                                  | De set [http-headers](https://developer.mozilla.org/docs/Web/HTTP/Headers) die aan het antwoord worden toegevoegd. <ul><li>Routenet teksten worden overschreven [`globalHeaders`](#global-headers) wanneer de route-specifieke header hetzelfde is als de algemene header in het antwoord.<li>Als u een koptekst wilt verwijderen, stelt u de waarde in op een lege teken reeks.</ul>                                                                                                                                                                                                                                                                                          |
| `statusCode`                        | No       | `200`, `301` , of `302` voor omleidingen | De [HTTP-status code](https://developer.mozilla.org/docs/Web/HTTP/Status) van het antwoord.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| `methods`                           | No       | Alle methoden                          | Lijst met aanvraag methoden die overeenkomen met een route. Beschik bare methoden zijn: `GET` ,, `HEAD` ,,,, `POST` `PUT` `DELETE` `CONNECT` `OPTIONS` , `TRACE` en `PATCH` .                                                                                                                                                                                                                                                                                                                                                                                                                                                             |

Elke eigenschap heeft een specifiek doel in de aanvraag/antwoord pijplijn.

| Doel                                        | Eigenschappen                                                                                   |
| ---------------------------------------------- | -------------------------------------------------------------------------------------------- |
| Overeenkomende routes                                   | `route`, `methods`                                                                           |
| Autoriseren nadat een route is gevonden             | `allowedRoles`                                                                               |
| Proces nadat een regel is vergeleken en geautoriseerd | `rewrite` (aanvraag wijzigen) <br><br>`redirect`, `headers` , `statusCode` (antwoord wijzigen) |

## <a name="securing-routes-with-roles"></a>Routes beveiligen met rollen

Routes worden beveiligd door een of meer rolnaams toe te voegen aan de matrix van een regel `allowedRoles` en gebruikers zijn gekoppeld aan aangepaste rollen via [uitnodigingen](./authentication-authorization.md). Zie het [voorbeeld configuratie bestand](#example-configuration-file) voor gebruiks voorbeelden.

Elke gebruiker maakt standaard deel uit van de ingebouwde `anonymous` rol en alle aangemelde gebruikers zijn lid van de `authenticated` rol.

Als u bijvoorbeeld een route wilt beperken naar alleen geverifieerde gebruikers, moet u de ingebouwde `authenticated` rol toevoegen aan de `allowedRoles` matrix.

```json
{
  "route": "/profile",
  "allowedRoles": ["authenticated"]
}
```

U kunt indien nodig nieuwe rollen maken in de `allowedRoles` matrix. Als u een route wilt beperken tot alleen beheerders, kunt u uw eigen rol definiëren `administrator` met de naam, in de `allowedRoles` matrix.

```json
{
  "route": "/admin",
  "allowedRoles": ["administrator"]
}
```

- U hebt volledige controle over de namen van rollen. Er is geen lijst waaraan uw rollen moeten voldoen.
- Afzonderlijke gebruikers zijn gekoppeld aan rollen via [uitnodigingen](authentication-authorization.md).

## <a name="wildcards"></a>Jokertekens

Joker regels komen overeen met alle aanvragen in een route patroon, worden alleen ondersteund aan het einde van een pad en kunnen worden gefilterd op bestands extensie. Zie het [voorbeeld configuratie bestand](#example-configuration-file) voor gebruiks voorbeelden.

Als u bijvoorbeeld routes voor een agenda toepassing wilt implementeren, kunt u alle Url's die onder de _agenda_ route vallen, opnieuw schrijven om één bestand te leveren.

```json
{
  "route": "/calendar/*",
  "rewrite": "/calendar.html"
}
```

Het bestand _calendar.html_ kan vervolgens client-side route ring gebruiken om een andere weer gave te leveren voor URL-variaties zoals `/calendar/january/1` , `/calendar/2020` , en `/calendar/overview` .

U kunt joker tekens filteren op bestands extensie. Als u bijvoorbeeld een regel wilt toevoegen die alleen overeenkomt met HTML-bestanden in een bepaald pad, kunt u de volgende regel maken:

```json
{
  "route": "/articles/*.html",
  "headers": {
    "Cache-Control": "public, max-age=604800, immutable"
  }
}
```

Als u wilt filteren op meerdere bestands extensies, neemt u de opties op in accolades, zoals wordt weer gegeven in dit voor beeld:

```json
{
  "route": "/images/thumbnails/*.{png,jpg,gif}",
  "headers": {
    "Cache-Control": "public, max-age=604800, immutable"
  }
}
```

Veelvoorkomende cases voor joker tekens zijn onder andere:

- Een specifiek bestand voor een volledig pad-patroon
- Verschillende HTTP-methoden toewijzen aan een volledig pad-patroon
- Verificatie-en autorisatie regels afdwingen
- Gespecialiseerde regels voor opslaan in cache implementeren

## <a name="fallback-routes"></a>Terugval routes

Toepassingen met één pagina zijn vaak afhankelijk van route ring aan client zijde. Deze routerings regels aan de client zijde voeren de venster locatie van de browser bij zonder aanvragen terug te sturen naar de server. Als u de pagina vernieuwt of rechtstreeks navigeert naar Url's die zijn gegenereerd door routerings regels aan de client zijde, is er een terugval route aan de server zijde vereist voor de juiste HTML-pagina (dit is doorgaans de _index.html_ voor uw app aan client zijde).

U kunt uw app configureren voor het gebruik van regels voor het implementeren van een terugval route, zoals wordt weer gegeven in het volgende voor beeld dat gebruikmaakt van een pad Joker teken met bestands filter:

```json
{
  "navigationFallback": {
    "rewrite": "/index.html",
    "exclude": ["/images/*.{png,jpg,gif}", "/css/*"]
  }
}
```

In de onderstaande voorbeeld bestands structuur zijn de volgende resultaten mogelijk met deze regel.

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

| Aanvragen naar...                                         | retourneert...                                                                                                    | met de status... |
| ------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------- | ------------------ |
| _wilt_                                              | Het bestand _/index.html_                                                                                        | `200`              |
| _/images/logo.png_                                     | Het afbeeldings bestand                                                                                                | `200`              |
| _/images/icon.svg_                                     | Het _/index.html_ -bestand-omdat de extensie van het _SVG_ -bestand niet wordt weer gegeven in het `/images/*.{png,jpg,gif}` filter | `200`              |
| _/images/unknown.png_                                  | Fout bestand niet gevonden                                                                                          | `404`              |
| _/css/unknown.css_                                     | Fout bestand niet gevonden                                                                                          | `404`              |
| _/css/global.css_                                      | Het opmaak model bestand                                                                                           | `200`              |
| Elk ander bestand buiten de _/images_ -of _/CSS_ -mappen | Het bestand _/index.html_                                                                                        | `200`              |

## <a name="global-headers"></a>Algemene kopteksten

De `globalHeaders` sectie bevat een set [http-headers](https://developer.mozilla.org/docs/Web/HTTP/Headers) die worden toegepast op elke reactie, tenzij deze wordt overschreven door een regel voor de [route-header](#route-headers) , anders wordt de samen voeging van beide kopteksten van de route en de globale headers geretourneerd.

Zie het [voorbeeld configuratie bestand](#example-configuration-file) voor gebruiks voorbeelden.

Als u een koptekst wilt verwijderen, stelt u de waarde in op een lege teken reeks ( `""` ).

Enkele veelvoorkomende use cases voor globale headers zijn:

- Aangepaste regels voor opslaan in cache
- Beveiligings beleid afdwingen
- Coderings instellingen

## <a name="response-overrides"></a>Overschrijvingen van antwoorden

De `responseOverrides` sectie biedt de mogelijkheid om een aangepast antwoord te definiëren wanneer de server anders een fout code retourneert. Zie het [voorbeeld configuratie bestand](#example-configuration-file) voor gebruiks voorbeelden.

De volgende HTTP-codes kunnen worden overschreven:

| Statuscode                                                   | Betekenis      | Mogelijke oorzaak                                                                                                                                                                                                                                                                                     |
| ------------------------------------------------------------- | ------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [400](https://developer.mozilla.org/docs/Web/HTTP/Status/400) | Ongeldige aanvraag  | Ongeldige uitnodiging                                                                                                                                                                                                                                                                            |
| [401](https://developer.mozilla.org/docs/Web/HTTP/Status/401) | Niet geautoriseerd | Aanvragen voor beperkte pagina's tijdens niet-geverifieerde                                                                                                                                                                                                                                                  |
| [403](https://developer.mozilla.org/docs/Web/HTTP/Status/403) | Verboden    | <ul><li>De gebruiker is aangemeld, maar beschikt niet over de vereiste rollen om de pagina weer te geven.<li>De gebruiker is aangemeld, maar de runtime kan de gebruikers gegevens niet ophalen uit hun identiteits claims.<li>Er zijn te veel gebruikers aangemeld bij de site met aangepaste rollen, waardoor de runtime de gebruiker niet kan aanmelden.</ul> |
| [404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) | Niet gevonden    | Kan het bestand niet vinden                                                                                                                                                                                                                                                                                     |

In de volgende voorbeeld configuratie ziet u hoe u een fout code kunt overschrijven.

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

## <a name="example-configuration-file"></a>Voorbeeld configuratie bestand

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

Bekijk de volgende scenario's op basis van de bovenstaande configuratie.

| Aanvragen naar...                                                    | resultaten in...                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ----------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| _/profile_                                                        | Geverifieerde gebruikers worden het _/profile/index.html_ -bestand geleverd. Niet-geverifieerde gebruikers worden omgeleid naar _/login_.                                                                                                                                                                                                                                                                                                                              |
| _/admin_                                                         | Geverifieerde gebruikers in _de beheerdersrol_ worden het _/admin/index.html_ -bestand geleverd. Geverifieerde gebruikers die niet voor komen in de _beheerdersrol_ , worden `403` fout <sup>1</sup>behandeld. Niet-geverifieerde gebruikers worden omgeleid naar _/login_.                                                                                                                                                                                                          |
| _/logo.png_                                                       | Fungeert als installatie kopie met een aangepaste cache regel waarbij de maximale leeftijd een beetje meer is dan 182 dagen (15.770.000 seconden).                                                                                                                                                                                                                                                                                                                                   |
| _/api/admin_                                                      | `GET` aanvragen van geverifieerde gebruikers in de _registeredusers_ -rol worden naar de API verzonden. Geverifieerde gebruikers die niet voor komen in de _registeredusers_ -rol en niet-geverifieerde gebruikers, krijgen een `401` fout.<br/><br/>`POST`, `PUT` , `PATCH` en `DELETE` aanvragen van geverifieerde gebruikers in de _beheerdersrol_ worden verzonden naar de API. Geverifieerde _gebruikers die niet_ voor komen in de beheerdersrol en niet-geverifieerde gebruikers, krijgen een `401` fout. |
| _/customers/contoso_                                              | Geverifieerde gebruikers die deel uitmaken van de _beheerder_ of _customers_contoso_ rollen, worden het _/Customers/contoso/index.html_ -bestand geleverd. Geverifieerde gebruikers die niet voor komen in de _beheerder_ of _customers_contoso_ rollen, worden op `403` fout <sup>1</sup>afgehandeld. Niet-geverifieerde gebruikers worden omgeleid naar _/login_.                                                                                                                            |
| _/login_                                                          | Niet-geverifieerde gebruikers worden gevraagd om te verifiëren met GitHub.                                                                                                                                                                                                                                                                                                                                                                             |
| _/.auth/login/twitter_                                            | Als autorisatie met Twitter is uitgeschakeld door de route regel, `404` wordt er een fout geretourneerd, die terugvalt op het leveren van _/index.html_ met een `200` status code.                                                                                                                                                                                                                                                                                     |
| _/logout_                                                         | Gebruikers worden afgemeld bij een verificatie provider.                                                                                                                                                                                                                                                                                                                                                                                          |
| _/calendar/2021/01_                                               | De browser wordt het bestand _/calendar.html_ geleverd.                                                                                                                                                                                                                                                                                                                                                                                              |
| _/specials_                                                       | De browser wordt permanent omgeleid naar _/deals_.                                                                                                                                                                                                                                                                                                                                                                                            |
| _/data.jsop_                                                      | Het bestand wordt geleverd met het `text/json` MIME-type.                                                                                                                                                                                                                                                                                                                                                                                               |
| _/about_ of een map die overeenkomt met routerings patronen aan de client zijde | Het bestand _/index.html_ wordt geleverd met een `200` status code.                                                                                                                                                                                                                                                                                                                                                                                    |
| Een niet-bestaand bestand in de map _/images/_                     | Een `404` fout.                                                                                                                                                                                                                                                                                                                                                                                                                                |

<sup>1</sup> u kunt een aangepaste fout pagina opgeven met behulp van een regel voor het overschrijven van een [antwoord](#response-overrides).

## <a name="restrictions"></a>Beperkingen

De volgende beperkingen bestaan voor de _staticwebapps.config.jsin_ het bestand.

- De maximale bestands grootte is 100 KB
- Maxi maal 50 afzonderlijke rollen

Zie het [artikel quota's](quotas.md) voor algemene beperkingen en beperkingen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Verificatie en autorisatie instellen](authentication-authorization.md)
