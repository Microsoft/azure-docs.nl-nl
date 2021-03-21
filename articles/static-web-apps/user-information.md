---
title: Toegang tot gebruikers gegevens in statische Web Apps van Azure
description: Meer informatie over het lezen van autorisatie provider-geretourneerde gebruikers gegevens.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.custom: devx-track-js
ms.openlocfilehash: d5a1d810c357aa83b8069023b00d76352da124df
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94844792"
---
# <a name="accessing-user-information-in-azure-static-web-apps-preview"></a>Toegang tot gebruikers gegevens in azure static Web Apps Preview

Azure static Web Apps biedt gebruikers informatie met betrekking tot authenticatie via een [eind punt voor directe toegang](#direct-access-endpoint) en met [API-functies](#api-functions).

Veel gebruikers interfaces zijn sterk afhankelijk van gebruikers verificatie gegevens. Het eind punt voor directe toegang is een API voor het hulp programma waarmee gebruikers gegevens worden weer gegeven zonder dat er een aangepaste functie hoeft te worden geïmplementeerd. Het eind punt voor directe toegang valt buiten het gemak niet onder koude start vertragingen die zijn gekoppeld aan serverloze architectuur.

## <a name="client-principal-data"></a>Client-Principal-gegevens

Met client Principal Data-object worden door de gebruiker geïdentificeerde informatie beschikbaar gesteld aan uw app. De volgende eigenschappen worden aanbevolen in het Principal-object van de client:

| Eigenschap  | Beschrijving |
|-----------|---------|
| `identityProvider` | De naam van de [ID-provider](authentication-authorization.md). |
| `userId` | Een statische Web Apps-specifieke Azure-id voor de gebruiker. <ul><li>De waarde is uniek per app. Bijvoorbeeld, dezelfde gebruiker retourneert een andere `userId` waarde op een andere statische web apps resource.<li>De waarde blijft behouden voor de levens duur van een gebruiker. Als u dezelfde gebruiker weer verwijdert en aan de app toevoegt, wordt er een nieuwe `userId` gegenereerd.</ul>|
| `userDetails` | De gebruikers naam of het e-mail adres van de gebruiker. Sommige providers retour neren het [e-mail adres van de gebruiker](authentication-authorization.md), terwijl anderen de [gebruikers ingang](authentication-authorization.md)verzenden. |
| `userRoles`     | Een matrix met de [toegewezen rollen](authentication-authorization.md)van de gebruiker. |

Het volgende voor beeld is een principal-voorbeeld object van de client:

```json
{
  "identityProvider": "facebook",
  "userId": "d75b260a64504067bfc5b2905e3b8182",
  "userDetails": "user@example.com",
  "userRoles": [ "anonymous", "authenticated" ]
}
```

## <a name="direct-access-endpoint"></a>Eind punt voor directe toegang

U kunt een `GET` aanvraag verzenden naar de `/.auth/me` route en direct toegang tot de client-Principal-gegevens ontvangen. Wanneer de status van uw weer gave afhankelijk is van de autorisatie gegevens, gebruikt u deze methode voor de beste prestaties.

Voor aangemelde gebruikers bevat het antwoord een JSON-object van de client. Aanvragen van niet-geverifieerde gebruikers retour neren `null` .

Met de API voor [ophalen](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch)<sup>1</sup> krijgt u toegang tot de client-principalgegevens met de volgende syntaxis.

```javascript
async function getUserInfo() {
  const response = await fetch("/.auth/me");
  const payload = await response.json();
  const { clientPrincipal } = payload;
  return clientPrincipal;
}

console.log(getUserInfo());
```

## <a name="api-functions"></a>API-functies

De API-functies die beschikbaar zijn in statische Web Apps via de Azure Functions back-end hebben toegang tot dezelfde gebruikers gegevens als een client toepassing. Hoewel de API gebruikers herken bare informatie ontvangt, worden er geen eigen controles uitgevoerd als de gebruiker is geverifieerd of als deze overeenkomen met een vereiste rol. Toegangs beheer regels worden gedefinieerd in het [`routes.json`](routes.md) bestand.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Client-principalgegevens worden door gegeven aan API-functies in de `x-ms-client-principal` aanvraag header. De client-principalgegevens worden verzonden als een [Base64](https://www.wikipedia.org/wiki/Base64)-gecodeerde teken reeks met een geserialiseerd JSON-object.

In de volgende voorbeeld functie ziet u hoe u gebruikers gegevens kunt lezen en retour neren.

```javascript
module.exports = async function (context, req) {
  const header = req.headers["x-ms-client-principal"];
  const encoded = Buffer.from(header, "base64");
  const decoded = encoded.toString("ascii");

  context.res = {
    body: {
      clientPrincipal: JSON.parse(decoded)
    }
  };
};
```

Ervan uitgaande dat de bovenstaande functie een naam heeft `user` , kunt u de browser-API [Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch)<sup>1</sup> gebruiken om toegang te krijgen tot de reactie van de API met behulp van de volgende syntaxis.

```javascript
async function getUser() {
  const response = await fetch("/api/user");
  const payload = await response.json();
  const { clientPrincipal } = payload;
  return clientPrincipal;
}

console.log(await getUser());
```

# <a name="c"></a>[C#](#tab/csharp)

In een C#-functie zijn de gebruikers gegevens beschikbaar vanuit de- `x-ms-client-principal` header die kan worden gedeserialiseerd in een `ClaimsPrincipal` object of uw eigen aangepaste type. De volgende code laat zien hoe u de header uitpakt in een tussenliggend type, `ClientPrincipal` dat vervolgens wordt omgezet in een `ClaimsPrincipal` exemplaar.

```csharp
  public static class StaticWebAppsAuth
  {
    private class ClientPrincipal
    {
        public string IdentityProvider { get; set; }
        public string UserId { get; set; }
        public string UserDetails { get; set; }
        public IEnumerable<string> UserRoles { get; set; }
    }

    public static ClaimsPrincipal Parse(HttpRequest req)
    {
        var principal = new ClientPrincipal();

        if (req.Headers.TryGetValue("x-ms-client-principal", out var header))
        {
            var data = header[0];
            var decoded = Convert.FromBase64String(data);
            var json = Encoding.ASCII.GetString(decoded);
            principal = JsonSerializer.Deserialize<ClientPrincipal>(json, new JsonSerializerOptions { PropertyNameCaseInsensitive = true });
        }

        principal.UserRoles = principal.UserRoles?.Except(new string[] { "anonymous" }, StringComparer.CurrentCultureIgnoreCase);

        if (!principal.UserRoles?.Any() ?? true)
        {
            return new ClaimsPrincipal();
        }

        var identity = new ClaimsIdentity(principal.IdentityProvider);
        identity.AddClaim(new Claim(ClaimTypes.NameIdentifier, principal.UserId));
        identity.AddClaim(new Claim(ClaimTypes.Name, principal.UserDetails));
        identity.AddClaims(principal.UserRoles.Select(r => new Claim(ClaimTypes.Role, r)));

        return new ClaimsPrincipal(identity);
    }
  }
```

---

<sup>1</sup> de [ophaal](https://caniuse.com/#feat=fetch) -API en de [wachtende](https://caniuse.com/#feat=mdn-javascript_operators_await) operator worden niet ondersteund in Internet Explorer.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [App-instellingen configureren](application-settings.md)
