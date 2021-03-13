---
title: Cache beleid voor Azure API Management | Microsoft Docs
description: Meer informatie over de cache beleidsregels die beschikbaar zijn voor gebruik in azure API Management. Bekijk voor beelden en Bekijk extra beschik bare resources.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: article
ms.date: 03/08/2021
ms.author: apimpm
ms.openlocfilehash: 9888627bed0fbf90abc75c81564dacc0d1aac18e
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103233463"
---
# <a name="api-management-caching-policies"></a>API Management-cachebeleid
In dit onderwerp vindt u een verwijzing naar de volgende API Management-beleids regels. Zie [beleid in API Management](./api-management-policies.md)voor meer informatie over het toevoegen en configureren van beleid.

> [!IMPORTANT]
> De ingebouwde cache is vluchtig en wordt gedeeld door alle eenheden in dezelfde regio in dezelfde API Management-service.

## <a name="caching-policies"></a><a name="CachingPolicies"></a> Cache beleidsregels

- Beleid voor antwoord caching
    - [Ophalen uit cache](#GetFromCache) -cache uitvoeren en een geldig antwoord in de cache retour neren wanneer deze beschikbaar zijn.
    - [Opslaan in cache](#StoreToCache) -caches op basis van de opgegeven cache beheer configuratie.
- Beleid voor waarde in cache opslaan
    - [Waarde ophalen uit cache](#GetFromCacheByKey) -een item in de cache ophalen met behulp van sleutel.
    - [Waarde voor opslaan in cache](#StoreToCacheByKey) : een item in de cache opslaan per sleutel.
    - [Waarde uit cache verwijderen](#RemoveCacheByKey) : een item in de cache verwijderen per sleutel.

## <a name="get-from-cache"></a><a name="GetFromCache"></a> Ophalen uit cache
Gebruik het `cache-lookup` beleid voor het opzoeken van de cache en het retour neren van een geldig antwoord in de cache, indien beschikbaar. Dit beleid kan worden toegepast in gevallen waarin de inhoud van de reactie in een bepaalde periode statisch blijft. Antwoord caching vermindert de band breedte en de verwerkings vereisten op de back-end-webserver en verlaagt de latentie die wordt waargenomen door API-consumers.

> [!NOTE]
> Dit beleid moet een bijbehorend [Archief voor cache](#StoreToCache) beleid hebben.

### <a name="policy-statement"></a>Beleids verklaring

```xml
<cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" caching-type="prefer-external | external | internal" downstream-caching-type="none | private | public" must-revalidate="true | false" allow-private-response-caching="@(expression to evaluate)">
  <vary-by-header>Accept</vary-by-header>
  <!-- should be present in most cases -->
  <vary-by-header>Accept-Charset</vary-by-header>
  <!-- should be present in most cases -->
  <vary-by-header>Authorization</vary-by-header>
  <!-- should be present when allow-private-response-caching is "true"-->
  <vary-by-header>header name</vary-by-header>
  <!-- optional, can repeated several times -->
  <vary-by-query-parameter>parameter name</vary-by-query-parameter>
  <!-- optional, can repeated several times -->
</cache-lookup>
```

### <a name="examples"></a>Voorbeelden

#### <a name="example"></a>Voorbeeld

```xml
<policies>
    <inbound>
        <base />
        <cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="none" must-revalidate="true" caching-type="internal" >
            <vary-by-query-parameter>version</vary-by-query-parameter>
        </cache-lookup>
    </inbound>
    <outbound>
        <cache-store duration="seconds" />
        <base />
    </outbound>
</policies>
```

#### <a name="example-using-policy-expressions"></a>Voor beeld van beleids expressies
In dit voor beeld ziet u hoe u de tijds duur voor het opslaan van antwoorden in het cache geheugen van de back-end-service kunt API Management configureren, zoals opgegeven door de instructie van de back-upservice `Cache-Control` . Zie voor een demonstratie van het configureren en gebruiken van dit beleid [Cloud cover aflevering 177: meer API management functies met Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) en Fast-forward to 25:25.

```xml
<!-- The following cache policy snippets demonstrate how to control API Management response cache duration with Cache-Control headers sent by the backend service. -->

<!-- Copy this snippet into the inbound section -->
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >
  <vary-by-header>Accept</vary-by-header>
  <vary-by-header>Accept-Charset</vary-by-header>
</cache-lookup>

<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the default value of 5 min if none is found  -->
<cache-store duration="@{
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;
  }"
 />
```

Zie [beleids expressies](api-management-policy-expressions.md) en [context variabelen](api-management-policy-expressions.md#ContextVariables)voor meer informatie.

### <a name="elements"></a>Elementen

|Naam|Beschrijving|Vereist|
|----------|-----------------|--------------|
|cache-opzoeken|Hoofd element.|Ja|
|variëren per koptekst|Begin de caching van antwoorden per waarde van de opgegeven header, zoals accepteren, accepteren-charset, accepteren-coderen, accepteren-taal, autorisatie, verwachten van, host, if-match.|Nee|
|variëren per query-para meter|Begin de caching van antwoorden per waarde van de opgegeven query parameters. Voer één of meerdere para meters in. Gebruik een punt komma als scheidings teken. Als er geen is opgegeven, worden alle query parameters gebruikt.|Nee|

### <a name="attributes"></a>Kenmerken

| Naam                           | Beschrijving                                                                                                                                                                                                                                                                                                                                                 | Vereist | Standaard           |
|--------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| toestaan: private-Response-caching | Als deze instelling `true` is ingesteld op, staat het opslaan van aanvragen in de cache toe die een autorisatie-header bevatten.                                                                                                                                                                                                                                                                        | Nee       | onjuist             |
| cache-type               | U kunt kiezen uit de volgende waarden van het kenmerk:<br />- `internal` de ingebouwde API Management-cache gebruiken<br />- `external` Als u de externe cache wilt gebruiken zoals wordt beschreven in [een externe Azure-cache gebruiken voor redis in Azure API Management](api-management-howto-cache-external.md),<br />- `prefer-external` Als u een externe cache wilt gebruiken als deze is geconfigureerd of als de interne cache anders. | Nee       | `prefer-external` |
| downstream-caching-type        | Dit kenmerk moet worden ingesteld op een van de volgende waarden.<br /><br /> -geen-downstream-caching is niet toegestaan.<br />-Private-downstream-caching is toegestaan.<br />-openbaar-privé en gedeeld downstream-caching is toegestaan.                                                                                                          | Nee       | geen              |
| moet opnieuw valideren                | Wanneer downstream-caching is ingeschakeld, wordt met dit kenmerk de `must-revalidate` Cache-Control-instructie in de gateway-antwoorden in-of uitgeschakeld.                                                                                                                                                                                                                      | Nee       | true              |
| variëren per ontwikkelaar              | Instellen op `true` het opslaan van antwoorden per ontwikkelaars account dat eigenaar is van de [abonnements sleutel](./api-management-subscriptions.md) die in de aanvraag is opgenomen.                                                                                                                                                                                                                                                                                                  | Ja      |         Niet waar          |
| variëren per ontwikkelaar-groepen       | Instellen op `true` het opslaan van antwoorden per [gebruikers groep](./api-management-howto-create-groups.md).                                                                                                                                                                                                                                                                                                             | Ja      |       Niet waar            |

### <a name="usage"></a>Gebruik
Dit beleid kan worden gebruikt in de volgende beleids [secties](./api-management-howto-policies.md#sections) en [bereiken](./api-management-howto-policies.md#scopes).

- **Beleids secties:** binnenkomend
- **Beleids bereik:** alle bereiken

## <a name="store-to-cache"></a><a name="StoreToCache"></a> Opslaan in cache
Het `cache-store` beleid slaat antwoorden op volgens de opgegeven cache-instellingen. Dit beleid kan worden toegepast in gevallen waarin de inhoud van de reactie in een bepaalde periode statisch blijft. Antwoord caching vermindert de band breedte en de verwerkings vereisten op de back-end-webserver en verlaagt de latentie die wordt waargenomen door API-consumers.

> [!NOTE]
> Dit beleid moet een bijbehorende [Get van cache](api-management-caching-policies.md#GetFromCache) -beleid hebben.

### <a name="policy-statement"></a>Beleids verklaring

```xml
<cache-store duration="seconds" cache-response="true | false" />
```

### <a name="examples"></a>Voorbeelden

#### <a name="example"></a>Voorbeeld

```xml
<policies>
    <inbound>
        <base />
        <cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" downstream-caching-type="none | private | public" must-revalidate="true | false">
            <vary-by-query-parameter>parameter name</vary-by-query-parameter> <!-- optional, can repeated several times -->
        </cache-lookup>
    </inbound>
    <outbound>
        <base />
        <cache-store duration="3600" />
    </outbound>
</policies>
```

#### <a name="example-using-policy-expressions"></a>Voor beeld van beleids expressies
In dit voor beeld ziet u hoe u de tijds duur voor het opslaan van antwoorden in het cache geheugen van de back-end-service kunt API Management configureren, zoals opgegeven door de instructie van de back-upservice `Cache-Control` . Zie voor een demonstratie van het configureren en gebruiken van dit beleid [Cloud cover aflevering 177: meer API management functies met Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) en Fast-forward to 25:25.

```xml
<!-- The following cache policy snippets demonstrate how to control API Management response cache duration with Cache-Control headers sent by the backend service. -->

<!-- Copy this snippet into the inbound section -->
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >
  <vary-by-header>Accept</vary-by-header>
  <vary-by-header>Accept-Charset</vary-by-header>
</cache-lookup>

<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the default value of 5 min if none is found  -->
<cache-store duration="@{
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;
  }"
 />
```

Zie [beleids expressies](api-management-policy-expressions.md) en [context variabelen](api-management-policy-expressions.md#ContextVariables)voor meer informatie.

### <a name="elements"></a>Elementen

|Naam|Beschrijving|Vereist|
|----------|-----------------|--------------|
|cache-Store|Hoofd element.|Ja|

### <a name="attributes"></a>Kenmerken

| Naam             | Beschrijving                                                                                                                                                                                                                                                                                                                                                 | Vereist | Standaard           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| duur         | De TTL (time-to-Live) van de in de cache opgeslagen vermeldingen, opgegeven in seconden.     | Ja      | N.v.t.               |
| cache-antwoord         | Stel deze waarde in op True om het huidige HTTP-antwoord in de cache op te slaan. Als het kenmerk wordt wegge laten of op ONWAAR is ingesteld, worden alleen HTTP-antwoorden met de status code `200 OK` in de cache opgeslagen.                           | Nee      | onjuist               |

### <a name="usage"></a>Gebruik
Dit beleid kan worden gebruikt in de volgende beleids [secties](./api-management-howto-policies.md#sections) en [bereiken](./api-management-howto-policies.md#scopes).

- **Beleids secties:** uitgaand
- **Beleids bereik:** alle bereiken

## <a name="get-value-from-cache"></a><a name="GetFromCacheByKey"></a> Waarde uit cache ophalen
Gebruik het `cache-lookup-value` beleid om zoek acties in de cache uit te voeren op sleutel en een waarde in de cache te retour neren. De sleutel kan een wille keurige teken reeks waarde hebben en wordt doorgaans verschaft met een beleids expressie.

> [!NOTE]
> Dit beleid moet een overeenkomende [Store-waarde in het cache](#StoreToCacheByKey) beleid hebben.

### <a name="policy-statement"></a>Beleids verklaring

```xml
<cache-lookup-value key="cache key value"
    default-value="value to use if cache lookup resulted in a miss"
    variable-name="name of a variable looked up value is assigned to"
    caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>Voorbeeld
Zie [Custom caching in Azure API Management](./api-management-sample-cache-by-key.md)voor meer informatie en voor beelden van dit beleid.

```xml
<cache-lookup-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    variable-name="userprofile" />

```

### <a name="elements"></a>Elementen

|Naam|Beschrijving|Vereist|
|----------|-----------------|--------------|
|cache-lookup-waarde|Hoofd element.|Ja|

### <a name="attributes"></a>Kenmerken

| Naam             | Beschrijving                                                                                                                                                                                                                                                                                                                                                 | Vereist | Standaard           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| cache-type | U kunt kiezen uit de volgende waarden van het kenmerk:<br />- `internal` de ingebouwde API Management-cache gebruiken<br />- `external` Als u de externe cache wilt gebruiken zoals wordt beschreven in [een externe Azure-cache gebruiken voor redis in Azure API Management](api-management-howto-cache-external.md),<br />- `prefer-external` Als u een externe cache wilt gebruiken als deze is geconfigureerd of als de interne cache anders. | Nee       | `prefer-external` |
| standaard waarde    | Een waarde die wordt toegewezen aan de variabele als de zoek actie in de cache sleutel een Missing heeft veroorzaakt. Als dit kenmerk niet is opgegeven, `null` wordt toegewezen.                                                                                                                                                                                                           | Nee       | `null`            |
| sleutel              | De cache sleutel waarde die moet worden gebruikt in de zoek opdracht.                                                                                                                                                                                                                                                                                                                       | Ja      | N.v.t.               |
| variabele-naam    | De naam van de [context variabele](api-management-policy-expressions.md#ContextVariables) waaraan de opgezochte waarde wordt toegewezen als de zoek actie is geslaagd. Als de zoek actie resulteert in een Missing, wordt de waarde van het kenmerk toegewezen aan de variabele of wordt het `default-value` `null` `default-value` kenmerk wegge laten.                                       | Ja      | N.v.t.               |

### <a name="usage"></a>Gebruik
Dit beleid kan worden gebruikt in de volgende beleids [secties](./api-management-howto-policies.md#sections) en [bereiken](./api-management-howto-policies.md#scopes).

- **Beleids secties:** inkomend, uitgaand, back-end, op fout
- **Beleids bereik:** alle bereiken

## <a name="store-value-in-cache"></a><a name="StoreToCacheByKey"></a> Waarde voor opslaan in cache
Hiermee wordt de `cache-store-value` cache opslag per sleutel uitgevoerd. De sleutel kan een wille keurige teken reeks waarde hebben en wordt doorgaans verschaft met een beleids expressie.

> [!NOTE]
> De bewerking voor het opslaan van de waarde in de cache die door dit beleid wordt uitgevoerd, is asynchroon. De opgeslagen waarde kan worden opgehaald met de [Get-waarde van het cache](#GetFromCacheByKey) beleid. De opgeslagen waarde kan echter niet onmiddellijk beschikbaar zijn voor ophalen, omdat de asynchrone bewerking waarin de waarde in de cache wordt opgeslagen, nog steeds wordt uitgevoerd. 

### <a name="policy-statement"></a>Beleids verklaring

```xml
<cache-store-value key="cache key value" value="value to cache" duration="seconds" caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>Voorbeeld
Zie [Custom caching in Azure API Management](./api-management-sample-cache-by-key.md)voor meer informatie en voor beelden van dit beleid.

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />

```

### <a name="elements"></a>Elementen

|Naam|Beschrijving|Vereist|
|----------|-----------------|--------------|
|cache-Store-waarde|Hoofd element.|Ja|

### <a name="attributes"></a>Kenmerken

| Naam             | Beschrijving                                                                                                                                                                                                                                                                                                                                                 | Vereist | Standaard           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| cache-type | U kunt kiezen uit de volgende waarden van het kenmerk:<br />- `internal` de ingebouwde API Management-cache gebruiken<br />- `external` Als u de externe cache wilt gebruiken zoals wordt beschreven in [een externe Azure-cache gebruiken voor redis in Azure API Management](api-management-howto-cache-external.md),<br />- `prefer-external` Als u een externe cache wilt gebruiken als deze is geconfigureerd of als de interne cache anders. | Nee       | `prefer-external` |
| duur         | De waarde wordt in de cache opgeslagen voor de opgegeven duur waarde, opgegeven in seconden.                                                                                                                                                                                                                                                                                 | Ja      | N.v.t.               |
| sleutel              | Cache sleutel de waarde wordt opgeslagen onder.                                                                                                                                                                                                                                                                                                                   | Ja      | N.v.t.               |
| waarde            | De waarde die in de cache moet worden opgeslagen.                                                                                                                                                                                                                                                                                                                                     | Ja      | N.v.t.               |
### <a name="usage"></a>Gebruik
Dit beleid kan worden gebruikt in de volgende beleids [secties](./api-management-howto-policies.md#sections) en [bereiken](./api-management-howto-policies.md#scopes).

- **Beleids secties:** inkomend, uitgaand, back-end, op fout
- **Beleids bereik:** alle bereiken

## <a name="remove-value-from-cache"></a><a name="RemoveCacheByKey"></a> Waarde uit cache verwijderen
`cache-remove-value`Hiermee verwijdert u een item in de cache, geïdentificeerd door de sleutel. De sleutel kan een wille keurige teken reeks waarde hebben en wordt doorgaans verschaft met een beleids expressie.

#### <a name="policy-statement"></a>Beleids verklaring

```xml

<cache-remove-value key="cache key value" caching-type="prefer-external | external | internal"  />

```

#### <a name="example"></a>Voorbeeld

```xml

<cache-remove-value key="@("userprofile-" + context.Variables["enduserid"])"/>

```

#### <a name="elements"></a>Elementen

|Naam|Beschrijving|Vereist|
|----------|-----------------|--------------|
|cache-Remove-waarde|Hoofd element.|Ja|

#### <a name="attributes"></a>Kenmerken

| Naam             | Beschrijving                                                                                                                                                                                                                                                                                                                                                 | Vereist | Standaard           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| cache-type | U kunt kiezen uit de volgende waarden van het kenmerk:<br />- `internal` de ingebouwde API Management-cache gebruiken<br />- `external` Als u de externe cache wilt gebruiken zoals wordt beschreven in [een externe Azure-cache gebruiken voor redis in Azure API Management](api-management-howto-cache-external.md),<br />- `prefer-external` Als u een externe cache wilt gebruiken als deze is geconfigureerd of als de interne cache anders. | Nee       | `prefer-external` |
| sleutel              | De sleutel van de eerder in de cache opgeslagen waarde die uit de cache moet worden verwijderd.                                                                                                                                                                                                                                                                                        | Ja      | N.v.t.               |

#### <a name="usage"></a>Gebruik
Dit beleid kan worden gebruikt in de volgende beleids [secties](./api-management-howto-policies.md#sections) en [bereiken](./api-management-howto-policies.md#scopes) .

- **Beleids secties:** inkomend, uitgaand, back-end, op fout
- **Beleids bereik:** alle bereiken

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het gebruik van beleid:

+ [Beleid in API Management](api-management-howto-policies.md)
+ [Api's transformeren](transform-api.md)
+ [Beleids verwijzing](./api-management-policies.md) voor een volledige lijst met beleids instructies en hun instellingen
+ [Voorbeelden van beleid](./policy-reference.md)