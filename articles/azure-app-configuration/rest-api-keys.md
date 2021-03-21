---
title: Azure-app configuratie REST API-sleutels
description: Naslag pagina's voor het werken met sleutels met behulp van de Azure-app configuratie REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 65ca190d7fbd6d8d4df473fbe2112eafbd031fde
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96932537"
---
# <a name="keys"></a>Sleutels

API-versie: 1,0

De volgende syntaxis vertegenwoordigt een sleutel Bron:

```http
{
    "name": [string]             // Name of the key
}
```

## <a name="operations"></a>Operations

Belangrijkste resources ondersteunen de volgende bewerking:

- Lijst

Voor alle bewerkingen `name` is een optionele filter parameter. Als u dit weglaat, wordt *een wille keurige* toets.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="list-keys"></a>Lijst met sleutels

```http
GET /keys?api-version={api-version} HTTP/1.1
```

**Rapporten**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.keyset+json; charset=utf-8"
```

```json
{
    "items": [
        {
          "name": "{key-name}"
        },
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="pagination"></a>Paginering

Het resultaat wordt gepagineerd als het aantal geretourneerde items de reactie limiet overschrijdt. Volg de optionele `Link` antwoord headers en gebruik `rel="next"` voor navigatie. De inhoud biedt ook een volgende koppeling in de vorm van de `@nextLink` eigenschap. De volgende koppeling bevat de `api-version` para meter.

```http
GET /keys?api-version={api-version} HTTP/1.1
```

**Beantwoord**

```http
HTTP/1.1 OK
Content-Type: application/vnd.microsoft.appconfig.keyset+json; charset=utf-8
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

Filteren op ```name``` wordt ondersteund.

```http
GET /keys?name={key-name}&api-version={api-version}
```

De volgende filters worden ondersteund:

|Sleutel filter|Effect|
|--|--|
|`name` wordt weggelaten of `name=*`|Komt overeen met **een** sleutel|
|`name=abc`|Komt overeen met een sleutel met de naam  **ABC**|
|`name=abc*`|Komt overeen met sleutelnamen die beginnen met **abc**|
|`name=abc,xyz`|Komt overeen met de sleutel namen **ABC** of **xyz** (beperkt tot 5 CSV)|

De volgende tekens zijn gereserveerd: `*` , `\` , `,`

Als een gereserveerd teken deel uitmaakt van de waarde, moet het worden voorafgegaan door `\{Reserved Character}` . Niet-gereserveerde tekens kunnen ook worden opgenomen in een escape-teken.

## <a name="filter-validation"></a>Filter validatie

In het geval van een filter validatie fout is het antwoord HTTP `400` met fout Details:

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

## <a name="examples"></a>Voorbeelden

- Alles

    ```http
    GET /keys?api-version={api-version}
    ```

- Sleutel naam begint met **ABC**

    ```http
    GET  /keys?name=abc*&api-version={api-version}
    ```

- De sleutel naam is **ABC** of **xyz**

    ```http
    GET /keys?name=abc,xyz&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>Specifieke velden aanvragen

Gebruik de optionele `$select` query teken reeks parameter en geef een door komma's gescheiden lijst met aangevraagde velden op. Als de `$select` para meter wordt wegge laten, bevat het antwoord de standaardset.

```http
GET /keys?$select=name&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>Toegang Time-Based

Een weer gave van het resultaat verkrijgen, omdat deze zich in een eerder tijdstip bevond. Zie sectie [2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1)

```http
GET /keys&api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**Beantwoord**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.keyset+json"
Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT
Link: <relative uri>; rel="original"
```

```json
{
    "items": [
        ....
    ]
}
```
