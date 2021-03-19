---
title: Een web-app schrijven die gebruikers aanmeldt/uitschrijft | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het bouwen van een web-app die in/uit-gebruikers aanmeldt
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 10ddee404de21c5bc04672fdb6dd32c30f481ba3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578240"
---
# <a name="web-app-that-signs-in-users-sign-in-and-sign-out"></a>Web-app die gebruikers aanmeldt: aanmelden en afmelden

Meer informatie over het toevoegen van een aanmelding aan de code voor uw web-app die gebruikers aantekent. Lees vervolgens hoe ze zich kunnen afmelden.

## <a name="sign-in"></a>Aanmelden

Aanmelden bestaat uit twee delen:

- De knop aanmelden op de HTML-pagina
- De aanmeldings actie in de onderliggende code in de controller

### <a name="sign-in-button"></a>Knop Aanmelden

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

In ASP.NET Core voor micro soft-identiteits platform-toepassingen, de knop **Aanmelden** wordt weer gegeven in `Views\Shared\_LoginPartial.cshtml` (voor een MVC-app) of `Pages\Shared\_LoginPartial.cshtm` (voor een haar app). Deze wordt alleen weer gegeven als de gebruiker niet is geverifieerd. Dat wil zeggen dat het wordt weer gegeven wanneer de gebruiker zich nog niet heeft aangemeld of zich heeft afgemeld. Anders wordt de knop **Afmelden** weer gegeven wanneer de gebruiker al is aangemeld. Houd er rekening mee dat de account controller is gedefinieerd in het NuGet-pakket **micro soft. Identity. Web. UI** , in het gebied met de naam **MicrosoftIdentity**

```html
<ul class="navbar-nav">
  @if (User.Identity.IsAuthenticated)
  {
    <li class="nav-item">
        <span class="navbar-text text-dark">Hello @User.Identity.Name!</span>
    </li>
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignOut">Sign out</a>
    </li>
  }
  else
  {
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignIn">Sign in</a>
    </li>
  }
</ul>
```

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

In ASP.NET MVC wordt de afmeldings knop weer gegeven in `Views\Shared\_LoginPartial.cshtml` . Deze wordt alleen weer gegeven wanneer er een geverifieerd account is. Dat wil zeggen dat het wordt weer gegeven wanneer de gebruiker zich eerder heeft aangemeld.

```html
@if (Request.IsAuthenticated)
{
 // Code omitted code for clarity
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

# <a name="java"></a>[Java](#tab/java)

In onze Java Quick Start bevindt de aanmeldings knop zich in het bestand [main/resources/templates/index.html](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/msal-java-webapp-sample/src/main/resources/templates/index.html) .

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>HomePage</title>
</head>
<body>
<h3>Home Page</h3>

<form action="/msal4jsample/secure/aad">
    <input type="submit" value="Login">
</form>

</body>
</html>
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

In de Node.js Quick start is er geen knop aanmelden. De code-behind vraagt de gebruiker automatisch om zich aan te melden wanneer de hoofdmap van de web-app wordt bereikt.

```javascript
app.get('/', (req, res) => {
    // authentication logic
});
```

# <a name="python"></a>[Python](#tab/python)

In de Quick Start van python is er geen knop aanmelden. De code-behind vraagt de gebruiker automatisch om zich aan te melden wanneer de hoofdmap van de web-app wordt bereikt. Zie [app. py # l14-L18](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app.py#L14-L18).

```Python
@app.route("/")
def index():
    if not session.get("user"):
        return redirect(url_for("login"))
    return render_template('index.html', user=session["user"])
```

---

### <a name="signin-action-of-the-controller"></a>`SignIn` actie van de controller

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

In ASP.NET wordt de  `SignIn` actie op de controller geactiveerd wanneer u de knop aanmelden in de web-app selecteert `AccountController` . In eerdere versies van de ASP.NET core-sjablonen `Account` is de controller Inge sloten met de web-app. Dat is niet langer het geval omdat de controller nu deel uitmaakt van het **micro soft. Identity. Web. UI** NuGet-pakket. Zie [AccountController. cs](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Controllers/AccountController.cs) (Engelstalig) voor meer informatie.

Deze controller verwerkt ook de Azure AD B2C toepassingen.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

In ASP.NET wordt afmelden geactiveerd vanuit de `SignOut()` methode op een controller (bijvoorbeeld [AccountController. cs # L16-L23](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L16-L23)). Deze methode maakt geen deel uit van het ASP.NET-Framework (in tegens telling tot wat er gebeurt in ASP.NET Core). Er wordt een OpenID Connect-aanmeldings vraag verzonden nadat een omleidings-URI is voorgesteld.

```csharp
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

# <a name="java"></a>[Java](#tab/java)

In Java wordt afmelden verwerkt door rechtstreeks het micro soft Identity platform- `logout` eind punt aan te roepen en de waarde op te geven `post_logout_redirect_uri` . Zie [AuthPageController. java # l30-L48](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L30-L48)voor meer informatie.

```Java
@Controller
public class AuthPageController {

    @Autowired
    AuthHelper authHelper;

    @RequestMapping("/msal4jsample")
    public String homepage(){
        return "index";
    }

    @RequestMapping("/msal4jsample/secure/aad")
    public ModelAndView securePage(HttpServletRequest httpRequest) throws ParseException {
        ModelAndView mav = new ModelAndView("auth_page");

        setAccountInfo(mav, httpRequest);

        return mav;
    }

    // More code omitted for simplicity
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

In tegens telling tot andere platforms zorgt het MSAL-knoop punt ervoor dat de gebruiker zich aanmeldt vanaf de aanmeldings pagina.

```javascript

// 1st leg of auth code flow: acquire a code
app.get('/', (req, res) => {
    const authCodeUrlParameters = {
        scopes: ["user.read"],
        redirectUri: REDIRECT_URI,
    };

    // get url to sign user in and consent to scopes needed for application
    pca.getAuthCodeUrl(authCodeUrlParameters).then((response) => {
        res.redirect(response);
    }).catch((error) => console.log(JSON.stringify(error)));
});

// 2nd leg of auth code flow: exchange code for token
app.get('/redirect', (req, res) => {
    const tokenRequest = {
        code: req.query.code,
        scopes: ["user.read"],
        redirectUri: REDIRECT_URI,
    };

    pca.acquireTokenByCode(tokenRequest).then((response) => {
        console.log("\nResponse: \n:", response);
        res.sendStatus(200);
    }).catch((error) => {
        console.log(error);
        res.status(500).send(error);
    });
});
```

# <a name="python"></a>[Python](#tab/python)

In tegens telling tot andere platforms zorgt MSAL python ervoor dat de gebruiker zich aanmeldt vanaf de aanmeldings pagina. Zie [app. py # L20-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L20-L28).

```Python
@app.route("/login")
def login():
    session["state"] = str(uuid.uuid4())
    auth_url = _build_msal_app().get_authorization_request_url(
        app_config.SCOPE,  # Technically we can use an empty list [] to just sign in
                           # Here we choose to also collect user consent up front
        state=session["state"],
        redirect_uri=url_for("authorized", _external=True))
    return "<a href='%s'>Login with Microsoft Identity</a>" % auth_url
```

De `_build_msal_app()` methode wordt als volgt gedefinieerd in [app. py # L81-L88](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L81-L88) :

```Python
def _load_cache():
    cache = msal.SerializableTokenCache()
    if session.get("token_cache"):
        cache.deserialize(session["token_cache"])
    return cache

def _save_cache(cache):
    if cache.has_state_changed:
        session["token_cache"] = cache.serialize()

def _build_msal_app(cache=None):
    return msal.ConfidentialClientApplication(
        app_config.CLIENT_ID, authority=app_config.AUTHORITY,
        client_credential=app_config.CLIENT_SECRET, token_cache=cache)

def _get_token_from_cache(scope=None):
    cache = _load_cache()  # This web app maintains one cache per session
    cca = _build_msal_app(cache)
    accounts = cca.get_accounts()
    if accounts:  # So all accounts belong to the current signed-in user
        result = cca.acquire_token_silent(scope, account=accounts[0])
        _save_cache(cache)
        return result

```

---

Nadat de gebruiker zich heeft aangemeld bij uw app, moet u deze inschakelen om u af te melden.

## <a name="sign-out"></a>Afmelden

Afmelden bij een web-app omvat meer dan het verwijderen van de informatie over het aangemelde account uit de status van de web-app.
De web-app moet ook de gebruiker omleiden naar het micro soft Identity platform- `logout` eind punt om u af te melden.

Als uw web-app de gebruiker omleidt naar het `logout` eind punt, wordt met dit eind punt de sessie van de gebruiker uit de browser gewist. Als uw app niet naar het `logout` eind punt gaat, wordt de gebruiker opnieuw geverifieerd bij uw app zonder de referenties opnieuw in te voeren. De reden hiervoor is dat ze een geldige sessie voor eenmalige aanmelding hebben met het micro soft Identity-platform.

Zie de sectie [een aanvraag voor een afmelding verzenden](v2-protocols-oidc.md#send-a-sign-out-request) in het [micro soft Identity-platform en de OpenID Connect Connect protocol](v2-protocols-oidc.md) -documentatie voor meer informatie.

### <a name="application-registration"></a>Een toepassing registreren

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Tijdens de registratie van de toepassing registreert u een URL voor de afmelding voor front Channel. In onze zelf studie hebt u geregistreerd `https://localhost:44321/signout-oidc` in het veld **front-Channel afmeldings-URL** op de pagina **verificatie** . Zie [de webApp-app registreren](scenario-web-app-sign-user-app-registration.md#register-an-app-by-using-the-azure-portal)voor meer informatie.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Tijdens de registratie van de toepassing hoeft u geen extra URL voor het afmelden van het voor kanaal te registreren. De app wordt teruggebeld op de hoofd-URL. 

# <a name="java"></a>[Java](#tab/java)

Er is geen afmeldings-URL voor front-Channel vereist in de registratie van de toepassing.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Er is geen afmeldings-URL voor front-Channel vereist in de registratie van de toepassing.

# <a name="python"></a>[Python](#tab/python)

Tijdens de registratie van de toepassing hoeft u geen extra URL voor het afmelden van het voor kanaal te registreren. De app wordt teruggebeld op de hoofd-URL.

---

### <a name="sign-out-button"></a>Knop voor afmelden

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Als u in ASP.NET de knop **Afmelden** in de web-app selecteert, wordt de `SignOut` actie op de `AccountController` controller geactiveerd (zie hieronder).

```html
<ul class="navbar-nav">
  @if (User.Identity.IsAuthenticated)
  {
    <li class="nav-item">
        <span class="navbar-text text-dark">Hello @User.Identity.Name!</span>
    </li>
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignOut">Sign out</a>
    </li>
  }
  else
  {
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignIn">Sign in</a>
    </li>
  }
</ul>
```

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

In ASP.NET MVC wordt de afmeldings knop weer gegeven in `Views\Shared\_LoginPartial.cshtml` . Deze wordt alleen weer gegeven wanneer er een geverifieerd account is. Dat wil zeggen dat het wordt weer gegeven wanneer de gebruiker zich eerder heeft aangemeld.

```html
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">
                Hello, @User.Identity.Name!
            </li>
            <li>
                @Html.ActionLink("Sign out", "SignOut", "Account")
            </li>
        </ul>
    </text>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

# <a name="java"></a>[Java](#tab/java)

In de Java-Snelstartgids bevindt de afmeldings knop zich in het bestand main/resources/templates/auth_page.html.

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<body>

<form action="/msal4jsample/sign_out">
    <input type="submit" value="Sign out">
</form>
...
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Deze voorbeeld toepassing implementeert de afmelding niet.

# <a name="python"></a>[Python](#tab/python)

In de Quick Start van python bevindt de afmeldings knop zich in het bestand [Templates/index.html # L10](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/templates/index.html#L10) .

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
</head>
<body>
    <h1>Microsoft Identity Python web app</h1>
    Welcome {{ user.get("name") }}!
    <li><a href='/graphcall'>Call Microsoft Graph API</a></li>
    <li><a href="/logout">Logout</a></li>
</body>
</html>
```

---

### <a name="signout-action-of-the-controller"></a>`SignOut` actie van de controller

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

In eerdere versies van de ASP.NET core-sjablonen `Account` is de controller Inge sloten met de web-app. Dat is niet langer het geval omdat de controller nu deel uitmaakt van het **micro soft. Identity. Web. UI** NuGet-pakket. Zie [AccountController. cs](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Controllers/AccountController.cs) (Engelstalig) voor meer informatie.

- Hiermee stelt u een omleidings-URI voor OpenID Connect in `/Account/SignedOut` zodat de controller weer wordt aangeroepen wanneer Azure AD de afmelding heeft voltooid.
- Aanroepen `Signout()` , waarmee de OpenID Connect Connect-verbinding kan maken met het micro soft Identity platform- `logout` eind punt. Het eind punt vervolgens:

  - Hiermee wordt de sessie cookie uit de browser gewist.
  - Roept de omleidings-URI na afmelding terug. Standaard geeft de omleidings-URI na afmelding de afgemelde weergave pagina [SignedOut. cshtml. cs](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Pages/Account/SignedOut.cshtml.cs)weer. Deze pagina wordt ook meegeleverd als onderdeel van micro soft. Identity. Web.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

In ASP.NET wordt afmelden geactiveerd vanuit de `SignOut()` methode op een controller (bijvoorbeeld [AccountController. cs # L25-L31](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L25-L31)). Deze methode maakt geen deel uit van het ASP.NET-Framework, in tegens telling tot wat er gebeurt in ASP.NET Core. ,

- Hiermee wordt een OpenID Connect-afmeldings Challenge verzonden.
- Hiermee wordt de cache gewist.
- Hiermee wordt omgeleid naar de pagina die het wil.

```csharp
/// <summary>
/// Send an OpenID Connect sign-out request.
/// </summary>
public void SignOut()
{
 HttpContext.GetOwinContext()
            .Authentication
            .SignOut(CookieAuthenticationDefaults.AuthenticationType);
 Response.Redirect("/");
}
```

# <a name="java"></a>[Java](#tab/java)

In Java wordt afmelden verwerkt door rechtstreeks het micro soft Identity platform- `logout` eind punt aan te roepen en de waarde op te geven `post_logout_redirect_uri` . Zie [AuthPageController. java # L50-L60](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L50-L60)voor meer informatie.

```Java
@RequestMapping("/msal4jsample/sign_out")
    public void signOut(HttpServletRequest httpRequest, HttpServletResponse response) throws IOException {

        httpRequest.getSession().invalidate();

        String endSessionEndpoint = "https://login.microsoftonline.com/common/oauth2/v2.0/logout";

        String redirectUrl = "http://localhost:8080/msal4jsample/";
        response.sendRedirect(endSessionEndpoint + "?post_logout_redirect_uri=" +
                URLEncoder.encode(redirectUrl, "UTF-8"));
    }
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Deze voorbeeld toepassing implementeert de afmelding niet.

# <a name="python"></a>[Python](#tab/python)

De code waarmee de gebruiker zich afmeldt, bevindt zich in [app. py # L46-L52](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L47-L48).

```Python
@app.route("/logout")
def logout():
    session.clear()  # Wipe out the user and the token cache from the session
    return redirect(  # Also need to log out from the Microsoft Identity platform
        "https://login.microsoftonline.com/common/oauth2/v2.0/logout"
        "?post_logout_redirect_uri=" + url_for("index", _external=True))
```

---

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>De aanroep naar het `logout` eind punt wordt onderschept

Met de URI na afmelding kunnen toepassingen deel nemen aan de globale afmelding.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Met de ASP.NET Core OpenID Connect Connect middleware kan uw app de aanroep naar het micro soft Identity platform- `logout` eind punt onderscheppen door een OpenID Connect Connect-gebeurtenis op te geven met de naam `OnRedirectToIdentityProviderForSignOut` . Dit wordt automatisch afgehandeld door micro soft. Identity. Web (waardoor accounts worden gewist als uw web-app Web-api's aanroept)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

In ASP.NET delegeren aan de middleware voor het uitvoeren van de afmelding, het verwijderen van de sessie cookie:

```csharp
public class AccountController : Controller
{
 ...
 public void EndSession()
 {
  Request.GetOwinContext().Authentication.SignOut();
  Request.GetOwinContext().Authentication.SignOut(Microsoft.AspNet.Identity.DefaultAuthenticationTypes.ApplicationCookie);
  this.HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
 }
}
```

# <a name="java"></a>[Java](#tab/java)

De omleidings-URI van de post-afmelding wordt in de Java Quick Start alleen de pagina index.html weer gegeven.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Deze voorbeeld toepassing implementeert de afmelding niet.

# <a name="python"></a>[Python](#tab/python)

In de Quick Start van python geeft de omleidings-URI na afmelding alleen de index.html-pagina weer.

---

## <a name="protocol"></a>Protocol

Als u meer wilt weten over afmelden, leest u de protocol documentatie die beschikbaar is via [Open ID Connect](./v2-protocols-oidc.md).

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel in dit scenario en [Ga naar productie](scenario-web-app-sign-user-production.md).