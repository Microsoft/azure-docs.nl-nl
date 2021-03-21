---
title: 'IP-adres instellingen van gateway wijzigen: Power shell'
description: In dit artikel vindt u instructies voor het wijzigen van IP-adres voorvoegsels voor uw lokale netwerk gateway met behulp van Power shell
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/10/2021
ms.author: cherylmc
ms.openlocfilehash: 07d9124105424d42970800b1c5bca956d512722d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100388665"
---
# <a name="modify-local-network-gateway-settings-using-powershell"></a>Instellingen voor lokale netwerkgateway wijzigen met PowerShell

Soms worden de instellingen voor de AddressPrefix of GatewayIPAddress van uw lokale netwerk gateway gewijzigd. In dit artikel wordt beschreven hoe u de instellingen van uw lokale netwerk gateway wijzigt. U kunt deze instellingen ook met behulp van een andere methode wijzigen door een andere optie te selecteren in de volgende lijst:

> [!div class="op_single_selector"]
> * [Azure-portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure-CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before-you-begin"></a><a name="before"></a>Voordat u begint

Installeer de meest recente versie van de PowerShell-cmdlets van Azure Resource Manager. Zie [How to install and configure Azure PowerShell](/powershell/azure/) (Azure PowerShell installeren en configureren) voor meer informatie over het installeren van de PowerShell-cmdlets.

## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>IP-adres voorvoegsels wijzigen

[!INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="modify-the-gateway-ip-address"></a><a name="gwip"></a>Het IP-adres van de gateway wijzigen

[!INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Volgende stappen

U kunt uw gateway verbinding controleren. Zie [een gateway verbinding controleren](vpn-gateway-verify-connection-resource-manager.md).