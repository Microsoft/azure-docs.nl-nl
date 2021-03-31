---
title: bestand opnemen
description: bestand opnemen
author: HeidiSteen
ms.service: cognitive-search
ms.topic: include
ms.date: 05/06/2019
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: ee430241173a6c19e2a32e176f28411631d9cb19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "80272900"
---
U kunt meerdere services in een abonnement maken. Elke service kan op een specifieke laag worden ingericht. U wordt alleen beperkt door het aantal services dat op elke laag is toegestaan. U kunt bijvoorbeeld maximaal 12 services op de Basic-laag en nog eens 12 services op de S1-laag maken in hetzelfde abonnement. Zie [Een SKU of laag kiezen voor Azure Cognitive Search](../articles/search/search-sku-tier.md) voor meer informatie over lagen.

De limieten voor het maximumaantal services kunnen op aanvraag worden verhoogd. Neem contact op met Azure-ondersteuning als u meer services in hetzelfde abonnement nodig hebt.

| Resource            | Gratis<sup>1</sup> | Basic | S1  | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| ------------------- | ---- | ----- | --- | -- | -- | ----- | -- | -- |
| Maximumaantal services    |1     | 16    | 16  | 8  | 6  | 6     | 6  | 6  |
| Maximale schaal in zoekeenheden (SU's)<sup>2</sup> |N.v.t. |3 SU |36 SU |36 SU |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup> Gratis is gebaseerd op gedeelde, niet toegewezen, resources. Opschalen wordt niet ondersteund voor gedeelde resources.

<sup>2</sup> Zoekeenheden zijn factureringseenheden, toegewezen als een *replica* of als een *partitie*. U hebt beide resources nodig voor opslag, indexering en querybewerkingen. Zie [Resourceniveaus schalen voor query- en indexwerkbelastingen](../articles/search/search-capacity-planning.md) voor meer informatie over SU-berekeningen. 