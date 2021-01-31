---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 01/27/2021
ms.author: rogarana
ms.openlocfilehash: 7da7c2fbb49a9dd936762b23f3c251d2142c52fd
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2021
ms.locfileid: "99221794"
---
| Resource | Standaardbestandsshares\* | Premiumbestandsshares |
|----------|---------------|------------------------------------------|
| Minimale grootte van een bestandsshare | Geen minimum; betalen naar gebruik | 100 GiB; ingericht |
| Maximale grootte van een bestandsshare | 100 TiB\*\*, 5 TiB | 100 TiB |
| Maximale grootte van een bestand in een bestandsshare | 4 TiB | 4 TiB |
| Maximum aantal bestanden in een bestandsshare | Geen limiet | Geen limiet |
| Maximum aantal IOPS per share | 10.000 IOPS\*\*, 1000 IOPS of 100 aanvragen in 100 ms | 100.000 IOPS |
| Maximaal aantal opgeslagen toegangsbeleidsregels per bestandsshare | 5 | 5 |
| Doeldoorvoer voor één bestandsshare | tot 300 MiB/sec\*\*, tot 60 MiB/sec,  | Bekijk de inkomende en uitgaande waarden voor premiumbestandsshares|
| Maximum aantal uitgaande waarden voor één bestandsshare | Bekijk de standaard doeldoorvoer voor bestandsshares | Maximaal 6204 MiB/s |
| Maximum aantal inkomende waarden voor één bestandsshare | Bekijk de standaard doeldoorvoer voor bestandsshares | Maximaal 4136 MiB/s |
| Maximum aantal open ingangen per bestand of map | 2000 open ingangen | 2000 open ingangen |
| Maximum aantal momentopnamen van shares | 200 momentopnamen van shares | 200 momentopnamen van shares |
| Maximum lengte van de naam van het object (mappen en bestanden) | 2048 tekens | 2048 tekens |
| Maximum pathname-onderdeel (in het pad \A\B\C\D is elke letter een onderdeel) | 255 tekens | 255 tekens |
| Limiet voor vaste koppelingen (alleen NFS) | N.v.t. | 178 |
| Maximumaantal SMB Multichannel-kanalen | N.v.t. | 4 |

\* De limieten voor standaard bestandsshares zijn van toepassing op alle drie beschikbare lagen voor standaard bestandsshares: geoptimaliseerd voor transacties, dynamisch en statisch.

\*\* Standaardbestandsshares zijn 5 TiB. Raadpleeg [Grote bestandsshares inschakelen en maken](../articles/storage/files/storage-files-how-to-create-large-file-share.md) voor meer informatie over hoe u de standaardbestandsshares kunt uitbreiden tot 100 TiB.
