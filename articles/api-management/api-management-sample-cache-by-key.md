---
title: Custom caching in Azure API Management (Aangepast opslaan in Azure API Management)
description: Meer informatie over het opslaan van items op sleutel in azure API Management. U kunt de sleutel wijzigen met behulp van aanvraag headers.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 772bc8dd-5cda-41c4-95bf-b9f6f052bc85
ms.service: api-management
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: cf9901b4e49460dd2fb91dceaf239571058c5284
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88213325"
---
# <a name="custom-caching-in-azure-api-management"></a>Custom caching in Azure API Management (Aangepast opslaan in Azure API Management)
De Azure API Management-service heeft ingebouwde ondersteuning voor [http-antwoord cache](api-management-howto-cache.md) met behulp van de resource-URL als sleutel. De sleutel kan worden gewijzigd met aanvraag headers met behulp van de `vary-by` Eigenschappen. Dit is handig voor het in de cache opslaan van volledige HTTP-antwoorden (ook wel bekend als weer geven), maar soms is het handig om alleen een deel van een representatie in de cache op te slaan. De nieuwe beleids regels voor [cache-lookup-value](./api-management-caching-policies.md#GetFromCacheByKey) en [cache-Store-value](./api-management-caching-policies.md#StoreToCacheByKey) bieden de mogelijkheid om wille keurige stukjes gegevens op te slaan en op te halen uit de beleids definities. Deze mogelijkheid voegt ook waarde toe aan het eerder geïntroduceerde beleid voor het [verzenden van aanvragen](./api-management-advanced-policies.md#SendRequest) omdat u nu reacties van externe services kunt opslaan in de cache.

## <a name="architecture"></a>Architectuur
API Management-service maakt gebruik van een gedeelde gegevens cache per Tenant, zodat u, wanneer u omhoog schaalt naar meerdere eenheden, u nog steeds toegang krijgt tot dezelfde gegevens in de cache. Wanneer u werkt met een implementatie met meerdere regio's, zijn er echter onafhankelijke caches in elk van de regio's. Het is belang rijk dat u de cache niet als een gegevens archief behandelt, waarbij het de enige bron is van een deel van de gegevens. Als u dit hebt gedaan en later besluit om te profiteren van de implementatie met meerdere regio's, kunnen klanten met gebruikers die onderweg geen toegang meer hebben tot de gegevens in de cache.

## <a name="fragment-caching"></a>Fragment caching
Er zijn bepaalde gevallen waarin het retour neren van antwoorden een deel van de gegevens bevat die kostbaar zijn om te bepalen en die gedurende een redelijke hoeveelheid tijd vernieuwd blijven. Een voor beeld hiervan is een service die is gebouwd door een luchtvaart maatschappij die informatie bevat over vlucht reserveringen, vlucht status, enzovoort. Als de gebruiker lid is van het programma Airlines Points, hebben ze ook informatie over de huidige status en de gecumuleerde kilometer vergoeding. Deze aan de gebruiker gerelateerde informatie kan worden opgeslagen in een ander systeem, maar het kan wenselijk zijn om deze op te vragen in antwoorden die zijn geretourneerd over de status en reserve ringen van de vlucht. Dit kan worden gedaan met behulp van een proces met de naam fragment-caching. De primaire weer gave kan worden geretourneerd vanaf de oorspronkelijke server met een bepaald type token om aan te geven waar de gebruikers gegevens moeten worden ingevoegd. 

Bekijk de volgende JSON-reactie van een back-end-API.

```json
{
  "airline" : "Air Canada",
  "flightno" : "871",
  "status" : "ontime",
  "gate" : "B40",
  "terminal" : "2A",
  "userprofile" : "$userprofile$"
}  
```

En de secundaire resource op `/userprofile/{userid}` dat lijkt,

```json
{ "username" : "Bob Smith", "Status" : "Gold" }
```

API Management moet identificeren wie de eind gebruiker is om te bepalen welke gebruikers gegevens moeten worden toegevoegd. Dit mechanisme is implementatie afhankelijk. Een voor beeld: Ik gebruik de `Subject` claim van een `JWT` token. 

```xml
<set-variable
  name="enduserid"
  value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />
```

API Management slaat de `enduserid` waarde in een context variabele op voor later gebruik. De volgende stap is om te bepalen of een eerdere aanvraag de gebruikers gegevens al heeft opgehaald en opgeslagen in de cache. Hiervoor gebruikt API Management het `cache-lookup-value` beleid.

```xml
<cache-lookup-value
key="@("userprofile-" + context.Variables["enduserid"])"
variable-name="userprofile" />
```

Als er geen vermelding is in de cache die overeenkomt met de sleutel waarde, wordt er geen `userprofile` context variabele gemaakt. API Management controleert of de zoek actie is geslaagd met behulp van het `choose` controle beleid.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("userprofile"))">
        <!-- If the userprofile context variable doesn’t exist, make an HTTP request to retrieve it.  -->
    </when>
</choose>
```

Als de `userprofile` context variabele niet bestaat, moet API management een HTTP-aanvraag indienen om deze op te halen.

```xml
<send-request
  mode="new"
  response-variable-name="userprofileresponse"
  timeout="10"
  ignore-error="true">

  <!-- Build a URL that points to the profile for the current end-user -->
  <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),
      (string)context.Variables["enduserid"]).AbsoluteUri)
  </set-url>
  <set-method>GET</set-method>
</send-request>
```

API Management gebruikt de `enduserid` om de URL voor de gebruikers profiel bron te maken. Zodra API Management de reactie heeft, wordt de hoofd tekst uit het antwoord opgehaald en wordt deze weer opgeslagen in een context variabele.

```xml
<set-variable
    name="userprofile"
    value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />
```

Als u wilt voor komen API Management deze HTTP-aanvraag opnieuw te maken wanneer dezelfde gebruiker een andere aanvraag doet, kunt u opgeven om het gebruikers profiel op te slaan in de cache.

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />
```

API Management slaat de waarde in de cache op met de exacte sleutel die API Management oorspronkelijk heeft geprobeerd deze op te halen. De duur die API Management kiest om de waarde op te slaan, moet gebaseerd zijn op hoe vaak de gegevens veranderen en hoe tolerante gebruikers verouderde informatie hebben. 

Het is belang rijk om te realiseren dat het ophalen uit de cache nog steeds een out-of-process-netwerk aanvraag is en mogelijk nog steeds tien tallen milliseconden kan worden toegevoegd aan de aanvraag. De voor delen zijn van belang wanneer het bepalen van de gebruikers profiel gegevens langer duurt dan die omdat u database query's of statistische gegevens van meerdere back-ends nodig hebt.

De laatste stap in het proces is het bijwerken van het geretourneerde antwoord met de gebruikers profiel gegevens.

```xml
<!-- Update response body with user profile-->
<find-and-replace
    from='"$userprofile$"'
    to="@((string)context.Variables["userprofile"])" />
```

U kunt ervoor kiezen om de aanhalings tekens als onderdeel van het token toe te voegen, zodat zelfs wanneer de vervangen niet plaatsvindt, het antwoord nog steeds een geldige JSON is.  

Wanneer u al deze stappen samen combineert, is het eind resultaat een beleid dat lijkt op het volgende.

```xml
<policies>
    <inbound>
        <!-- How you determine user identity is application dependent -->
        <set-variable
          name="enduserid"
          value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />

        <!--Look for userprofile for this user in the cache -->
        <cache-lookup-value
          key="@("userprofile-" + context.Variables["enduserid"])"
          variable-name="userprofile" />

        <!-- If API Management doesn’t find it in the cache, make a request for it and store it -->
        <choose>
            <when condition="@(!context.Variables.ContainsKey("userprofile"))">
                <!-- Make HTTP request to get user profile -->
                <send-request
                  mode="new"
                  response-variable-name="userprofileresponse"
                  timeout="10"
                  ignore-error="true">

                   <!-- Build a URL that points to the profile for the current end-user -->
                    <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),(string)context.Variables["enduserid"]).AbsoluteUri)</set-url>
                    <set-method>GET</set-method>
                </send-request>

                <!-- Store response body in context variable -->
                <set-variable
                  name="userprofile"
                  value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />

                <!-- Store result in cache -->
                <cache-store-value
                  key="@("userprofile-" + context.Variables["enduserid"])"
                  value="@((string)context.Variables["userprofile"])"
                  duration="100000" />
            </when>
        </choose>
        <base />
    </inbound>
    <outbound>
        <!-- Update response body with user profile-->
        <find-and-replace
              from='"$userprofile$"'
              to="@((string)context.Variables["userprofile"])" />
        <base />
    </outbound>
</policies>
```

Deze cache benadering wordt voornamelijk gebruikt in websites waarbij HTML is samengesteld op de server, zodat deze kan worden weer gegeven als één pagina. Het kan ook nuttig zijn in Api's waarbij clients geen HTTP-caching aan client zijde kunnen uitvoeren of dat het wenselijk is dat deze verantwoordelijkheid niet op de client wordt geplaatst.

Hetzelfde soort fragment cache kan ook worden uitgevoerd op de back-end-webservers met behulp van een redis-cache server, maar het gebruik van de API Management-service voor het uitvoeren van dit werk is handig als de in cache gefragmenteerde fragmenten afkomstig zijn van verschillende back-ends dan de primaire reacties.

## <a name="transparent-versioning"></a>Transparante versie beheer
Het is gebruikelijk dat meerdere verschillende implementatie versies van een API tegelijk worden ondersteund. Ter ondersteuning van verschillende omgevingen (dev, test, productie, enz.) of ter ondersteuning van oudere versies van de API om tijd te bieden aan API-gebruikers om te migreren naar nieuwere versies. 

Een manier om dit af te handelen, in plaats van client ontwikkelaars in te stellen om de Url's te wijzigen van `/v1/customers` in `/v2/customers` , is om op te slaan in de profiel gegevens van de gebruiker welke versie van de API ze momenteel willen gebruiken en de juiste back-end-URL aan te roepen. Als u wilt bepalen welke back-end-URL moet worden aangeroepen voor een bepaalde client, moet u een query uitvoeren op sommige configuratie gegevens. Als u deze configuratie gegevens in de cache opslaat, kan API Management de prestaties van deze zoek actie tot een minimum beperken.

De eerste stap is het bepalen van de id die wordt gebruikt voor het configureren van de gewenste versie. In dit voor beeld koos ik ervoor om de versie te koppelen aan de sleutel voor het product abonnement. 

```xml
<set-variable name="clientid" value="@(context.Subscription.Key)" />
```

API Management vervolgens een cache zoekopdracht om te zien of deze al de gewenste client versie heeft opgehaald.

```xml
<cache-lookup-value
key="@("clientversion-" + context.Variables["clientid"])"
variable-name="clientversion" />
```

API Management controleert vervolgens of deze niet in de cache is gevonden.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("clientversion"))">
```

Als API Management het niet vindt, wordt het door API Management opgehaald.

```xml
<send-request
    mode="new"
    response-variable-name="clientconfiguresponse"
    timeout="10"
    ignore-error="true">
            <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
            <set-method>GET</set-method>
</send-request>
```

Haal de tekst van het antwoord op uit het antwoord.

```xml
<set-variable
      name="clientversion"
      value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
```

Sla het opnieuw op in de cache voor toekomstig gebruik.

```xml
<cache-store-value
      key="@("clientversion-" + context.Variables["clientid"])"
      value="@((string)context.Variables["clientversion"])"
      duration="100000" />
```

En werk vervolgens de back-end-URL bij om de versie te selecteren van de service die door de client wordt gewenst.

```xml
<set-backend-service
      base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
```

Het volledige beleid is als volgt:

```xml
<inbound>
    <base />
    <set-variable name="clientid" value="@(context.Subscription.Key)" />
    <cache-lookup-value key="@("clientversion-" + context.Variables["clientid"])" variable-name="clientversion" />

    <!-- If API Management doesn’t find it in the cache, make a request for it and store it -->
    <choose>
        <when condition="@(!context.Variables.ContainsKey("clientversion"))">
            <send-request mode="new" response-variable-name="clientconfiguresponse" timeout="10" ignore-error="true">
                <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
                <set-method>GET</set-method>
            </send-request>
            <!-- Store response body in context variable -->
            <set-variable name="clientversion" value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
            <!-- Store result in cache -->
            <cache-store-value key="@("clientversion-" + context.Variables["clientid"])" value="@((string)context.Variables["clientversion"])" duration="100000" />
        </when>
    </choose>
    <set-backend-service base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
</inbound>
```

Het inschakelen van API-gebruikers om op transparante wijze te bepalen welke back-end-versie wordt gebruikt door clients, zonder dat clients hoeven te worden bijgewerkt en opnieuw te implementeren is een elegante oplossing die veel API-versie problemen behandelt.

## <a name="tenant-isolation"></a>Tenant isolatie
In grotere implementaties met meerdere tenants maken sommige bedrijven afzonderlijke groepen tenants op verschillende implementaties van back-end-hardware. Dit beperkt het aantal klanten dat wordt beïnvloed door een hardwareprobleem op de back-end. Daarnaast kunnen nieuwe software versies in fasen worden geïmplementeerd. In het ideale geval is deze back-end-architectuur transparant voor gebruikers van de API. Dit kan op een vergelijk bare manier worden gerealiseerd als transparante versie beheer, omdat deze is gebaseerd op dezelfde techniek van het bewerken van de back-end-URL met configuratie status per API-sleutel.  

In plaats van een voorkeurs versie van de API te retour neren voor elke abonnements sleutel, zou u een id retour neren die een Tenant verbindt met de toegewezen hardwaregroep. Deze id kan worden gebruikt om de juiste back-end-URL te maken.

## <a name="summary"></a>Samenvatting
De vrijheid van het gebruik van de cache van Azure API Management voor het opslaan van elk soort gegevens maakt efficiënte toegang mogelijk tot configuratie gegevens die van invloed kunnen zijn op de manier waarop een inkomende aanvraag wordt verwerkt. Het kan ook worden gebruikt voor het opslaan van gegevens fragmenten die reacties kunnen uitbreiden, geretourneerd door een back-end-API.
