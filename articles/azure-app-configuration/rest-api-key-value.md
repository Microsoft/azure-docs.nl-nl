---
title: Azure-app configuratie REST API-sleutel waarde
description: Referentie pagina's voor het werken met sleutel waarden met behulp van de Azure-app configuratie REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 95081d6f8c2770d01f7836e08b6851860bf47ba8
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932554"
---
# <a name="key-values"></a>Sleutelwaarden

Een sleutel waarde is een resource die wordt geïdentificeerd door een unieke combi natie van `key`  +  `label` . `label` is optioneel. Als u expliciet wilt verwijzen naar een sleutel waarde zonder label, gebruikt u \ 0 (URL gecodeerd als ``%00`` ). Bekijk de Details voor elke bewerking.

Dit artikel is van toepassing op API-versie 1,0.

## <a name="operations"></a>Operations

- Ophalen
- Meerdere lijsten
- Instellen
- Verwijderen

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="syntax"></a>Syntax

```json
{
  "etag": [string],
  "key": [string],
  "label": [string, optional],
  "content_type": [string, optional],
  "value": [string],
  "last_modified": [datetime ISO 8601],
  "locked": [boolean],
  "tags": [object with string properties, optional]
}
```

## <a name="get-key-value"></a>Sleutel waarde ophalen

Vereist: ``{key}`` , ``{api-version}``  
Optioneel: ``label`` (als u dit weglaat, impliceert dit een sleutel waarde zonder label.)

```http
GET /kv/{key}?label={label}&api-version={api-version}
```

**Rapporten**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8;
Last-Modified: Tue, 05 Dec 2017 02:41:26 GMT
ETag: "4f6dd610dd5e4deebc7fbaef685fb903"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": null,
  "value": "example value",
  "last_modified": "2017-12-05T02:41:26+00:00",
  "locked": "false",
  "tags": {
    "t1": "value1",
    "t2": "value2"
  }
}
```

Als de sleutel niet bestaat, wordt het volgende antwoord geretourneerd:

```http
HTTP/1.1 404 Not Found
```

## <a name="get-conditionally"></a>Ophalen (voorwaardelijk)

Om de caching, het gebruik `If-Match` of de aanvraag headers van de client te verbeteren `If-None-Match` . Het `etag` argument maakt deel uit van de sleutel weergave. Zie de [secties 14,24 en 14,26](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)voor meer informatie.

Met de volgende aanvraag wordt de sleutel waarde alleen opgehaald als de huidige representatie niet overeenkomt met de opgegeven `etag` :

```http
GET /kv/{key}?api-version={api-version} HTTP/1.1
Accept: application/vnd.microsoft.appconfig.kv+json;
If-None-Match: "{etag}"
```

**Rapporten**

```http
HTTP/1.1 304 NotModified
```

of

```http
HTTP/1.1 200 OK
```

## <a name="list-key-values"></a>Sleutel waarden weer geven

Optioneel: ``key`` (indien niet opgegeven, is dit een wille keurige toets.) Optioneel: ``label`` (indien niet opgegeven, impliceert dit een wille keurig label.)

```http
GET /kv?label=*&api-version={api-version} HTTP/1.1
```

**Beantwoord**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvset+json; charset=utf-8
```

Zie de sectie ' filteren ' verderop in dit artikel voor meer opties.

## <a name="pagination"></a>Paginering

Het resultaat wordt gepagineerd als het aantal geretourneerde items de reactie limiet overschrijdt. Volg de optionele `Link` reactie headers en gebruik `rel="next"` voor navigatie.
De inhoud biedt ook een volgende koppeling in de vorm van de `@nextLink` eigenschap. De gekoppelde URI bevat het `api-version` argument.

```http
GET /kv?api-version={api-version} HTTP/1.1
```

**Beantwoord**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvs+json; charset=utf-8
Link: <{relative uri}>; rel="next"
```

```json
{
    "items": [
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="filtering"></a>Filteren

Een combi natie van `key` en `label` filteren wordt ondersteund.
Gebruik de optionele `key` `label` para meters en de query reeks parameters.

```http
GET /kv?key={key}&label={label}&api-version={api-version}
```

### <a name="supported-filters"></a>Ondersteunde filters

|Sleutel filter|Effect|
|--|--|
|`key` wordt weggelaten of `key=*`|Komt overeen met **een** sleutel|
|`key=abc`|Komt overeen met een sleutel met de naam **ABC**|
|`key=abc*`|Komt overeen met de namen van sleutels die met **ABC** beginnen|
|`key=abc,xyz`|Komt overeen met de sleutel namen **ABC** of **xyz** (beperkt tot 5 CSV)|

|Label filter|Effect|
|--|--|
|`label` wordt weggelaten of `label=*`|Komt overeen met **een** label|
|`label=%00`|Komt overeen met KV zonder label|
|`label=prod`|Komt overeen met het label **Prod**|
|`label=prod*`|Komt overeen met labels die beginnen met een **Prod**|
|`label=prod,test`|Komt overeen met de labels **Prod** of **test** (beperkt tot 5 CSV)|

**_Gereserveerde tekens_* _

`_`, `\`, `,`

Als een gereserveerd teken deel uitmaakt van de waarde, moet het worden voorafgegaan door `\{Reserved Character}` . Niet-gereserveerde tekens kunnen ook worden opgenomen in een escape-teken.

***Filter validatie** _

In het geval van een filter validatie fout is het antwoord HTTP `400` met fout Details:

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
```

```json
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Invalid request parameter '{filter}'",
  "name": "{filter}",
  "detail": "{filter}(2): Invalid character",
  "status": 400
}
```

_ *Voor beelden**

- Alles

    ```http
    GET /kv?api-version={api-version}
    ```

- Sleutel naam begint met **ABC** en bevat alle labels

    ```http
    GET /kv?key=abc*&label=*&api-version={api-version}
    ```

- Sleutel naam begint met **ABC** en label is gelijk aan **v1** of **v2**

    ```http
    GET /kv?key=abc*&label=v1,v2&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>Specifieke velden aanvragen

Gebruik de optionele `$select` query teken reeks parameter en geef een door komma's gescheiden lijst op met de aangevraagde velden. Als de `$select` para meter wordt wegge laten, bevat het antwoord de standaardset.

```http
GET /kv?$select=key,value&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>Toegang op basis van tijd

Een weer gave van het resultaat verkrijgen, omdat deze zich in een eerder tijdstip bevond. Zie voor meer informatie sectie [2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1). De paginering wordt nog steeds ondersteund zoals eerder in dit artikel is gedefinieerd.

```http
GET /kv?api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**Beantwoord**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvset+json"
Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT
Link: <{relative uri}>; rel="original"
```

```json
{
    "items": [
        ....
    ]
}
```

## <a name="set-key"></a>Sleutel instellen

- Vereist: ``{key}``
- Optioneel: ``label`` (indien niet opgegeven, of label = %00, impliceert deze sleutel waarde zonder label.)

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json
```

```json
{
  "value": "example value",         // optional
  "content_type": "user defined",   // optional
  "tags": {                         // optional
    "tag1": "value1",
    "tag2": "value2",
  }
}
```

**Rapporten**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8
Last-Modified: Tue, 05 Dec 2017 02:41:26 GMT
ETag: "4f6dd610dd5e4deebc7fbaef685fb903"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": "user defined",
  "value": "example value",
  "last_modified": "2017-12-05T02:41:26.4874615+00:00",
  "tags": {
    "tag1": "value1",
    "tag2": "value2",
  }
}
```

Als het item is vergrendeld, ontvangt u het volgende antwoord:

```http
HTTP/1.1 409 Conflict
Content-Type: application/problem+json; charset="utf-8"
```

```json
{
    "type": "https://azconfig.io/errors/key-locked",
    "title": "Modifing key '{key}' is not allowed",
    "name": "{key}",
    "detail": "The key is read-only. To allow modification unlock it first.",
    "status": "409"
}
```

## <a name="set-key-conditionally"></a>Sleutel instellen (voorwaardelijk)

Gebruik `If-Match` of aanvraag headers om race voorwaarden te voor komen `If-None-Match` . Het `etag` argument maakt deel uit van de sleutel weergave.
Als `If-Match` of `If-None-Match` wordt wegge laten, is de bewerking onvoorwaardelijk.

Met het volgende antwoord wordt de waarde alleen bijgewerkt als de huidige representatie overeenkomt met de opgegeven `etag` :

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json
If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

Met het volgende antwoord wordt de waarde alleen bijgewerkt als de huidige representatie niet overeenkomt met de opgegeven `etag` :

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json;
If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

De volgende aanvraag voegt de waarde alleen toe als er al een representatie bestaat:

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json;
If-Match: "*"
```

Met de volgende aanvraag wordt de waarde alleen toegevoegd als een representatie nog niet bestaat:

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json
If-None-Match: "*"
```

**Antwoorden**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8
...
```

of

```http
HTTP/1.1 412 PreconditionFailed
```

## <a name="delete"></a>Verwijderen

- Vereist: `{key}` , `{api-version}`
- Optioneel: `{label}` (indien niet opgegeven, of label = %00, impliceert deze sleutel waarde zonder label.)

```http
DELETE /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
```

**Antwoord:** Retourneert de verwijderde sleutel waarde of geen als de sleutel waarde niet bestaat.

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8
...
```

of

```http
HTTP/1.1 204 No Content
```

## <a name="delete-key-conditionally"></a>Sleutel verwijderen (voorwaardelijk)

Dit is vergelijkbaar met de sectie ' set Key (voorwaardelijk) ' eerder in dit artikel.
