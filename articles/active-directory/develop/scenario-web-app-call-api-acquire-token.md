---
title: Een Token ophalen in een web-app die web-Api's aanroept | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het verkrijgen van een token voor een web-app die web-Api's aanroept
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/25/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: cfd479382cb69e7355b033312e165699223fdbf0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98756307"
---
# <a name="a-web-app-that-calls-web-apis-acquire-a-token-for-the-app"></a>Een web-app die web-Api's aanroept: een Token ophalen voor de app

U hebt uw client toepassings object gemaakt. Nu gebruikt u het om een token te verkrijgen om een web-API aan te roepen. In ASP.NET of ASP.NET Core wordt het aanroepen van een web-API uitgevoerd in de controller:

- Een Token ophalen voor de Web-API met behulp van de token cache. Als u dit token wilt ophalen, roept u de methode MSAL aan `AcquireTokenSilent` (of het equivalent in micro soft. Identity. web).
- Roep de beveiligde API aan, waarbij het toegangs token wordt door gegeven als para meter.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

*Micro soft. Identity. Web* voegt extensie methoden toe die de mogelijkheid bieden om Microsoft Graph of een stroomafwaartse Web-API aan te roepen. Deze methoden worden gedetailleerd beschreven in [een web-app die web-api's aanroept: een API aanroepen](scenario-web-app-call-api-call-api.md). Met deze helper-methoden hoeft u geen token hand matig te verkrijgen.

Als u echter hand matig een token moet verkrijgen, ziet u in de volgende code een voor beeld van het gebruik van *micro soft. Identity. Web* om dit te doen in een start controller. Het roept Microsoft Graph met behulp van de REST API (in plaats van de Microsoft Graph SDK). Als u een token wilt ophalen voor het aanroepen van de stroomafwaartse API, kunt u de service injecteren `ITokenAcquisition` door afhankelijkheids injecties in de constructor van uw controller (of uw pagina-constructor als u een razendsnelle functie gebruikt) en u deze gebruiken in de controller acties, waarbij een token wordt opgehaald voor de gebruiker ( `GetAccessTokenForUserAsync` ) of voor de toepassing zelf ( `GetAccessTokenForAppAsync` ) in een daemon-scenario.

De controller methoden worden beveiligd door een `[Authorize]` kenmerk dat ervoor zorgt dat alleen geverifieerde gebruikers de web-app kunnen gebruiken.

```csharp
[Authorize]
public class HomeController : Controller
{
 readonly ITokenAcquisition tokenAcquisition;

 public HomeController(ITokenAcquisition tokenAcquisition)
 {
  this.tokenAcquisition = tokenAcquisition;
 }

 // Code for the controller actions (see code below)

}
```

De `ITokenAcquisition` service wordt geïnjecteerd door ASP.net met behulp van afhankelijkheids injectie.

Hier volgt een vereenvoudigde code voor de actie van `HomeController` , waarmee een token wordt opgehaald om Microsoft Graph aan te roepen:

```csharp
[AuthorizeForScopes(Scopes = new[] { "user.read" })]
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenForUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
 string json = await client.GetStringAsync(url);
}
```

Zie de stap 2 ([2-1-Web-app-aanroepen Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)) van de zelf studie [MS-Identity-aspnetcore-zelf studie](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) voor meer informatie over de code die is vereist voor dit scenario.

Het `AuthorizeForScopes` kenmerk boven op de controller actie (of op de pagina als u een Scheer sjabloon gebruikt) wordt geboden door micro soft. Identity. Web. Hiermee wordt gegarandeerd dat de gebruiker om toestemming wordt gevraagd, indien nodig en incrementeel.

Er zijn andere complexe variaties, zoals:

- Meerdere Api's aanroepen.
- Incrementele toestemming en voorwaardelijke toegang verwerken.

Deze geavanceerde stappen zijn opgenomen in hoofd stuk 3 van de zelf studie [3-webapp-multi-api's](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/3-WebApp-multi-APIs) .

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

De code voor ASP.NET is vergelijkbaar met de code die wordt weer gegeven voor ASP.NET Core:

- Een controller actie, beveiligd door het kenmerk [autoriseren], extraheert de Tenant-ID en de gebruikers-ID van het `ClaimsPrincipal` lid van de controller. (ASP.NET gebruikt `HttpContext.User` .)
- Van daaruit bouwt het een MSAL.NET- `IConfidentialClientApplication` object.
- Ten slotte wordt de `AcquireTokenSilent` methode van de vertrouwelijke client toepassing aangeroepen.
- Als interactie vereist is, moet de Web-App de gebruiker vragen (Meld u opnieuw aan) en vraagt u om meer claims.

Het volgende code fragment is geëxtraheerd uit [HomeController. cs # L157-L192](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/257c8f96ec3ff875c351d1377b36403eed942a18/WebApp/Controllers/HomeController.cs#L157-L192) in het voor beeld [MS-Identity-ASPNET-webapp-openidconnect](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect) ASP.NET MVC-code:

```C#
public async Task<ActionResult> ReadMail()
{
    IConfidentialClientApplication app = MsalAppBuilder.BuildConfidentialClientApplication();
    AuthenticationResult result = null;
    var account = await app.GetAccountAsync(ClaimsPrincipal.Current.GetMsalAccountId());
    string[] scopes = { "Mail.Read" };

    try
    {
        // try to get token silently
        result = await app.AcquireTokenSilent(scopes, account).ExecuteAsync().ConfigureAwait(false);
    }
    catch (MsalUiRequiredException)
    {
        ViewBag.Relogin = "true";
        return View();
    }

    // More code here
    return View();
}
```

Zie de code voor [BuildConfidentialClientApplication ()](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Utils/MsalAppBuilder.cs) en [GetMsalAccountId](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/257c8f96ec3ff875c351d1377b36403eed942a18/WebApp/Utils/ClaimPrincipalExtension.cs#L38) in het code-voor beeld voor meer informatie.


# <a name="java"></a>[Java](#tab/java)

In het Java-voor beeld bevindt de code waarmee een API wordt aangeroepen zich in de methode getUsersFromGraph in [AuthPageController. java # L62](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L62).

De methode probeert aan te roepen `getAuthResultBySilentFlow` . Als de gebruiker moet toestemming geven voor meer bereiken, verwerkt de code het `MsalInteractionRequiredException` object om de gebruiker te vragen.

```java
@RequestMapping("/msal4jsample/graph/me")
public ModelAndView getUserFromGraph(HttpServletRequest httpRequest, HttpServletResponse response)
        throws Throwable {

    IAuthenticationResult result;
    ModelAndView mav;
    try {
        result = authHelper.getAuthResultBySilentFlow(httpRequest, response);
    } catch (ExecutionException e) {
        if (e.getCause() instanceof MsalInteractionRequiredException) {

            // If the silent call returns MsalInteractionRequired, redirect to authorization endpoint
            // so user can consent to new scopes.
            String state = UUID.randomUUID().toString();
            String nonce = UUID.randomUUID().toString();

            SessionManagementHelper.storeStateAndNonceInSession(httpRequest.getSession(), state, nonce);

            String authorizationCodeUrl = authHelper.getAuthorizationCodeUrl(
                    httpRequest.getParameter("claims"),
                    "User.Read",
                    authHelper.getRedirectUriGraph(),
                    state,
                    nonce);

            return new ModelAndView("redirect:" + authorizationCodeUrl);
        } else {

            mav = new ModelAndView("error");
            mav.addObject("error", e);
            return mav;
        }
    }

    if (result == null) {
        mav = new ModelAndView("error");
        mav.addObject("error", new Exception("AuthenticationResult not found in session."));
    } else {
        mav = new ModelAndView("auth_page");
        setAccountInfo(mav, httpRequest);

        try {
            mav.addObject("userInfo", getUserInfoFromGraph(result.accessToken()));

            return mav;
        } catch (Exception e) {
            mav = new ModelAndView("error");
            mav.addObject("error", e);
        }
    }
    return mav;
}
// Code omitted here
```

# <a name="python"></a>[Python](#tab/python)

In het python-voor beeld bevindt de code die Microsoft Graph aanroept, zich in [app. py # L53-L62](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L53-L62).

De code probeert een token op te halen uit de token cache. Nadat de autorisatie-header is ingesteld, wordt de Web-API aangeroepen. Als er geen token kan worden opgehaald, wordt de gebruiker opnieuw in het bericht ondertekend.

```python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service.
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

---

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel in dit scenario en [roep een web-API](scenario-web-app-call-api-call-api.md)aan.
