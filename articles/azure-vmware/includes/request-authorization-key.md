---
title: Autorisatie sleutel voor ExpressRoute aanvragen
description: Stappen voor het aanvragen van een autorisatie sleutel voor ExpressRoute.
ms.topic: include
ms.date: 03/15/2021
ms.openlocfilehash: 99d9fba33d64fca1d9c5b960041fbabe1f9060db
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105026970"
---
<!-- used in expressroute-global-reach-private-cloud.md and create-ipsec-tunnel.md -->

1. Selecteer in de Azure Portal, onder de sectie **connectiviteit** , op het tabblad **ExpressRoute** **+ een autorisatie sleutel aanvragen**. 

   :::image type="content" source="../media/expressroute-global-reach/start-request-authorization-key.png" alt-text="Scherm afbeelding waarin wordt getoond hoe een ExpressRoute-autorisatie sleutel moet worden aangevraagd." border="true" lightbox="../media/expressroute-global-reach/start-request-authorization-key.png":::

1. Geef een naam op en selecteer **maken**. 
      
   Het duurt ongeveer 30 seconden om de sleutel te maken. Zodra de nieuwe sleutel is gemaakt, wordt deze weergegeven in de lijst met autorisatiesleutels voor de privécloud.

   :::image type="content" source="../media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="Scherm opname van de ExpressRoute-Global Reach autorisatie sleutel.":::
  
1. Kopieer de autorisatie sleutel en de ExpressRoute-ID. U gebruikt deze voor het volt ooien van de peering.  

   > [!NOTE]
   > De autorisatie sleutel verdwijnt na enige tijd, dus kopieer deze zo snel als deze wordt weer gegeven.