---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: cf9d4c3fd96df83361e7d9aa89ba702d37265ec6
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95560372"
---
Door gebruikers gedefinieerde routes met de bestemming 0.0.0.0.0/0 en NSG's in het GatewaySubnet **worden niet ondersteund**. Gateways die gemaakt zijn met deze configuratie, worden geblokkeerd en kunnen niet worden gemaakt. Gateways vereisen toegang tot de management controllers om correct te kunnen funcioneren. [Doorgifte van BGP-route](../articles/virtual-network/virtual-networks-udr-overview.md#border-gateway-protocol) moet worden ingesteld op 'Ingeschakeld' in het GatewaySubnet om voor de beschikbaarheid van de gateway te zorgen. Als deze optie is uitgeschakeld, functioneert de gateway niet.