---
title: Acties voor de standaard/Premium-regelset van Azure front deur configureren
description: Dit artikel bevat een overzicht van de verschillende acties die u kunt uitvoeren met de Azure front deur-regelset.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: yuajia
ms.openlocfilehash: e4698a1c1576d15042dd050e0123b83dba39a3e3
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106064748"
---
# <a name="azure-front-door-standardpremium-preview-rule-set-actions"></a>Acties voor het instellen van regels voor Azure front-deur Standard/Premium (preview)

> [!Note]
> Deze documentatie is voor Azure front deur Standard/Premium (preview). Zoekt u informatie over de voor deur van Azure? [Hier](../front-door-overview.md)weer geven.

Een Azure front deur Standard/Premium- [regel reeks](concept-rule-set.md) bestaat uit regels met een combi natie van match voorwaarden en acties. Dit artikel bevat een gedetailleerde beschrijving van de acties die u kunt gebruiken in de Azure front deur Standard/Premium-regelset. De actie definieert het gedrag dat wordt toegepast op een aanvraag type dat overeenkomt met een of meer voor waarden. In een regelset voor een Azure front-deur (Standard/Premium) kan een regel Maxi maal vijf acties bevatten.

> [!IMPORTANT]
> Azure front deur Standard/Premium (preview) is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

De volgende acties zijn beschikbaar voor gebruik in de Azure front-deur regel reeks.  

## <a name="cache-expiration"></a><a name="CacheExpiration"></a> Verval datum van cache

Gebruik de actie **verloop tijd cache** om de TTL-waarde (time to Live) van het eind punt te overschrijven voor aanvragen die voldoen aan de regels voor waarden.

> [!NOTE]
> Oorsprongen kunnen opgeven dat geen specifieke antwoorden in de cache moeten worden opgeslagen met de `Cache-Control` header met de waarde `no-cache` , `private` of `no-store` . In deze omstandigheden wordt de inhoud nooit in de cache opgeslagen en deze actie heeft geen effect.

### <a name="properties"></a>Eigenschappen

| Eigenschap | Ondersteunde waarden |
|-------|------------------|
| Cachegedrag | <ul><li>**Cache overs Laan:** De inhoud mag niet in de cache worden opgeslagen. Stel in ARM-sjablonen de `cacheBehavior` eigenschap in op `BypassCache` .</li><li>**Overschrijven:** De TTL-waarde die wordt geretourneerd door uw oorsprong, wordt overschreven door de waarde die is opgegeven in de actie. Dit gedrag wordt alleen toegepast als het antwoord in de cache kan worden opgeslagen. Stel in ARM-sjablonen de `cacheBehavior` eigenschap in op `Override` .</li><li>**Instellen indien ontbrekend:** Als er geen TTL-waarde van uw oorsprong wordt geretourneerd, stelt de regel de TTL in op de waarde die is opgegeven in de actie. Dit gedrag wordt alleen toegepast als het antwoord in de cache kan worden opgeslagen. Stel in ARM-sjablonen de `cacheBehavior` eigenschap in op `SetIfMissing` .</li></ul> |
| Cacheduur | Wanneer het _cache gedrag_ is ingesteld op `Override` of `Set if missing` , moeten in deze velden de cache duur worden opgegeven die moet worden gebruikt. De maximale duur is 366 dagen.<ul><li>In de Azure Portal: Geef de dagen, uren, minuten en seconden op.</li><li>In ARM-sjablonen: Geef de duur op in de notatie `d.hh:mm:ss` . |

### <a name="example"></a>Voorbeeld

In dit voor beeld overschrijven we de verval datum van de cache tot 6 uur, voor overeenkomende aanvragen waarbij geen cache duur al is opgegeven.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/cache-expiration.png" alt-text="Scherm opname van de portal toont de verval actie van de cache.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "CacheExpiration",
  "parameters": {
    "cacheBehavior": "SetIfMissing",
    "cacheType": "All",
    "cacheDuration": "0.06:00:00",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheExpirationActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'CacheExpiration'
  parameters: {
    cacheBehavior: 'SetIfMissing'
    cacheType: All
    cacheDuration: '0.06:00:00'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheExpirationActionParameters'
  }
}
```

---

## <a name="cache-key-query-string"></a><a name="CacheKeyQueryString"></a> Query reeks voor de cache sleutel

Gebruik de **query teken reeks actie cache sleutel** om de cache sleutel te wijzigen op basis van query teken reeksen. De cache sleutel is de manier waarop met de voor deur unieke aanvragen worden geïdentificeerd voor de cache.

### <a name="properties"></a>Eigenschappen

| Eigenschap | Ondersteunde waarden |
|-------|------------------|
| Gedrag | <ul><li>**Omvatten:** Query reeksen die zijn opgegeven in de para meters, worden opgenomen als de cache sleutel wordt gegenereerd. Stel in ARM-sjablonen de `queryStringBehavior` eigenschap in op `Include` .</li><li>**Elke unieke URL in de cache opslaan:** Elke unieke URL heeft een eigen cache sleutel. In ARM-sjablonen gebruikt u de `queryStringBehavior` van `IncludeAll` .</li><li>**Uitsluiten:** Query reeksen die zijn opgegeven in de para meters, worden uitgesloten wanneer de cache sleutel wordt gegenereerd. Stel in ARM-sjablonen de `queryStringBehavior` eigenschap in op `Exclude` .</li><li>**Query reeksen negeren:** Query reeksen worden niet meegenomen wanneer de cache sleutel wordt gegenereerd. Stel in ARM-sjablonen de `queryStringBehavior` eigenschap in op `ExcludeAll` .</li></ul>  |
| Parameters | De lijst met parameter namen voor de query reeks, gescheiden door komma's. |

### <a name="example"></a>Voorbeeld

In dit voor beeld wijzigen we de cache sleutel zodat deze een query teken reeks parameter bevat met de naam `customerId` .

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/cache-key-query-string.png" alt-text="Scherm opname van de portal met de query teken reeks actie cache sleutel.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "CacheKeyQueryString",
  "parameters": {
    "queryStringBehavior": "Include",
    "queryParameters": "customerId",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheKeyQueryStringBehaviorActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'CacheKeyQueryString'
  parameters: {
    queryStringBehavior: 'Include'
    queryParameters: 'customerId'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheKeyQueryStringBehaviorActionParameters'
  }
}
```

---

## <a name="modify-request-header"></a><a name="ModifyRequestHeader"></a> Aanvraag header wijzigen

Gebruik de actie **aanvraag header wijzigen** om de kopteksten in de aanvraag te wijzigen wanneer deze naar uw oorsprong wordt verzonden.

### <a name="properties"></a>Eigenschappen

| Eigenschap | Ondersteunde waarden |
|-------|------------------|
| Operator | <ul><li>**Toevoegen:** De opgegeven header wordt toegevoegd aan de aanvraag met de opgegeven waarde. Als de header al aanwezig is, wordt de waarde toegevoegd aan de bestaande header waarde met behulp van teken reeksen samen voegen. Er worden geen scheidings tekens toegevoegd. In ARM-sjablonen gebruikt u de `headerAction` van `Append` .</li><li>**Overschrijven:** De opgegeven header wordt toegevoegd aan de aanvraag met de opgegeven waarde. Als er al een header aanwezig is, overschrijft de opgegeven waarde de bestaande waarde. In ARM-sjablonen gebruikt u de `headerAction` van `Overwrite` .</li><li>**Verwijderen:** Als de in de regel opgegeven header aanwezig is, wordt de header uit de aanvraag verwijderd. In ARM-sjablonen gebruikt u de `headerAction` van `Delete` .</li></ul> |
| Headernaam | De naam van de koptekst die moet worden gewijzigd. |
| Headerwaarde | De waarde die moet worden toegevoegd of overschreven. |

### <a name="example"></a>Voorbeeld

In dit voor beeld voegen we de waarde `AdditionalValue` toe aan de `MyRequestHeader` aanvraag header. Als de oorsprong de antwoord header instelt op een waarde van `ValueSetByClient` , wordt nadat deze actie is toegepast, de aanvraag header de waarde `ValueSetByClientAdditionalValue` .

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/modify-request-header.png" alt-text="Scherm opname van de portal met de actie aanvraag header wijzigen.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "ModifyRequestHeader",
  "parameters": {
    "headerAction": "Append",
    "headerName": "MyRequestHeader",
    "value": "AdditionalValue",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'ModifyRequestHeader'
  parameters: {
    headerAction: 'Append'
    headerName: 'MyRequestHeader'
    value: 'AdditionalValue'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters'
  }
}
```

---

## <a name="modify-response-header"></a><a name="ModifyResponseHeader"></a> Antwoord header wijzigen

Gebruik de actie **antwoord header wijzigen** om kopteksten te wijzigen die aanwezig zijn in antwoorden voordat ze worden geretourneerd naar uw clients.

### <a name="properties"></a>Eigenschappen

| Eigenschap | Ondersteunde waarden |
|-------|------------------|
| Operator | <ul><li>**Toevoegen:** De opgegeven header wordt toegevoegd aan het antwoord met de opgegeven waarde. Als de header al aanwezig is, wordt de waarde toegevoegd aan de bestaande header waarde met behulp van teken reeksen samen voegen. Er worden geen scheidings tekens toegevoegd. In ARM-sjablonen gebruikt u de `headerAction` van `Append` .</li><li>**Overschrijven:** De opgegeven header wordt toegevoegd aan het antwoord met de opgegeven waarde. Als er al een header aanwezig is, overschrijft de opgegeven waarde de bestaande waarde. In ARM-sjablonen gebruikt u de `headerAction` van `Overwrite` .</li><li>**Verwijderen:** Als de in de regel opgegeven header aanwezig is, wordt de koptekst uit het antwoord verwijderd.  In ARM-sjablonen gebruikt u de `headerAction` van `Delete` .</li></ul> |
| Headernaam | De naam van de koptekst die moet worden gewijzigd. |
| Headerwaarde | De waarde die moet worden toegevoegd of overschreven. |

### <a name="example"></a>Voorbeeld

In dit voor beeld wordt de header met de naam `X-Powered-By` van de antwoorden verwijderd voordat ze worden geretourneerd naar de client.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/modify-response-header.png" alt-text="Scherm opname van de portal met actie voor het wijzigen van de reactie header.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "ModifyResponseHeader",
  "parameters": {
    "headerAction": "Delete",
    "headerName": "X-Powered-By",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'ModifyResponseHeader'
  parameters: {
    headerAction: 'Delete'
    headerName: 'X-Powered-By'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters'
  }
}
```

---

## <a name="url-redirect"></a><a name="UrlRedirect"></a> URL-omleiding

Gebruik de actie **URL-omleiding** om clients om te leiden naar een nieuwe URL. Clients ontvangen een omleidings reactie van de voor deur.

### <a name="properties"></a>Eigenschappen

| Eigenschap | Ondersteunde waarden |
|----------|------------------|
| Type omleiding | Het antwoord type dat moet worden geretourneerd naar de aanvrager. <ul><li>In de Azure Portal: gevonden (302), verplaatst (301), tijdelijke omleiding (307), permanente omleiding (308).</li><li>In ARM-sjablonen: `Found` , `Moved` , `TemporaryRedirect` , `PermanentRedirect`</li></ul> |
| Omleidingsprotocol | <ul><li>In de Azure Portal: `Match Request` , `HTTP` , `HTTPS`</li><li>In ARM-sjablonen: `MatchRequest` , `Http` , `Https`</li></ul> |
| Doelhost | De naam van de host waarnaar u de aanvraag wilt omleiden. Laat het veld leeg om de binnenkomende host te behouden. |
| Doelpad | Het pad dat in de omleiding moet worden gebruikt. Neem de regel op `/` . Laat het veld leeg als u het binnenkomende pad wilt behouden. |
| Queryreeksen | De query teken reeks die in de omleiding wordt gebruikt. Neem de regel niet op `?` . Laat het veld leeg om de binnenkomende queryreeks te behouden. |
| Doelfragment | Het fragment dat in de omleiding moet worden gebruikt. Laat het veld leeg om het binnenkomende fragment te behouden. |

### <a name="example"></a>Voorbeeld

In dit voor beeld wordt de aanvraag omgeleid naar `https://contoso.com/exampleredirection?clientIp={client_ip}` , terwijl het fragment behouden blijft. Er wordt een HTTP-tijdelijke omleiding (307) gebruikt. Het IP-adres van de client wordt gebruikt in plaats van het `{client_ip}` token in de URL met behulp van de `client_ip` [server variabele](#server-variables).

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/url-redirect.png" alt-text="Scherm opname van de portal met de URL-omleidings actie.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlRedirect",
  "parameters": {
    "redirectType": "TemporaryRedirect",
    "destinationProtocol": "Https",
    "customHostname": "contoso.com",
    "customPath": "/exampleredirection",
    "customQueryString": "clientIp={client_ip}",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRedirectActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlRedirect'
  parameters: {
    redirectType: 'TemporaryRedirect'
    destinationProtocol: 'Https'
    customHostname: 'contoso.com'
    customPath: '/exampleredirection'
    customQueryString: 'clientIp={client_ip}'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRedirectActionParameters'
  }
}
```

---

## <a name="url-rewrite"></a><a name="UrlRewrite"></a> URL opnieuw schrijven

Gebruik de actie voor het opnieuw **schrijven van url's** om het pad van een aanvraag die naar uw oorsprong is doorgestuurd, opnieuw te schrijven.

### <a name="properties"></a>Eigenschappen

| Eigenschap | Ondersteunde waarden |
|----------|------------------|
| Bron patroon | Definieer het bron patroon in het URL-pad dat moet worden vervangen. Op dit moment gebruikt het bron patroon een overeenkomst op basis van voor voegsels. Als u wilt dat alle URL-paden overeenkomen, gebruikt u een slash ( `/` ) als bron patroon waarde. |
| Doel | Definieer het doelpad dat moet worden gebruikt in de herschrijf bewerking. Het doelpad overschrijft het bron patroon. |
| Niet-overeenkomend pad behouden | Als deze instelling is ingesteld op _Ja_, wordt het resterende pad na het bron patroon toegevoegd aan het nieuwe doelpad. |

### <a name="example"></a>Voorbeeld

In dit voor beeld worden alle aanvragen opnieuw naar het pad geschreven `/redirection` en blijft de rest van het pad onbewaard.

# <a name="portal"></a>[Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/url-rewrite.png" alt-text="Scherm opname van de portal met de actie voor het opnieuw schrijven van URL'S.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlRewrite",
  "parameters": {
    "sourcePattern": "/",
    "destination": "/redirection",
    "preserveUnmatchedPath": false,
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRewriteActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlRewrite'
  parameters: {
    sourcePattern: '/'
    destination: '/redirection'
    preserveUnmatchedPath: false
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRewriteActionParameters'
  }
}
```

---

## <a name="server-variables"></a>Server variabelen

Server variabelen voor regel sets bieden toegang tot gestructureerde informatie over de aanvraag. U kunt Server variabelen gebruiken voor het dynamisch wijzigen van de aanvraag/antwoord headers of het herschrijven van URL'S/query reeksen, bijvoorbeeld wanneer een nieuwe pagina wordt geladen of wanneer een formulier wordt gepost.

### <a name="supported-variables"></a>Ondersteunde variabelen

| Naam van de variabele    | Beschrijving                                                                                                                                                                                                                                                                               |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `socket_ip`      | Het IP-adres van de directe verbinding met de breedte van de Azure front-deur. Als de client een HTTP-proxy of een load balancer gebruikt voor het verzenden van de aanvraag, is de waarde van het `socket_ip` IP-adres van de proxy of Load Balancer.                                                                      |
| `client_ip`      | Het IP-adres van de client die de oorspronkelijke aanvraag heeft ingediend. Als `X-Forwarded-For` de aanvraag een header bevat, wordt het IP-adres van de client uit de header opgehaald.                                                                                                               |
| `client_port`    | De IP-poort van de client die de aanvraag heeft ingediend.                                                                                                                                                                                                                                          |
| `hostname`       | De hostnaam in de aanvraag van de client.                                                                                                                                                                                                                                             |
| `geo_country`    | Geeft het land of de regio van oorsprong van de aanvrager aan via het land-of regio nummer.                                                                                                                                                                                                       |
| `http_method`    | De methode die wordt gebruikt voor het maken van de URL-aanvraag, zoals `GET` of `POST` .                                                                                                                                                                                                                         |
| `http_version`   | Het aanvraag protocol. Gewoonlijk `HTTP/1.0` , `HTTP/1.1` of `HTTP/2.0` .                                                                                                                                                                                                                      |
| `query_string`   | De lijst met variabele/waarde-paren die volgt op '? ' in de aangevraagde URL.<br />Zo is de waarde in de aanvraag `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` `query_string` `id=123&title=fabrikam` .                                                      |
| `request_scheme` | Het aanvraag schema: `http` of `https` .                                                                                                                                                                                                                                                    |
| `request_uri`    | De volledige oorspronkelijke aanvraag-URI (met argumenten).<br />Zo is de waarde in de aanvraag `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` `request_uri` `/article.aspx?id=123&title=fabrikam` .                                                                     |
| `ssl_protocol`   | Het Protocol van een tot stand gebrachte TLS-verbinding.                                                                                                                                                                                                                                            |
| `server_port`    | De poort van de server die een aanvraag heeft geaccepteerd.                                                                                                                                                                                                                                           |
| `url_path`       | Identificeert de specifieke resource in de host waartoe de webclient toegang wil. Dit is het deel van de aanvraag-URI zonder de argumenten.<br />Zo is de waarde in de aanvraag `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` `uri_path` `/article.aspx` . |

### <a name="server-variable-format"></a>Indeling server variabele    

Server variabelen kunnen worden opgegeven met de volgende indelingen:

* `{variable}`: De volledige server variabele toevoegen. Als het IP-adres van de client bijvoorbeeld is, wordt `111.222.333.444` het `{client_ip}` token door berekend `111.222.333.444` .
* `{variable:offset}`: Voeg de server variabele toe na een specifieke offset, tot aan het einde van de variabele. De offset is gebaseerd op nul. Als het IP-adres van de client bijvoorbeeld is, wordt `111.222.333.444` het `{client_ip:3}` token door berekend `.222.333.444` .
* `{variable:offset:length}`: Voeg de server variabele toe na een bepaalde offset, tot aan de opgegeven lengte. De offset is gebaseerd op nul. Als het IP-adres van de client bijvoorbeeld is, wordt `111.222.333.444` het `{client_ip:4:3}` token door berekend `222` .

### <a name="supported-actions"></a>Ondersteunde acties

Server variabelen worden ondersteund voor de volgende acties:

* Query reeks voor de cache sleutel
* Aanvraagheader wijzigen
* Antwoordheader wijzigen
* URL-omleiding
* URL opnieuw genereren

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [Azure front deur Standard/Premium-regelset](concept-rule-set.md).
* Meer informatie over de [voor waarden voor de regel instellingen](concept-rule-set-match-conditions.md).
