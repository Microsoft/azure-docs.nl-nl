---
title: Azure PowerShell-voor beelden-netwerken
description: Meer informatie over Azure PowerShell-voor beelden voor netwerken, waaronder een voor beeld voor het maken van een virtueel netwerk voor toepassingen met meerdere lagen.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/24/2017
ms.author: gwallace
ms.openlocfilehash: aa3304cbabb183368f0e47415e64120854f43535
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87282072"
---
# <a name="azure-powershell-samples-for-networking"></a>Voor beelden Azure PowerShell voor netwerken

De volgende tabel bevat koppelingen naar scripts die zijn gebouwd met behulp van Azure PowerShell.

| Script | Beschrijving |
|-|-|
|**Connectiviteit tussen Azure-resources**||
| [Een virtueel netwerk maken voor toepassingen met meerdere lagen](./scripts/virtual-network-powershell-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | Hiermee wordt een virtueel netwerk met front-end- en back-end-subnetten gemaakt. Verkeer naar het front-end-subnet wordt beperkt tot HTTP, terwijl verkeer naar het back-end-subnet wordt beperkt tot SQL, poort 1433. |
| [Peering van twee virtuele netwerken](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | Hiermee worden twee virtuele netwerken in dezelfde regio gemaakt en verbonden. |
| [Verkeer routeren via een virtueel netwerkapparaat](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | Hiermee wordt een virtueel netwerk gemaakt met front-end- en back-end-subnetten en een VM die verkeer tussen de twee subnetten kan routeren. |
| [Binnenkomend en uitgaand VM-netwerkverkeer filteren](./scripts/virtual-network-powershell-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | Hiermee wordt een virtueel netwerk met front-end- en back-end-subnetten gemaakt. Binnenkomend netwerk verkeer naar het front-end-subnet is beperkt tot HTTP en HTTPS.. Uitgaand verkeer naar Internet vanuit het back-end-subnet is niet toegestaan. |
|**Taak verdeling en verkeers richting**||
| [Verkeer verdelen naar virtuele machines voor hoge beschikbaarheid](./scripts/load-balancer-windows-powershell-sample-nlb.md?toc=%2fazure%2fnetworking%2ftoc.json) | Maakt verschillende virtuele machines in een configuratie met hoge Beschik baarheid en taak verdeling. |
| [Direct verkeer tussen meerdere regio's voor hoge Beschik baarheid van toepassingen](./scripts/traffic-manager-powershell-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  Hiermee maakt u twee app service-abonnementen, twee web-apps, een Traffic Manager-profiel en twee Traffic Manager-eind punten. |
| | |
