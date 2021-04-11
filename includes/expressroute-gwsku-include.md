---
title: bestand opnemen
description: bestand opnemen
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: include
ms.date: 04/05/2021
ms.author: duau
ms.custom: include file
ms.openlocfilehash: 27f5755ce8b7d204cad6cdc2281d7992bf86615a
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504677"
---
Wanneer u een virtuele netwerkgateway maakt, moet u de gewenste gateway-SKU opgeven. Wanneer u een hogere gateway-SKU selecteert, wordt meer CPU en bandbreedte van het netwerk toegewezen aan de gateway. Daardoor kan de gateway hogere netwerkdoorvoer aan het virtuele netwerk ondersteunen. 

Virtuele ExpressRoute-netwerkgateways kunnen de volgende SKU's gebruiken:

|     | VPN-gateway en ExpressRoute bestaan tegelijk | FastPath | Maximum aantal circuit verbindingen |
| --- | --- | --- | --- |
| **Standard** | Ja | Nee | 4 |
| **HighPerformance** | Ja | Nee | 4 |
| **UltraPerformance** | Ja | Ja | 16 |