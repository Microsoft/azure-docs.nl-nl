---
title: bestand opnemen
description: Include-bestand
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4940bce453729d13ba76071fa59bcf95c7672a24
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94359494"
---
* U een Azure-abonnement heeft. Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan.

* U hebt een virtueel netwerk waarmee u verbinding wilt maken. Controleer of geen van de subnetten van uw on-premises netwerken overlapt met de virtuele netwerken waarmee u verbinding wilt maken. Zie het [quickstart-artikel](../articles/virtual-network/quick-create-portal.md) als u een virtueel netwerk in het Azure-portaal wilt maken.

* Uw virtuele netwerk mogen geen bestaande virtuele netwerkgateways bevatten. Als uw virtuele netwerk al gateways heeft (VPN of ExpressRoute) heeft, moet u alle gateways verwijderen voordat u verdergaat. Voor deze configuratie mogen virtuele netwerken enkel verbinding maken met de Virtual WAN-hubgateway.

* Een virtuele hub is een virtueel netwerk dat wordt gemaakt en gebruikt door Virtual WAN. Dit vormt de kern van uw Virtual WAN-netwerk in een regio. Haal een IP-adresbereik op voor de regio van uw virtuele hub. Het adresbereik dat u voor de hub opgeeft mag niet overlappen met een van de bestaande virtuele netwerken waarmee u verbinding wilt maken. Dit bereik mag ook niet overlappen met de on-premises adresbereiken waarmee u verbinding wilt maken. Als u de IP-adresbereiken in uw on-premises netwerkconfiguratie niet kent, moet u contact opnemen met iemand die deze gegevens kan verstrekken.
