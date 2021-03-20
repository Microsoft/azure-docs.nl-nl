---
author: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/09/2018
ms.author: nitinme
ms.openlocfilehash: 1d8340054ace25a0cdc36eef9c3d5b4238a6f99b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "67176319"
---
De service en uw abonnementtype bepalen hoeveel query’s u per seconde kunt uitvoeren (QPS). Zorg dat uw toepassing logica bevat die zorgt dat u binnen uw quotum blijft. Als de QPS-limiet is bereikt of overschreden, mislukt de aanvraag en wordt er een HTTP 429-statuscode geretourneerd. Het antwoord bevat de header `Retry-After`, waarin wordt aangegeven hoe lang u moet wachten voordat u een nieuwe aanvraag kunt verzenden.

## <a name="denial-of-service-versus-throttling"></a>Denial of Service (DoS) versus beperking

De service maakt een onderscheid tussen een denial-of-service (DoS)-aanval en een overschrijding van de QPS. Als de service een Denial of Service-aanval vermoedt, slaagt de aanvraag (HTTP-statuscode is 200 OK). De hoofdtekst van het antwoord is in dit geval echter leeg.