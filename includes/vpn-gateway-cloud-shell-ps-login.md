---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 24d146da7946176c92902698d0f52ae01baf79ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93061601"
---
Als u Power shell lokaal uitvoert, opent u de Power shell-console met verhoogde bevoegdheden en maakt u verbinding met uw Azure-account. Met de cmdlet *Connect-AzAccount* wordt u gevraagd referenties op te vragen. Na de verificatie worden uw account instellingen gedownload zodat ze beschikbaar zijn voor Azure PowerShell.

Als u Azure Cloud Shell gebruikt in plaats van lokaal Power shell uit te voeren, zult u merken dat u geen *Connect-AzAccount* hoeft uit te voeren. Azure Cloud Shell maakt automatisch verbinding met uw Azure-account nadat u het selectie vakje **proberen** hebt geselecteerd.

1. Als u Power shell lokaal uitvoert, meldt u zich aan.

   ```azurepowershell
   Connect-AzAccount
   ```

1. Als u meer dan één abonnement hebt, haalt u een lijst met uw abonnementen op.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

1. Geef het abonnement op dat u wilt gebruiken.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```
