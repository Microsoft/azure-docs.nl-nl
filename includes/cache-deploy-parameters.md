---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 04/02/2019
ms.author: wesmc
ms.openlocfilehash: 498a7ee28b9404d0733e4615f4df635a8c904b51
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "67175844"
---
### <a name="cacheskuname"></a>cacheSKUName

De prijs categorie van de nieuwe Azure-cache voor redis.

```json
    "cacheSKUName": {
      "type": "string",
      "allowedValues": [
        "Basic",
        "Standard",
        "Premium"
      ],
      "defaultValue": "Basic",
      "metadata": {
        "description": "The pricing tier of the new Azure Cache for Redis."
      }
    },
```

De sjabloon definieert de waarden die zijn toegestaan voor deze para meter (Basic, Standard of Premium) en wijst een standaard waarde (Basic) toe als er geen waarde is opgegeven. Basic biedt één knoop punt met meerdere grootten beschikbaar tot 53 GB. Standard voorziet in twee knoop punten: primair/replica met meerdere grootten beschikbaar tot 53 GB en 99,9% SLA.

### <a name="cacheskufamily"></a>cacheSKUFamily

De familie voor de SKU.

```json
    "cacheSKUFamily": {
      "type": "string",
      "allowedValue/s": [
        "C",
        "P"
      ],
      "defaultValue": "C",
      "metadata": {
        "description": "The family for the sku."
      }
    },
```

### <a name="cacheskucapacity"></a>cacheSKUCapacity

De grootte van de nieuwe Azure-cache voor redis-instantie.

Voor de Basic-en Standard-families:

```json
    "cacheSKUCapacity": {
      "type": "int",
      "allowedValues": [
        0,
        1,
        2,
        3,
        4,
        5,
        6
      ],
      "defaultValue": 0,
      "metadata": {
        "description": "The size of the new Azure Cache for Redis instance. "
      }
    }
```

De Premium-waarde cache capaciteit wordt hetzelfde gedefinieerd, behalve de toegestane waarden worden uitgevoerd van 1 tot 5 in plaats van tussen 0 en 6.

De sjabloon definieert de gehele waarden die zijn toegestaan voor deze para meter (0 tot en met 6 voor de Basic-en Standard-families, 1 t/m 5 voor de Premium-serie). Als er geen waarde is opgegeven, wijst de sjabloon een standaard waarde van 0 toe voor Basic en Standard, 1 voor Premium.

De waarden komen overeen met de volgende cache grootten:

| Waarde | Basis en standaard<br>cache grootte | Premium<br>cache grootte |
| :---: | :------------------------------: | :-------------------: |
| 0     | 250 MB (standaard)                 | n.v.t.                   |
| 1     | 1 GB                             | 6 GB (standaard)        |
| 2     | 2,5 GB                           | 13 GB                 |
| 3     | 6 GB                             | 26 GB                 |
| 4     | 13 GB                            | 53 GB                 |
| 5     | 26 GB                            | 120 GB                |
| 6     | 53 GB                            | n.v.t.                   |
