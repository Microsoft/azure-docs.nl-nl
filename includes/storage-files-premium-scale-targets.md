---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/10/2020
ms.author: rogarana
ms.openlocfilehash: 8dcb58499113b0b7ae0814419f0a76965a0ed945
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94680719"
---
#### <a name="additional-premium-file-share-level-limits"></a>Extra limieten voor Premium file share-niveaus

|Gebied  |Doel  |
|---------|---------|
|Toename van minimale grootte/afname    |1 GiB      |
|IOPS basis lijn    |1 IOPS per GiB, Maxi maal 100.000|
|IOPS-bursting    |3x IOPS per GiB, Maxi maal 100.000|
|Uitgangs frequentie         |60-MiB/s + 0,06 * ingerichte GiB        |
|Ingangs frequentie| 40-MiB/s + 0,04 * ingerichte GiB |

#### <a name="file-level-limits"></a>Limieten voor bestands niveau

|Gebied  |Standaard bestand  |Premium-bestand  |
|---------|---------|---------|
|Grootte     |1 TiB         |4 TiB         |
|Maximum aantal IOPS per bestand      |1000         |Maxi maal 8.000 *         |
|Gelijktijdige ingangen     |2.000         |2.000         |
|Uitgaand verkeer     |Zie standaard waarden voor doorvoer bestanden         |300 MiB per seconde (Maxi maal 1 GiB/s met SMB meerdere kanalen preview) * *         |
|Inkomend verkeer     |Zie standaard waarden voor doorvoer bestanden         |200 MiB per seconde (Maxi maal 1 GiB/s met SMB meerdere kanalen preview) * *        |
|Doorvoer     |Maxi maal 60 MiB per seconde         |Zie Premium file ingress/uitgangs waarden         |

\*<sup>Is van toepassing op het lezen en schrijven van Ios (doorgaans kleinere io-grootten <= 64k). Meta gegevensbewerkingen, behalve Lees-en schrijf bewerkingen, kunnen lager zijn. </sup>

\*\*<sup>Onderhevig aan netwerk limieten voor computers, beschik bare band breedte, i/o-grootte, wachtrij diepte en andere factoren. Zie prestaties van [SMB meerdere kanalen](../articles/storage/files/storage-files-smb-multichannel-performance.md)voor meer informatie. </sup>
