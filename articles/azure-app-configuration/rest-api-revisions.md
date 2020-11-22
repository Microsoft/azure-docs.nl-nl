---
title: Revisies van Azure-app configuratie REST API sleutel waarde
description: Naslag pagina's voor het werken met revisies van sleutel waarden met behulp van de Azure-app configuratie REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 668345da8bb89412f7b1dd36975c5bed6f229580
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2020
ms.locfileid: "95246381"
---
# <a name="key-value-revisions"></a>Revisies van sleutel waarden

Een *sleutel waarde-revisie* definieert de historische weer gave van een sleutel waarde resource. Revisies verlopen na 7 dagen voor gratis laag winkels of 30 dagen voor de Standard-laag. Revisies ondersteunen de `List` bewerking.

Voor alle bewerkingen ``key`` is een optionele para meter. Als u dit weglaat, wordt een wille keurige toets.

Voor alle bewerkingen ``label`` is een optionele para meter. Als u dit weglaat, impliceert dit een wille keurig label.

Dit artikel is van toepassing op API-versie 1,0.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="list-revisions"></a>Revisies weer geven

```http
GET /revisions?label=*&api-version={api-version} HTTP/1.1
```

**Rapporten**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvset+json; charset=utf-8"
Accept-Ranges: items
```

```json
{
    "items": [
        {
          "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
          "key": "{key}",
          "label": "{label}",
          "content_type": null,
          "value": "example value",
          "last_modified": "2017-12-05T02:41:26.4874615+00:00",
          "tags": []
        },
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="pagination"></a>Paginering

Het resultaat wordt gepagineerd als het aantal geretourneerde items de reactie limiet overschrijdt. Volg de optionele ``Link`` reactie header en gebruik ``rel="next"`` voor navigatie. De inhoud biedt ook een volgende koppeling in de vorm van de ``@nextLink`` eigenschap.

```http
GET /revisions?api-version={api-version} HTTP/1.1
```

**Beantwoord**

```http
HTTP/1.1 OK
Content-Type: application/vnd.microsoft.appconfig.kvs+json; charset=utf-8
Accept-Ranges: items
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

## <a name="list-subset-of-revisions"></a>Subset van revisies weer geven

De `Range` aanvraag header gebruiken. Het antwoord bevat de header `Content-Range`. Als de server niet kan voldoen aan het aangevraagde bereik, reageert het met HTTP `416` ( `RangeNotSatisfiable` ).

```http
GET /revisions?api-version={api-version} HTTP/1.1
Range: items=0-2
```

**Response**

```http
HTTP/1.1 206 Partial Content
Content-Type: application/vnd.microsoft.appconfig.revs+json; charset=utf-8
Content-Range: items 0-2/80
```

## <a name="filtering"></a>Filteren

Een combi natie van `key` en `label` filteren wordt ondersteund.
Gebruik de optionele `key` `label` para meters en de query reeks parameters.

```http
GET /revisions?key={key}&label={label}&api-version={api-version}
```

### <a name="supported-filters"></a>Ondersteunde filters

|Sleutel filter|Effect|
|--|--|
|`key` wordt weggelaten of `key=*`|Komt overeen met **een** sleutel|
|`key=abc`|Komt overeen met een sleutel met de naam  **ABC**|
|`key=abc*`|Komt overeen met de namen van sleutels die met **ABC** beginnen|
|`key=*abc`|Komt overeen met de namen van sleutels die met **ABC** eindigen|
|`key=*abc*`|Komt overeen met sleutel namen die **ABC** bevatten|
|`key=abc,xyz`|Komt overeen met de sleutel namen **ABC** of **xyz** (beperkt tot 5 CSV)|

|Label filter|Effect|
|--|--|
|`label` wordt weggelaten of `label=`|Komt overeen met vermelding zonder label|
|`label=*`|Komt overeen met **een** label|
|`label=prod`|Komt overeen met het label **Prod**|
|`label=prod*`|Komt overeen met labels die beginnen met een **Prod**|
|`label=*prod`|Komt overeen met labels die met een **Prod** -reeks eindigen|
|`label=*prod*`|Komt overeen met labels die **Prod** bevatten|
|`label=prod,test`|Komt overeen met de labels **Prod** of **test** (beperkt tot 5 CSV)|

### <a name="reserved-characters"></a>Gereserveerde tekens

De gereserveerde tekens zijn:

`*`, `\`, `,`

Als een gereserveerd teken deel uitmaakt van de waarde, moet het worden voorafgegaan door `\{Reserved Character}` . Niet-gereserveerde tekens kunnen ook worden opgenomen in een escape-teken.

### <a name="filter-validation"></a>Filter validatie

Als er een filter validatie fout optreedt, is het antwoord HTTP `400` met fout Details:

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

### <a name="examples"></a>Voorbeelden

- Hele

    ```http
    GET /revisions
    ```

- Items waarbij de sleutel naam begint met **ABC**:

    ```http
    GET /revisions?key=abc*&api-version={api-version}
    ```

- Items waarbij de sleutel naam **ABC** of **xyz** is en labels bevatten **Prod**:

    ```http
    GET /revisions?key=abc,xyz&label=*prod*&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>Specifieke velden aanvragen

Gebruik de optionele `$select` query teken reeks parameter en geef een door komma's gescheiden lijst op met de aangevraagde velden. Als de `$select` para meter wordt wegge laten, bevat het antwoord de standaardset.

```http
GET /revisions?$select=value,label,last_modified&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>Toegang op basis van tijd

Een weer gave van het resultaat verkrijgen, omdat deze zich in een eerder tijdstip bevond. Zie voor meer informatie [http-Framework voor Time-Based toegang tot resource Staten--'memento](https://tools.ietf.org/html/rfc7089#section-2.1), punt 2.1.1.

```http
GET /revisions?api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**Beantwoord**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.revs+json"
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
