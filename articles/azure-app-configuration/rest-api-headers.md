---
title: Azure-app configuratie REST API-headers
description: Naslag pagina's voor kopteksten die worden gebruikt met de Azure-app configuratie REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 80b20b0b55219766872166685c0b1257f3c39c55
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932571"
---
# <a name="headers"></a>Kopteksten

Dit artikel bevat koppelingen naar naslag pagina's voor kopteksten die worden gebruikt met de Azure-app configuratie REST API.

## <a name="request-headers"></a>Aanvraag headers

In de volgende tabel worden algemene aanvraag headers beschreven die worden gebruikt in Azure-app configuratie.

| Header | Beschrijving | Voorbeeld |
|--|--|--|
| **Autorisatie** | Wordt gebruikt om een aanvraag voor de service te [verifiëren](./rest-api-authentication-index.md) . Zie [sectie 14,8](https://tools.ietf.org/html/rfc2616#section-14.8) | `Authorization: HMAC-SHA256 Credential=<Credential>&SignedHeaders=Host;x-ms-date;x-ms-content-sha256&Signature=<Signature>` |
| **Accepteren** | De server informeert welk media type de client zal accepteren in een HTTP-antwoord. Zie [sectie 14,1](https://tools.ietf.org/html/rfc2616#section-14.1) | `Accept: application/vnd.microsoft.appconfig.kv+json;` |
| **Accept-datetime** | Vraagt de server om de inhoud te retour neren als een representatie van de voor gaande status. De waarde van deze header is de aangevraagde datum/tijd van die status. Zie [RFC 7089](https://tools.ietf.org/html/rfc7089#section-2.1.1) | `Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT` |
| **Content-Type** | Bevat het media type van de inhoud in de hoofd tekst van de HTTP-aanvraag. Zie [sectie 14,17](https://tools.ietf.org/html/rfc2616#section-14.17) | `Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8;` |
| **Datum** | De datum/tijd waarop de HTTP-aanvraag is verzonden. Deze header wordt gebruikt in [HMAC-verificatie](./rest-api-authentication-hmac.md). Zie [sectie 14,18](https://tools.ietf.org/html/rfc2616#section-14.18) | `Date: Fri, 11 May 2018 18:48:36 GMT` |
| **Host** | Hiermee geeft u de Tenant op waarvoor de aanvraag is verleend. Deze header wordt gebruikt in [HMAC-verificatie](./rest-api-authentication-hmac.md). Zie [sectie 14,23](https://tools.ietf.org/html/rfc2616#section-14.23) | `Host: contoso.azconfig.io` |
| **If-match** | Wordt gebruikt om een HTTP-aanvraag voorwaardelijk te maken. Deze aanvraag zou alleen kunnen slagen als de ETag van de doel resource overeenkomt met de waarde van deze koptekst. De waarde * komt overeen met een wille keurige ETag. Zie [sectie 14,24](https://tools.ietf.org/html/rfc2616#section-14.24) | `If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"` |
| **If-None-Match** | Wordt gebruikt om een HTTP-aanvraag voorwaardelijk te maken. Deze aanvraag zou alleen kunnen slagen als de ETag van de doel resource niet overeenkomt met de waarde van deze koptekst. De waarde * komt overeen met een wille keurige ETag. Zie [sectie 14,26](https://tools.ietf.org/html/rfc2616#section-14.26) | `If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"` |
| **Sync-token** | Wordt gebruikt voor het inschakelen van real-time consistentie tijdens een reeks aanvragen. | `Sync-Token: jtqGc1I4=MDoyOA==;sn=28` |
| **x-MS-Client-Request-id** | Een unieke ID die door de client wordt gebruikt om de retour van een aanvraag te volgen. | `x-ms-client-request-id: 00000000-0000-0000-0000-000000000000` |
| **x-MS-content-sha256** | Een sha256-samen vatting van de HTTP-aanvraag tekst. Deze header wordt gebruikt in [HMAC-verificatie](./rest-api-authentication-hmac.md). | `x-ms-content-sha256: 47DEQpj8HBSa+/TImW+5JCeuQeRkm5NMpJWZG3hSuFU=` |
| **x-MS-date** | Deze header kan worden ingesteld en gebruikt in plaats van de `Date` koptekst als de datum-header niet kan worden geopend. Deze header wordt gebruikt in [HMAC-verificatie](./rest-api-authentication-hmac.md). | `x-ms-date: Fri, 11 May 2018 18:48:36 GMT` |
| **x-MS-Return-client-request-id** | Wordt gebruikt in combi natie met de `x-ms-client-request-id` koptekst. Als de waarde van deze header ' True ' is, wordt de server geïnstrueerd om de waarde van de aanvraag header te retour neren `x-ms-client-request-id` . | `x-ms-return-client-request-id: true` |

## <a name="response-headers"></a>Antwoordheaders

De-server kan de volgende HTTP-headers in de reacties bevatten.

| Header | Beschrijving | Voorbeeld |
|--|--|--|
| **Content-Type** | Bevat het media type van de inhoud in de hoofd tekst van het HTTP-antwoord. Zie [sectie 14,17](https://tools.ietf.org/html/rfc2616#section-14.17) | `Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8;` |
| **ETag** | Een ondoorzichtige token die de status van een bepaalde resource weergeeft. Kan worden gebruikt in voorwaardelijke bewerkingen. Zie [sectie 14,19](https://tools.ietf.org/html/rfc2616#section-14.19) | `ETag: "4f6dd610dd5e4deebc7fbaef685fb903"` |
| **Laatst gewijzigd** | Beschrijft wanneer de aangevraagde resource voor het laatst is gewijzigd. Opgemaakt als een [http-date](https://tools.ietf.org/html/rfc2616#section-3.3.1). Zie [sectie 14,29](https://tools.ietf.org/html/rfc2616#section-14.29) | `Last-Modified: Tue, 05 Dec 2017 02:41:26 GMT` |
| **Koppeling** | Bevat koppelingen naar bronnen die betrekking hebben op het antwoord. Deze header wordt gebruikt voor paginering met behulp van de _volgende_ koppeling. Zie [RFC 5988](https://tools.ietf.org/html/rfc5988) | `Link: </kv?after={token}>; rel="next"` |
| **'Memento-datetime** | Geeft aan dat de inhoud in een antwoord een eerdere status vertegenwoordigt. De waarde van deze header is de datum/tijd van die status. Zie [RFC 7089](https://tools.ietf.org/html/rfc7089#section-2.1.1) | `Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT` |
| **opnieuw proberen na-MS** | Geeft een aanbevolen periode (in milliseconden) op die de client moet wachten voordat een mislukte aanvraag opnieuw wordt uitgevoerd. | `retry-after-ms: 10` |
| **x-MS-Request-id** | Een unieke ID die wordt gegenereerd door de server die wordt gebruikt om de aanvraag binnen de service bij te houden. | `x-ms-request-id: 00000000-0000-0000-0000-000000000000` |
| **WWW-verificatie** | Wordt gebruikt om clients voor verificatie te vragen en een reden te geven waarom een verificatie poging is mislukt. Zie [sectie 14,47](https://tools.ietf.org/html/rfc2616#section-14.47) | `WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="Invalid Signature"` |
