---
title: Bereiken en de beveiligde web-API van de app-rollen controleren | Azure
titleSuffix: Microsoft identity platform
description: Controleer of de API alleen wordt aangeroepen door toepassingen namens gebruikers die de juiste bereiken hebben en door daemon-apps met de juiste toepassings rollen.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 6d737f107e7a9b1476fdf86ac0320931ea137671
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94442902"
---
# <a name="protected-web-api-verify-scopes-and-app-roles"></a>Beveiligde web-API: scopes en app-rollen controleren

In dit artikel wordt beschreven hoe u autorisatie kunt toevoegen aan uw web-API. Deze bescherming zorgt ervoor dat de API alleen wordt aangeroepen door:

- Toepassingen namens gebruikers die de juiste bereiken hebben.
- Daemon-apps met de juiste toepassings rollen.

> [!NOTE]
> De code fragmenten in dit artikel worden geëxtraheerd uit de volgende code voorbeelden op GitHub:
>
> - [Stapsgewijze zelf studie voor ASP.NET Core Web-API](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/master/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs)
> - [ASP.NET Web API-voor beeld](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/master/TodoListService/Controllers/TodoListController.cs)

Als u een ASP.NET of ASP.NET Core Web-API wilt beveiligen, moet u het `[Authorize]` kenmerk toevoegen aan een van de volgende items:

- De controller zelf als u wilt dat alle controller acties worden beveiligd
- De afzonderlijke controller actie voor uw API

```csharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

Deze beveiliging is echter niet voldoende. Het garandeert alleen dat ASP.NET en ASP.NET Core het token te valideren. Uw API moet controleren of het token dat wordt gebruikt om de API aan te roepen, is aangevraagd met de verwachte claims. Deze claims hebben met name betrekking op verificatie:

- De *bereiken* als de API namens een gebruiker wordt genoemd.
- De *app-rollen* als de API kan worden aangeroepen vanuit een daemon-app.

## <a name="verify-scopes-in-apis-called-on-behalf-of-users"></a>Scopes verifiëren in Api's die namens gebruikers worden aangeroepen

Als een client-app uw API namens een gebruiker aanroept, moet de API een Bearer-token aanvragen dat specifieke bereiken voor de API heeft. Zie voor meer informatie [code configuratie | Bearer-token](scenario-protected-web-api-app-configuration.md#bearer-token).

### <a name="net-core"></a>.NET Core

#### <a name="verify-the-scopes-on-each-controller-action"></a>De bereiken voor elke controller actie controleren

```csharp
[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
         HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);
        // Do the work and return the result.
        // ...
    }
...
}
```

De `VerifyUserHasAnyAcceptedScope` methode ziet er ongeveer als volgt uit:

- Controleer of er een claim is met de naam `http://schemas.microsoft.com/identity/claims/scope` of `scp` .
- Controleer of de claim een waarde heeft die het bereik bevat dat door de API wordt verwacht.


#### <a name="verify-the-scopes-more-globally"></a>De bereiken wereld wijd controleren

Het definiëren van gedetailleerde bereiken voor uw web-API en het controleren van de scopes in elke controller actie is de aanbevolen benadering. Het is echter ook mogelijk om de scopes op het niveau van de toepassing of een controller te verifiëren met behulp van ASP.NET Core. Zie [autorisatie op basis van claims](/aspnet/core/security/authorization/claims) in de ASP.net core-documentatie voor meer informatie.

### <a name="net-mvc"></a>.NET MVC

Voor ASP.NET vervangt u gewoon `HttpContext.User` door `ClaimsPrincipal.Current` en vervangt u het claim type `"http://schemas.microsoft.com/identity/claims/scope"` door `"scp"` . Zie ook het code fragment verderop in dit artikel.

## <a name="verify-app-roles-in-apis-called-by-daemon-apps"></a>App-rollen controleren in Api's die worden aangeroepen door daemon-apps

Als uw web-API wordt aangeroepen door een [daemon-app](scenario-daemon-overview.md), moet voor die app een toepassings machtiging voor uw web-API zijn vereist. Zoals u kunt zien in het weer geven van [toepassings machtigingen (app-rollen)](./scenario-protected-web-api-app-registration.md#exposing-application-permissions-app-roles), worden deze machtigingen beschikbaar gesteld door uw API. Een voor beeld is de rol van de `access_as_application` app.

U moet uw API nu controleren of het token dat wordt ontvangen de claim bevat `roles` en dat deze claim de verwachte waarde heeft. De verificatie code is vergelijkbaar met de code die de gedelegeerde machtigingen verifieert, behalve dat uw controller actie tests voor rollen heeft uitgevoerd in plaats van bereiken:

### <a name="aspnet-core"></a>ASP.NET Core

```csharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
        HttpContext.ValidateAppRole("access_as_application");
        ...
    }
```

De `ValidateAppRole` methode is gedefinieerd in micro soft. Identity. web in [RolesRequiredHttpContextExtensions. cs](https://github.com/AzureAD/microsoft-identity-web/blob/d2ad0f5f830391a34175d48621a2c56011a45082/src/Microsoft.Identity.Web/Resource/RolesRequiredHttpContextExtensions.cs#L28).

### <a name="aspnet-mvc"></a>ASP.NET MVC

```csharp
private void ValidateAppRole(string appRole)
{
    //
    // The `role` claim tells you what permissions the client application has in the service.
    // In this case, we look for a `role` value of `access_as_application`.
    //
    Claim roleClaim = ClaimsPrincipal.Current.FindFirst("roles");
    if (roleClaim == null || !roleClaim.Value.Split(' ').Contains(appRole))
    {
        throw new HttpResponseException(new HttpResponseMessage
        { StatusCode = HttpStatusCode.Unauthorized,
            ReasonPhrase = $"The 'roles' claim does not contain '{appRole}' or was not found"
        });
    }
}
}
```

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Alleen app-tokens accepteren als de Web-API alleen moet worden aangeroepen door daemon-apps

Gebruikers kunnen ook rollen claims gebruiken in gebruikers toewijzings patronen, zoals wordt weer gegeven in [procedure: app-rollen toevoegen in uw toepassing en ontvangen in het token](howto-add-app-roles-in-azure-ad-apps.md). Als de rollen kunnen worden toegewezen aan beide, kunnen gebruikers met behulp van rollen het aanmelden als gebruiker en gebruikers zich aanmelden als apps. U kunt het beste verschillende rollen voor gebruikers en apps declareren om deze Verwar ring te voor komen.

Als u wilt dat alleen daemon-apps uw web-API aanroepen, voegt u de voor waarde dat het token een token voor app-only is wanneer u de functie van de app valideert.

```csharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnlyToken = oid == sub;
```

Door de inverse voor waarde te controleren, kunnen alleen apps die zich aanmelden bij een gebruiker, uw API aanroepen.

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel in dit scenario en [Ga naar productie](scenario-protected-web-api-production.md).
