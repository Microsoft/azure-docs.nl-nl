---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4e958026b1167d65f47bddbe5e89ec4d6fed7ee3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92217931"
---
U kunt controleren of uw verbinding is geslaagd met behulp van de opdracht [az network vpn-connection show](/cli/azure/network/vpn-connection). In het voorbeeld verwijst "--name" naar de naam van de verbinding die u wilt testen. Zolang het tot stand brengen van de verbinding bezig is, is de verbindingsstatus "Verbinding maken". Wanneer de verbinding tot stand is gebracht, verandert de status in "Verbonden".

```azurecli-interactive
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```
