---
title: Azure-app configuratie REST API-consistentie
description: Referentie pagina's voor het garanderen van realtime consistentie met behulp van de Azure-app configuratie REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: db9553c2c9c79a6beb9c66d0cb1a1a60435b2abd
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2020
ms.locfileid: "95253334"
---
# <a name="real-time-consistency"></a>Real-time consistentie

Vanwege de aard van sommige gedistribueerde systemen is consistentie in realtime tussen aanvragen moeilijk om impliciet af te dwingen. Een oplossing is het toestaan van Protocol ondersteuning in de vorm van meerdere synchronisatie tokens. Synchronisatie tokens zijn optioneel.

## <a name="initial-request"></a>Eerste aanvraag

Gebruik optionele `Sync-Token` aanvraag-en reactie headers om realtime-consistentie tussen verschillende client instanties en-aanvragen te garanderen.

Syntaxis:

```http
Sync-Token: <id>=<value>;sn=<sn>
```

|Parameter|Beschrijving|
|--|--|
| `<id>` | Token-ID (ondoorzichtig) |
| `<value>` | Token waarde (ondoorzichtig). Base64-gecodeerde teken reeks toestaan. |
| `<sn>` | Token Sequence Number (versie). Hoger betekent dat een nieuwere versie van hetzelfde token. Biedt betere gelijktijdigheid en caching van client. De client kan ervoor kiezen om alleen de laatste versie van het token te gebruiken, omdat token versies inclusief zijn. Deze para meter is niet vereist voor aanvragen. |

## <a name="response"></a>Antwoord

De service biedt een `Sync-Token` header met elk antwoord.

```http
Sync-Token: jtqGc1I4=MDoyOA==;sn=28
```

## <a name="subsequent-requests"></a>Volgende aanvragen

Elke volgende aanvraag wordt gegarandeerd real-time consistente reactie in verhouding tot de gegeven `Sync-Token` .

```http
Sync-Token: <id>=<value>
```

Als u de `Sync-Token` header van de aanvraag weglaat, is het mogelijk dat de service gedurende een korte periode (tot enkele seconden) reageert op gegevens in de cache, voordat deze intern wordt afgehandeld. Dit gedrag kan inconsistente Lees bewerkingen veroorzaken als er direct wijzigingen zijn aangebracht voor het lezen.

## <a name="multiple-sync-tokens"></a>Meerdere synchronisatie-tokens

De server reageert mogelijk met meerdere synchronisatie tokens voor een enkele aanvraag. Om realtime consistentie te blijven voor de volgende aanvraag, moet de client reageren met alle ontvangen synchronisatie tokens. Meerdere header-waarden moeten door komma's zijn gescheiden.

```http
Sync-Token: <token1-id>=<value>,<token2-id>=<value>
```
