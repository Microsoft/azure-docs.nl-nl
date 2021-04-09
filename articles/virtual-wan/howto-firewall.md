---
title: Azure Firewall installeren in een virtuele WAN-hub
titleSuffix: Azure Virtual WAN
description: Stappen voor het configureren van Azure Firewall in een virtuele WAN-hub
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 577340e485550e84941a33d82b58aa6ff1c933d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90983669"
---
# <a name="configure-azure-firewall-in-a-virtual-wan-hub"></a>Azure Firewall configureren in een virtuele WAN-hub

Een **beveiligde hub** is een virtuele WAN-hub van Azure met Azure firewall. Dit artikel begeleidt u stapsgewijs door de stappen voor het converteren van een virtuele WAN-hub naar een beveiligde hub door Azure Firewall rechtstreeks te installeren vanaf de pagina's van de virtuele WAN-portal van Azure.

## <a name="before-you-begin"></a>Voordat u begint

In de stappen in dit artikel wordt ervan uitgegaan dat u al een virtueel WAN hebt geïmplementeerd met een of meer hubs.

Als u een nieuwe virtuele WAN en een nieuwe hub wilt maken, gebruikt u de stappen in de volgende artikelen:

* [Een virtueel WAN maken](virtual-wan-site-to-site-portal.md#openvwan)
* [Een hub maken](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-virtual-hubs"></a>Virtuele hubs weer geven

Op de **overzichts** pagina voor uw virtuele WAN wordt een lijst met virtuele hubs en beveiligde hubs weer gegeven. In de volgende afbeelding ziet u een virtueel WAN zonder beveiligde hubs.

[![Scherm afbeelding toont de overzichts pagina voor een virtueel WAN met een lijst met virtuele hubs.](./media/howto-firewall/overview.png)](./media/howto-firewall/overview.png#lightbox)

## <a name="convert-to-secured-hub"></a>Converteren naar beveiligde hub

1. Selecteer op de pagina **overzicht** voor uw virtuele WAN de hub die u wilt converteren naar een beveiligde hub. Op de pagina virtuele hub ziet u twee opties voor het implementeren van Azure Firewall in deze hub. Selecteer een van beide opties.

   [![Scherm afbeelding toont de overzichts pagina voor uw virtuele WAN, waar u converteren naar beveiligde hub of Azure firewall kunt selecteren.](./media/howto-firewall/security.png)](./media/howto-firewall/security.png#lightbox)

1. Nadat u een van de opties hebt geselecteerd, ziet u de pagina **converteren naar beveiligde hub** . Selecteer een hub die u wilt converteren en selecteer vervolgens **volgende: Azure firewall** aan de onderkant van de pagina.

   [![hub selecteren](./media/howto-firewall/select-hub.png)](./media/howto-firewall/select-hub.png#lightbox)
1. Nadat de werk stroom is voltooid, selecteert u **bevestigen**.

   [![Scherm afbeelding toont het deel venster converteren naar beveiligde hub met bevestigen geselecteerd.](./media/howto-firewall/confirm.png)](./media/howto-firewall/confirm.png#lightbox)

1. Nadat de hub is geconverteerd naar een beveiligde hub, kunt u deze weer geven op de **overzichts** pagina van het virtuele WAN.

   [![beveiligde hub weer geven](./media/howto-firewall/secured-hub.png)](./media/howto-firewall/secured-hub.png#lightbox)

## <a name="view-hub-resources"></a>Hub-resources weer geven

Selecteer op de pagina **overzicht** van virtueel WAN de beveiligde hub. Op de hub-pagina kunt u alle resources van de virtuele hub, met inbegrip van Azure Firewall, weer geven.

[![hub-resources weer geven](./media/howto-firewall/view-resources.png)](./media/howto-firewall/view-resources.png#lightbox)

Als u Azure Firewall instellingen van de beveiligde hub wilt weer geven, selecteert u onder **beveiliging** **beveiligde instellingen voor virtuele hub**.
[![hub-instellingen weer geven](./media/howto-firewall/hub-settings.png)](./media/howto-firewall/hub-settings.png#lightbox)

## <a name="configure-additional-settings"></a>Aanvullende instellingen configureren

Als u aanvullende Azure Firewall instellingen voor de virtuele hub wilt configureren, selecteert u de koppeling naar **Azure firewall Manager**. Zie [Azure firewall Manager](../firewall-manager/secure-cloud-network.md#create-a-firewall-policy-and-secure-your-hub)voor informatie over Firewall-beleid.

[![aanvullende instellingen](./media/howto-firewall/additional-settings.png)](./media/howto-firewall/additional-settings.png#lightbox)

Ga terug naar de pagina **overzicht** van de hub door te klikken op het pad, zoals wordt weer gegeven op de pijl in de volgende afbeelding.

[![terug naar overzicht](./media/howto-firewall/arrow.png)](./media/howto-firewall/arrow.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [Veelgestelde vragen](virtual-wan-faq.md)voor meer informatie over Virtual WAN.
