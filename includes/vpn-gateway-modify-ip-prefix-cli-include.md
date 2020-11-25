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
ms.openlocfilehash: f222d4a7f4724506112a47eff61ccc48354dd622
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027549"
---
### <a name="to-modify-local-network-gateway-ip-address-prefixes---no-gateway-connection"></a><a name="noconnection"></a>IP-adresvoorvoegsels wijzigen voor de gateway van een lokaal netwerk - geen gatewayverbinding

Als u geen gatewayverbinding hebt en u IP-adresvoorvoegsels wilt toevoegen of verwijderen, gebruikt u dezelfde opdracht die u gebruikt voor het maken van de gateway van het lokale netwerk, [az network local-gateway create](/cli/azure/network/local-gateway). U kunt deze opdracht ook gebruiken om het gateway-IP-adres voor het VPN-apparaat bij te werken. Gebruik de bestaande naam van de gateway van uw lokale netwerk om de huidige instellingen te overschrijven. Als u een andere naam gebruikt, maakt u een nieuwe lokale netwerkgateway in plaats van de bestaande gateway te overschrijven.

Elke keer dat u een wijziging aanbrengt, moet de volledige lijst met voorvoegsels worden opgegeven, niet alleen de voorvoegsels die u wilt wijzigen. Geef alleen de voorvoegsels op die u wilt houden. In dit geval 10.0.0.0/24 en 20.0.0.0/24

```azurecli
az network local-gateway create --gateway-ip-address 23.99.221.164 --name Site2 -g TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
```

### <a name="to-modify-local-network-gateway-ip-address-prefixes---existing-gateway-connection"></a><a name="withconnection"></a>IP-adresvoorvoegsels wijzigen voor de gateway van een lokaal netwerk - bestaande gatewayverbinding

Als u een gatewayverbinding hebt en u IP-adresvoorvoegsels wilt toevoegen of verwijderen, kunt u de voorvoegsels bijwerken met [az network local-gateway update](/cli/azure/network/local-gateway). Dit veroorzaakt enige downtime in uw VPN-verbinding. Als u de IP-adresvoorvoegsels wijzigt, hoeft u de VPN-gateway niet te verwijderen.

Elke keer dat u een wijziging aanbrengt, moet de volledige lijst met voorvoegsels worden opgegeven, niet alleen de voorvoegsels die u wilt wijzigen. In dit voorbeeld zijn 10.0.0.0/24 en 20.0.0.0/24 al aanwezig. We voegen de voorvoegsels 30.0.0.0/24 en 40.0.0.0/24 toe en geven alle 4 voorvoegsels op bij het bijwerken.

```azurecli
az network local-gateway update --local-address-prefixes 10.0.0.0/24 20.0.0.0/24 30.0.0.0/24 40.0.0.0/24 --name VNet1toSite2 -g TestRG1
```