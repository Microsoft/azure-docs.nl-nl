---
title: Limieten voor Azure VMware-oplossingen
description: Beperkingen van de Azure VMware-oplossing.
ms.topic: include
ms.date: 03/16/2021
ms.openlocfilehash: 0e2359d951f5348b69e95ab7fa046981b2b7b32d
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103621968"
---
<!-- Used in /azure/azure-resource-manager/management/azure-subscription-service-limits.md -->

In de volgende tabel worden de maximum limieten voor de Azure VMware-oplossing beschreven.

| **Resource** | **Limiet** |
| :-- | :-- |
| Clusters per privécloud | 12 |
| Minimum aantal knoop punten per cluster | 3 |
| Maximum aantal knoop punten per cluster | 16 |
| Knoop punten per privécloud | 64 |
| vCenter per privécloud | 1  |
| HCX-site koppeling | 3 met Advanced Edition, 10 met Enter prise Edition |
| Aantal gekoppelde SDDCs voor AVS ExpressRoute | 4 |
| AVS ExpressRoute portspeed | 10 Gbps<br />De virtuele netwerk gateway die wordt gebruikt, bepaalt de werkelijke band breedte. Zie [over ExpressRoute virtuele netwerk gateways](../../expressroute/expressroute-about-virtual-network-gateways.md) voor meer informatie. | 
| Open bare Ip's die worden weer gegeven via vWAN | 100 |
| vSAN-capaciteits limieten | 75% van totaal bruikbaar (Beperk 25% beschikbaar voor SLA)  |

Gebruik voor andere VMware-specifieke limieten het [VMware-configuratie maximum tool!](https://configmax.vmware.com/).
