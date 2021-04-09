---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 923620c1c9719d857cc848507a4c5507f528d34e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95557380"
---
### <a name="to-view-local-network-gateways"></a>Lokale netwerkgateways bekijken

Gebruik de opdracht [az network local-gateway list](/cli/azure/network/local-gateway) om een lijst met gateways voor lokale netwerken weer te geven.

```azurecli
az network local-gateway list --resource-group TestRG1
```

[!INCLUDE [modify-prefix](vpn-gateway-modify-ip-prefix-cli-include.md)]

[!INCLUDE [modify-gateway-IP](vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

### <a name="to-verify-the-shared-key-values"></a>De gedeelde sleutelwaarden controleren

Controleer of de waarde van de gedeelde sleutel gelijk is aan de waarde die u voor de configuratie van uw VPN-apparaat hebt gebruikt. Als dat niet het geval is, voert u de verbinding opnieuw uit met de waarde van het apparaat of werkt u het apparaat bij met de waarde die is geretourneerd. De waarden moeten overeenkomen. Gebruik [az network vpn-connection-list](/cli/azure/network/vpn-connection) om de gedeelde sleutel weer te geven.

```azurecli
az network vpn-connection shared-key show --connection-name VNet1toSite2 --resource-group TestRG1
```
### <a name="to-view-the-vpn-gateway-public-ip-address"></a>Het openbare IP-adres van de VPN Gateway weergeven

  Gebruik de opdracht [az network public-ip list](/cli/azure/network/public-ip) om het openbare IP-adres van uw virtuele netwerkgateway te vinden. De lijst met openbare IP-adressen wordt in dit voorbeeld in een gemakkelijk leesbare tabelindeling weergegeven.

```azurecli
az network public-ip list --resource-group TestRG1 --output table
```