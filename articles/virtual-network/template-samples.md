---
title: Voorbeeldsjablonen van Azure Resource Manager voor virtueel netwerk | Microsoft Docs
description: Meer informatie over de verschillende Azure Resource Manager-sjablonen die u kunt implementeren met virtuele Azure-netwerken.
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
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: c269831c391390d120f769d2c1da3fa39afed1cf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98217903"
---
# <a name="azure-resource-manager-template-samples-for-virtual-network"></a>Voorbeeldsjablonen van Azure Resource Manager voor virtueel netwerk

De volgende tabel bevat koppelingen naar Azure Resource Manager-sjablonen. U kunt sjablonen implementeren met behulp van Azure [Portal](../azure-resource-manager/templates/deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json), Azure [CLI](../azure-resource-manager/templates/deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) of Azure [PowerShell](../azure-resource-manager/templates/deploy-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Zie voor informatie over het ontwerpen van uw eigen sjablonen [Maken van uw eerste sjabloon](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) en [Inzicht in de structuur en de syntaxis van Azure Resource Manager-sjablonen](../azure-resource-manager/templates/template-syntax.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Zie [Microsoft.Network resource types](/azure/templates/microsoft.network/allversions) (Microsoft.Network-resourcetypen) voor de JSON-syntaxis en eigenschappen die u kunt gebruiken in sjablonen.


| Taak | Beschrijving |
|----|----|
|[Een virtueel netwerk met twee subnetten maken](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets)| Hiermee maakt u een virtueel netwerk met twee subnetten.|
|[Verkeer routeren via een virtueel netwerkapparaat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-userdefined-routes-appliance)| Hiermee maakt u een virtueel netwerk met drie subnetten. Hiermee implementeert u een virtuele machine in elk van de subnetten. Hiermee maakt u een routetabel met routes om verkeer van één subnet naar een andere te sturen via de virtuele machine in het derde subnet. Hiermee koppelt u de routetabel op een van de subnetten.|
|[Hiermee maakt u een service-eindpunt voor het virtuele netwerk voor Azure Storage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vnet-2subnets-service-endpoints-storage-integration)|Hiermee maakt u een nieuw virtueel netwerk met twee subnetten en een netwerkinterface in elk subnet. Hiermee activeert u een service-eindpunt naar Azure Storage voor een van de subnetten en beveiligt u een nieuw opslagaccount op dat subnet.|
|[Verbinden van twee virtuele netwerken](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vnet-to-vnet-peering)| Hiermee maakt u twee virtuele netwerken en een virtueel peering-netwerk ertussen.|
|[Een virtuele machine met meerdere IP-adressen maken](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-multiple-ipconfig)| Hiermee maakt u een Windows- of Linux-VM met meerdere IP-adressen.|
|[Virtueel netwerk met dubbele stack van IPv4 + IPv6 configureren](https://github.com/Azure/azure-quickstart-templates/tree/master/ipv6-in-vnet)|Implementeert een dual-stack (IPv4+IPv6) virtueel netwerk met twee virtuele machines en een Azure Basic Load Balancer met openbare IPv4- en IPv6-IP-adressen. |