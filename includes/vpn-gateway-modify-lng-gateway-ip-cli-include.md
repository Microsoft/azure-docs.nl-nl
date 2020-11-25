---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 069d7af9cee0bee673c8f0b32659ce362fe4dd70
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027080"
---
### <a name="to-modify-the-local-network-gateway-gatewayipaddress"></a>Het 'gatewayIpAddress' van de lokale netwerkgateway wijzigen

Als van het VPN-apparaat waarmee u verbinding wilt maken het openbare IP-adres is gewijzigd, moet u de gateway van het lokale netwerk aanpassen met deze wijziging. Het IP-adres van de gateway kan worden gewijzigd zonder een bestaande VPN-gatewayverbinding te verwijderen (als u er een hebt). Voor het wijzigen van het gateway-IP-adres vervang u de waarden 'Site2' en 'TestRG1' door uw eigen waarden met behulp van de opdracht [az network local-gateway update](/cli/azure/network/local-gateway).

```azurecli
az network local-gateway update --gateway-ip-address 23.99.222.170 --name Site2 --resource-group TestRG1
```

Controleer of het IP-adres juist is in de uitvoer:

```
"gatewayIpAddress": "23.99.222.170",
```