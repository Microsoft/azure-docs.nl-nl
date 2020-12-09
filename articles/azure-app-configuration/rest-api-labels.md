---
title: Azure-app configuratie REST API-labels
description: Naslag pagina's voor het werken met labels met behulp van de Azure-app configuratie REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 5a59f5910d44f2a2b4cd75e7a1d51c2ed5dd51a4
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932503"
---
# <a name="labels"></a>Labels

API-versie: 1,0

De **Label** resource wordt als volgt gedefinieerd:

```json
{
      "name": [string]             // Name of the label
}
```

Ondersteunt de volgende bewerkingen:

- Lijst

Voor alle bewerkingen ``name`` is een optionele filter parameter. Als u dit weglaat, impliceert dit **een** label.

## <a name="prerequisites"></a>Vereisten

- Alle HTTP-aanvragen moeten worden geverifieerd. Zie de sectie [verificatie](./rest-api-authentication-index.md) .
- Alle HTTP-aanvragen moeten expliciet worden verstrekt `api-version` . Zie de sectie [versie beheer](./rest-api-versioning.md) .

## <a name="list-labels"></a>Labels weer geven

```http
GET /labels?api-version={api-version} HTTP/1.1
```

**Rapporten**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.labelset+json; charset=utf-8"
```

```json
{
    "items": [
        {
          "name": "{label-name}"
        },
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="pagination"></a>Paginering

Het resultaat wordt gepagineerd als het aantal geretourneerde items de reactie limiet overschrijdt. Volg de optionele `Link` antwoord headers en gebruik `rel="next"` voor navigatie. De inhoud biedt ook een volgende koppeling in de vorm van de `@nextLink` eigenschap. De volgende koppeling bevat de `api-version` para meter.

```http
GET /labels?api-version={api-version} HTTP/1.1
```

**Beantwoord**

```http
HTTP/1.1 OK
Content-Type: application/vnd.microsoft.appconfig.labelset+json; charset=utf-8
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

## <a name="filtering"></a>Filteren

Filteren op `name` wordt ondersteund.

```http
GET /labels?name={label-name}&api-version={api-version}
```

### <a name="supported-filters"></a>Ondersteunde filters

|Sleutel filter|Effect|
|--|--|
|`name` wordt weggelaten of `name=*`|Komt overeen met **een** label|
|`name=abc`|Komt overeen met een label met de naam  **ABC**|
|`name=abc*`|Komt overeen met label namen die met **ABC** beginnen|
|`name=abc,xyz`|Komt overeen met label namen **ABC** of **xyz** (beperkt tot 5 CSV)|

### <a name="reserved-characters"></a>Gereserveerde tekens

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
  "title": "Invalid request parameter 'name'",
  "name": "name",
  "detail": "name(2): Invalid character",
  "status": 400
}
```

### <a name="examples"></a>Voorbeelden

- Alles

    ```http
    GET /labels?api-version={api-version}
    ```

- Label naam begint met **ABC**

    ```http
    GET  /labels?name=abc*&api-version={api-version}
    ```

- De label naam is **ABC** of **xyz**

    ```http
    GET /labels?name=abc,xyz&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>Specifieke velden aanvragen

Gebruik de optionele `$select` query teken reeks parameter en geef een door komma's gescheiden lijst op met de aangevraagde velden. Als de `$select` para meter wordt wegge laten, bevat het antwoord de standaardset.

```http
GET /labels?$select=name&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>Toegang Time-Based

Een weer gave van het resultaat verkrijgen, omdat deze zich in een eerder tijdstip bevond. Zie sectie [2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1)

```http
GET /labels&api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**Beantwoord**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.labelset+json"
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
