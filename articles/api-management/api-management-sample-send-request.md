---
title: API Management-service gebruiken om HTTP-aanvragen te genereren
description: Meer informatie over het gebruik van de beleids regels voor aanvragen en antwoorden in API Management om externe services vanuit uw API aan te roepen
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 4539c0fa-21ef-4b1c-a1d4-d89a38c242fa
ms.service: api-management
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 2f4bd040d7e5858fd561444f56dbce7b3f940d9a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92742391"
---
# <a name="using-external-services-from-the-azure-api-management-service"></a>Externe services van de Azure API Management-service gebruiken
De beschik bare beleids regels in azure API Management service kunnen een breed scala aan nuttige werk wijze hebben op basis van de inkomende aanvraag, het uitgaande antwoord en de basis configuratie-informatie. Als u echter met externe services van API Management beleid wilt werken, worden er veel meer mogelijkheden geopend.

U hebt eerder gezien hoe u kunt communiceren met de [Azure Event hub-service voor logboek registratie, bewaking en analyses](api-management-log-to-eventhub-sample.md). In dit artikel wordt uitgelegd hoe u kunt communiceren met een externe HTTP-gebaseerde service. Deze beleids regels kunnen worden gebruikt voor het activeren van externe gebeurtenissen of voor het ophalen van informatie die wordt gebruikt om de oorspronkelijke aanvraag en het antwoord op een of andere manier te manipuleren.

## <a name="send-one-way-request"></a>Send-eenrichtings aanvraag
Mogelijk is de eenvoudigste externe interactie de brand-en-vergeet-stijl van een aanvraag waarmee een externe service op de hoogte wordt gebracht van een soort belang rijke gebeurtenis. Het controle stroom beleid `choose` kan worden gebruikt om te bepalen welke voor waarde u wilt gebruiken.  Als aan de voor waarde is voldaan, kunt u een externe HTTP-aanvraag indienen via het beleid voor het [aanvragen van een aanvraag voor eenmalige verzen ding](./api-management-advanced-policies.md#SendOneWayRequest) . Dit kan een aanvraag zijn voor een berichten systeem zoals Hipchat of toegestane vertraging, of een e-mail-API zoals SendGrid of MailChimp, of voor essentiële ondersteunings incidenten zoals PagerDuty. Al deze berichten systemen hebben eenvoudige HTTP-Api's die kunnen worden aangeroepen.

### <a name="alerting-with-slack"></a>Waarschuwingen met toegestane vertraging
In het volgende voor beeld ziet u hoe u een bericht verzendt naar een ruimte voor de chat room als de status code van het HTTP-antwoord groter is dan of gelijk is aan 500. Een 500-bereik fout wijst op een probleem met de back-end-API dat de client van de API zichzelf niet kan oplossen. Normaal gesp roken is enige soort interventie op API Management onderdeel vereist.  

```xml
<choose>
    <when condition="@(context.Response.StatusCode >= 500)">
      <send-one-way-request mode="new">
        <set-url>https://hooks.slack.com/services/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</set-url>
        <set-method>POST</set-method>
        <set-body>@{
                return new JObject(
                        new JProperty("username","APIM Alert"),
                        new JProperty("icon_emoji", ":ghost:"),
                        new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",
                                                context.Request.Method,
                                                context.Request.Url.Path + context.Request.Url.QueryString,
                                                context.Request.Url.Host,
                                                context.Response.StatusCode,
                                                context.Response.StatusReason,
                                                context.User.Email
                                                ))
                        ).ToString();
            }</set-body>
      </send-one-way-request>
    </when>
</choose>
```

De toegestane vertraging is het principe van inkomende webhooks. Bij het configureren van een inkomende webhook genereert de toegestane vertraging een speciale URL, waarmee u een eenvoudige POST-aanvraag kunt uitvoeren en een bericht in het toegestane kanaal kunt door geven. De JSON-hoofd tekst die u maakt, is gebaseerd op een indeling die is gedefinieerd door een toegestane vertraging.

![Toegestane webhook](./media/api-management-sample-send-request/api-management-slack-webhook.png)

### <a name="is-fire-and-forget-good-enough"></a>Is de brand-en vergeet goed genoeg?
Er zijn bepaalde afwegingen bij het gebruik van een brand-en-vergeet-stijl voor de aanvraag. Als de aanvraag om de een of andere reden mislukt, wordt de fout niet gerapporteerd. In deze specifieke situatie is de complexiteit van het gebruik van een secundair systeem voor fout rapportage en de extra prestatie kosten voor het reageren op het antwoord niet gerechtvaardigd. Voor scenario's waarbij het van essentieel belang is om het antwoord te controleren, is het beleid voor het [verzenden van aanvragen](./api-management-advanced-policies.md#SendRequest) een betere optie.

## <a name="send-request"></a>Send-Request
`send-request`Met het beleid kunt u een externe service gebruiken om complexe verwerkings functies uit te voeren en gegevens te retour neren naar de API Management-service die kan worden gebruikt voor verdere beleids verwerking.

### <a name="authorizing-reference-tokens"></a>Referentie tokens autoriseren
Een belang rijke functie van API Management is het beveiligen van back-endservers. Als de autorisatie server die door uw API wordt gebruikt, JWT [Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md) - [tokens](https://jwt.io/) maakt als onderdeel van de OAuth2-stroom, kunt u het `validate-jwt` beleid gebruiken om de geldigheid van het token te controleren. Sommige autorisatie servers maken wat [referentie tokens](https://leastprivilege.com/2015/11/25/reference-tokens-and-introspection/) , die niet kunnen worden geverifieerd zonder dat ze een call back naar de autorisatie server maken.

### <a name="standardized-introspection"></a>Gestandaardiseerde introspectie
In het verleden is er geen gestandaardiseerde manier voor het controleren van een referentie token met een autorisatie server. Een recent voorgesteld standaard [RFC 7662](https://tools.ietf.org/html/rfc7662) is echter gepubliceerd door de IETF, waarmee wordt gedefinieerd hoe een bron server de geldigheid van een token kan controleren.

### <a name="extracting-the-token"></a>Het token extra heren
De eerste stap bestaat uit het extra heren van het token uit de autorisatie-header. De waarde van de header moet worden opgemaakt met het `Bearer` autorisatie schema, één spatie en vervolgens het autorisatie token conform [RFC 6750](https://tools.ietf.org/html/rfc6750#section-2.1). Er zijn helaas gevallen waarin het autorisatie schema wordt wegge laten. Als u deze account tijdens het parseren wilt verwerken, splitst API Management de header waarde op een spatie en selecteert de laatste teken reeks uit de geretourneerde matrix met teken reeksen. Dit biedt een tijdelijke oplossing voor autorisatie headers met ongeldige indeling.

```xml
<set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />
```

### <a name="making-the-validation-request"></a>De validatie aanvraag maken
Als API Management het autorisatie token heeft, kan API Management de aanvraag indienen om het token te valideren. RFC 7662 roept dit proces introspectie aan en vereist dat u `POST` een HTML-formulier naar de introspectie-resource. Het HTML-formulier moet ten minste een sleutel/waarde-paar met de sleutel bevatten `token` . Deze aanvraag voor de autorisatie server moet ook worden geverifieerd, om ervoor te zorgen dat kwaad aardige clients geen trawling kunnen doen voor geldige tokens.

```xml
<send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
  <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
  <set-method>POST</set-method>
  <set-header name="Authorization" exists-action="override">
    <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
  </set-header>
  <set-header name="Content-Type" exists-action="override">
    <value>application/x-www-form-urlencoded</value>
  </set-header>
  <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
</send-request>
```

### <a name="checking-the-response"></a>Het antwoord controleren
Het `response-variable-name` kenmerk wordt gebruikt om toegang te geven tot het geretourneerde antwoord. De naam die in deze eigenschap is gedefinieerd, kan worden gebruikt als sleutel in de `context.Variables` woorden lijst om het object te openen `IResponse` .

Vanuit het antwoord object kunt u de hoofd tekst ophalen en RFC 7622 vertelt API Management dat het antwoord een JSON-object moet zijn en moet ten minste een eigenschap hebben `active` die een Booleaanse waarde is. Wanneer `active` is ingesteld op True, wordt het token als geldig beschouwd.

Als de autorisatie server niet het veld actief bevat om aan te geven of het token geldig is, gebruikt u een hulp programma zoals postman om te bepalen welke eigenschappen in een geldig token zijn ingesteld. Als een geldig token antwoord bijvoorbeeld een eigenschap bevat met de naam ' expires_in ', controleert u of deze eigenschaps naam op de volgende manier in het antwoord op de autorisatie server aanwezig is:

<als voor waarde = "@ ((IResponse)-context. Variabelen ["tokenstate"]). Body.As <JObject> (). Eigenschap ("expires_in") = = null) ">

### <a name="reporting-failure"></a>Fout rapportage
U kunt een `<choose>` beleid gebruiken om te detecteren of het token ongeldig is en als dit het geval is, retour neren we een 401-antwoord.

```xml
<choose>
  <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
    <return-response response-variable-name="existing response variable">
      <set-status code="401" reason="Unauthorized" />
      <set-header name="WWW-Authenticate" exists-action="override">
        <value>Bearer error="invalid_token"</value>
      </set-header>
    </return-response>
  </when>
</choose>
```

Conform [RFC 6750](https://tools.ietf.org/html/rfc6750#section-3) , waarin wordt beschreven hoe `bearer` tokens moeten worden gebruikt, retourneert API Management ook een `WWW-Authenticate` header met het 401-antwoord. De WWW-Authenticate is bedoeld om een client te instrueren om een goed geautoriseerde aanvraag te maken. Vanwege de grote verscheidenheid aan benaderingen van het OAuth2-Framework is het moeilijk om alle benodigde informatie te communiceren. Gelukkig zijn er inspanningen om clients te helpen [detecteren hoe ze op de juiste wijze aanvragen aan een bron server kunnen autoriseren](https://tools.ietf.org/html/draft-jones-oauth-discovery-00).

### <a name="final-solution"></a>Laatste oplossing
Aan het einde krijgt u het volgende beleid:

```xml
<inbound>
  <!-- Extract Token from Authorization header parameter -->
  <set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />

  <!-- Send request to Token Server to validate token (see RFC 7662) -->
  <send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
    <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
    <set-method>POST</set-method>
    <set-header name="Authorization" exists-action="override">
      <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
    </set-header>
    <set-header name="Content-Type" exists-action="override">
      <value>application/x-www-form-urlencoded</value>
    </set-header>
    <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
  </send-request>

  <choose>
          <!-- Check active property in response -->
          <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
              <!-- Return 401 Unauthorized with http-problem payload -->
              <return-response response-variable-name="existing response variable">
                  <set-status code="401" reason="Unauthorized" />
                  <set-header name="WWW-Authenticate" exists-action="override">
                      <value>Bearer error="invalid_token"</value>
                  </set-header>
              </return-response>
          </when>
      </choose>
  <base />
</inbound>
```

Dit is slechts een van de vele voor beelden van de manier waarop het `send-request` beleid kan worden gebruikt om nuttige externe services te integreren in het proces van aanvragen en reacties die via de API Management-service stromen.

## <a name="response-composition"></a>Reactie compositie
Het `send-request` beleid kan worden gebruikt voor het verbeteren van een primaire aanvraag naar een back-end-systeem, zoals u hebt gezien in het vorige voor beeld, of het kan worden gebruikt als een volledige vervangende functie voor de back-end. Met deze techniek kunt u eenvoudig samengestelde resources maken die worden geaggregeerd van meerdere verschillende systemen.

### <a name="building-a-dashboard"></a>Een dash board bouwen
Soms wilt u in staat zijn om informatie in meerdere back-end-systemen beschikbaar te stellen, bijvoorbeeld om een dash board te maken. De Kpi's zijn afkomstig van alle verschillende back-ends, maar u kunt liever geen directe toegang geven en het zou handig zijn als alle informatie in één aanvraag kan worden opgehaald. Het is mogelijk dat een deel van de back-end-informatie enkele segmentering en delen en nog een beetje opschonen heeft. Het kan een goed idee zijn om de samen stelling van de back-up in de cache op te lossen, omdat u weet dat gebruikers een gewoonte hebben om de F5-toets te hameren om te zien of de metrische gegevens die worden uitgevoerd, kunnen worden gewijzigd.    

### <a name="faking-the-resource"></a>De resource Faking
De eerste stap bij het bouwen van de resource in het dash board is het configureren van een nieuwe bewerking in de Azure Portal. Dit is een tijdelijke bewerking die wordt gebruikt voor het configureren van een compositie beleid voor het bouwen van de dynamische resource.

![Dashboard bewerking](./media/api-management-sample-send-request/api-management-dashboard-operation.png)

### <a name="making-the-requests"></a>De aanvragen indienen
Zodra de bewerking is gemaakt, kunt u een beleid voor die bewerking specifiek configureren. 

![Scherm opname van het scherm met het beleids bereik.](./media/api-management-sample-send-request/api-management-dashboard-policy.png)

De eerste stap bestaat uit het extra heren van alle query parameters uit de inkomende aanvraag, zodat u deze kunt door sturen naar de back-end. In dit voor beeld wordt in het dash board informatie weer gegeven op basis van een bepaalde tijd en heeft deze daarom een `fromDate` en- `toDate` para meter. U kunt het `set-variable` beleid gebruiken om de gegevens van de aanvraag-URL uit te pakken.

```xml
<set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
<set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">
```

Zodra u deze informatie hebt, kunt u aanvragen indienen voor alle back-end-systemen. Elke aanvraag bouwt een nieuwe URL met de parameter informatie en roept de betreffende server op en slaat de reactie op in een context variabele.

```xml
<send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
  <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
  <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
<set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
<set-url>@($"https://production.acme.com/accidentdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>
```

Deze aanvragen worden in de juiste volg orde uitgevoerd. Dit is niet ideaal. 

### <a name="responding"></a>Reageert
Als u het samengestelde antwoord wilt maken, kunt u het beleid voor [retour reacties](./api-management-advanced-policies.md#ReturnResponse) gebruiken. Het `set-body` element kan een expressie gebruiken om een nieuw item samen te stellen `JObject` met alle onderdelen die als eigenschappen zijn Inge sloten.

```xml
<return-response response-variable-name="existing response variable">
  <set-status code="200" reason="OK" />
  <set-header name="Content-Type" exists-action="override">
    <value>application/json</value>
  </set-header>
  <set-body>
    @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                  new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                  new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                  new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                  ).ToString())
  </set-body>
</return-response>
```

Het volledige beleid ziet er als volgt uit:

```xml
<policies>
    <inbound>

  <set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
  <set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">

    <send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
      <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
      <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/accidentdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <return-response response-variable-name="existing response variable">
      <set-status code="200" reason="OK" />
      <set-header name="Content-Type" exists-action="override">
        <value>application/json</value>
      </set-header>
      <set-body>
        @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                      new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                      new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                      new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                      ).ToString())
      </set-body>
    </return-response>
    </inbound>
    <backend>
        <base />
    </backend>
    <outbound>
        <base />
    </outbound>
</policies>
```

In de configuratie van de tijdelijke aanduiding kunt u de resource van het dash board zodanig configureren dat deze ten minste een uur in de cache wordt opgeslagen. 

## <a name="summary"></a>Samenvatting
Azure API Management-service biedt een flexibel beleid dat selectief kan worden toegepast op HTTP-verkeer en waarmee back-end-services kunnen worden samengesteld. Of u uw API-gateway wilt verbeteren met waarschuwings functies, verificatie, validatie mogelijkheden of het maken van nieuwe samengestelde resources op basis van meerdere back-endservers, `send-request` maar het beleid en de bijbehorende beleids regels openen een wereld van mogelijkheden.
