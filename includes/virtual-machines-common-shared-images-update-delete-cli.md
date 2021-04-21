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
ms.openlocfilehash: b966f68e19794aadebff76e3e9b29ed79a32eebe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800215"
---
## <a name="update-resources"></a>Resources bijwerken

Er gelden enkele beperkingen voor wat kan worden bijgewerkt. De volgende items kunnen worden bijgewerkt: 

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
- Uitsluiting van de meest recente
- Datum einde levensduur

Als u van plan bent om replicaregio's toe te voegen, moet u de door de bron beheerde afbeelding niet verwijderen. De door de bron beheerde afbeelding is nodig voor het repliceren van de versie van de afbeelding naar aanvullende regio's. 

Werk de beschrijving van een galerie bij met behulp van ([az sig update](/cli/azure/sig#az_sig_update). 

```azurecli-interactive
az sig update \
   --gallery-name myGallery \
   --resource-group myGalleryRG \
   --set description="My updated description."
```


Werk de beschrijving van een afbeeldingsdefinitie bij [met az sig image-definition update](/cli/azure/sig/image-definition#az_sig_image_definition_update).

```azurecli-interactive
az sig image-definition update \
   --gallery-name myGallery\
   --resource-group myGalleryRG \
   --gallery-image-definition myImageDefinition \
   --set description="My updated description."
```

Werk een versie van de afbeelding bij om een regio toe te voegen om te repliceren naar [met az sig image-version update](/cli/azure/sig/image-definition#az_sig_image_definition_update). Deze wijziging duurt even terwijl de afbeelding naar de nieuwe regio wordt gerepliceerd.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --add publishingProfile.targetRegions  name=eastus
```

In dit voorbeeld ziet u hoe u [az sig image-version update](/cli/azure/sig/image-definition#az_sig_image_definition_update) gebruikt om uit te sluiten dat deze versie van de afbeelding wordt gebruikt als de *meest recente* afbeelding.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --set publishingProfile.excludeFromLatest=true
```

In dit voorbeeld ziet u hoe u [az sig image-version update](/cli/azure/sig/image-definition#az_sig_image_definition_update) gebruikt om deze versie van de afbeelding op te nemen in de meest *recente afbeelding.*

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --set publishingProfile.excludeFromLatest=false
```

## <a name="delete-resources"></a>Resources verwijderen

U moet resources in omgekeerde volgorde verwijderen door eerst de versie van de afbeelding te verwijderen. Nadat u alle versies van de afbeelding hebt verwijderd, kunt u de definitie van de afbeelding verwijderen. Nadat u alle definities van afbeeldingen hebt verwijderd, kunt u de galerie verwijderen. 

Verwijder een versie van de afbeelding [met az sig image-version delete.](/cli/azure/sig/image-version#az_sig_image_version_delete)

```azurecli-interactive
az sig image-version delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 
```

Verwijder een definitie van een afbeelding [met az sig image-definition delete.](/cli/azure/sig/image-definition#az_sig_image_definition_delete)

```azurecli-interactive
az sig image-definition delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```


Verwijder een galerie met afbeeldingen met [az sig delete.](/cli/azure/sig#az_sig_delete)

```azurecli-interactive
az sig delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery
```