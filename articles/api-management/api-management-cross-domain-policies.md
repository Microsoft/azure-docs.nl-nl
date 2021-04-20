---
title: Beleidsregels API Management meerdere domeinen van Azure | Microsoft Docs
description: Meer informatie over de beleidsregels voor meerdere domeinen die beschikbaar zijn voor gebruik in Azure API Management.
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
ms.openlocfilehash: 6f074ff389971fa56da7838a9a46ec5c4d42dc5a
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739094"
---
# <a name="api-management-cross-domain-policies"></a>API Management-beleid voor meerdere domeinen
In dit onderwerp vindt u een naslag voor de volgende API Management beleidsregels. Zie Beleidsregels in API Management voor meer informatie over het toevoegen [en configureren API Management.](./api-management-policies.md)

## <a name="cross-domain-policies"></a><a name="CrossDomainPolicies"></a> Beleid voor meerdere domeinen

- [Aanroepen tussen domeinen toestaan:](api-management-cross-domain-policies.md#AllowCrossDomainCalls) maakt de API toegankelijk via Adobe Flash- en Microsoft Silverlight-browsergebaseerde clients.
- [CORS:](api-management-cross-domain-policies.md#CORS) voegt ondersteuning voor cross-origin-resource sharing (CORS) toe aan een bewerking of api om aanroepen tussen domeinen van browsergebaseerde clients toe te staan.
- [JSONP: voegt](api-management-cross-domain-policies.md#JSONP) JSON met ondersteuning voor opvulling (JSONP) toe aan een bewerking of api om aanroepen tussen domeinen van clients in de JavaScript-browser toe te staan.

## <a name="allow-cross-domain-calls"></a><a name="AllowCrossDomainCalls"></a> Aanroepen tussen domeinen toestaan
Gebruik het `cross-domain` beleid om de API toegankelijk te maken vanuit Adobe Flash- en Microsoft Silverlight-browsergebaseerde clients.

### <a name="policy-statement"></a>Beleidsverklaring

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
|meerdere domeinen|Hoofdelement. Onderliggende elementen moeten voldoen aan de specificatie van het [adobe-beleid voor meerdere domeinen.](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html)|Yes|

### <a name="usage"></a>Gebruik
Dit beleid kan worden gebruikt in de volgende [beleidssecties](./api-management-howto-policies.md#sections) en [-scopes.](./api-management-howto-policies.md#scopes)

- **Beleidssecties:** inkomende
- **Beleidsbereiken:** alle scopes

## <a name="cors"></a><a name="CORS"></a> CORS
Het `cors` beleid voegt ondersteuning voor cross-origin-resource sharing (CORS) toe aan een bewerking of een API om aanroepen tussen domeinen van browsergebaseerde clients toe te staan. 

> [!NOTE]
> Als de aanvraag overeenkomt met een bewerking met een OPTIONS-methode die is gedefinieerd in de API, wordt de verwerkingslogica voor aanvraag vooraf die is gekoppeld aan CORS-beleid, niet uitgevoerd. Dergelijke bewerkingen kunnen daarom worden gebruikt voor het implementeren van aangepaste verwerkingslogica vóór de vlucht.

Met CORS kunnen een browser en een server communiceren en bepalen of specifieke cross-origin-aanvragen (dat wil zeggen XMLHttpRequests-aanroepen van JavaScript op een webpagina naar andere domeinen) zijn toegestaan. Dit biedt meer flexibiliteit dan alleen het toestaan van aanvragen van dezelfde oorsprong, maar is veiliger dan het toestaan van alle cross-origin-aanvragen.

U moet het CORS-beleid toepassen om de interactieve console in te kunnenschakelen in de ontwikkelaarsportal. Raadpleeg de documentatie [voor de ontwikkelaarsportal](./developer-portal-faq.md#cors) voor meer informatie.

### <a name="policy-statement"></a>Beleidsverklaring

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
In dit voorbeeld wordt gedemonstreerd hoe u aanvragen vóór de vlucht kunt ondersteunen, zoals aanvragen met aangepaste headers of andere methoden dan GET en POST. Als u aangepaste headers en aanvullende HTTP-woorden wilt ondersteunen, gebruikt u de `allowed-methods` secties en `allowed-headers` zoals weergegeven in het volgende voorbeeld.

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
|cors|Hoofdelement.|Yes|N.v.t.|
|toegestane oorsprongen|Bevat `origin` elementen die de toegestane oorsprongen voor aanvragen voor meerdere domeinen beschrijven. `allowed-origins` kan één element bevatten dat aangeeft dat elke oorsprong is toegestaan, of een of meer `origin` `*` elementen die een `origin` URI bevatten.|Yes|N.v.t.|
|origin|De waarde kan zijn om alle oorsprongen toe te staan, of een `*` URI die één oorsprong specificeert. De URI moet een schema, host en poort bevatten.|Yes|Als de poort wordt weggelaten in een URI, wordt poort 80 gebruikt voor HTTP en poort 443 voor HTTPS.|
|allowed-methods|Dit element is vereist als andere methoden dan GET of POST zijn toegestaan. Bevat `method` elementen die de ondersteunde HTTP-woorden opgeven. De waarde `*` geeft alle methoden aan.|No|Als deze sectie niet aanwezig is, worden GET en POST ondersteund.|
|method|Hiermee geeft u een HTTP-woord.|Er is ten `method` minste één element vereist als de sectie aanwezig `allowed-methods` is.|N.v.t.|
|allowed-headers|Dit element bevat `header` elementen die namen opgeven van de headers die in de aanvraag kunnen worden opgenomen.|No|N.v.t.|
|expose-headers|Dit element bevat `header` elementen die namen opgeven van de headers die toegankelijk zijn voor de client.|No|N.v.t.|
|koptekst|Hiermee geeft u een headernaam.|Er is ten `header` minste één element vereist in of als de sectie aanwezig `allowed-headers` `expose-headers` is.|N.v.t.|

### <a name="attributes"></a>Kenmerken

|Naam|Beschrijving|Vereist|Standaard|
|----------|-----------------|--------------|-------------|
|allow-credentials|De `Access-Control-Allow-Credentials` header in het preflight-antwoord wordt ingesteld op de waarde van dit kenmerk en is van invloed op de mogelijkheid van de client om referenties in te dienen in aanvragen voor meerdere domeinen.|No|onjuist|
|terminate-unmatched-request|Dit kenmerk bepaalt de verwerking van cross-origin-aanvragen die niet overeenkomen met de CORS-beleidsinstellingen. Wanneer de OPTIONS-aanvraag wordt verwerkt als een pre-flight-aanvraag en niet met CORS-beleidsinstellingen komt: als het kenmerk is ingesteld op , beëindigt u de aanvraag onmiddellijk met een leeg `true` 200 OK-antwoord; Als het kenmerk is ingesteld op , controleert u inkomende op andere CORS-beleidsregels binnen het bereik die directe kinderen van het inkomende element zijn en `false` passen ze toe.  Als er geen CORS-beleid wordt gevonden, beëindigt u de aanvraag met een leeg 200 OK-antwoord. Wanneer GET- of HEAD-aanvraag de Origin-header bevat (en daarom wordt verwerkt als een cross-origin-aanvraag) en niet met cors-beleidsinstellingen komt: als het kenmerk is ingesteld op , wordt de aanvraag onmiddellijk beëindigd met een leeg `true` 200 OK-antwoord; Als het kenmerk is ingesteld op , staat u toe dat de aanvraag normaal verloopt en `false` voegt u geen CORS-headers toe aan het antwoord.|No|true|
|preflight-result-max-age|De header in het preflight-antwoord wordt ingesteld op de waarde van dit kenmerk en is van invloed op de mogelijkheid van de gebruikersagent om `Access-Control-Max-Age` pre-flight-reacties in de cache op te nemen.|No|0|

### <a name="usage"></a>Gebruik
Dit beleid kan worden gebruikt in de volgende [beleidssecties](./api-management-howto-policies.md#sections) en [-scopes.](./api-management-howto-policies.md#scopes)

- **Beleidssecties:** inkomende
- **Beleidsbereiken:** alle scopes

## <a name="jsonp"></a><a name="JSONP"></a> JSONP
Met het beleid wordt JSON met ondersteuning voor opvulling (JSONP) toegevoegd aan een bewerking of een API om aanroepen tussen domeinen toe te staan vanaf clients in `jsonp` de JavaScript-browser. JSONP is een methode die wordt gebruikt in JavaScript-programma's om gegevens op te vragen bij een server in een ander domein. JSONP omzeilt de beperking die wordt afgedwongen door de meeste webbrowsers waarbij de toegang tot webpagina's in hetzelfde domein moet zijn.

### <a name="policy-statement"></a>Beleidsverklaring

```xml
<jsonp callback-parameter-name="callback function name" />
```

### <a name="example"></a>Voorbeeld

```xml
<jsonp callback-parameter-name="cb" />
```

Als u de methode aanroept zonder de callbackparameter ?cb=XXX, retourneert deze gewone JSON (zonder een wrapper voor functie-aanroepen).

Als u de callbackparameter toevoegt, retourneert deze een JSONP-resultaat, waardoor de oorspronkelijke JSON-resultaten rond de `?cb=XXX` callback-functie worden verpakt, zoals `XYZ('<json result goes here>');`

### <a name="elements"></a>Elementen

|Naam|Beschrijving|Vereist|
|----------|-----------------|--------------|
|jsonp|Hoofdelement.|Yes|

### <a name="attributes"></a>Kenmerken

|Naam|Beschrijving|Vereist|Standaard|
|----------|-----------------|--------------|-------------|
|callback-parameter-name|De JavaScript-functie voor meerdere domeinen roept het voorvoegsel aan met de volledig gekwalificeerde domeinnaam waarin de functie zich bevindt.|Yes|N.v.t.|

### <a name="usage"></a>Gebruik
Dit beleid kan worden gebruikt in de volgende [beleidssecties](./api-management-howto-policies.md#sections) en [-scopes.](./api-management-howto-policies.md#scopes)

- **Beleidssecties:** uitgaand
- **Beleidsbereiken:** alle scopes

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het werken met beleidsregels:

+ [Beleidsregels in API Management](api-management-howto-policies.md)
+ [API's transformeren](transform-api.md)
+ [Beleidsverwijzing](./api-management-policies.md) voor een volledige lijst met beleidsverklaringen en de instellingen
+ [Voorbeelden van beleid](./policy-reference.md)
