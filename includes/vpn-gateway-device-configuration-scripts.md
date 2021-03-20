---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/07/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e84a77629026bb8885a48b8ed928699825f07115
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "77111241"
---
| **Leverancier** | **Apparaatfamilie** | **Firmwareversie** |
| --- | --- | --- |
|Cisco | ISR| IOS 15.1 (preview)|
|Cisco | ASA | ASA ( * ) RouteBased (IKEv2- geen BGP) voor ASA-versies ouder dan 9.8 |
|Cisco | ASA | ASA RouteBased (IKEv2 - geen BGP) voor ASA 9.8+ |
|Juniper | SRX_GA | 12.x|
|Juniper | SSG_GA | ScreenOS 6.2.x|
|Juniper | JSeries_GA | JunOS 12.x|
|Juniper | SRX | JunOS 12.x RouteBased BGP |
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased VTI|
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased BGP|

> [!NOTE]
> (*) Vereist: NarrowAzureTrafficSelectors (UsePolicyBasedTrafficSelectors-optie inschakelen) en CustomAzurePolicies (IKE/IPsec)
>
