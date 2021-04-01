---
title: 'Een mislukt circuit opnieuw instellen-ExpressRoute: Power shell: Azure | Microsoft Docs'
description: Dit artikel helpt u bij het opnieuw instellen van een ExpressRoute-circuit met de status mislukt.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/07/2021
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 84c8275b7a7257530a735e8612047ef42e2f8a7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98011336"
---
# <a name="reset-a-failed-expressroute-circuit"></a>Een ExpressRoute-circuit met fouten opnieuw instellen

Wanneer een bewerking in een ExpressRoute-circuit niet kan worden voltooid, kan het circuit de status mislukt hebben. Dit artikel helpt u bij het opnieuw instellen van een mislukt Azure ExpressRoute-circuit.

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="reset-a-circuit"></a>Een circuit opnieuw instellen

1. Installeer de meest recente versie van de PowerShell-cmdlets van Azure Resource Manager. Zie [Azure PowerShell installeren en configureren](/powershell/azure/install-az-ps) voor meer informatie.

2. Open de PowerShell-console met verhoogde rechten en maak verbinding met uw account. Gebruik het volgende voorbeeld als hulp bij het maken van de verbinding:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
3. Als u meerdere Azure-abonnementen hebt, controleert u de abonnementen voor het account.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
4. Geef het abonnement op dat u wilt gebruiken.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```
5. Voer de volgende opdrachten uit om een circuit met de status mislukt opnieuw in te stellen:

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

Het circuit zou nu in orde moeten zijn. Open een ondersteunings ticket met [ondersteuning van micro soft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) als het circuit nog steeds de status Mislukt heeft.

## <a name="next-steps"></a>Volgende stappen

Open een ondersteunings ticket met [micro soft ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) als u nog steeds problemen ondervindt.
