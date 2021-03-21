---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 807cbd283cf7971bf4256451028ffa16a0911266
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96025618"
---
## <a name="create-an-image-gallery"></a>Een galerie met installatiekopieën maken 

Een galerie met installatiekopieën is de primaire resource die wordt gebruikt voor het inschakelen van het delen van installatiekopieën. De naam van de galerie kan bestaan uit hoofdletters en kleine letters, cijfers en punten. De naam van de galerie kan geen liggende streepjes bevatten. De naam van de galerie moet uniek zijn binnen uw abonnement. 

Maak een galerie met installatiekopieën met de opdracht [New-AzGallery](/powershell/module/az.compute/new-azgallery). In het volgende voorbeeld wordt een galerie met de naam *myGallery* gemaakt in de resourcegroep *myGalleryRG*.

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'West Central US'  
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Shared Image Gallery for my organization'  
```
   

## <a name="share-the-gallery"></a>De galerie delen

We raden aan om te delen op galerieniveau. Gebruik een e-mailadres en de cmdlet [Get-AzADUser](/powershell/module/az.resources/get-azaduser) om de object-id voor gebruikers op te halen en gebruik vervolgens [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) om hen toegang te geven tot de galerie. Vervang het e-mailadres, alinne_montes@contoso.com in dit voorbeeld, door uw eigen gegevens.

```azurepowershell-interactive
# Get the object ID for the user
$user = Get-AzADUser -StartsWith alinne_montes@contoso.com
# Grant access to the user for our gallery
New-AzRoleAssignment `
   -ObjectId $user.Id `
   -RoleDefinitionName Reader `
   -ResourceName $gallery.Name `
   -ResourceType Microsoft.Compute/galleries `
   -ResourceGroupName $resourceGroup.ResourceGroupName

```