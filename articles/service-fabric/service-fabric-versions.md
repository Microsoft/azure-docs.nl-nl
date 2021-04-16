---
title: Azure Service Fabric-versies
description: Meer informatie over clusterversies in Azure Service Fabric en platformversies die actief worden ondersteund
ms.topic: troubleshooting
ms.date: 04/12/2021
ms.openlocfilehash: 4e656b0cd051079d4504ee2a55a9bf6993065396
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515308"
---
# <a name="service-fabric-supported-versions"></a>Service Fabric ondersteunde versies
In de tabellen in dit artikel worden de Service Fabric en platformversies beschreven die actief worden ondersteund.

## <a name="windows"></a>Windows

| Service Fabric runtime |Kan rechtstreeks vanuit upgraden|Kan downgraden naar|Compatibele SDK of NuGet-pakketversie|Ondersteunde dotnet-runtimes** |Versie van het besturingssysteem |Einde van ondersteuning |
| --- | --- | --- | --- | --- | --- | --- |
| 8.0 RTO | 7.1 CU10 | 7.2 | Kleiner dan of gelijk aan versie 5.0 | .NET 5.0 (GA), .NET Core 3.1, .NET Core 2.1, <br>All >=4.5 .NET Full Framework| [Zie ondersteunde versie van het besturingssysteem](#supported-windows-versions-and-support-end-date) | Huidige versie |
| 7.2 CU7 | 7.0 CU9 | 7.1 | Kleiner dan of gelijk aan versie 4.2 | .NET 5.0 (preview-ondersteuning), .NET Core 3.1, .NET Core 2.1,<br>All >= 4.5 Net Full Framework | [Zie ondersteunde versie van het besturingssysteem](#supported-windows-versions-and-support-end-date) | 30 november 2021 |
| 7.2 CU6 | 7.0 CU4 |7.1 | Kleiner dan of gelijk aan versie 4.2 | .NET 5.0 (preview-ondersteuning), .NET Core 3.1, .NET Core 2.1,<br>All >= 4.5 Net Full Framework | [Zie ondersteunde versie van het besturingssysteem](#supported-windows-versions-and-support-end-date)| 30 november 2021 |
| 7.2 RTO-CU5 | 7.0 CU4 | 7.1 |Kleiner dan of gelijk aan versie 4.2 | .NET Core 3.1, .NET Core 2.1,<br>All >= 4.5 Net Full Framework | [Zie ondersteunde versie van het besturingssysteem](#supported-windows-versions-and-support-end-date)| 30 november 2021 |
| 7.1 |7.0 CU3 |N.v.t. | Kleiner dan of gelijk aan versie 4.1 | .NET Core 3.1, .NET Core 2.1,<br>All >= 4.5 Net Full Framework | [Zie ondersteunde versie van het besturingssysteem](#supported-windows-versions-and-support-end-date) | 31 juli 2021 |

** Service Fabric geen .NET Core-runtime. De serviceauteur moet ervoor zorgen dat deze <a href="https://docs.microsoft.com/dotnet/core/deploying/">beschikbaar is.</a>

## <a name="supported-windows-versions-and-support-end-date"></a>Ondersteunde Windows-versies en ondersteuning voor einddatum
Ondersteuning voor Service Fabric een specifiek besturingssysteem wordt beëindigd wanneer de ondersteuning voor de versie van het besturingssysteem het einde van de levensduur bereikt.


### <a name="windows-server"></a>Windows Server

| Besturingssysteemversie | Service Fabric einddatum van ondersteuning | Koppeling naar levenscyclus van het besturingssysteem |
|---|---|---|
|Windows Server 2019|1/9/2029|<a href="https://docs.microsoft.com/lifecycle/products/windows-server-2019">Windows Server 2019 - Microsoft Lifecycle</a>|
|Windows Server 2016 |1/12/2027|<a href="https://docs.microsoft.com/lifecycle/products/windows-server-2016">Windows Server 2016 - Microsoft Lifecycle</a>|
|Windows Server 2012 R2 |10/10/2023|<a href="https://docs.microsoft.com/lifecycle/products/windows-server-2012-r2">Windows Server 2012 R2 - Microsoft Lifecycle</a>|
|Versie 20H2 |5/10/2022|<a href="https://docs.microsoft.com/lifecycle/products/windows-server">Windows Server - Microsoft Lifecycle</a>|
|Versie 2004 |12/14/2021|<a href="https://docs.microsoft.com/lifecycle/products/windows-server">Windows Server - Microsoft Lifecycle</a>|
|Versie 1909 |5/11/2021|<a href="https://docs.microsoft.com/lifecycle/products/windows-server">Windows Server - Microsoft Lifecycle</a>|

<br>

### <a name="windows-10"></a>Windows 10

| Besturingssysteemversie | Service Fabric einddatum van ondersteuning | Koppeling naar levenscyclus van besturingssysteem |
| --- | --- | --- |
| Windows 10 2019 LTSC | 1/9/2029 | <a href="https://docs.microsoft.com/lifecycle/products/windows-10-2019-ltsc">Windows 10 2019 LTSC - Microsoft Lifecycle</a> |
| Versie 20H2 | 5/9/2023 | <a href="https://docs.microsoft.com/lifecycle/products/windows-10-enterprise-and-education">Windows 10 Enterprise and Education - Microsoft Lifecycle</a> |
| Versie 2004 | 12/14/2021| <a href="https://docs.microsoft.com/lifecycle/products/windows-10-enterprise-and-education">Windows 10 Enterprise and Education - Microsoft Lifecycle</a> |
| Versie 1909 | 5/10/2022 | <a href="https://docs.microsoft.com/lifecycle/products/windows-10-enterprise-and-education">Windows 10 Enterprise and Education - Microsoft Lifecycle</a> |
| Versie 1809 | 5/11/2021 | <a href="https://docs.microsoft.com/lifecycle/products/windows-10-enterprise-and-education">Windows 10 Enterprise and Education - Microsoft Lifecycle</a> |
| Versie 1803 | 5/11/2021 | <a href="https://docs.microsoft.com/lifecycle/products/windows-10-enterprise-and-education">Windows 10 Enterprise and Education - Microsoft Lifecycle</a> |

## <a name="linux"></a>Linux

| Service Fabric runtime | Kan rechtstreeks vanuit upgraden |Kan downgraden naar |Compatibele SDK of NuGet-pakketversie | Ondersteunde dotnet-runtimes** | Besturingssysteemversie | Einde van ondersteuning |
| --- | --- | --- | --- | --- | --- | --- |
| 8.0 RTO | 7.1 CU8 | 7.2 | Kleiner dan of gelijk aan versie 5.0 | .NET Core 3.1, .NET Core 2.1 | [Zie ondersteunde versie van het besturingssysteem](#supported-linux-versions-and-support-end-date) | Huidige versie |
| 7.2 CU7 | 7.0 CU9 | 7.1 | Kleiner dan of gelijk aan versie 4.2 | .NET Core 3.1, .NET Core 2.1 | [Zie ondersteunde versie van het besturingssysteem](#supported-linux-versions-and-support-end-date) | 30 november 2021 |
| 7.2 RTO-CU6 | 7.0 CU4 | 7.1 | Kleiner dan of gelijk aan versie 4.2 | .NET Core 3.1, .NET Core 2.1 | [Zie ondersteunde versie van het besturingssysteem](#supported-linux-versions-and-support-end-date) | 30 november 2021 |
| 7.1 | 7.0 CU3 | N.v.t. | Kleiner dan of gelijk aan versie 4.1 | .NET Core 3.1, .NET Core 2.1 | [Zie ondersteunde versie van het besturingssysteem](#supported-linux-versions-and-support-end-date) | 31 juli 2021 |

** Service Fabric geen .NET Core-runtime biedt en de serviceauteur is verantwoordelijk om ervoor te zorgen dat <a href="https://docs.microsoft.com/dotnet/core/deploying/">deze</a> beschikbaar is

## <a name="supported-linux-versions-and-support-end-date"></a>Ondersteunde Linux-versies en einddatum van ondersteuning
Ondersteuning voor Service Fabric een specifiek besturingssysteem wordt beëindigd wanneer de ondersteuning voor de versie van het besturingssysteem het einde van de levensduur bereikt.

#### <a name="ubuntu"></a>Ubuntu
| Besturingssysteemversie | Service Fabric einddatum van ondersteuning| Koppeling naar levenscyclus van besturingssysteem |
| --- | --- | --- |
| Ubuntu 18.04 | April 2028 | <a href="https://wiki.ubuntu.com/Releases">Levenscyclus van Ubuntu</a>|
| Ubuntu 16.04 | April 2024 | <a href="https://wiki.ubuntu.com/Releases">Ubuntu-levenscyclus</a>|

<br>

## <a name="service-fabric-version-name-and-number-reference"></a>Service Fabric versienaam en nummerverwijzing
De volgende tabel bevat de versienamen van Service Fabric en de bijbehorende versienummers.

| Versienaam | Windows-versienummer | Linux-versienummer |
| --- | --- | --- |
| 8.0 RTO | 8.0.514.9590 | 8.0.513.1 | 
| 7.2 CU7 | 7.2.477.9590 | 7.2.476.1 |
| 7.2 CU6 | 7.2.457.9590 | 7.2.456.1 |
| 7.2 CU5 | 7.2.452.9590 | 7.2.454.1 |
| 7.2 CU4 | 7.2.445.9590 | 7.2.447.1 |
| 7.2 CU3 | 7.2.433.9590 | NA |
| 7.2 CU2 | 7.2.432.9590 | 7.2.431.1 |
| 7.2 RTO | 7.2.413.9590 | NA |
| 7.1 CU10 | 7.1.510.9590 | NA |
| 7.1 CU8 | 7.1.503.9590 | 7.1.508.1 |
| 7.1 CU6 | 7.1.459.9590 | 7.1.455.1 |
| 7.1 CU5 | 7.1.458.9590 | 7.1.454.1 |
| 7.1 CU3 | 7.1.456.9590 | 7.1.452.1 |
| 7.1 CU2 | 7.1.428.9590 | 7.1.428.1 |
| 7.1 CU1 | 7.1.417.9590 | 7.1.418.1 |
| 7.1 RTO | 7.1.409.9590 | 7.1.410.1 |
| 7.0 CU9 | 7.0.478.9590 | 7.0.472.1 |
| 7.0 CU6 | 7.0.472.9590 | 7.0.471.1 |
| 7.0 CU4 | 7.0.470.9590 | 7.0.469.1 |
| 7.0 CU3 | 7.0.466.9590 | 7.0.465.1 |
| 7.0 CU2 | 7.0.464.9590 | 7.0.464.1 |
| 7.0 RTO | 7.0.457.9590 | 7.0.457.1 |
| 6.5 CU5 | 6.5.676.9590 | 6.5.467.1 |
| 6.5 CU3 | 6.5.664.9590 | 6.5.466.1 |
| 6.5 CU2 | 6.5.658.9590 | 6.5.460.1 |
| 6.5 CU1 | 6.5.641.9590 | 6.5.454.1 |
| 6.5 RTO | 6.5.639.9590 | 6.5.435.1 |
| 6.4 CU8 | 6.4.670.9590 | Niet van toepassing|
| 6.4 CU7 | 6.4.664.9590 | 6.4.661.1 |
| 6.4 CU6 | 6.4.658.9590 | Niet van toepassing|
| 6.4 CU5 | 6.4.654.9590 | 6.4.649.1 |
| 6.4 CU4 | 6.4.644.9590 | 6.4.639.1 |
| 6.4 CU3 | 6.4.637.9590 | 6.4.634.1 |
| 6.4 CU2 | 6.4.622.9590 | Niet van toepassing|
| 6.4 RTO | 6.4.617.9590 | 6.4.625.1 |
| 6.3 CU1 | 6.3.187.9494 | 6.3.129.1 |
| 6.3 RTO | 6.3.162.9494 | 6.3.119.1 |
| 6.2 CU3 | 6.2.301.9494 | 6.2.199.1 |
| 6.2 CU2 | 6.2.283.9494 | 6.2.194.1 |
| 6.2 CU1 | 6.2.274.9494 | 6.2.191.1 |
| 6.2 RTO | 6.2.269.9494 | 6.2.184.1 |
| 6.1 CU4 | 6.1.480.9494 | 6.1.187.1 |
| 6.1 CU3 | 6.1.472.9494 | Niet van toepassing|
| 6.1 CU2 | 6.1.467.9494 | 6.1.185.1 |
| 6.1 CU1 | 6.1.456.9494 | 6.1.183.1 |
| 6.0 CU2 | 6.0.232.9494 | 6.0.133.1 |
| 6.0 CU1 | 6.0.219.9494 | 6.0.127.1 |
| 6.0 RTO | 6.0.211.9494 | 6.0.120.1 |
| 5.7 CU4 | 5.7.221.9494 | Niet van toepassing|
| 5.7 RTO | 5.7.198.9494 | Niet van toepassing|
| 5.6 CU3 | 5.6.220.9494 | Niet van toepassing|
| 5.6 CU2 | 5.6.210.9494 | Niet van toepassing|
| 5.6 RTO | 5.6.204.9494 | Niet van toepassing|
| 5.5 CU4 | 5.5.232.0 | Niet van toepassing|
| 5.5 CU3 | 5.5.227.0 | Niet van toepassing|
| 5.5 CU2 | 5.5.219.0 | Niet van toepassing|
| 5.5 CU1 | 5.5.216.0    | Niet van toepassing|
| 5.4 CU2 | 5.4.164.9494 | Niet van toepassing|
| 5.3 CU3 | 5.3.311.9590 | Niet van toepassing|
| 5.3 CU2 | 5.3.301.9590 | Niet van toepassing|
| 5.3 CU1 | 5.3.204.9494 | Niet van toepassing|
| 5.3 RTO | 5.3.121.9494 | Niet van toepassing|
