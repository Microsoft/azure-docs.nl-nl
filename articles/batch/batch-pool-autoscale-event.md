---
title: De gebeurtenis Azure Batch groep automatisch schalen
description: Verwijzing voor de gebeurtenis auto scale van batch-pool, die wordt verzonden wanneer een groep automatisch wordt geschaald. Met de inhoud van het logboek worden automatisch geschaalde formules en evaluatie resultaten voor de groep weer gegeven.
ms.topic: reference
ms.date: 10/08/2020
ms.openlocfilehash: e548ed484e5f683a8f79434ce4095ac66900f01a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91852134"
---
# <a name="pool-autoscale-event"></a>Gebeurtenis voor pool maken voor automatisch schalen

 Deze gebeurtenis wordt verzonden zodra een pool automatisch wordt geschaald. Met de inhoud van het logboek worden automatisch geschaalde formules en evaluatie resultaten voor de groep weer gegeven.

 In het volgende voor beeld ziet u de hoofd tekst van een groep voor het automatisch schalen van een pool, die is mislukt vanwege onvoldoende voorbeeld gegevens.

```
{
    "id": "myPool1",
    "timestamp": "2020-09-21T18:59:56.204Z",
    "formula": "...",
    "results": "...",
    "error": {
        "code": "InsufficientSampleData",
        "message": "Autoscale evaluation failed due to insufficient sample data",
        "values": [{
                "name": "Message",
                "value": "Line 15, Col 44: Insufficient data from data set: $RunningTasks wanted 70%, received 50%"
            }
        ]
    }
}
```

|Element|Type|Opmerkingen|
|-------------|----------|-----------|
|`id`|Tekenreeks|De ID van de pool.|
|`timestamp`|DateTime|De tijds tempel wanneer automatisch schalen wordt uitgevoerd.|
|`formula`|Tekenreeks|De formule die is gedefinieerd voor automatisch schalen.|
|`results`|Tekenreeks|Evaluatie resultaten voor alle variabelen die in de formule worden gebruikt.|
|[`error`](#error)|Complex type|De gedetailleerde fout voor automatisch schalen.|

###  <a name="error"></a><a name="error"></a> optreedt

|Elementnaam|Type|Opmerkingen|
|------------------|----------|-----------|
|`code`|Tekenreeks|Een id voor de fout bij automatisch schalen. Codes zijn niet-variabel en zijn bedoeld om programmatisch te worden verbruikt.|
|`message`|Tekenreeks|Een bericht met een beschrijving van de fout automatisch schalen, bedoeld om te worden weer gegeven in een gebruikers interface.|
|`values`|Matrix|Lijst met naam/waarde-paren waarin meer details van de fout voor automatisch schalen worden beschreven.|
