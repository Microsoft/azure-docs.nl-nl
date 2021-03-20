---
title: 'Azure ExpressRoute: klassieke circuits verplaatsen naar Resource Manager'
description: Op deze pagina wordt beschreven hoe u een klassiek circuit verplaatst naar het Resource Manager-implementatie model met behulp van Power shell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/25/2019
ms.author: duau
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 460ea446fc6dfc43e81a1a57bbba032a61f3a72d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90532542"
---
# <a name="move-expressroute-circuits-from-classic-to-resource-manager-deployment-model-using-powershell"></a>ExpressRoute-circuits verplaatsen van klassiek naar Resource Manager-implementatie model met behulp van Power shell

Als u een ExpressRoute-circuit voor het klassieke en Resource Manager-implementatie model wilt gebruiken, moet u het circuit verplaatsen naar het Resource Manager-implementatie type. In de volgende secties wordt beschreven hoe u uw circuit kunt verplaatsen met behulp van Power shell.

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

* Controleer of u de klassieke en AZ Azure PowerShell-modules lokaal op uw computer hebt geïnstalleerd. Zie [Azure PowerShell installeren en configureren](/powershell/azure/) voor meer informatie.
* Zorg ervoor dat [u de vereisten](expressroute-prerequisites.md), [routerings behoeften](expressroute-routing.md)en [werk stromen](expressroute-workflows.md) hebt gecontroleerd voordat u begint met de configuratie.
* Lees de informatie die wordt weer gegeven bij het [verplaatsen van een ExpressRoute-circuit van klassiek naar Resource Manager](expressroute-move.md). Zorg ervoor dat u de limieten en beperkingen volledig begrijpt.
* Controleer of het circuit volledig operationeel is in het klassieke implementatie model.
* Zorg ervoor dat u een resource groep hebt die is gemaakt in het Resource Manager-implementatie model.

## <a name="move-an-expressroute-circuit"></a>Een ExpressRoute-circuit verplaatsen

### <a name="step-1-gather-circuit-details-from-the-classic-deployment-model"></a>Stap 1: circuit details van het klassieke implementatie model verzamelen

Meld u aan bij de klassieke Azure-omgeving en verzamel de service sleutel.

1. Meld u aan bij uw Azure-account.

   ```powershell
   Add-AzureAccount
   ```

2. Selecteer het juiste Azure-abonnement.

   ```powershell
   Select-AzureSubscription "<Enter Subscription Name here>"
   ```

3. Importeer de Power shell-modules voor Azure en ExpressRoute.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
   ```

4. Gebruik de onderstaande cmdlet om de service sleutels voor al uw ExpressRoute-circuits op te halen. Nadat u de sleutels hebt opgehaald, kopieert u de **Service sleutel** van het circuit dat u wilt verplaatsen naar het Resource Manager-implementatie model.

   ```powershell
   Get-AzureDedicatedCircuit
   ```

### <a name="step-2-sign-in-and-create-a-resource-group"></a>Stap 2: Meld u aan en maak een resource groep

Meld u aan bij de Resource Manager-omgeving en maak een nieuwe resource groep.

1. Meld u aan bij uw Azure Resource Manager-omgeving.

   ```powershell
   Connect-AzAccount
   ```

2. Selecteer het juiste Azure-abonnement.

   ```powershell
   Get-AzSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzSubscription
   ```

3. Wijzig het onderstaande fragment om een nieuwe resource groep te maken als u nog geen resource groep hebt.

   ```powershell
   New-AzResourceGroup -Name "DemoRG" -Location "West US"
   ```

### <a name="step-3-move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>Stap 3: het ExpressRoute-circuit verplaatsen naar het Resource Manager-implementatie model

U bent nu klaar om uw ExpressRoute-circuit te verplaatsen van het klassieke implementatie model naar het Resource Manager-implementatie model. Controleer voordat u verdergaat de informatie in [een ExpressRoute-circuit verplaatsen van het klassieke naar het Resource Manager-implementatie model](expressroute-move.md).

Wijzig en voer het volgende code fragment uit om het circuit te verplaatsen:

```powershell
Move-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"
```

In de klassieke modus is een ExpressRoute-circuit niet in staat om te worden gekoppeld aan een regio. In Resource Manager moet elke resource echter worden toegewezen aan een Azure-regio. De regio die is opgegeven in de Move-AzExpressRouteCircuit cmdlet, kan in elk land worden beschouwd. Voor organisatie doeleinden wilt u mogelijk een regio kiezen die de locatie van uw peering nauw keurig weergeeft.

> [!NOTE]
> * Nadat u uw klassieke ExpressRoute-circuit naar het Resource Manager-implementatie model hebt verplaatst, heeft het standaard toegang tot de klassieke en Resource Manager-implementatie modellen.
> * De nieuwe naam die wordt vermeld in de vorige cmdlet wordt gebruikt om de resource te adresseren. De naam van het circuit wordt in feite gewijzigd.

## <a name="modify-circuit-access"></a>De toegang tot het circuit wijzigen

### <a name="to-enable-expressroute-circuit-access-for-both-deployment-models"></a>ExpressRoute-circuit toegang inschakelen voor beide implementatie modellen

U kunt toegang tot het klassieke implementatie model inschakelen voor ExpressRoute-circuits die zijn gemaakt in het Resource Manager-implementatie model. Voer de volgende cmdlets uit om toegang tot beide implementatie modellen mogelijk te maken:

1. De details van het circuit ophalen.

   ```powershell
   $ckt = Get-AzExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
   ```

2. Stel klassieke bewerkingen toestaan in op waar.

   ```powershell
   $ckt.AllowClassicOperations = $true
   ```

3. Werk het circuit bij. Nadat deze bewerking is voltooid, kunt u het circuit bekijken in het klassieke implementatie model.

   ```powershell
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

4. Voer de volgende cmdlet uit om de details van het ExpressRoute-circuit op te halen. U moet de vermelde service sleutel kunnen zien.

   ```powershell
   get-azurededicatedcircuit
   ```

5. U kunt nu koppelingen naar het ExpressRoute-circuit beheren met de klassieke implementatie model opdrachten voor klassieke VNets, en de Resource Manager-opdrachten voor Resource Manager VNets. De volgende artikelen helpen u bij het beheren van koppelingen naar het ExpressRoute-circuit:

    * [Uw virtuele netwerk koppelen aan uw ExpressRoute-circuit in het Resource Manager-implementatie model](expressroute-howto-linkvnet-arm.md)
    * [Uw virtuele netwerk koppelen aan uw ExpressRoute-circuit in het klassieke implementatie model](expressroute-howto-linkvnet-classic.md)

### <a name="to-disable-expressroute-circuit-access-to-the-classic-deployment-model"></a>ExpressRoute-circuit toegang tot het klassieke implementatie model uitschakelen

Voer de volgende cmdlets uit om de toegang tot het klassieke implementatie model uit te scha kelen.

1. Meer informatie over het ExpressRoute-circuit.

   ```powershell
   $ckt = Get-AzExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
   ```

2. Stel klassieke bewerkingen toestaan in op ONWAAR.

   ```powershell
   $ckt.AllowClassicOperations = $false
   ```

3. Werk het circuit bij. Nadat deze bewerking is voltooid, is het niet mogelijk om het circuit weer te geven in het klassieke implementatie model.

   ```powershell
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

## <a name="next-steps"></a>Volgende stappen

* [Routering voor uw ExpressRoute-circuit maken en wijzigen](expressroute-howto-routing-arm.md)
* [Uw virtuele netwerk koppelen aan uw ExpressRoute-circuit](expressroute-howto-linkvnet-arm.md)
