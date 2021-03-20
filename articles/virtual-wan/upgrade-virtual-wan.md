---
title: Een virtuele WAN van Azure upgraden van Basic naar Standard-Azure Portal | Microsoft Docs
description: U kunt uw virtuele WAN-type upgraden voor meer functionaliteit.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 6290d89ed0ee539b4df4c2c8bc9070097da98c81
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91447352"
---
# <a name="upgrade-a-virtual-wan-from-basic-to-standard"></a>Een virtueel WAN upgraden van Basic naar Standard

Dit artikel helpt u bij het upgraden van een basis WAN naar een standaard WAN. Een ' standaard ' WAN-type maakt alle hubs erin als basis-SKU-hubs. In een Basic-hub bent u beperkt tot alleen de site-naar-site VPN-functionaliteit. Met een ' standaard ' WAN-type worden alle hubs erin gemaakt als standaard-SKU-hubs. Wanneer u standaard hubs gebruikt, kunt u ExpressRoute, gebruiker (punt-naar-site) VPN inschakelen, een volledige mesh-hub en VNet-naar-VNet-transit via de Azure-hubs.

De volgende tabel bevat de configuraties die beschikbaar zijn voor elk WAN-type:

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

## <a name="to-change-the-virtual-wan-type"></a>Het virtuele WAN-type wijzigen

1. Selecteer op de pagina voor uw virtuele WAN **configuratie** om de configuratie pagina te openen.

   ![Scherm opname van de pagina ' configuratie ' met een tekstvak voor informatie over het uitvoeren van een upgrade naar een standaard virtueel WAN dat onderaan wordt gemarkeerd.](./media/upgrade-virtual-wan/1.png)
2. Selecteer voor het virtuele WAN-type de optie **standaard** in de vervolg keuzelijst.

   ![Scherm opname van de vervolg keuzelijst virtueel WAN-type.](./media/upgrade-virtual-wan/2.png)
3. Als u een upgrade uitvoert naar een standaard virtueel WAN, kunt u niet terugkeren naar een virtueel WAN. Selecteer **bevestigen** als u een upgrade wilt uitvoeren.

   ![Scherm afbeelding waarin het dialoog venster Upgrade bevestiging wordt weer gegeven.](./media/upgrade-virtual-wan/4.png)
4. Zodra de wijziging is opgeslagen, ziet uw virtuele WAN-pagina er ongeveer uit in dit voor beeld.

   ![Virtual WAN-diagram](./media/upgrade-virtual-wan/5.png)

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Virtual WAN de pagina [Overzicht van Virtual WAN](virtual-wan-about.md).