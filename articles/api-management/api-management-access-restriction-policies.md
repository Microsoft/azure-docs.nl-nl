---
title: Restrictie beleid voor Azure API Management-toegang | Microsoft Docs
description: Meer informatie over het toegangs beperkings beleid dat beschikbaar is voor gebruik in azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
ms.assetid: 034febe3-465f-4840-9fc6-c448ef520b0f
ms.service: api-management
ms.topic: article
ms.date: 02/26/2021
ms.author: apimpm
ms.openlocfilehash: 882d96271b6976db1ffc0dde181d5699c5cc27de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101688243"
---
# <a name="api-management-access-restriction-policies"></a>API Management access restriction policies (Beleid voor toegangsbeperking API Management)

In dit onderwerp vindt u een verwijzing naar de volgende API Management-beleids regels. Zie [beleid in API Management](./api-management-policies.md)voor meer informatie over het toevoegen en configureren van beleid.

## <a name="access-restriction-policies"></a><a name="AccessRestrictionPolicies"></a> Toegangs restrictie beleid

-   [Controleer de http-header](#CheckHTTPHeader) : Hiermee wordt het bestaan en/of de waarde van een http-header afgedwongen.
-   De [aanroepen per abonnement beperken](#LimitCallRate) : hiermee voor komt u dat het API-gebruik piekt door de oproep frequentie te beperken, op basis van een abonnement.
-   [Beperk de aanroepen per sleutel](#LimitCallRateByKey) : Hiermee wordt voor komen dat het API-gebruik pieken oploopt door de oproep frequentie per sleutel te beperken.
-   [Aanroeper Ip's beperken](#RestrictCallerIPs) : filters (toestaan/weigeren) aanroepen van specifieke IP-adressen en/of adresbereiken.
-   [Gebruiks quotum per abonnement instellen](#SetUsageQuota) : Hiermee kunt u een Verleng bare of levensduur oproep volume en/of bandbreedte quota afdwingen op basis van elk abonnement.
-   [Gebruiks quotum instellen op sleutel](#SetUsageQuotaByKey) : Hiermee kunt u een Verleng bare of levensduur oproep volume en/of bandbreedte quotum per sleutel afdwingen.
-   [Valideer JWT](#ValidateJWT) : afdwingt aanwezigheid en geldigheid van een JWT die is geëxtraheerd uit een opgegeven HTTP-header of een opgegeven query parameter.

> [!TIP]
> U kunt toegangs restrictie beleid in verschillende bereiken gebruiken voor verschillende doel einden. U kunt bijvoorbeeld de volledige API met AAD-verificatie beveiligen door het beleid toe te passen `validate-jwt` op het API-niveau, maar u kunt het ook Toep assen op het API-bewerkings niveau en gebruiken voor gedetailleerdere `claims` controle.

## <a name="check-http-header"></a><a name="CheckHTTPHeader"></a> HTTP-header controleren

Gebruik het `check-header` beleid om af te dwingen dat een aanvraag een opgegeven HTTP-header heeft. U kunt eventueel controleren of de header een specifieke waarde heeft of een bereik van toegestane waarden controleren. Als de controle mislukt, wordt de verwerking van aanvragen door het beleid beëindigd en worden de HTTP-status code en het fout bericht geretourneerd dat door het beleid is opgegeven.

### <a name="policy-statement"></a>Beleids verklaring

```xml
<check-header name="header name" failed-check-httpcode="code" failed-check-error-message="message" ignore-case="true">
    <value>Value1</value>
    <value>Value2</value>
</check-header>
```

### <a name="example"></a>Voorbeeld

```xml
<check-header name="Authorization" failed-check-httpcode="401" failed-check-error-message="Not authorized" ignore-case="false">
    <value>f6dc69a089844cf6b2019bae6d36fac8</value>
</check-header>
```

### <a name="elements"></a>Elementen

| Naam         | Beschrijving                                                                                                                                   | Vereist |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| check-header | Hoofd element.                                                                                                                                 | Yes      |
| waarde        | Toegestane waarde voor de HTTP-header. Als er meerdere waarde-elementen zijn opgegeven, wordt de controle als geslaagd beschouwd als een van de waarden een overeenkomst is. | No       |

### <a name="attributes"></a>Kenmerken

| Naam                       | Beschrijving                                                                                                                                                            | Vereist | Standaard |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| mislukt-controle-fout-bericht | Fout bericht dat moet worden geretourneerd in de tekst van het HTTP-antwoord als de header niet bestaat of een ongeldige waarde heeft. Dit bericht moet speciale tekens op de juiste manier hebben geescapet. | Yes      | N.v.t.     |
| mislukt-controle-httpcode      | HTTP-status code die moet worden geretourneerd als de koptekst niet bestaat of een ongeldige waarde heeft.                                                                                        | Yes      | N.v.t.     |
| header-naam                | De naam van de HTTP-header die moet worden gecontroleerd.                                                                                                                                  | Yes      | N.v.t.     |
| negeren-Case                | Kan worden ingesteld op True of false. Als deze eigenschap is ingesteld op True, wordt genegeerd wanneer de waarde van de header wordt vergeleken met de set acceptabele waarden.                                    | Yes      | N.v.t.     |

### <a name="usage"></a>Gebruik

Dit beleid kan worden gebruikt in de volgende beleids [secties](./api-management-howto-policies.md#sections) en [bereiken](./api-management-howto-policies.md#scopes).

-   **Beleids secties:** inkomend, uitgaand

-   **Beleids bereik:** alle bereiken

## <a name="limit-call-rate-by-subscription"></a><a name="LimitCallRate"></a> Oproep frequentie per abonnement beperken

Het `rate-limit` beleid voor komt dat het API-gebruik piekt per abonnement door de oproep frequentie te beperken tot een opgegeven aantal per opgegeven periode. Wanneer de aanroep frequentie wordt overschreden, ontvangt de aanroeper een `429 Too Many Requests` status code voor de reactie.

> [!IMPORTANT]
> Dit beleid kan slechts eenmaal per beleids document worden gebruikt.
>
> [Beleids expressies](api-management-policy-expressions.md) kunnen niet worden gebruikt in een van de beleids kenmerken voor dit beleid.

> [!CAUTION]
> Vanwege de gedistribueerde aard van beperkings architectuur is de frequentie beperking nooit volledig nauw keurig. Het verschil tussen de geconfigureerde en het werkelijke aantal toegestane aanvragen varieert op basis van het volume en de frequentie van de back-end, de backend-latentie en andere factoren.

> [!NOTE]
> [Zie frequentie limieten en quota's](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas) voor meer informatie over het verschil tussen frequentie limieten en quota's.

### <a name="policy-statement"></a>Beleids verklaring

```xml
<rate-limit calls="number" renewal-period="seconds">
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" 
        retry-after-header-name="header name" 
        retry-after-variable-name="policy expression variable name"
        remaining-calls-header-name="header name"  
        remaining-calls-variable-name="policy expression variable name"
        total-calls-header-name="header name"/>
    </api>
</rate-limit>
```

### <a name="example"></a>Voorbeeld

In het volgende voor beeld is de frequentie limiet per abonnement 20 aanroepen per 90 seconden. Na elke uitvoering van elk beleid, worden de resterende aanroepen die in de tijds periode zijn toegestaan, opgeslagen in de variabele `remainingCallsPerSubscription` .

```xml
<policies>
    <inbound>
        <base />
        <rate-limit calls="20" renewal-period="90" remaining-calls-variable-name="remainingCallsPerSubscription"/>
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Elementen

| Naam       | Beschrijving                                                                                                                                                                                                                                                                                              | Vereist |
| ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| frequentie limiet | Hoofd element.                                                                                                                                                                                                                                                                                            | Yes      |
| api        | Voeg een of meer van deze elementen toe om een aanroep frequentie limiet te leggen voor Api's binnen het product. De limieten voor product-en API-aanroepen worden onafhankelijk toegepast. Naar de API kan worden verwezen via `name` of `id` . Als beide kenmerken worden gegeven, `id` wordt gebruikt en `name` wordt deze genegeerd.                    | No       |
| bewerking  | Voeg een of meer van deze elementen toe om een aanroep frequentie limiet in te stellen voor bewerkingen binnen een API. Product-, API-en bewerkings frequentie limieten worden onafhankelijk toegepast. U kunt naar de bewerking verwijzen via `name` of `id` . Als beide kenmerken worden gegeven, `id` wordt gebruikt en `name` wordt deze genegeerd. | No       |

### <a name="attributes"></a>Kenmerken

| Naam           | Beschrijving                                                                                           | Vereist | Standaard |
| -------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| naam           | De naam van de API waarvoor de frequentie limiet moet worden toegepast.                                                | Yes      | N.v.t.     |
| rpc's          | Het maximale aantal aanroepen dat is toegestaan tijdens het opgegeven tijds interval `renewal-period` . | Yes      | N.v.t.     |
| verlenging-periode | De lengte in seconden van de sliding window waarvoor het aantal toegestane aanvragen de opgegeven waarde in moet overschrijden `calls` .                                              | Yes      | N.v.t.     |
| opnieuw proberen na-header-naam    | De naam van een antwoord header waarvan de waarde het aanbevolen interval voor nieuwe pogingen in seconden na het overschrijden van de opgegeven oproep frequentie is overschreden. |  No | N.v.t.  |
| opnieuw proberen na variabele-naam    | De naam van een beleid-expressie variabele waarin het aanbevolen interval voor nieuwe pogingen in seconden wordt opgeslagen nadat de opgegeven oproep frequentie is overschreden. |  No | N.v.t.  |
| resterend-aanroepen-header-name    | De naam van een antwoord header waarvan de waarde na elke uitvoering van elk beleid het aantal resterende aanroepen is dat is toegestaan voor het tijds interval dat is opgegeven in de `renewal-period` . |  No | N.v.t.  |
| resterend-aanroepen-variabele-name    | De naam van een beleids expressie-variabele die na elke uitvoering van elk beleid het aantal resterende aanroepen opslaat dat is toegestaan voor het tijds interval dat is opgegeven in de `renewal-period` . |  No | N.v.t.  |
| totaal-calls-header-name    | De naam van een antwoord header waarvan de waarde de waarde is die is opgegeven in `calls` . |  No | N.v.t.  |

### <a name="usage"></a>Gebruik

Dit beleid kan worden gebruikt in de volgende beleids [secties](./api-management-howto-policies.md#sections) en [bereiken](./api-management-howto-policies.md#scopes).

-   **Beleids secties:** binnenkomend

-   **Beleids bereiken:** product, API, bewerking

## <a name="limit-call-rate-by-key"></a><a name="LimitCallRateByKey"></a> De aanroep frequentie beperken op basis van de sleutel

> [!IMPORTANT]
> Deze functie is niet beschikbaar in de laag **verbruik** van API management.

Het `rate-limit-by-key` beleid voor komt dat het API-gebruik pieken per sleutel oploopt door de oproep frequentie te beperken tot een opgegeven getal per een opgegeven periode. De sleutel kan een wille keurige teken reeks waarde hebben en wordt doorgaans verschaft met een beleids expressie. Optionele increment condition kan worden toegevoegd om aan te geven welke aanvragen bij de limiet moeten worden geteld. Als deze aanroep frequentie wordt overschreden, ontvangt de aanroeper een `429 Too Many Requests` status code voor de reactie.

Zie [Geavanceerde aanvraag beperking met Azure API Management](./api-management-sample-flexible-throttling.md)voor meer informatie en voor beelden van dit beleid.

> [!CAUTION]
> Vanwege de gedistribueerde aard van beperkings architectuur is de frequentie beperking nooit volledig nauw keurig. Het verschil tussen de geconfigureerde en het werkelijke aantal toegestane aanvragen varieert op basis van het volume en de frequentie van de back-end, de backend-latentie en andere factoren.

> [!NOTE]
> [Zie frequentie limieten en quota's](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas) voor meer informatie over het verschil tussen frequentie limieten en quota's.

### <a name="policy-statement"></a>Beleids verklaring

```xml
<rate-limit-by-key calls="number"
                   renewal-period="seconds"
                   increment-condition="condition"
                   counter-key="key value" 
                   retry-after-header-name="header name" retry-after-variable-name="policy expression variable name"
                   remaining-calls-header-name="header name"  remaining-calls-variable-name="policy expression variable name"
                   total-calls-header-name="header name"/> 

```

### <a name="example"></a>Voorbeeld

In het volgende voor beeld wordt de frequentie limiet van 10 aanroepen per 60 seconden ingesteld op basis van het IP-adres van de beller. Na elke uitvoering van elk beleid, worden de resterende aanroepen die in de tijds periode zijn toegestaan, opgeslagen in de variabele `remainingCallsPerIP` .

```xml
<policies>
    <inbound>
        <base />
        <rate-limit-by-key  calls="10"
              renewal-period="60"
              increment-condition="@(context.Response.StatusCode == 200)"
              counter-key="@(context.Request.IpAddress)"
              remaining-calls-variable-name="remainingCallsPerIP"/>
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Elementen

| Naam              | Beschrijving   | Vereist |
| ----------------- | ------------- | -------- |
| frequentie per sleutel | Hoofd element. | Yes      |

### <a name="attributes"></a>Kenmerken

| Naam                | Beschrijving                                                                                           | Vereist | Standaard |
| ------------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| rpc's               | Het maximale aantal aanroepen dat is toegestaan tijdens het tijds interval dat is opgegeven in de `renewal-period` . | Yes      | N.v.t.     |
| teller-sleutel         | De sleutel die moet worden gebruikt voor het beleid voor frequentie limieten.                                                             | Yes      | N.v.t.     |
| Increment-condition | De booleaanse expressie waarmee wordt aangegeven of de aanvraag moet worden geteld bij het aantal ( `true` ).        | No       | N.v.t.     |
| verlenging-periode      | De lengte in seconden van de sliding window waarvoor het aantal toegestane aanvragen de opgegeven waarde in moet overschrijden `calls` .                                           | Yes      | N.v.t.     |
| opnieuw proberen na-header-naam    | De naam van een antwoord header waarvan de waarde het aanbevolen interval voor nieuwe pogingen in seconden na het overschrijden van de opgegeven oproep frequentie is overschreden. |  No | N.v.t.  |
| opnieuw proberen na variabele-naam    | De naam van een beleid-expressie variabele waarin het aanbevolen interval voor nieuwe pogingen in seconden wordt opgeslagen nadat de opgegeven oproep frequentie is overschreden. |  No | N.v.t.  |
| resterend-aanroepen-header-name    | De naam van een antwoord header waarvan de waarde na elke uitvoering van elk beleid het aantal resterende aanroepen is dat is toegestaan voor het tijds interval dat is opgegeven in de `renewal-period` . |  No | N.v.t.  |
| resterend-aanroepen-variabele-name    | De naam van een beleids expressie-variabele die na elke uitvoering van elk beleid het aantal resterende aanroepen opslaat dat is toegestaan voor het tijds interval dat is opgegeven in de `renewal-period` . |  No | N.v.t.  |
| totaal-calls-header-name    | De naam van een antwoord header waarvan de waarde de waarde is die is opgegeven in `calls` . |  No | N.v.t.  |

### <a name="usage"></a>Gebruik

Dit beleid kan worden gebruikt in de volgende beleids [secties](./api-management-howto-policies.md#sections) en [bereiken](./api-management-howto-policies.md#scopes).

-   **Beleids secties:** binnenkomend

-   **Beleids bereik:** alle bereiken

## <a name="restrict-caller-ips"></a><a name="RestrictCallerIPs"></a> IP-adressen van beller beperken

De `ip-filter` beleids filters (toestaan/weigeren) aanroepen van specifieke IP-adressen en/of adresbereiken.

### <a name="policy-statement"></a>Beleids verklaring

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address" />
</ip-filter>
```

### <a name="example"></a>Voorbeeld

In het volgende voor beeld staat het beleid alleen toe dat aanvragen afkomstig zijn van het opgegeven IP-adres of IP-bereik.

```xml
<ip-filter action="allow">
    <address>13.66.201.169</address>
    <address-range from="13.66.140.128" to="13.66.140.143" />
</ip-filter>
```

### <a name="elements"></a>Elementen

| Naam                                      | Beschrijving                                         | Vereist                                                       |
| ----------------------------------------- | --------------------------------------------------- | -------------------------------------------------------------- |
| IP-filter                                 | Hoofd element.                                       | Yes                                                            |
| adres                                   | Hiermee geeft u één IP-adres op waarop moet worden gefilterd.   | Er is ten minste één `address` `address-range` element vereist. |
| adres bereik van = "adres" naar = "adres" | Hiermee geeft u een bereik van IP-adres op waarop moet worden gefilterd. | Er is ten minste één `address` `address-range` element vereist. |

### <a name="attributes"></a>Kenmerken

| Naam                                      | Beschrijving                                                                                 | Vereist                                           | Standaard |
| ----------------------------------------- | ------------------------------------------------------------------------------------------- | -------------------------------------------------- | ------- |
| adres bereik van = "adres" naar = "adres" | Een bereik van IP-adressen voor het toestaan of weigeren van toegang voor.                                        | Vereist wanneer het `address-range` element wordt gebruikt. | N.v.t.     |
| IP-filter actie = "toestaan &#124; verbieden"    | Hiermee geeft u op of aanroepen moeten worden toegestaan of niet voor de opgegeven IP-adressen en-bereiken. | Yes                                                | N.v.t.     |

### <a name="usage"></a>Gebruik

Dit beleid kan worden gebruikt in de volgende beleids [secties](./api-management-howto-policies.md#sections) en [bereiken](./api-management-howto-policies.md#scopes).

-   **Beleids secties:** binnenkomend
-   **Beleids bereik:** alle bereiken

## <a name="set-usage-quota-by-subscription"></a><a name="SetUsageQuota"></a> Gebruiks quotum per abonnement instellen

`quota`Met het beleid wordt een Verleng volume en/of een levens duur van het gesprek en/of het quotum voor de band breedte afgedwongen op basis van elk abonnement.

> [!IMPORTANT]
> Dit beleid kan slechts eenmaal per beleids document worden gebruikt.
>
> [Beleids expressies](api-management-policy-expressions.md) kunnen niet worden gebruikt in een van de beleids kenmerken voor dit beleid.

> [!NOTE]
> [Zie frequentie limieten en quota's](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas) voor meer informatie over het verschil tussen frequentie limieten en quota's.

### <a name="policy-statement"></a>Beleids verklaring

```xml
<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" />
    </api>
</quota>
```

### <a name="example"></a>Voorbeeld

```xml
<policies>
    <inbound>
        <base />
        <quota calls="10000" bandwidth="40000" renewal-period="3600" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Elementen

| Naam      | Beschrijving                                                                                                                                                                                                                                                                                  | Vereist |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| quota     | Hoofd element.                                                                                                                                                                                                                                                                                | Yes      |
| api       | Voeg een of meer van deze elementen toe om het gespreks quotum voor Api's binnen het product in te stellen. Product-en API-oproep quota worden onafhankelijk toegepast. Naar de API kan worden verwezen via `name` of `id` . Als beide kenmerken worden gegeven, `id` wordt gebruikt en `name` wordt deze genegeerd.                    | No       |
| bewerking | Voeg een of meer van deze elementen toe om het aanroepen van quota voor bewerkingen in een API in te stellen. Product-, API-en bewerkings oproep quota worden onafhankelijk toegepast. U kunt naar de bewerking verwijzen via `name` of `id` . Als beide kenmerken worden gegeven, `id` wordt gebruikt en `name` wordt deze genegeerd. | No      |

### <a name="attributes"></a>Kenmerken

| Naam           | Beschrijving                                                                                               | Vereist                                                         | Standaard |
| -------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| naam           | De naam van de API of bewerking waarvoor het quotum geldt.                                             | Yes                                                              | N.v.t.     |
| BAP      | Het Maxi maal toegestane aantal kilo bytes tijdens het tijds interval dat is opgegeven in de `renewal-period` . | Ofwel `calls` , `bandwidth` of beide moeten worden opgegeven. | N.v.t.     |
| rpc's          | Het maximale aantal aanroepen dat is toegestaan tijdens het tijds interval dat is opgegeven in de `renewal-period` .     | Ofwel `calls` , `bandwidth` of beide moeten worden opgegeven. | N.v.t.     |
| verlenging-periode | De tijds periode in seconden waarna het quotum opnieuw wordt ingesteld. Wanneer deze is ingesteld op `0` de periode, wordt ingesteld op oneindig. | Yes                                                              | N.v.t.     |

### <a name="usage"></a>Gebruik

Dit beleid kan worden gebruikt in de volgende beleids [secties](./api-management-howto-policies.md#sections) en [bereiken](./api-management-howto-policies.md#scopes).

-   **Beleids secties:** binnenkomend
-   **Beleids bereiken:** product

## <a name="set-usage-quota-by-key"></a><a name="SetUsageQuotaByKey"></a> Gebruiks quotum instellen op sleutel

> [!IMPORTANT]
> Deze functie is niet beschikbaar in de laag **verbruik** van API management.

`quota-by-key`Met het beleid wordt een Verleng volume en/of een levens duur van het gesprek en/of het quotum voor de band breedte afgedwongen op basis van per sleutel. De sleutel kan een wille keurige teken reeks waarde hebben en wordt doorgaans verschaft met een beleids expressie. Optionele increment condition kan worden toegevoegd om aan te geven welke aanvragen bij het quotum moeten worden geteld. Als meerdere beleids regels dezelfde sleutel waarde zouden verhogen, wordt deze slechts eenmaal per aanvraag verhoogd. Wanneer de aanroep frequentie wordt overschreden, ontvangt de aanroeper een `403 Forbidden` status code voor de reactie.

Zie [Geavanceerde aanvraag beperking met Azure API Management](./api-management-sample-flexible-throttling.md)voor meer informatie en voor beelden van dit beleid.

> [!NOTE]
> [Zie frequentie limieten en quota's](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas) voor meer informatie over het verschil tussen frequentie limieten en quota's.

### <a name="policy-statement"></a>Beleids verklaring

```xml
<quota-by-key calls="number"
              bandwidth="kilobytes"
              renewal-period="seconds"
              increment-condition="condition"
              counter-key="key value" />

```

### <a name="example"></a>Voorbeeld

In het volgende voor beeld wordt het quotum door het IP-adres van de beller gesleuteld.

```xml
<policies>
    <inbound>
        <base />
        <quota-by-key calls="10000" bandwidth="40000" renewal-period="3600"
                      increment-condition="@(context.Response.StatusCode >= 200 && context.Response.StatusCode < 400)"
                      counter-key="@(context.Request.IpAddress)" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Elementen

| Naam  | Beschrijving   | Vereist |
| ----- | ------------- | -------- |
| quota | Hoofd element. | Yes      |

### <a name="attributes"></a>Kenmerken

| Naam                | Beschrijving                                                                                               | Vereist                                                         | Standaard |
| ------------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| BAP           | Het Maxi maal toegestane aantal kilo bytes tijdens het tijds interval dat is opgegeven in de `renewal-period` . | Ofwel `calls` , `bandwidth` of beide moeten worden opgegeven. | N.v.t.     |
| rpc's               | Het maximale aantal aanroepen dat is toegestaan tijdens het tijds interval dat is opgegeven in de `renewal-period` .     | Ofwel `calls` , `bandwidth` of beide moeten worden opgegeven. | N.v.t.     |
| teller-sleutel         | De sleutel die moet worden gebruikt voor het quotum beleid.                                                                      | Yes                                                              | N.v.t.     |
| Increment-condition | De booleaanse expressie waarmee wordt aangegeven of de aanvraag moet worden geteld bij het quotum ( `true` )             | No                                                               | N.v.t.     |
| verlenging-periode      | De tijds periode in seconden waarna het quotum opnieuw wordt ingesteld. Wanneer deze is ingesteld op `0` de periode, wordt ingesteld op oneindig.                                                   | Yes                                                              | N.v.t.     |

### <a name="usage"></a>Gebruik

Dit beleid kan worden gebruikt in de volgende beleids [secties](./api-management-howto-policies.md#sections) en [bereiken](./api-management-howto-policies.md#scopes).

-   **Beleids secties:** binnenkomend
-   **Beleids bereik:** alle bereiken

## <a name="validate-jwt"></a><a name="ValidateJWT"></a> JWT valideren

Het `validate-jwt` beleid afdwingt de aanwezigheid en de geldigheid van een JSON-webtoken (JWT) die is geëxtraheerd uit een opgegeven HTTP-header of een opgegeven query parameter.

> [!IMPORTANT]
> Het `validate-jwt` beleid vereist dat de `exp` geregistreerde claim is opgenomen in het JWT-token, tenzij het `require-expiration-time` kenmerk is opgegeven en is ingesteld op `false` .
> Het `validate-jwt` beleid ondersteunt HS256-en RS256-handtekening algoritmen. Voor HS256 moet de sleutel in de base64-gecodeerd formulier worden aangelegd in het beleid. Voor RS256 de sleutel kan worden opgegeven via een open ID-configuratie-eind punt of door de ID van een geüpload certificaat op te geven dat de open bare sleutel of het combi natie van de modulus-exponent van de open bare sleutel bevat.
> Het `validate-jwt` beleid ondersteunt tokens die zijn versleuteld met symmetrische sleutels met behulp van de volgende versleutelings algoritmen: A128CBC-HS256, A192CBC-HS384, A256CBC-HS512.

### <a name="policy-statement"></a>Beleids verklaring

```xml
<validate-jwt
    header-name="name of http header containing the token (use query-parameter-name attribute if the token is passed in the URL)"
    failed-validation-httpcode="http status code to return on failure"
    failed-validation-error-message="error message to return on failure"
    token-value="expression returning JWT token as a string"
    require-expiration-time="true|false"
    require-scheme="scheme"
    require-signed-tokens="true|false"
    clock-skew="allowed clock skew in seconds"
    output-token-variable-name="name of a variable to receive a JWT object representing successfully validated token">
  <openid-config url="full URL of the configuration endpoint, e.g. https://login.constoso.com/openid-configuration" />
  <issuer-signing-keys>
    <key>base64 encoded signing key</key>
    <!-- if there are multiple keys, then add additional key elements -->
  </issuer-signing-keys>
  <decryption-keys>
    <key>base64 encoded signing key</key>
    <!-- if there are multiple keys, then add additional key elements -->
  </decryption-keys>
  <audiences>
    <audience>audience string</audience>
    <!-- if there are multiple possible audiences, then add additional audience elements -->
  </audiences>
  <issuers>
    <issuer>issuer string</issuer>
    <!-- if there are multiple possible issuers, then add additional issuer elements -->
  </issuers>
  <required-claims>
    <claim name="name of the claim as it appears in the token" match="all|any" separator="separator character in a multi-valued claim">
      <value>claim value as it is expected to appear in the token</value>
      <!-- if there is more than one allowed values, then add additional value elements -->
    </claim>
    <!-- if there are multiple possible allowed values, then add additional value elements -->
  </required-claims>
</validate-jwt>

```

### <a name="examples"></a>Voorbeelden

#### <a name="simple-token-validation"></a>Eenvoudige token validatie

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer">
    <issuer-signing-keys>
        <key>{{jwt-signing-key}}</key>  <!-- signing key specified as a named value -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>  <!-- audience is set to API Management host name -->
    </audiences>
    <issuers>
        <issuer>http://contoso.com/</issuer>
    </issuers>
</validate-jwt>
```

#### <a name="token-validation-with-rsa-certificate"></a>Token validatie met RSA-certificaat

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer">
    <issuer-signing-keys>
        <key certificate-id="my-rsa-cert" />  <!-- signing key specified as certificate ID, enclosed in double-quotes -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>  <!-- audience is set to API Management host name -->
    </audiences>
    <issuers>
        <issuer>http://contoso.com/</issuer>
    </issuers>
</validate-jwt>
```

#### <a name="azure-active-directory-token-validation"></a>Validatie van Azure Active Directory-token

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration" />
    <audiences>
        <audience>25eef6e4-c905-4a07-8eb4-0d08d5df8b3f</audience>
    </audiences>
    <required-claims>
        <claim name="id" match="all">
            <value>insert claim here</value>
        </claim>
    </required-claims>
</validate-jwt>
```

#### <a name="azure-active-directory-b2c-token-validation"></a>Validatie van Azure Active Directory B2C-token

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/b2c_1_signin/v2.0/.well-known/openid-configuration" />
    <audiences>
        <audience>d313c4e4-de5f-4197-9470-e509a2f0b806</audience>
    </audiences>
    <required-claims>
        <claim name="id" match="all">
            <value>insert claim here</value>
        </claim>
    </required-claims>
</validate-jwt>
```

#### <a name="authorize-access-to-operations-based-on-token-claims"></a>Toegang tot bewerkingen op basis van token claims autoriseren

In dit voor beeld ziet u hoe u het JWT-beleid [valideren](api-management-access-restriction-policies.md#ValidateJWT) gebruikt om toegang te verlenen tot bewerkingen op basis van een token claim waarde.

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer" output-token-variable-name="jwt">
    <issuer-signing-keys>
        <key>{{jwt-signing-key}}</key> <!-- signing key is stored in a named value -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>
    </audiences>
    <issuers>
        <issuer>contoso.com</issuer>
    </issuers>
    <required-claims>
        <claim name="group" match="any">
            <value>finance</value>
            <value>logistics</value>
        </claim>
    </required-claims>
</validate-jwt>
<choose>
    <when condition="@(context.Request.Method == "POST" && !((Jwt)context.Variables["jwt"]).Claims["group"].Contains("finance"))">
        <return-response>
            <set-status code="403" reason="Forbidden" />
        </return-response>
    </when>
</choose>
```

### <a name="elements"></a>Elementen

| Element             | Beschrijving                                                                                                                                                                                                                                                                                                                                           | Vereist |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validate-JWT        | Hoofd element.                                                                                                                                                                                                                                                                                                                                         | Yes      |
| doel groepen           | Bevat een lijst met acceptabele claim claims die aanwezig kunnen zijn op het token. Als er meerdere Audience-waarden aanwezig zijn, wordt elke waarde geprobeerd totdat alle gegevens zijn uitgeput (in welk geval de validatie mislukt) of totdat er een slaagt. Er moet ten minste één doel groep worden opgegeven.                                                                     | No       |
| verlener-handtekening sleutels | Een lijst met met base64 gecodeerde beveiligings sleutels die worden gebruikt voor het valideren van ondertekende tokens. Als er meerdere beveiligings sleutels aanwezig zijn, wordt elke sleutel geprobeerd totdat alle sleutels zijn uitgeput (in het geval dat de validatie mislukt) of een geslaagd (handig voor de rollover van tokens). Sleutel elementen hebben een optioneel `id` kenmerk dat wordt gebruikt om te matchen op `kid` claim. <br/><br/>U kunt ook een handtekening sleutel voor de verlener opgeven met:<br/><br/> - `certificate-id` in indeling `<key certificate-id="mycertificate" />` om de id op te geven van een certificaat entiteit die is [geüpload](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity#Add) naar API Management<br/>-Combi natie van RSA-modulus `n` en exponent `e` in indeling `<key n="<modulus>" e="<exponent>" />` om de RSA-para meters op te geven in een met base64url gecodeerde indeling               | No       |
| ontsleuteling-sleutels     | Een lijst met met base64 gecodeerde sleutels die worden gebruikt om de tokens te ontsleutelen. Als er meerdere beveiligings sleutels aanwezig zijn, wordt elke sleutel geprobeerd totdat alle sleutels zijn uitgeput (in het geval dat de validatie mislukt) of een sleutel slaagt. Sleutel elementen hebben een optioneel `id` kenmerk dat wordt gebruikt om te matchen op `kid` claim.<br/><br/>U kunt ook een ontsleutelings sleutel opgeven met:<br/><br/> - `certificate-id` in indeling `<key certificate-id="mycertificate" />` om de id op te geven van een certificaat entiteit die is [geüpload](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity#Add) naar API Management                                                 | No       |
| verleners             | Een lijst met acceptabele principals die het token hebben uitgegeven. Als er meerdere Issuer-waarden aanwezig zijn, wordt elke waarde geprobeerd totdat alle gegevens zijn uitgeput (in het geval dat de validatie mislukt) of totdat er een slaagt.                                                                                                                                         | No       |
| OpenID Connect-config       | Het element dat wordt gebruikt voor het opgeven van een compatibel Open ID-configuratie-eind punt waarvan de handtekening sleutels en de certificaat verlener kunnen worden verkregen.                                                                                                                                                                                                                        | No       |
| vereist: claims     | Bevat een lijst met claims die naar verwachting aanwezig zijn op het token om als geldig te worden beschouwd. Wanneer het `match` kenmerk is ingesteld op `all` elke claim waarde in het beleid, moet aanwezig zijn in het token om de validatie te volt ooien. Wanneer het `match` kenmerk is ingesteld op `any` ten minste één claim moet aanwezig zijn in het token om de validatie te volt ooien. | No       |

### <a name="attributes"></a>Kenmerken

| Naam                            | Beschrijving                                                                                                                                                                                                                                                                                                                                                                                                                                            | Vereist                                                                         | Standaard                                                                           |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| klok scheef trekken                      | Duur. Gebruik dit om het maximale verwachte tijd verschil tussen de systeem klokken van de token Uitgever en het API Management-exemplaar op te geven.                                                                                                                                                                                                                                                                                                               | No                                                                               | 0 seconden                                                                         |
| mislukt-validatie-fout-bericht | Fout bericht dat moet worden geretourneerd in de hoofd tekst van het HTTP-antwoord als de JWT geen validatie doorgeeft. Dit bericht moet speciale tekens op de juiste manier hebben geescapet.                                                                                                                                                                                                                                                                                                 | No                                                                               | Het standaard fout bericht is afhankelijk van het validatie probleem, bijvoorbeeld ' JWT niet aanwezig '. |
| mislukt: validatie-httpcode      | HTTP-status code die moet worden geretourneerd als de JWT-validatie niet is geslaagd.                                                                                                                                                                                                                                                                                                                                                                                         | No                                                                               | 401                                                                               |
| header-naam                     | De naam van de HTTP-header die het token heeft.                                                                                                                                                                                                                                                                                                                                                                                                         | Een van `header-name` `query-parameter-name` of `token-value` moet worden opgegeven. | N.v.t.                                                                               |
| query-para meter-naam            | De naam van de query parameter die het token bewaart.                                                                                                                                                                                                                                                                                                                                                                                                     | Een van `header-name` `query-parameter-name` of `token-value` moet worden opgegeven. | N.v.t.                                                                               |
| token-waarde                     | Expressie die een teken reeks met een JWT-token retourneert                                                                                                                                                                                                                                                                                                                                                                                                     | Een van `header-name` `query-parameter-name` of `token-value` moet worden opgegeven. | N.v.t.                                                                               |
| id                              | Met het `id` kenmerk van het `key` element kunt u de teken reeks opgeven die overeenkomt met de `kid` claim in het token (indien aanwezig) om de juiste sleutel te vinden die moet worden gebruikt voor handtekening validatie.                                                                                                                                                                                                                                           | No                                                                               | N.v.t.                                                                               |
| overeen met                           | Het `match` kenmerk van het `claim` element geeft aan of elke claim waarde in het beleid aanwezig moet zijn in het token om de validatie te kunnen volt ooien. Mogelijke waarden zijn:<br /><br /> - `all` -elke claim waarde in het beleid moet aanwezig zijn in het token om de validatie te volt ooien.<br /><br /> - `any` -Er moet ten minste één claim waarde aanwezig zijn in het token om de validatie te volt ooien.                                                       | No                                                                               | all                                                                               |
| vereisen-verval tijd         | True. Hiermee geeft u op of er een verval claim vereist is in het token.                                                                                                                                                                                                                                                                                                                                                                               | No                                                                               | true                                                                              |
| vereisen-schema                  | De naam van het token schema, bijvoorbeeld ' Bearer '. Als dit kenmerk is ingesteld, zorgt het beleid ervoor dat het opgegeven schema aanwezig is in de waarde van de autorisatie-header.                                                                                                                                                                                                                                                                                    | No                                                                               | N.v.t.                                                                               |
| vereisen: ondertekende tokens           | True. Hiermee geeft u op of een token moet worden ondertekend.                                                                                                                                                                                                                                                                                                                                                                                           | No                                                                               | true                                                                              |
| scheiding                       | Tekenreeks. Hiermee geeft u een scheidings teken (bijvoorbeeld ",") op dat moet worden gebruikt voor het extra heren van een set waarden uit een claim met meerdere waarden.                                                                                                                                                                                                                                                                                                                                          | No                                                                               | N.v.t.                                                                               |
| url                             | Open ID configuratie eind punt URL van waar de meta gegevens van de configuratie van de Open-ID kunnen worden verkregen. Het antwoord moet overeenkomen met de specificaties zoals gedefinieerd op URL: `https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata` . Voor Azure Active Directory gebruikt u de volgende URL: de `https://login.microsoftonline.com/{tenant-name}/.well-known/openid-configuration` naam van uw adreslijst Tenant vervangen, bijvoorbeeld `contoso.onmicrosoft.com` . | Yes                                                                              | N.v.t.                                                                               |
| uitvoer-token-variabele-naam      | Tekenreeks. De naam van de context variabele waarmee de token waarde wordt ontvangen als een object van het type [`Jwt`](api-management-policy-expressions.md) bij een geslaagde token validatie                                                                                                                                                                                                                                                                                     | No                                                                               | N.v.t.                                                                               |

### <a name="usage"></a>Gebruik

Dit beleid kan worden gebruikt in de volgende beleids [secties](./api-management-howto-policies.md#sections) en [bereiken](./api-management-howto-policies.md#scopes).

-   **Beleids secties:** binnenkomend
-   **Beleids bereik:** alle bereiken

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het gebruik van beleid:

-   [Beleid in API Management](api-management-howto-policies.md)
-   [Api's transformeren](transform-api.md)
-   [Beleids verwijzing](./api-management-policies.md) voor een volledige lijst met beleids instructies en hun instellingen
-   [Voorbeelden van beleid](./policy-reference.md)
