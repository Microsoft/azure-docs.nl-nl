---
title: Limieten voor Azure VMware-oplossingen
description: Beperkingen van de Azure VMware-oplossing.
ms.topic: include
ms.date: 03/04/2021
ms.openlocfilehash: 99c27694a300284cfd99b8411306c90ad4d8a12e
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102193879"
---
<!-- Used in /azure/azure-resource-manager/management/azure-subscription-service-limits.md -->

In de volgende tabel worden de maximum limieten voor de Azure VMware-oplossing beschreven.

| **Resource** | **Limiet** |
| :-- | :-- |
| Clusters per privécloud | 4 |
| Minimum aantal knoop punten per cluster | 3 |
| Maximum aantal knoop punten per cluster | 16 |
| Knoop punten per privécloud | 64 |
| vCenter per privécloud | 1  |
| HCX-site koppeling | 3 met Advanced Edition, 10 met Enter prise Edition |
| Aantal gekoppelde SDDCs voor AVS ExpressRoute | 4 |
| AVS ExpressRoute portspeed | 10 Gbps | 
| Open bare Ip's die worden weer gegeven via vWAN | 100 |
| vSAN-capaciteits limieten | 75% van totaal bruikbaar (Beperk 25% beschikbaar voor SLA)  |

Gebruik voor andere VMware-specifieke limieten het [VMware-configuratie maximum tool!](https://configmax.vmware.com/).
