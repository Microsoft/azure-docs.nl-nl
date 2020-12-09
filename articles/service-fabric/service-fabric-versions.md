---
title: Ondersteunde cluster versies in azure Service Fabric
description: Meer informatie over cluster versies in azure Service Fabric, met inbegrip van een koppeling naar de nieuwste releases van de Service Fabric-team blog.
ms.topic: troubleshooting
ms.date: 06/15/2020
ms.openlocfilehash: 46b1a7845a0da1ede30ed450f1e673f40e450105
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905380"
---
# <a name="supported-service-fabric-versions"></a>Ondersteunde Service Fabric versies

Zorg ervoor dat uw cluster altijd een ondersteunde versie van Azure Service Fabric uitvoert. Een minimum van 60 dagen nadat de release van een nieuwe versie van Service Fabric is aangekondigd, wordt de ondersteuning voor de vorige versie beëindigd. U vindt meldingen over nieuwe releases op het blog van het [service Fabric-team](https://azure.microsoft.com/updates/?product=service-fabric).

Voor een bepaalde versie van de Service Fabric runtime kunt u de opgegeven of oudere versies van de SDK-NuGet-pakketten gebruiken. Nieuwere versies van de pakketten worden niet ondersteund en kunnen problemen hebben met het richten op oudere clusters, omdat deze mogelijk functies of protocol wijzigingen hebben die niet door deze omgevingen worden ondersteund.

Raadpleeg de volgende documenten voor meer informatie over hoe u uw cluster met een ondersteunde Service Fabric versie kunt blijven gebruiken:

- [Een Azure Service Fabric-cluster upgraden](service-fabric-cluster-upgrade.md)
- [Een upgrade uitvoeren van de Service Fabric versie die wordt uitgevoerd op uw zelfstandige Windows Server-cluster](service-fabric-cluster-upgrade-windows-server.md)

## <a name="supported-versions"></a>Ondersteunde versies

De volgende tabel bevat de versies van Service Fabric en de bijbehorende eind datums voor ondersteuning.

| Service Fabric runtime in het cluster | Kan rechtstreeks upgraden vanaf Cluster versie |Versie van compatibele SDK of NuGet-pakket | Einde van ondersteuning |
| --- | --- |--- | --- |
| Alle cluster versies vóór 5.3.121 | 5.1.158.* |Kleiner dan of gelijk aan versie 2,3 |20 januari 2017 |
| 5,3. * | 5.1.158.* |Kleiner dan of gelijk aan versie 2,3 |24 februari 2017 |
| 5,4. * | 5.1.158.* |Kleiner dan of gelijk aan versie 2,4 |10 mei 2017       |
| 5,5. * | 5.4.164.* |Kleiner dan of gelijk aan versie 2,5 |10 augustus 2017    |
| 5,6. * | 5.4.164.* |Kleiner dan of gelijk aan versie 2,6 |13 oktober 2017   |
| 5,7. * | 5.4.164.* |Kleiner dan of gelijk aan versie 2,7 |15 december 2017  |
| 6,0. * | 5.6.205.* |Kleiner dan of gelijk aan versie 2,8 |30 maart 2018     |
| 6,1. * | 5.7.221.* |Kleiner dan of gelijk aan versie 3,0 |15 juli 2018      |
| 6,2. * | 6.0.232.* |Kleiner dan of gelijk aan versie 3,1 |26 oktober 2018   |
| 6,3. * | 6.1.480.* |Kleiner dan of gelijk aan versie 3,2 |31 maart 2019  |
| 6,4. * | 6.2.301.* |Kleiner dan of gelijk aan versie 3,3 |15 september 2019 |
| 6,5. * | 6.4.617.* |Kleiner dan of gelijk aan versie 3,4 |1 augustus 2020 |
| 7.0.466.* | 6.4.664.* |Kleiner dan of gelijk aan versie 4,0|31 januari 2021  |
| 7.0.466.* | 6,5. * |Kleiner dan of gelijk aan versie 4,0|31 januari 2021 |
| 7.0.470.* | 7.0.466.* |Kleiner dan of gelijk aan versie 4,0 |31 januari 2021  |
| 7.0.472.* | 7.0.466.* |Kleiner dan of gelijk aan versie 4,0 |31 januari 2021  |
| 7.0.478.* | 7.0.466.* |Kleiner dan of gelijk aan versie 4,0 |31 januari 2021  |
| 7.1.409.* | 7.0.466.* |Kleiner dan of gelijk aan versie 4,1 |31 maart 2021 |
| 7.1.417.* | 7.0.466.* |Kleiner dan of gelijk aan versie 4,1 |31 maart 2021 |
| 7.1.428.* | 7.0.466.* |Kleiner dan of gelijk aan versie 4,1 |31 maart 2021 |
| 7.1.456.* | 7.0.466.* |Kleiner dan of gelijk aan versie 4,1 |31 maart 2021 |
| 7.1.458.* | 7.0.466.* |Kleiner dan of gelijk aan versie 4,1 |31 maart 2021 |
| 7.1.459.* | 7.0.466.* |Kleiner dan of gelijk aan versie 4,1 |31 maart 2021 |
| 7.1.503.* | 7.0.466.* |Kleiner dan of gelijk aan versie 4,1 |31 maart 2021 |
| 7.2.413.* | 7.0.470.* |Kleiner dan of gelijk aan versie 4,2 |Huidige versie, dus geen eind datum |
| 7.2.432.* | 7.0.470.* |Kleiner dan of gelijk aan versie 4,2 |Huidige versie, dus geen eind datum |
| 7.2.433.* | 7.0.470.* |Kleiner dan of gelijk aan versie 4,2 |Huidige versie, dus geen eind datum |
| 7.2.445.* | 7.0.470.* |Kleiner dan of gelijk aan versie 4,2 |Huidige versie, dus geen eind datum |

## <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

De volgende tabel geeft een lijst van de ondersteunde besturings systemen voor de ondersteunde Service Fabric versies.

| Besturingssysteem | Eerste ondersteunde Service Fabric versie |
| --- | --- |
| Windows Server 2012 R2 | Alle versies |
| Windows Server 2016 | Alle versies |
| Windows Server 1709 | 6.0 |
| Windows Server 1803 | 6.4 |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16,04 | 6.0 |
| Linux Ubuntu 18,04 | 7.1 |

## <a name="supported-version-names"></a>Ondersteunde versie namen

De volgende tabel bevat de versie namen van Service Fabric en de bijbehorende versie nummers.

| Versie naam | Windows-versienummer | Linux-versie nummer |
| --- | --- | --- |
| 5,3 RTO | 5.3.121.9494 | NA |
| 5,3 CU1 | 5.3.204.9494 | NA |
| 5,3 CU2 | 5.3.301.9590 | NA |
| 5,3 CU3 | 5.3.311.9590 | NA |
| 5,4 CU2 | 5.4.164.9494 | NA |
| 5,5 CU1 | 5.5.216.0    | NA |
| 5,5 CU2 | 5.5.219.0    | NA |
| 5,5 CU3 | 5.5.227.0    | NA |
| 5,5 CU4 | 5.5.232.0    | NA |
| 5,6 RTO | 5.6.204.9494 | NA |
| 5,6 CU2 | 5.6.210.9494 | NA |
| 5,6 CU3 | 5.6.220.9494 | NA |
| 5,7 RTO | 5.7.198.9494 | NA |
| 5,7 CU4 | 5.7.221.9494 | NA |
| 6,0 RTO | 6.0.211.9494 | 6.0.120.1 |
| 6,0 CU1 | 6.0.219.9494 | 6.0.127.1 |
| 6,0 CU2 | 6.0.232.9494 | 6.0.133.1 |
| 6,1 CU1 | 6.1.456.9494 | 6.1.183.1 |
| 6,1 CU2 | 6.1.467.9494 | 6.1.185.1 |
| 6,1 CU3 | 6.1.472.9494 | NA |
| 6,1 CU4 | 6.1.480.9494 | 6.1.187.1 |
| 6,2 RTO | 6.2.269.9494 | 6.2.184.1 | 
| 6,2 CU1 | 6.2.274.9494 | 6.2.191.1 |
| 6,2 CU2 | 6.2.283.9494 | 6.2.194.1 |
| 6,2 CU3 | 6.2.301.9494 | 6.2.199.1 |
| 6,3 RTO | 6.3.162.9494 | 6.3.119.1 |
| 6,3 CU1 | 6.3.176.9494 | 6.3.124.1 |
| 6,3 CU1 | 6.3.187.9494 | 6.3.129.1 |
| 6,4 RTO | 6.4.617.9590 | 6.4.625.1 |
| 6,4 CU2 | 6.4.622.9590 | NA |
| 6,4 CU3 | 6.4.637.9590 | 6.4.634.1 |
| 6,4 CU4 | 6.4.644.9590 | 6.4.639.1 |
| 6,4 CU5 | 6.4.654.9590 | 6.4.649.1 |
| 6,4 CU6 | 6.4.658.9590 | NA |
| 6,4 CU7 | 6.4.664.9590 | 6.4.661.1 |
| 6,4 CU8 | 6.4.670.9590 | NA |
| 6,5 RTO | 6.5.639.9590 | 6.5.435.1 |
| 6,5 CU1 | 6.5.641.9590 | 6.5.454.1 |
| 6,5 CU2 | 6.5.658.9590 | 6.5.460.1 |
| 6,5 CU3 | 6.5.664.9590 | 6.5.466.1 |
| 6,5 CU5 | 6.5.676.9590 | 6.5.467.1 |
| 7,0 RTO | 7.0.457.9590 | 7.0.457.1 |
| 7,0 CU2 | 7.0.464.9590 | 7.0.464.1 |
| 7,0 CU3 | 7.0.466.9590 | 7.0.465.1 |
| 7,0 CU4 | 7.0.470.9590 | 7.0.469.1 |
| 7,0 CU6 | 7.0.472.9590 | 7.0.471.1 |
| 7,0 CU9 | 7.0.478.9590 | 7.0.472.1 |
| 7,1 RTO | 7.1.409.9590 | 7.1.410.1 |
| 7,1 CU1 | 7.1.417.9590 | 7.1.418.1 |
| 7,1 CU2 | 7.1.428.9590 | 7.1.428.1 |
| 7,1 CU3 | 7.1.456.9590 | 7.1.452.1 |
| 7,1 CU5 | 7.1.458.9590 | 7.1.454.1 |
| 7,1 CU6 | 7.1.459.9590 | 7.1.455.1 |
| 7,1 CU8 | 7.1.503.9590 | 7.1.508.1 |
| 7,2 RTO | 7.2.413.9590 | NA |
| 7,2 CU2 | 7.2.432.9590 | 7.2.431.1 |
| 7,2 CU3 | 7.2.433.9590 | NA |
| 7,2 CU4 | 7.2.445.9590 | 7.2.447.1 |

