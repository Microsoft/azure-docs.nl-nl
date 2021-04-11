---
title: bestand opnemen
description: bestand opnemen
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: include
ms.date: 12/05/2019
ms.author: duau
ms.custom: include file
ms.openlocfilehash: b1465b9bf855d592d195277b616c860a23c179f6
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504678"
---
| Gateway-SKU | Verbindingen per seconde | Aantal stromen | Mega bits per seconde | Pakketten per seconde | Circuitbandbreedte | Aantal routes dat is geadverteerd door de gateway (MSEE) | Aantal routes dat is geleerd door de gateway (van MSEE) | Aantal virtuele machines in het virtuele netwerk |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **Basic-SKU (afgeschaft)** | N.v.t. | N.v.t. | 500 | N.v.t. | N.v.t. | N.v.t. | N.v.t. | N.v.t. |
| **Standard SKU/ErGw1AZ** | 7\.000 | 400,000 | >1.000 | >100.000 | 1 Gbps |  500 | 4000 | 2.000 (Verminder tot 1.000 tijdens het onderhoud, worden later hersteld.) | 
| **High Performance SKU/ErGw2AZ** | 14.000 | 840.000 | >2.000 | 250.000 | 1 Gbps | 500 | ~ 9.500 (Verminder tot 4.000 als er meer dan 6.500 virtuele machines in het virtuele netwerk zijn.) | 4.500 |
| **Ultra Performance SKU/ErGw3AZ** | 16.000 | 950.000 | ~ 10.000 | 1.000.000 | 1 Gbps | 500 | ~ 9.500 | 11.000 |
