---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a3ae2a876d6a3772d941fec0b8a1ea3f537e60c3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96010805"
---
U kunt de `Resize-AzVirtualNetworkGateway`PowerShell-cmdlet gebruiken om een Generation1- of Generation2-SKU te upgraden of te downgraden (alle VpnGw-SKU's kunnen worden gewijzigd, behalve de Basic-SKU's). Als u de basis-gateway-SKU gebruikt, [kunt u deze instructies gebruiken in plaats](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) het formaat van uw gateway te wijzigen.

In het volgende Powershell-voor beeld ziet u een gateway-SKU waarvan de grootte wordt gewijzigd in VpnGw2.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```