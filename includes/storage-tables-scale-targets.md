---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/09/2020
ms.author: tamram
ms.openlocfilehash: 27617da97ced9ac775beae71e4c25202944b2aba
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "78940965"
---
In de volgende tabel worden de capaciteit, schaalbaarheid en prestatiedoelen voor Table Storage beschreven.

| Resource | Doel |
|----------|---------------|
| Aantal tabellen in een Azure Storage-account | Alleen beperkt door de capaciteit van het opslagaccount |
| Aantal partities in een tabel | Alleen beperkt door de capaciteit van het opslagaccount |
| Aantal entiteiten in een partitie | Alleen beperkt door de capaciteit van het opslagaccount |
| Maximale grootte van één tabel | 500 TiB |
| Maximale grootte van één entiteit, inclusief alle eigenschapswaarden | 1 MiB |
| Maximum aantal eigenschappen in een tabelentiteit | 255 (inclusief de drie systeemeigenschappen, **PartitionKey**, **RowKey** en **Timestamp**) |
| Maximale totale grootte van een afzonderlijke eigenschap in een entiteit | Verschilt per eigenschapstype. Zie voor meer informatie **Eigenschapstypen** in [Het gegevensmodel van de tabelservice](/rest/api/storageservices/understanding-the-table-service-data-model). |
| Grootte van de **PartitionKey** | Een tekenreeks met een maximale grootte van 1 KiB |
| Grootte van de **RowKey** | Een tekenreeks met een maximale grootte van 1 KiB |
| Grootte van een transactie van entiteitsgroepen | Een transactie kan maximaal 100 entiteiten bevatten en de payload moet kleiner zijn dan 4 MiB. Een transactie van entiteitsgroepen mag slechts één keer een update naar een entiteit bevatten. |
| Maximaal aantal opgeslagen toegangsbeleidsregels per tabel | 5 |
| Maximum aantal aanvragen per opslagaccount | 20.000 transacties per seconde, uitgaande van een entiteitsgrootte van 1 KiB |
| Doeldoorvoer voor één tabelpartitie (entiteiten van 1 KiB) | Maximaal 2000 entiteiten per seconde |