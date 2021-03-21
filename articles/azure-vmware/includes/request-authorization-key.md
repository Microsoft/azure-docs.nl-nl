---
title: Autorisatie sleutel voor ExpressRoute aanvragen
description: Stappen voor het aanvragen van een autorisatie sleutel voor ExpressRoute.
ms.topic: include
ms.date: 03/15/2021
ms.openlocfilehash: 54a610c8b0f3f3fe9d3ebe39291bba7767007839
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103491831"
---
<!-- used in expressroute-global-reach-private-cloud.md and create-ipsec-tunnel.md -->

1. Selecteer in de Azure Portal, onder de sectie **connectiviteit** , op het tabblad **ExpressRoute** **+ een autorisatie sleutel aanvragen**. 

   :::image type="content" source="../media/expressroute-global-reach/start-request-authorization-key.png" alt-text="Scherm afbeelding waarin wordt getoond hoe een ExpressRoute-autorisatie sleutel moet worden aangevraagd." border="true" lightbox="../media/expressroute-global-reach/start-request-authorization-key.png":::

1. Geef een naam op en selecteer **maken**. 
      
   Het duurt ongeveer 30 seconden om de sleutel te maken. Zodra de nieuwe sleutel is gemaakt, wordt deze weergegeven in de lijst met autorisatiesleutels voor de privécloud.

   :::image type="content" source="../media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="Scherm opname van de ExpressRoute-Global Reach autorisatie sleutel.":::
  
1. Noteer de autorisatie sleutel en de ExpressRoute-ID. U gebruikt deze voor het volt ooien van de peering.  

   > [!NOTE]
   > De autorisatie sleutel verdwijnt na enige tijd, dus kopieer deze zo snel als deze wordt weer gegeven.