---
title: bestand opnemen
description: bestand opnemen
services: search
author: HeidiSteen
ms.service: cognitive-search
ms.topic: include
ms.date: 05/11/2020
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: fe5c8129434ddb4eec2dd25a3f123f28b4db221b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "83682641"
---
Een zoekservice wordt beperkt door de schijfruimte of door de vaste limiet voor het maximumaantal indexen of indexeerfuncties (wat zich als eerste voordoet). De volgende tabel bevat opslaglimieten. Zie [Limieten per resource](../articles/search/search-limits-quotas-capacity.md#index-limits) voor maximale objectlimieten.

| Resource | Gratis | Basic<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| -------- | --- | --- | --- | --- | --- | --- | --- | --- |
| Service Level Agreement (SLA)<sup>2</sup>  |Nee |Ja |Ja |Ja |Ja |Ja |Ja |Ja |
| Opslag per partitie |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Partities per service |N.v.t. |1 |12 |12 |12 |3 |12 |12 |
| Partitiegrootte |N.v.t. |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Replica's |N.v.t. |3 |12 |12 |12 |12 |12 |12 |

<sup>1</sup> Basic heeft één vaste partitie. Er kunnen aanvullende zoekeenheden worden gebruikt om replica's toe te voegen voor grotere queryvolumes.

<sup>2</sup> Service Level Agreements zijn van kracht voor factureerbare services op toegewezen resources. Gratis services en preview-functies hebben geen SLA. Voor factureerbare services worden SLA's van kracht wanneer u voldoende redundantie voor uw service inricht. SLA's voor query's (lezen) vereisen twee of meer replica's. SLA's voor query's en indexering (lezen-schrijven) vereisen drie of meer replica's. Het aantal partities is geen SLA-overweging. 