---
title: Sessie beheer-Microsoft Threat Modeling Tool-Azure | Microsoft Docs
description: Meer informatie over het oplossen van problemen met sessie beheer voor bedreigingen die worden weer gegeven in de Threat Modeling Tool. Zie informatie over risico beperking en voor beelden van code weer geven.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.custom: has-adal-ref, devx-track-js, devx-track-csharp
ms.openlocfilehash: a1f4d4a3bb78da82753d651e1a73cf244096d5df
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94518098"
---
# <a name="security-frame-session-management"></a>Beveiligings kader: sessie beheer
| Product/service | Artikel |
| --------------- | ------- |
| **Azure AD**    | <ul><li>[Goed afmelden met behulp van ADAL-methoden implementeren bij gebruik van Azure AD](#logout-adal)</li></ul> |
| **IoT-apparaat** | <ul><li>[Een eindige levens duur gebruiken voor gegenereerde SaS-tokens](#finite-tokens)</li></ul> |
| **Azure document DB** | <ul><li>[Minimale token levensduur gebruiken voor gegenereerde bron tokens](#resource-tokens)</li></ul> |
| **ADFS** | <ul><li>[Goed afmelden met behulp van WsFederation-methoden implementeren bij gebruik van ADFS](#wsfederation-logout)</li></ul> |
| **Identiteits server** | <ul><li>[Juiste afmelding implementeren bij gebruik van identiteits server](#proper-logout)</li></ul> |
| **Webtoepassing** | <ul><li>[Toepassingen die beschikbaar zijn via HTTPS, moeten beveiligde cookies gebruiken](#https-secure-cookies)</li><li>[Voor alle op http gebaseerde toepassingen moet alleen http worden opgegeven voor de cookie definitie](#cookie-definition)</li><li>[Problemen oplossen met CSRF-aanvallen (cross-site request vervalsing) op ASP.NET webpagina's](#csrf-asp)</li><li>[Sessie instellen voor een levens duur van inactiviteit](#inactivity-lifetime)</li><li>[De juiste afmelding van de toepassing implementeren](#proper-app-logout)</li></ul> |
| **Web-API** | <ul><li>[Problemen oplossen met CSRF-aanvallen (cross-site request vervalsing) op ASP.NET-Web-Api's](#csrf-api)</li></ul> |

## <a name="implement-proper-logout-using-adal-methods-when-using-azure-ad"></a><a id="logout-adal"></a>Goed afmelden met behulp van ADAL-methoden implementeren bij gebruik van Azure AD

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Azure AD | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | N.v.t.  |
| **Stappen** | Als de toepassing afhankelijk is van het toegangs token dat is uitgegeven door Azure AD, moet de afmeldings gebeurtenis-handler worden aangeroepen |

### <a name="example"></a>Voorbeeld
```csharp
HttpContext.GetOwinContext().Authentication.SignOut(OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType)
```

### <a name="example"></a>Voorbeeld
Ook moet de gebruikers sessie worden vernietigd door de methode Session. Abandon () aan te roepen. De volgende methode toont de veilige implementatie van gebruikers afmelding:
```csharp
    [HttpPost]
        [ValidateAntiForgeryToken]
        public void LogOff()
        {
            string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
            AuthenticationContext authContext = new AuthenticationContext(Authority + TenantId, new NaiveSessionCache(userObjectID));
            authContext.TokenCache.Clear();
            Session.Clear();
            Session.Abandon();
            Response.SetCookie(new HttpCookie("ASP.NET_SessionId", string.Empty));
            HttpContext.GetOwinContext().Authentication.SignOut(
                OpenIdConnectAuthenticationDefaults.AuthenticationType,
                CookieAuthenticationDefaults.AuthenticationType);
        } 
```

## <a name="use-finite-lifetimes-for-generated-sas-tokens"></a><a id="finite-tokens"></a>Een eindige levens duur gebruiken voor gegenereerde SaS-tokens

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | IoT-apparaat | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | N.v.t.  |
| **Stappen** | SaS-tokens die zijn gegenereerd voor authenticatie naar Azure IoT Hub moeten een eindige verval periode hebben. Behoud de levens duur van het SaS-token tot een minimum om de hoeveelheid tijd te beperken die ze kunnen herhalen voor het geval de tokens worden aangetast.|

## <a name="use-minimum-token-lifetimes-for-generated-resource-tokens"></a><a id="resource-tokens"></a>Minimale token levensduur gebruiken voor gegenereerde bron tokens

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Azure document DB | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | N.v.t.  |
| **Stappen** | Verminder de tijds duur van het bron token naar een vereiste minimum waarde. Bron tokens hebben een standaard geldige time span van 1 uur.|

## <a name="implement-proper-logout-using-wsfederation-methods-when-using-adfs"></a><a id="wsfederation-logout"></a>Goed afmelden met behulp van WsFederation-methoden implementeren bij gebruik van ADFS

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | ADFS | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | N.v.t.  |
| **Stappen** | Als de toepassing gebruikmaakt van een STS-token dat is uitgegeven door ADFS, moet de afmeldings gebeurtenis-handler de methode WSFederationAuthenticationModule. FederatedSignOut () aanroepen om de gebruiker af te melden. Ook moet de huidige sessie worden vernietigd en moet de waarde van het sessie token opnieuw worden ingesteld en nullified.|

### <a name="example"></a>Voorbeeld
```csharp
        [HttpPost, ValidateAntiForgeryToken]
        [Authorization]
        public ActionResult SignOut(string redirectUrl)
        {
            if (!this.User.Identity.IsAuthenticated)
            {
                return this.View("LogOff", null);
            }

            // Removes the user profile.
            this.Session.Clear();
            this.Session.Abandon();
            HttpContext.Current.Response.Cookies.Add(new System.Web.HttpCookie("ASP.NET_SessionId", string.Empty)
                {
                    Expires = DateTime.Now.AddDays(-1D),
                    Secure = true,
                    HttpOnly = true
                });

            // Signs out at the specified security token service (STS) by using the WS-Federation protocol.
            Uri signOutUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Issuer);
            Uri replyUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Realm);
            if (!string.IsNullOrEmpty(redirectUrl))
            {
                replyUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Realm + redirectUrl);
            }
           //     Signs out of the current session and raises the appropriate events.
            var authModule = FederatedAuthentication.WSFederationAuthenticationModule;
            authModule.SignOut(false);
        //     Signs out at the specified security token service (STS) by using the WS-Federation
        //     protocol.            
            WSFederationAuthenticationModule.FederatedSignOut(signOutUrl, replyUrl);
            return new RedirectResult(redirectUrl);
        }
```

## <a name="implement-proper-logout-when-using-identity-server"></a><a id="proper-logout"></a>Juiste afmelding implementeren bij gebruik van identiteits server

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Identiteits server | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [IdentityServer3-federatieve afmelden](https://identityserver.github.io/Documentation/docsv2/advanced/federated-signout.html) |
| **Stappen** | IdentityServer biedt ondersteuning voor de mogelijkheid om met externe ID-providers te communiceren. Wanneer een gebruiker zich afmeldt bij een upstream-ID-provider, is het mogelijk dat er een melding wordt weer gegeven wanneer de gebruiker zich afmeldt, afhankelijk van het gebruikte protocol. Hiermee kunnen IdentityServer de clients hiervan op de hoogte stellen, zodat ze de gebruiker ook kunnen afmelden. Raadpleeg de documentatie in het gedeelte met verwijzingen voor de implementatie details.|

## <a name="applications-available-over-https-must-use-secure-cookies"></a><a id="https-secure-cookies"></a>Toepassingen die beschikbaar zijn via HTTPS, moeten beveiligde cookies gebruiken

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | EnvironmentType-premises |
| **Referenties**              | [httpCookies-element (ASP.net-instellingen schema)](/previous-versions/dotnet/netframework-4.0/ms228262(v=vs.100)), [HttpCookie. Secure, eigenschap](/dotnet/api/system.web.httpcookie.secure) |
| **Stappen** | Cookies zijn normaal gesp roken alleen toegankelijk voor het domein waarvoor ze zijn bereik. De definitie van ' Domain ' bevat helaas niet het Protocol, dus cookies die via HTTPS worden gemaakt, zijn toegankelijk via HTTP. Het kenmerk ' Secure ' geeft aan de browser aan dat de cookie alleen via HTTPS beschikbaar moet worden gemaakt. Zorg ervoor dat alle cookies die via HTTPS zijn ingesteld, gebruikmaken van het kenmerk **Secure** . De vereiste kan worden afgedwongen in het web.config bestand door het kenmerk requireSSL in te stellen op True. Het is de voorkeurs benadering omdat hiermee het **beveiligde** kenmerk wordt afgedwongen voor alle huidige en toekomstige cookies zonder dat er extra code wijzigingen hoeven te worden aangebracht.|

### <a name="example"></a>Voorbeeld
```csharp
<configuration>
  <system.web>
    <httpCookies requireSSL="true"/>
  </system.web>
</configuration>
```
De instelling wordt afgedwongen, zelfs als HTTP wordt gebruikt voor toegang tot de toepassing. Als HTTP wordt gebruikt voor toegang tot de toepassing, wordt de toepassing door de instelling verbroken, omdat de cookies zijn ingesteld met het kenmerk Secure en de browser deze niet terugstuurt naar de toepassing.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Webformulieren, MVC5 |
| **Kenmerken**              | EnvironmentType-premises |
| **Referenties**              | N.v.t.  |
| **Stappen** | Wanneer de webtoepassing de Relying Party is en de IdP de ADFS-server is, kan het beveiligde kenmerk van het FedAuth-token worden geconfigureerd door requireSSL in te stellen op True in de `system.identityModel.services` sectie van web.config:|

### <a name="example"></a>Voorbeeld
```csharp
  <system.identityModel.services>
    <federationConfiguration>
      <!-- Set requireSsl=true; domain=application domain name used by FedAuth cookies (Ex: .gdinfra.com); -->
      <cookieHandler requireSsl="true" persistentSessionLifetime="0.0:20:0" />
    ....  
    </federationConfiguration>
  </system.identityModel.services>
```

## <a name="all-http-based-application-should-specify-http-only-for-cookie-definition"></a><a id="cookie-definition"></a>Voor alle op http gebaseerde toepassingen moet alleen http worden opgegeven voor de cookie definitie

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [Kenmerk van beveiligde cookie](https://en.wikipedia.org/wiki/HTTP_cookie#Secure_cookie) |
| **Stappen** | Om het risico van het vrijgeven van informatie met een XSS-aanval (cross-site scripting) te beperken, werd een nieuw kenmerk-httpOnly-geïntroduceerd in cookies en wordt het door alle belang rijke browsers ondersteund. Het kenmerk geeft aan dat een cookie niet toegankelijk is via het script. Door gebruik te maken van HttpOnly cookies vermindert een webtoepassing de kans dat gevoelige informatie in de cookie via script kan worden gestolen en verzonden naar de website van een aanvaller. |

### <a name="example"></a>Voorbeeld
Alle HTTP-toepassingen die gebruikmaken van cookies, moeten HttpOnly opgeven in de cookie definitie door de volgende configuratie in web.config te implementeren:
```XML
<system.web>
.
.
   <httpCookies requireSSL="false" httpOnlyCookies="true"/>
.
.
</system.web>
```

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Webformulieren |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [Eigenschap FormsAuthentication. RequireSSL](/dotnet/api/system.web.security.formsauthentication.requiressl) |
| **Stappen** | De waarde van de eigenschap RequireSSL is ingesteld in het configuratie bestand voor een ASP.NET-toepassing met behulp van het kenmerk requireSSL van het configuratie-element. U kunt in het Web.config-bestand opgeven voor uw ASP.NET-toepassing, ongeacht of Transport Layer Security (TLS), voorheen bekend als SSL (Secure Sockets Layer), is vereist om de cookie voor formulier verificatie te retour neren naar de server door het kenmerk requireSSL in te stellen.|

### <a name="example"></a>Voorbeeld 
In het volgende code voorbeeld wordt het kenmerk requireSSL in het Web.config-bestand ingesteld.
```XML
<authentication mode="Forms">
  <forms loginUrl="member_login.aspx" cookieless="UseCookies" requireSSL="true"/>
</authentication>
```

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | MVC5 |
| **Kenmerken**              | EnvironmentType-premises |
| **Referenties**              | [Configuratie van Windows Identity Foundation (WIF) – deel II](/archive/blogs/alikl/windows-identity-foundation-wif-configuration-part-ii-cookiehandler-chunkedcookiehandler-customcookiehandler) |
| **Stappen** | HideFromCsript kenmerk waarde moet worden ingesteld op True om het kenmerk httpOnly in te stellen voor FedAuth-cookies. |

### <a name="example"></a>Voorbeeld
De volgende configuratie toont de juiste configuratie:
```XML
<federatedAuthentication>
<cookieHandler mode="Custom"
                       hideFromScript="true"
                       name="FedAuth"
                       path="/"
                       requireSsl="true"
                       persistentSessionLifetime="25">
</cookieHandler>
</federatedAuthentication>
```

## <a name="mitigate-against-cross-site-request-forgery-csrf-attacks-on-aspnet-web-pages"></a><a id="csrf-asp"></a>Problemen oplossen met CSRF-aanvallen (cross-site request vervalsing) op ASP.NET webpagina's

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | N.v.t.  |
| **Stappen** | Cross-site-aanvraag vervalsing (CSRF of XSRF) is een type aanval waarbij een aanvaller acties kan uitvoeren in de beveiligings context van een afwijkende sessie van een andere gebruiker op een website. Het doel is om inhoud te wijzigen of te verwijderen, als de doel website uitsluitend is gebaseerd op sessie cookies om ontvangen aanvragen te verifiëren. Een aanvaller kan dit beveiligingslek misbruiken door de browser van een andere gebruiker te laten laden van een URL met een opdracht van een kwets bare site waarop de gebruiker al is aangemeld. Er zijn veel manieren om dit te doen, bijvoorbeeld door een andere website te hosten die een resource van de kwets bare server laadt of waarmee de gebruiker op een koppeling kan klikken. De aanval kan worden voor komen als de server een extra token naar de client stuurt, vereist dat de client dat token opneemt in alle toekomstige aanvragen en verifieert dat alle toekomstige aanvragen een token bevatten dat betrekking heeft op de huidige sessie, zoals door de ASP.NET AntiForgeryToken of View State te gebruiken. |

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | MVC5, MVC6 |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [XSRF/CSRF voor komen in ASP.NET MVC en webpagina's](https://www.asp.net/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) |
| **Stappen** | Anti-CSRF en ASP.NET MVC-formulieren: gebruik de `AntiForgeryToken` Help-methode voor weer gaven; plaats een `Html.AntiForgeryToken()` in het formulier, bijvoorbeeld|

### <a name="example"></a>Voorbeeld
```csharp
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
```

### <a name="example"></a>Voorbeeld
```csharp
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>Voorbeeld
Tegelijkertijd geeft HTML. AntiForgeryToken () de bezoeker een cookie met de naam __RequestVerificationToken, met dezelfde waarde als de weer gegeven wille keurige verborgen waarde. Voeg vervolgens, om een binnenkomende formulier post te valideren, het filter [ValidateAntiForgeryToken] toe aan de doel actie methode. Bijvoorbeeld:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Autorisatie filter waarmee wordt gecontroleerd of:
* De binnenkomende aanvraag heeft een cookie met de naam __RequestVerificationToken
* De binnenkomende aanvraag bevat een `Request.Form` vermelding met de naam __RequestVerificationToken
* Deze cookie en `Request.Form` waarden komen overeen met het hele goed, de aanvraag gaat normaal door. Als dat niet het geval is, wordt een autorisatie fout met het bericht "een vereiste anti-vervalsing-token niet opgegeven of is het niet geldig". 

### <a name="example"></a>Voorbeeld
Anti-CSRF en AJAX: het formulier token kan een probleem zijn voor AJAX-aanvragen, omdat een AJAX-aanvraag JSON-gegevens kan verzenden, niet de HTML-formulier gegevens. Eén oplossing is het verzenden van de tokens in een aangepaste HTTP-header. De volgende code maakt gebruik van de syntaxis voor het genereren van de tokens en voegt vervolgens de tokens toe aan een AJAX-aanvraag. 
```csharp
<script>
    @functions{
        public string TokenHeaderValue()
        {
            string cookieToken, formToken;
            AntiForgery.GetTokens(null, out cookieToken, out formToken);
            return cookieToken + ":" + formToken;                
        }
    }

    $.ajax("api/values", {
        type: "post",
        contentType: "application/json",
        data: {  }, // JSON data goes here
        dataType: "json",
        headers: {
            'RequestVerificationToken': '@TokenHeaderValue()'
        }
    });
</script>
```

### <a name="example"></a>Voorbeeld
Wanneer u de aanvraag verwerkt, extraheert u de tokens uit de aanvraag header. Roep vervolgens de methode AntiForgery. validate aan om de tokens te valideren. De methode validate genereert een uitzonde ring als de tokens ongeldig zijn.
```csharp
void ValidateRequestHeader(HttpRequestMessage request)
{
    string cookieToken = "";
    string formToken = "";

    IEnumerable<string> tokenHeaders;
    if (request.Headers.TryGetValues("RequestVerificationToken", out tokenHeaders))
    {
        string[] tokens = tokenHeaders.First().Split(':');
        if (tokens.Length == 2)
        {
            cookieToken = tokens[0].Trim();
            formToken = tokens[1].Trim();
        }
    }
    AntiForgery.Validate(cookieToken, formToken);
}
```

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Webformulieren |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [Profiteer van ASP.NET ingebouwde functies om webaanvalen uit te Fend](/previous-versions/dotnet/articles/ms972969(v=msdn.10)#securitybarriers_topic2) |
| **Stappen** | CSRF-aanvallen in webformulieren kunnen worden verholpen door ViewStateUserKey in te stellen op een wille keurige teken reeks die varieert voor elke gebruiker-gebruikers-ID of, nog beter, sessie-ID. Voor een aantal technische en sociale redenen is sessie-ID veel beter, omdat een sessie-ID niet te voors pellen, een time-out optreedt en per gebruiker verschilt.|

### <a name="example"></a>Voorbeeld
Hier ziet u de code die u op al uw pagina's moet hebben:
```csharp
void Page_Init (object sender, EventArgs e) {
   ViewStateUserKey = Session.SessionID;
   :
}
```

## <a name="set-up-session-for-inactivity-lifetime"></a><a id="inactivity-lifetime"></a>Sessie instellen voor een levens duur van inactiviteit

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [Eigenschap HttpSessionState. timeout](/dotnet/api/system.web.sessionstate.httpsessionstate.timeout) |
| **Stappen** | Sessietime time-out vertegenwoordigt de gebeurtenis die optreedt wanneer een gebruiker geen actie uitvoert op een website gedurende een interval (gedefinieerd door de webserver). De gebeurtenis, op server zijde, wijzigt u de status van de gebruikers sessie in ' ongeldig ' (bijvoorbeeld ' niet meer gebruikt ') en geeft de webserver de instructie om deze te vernietigen (waarbij alle gegevens erin worden verwijderd). In het volgende code voorbeeld wordt het sessie kenmerk time-out ingesteld op 15 minuten in het Web.config bestand.|

### <a name="example"></a>Voorbeeld
```XML 
<configuration>
  <system.web>
    <sessionState mode="InProc" cookieless="true" timeout="15" />
  </system.web>
</configuration>
```

## <a name="enable-threat-detection-on-azure-sql"></a><a id="threat-detection"></a>Bedreigings detectie op Azure SQL inschakelen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Webformulieren |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [Formulier element voor verificatie (ASP.NET-instellingen schema)](/previous-versions/dotnet/netframework-4.0/1d3t3c61(v=vs.100)) |
| **Stappen** | Stel de cookie-time-out voor Forms-verificatie ticket in op 15 minuten|

### <a name="example"></a>Voorbeeld
```XML
<forms  name=".ASPXAUTH" loginUrl="login.aspx"  defaultUrl="default.aspx" protection="All" timeout="15" path="/" requireSSL="true" slidingExpiration="true"/>
</forms>
```

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Webformulieren, MVC5 |
| **Kenmerken**              | EnvironmentType-premises |
| **Referenties**              | [asdeqa](https://skf.azurewebsites.net/Mitigations/Details/wefr) |
| **Stappen** | Wanneer de webtoepassing Relying Party is en ADFS de STS is, kan de levens duur van de verificatie cookies-FedAuth-tokens worden ingesteld door de volgende configuratie in web.config:|

### <a name="example"></a>Voorbeeld
```XML
  <system.identityModel.services>
    <federationConfiguration>
      <!-- Set requireSsl=true; domain=application domain name used by FedAuth cookies (Ex: .gdinfra.com); -->
      <cookieHandler requireSsl="true" persistentSessionLifetime="0.0:15:0" />
      <!-- Set requireHttps=true; -->
      <wsFederation passiveRedirectEnabled="true" issuer="http://localhost:39529/" realm="https://localhost:44302/" reply="https://localhost:44302/" requireHttps="true"/>
      <!--
      Use the code below to enable encryption-decryption of claims received from ADFS. Thumbprint value varies based on the certificate being used.
      <serviceCertificate>
        <certificateReference findValue="4FBBBA33A1D11A9022A5BF3492FF83320007686A" storeLocation="LocalMachine" storeName="My" x509FindType="FindByThumbprint" />
      </serviceCertificate>
      -->
    </federationConfiguration>
  </system.identityModel.services>
```

### <a name="example"></a>Voorbeeld
Daarnaast moet de levens duur van de door ADFS uitgegeven SAML-claim token worden ingesteld op 15 minuten, door de volgende Power shell-opdracht op de ADFS-server uit te voeren:
```csharp
Set-ADFSRelyingPartyTrust -TargetName "<RelyingPartyWebApp>" -ClaimsProviderName @("Active Directory") -TokenLifetime 15 -AlwaysRequireAuthentication $true
```

## <a name="implement-proper-logout-from-the-application"></a><a id="proper-app-logout"></a>De juiste afmelding van de toepassing implementeren

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | N.v.t.  |
| **Stappen** | De juiste afmelding van de toepassing uitvoeren wanneer de gebruiker op de knop Afmelden drukt. Na afmelden moet de toepassing de sessie van de gebruiker vernietigen en ook de cookie waarde voor de sessie opnieuw instellen en herstellen, samen met het opnieuw instellen en de cookie waarde voor nullifying-verificatie. Wanneer meerdere sessies zijn gekoppeld aan één gebruikers-id, moeten ze ook gezamenlijk worden beëindigd op de server aan de kant van de time-out of afmelding. Ten slotte moet u ervoor zorgen dat de afmeldings functionaliteit op elke pagina beschikbaar is. |

## <a name="mitigate-against-cross-site-request-forgery-csrf-attacks-on-aspnet-web-apis"></a><a id="csrf-api"></a>Problemen oplossen met CSRF-aanvallen (cross-site request vervalsing) op ASP.NET-Web-Api's

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Web-API | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | N.v.t.  |
| **Stappen** | Cross-site-aanvraag vervalsing (CSRF of XSRF) is een type aanval waarbij een aanvaller acties kan uitvoeren in de beveiligings context van een afwijkende sessie van een andere gebruiker op een website. Het doel is om inhoud te wijzigen of te verwijderen, als de doel website uitsluitend is gebaseerd op sessie cookies om ontvangen aanvragen te verifiëren. Een aanvaller kan dit beveiligingslek misbruiken door de browser van een andere gebruiker te laten laden van een URL met een opdracht van een kwets bare site waarop de gebruiker al is aangemeld. Er zijn veel manieren om dit te doen, bijvoorbeeld door een andere website te hosten die een resource van de kwets bare server laadt of waarmee de gebruiker op een koppeling kan klikken. De aanval kan worden voor komen als de server een extra token naar de client stuurt, vereist dat de client dat token opneemt in alle toekomstige aanvragen en verifieert dat alle toekomstige aanvragen een token bevatten dat betrekking heeft op de huidige sessie, zoals door de ASP.NET AntiForgeryToken of View State te gebruiken. |

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Web-API | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | MVC5, MVC6 |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [CSRF-aanvallen (cross-site request vervalsing) in ASP.NET-Web-API voor komen](https://www.asp.net/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks) |
| **Stappen** | Anti-CSRF en AJAX: het formulier token kan een probleem zijn voor AJAX-aanvragen, omdat een AJAX-aanvraag JSON-gegevens kan verzenden, niet de HTML-formulier gegevens. Eén oplossing is het verzenden van de tokens in een aangepaste HTTP-header. De volgende code maakt gebruik van de syntaxis voor het genereren van de tokens en voegt vervolgens de tokens toe aan een AJAX-aanvraag. |

### <a name="example"></a>Voorbeeld
```Javascript
<script>
    @functions{
        public string TokenHeaderValue()
        {
            string cookieToken, formToken;
            AntiForgery.GetTokens(null, out cookieToken, out formToken);
            return cookieToken + ":" + formToken;                
        }
    }
    $.ajax("api/values", {
        type: "post",
        contentType: "application/json",
        data: {  }, // JSON data goes here
        dataType: "json",
        headers: {
            'RequestVerificationToken': '@TokenHeaderValue()'
        }
    });
</script>
```

### <a name="example"></a>Voorbeeld
Wanneer u de aanvraag verwerkt, extraheert u de tokens uit de aanvraag header. Roep vervolgens de methode AntiForgery. validate aan om de tokens te valideren. De methode validate genereert een uitzonde ring als de tokens ongeldig zijn.
```csharp
void ValidateRequestHeader(HttpRequestMessage request)
{
    string cookieToken = "";
    string formToken = "";

    IEnumerable<string> tokenHeaders;
    if (request.Headers.TryGetValues("RequestVerificationToken", out tokenHeaders))
    {
        string[] tokens = tokenHeaders.First().Split(':');
        if (tokens.Length == 2)
        {
            cookieToken = tokens[0].Trim();
            formToken = tokens[1].Trim();
        }
    }
    AntiForgery.Validate(cookieToken, formToken);
}
```

### <a name="example"></a>Voorbeeld
Anti-CSRF en ASP.NET MVC-formulieren: gebruik de AntiForgeryToken-hulp methode voor weer gaven. plaats een HTML. AntiForgeryToken () in het formulier, bijvoorbeeld
```csharp
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
}
```

### <a name="example"></a>Voorbeeld
In het bovenstaande voor beeld wordt ongeveer het volgende uitgevoerd:
```csharp
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>Voorbeeld
Tegelijkertijd geeft HTML. AntiForgeryToken () de bezoeker een cookie met de naam __RequestVerificationToken, met dezelfde waarde als de weer gegeven wille keurige verborgen waarde. Voeg vervolgens, om een binnenkomende formulier post te valideren, het filter [ValidateAntiForgeryToken] toe aan de doel actie methode. Bijvoorbeeld:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Autorisatie filter waarmee wordt gecontroleerd of:
* De binnenkomende aanvraag heeft een cookie met de naam __RequestVerificationToken
* De binnenkomende aanvraag bevat een `Request.Form` vermelding met de naam __RequestVerificationToken
* Deze cookie en `Request.Form` waarden komen overeen met het hele goed, de aanvraag gaat normaal door. Als dat niet het geval is, wordt een autorisatie fout met het bericht "een vereiste anti-vervalsing-token niet opgegeven of is het niet geldig".

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Web-API | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | MVC5, MVC6 |
| **Kenmerken**              | ID-provider-ADFS, ID-provider-Azure AD |
| **Referenties**              | [Een web-API beveiligen met afzonderlijke accounts en lokale aanmelding in ASP.NET Web API 2,2](https://www.asp.net/web-api/overview/security/individual-accounts-in-web-api) |
| **Stappen** | Als de Web-API wordt beveiligd met OAuth 2,0, verwacht deze een Bearer-token in de header van de autorisatie aanvraag en wordt alleen toegang verleend aan de aanvraag als het token geldig is. In tegens telling tot op cookies gebaseerde verificatie, koppelen browsers geen Bearer-tokens aan aanvragen. De aanvragende client moet het Bearer-token expliciet koppelen aan de aanvraag header. Daarom worden voor ASP.NET-Web-Api's die zijn beveiligd met OAuth 2,0, Bearer-tokens beschouwd als een verdediging tegen CSRF-aanvallen. Houd er rekening mee dat als het MVC-gedeelte van de toepassing formulier verificatie gebruikt (bijvoorbeeld cookies gebruikt), anti-vervalsing tokens moeten worden gebruikt door de MVC-Web-app. |

### <a name="example"></a>Voorbeeld
De Web-API moet worden geïnformeerd om alleen te vertrouwen op Bearer-tokens en niet op cookies. Dit kan worden gedaan door de volgende configuratie in `WebApiConfig.Register` methode:

```csharp
config.SuppressDefaultHostAuthentication();
config.Filters.Add(new HostAuthenticationFilter(OAuthDefaults.AuthenticationType));
```

De methode SuppressDefaultHostAuthentication vertelt Web API voor het negeren van eventuele verificaties die zich voordoen voordat de aanvraag de Web-API-pijp lijn, hetzij door IIS of door de middleware van OWIN, wordt bereikt. Op die manier kunnen we Web API beperken voor verificatie alleen met Bearer-tokens.