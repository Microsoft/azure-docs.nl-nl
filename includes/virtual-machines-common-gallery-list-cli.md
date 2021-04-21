---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 01/28/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 83d70a8d4c5806120ddb4ea776a8f4a6f7e63857
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107799929"
---
## <a name="list-information"></a>Lijstgegevens

Haal de locatie, status en andere informatie over de beschikbare galerieën met afbeeldingen op [met az sig list](/cli/azure/sig#az_sig_list).

```azurecli-interactive 
az sig list -o table
```

Vermeld de definities van de afbeeldingen in een galerie, inclusief informatie over het type besturingssysteem en de status, met [behulp van az sig image-definition list](/cli/azure/sig/image-definition#az_sig_image_definition_list).

```azurecli-interactive 
az sig image-definition list --resource-group myGalleryRG --gallery-name myGallery -o table
```

Gebruik [az sig image-version list](/cli/azure/sig/image-version#az_sig_image_version_list)om de versies van gedeelde afbeeldingen in een galerie weer te geven.

```azurecli-interactive
az sig image-version list --resource-group myGalleryRG --gallery-name myGallery --gallery-image-definition myImageDefinition -o table
```

Haal de id van een versie van een afbeelding op [met az sig image-version show](/cli/azure/sig/image-version#az_sig_image_version_show).

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id"
```
