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
ms.openlocfilehash: b3907882df09bfae1d6453fbffbd3e7562554f7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010820"
---
* **Op beleid gebaseerd:** op beleid gebaseerde VPN-verbindingen werden voorheen statische routeringsgateways genoemd in het klassieke implementatiemodel. VPN-verbindingen op basis van beleid versleutelen pakketten en sturen deze via IPsec-tunnels op basis van het IPsec-beleid dat is geconfigureerd met de combinaties van adresvoorvoegsels tussen uw lokale netwerk en het Azure VNET. Het beleid (of de verkeersselector) wordt gewoonlijk gedefinieerd als een toegangslijst in de configuratie van het VPN-apparaat. De waarde voor een op beleid gebaseerd VPN-type is *PolicyBased*. Houd bij het gebruik van een op beleid gebaseerde VPN rekening met de volgende beperkingen:
  
  * Op beleid gebaseerde VPN's kunnen **alleen** worden gebruikt met de Basic-gateway-SKU. Dit VPN-type is niet compatibel met andere gateway-SKU's.
  * U kunt slechts één tunnel gebruiken wanneer u een op beleid gebaseerde VPN-verbinding gebruikt.
  * U kunt op beleid gebaseerde VPN's alleen gebruiken voor S2S-verbindingen en alleen voor bepaalde configuraties. De meeste VPN Gateway-configuraties vereisen een op route gebaseerde VPN.
* **Op route gebaseerd**: op route gebaseerde VPN-verbindingen werden voorheen dynamische routeringsgateways genoemd in het klassieke implementatiemodel. Op route gebaseerde VPN's gebruiken 'routes' in de IP-doorstuurtabel of routeringstabel om pakketten naar de bijbehorende tunnelinterfaces te sturen. De tunnelinterfaces versleutelen of ontsleutelen de pakketten vervolgens naar en vanuit de tunnels. Het beleid (of de verkeersselector) voor op route gebaseerde VPN's is geconfigureerd als alles-naar-alles (of jokertekens). De waarde voor een op route gebaseerd VPN-type is *RouteBased*.
