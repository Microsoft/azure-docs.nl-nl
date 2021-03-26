---
title: Limieten voor Azure VMware-oplossingen
description: Beperkingen van de Azure VMware-oplossing.
ms.topic: include
ms.date: 03/24/2021
ms.openlocfilehash: 997a5ae96ff30226d055b7b966b128d7ec0ae5bd
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105582832"
---
<!-- Used in /azure/azure-resource-manager/management/azure-subscription-service-limits.md -->

In de volgende tabel worden de maximum limieten voor de Azure VMware-oplossing beschreven.

| **Resource** | **Limiet** |
| :-- | :-- |
| Clusters per privécloud | 12 |
| Minimum aantal knoop punten per cluster | 3 |
| Maximum aantal knoop punten per cluster | 16 |
| Knoop punten per privécloud | 96 |
| vCenter per privécloud | 1  |
| HCX-site koppeling | 3 met Advanced Edition, 10 met Enter prise Edition |
| Aantal gekoppelde SDDCs voor AVS ExpressRoute | 4 |
| AVS ExpressRoute portspeed | 10 Gbps<br />De virtuele netwerk gateway die wordt gebruikt, bepaalt de werkelijke band breedte. Zie [over ExpressRoute virtuele netwerk gateways](../../expressroute/expressroute-about-virtual-network-gateways.md) voor meer informatie. | 
| Open bare Ip's die worden weer gegeven via vWAN | 100 |
| vSAN-capaciteits limieten | 75% van totaal bruikbaar (Beperk 25% beschikbaar voor SLA)  |

Gebruik voor andere VMware-specifieke limieten het [VMware-configuratie maximum tool!](https://configmax.vmware.com/).
