---
title: Azure API Management-beleid voor meerdere domeinen | Microsoft Docs
description: Meer informatie over het interdomeinbeveiligingsmodel dat beschikbaar is voor gebruik in azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 7689d277-8abe-472a-a78c-e6d4bd43455d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/01/2021
ms.author: apimpm
ms.openlocfilehash: 85abf30d792b24b92685e191f5b460a42dc29142
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101688413"
---
# <a name="api-management-cross-domain-policies"></a>API Management-beleid voor meerdere domeinen
In dit onderwerp vindt u een verwijzing naar de volgende API Management-beleids regels. Zie [beleid in API Management](./api-management-policies.md)voor meer informatie over het toevoegen en configureren van beleid.

## <a name="cross-domain-policies"></a><a name="CrossDomainPolicies"></a> Beleid voor meerdere domeinen

- [Interdomein-aanroepen toestaan](api-management-cross-domain-policies.md#AllowCrossDomainCalls) : maakt de API toegankelijk vanuit Adobe Flash en micro soft Silverlight-clients op basis van een browser.
- [Cors](api-management-cross-domain-policies.md#CORS) -voegt cross-Origin resource SHARING (CORS)-ondersteuning toe aan een bewerking of een API voor het toestaan van interdomein-aanroepen vanuit clients op basis van de browser.
- [Jsonp](api-management-cross-domain-policies.md#JSONP) : voegt JSON met opvulling (Jsonp)-ondersteuning toe aan een bewerking of een API voor het toestaan van cross-domein aanroepen vanuit Java script-clients op basis van een browser.

## <a name="allow-cross-domain-calls"></a><a name="AllowCrossDomainCalls"></a> Interdomein-aanroepen toestaan
Gebruik het `cross-domain` beleid om de API toegankelijk te maken vanuit Adobe Flash en micro soft Silverlight browser-gebaseerde clients.

### <a name="policy-statement"></a>Beleids verklaring

```xml
<cross-domain>
    <!-Policy configuration is in the Adobe cross-domain policy file format,
        see https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html-->
</cross-domain>
```

### <a name="example"></a>Voorbeeld

```xml
<cross-domain>
        <allow-http-request-headers-from domain='*' headers='*' />
</cross-domain>
```

### <a name="elements"></a>Elementen

|Naam|Beschrijving|Vereist|
|----------|-----------------|--------------|
|Kruis domein|Hoofd element. Onderliggende elementen moeten voldoen aan de [Adobe Cross-Domain policy file-specificatie](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html).|Yes|

### <a name="usage"></a>Gebruik
Dit beleid kan worden gebruikt in de volgende beleids [secties](./api-management-howto-policies.md#sections) en [bereiken](./api-management-howto-policies.md#scopes).

- **Beleids secties:** binnenkomend
- **Beleids bereik:** alle bereiken

## <a name="cors"></a><a name="CORS"></a> VOORBEREIDENDE
Met het `cors` beleid wordt ondersteuning geboden voor het gebruik van een CORS (cross-Origin Resource Sharing) aan een bewerking of een API voor het toestaan van interdomein-aanroepen vanuit clients die zijn gebaseerd op de browser. 

> [!NOTE]
> Als de aanvraag overeenkomt met een bewerking met een optie methode die is gedefinieerd in de API, wordt de logica voor de verwerking van aanvragen vóór de vlucht die is gekoppeld aan het CORS-beleid niet uitgevoerd. Daarom kunnen dergelijke bewerkingen worden gebruikt voor het implementeren van aangepaste logica voor de verwerking van vóór de vlucht.

Met CORS kunnen een browser en een server communiceren en bepalen of specifieke cross-Origin-aanvragen (XMLHttpRequests-aanroepen van Java script op een webpagina naar andere domeinen) al dan niet mogen worden uitgevoerd. Dit biedt meer flexibiliteit dan alleen het toestaan van niet-oorspronkelijke aanvragen, maar is veiliger dan het toestaan van alle cross-Origin-aanvragen.

U moet het CORS-beleid Toep assen om de interactieve console in de ontwikkelaars Portal in te scha kelen. Raadpleeg de documentatie voor de [ontwikkelaars Portal](./api-management-howto-developer-portal.md#cors) voor meer informatie.

### <a name="policy-statement"></a>Beleids verklaring

```xml
<cors allow-credentials="false|true" terminate-unmatched-request="true|false">
    <allowed-origins>
        <origin>origin uri</origin>
    </allowed-origins>
    <allowed-methods preflight-result-max-age="number of seconds">
        <method>http verb</method>
    </allowed-methods>
    <allowed-headers>
        <header>header name</header>
    </allowed-headers>
    <expose-headers>
        <header>header name</header>
    </expose-headers>
</cors>
```

### <a name="example"></a>Voorbeeld
In dit voor beeld wordt gedemonstreerd hoe aanvragen voorafgaand aan de vlucht worden ondersteund, zoals gebruikers met aangepaste kopteksten of andere methoden dan GET en POST. Gebruik de `allowed-methods` secties en, `allowed-headers` zoals wordt weer gegeven in het volgende voor beeld, ter ondersteuning van aangepaste headers en extra http-termen.

```xml
<cors allow-credentials="true">
    <allowed-origins>
        <!-- Localhost useful for development -->
        <origin>http://localhost:8080/</origin>
        <origin>http://example.com/</origin>
    </allowed-origins>
    <allowed-methods preflight-result-max-age="300">
        <method>GET</method>
        <method>POST</method>
        <method>PATCH</method>
        <method>DELETE</method>
    </allowed-methods>
    <allowed-headers>
        <!-- Examples below show Azure Mobile Services headers -->
        <header>x-zumo-installation-id</header>
        <header>x-zumo-application</header>
        <header>x-zumo-version</header>
        <header>x-zumo-auth</header>
        <header>content-type</header>
        <header>accept</header>
    </allowed-headers>
    <expose-headers>
        <!-- Examples below show Azure Mobile Services headers -->
        <header>x-zumo-installation-id</header>
        <header>x-zumo-application</header>
    </expose-headers>
</cors>
```

### <a name="elements"></a>Elementen

|Naam|Beschrijving|Vereist|Standaard|
|----------|-----------------|--------------|-------------|
|voorbereidende|Hoofd element.|Yes|N.v.t.|
|toegestaan-oorsprong|Bevat `origin` elementen die de toegestane oorsprongen voor meerdere domein aanvragen beschrijven. `allowed-origins` kan één `origin` element bevatten dat aangeeft dat een `*` wille keurige oorsprong of een of meer `origin` elementen die een URI bevatten, kunnen worden toegestaan.|Yes|N.v.t.|
|origin|De waarde kan ofwel `*` alle oorsprongen toestaan, ofwel een URI die één oorsprong opgeeft. De URI moet een schema, host en poort bevatten.|Yes|Als de poort wordt wegge laten in een URI, wordt poort 80 gebruikt voor HTTP en poort 443 wordt gebruikt voor HTTPS.|
|toegestane methoden|Dit element is vereist als andere methoden dan GET of POST zijn toegestaan. Bevat `method` elementen waarmee de ondersteunde HTTP-woorden worden opgegeven. De waarde `*` geeft alle methoden aan.|No|Als deze sectie niet aanwezig is, worden GET en POST ondersteund.|
|method|Hiermee geeft u een HTTP-woord op.|Er is mini maal één `method` element vereist als de `allowed-methods` sectie aanwezig is.|N.v.t.|
|toegestaan-headers|Dit element bevat `header` elementen die namen van de kopteksten opgeven die in de aanvraag kunnen worden opgenomen.|No|N.v.t.|
|weer geven-headers|Dit element bevat `header` elementen die namen van de headers opgeven die toegankelijk zijn voor de client.|No|N.v.t.|
|koptekst|Hiermee geeft u de naam van een header.|Ten minste één `header` element is vereist in `allowed-headers` of `expose-headers` als de sectie aanwezig is.|N.v.t.|

### <a name="attributes"></a>Kenmerken

|Naam|Beschrijving|Vereist|Standaard|
|----------|-----------------|--------------|-------------|
|toestaan-referenties|De `Access-Control-Allow-Credentials` header in het Preflight-antwoord wordt ingesteld op de waarde van dit kenmerk en is van invloed op de mogelijkheid van de client om referenties in meerdere domein aanvragen in te dienen.|No|onjuist|
|Terminate-Request niet-overeenkomend|Dit kenmerk bepaalt de verwerking van cross-Origin-aanvragen die niet overeenkomen met de CORS-beleids instellingen. Wanneer de OPTIONS-aanvraag wordt verwerkt als een aanvraag vóór de vlucht en niet overeenkomt met de instellingen voor het CORS-beleid: als het kenmerk is ingesteld op `true` , wordt de aanvraag onmiddellijk beëindigd met een leeg 200 OK-antwoord. Als het kenmerk is ingesteld op `false` , moet u inkomend controleren op een ander CORS-beleid in de scope dat directe onderliggende elementen van het inkomende element is en deze Toep assen.  Als er geen CORS-beleid wordt gevonden, beëindigt u de aanvraag met een leeg 200 OK-antwoord. Als GET-of HEAD-aanvraag de oorspronkelijke header bevat (en dus als een cross-Origin-aanvraag is verwerkt) en niet overeenkomt met de instellingen voor het CORS-beleid: als het kenmerk is ingesteld op `true` , wordt de aanvraag onmiddellijk beëindigd met een leeg 200 OK-antwoord. Als het kenmerk is ingesteld op `false` , laat u de aanvraag gewoon door gaan en voegt u geen CORS-headers toe aan het antwoord.|No|true|
|Preflight: resultaat-Max-Age|De `Access-Control-Max-Age` header in het Preflight-antwoord wordt ingesteld op de waarde van dit kenmerk en heeft invloed op de mogelijkheid van de gebruikers agent om een reactie in de cache op te slaan.|No|0|

### <a name="usage"></a>Gebruik
Dit beleid kan worden gebruikt in de volgende beleids [secties](./api-management-howto-policies.md#sections) en [bereiken](./api-management-howto-policies.md#scopes).

- **Beleids secties:** binnenkomend
- **Beleids bereik:** alle bereiken

## <a name="jsonp"></a><a name="JSONP"></a> JSONP
Het `jsonp` beleid voegt JSON met opvulling (Jsonp)-ondersteuning toe aan een bewerking of een API voor het toestaan van cross-domein aanroepen vanuit Java script-clients op basis van een browser. JSONP is een methode die wordt gebruikt in Java script-Program ma's om gegevens op te vragen van een server in een ander domein. JSONP omzeilt de beperking die wordt afgedwongen door de meeste webbrowsers, waarbij toegang tot webpagina's moet zich in hetzelfde domein bevallen.

### <a name="policy-statement"></a>Beleids verklaring

```xml
<jsonp callback-parameter-name="callback function name" />
```

### <a name="example"></a>Voorbeeld

```xml
<jsonp callback-parameter-name="cb" />
```

Als u de methode aanroept zonder de para meter call back? CB = XXX, wordt een ongewone JSON geretourneerd (zonder een functie aanroep wrapper).

Als u de call back-para meter toevoegt `?cb=XXX` , wordt een Jsonp resultaat geretourneerd, waarbij de oorspronkelijke JSON-resultaten rond de call back-functie, zoals `XYZ('<json result goes here>');`

### <a name="elements"></a>Elementen

|Naam|Beschrijving|Vereist|
|----------|-----------------|--------------|
|Jsonp|Hoofd element.|Yes|

### <a name="attributes"></a>Kenmerken

|Naam|Beschrijving|Vereist|Standaard|
|----------|-----------------|--------------|-------------|
|call back-para meter-name|De Java script-functie aanroep van meerdere domeinen, voorafgegaan door de Fully Qualified Domain Name waarbij de functie zich bevindt.|Yes|N.v.t.|

### <a name="usage"></a>Gebruik
Dit beleid kan worden gebruikt in de volgende beleids [secties](./api-management-howto-policies.md#sections) en [bereiken](./api-management-howto-policies.md#scopes).

- **Beleids secties:** uitgaand
- **Beleids bereik:** alle bereiken

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het gebruik van beleid:

+ [Beleid in API Management](api-management-howto-policies.md)
+ [Api's transformeren](transform-api.md)
+ [Beleids verwijzing](./api-management-policies.md) voor een volledige lijst met beleids instructies en hun instellingen
+ [Voorbeelden van beleid](./policy-reference.md)
