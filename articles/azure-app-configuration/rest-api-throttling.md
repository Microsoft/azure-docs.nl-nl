---
title: Azure-app configuratie REST API-beperking
description: Naslag informatie over het beperken van beperkingen bij het gebruik van de Azure-app configuratie REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: b4472fdcacfe5b62c0cded089224c6d41e32f47d
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932435"
---
# <a name="throttling"></a>Beperking

Configuratie archieven hebben limieten voor de aanvragen die ze kunnen verwerken. Aanvragen die een toegewezen quotum voor een configuratie archief overschrijden, ontvangen een antwoord van HTTP 429 (te veel aanvragen).

Beperking is onderverdeeld in verschillende quota beleidsregels:

- **Totaal aantal aanvragen** -totaal van de aanvragen
- **Totale band breedte** -uitgaande gegevens in bytes
- **Opslag** : totale opslag grootte van gebruikers gegevens in bytes

## <a name="handling-throttled-responses"></a>Vertraagde antwoorden verwerken

Wanneer de frequentie limiet voor een gegeven quotum is bereikt, reageert de server op verdere aanvragen van dat type met een _429_ -status code. Het _429_ -antwoord bevat de header _nieuwe poging na MS_ die de client een aanbevolen wacht tijd (in milliseconden) heeft, zodat de aanvraag quotum kan worden aangevuld.

```http
HTTP/1.1 429 (Too Many Requests)
retry-after-ms: 10
Content-Type: application/problem+json; charset=utf-8
```

```json
{
  "type": "https://azconfig.io/errors/too-many-requests",
  "title": "Resource utilization has surpassed the assigned quota",
  "policy": "Total Requests",
  "status": 429
}
```

In het bovenstaande voor beeld heeft de client het toegestane quotum overschreden en wordt er 10 milliseconden gewacht voordat er verdere aanvragen worden gedaan. Clients moeten ook progressieve uitstel overwegen.

## <a name="other-retry"></a>Andere nieuwe poging

De service kan andere situaties identificeren dan de beperking waarbij een client opnieuw moet worden geprobeerd (bijvoorbeeld: 503 Service niet beschikbaar). In al deze gevallen wordt de `retry-after-ms` reactie header weer gegeven. Ter verbetering van de robuustheid wordt de client aanbevolen het aanbevolen interval te volgen en een nieuwe poging uit te voeren.

```http
HTTP/1.1 503 Service Unavailable
retry-after-ms: 787
```
