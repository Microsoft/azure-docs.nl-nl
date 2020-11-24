---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: fe37b0c9dbc16520a0dcb0993236db2797da6b68
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95557709"
---
## <a name="update-resources"></a>Resources bijwerken

Er zijn enkele beperkingen ten aanzien van wat er kan worden bijgewerkt. De volgende items kunnen worden bijgewerkt: 

Galerie met gedeelde installatiekopieën:
- Beschrijving

Definitie van installatiekopie:
- Aanbevolen vCPU's
- Aanbevolen geheugen
- Beschrijving
- Datum einde levensduur

Versie van installatiekopie:
- Aantal regionale replica's
- Doelregio's
- Uitsluiting van laatste
- Datum einde levensduur

Als u van plan bent om replica regio's toe te voegen, moet u de door de bron beheerde installatie kopie niet verwijderen. De door de bron beheerde installatie kopie is nodig voor het repliceren van de installatie kopie versie naar extra regio's. 

De beschrijving van een galerie bijwerken met ([AZ sig update](/cli/azure/sig?view=azure-cli-latest#az-sig-update). 

```azurecli-interactive
az sig update \
   --gallery-name myGallery \
   --resource-group myGalleryRG \
   --set description="My updated description."
```


De beschrijving van een definitie van een installatie kopie bijwerken met behulp van [AZ sig image definition update](/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update).

```azurecli-interactive
az sig image-definition update \
   --gallery-name myGallery\
   --resource-group myGalleryRG \
   --gallery-image-definition myImageDefinition \
   --set description="My updated description."
```

Een installatie kopie versie bijwerken om een regio toe te voegen die moet worden gerepliceerd met behulp van [AZ sig installatie kopie-versie bijwerken](/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update). Deze wijziging neemt enige tijd in beslag wanneer de afbeelding wordt gerepliceerd naar de nieuwe regio.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --add publishingProfile.targetRegions  name=eastus
```

In dit voor beeld ziet u hoe u [AZ sig image-version update](/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update) gebruikt om deze versie van de installatie kopie uit te sluiten van gebruik als de *nieuwste* afbeelding.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --set publishingProfile.excludeFromLatest=true
```

In dit voor beeld ziet u hoe u [AZ sig image-version update](/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update) gebruikt voor het toevoegen van deze installatie kopie versie die wordt overwogen voor de *nieuwste* afbeelding.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --set publishingProfile.excludeFromLatest=false
```

## <a name="delete-resources"></a>Resources verwijderen

U moet de resources in omgekeerde volg orde verwijderen door eerst de versie van de installatie kopie te verwijderen. Nadat u alle versies van de installatie kopie hebt verwijderd, kunt u de definitie van de installatie kopie verwijderen. Nadat u alle afbeeldings definities hebt verwijderd, kunt u de galerie verwijderen. 

Een installatie kopie versie verwijderen met [AZ sig installatie kopie-versie verwijderen](/cli/azure/sig/image-version?view=azure-cli-latest#az-sig-image-version-delete).

```azurecli-interactive
az sig image-version delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 
```

Een definitie van een installatie kopie verwijderen met [AZ sig image-definition delete](/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-delete).

```azurecli-interactive
az sig image-definition delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```


Een galerie met installatie kopieën verwijderen met [AZ sig delete](/cli/azure/sig?view=azure-cli-latest#az-sig-delete).

```azurecli-interactive
az sig delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery
```