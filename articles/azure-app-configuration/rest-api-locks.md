---
title: Azure-app configuratie REST API-vergren delingen
description: Naslag pagina's voor het werken met sleutel-waarde vergrendelingen met behulp van de Azure-app configuratie REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: e99ce5595bae8ed64285317d9249da60e0fc1b83
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96932520"
---
# <a name="locks"></a>Vergrendelingen

Deze API (versie 1,0) biedt vergrendelings-en ontgrendelings semantiek voor de bron van de sleutel waarde. Het ondersteunt de volgende bewerkingen:

- Vergren deling
- Vergren deling verwijderen

Indien aanwezig, `label` moet een expliciete label waarde zijn (geen joker teken). Voor alle bewerkingen is het een optionele para meter. Als u dit weglaat, betekent dit dat er geen label is.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="lock-key-value"></a>Lock-waarde

- Vereist: ``{key}`` , ``{api-version}``  
- Beschrijving ``label``

```http
PUT /locks/{key}?label={label}&api-version={api-version} HTTP/1.1
```

**Rapporten**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": null,
  "value": "example value",
  "created": "2017-12-05T02:41:26.4874615+00:00",
  "locked": true,
  "tags": []
}
```

Als de sleutel waarde niet bestaat, wordt het volgende antwoord geretourneerd:

```http
HTTP/1.1 404 Not Found
```

## <a name="unlock-key-value"></a>Sleutel waarde ontgrendelen

- Vereist: ``{key}`` , ``{api-version}``  
- Beschrijving ``label``

```http
DELETE /locks/{key}?label={label}?api-version={api-version} HTTP/1.1
```

**Rapporten**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": null,
  "value": "example value",
  "created": "2017-12-05T02:41:26.4874615+00:00",
  "locked": true,
  "tags": []
}
```

Als de sleutel waarde niet bestaat, wordt het volgende antwoord geretourneerd:

```http
HTTP/1.1 404 Not Found
```

## <a name="conditional-lock-and-unlock"></a>Voorwaardelijke vergren deling en ontgrendelen

Gebruik `If-Match` of aanvraag headers om race voorwaarden te voor komen `If-None-Match` . Het `etag` argument maakt deel uit van de sleutel weergave. Als `If-Match` of `If-None-Match` wordt wegge laten, is de bewerking onvoorwaardelijk.

Met de volgende aanvraag wordt de bewerking alleen toegepast als de huidige weer gave van de sleutel waarde overeenkomt met de opgegeven `etag` :

```http
PUT|DELETE /locks/{key}?label={label}&api-version={api-version} HTTP/1.1
If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

Met de volgende aanvraag wordt de bewerking alleen toegepast als de huidige weer gave van de sleutel waarde bestaat, maar niet overeenkomt met de opgegeven `etag` :

```http
PUT|DELETE /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```
