---
title: Voor beeld van proximity placement groups voor schaal sets voor virtuele machines
description: Meer informatie over het maken en gebruiken van proximity-plaatsings groepen voor schaal sets voor virtuele Windows-machines in Azure.
author: cynthn
ms.author: cynthn
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 07/01/2019
ms.reviewer: zivr
ms.custom: mimckitt
ms.openlocfilehash: a3a7b50c82b3deffa7526bfcfe799972c3b02762
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98877863"
---
# <a name="preview-creating-and-using-proximity-placement-groups-using-powershell"></a>Voor beeld: proximity-plaatsings groepen maken en gebruiken met Power shell

Als u virtuele machines zo dicht mogelijk wilt ophalen, moet u de schaalset binnen een [proximity-plaatsings groep](../virtual-machines/co-location.md#proximity-placement-groups)implementeren.

Een proximity-plaatsings groep is een logische groepering die wordt gebruikt om ervoor te zorgen dat Azure Compute-resources zich fysiek dicht bij elkaar bevinden. Proximity-plaatsings groepen zijn handig voor werk belastingen waarbij lage latentie een vereiste is.

> [!IMPORTANT]
> Proximity-plaatsings groepen bevindt zich momenteel in een open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.
>
> Proximity placement groups zijn niet beschikbaar in deze regio's tijdens de preview-versie: **Japan-Oost**, **Australië-Oost** en **India centraal**.


## <a name="create-a-proximity-placement-group"></a>Een nabijheidsplaatsingsgroep maken
Maak een proximity-plaatsings groep met behulp van de cmdlet [New-AzProximityPlacementGroup](/powershell/module/az.compute/new-azproximityplacementgroup) . 

```azurepowershell-interactive
$resourceGroup = "myPPGResourceGroup"
$location = "East US"
$ppgName = "myPPG"
New-AzResourceGroup -Name $resourceGroup -Location $location
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard
```

## <a name="list-proximity-placement-groups"></a>Proximity-plaatsings groepen weer geven

U kunt alle proximity-plaatsings groepen weer geven met behulp van de cmdlet [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup) .

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-scale-set"></a>Een schaalset maken

Maak een schaal in de locatie van de Proximity-plaatsings groep met `-ProximityPlacementGroup $ppg.Id` om te verwijzen naar de locatie van de plaatsings groep bij gebruik van [New-AzVMSS](/powershell/module/az.compute/new-azvmss) om de schaalset te maken.

```azurepowershell-interactive
$scalesetName = "myVM"

New-AzVmss `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -VMScaleSetName $scalesetName `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -ProximityPlacementGroup $ppg.Id
```

U kunt het exemplaar in de plaatsings groep bekijken met [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup).

```azurepowershell-interactive
  Get-AzProximityPlacementGroup `
   -ResourceId $ppg.Id | Format-Table `
   -Wrap `
   -Property VirtualMachineScaleSets
```

## <a name="next-steps"></a>Volgende stappen

U kunt ook de [Azure cli](../virtual-machines/linux/proximity-placement-groups.md) gebruiken om proximity-plaatsings groepen te maken.
