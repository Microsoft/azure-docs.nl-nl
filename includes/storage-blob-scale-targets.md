---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/30/2020
ms.author: tamram
ms.openlocfilehash: fe493ca4a2edf16bfda405e8b2c5581c0f745616
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91665592"
---
| Resource | Doel | Doel (preview) |
|-|-|-|
| Maximale grootte van één blob-container | Hetzelfde als de maximale capaciteit van het opslagaccount |  |
| Maximale aantal blokken in een blok-blob of toevoeg-blob | 50.000 blokken |  |
| Maximale grootte van een blok in een blok-blob | 100 MiB | 4000 MiB (preview) |
| Maximale grootte van een blok-blob | 50.000 x 100 MiB (ongeveer 4,75 TiB) | 50.000 x 4000 MiB (ongeveer 190,7 TiB) (preview) |
| Maximale grootte van een blok in een toevoeg-blob | 4 MiB |  |
| Maximale grootte van een toevoeg-blob | 50.000 x 4 MiB (ongeveer 195 GiB) |  |
| Maximale grootte van een pagina-blob | 8 TiB<sup>2</sup> |  |
| Maximale aantal opgeslagen toegangsbeleidsregels per blob-container | 5 |  |
| Aantal doelaanvragen voor één blob | Maximaal 500 aanvragen per seconde |  |
| Doeldoorvoer voor één pagina-blob | Maximaal 60 MiB per seconde<sup>2</sup> |  |
| Doeldoorvoer voor één blok-blob | Limieten voor het binnenkomende en uitgaande verkeer van het opslagaccount<sup>1</sup> |  |

<sup>1</sup> De doorvoer van één blob is afhankelijk van verschillende factoren, waaronder, maar niet beperkt tot: gelijktijdigheid, aanvraaggrootte, prestatielaag, bronsnelheid voor uploads en bestemming voor downloads. Upload grotere blobs of blokken om te profiteren van de prestatieverbeteringen van [Blok-blobs met een hoge doorvoer](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/). Roep voor standaard opslagaccounts met name de bewerking [Put blob](/rest/api/storageservices/put-blob) of [Put block](/rest/api/storageservices/put-block) aan met een blob- of blokgrootte van meer dan 4 MiB. Gebruik voor Premium-blok-blobs of Data Lake Storage Gen2-opslagaccounts een blok-of blobgrootte die groter is dan 256 KiB.

<sup>2</sup> Pagina-blobs worden nog niet ondersteund in accounts die de instelling **Hiërarchische naamruimte** hebben.

In de volgende tabel staan de maximale blok- en blobgroottes die per serviceversie zijn toegestaan.

| Serviceversie | Maximale blokgrootte (via Put Block) | Maximale blobgrootte (via Put Block List) | Maximale blobgrootte via enkele schrijfbewerking (via Put Blob) |
|-|-|-|-|
| Versie 2019-12-12 en hoger | 4000 MiB (preview) | Ongeveer 190,7 TiB (4000 MiB x 50.000 blokken) (preview) | 5000 MiB (preview) |
| Versie 2016-05-31 t/m versie 2019-07-07 | 100 MiB | Ongeveer 4,75 TiB (100 MiB x 50.000 blokken) | 256 MiB |
| Versies vóór 2016-05-31 | 4 MiB | Ongeveer 195 GiB (4 MiB x 50.000 blokken) | 64 MiB |
