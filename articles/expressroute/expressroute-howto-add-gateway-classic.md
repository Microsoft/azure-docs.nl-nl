---
title: 'Azure ExpressRoute: een gateway toevoegen aan een VNet: klassiek'
description: Een VNet-gateway configureren voor een klassiek implementatie model VNet met behulp van Power shell voor een ExpressRoute-configuratie.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/06/2019
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 0f42a7d030f45be1b96728fdcac7a13cf797ed34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89396400"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell-classic"></a>Een virtuele netwerk gateway configureren voor ExpressRoute met behulp van Power shell (klassiek)
> [!div class="op_single_selector"]
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klassiek - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video-Azure-Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Dit artikel begeleidt u stapsgewijs door de stappen voor het toevoegen, verg Roten of verkleinen en verwijderen van een virtuele netwerk (VNet)-gateway voor een bestaand VNet. De stappen voor deze configuratie zijn specifiek voor VNets die zijn gemaakt met behulp van het **klassieke implementatie model** en die worden gebruikt in een ExpressRoute-configuratie. 

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Over Azure-implementatiemodellen**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-beginning"></a>Voordat u begint
Controleer of u de Azure PowerShell-cmdlets hebt geïnstalleerd die nodig zijn voor deze configuratie.

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

[!INCLUDE [expressroute-gateway-classic-ps](../../includes/expressroute-gateway-classic-ps-include.md)]

## <a name="next-steps"></a>Volgende stappen
Nadat u de VNet-gateway hebt gemaakt, kunt u uw VNet koppelen aan een ExpressRoute-circuit. Zie [een Virtual Network koppelen aan een ExpressRoute-circuit](expressroute-howto-linkvnet-classic.md).

