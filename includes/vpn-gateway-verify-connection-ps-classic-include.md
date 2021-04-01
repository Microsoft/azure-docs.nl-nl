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
ms.openlocfilehash: bd096e5a562bd9117ee895534c087d6fde48cb7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92217920"
---
U kunt controleren of uw verbinding is geslaagd met behulp van de cmdlet ' Get-AzureVNetConnection '.

1. Gebruik het volgende cmdlet-voorbeeld om de waarden aan te passen aan uw eigen waarden. De naam van het virtuele netwerk moet tussen aanhalings tekens staan als deze spaties bevat.

   ```azurepowershell
   Get-AzureVNetConnection "Group ClassicRG TestVNet1"
   ```
2. Bekijk de waarden nadat de cmdlet is voltooid. In het onderstaande voor beeld wordt de connectiviteits status weer gegeven als verbonden en ziet u inkomende en uitgaande bytes.

   ```output
   ConnectivityState         : Connected
   EgressBytesTransferred    : 181664
   IngressBytesTransferred   : 182080
   LastConnectionEstablished : 10/19/22020 12:40:54 AM
   LastEventID               : 24401
   LastEventMessage          : The connectivity state for the local network site 'F7F7BFC7_SiteVNet4' changed from Connecting to
                               Connected.
   LastEventTimeStamp        : 10/19/2020 12:40:54 AM
   LocalNetworkSiteName      : F7F7BFC7_SiteVNet4
   ```
