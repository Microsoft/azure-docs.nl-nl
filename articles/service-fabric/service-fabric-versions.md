---
title: De versie van uw Azure Service Fabric-cluster bijwerken
description: Meer informatie over cluster versies in azure Service Fabric, met inbegrip van een koppeling naar de nieuwste releases van de Service Fabric-team blog.
ms.topic: troubleshooting
ms.date: 06/15/2020
ms.openlocfilehash: 5abfe83fcb68fcab7df22f1fd266cc695f2b9c80
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99549065"
---
# <a name="upgrade-your-azure-service-fabric-cluster-version"></a>De versie van uw Azure Service Fabric-cluster bijwerken

Zorg ervoor dat uw cluster altijd een ondersteunde versie van Azure Service Fabric uitvoert. Een minimum van 60 dagen nadat de release van een nieuwe versie van Service Fabric is aangekondigd, wordt de ondersteuning voor de vorige versie beëindigd. U vindt meldingen over nieuwe releases op het blog van het [service Fabric-team](https://azure.microsoft.com/updates/?product=service-fabric).

Voor elke versie van de Service Fabric runtime kunt u de opgegeven of oudere versies van de SDK-NuGet-pakketten gebruiken. Nieuwere versies van de pakketten zijn mogelijk niet in staat om oudere clusters te richten. Oudere clusters kunnen wijzigingen in de functie of het protocol hebben die de nieuwere pakket omgevingen niet ondersteunen.

Raadpleeg de volgende artikelen voor meer informatie over hoe u uw cluster met een ondersteunde Service Fabric versie kunt blijven gebruiken:

- [Een Azure Service Fabric-cluster upgraden](service-fabric-cluster-upgrade.md)
- [Een upgrade uitvoeren van de Service Fabric versie die wordt uitgevoerd op uw zelfstandige Windows Server-cluster](service-fabric-cluster-upgrade-windows-server.md)

## <a name="unsupported-versions"></a>Niet-ondersteunde versies

### <a name="upgrade-alert-for-versions-between-57-and-6363"></a>Upgrade waarschuwing voor versies tussen 5,7 en 6.3.63. *

Azure-infra structuur heeft een wijziging aangebracht die van invloed kan zijn op Service Fabric klanten om de beveiliging en beschik baarheid te verbeteren. Deze wijziging is van invloed op alle Service Fabric clusters met versie 5,7 tot 6,3.

Een update voor de Service Fabric runtime is beschikbaar voor alle ondersteunde versies van Service Fabric in alle regio's. Voer een upgrade uit naar een van de meest recente ondersteunde versies van 19 januari 2021 om service onderbrekingen te voor komen.

Als u een ondersteunings abonnement hebt en u technische hulp nodig hebt, kunt u contact met de ondersteunings kanalen van Azure bereiken. Open een ondersteunings aanvraag voor Azure Service Fabric en vermeld deze context in het ondersteunings ticket.

#### <a name="if-you-dont-upgrade-to-a-supported-version"></a>Als u niet bijwerkt naar een ondersteunde versie

Azure Service Fabric-clusters die worden uitgevoerd op versies van 5,7 naar 6.3.63. * zijn niet beschikbaar als u niet hebt bijgewerkt met 19 januari 2021.

#### <a name="required-action"></a>Vereiste actie

Voer een upgrade uit naar een ondersteunde versie van Service Fabric om uitval tijd of verlies van functionaliteit die betrekking heeft op deze wijziging te voor komen. Zorg ervoor dat op uw clusters ten minste de volgende versies worden uitgevoerd om problemen in uw omgeving te voor komen.

> [!Note]
> **Alle uitgebrachte versies van 7,2 bevatten de benodigde wijzigingen**.
  
  | Besturingssysteem | Huidige Service Fabric runtime in het cluster | Release van CU/patch |
  | --- | --- |--- |
  | Windows | 7,0. * | 7.0.478.9590 |
  | Windows | 7,1. * | 7.1.503.9590 |
  | Windows | 7,2. * | 7,2. * |
  | Ubuntu 16 | 7,0. * | 7.0.472.1  |
  | Linux Ubuntu 16,04 | 7,1. * | 7.1.455.1  |
  | Linux Ubuntu 18,04 | 7,1. * | 7.1.455.1804 |
  | Linux Ubuntu 16,04 | 7,2. * | 7,2. * |
  | Linux Ubuntu 18,04 | 7,2. * | 7,2. * |

### <a name="upgrade-alert-for-versions-later-than-63"></a>Upgrade waarschuwing voor versies die hoger zijn dan 6,3

Azure-infra structuur heeft een wijziging aangebracht die van invloed kan zijn op Service Fabric klanten om de beveiliging en beschik baarheid te verbeteren. Deze wijziging is van invloed op alle Service Fabric clusters die gebruikmaken [van de open-netwerk modus voor containers](./service-fabric-networking-modes.md#set-up-open-networking-mode) en voert versie 6,3 uit op 7,0 of niet-compatibele ondersteunde versies later dan 7,0. Een update voor de Service Fabric runtime is beschikbaar voor alle ondersteunde versies van Service Fabric in alle regio's.

#### <a name="if-you-dont-upgrade-to-a-supported-version"></a>Als u niet bijwerkt naar een ondersteunde versie

Azure Service Fabric-clusters die worden uitgevoerd op niet-verouderde versies van meer dan 6,3, zullen de functionaliteit of service onderbrekingen ondervinden als ze niet meer dan 19 januari 2021 op een ondersteunde versie werden bijgewerkt.
  
  - **Voor clusters met een versie van service Fabric hoger dan 6,3, die niet gebruikmaken van de open-netwerk functie**, blijft het cluster actief.

 - **Voor clusters met een versie van service Fabric hoger dan 6,3 en met de [open-netwerk functie voor containers](https://docs.microsoft.com/azure/service-fabric/service-fabric-networking-modes#set-up-open-networking-mode)** , kan het cluster niet meer beschikbaar zijn en werkt het niet meer, waardoor er service onderbrekingen kunnen ontstaan voor uw workloads.
 
 -   **Voor clusters met [Windows-versies tussen 7.0.457 en 7.0.466 (beide versies inbegrepen)](https://docs.microsoft.com/azure/service-fabric/service-fabric-versions#supported-version-names) en Windows-besturings systeem is de functie Windows-containers ingeschakeld. Opmerking: Linux-versies 7.0.457, 7.0.464 en 7.0.465 worden niet beïnvloed**.
    - **Impact**: het cluster werkt niet meer, waardoor er service onderbrekingen kunnen ontstaan voor uw workloads.
    
#### <a name="required-action"></a>Vereiste actie

Om uitval tijd of verlies van functionaliteit te voor komen, moet u ervoor zorgen dat uw clusters een van de volgende versies uitvoeren.

De versies van Service Fabric in de tabel bevatten de benodigde wijzigingen om verlies van functionaliteit te voor komen. Zorg ervoor dat u een van deze versies gebruikt.  

> [!Note]
> **Azure service Fabric-clusters die worden uitgevoerd op versie 6,5, moeten meerdere upgrades tegelijk uitvoeren voordat de infrastucuture worden gewijzigd om verlies van functionaliteit aan het cluster te voor komen**. 
>   -   1. Voer een upgrade uit naar 7.0.466. **Clusters waarop het Windows-besturings systeem wordt uitgevoerd en waarop de functie Windows-containers is ingeschakeld, kunnen zich niet op deze tussenliggende versie betreden. De volgende stap (II) moet hieronder worden uitgevoerd. dat wil zeggen.  Voer een upgrade uit naar veiliger en compatibele versie om service onderbrekingen te voor komen**
>   -   2. Voer een upgrade uit naar de meest recente versie van de klacht in 7,0 * release (7.0.478) of een van de hogere versies die hieronder worden weer gegeven.


> [!Note]
> **Alle release versies van 7,2 bevatten de benodigde wijzigingen**.

 | Besturingssysteem | Huidige Service Fabric runtime in het cluster | Release van CU/patch |
  | --- | --- |--- |
  | Windows | 7,0. * | 7.0.478.9590 |
  | Windows | 7,1. * | 7.1.503.9590 |
  | Windows | 7,2. * | 7,2. * |
  | Linux Ubuntu 16,04 | 7,0. * | 7.0.472.1  |
  | Linux Ubuntu 16,04 | 7,1. * | 7.1.455.1  |
  | Linux Ubuntu 18,04 | 7,1. * | 7.1.455.1804 |
  | Linux Ubuntu 16,04 | 7,2. * | 7,2. * |
  | Linux Ubuntu 18,04 | 7,2. * | 7,2. * |

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
| 7.1.510.* | 7.0.466.* |Kleiner dan of gelijk aan versie 4,1 |31 maart 2021 |
| 7.2.413.* | 7.0.470.* |Kleiner dan of gelijk aan versie 4,2 |Huidige versie, dus geen eind datum |
| 7.2.432.* | 7.0.470.* |Kleiner dan of gelijk aan versie 4,2 |Huidige versie, dus geen eind datum |
| 7.2.433.* | 7.0.470.* |Kleiner dan of gelijk aan versie 4,2 |Huidige versie, dus geen eind datum |
| 7.2.445.* | 7.0.470.* |Kleiner dan of gelijk aan versie 4,2 |Huidige versie, dus geen eind datum |
| 7.2.452.* | 7.0.470.* |Kleiner dan of gelijk aan versie 4,2 |Huidige versie, dus geen eind datum |

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
| 5,3 RTO | 5.3.121.9494 | Niet van toepassing|
| 5,3 CU1 | 5.3.204.9494 | Niet van toepassing|
| 5,3 CU2 | 5.3.301.9590 | Niet van toepassing|
| 5,3 CU3 | 5.3.311.9590 | Niet van toepassing|
| 5,4 CU2 | 5.4.164.9494 | Niet van toepassing|
| 5,5 CU1 | 5.5.216.0    | Niet van toepassing|
| 5,5 CU2 | 5.5.219.0 | Niet van toepassing|
| 5,5 CU3 | 5.5.227.0 | Niet van toepassing|
| 5,5 CU4 | 5.5.232.0 | Niet van toepassing|
| 5,6 RTO | 5.6.204.9494 | Niet van toepassing|
| 5,6 CU2 | 5.6.210.9494 | Niet van toepassing|
| 5,6 CU3 | 5.6.220.9494 | Niet van toepassing|
| 5,7 RTO | 5.7.198.9494 | Niet van toepassing|
| 5,7 CU4 | 5.7.221.9494 | Niet van toepassing|
| 6,0 RTO | 6.0.211.9494 | 6.0.120.1 |
| 6,0 CU1 | 6.0.219.9494 | 6.0.127.1 |
| 6,0 CU2 | 6.0.232.9494 | 6.0.133.1 |
| 6,1 CU1 | 6.1.456.9494 | 6.1.183.1 |
| 6,1 CU2 | 6.1.467.9494 | 6.1.185.1 |
| 6,1 CU3 | 6.1.472.9494 | Niet van toepassing|
| 6,1 CU4 | 6.1.480.9494 | 6.1.187.1 |
| 6,2 RTO | 6.2.269.9494 | 6.2.184.1 |
| 6,2 CU1 | 6.2.274.9494 | 6.2.191.1 |
| 6,2 CU2 | 6.2.283.9494 | 6.2.194.1 |
| 6,2 CU3 | 6.2.301.9494 | 6.2.199.1 |
| 6,3 RTO | 6.3.162.9494 | 6.3.119.1 |
| 6,3 CU1 | 6.3.176.9494 | 6.3.124.1 |
| 6,3 CU1 | 6.3.187.9494 | 6.3.129.1 |
| 6,4 RTO | 6.4.617.9590 | 6.4.625.1 |
| 6,4 CU2 | 6.4.622.9590 | Niet van toepassing|
| 6,4 CU3 | 6.4.637.9590 | 6.4.634.1 |
| 6,4 CU4 | 6.4.644.9590 | 6.4.639.1 |
| 6,4 CU5 | 6.4.654.9590 | 6.4.649.1 |
| 6,4 CU6 | 6.4.658.9590 | Niet van toepassing|
| 6,4 CU7 | 6.4.664.9590 | 6.4.661.1 |
| 6,4 CU8 | 6.4.670.9590 | Niet van toepassing|
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
| 7,1 CU10 | 7.1.510.9590 | NA |
| 7,2 RTO | 7.2.413.9590 | NA |
| 7,2 CU2 | 7.2.432.9590 | 7.2.431.1 |
| 7,2 CU3 | 7.2.433.9590 | Niet van toepassing|
| 7,2 CU4 | 7.2.445.9590 | 7.2.447.1 |
| 7,2 CU5 | 7.2.452.9590 | 7.2.454.1 |
