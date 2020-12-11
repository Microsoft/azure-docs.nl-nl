---
title: Azure Virtual Network-concepten en aanbevolen procedures
description: Meer informatie over Azure Virtual Network-concepten en aanbevolen procedures.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/03/2020
ms.author: kumud
ms.openlocfilehash: d279516c1c9c08512c850a0f70eb84c0c1f63166
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97111220"
---
# <a name="azure-virtual-network-concepts-and-best-practices"></a>Azure Virtual Network-concepten en aanbevolen procedures

In dit artikel worden de belangrijkste concepten en aanbevolen procedures voor Azure Virtual Network (VNet) beschreven.

## <a name="vnet-concepts"></a>VNet-concepten

- **Adresruimte:** Wanneer u een VNet maakt, moet u een aangepaste persoonlijke IP-adresruimte opgeven met behulp van openbare en persoonlijke adressen (RFC 1918). Azure wijst resources in een virtueel netwerk een persoonlijk IP-adres toe op basis van de adresruimte die u toewijst. Als u bijvoorbeeld een VM in een VNet implementeert met adresruimte 10.0.0.0/16, wordt aan de VMe een privé-IP-adres, zoals 10.0.0.4, toegewezen.
- **Subnetten:** Met subnetten kunt u het virtuele netwerk in een of meer subnetwerken segmenteren en een deel van de adresruimte van het virtuele netwerk aan elk subnet toewijzen. Vervolgens kunt u Azure-resources implementeren in een specifiek subnet. Net als in een traditioneel netwerk kunt u met subnetten uw VNet-adresruimte segmenteren in segmenten die geschikt zijn voor het interne netwerk van de organisatie. Dit verbetert ook de efficiëntie van de adrestoewijzing. U kunt resources binnen subnetten beveiligen met behulp van netwerkbeveiligingsgroepen. Zie [Netwerkbeveiligingsgroepen](security-overview.md) voor meer informatie.
- **Regio's**: Een VNet is afgebakend tot één regio/locatie, maar meerdere virtuele netwerken in verschillende regio's kunnen worden verbonden samen met behulp van peering van virtuele netwerken.
- **Abonnement:** Het VNet bevindt zich in een abonnement. U kunt meerdere virtuele netwerken binnen elk Azure-[abonnement](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) en elke Azure-[regio](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region) implementeren.

## <a name="best-practices"></a>Aanbevolen procedures

Wanneer u uw netwerk in Azure bouwt, is het belangrijk om de volgende universele principes te onthouden bij het ontwerpen:

- Zorg ervoor dat de adresruimten niet overlappen. Zorg ervoor dat uw VNet-adresruimte (CIDR-blok) niet overlapt met de andere netwerkbereiken van uw organisatie.
- Uw subnetten mogen niet de gehele adresruimte van het VNet beslaan. Plan vooruit en reserveer wat adresruimte voor de toekomst.
- Het is beter om een klein aantal grote VNets te hebben dan een groot aantal kleine VNets. Dit voor komt beheeroverhead.
- Beveilig uw VNet door netwerkbeveiligingsgroepen (NSG's) toe te wijzen aan de onderliggende subnetten.

## <a name="next-steps"></a>Volgende stappen

 Als u aan de slag wilt gaan met een virtueel netwerk, maakt u een virtueel netwerk, implementeert u een paar VM's en laat u de virtuele machines met elkaar communiceren. Zie de snelstart [Een virtueel netwerk maken](quick-create-portal.md) voor meer informatie.
