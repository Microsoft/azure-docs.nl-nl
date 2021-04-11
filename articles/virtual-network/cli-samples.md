---
title: Azure CLI-voorbeelden voor virtueel netwerk
description: Meer informatie over de verschillende voorbeeldscripts die u kunt gebruiken voor het voltooien van taken in de Azure CLI, waaronder het maken van een virtueel netwerk voor toepassingen met meerdere lagen.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 97d63598f6a9cca199779e56cc211b9622117301
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048830"
---
# <a name="azure-cli-samples-for-virtual-network"></a>Azure CLI-voorbeelden voor virtueel netwerk

De volgende tabel bevat koppelingen naar bash-scripts met Azure CLI-opdrachten:

| Script | Beschrijving |
|----|----|
| [Een virtueel netwerk maken voor toepassingen met meerdere lagen](./scripts/virtual-network-cli-sample-multi-tier-application.md) | Hiermee wordt een virtueel netwerk met front-end- en back-end-subnetten gemaakt. Verkeer naar het front-end-subnet wordt beperkt tot HTTP en SSH, terwijl het verkeer naar het back-end-subnet wordt beperkt tot MySQL, poort 3306. |
| [Peering van twee virtuele netwerken](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md) | Hiermee worden twee virtuele netwerken in dezelfde regio gemaakt en verbonden. |
| [Verkeer routeren via een virtueel netwerkapparaat](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md) | Hiermee wordt een virtueel netwerk gemaakt met front-end- en back-end-subnetten en een VM die verkeer tussen de twee subnetten kan routeren. |
| [Binnenkomend en uitgaand VM-netwerkverkeer filteren](./scripts/virtual-network-cli-sample-filter-network-traffic.md) | Hiermee wordt een virtueel netwerk met front-end- en back-end-subnetten gemaakt. Binnenkomend netwerkverkeer naar het front-end-subnet wordt beperkt tot HTTP, HTTPS en SSH. Uitgaand verkeer van het back-end-subnet naar internet is niet toegestaan. |
|[Dual-stack virtueel netwerk IPv4 + IPv6 configureren met Basic Load Balancer](./scripts/virtual-network-cli-sample-ipv6-dual-stack.md)|Hiermee wordt een dual-stack (IPv4+IPv6) virtueel netwerk geïmplementeerd met twee virtuele machines en een Azure Basic Load Balancer met openbare IPv4- en IPv6-IP-adressen. |
|[Dual-stack virtueel netwerk IPv4 + IPv6 configureren met Standard Load Balancer](./scripts/virtual-network-cli-sample-ipv6-dual-stack-standard-load-balancer.md)|Hiermee wordt een dual-stack (IPv4+IPv6) virtueel netwerk geïmplementeerd met twee virtuele machines en een Azure Standard Load Balancer met openbare IPv4- en IPv6-IP-adressen. |
|[Zelfstudie: Een NAT-gateway maken en testen - Azure CLI](./tutorial-create-nat-gateway-cli.md)|Als u de NAT-gateway wilt testen, implementeert u een bron- en doel-VM. |