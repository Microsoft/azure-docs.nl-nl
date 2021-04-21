---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/24/2020
ms.author: cynthn
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: fc35f277bfafa80f6239ef807f1a83591646a503
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107799988"
---
## <a name="create-an-image-gallery"></a>Een galerie met installatiekopieën maken 

Een galerie met installatiekopieën is de primaire resource die wordt gebruikt voor het inschakelen van het delen van installatiekopieën. 

De naam van de galerie kan bestaan uit hoofdletters en kleine letters, cijfers en punten. De naam van de galerie kan geen liggende streepjes bevatten.   De naam van de galerie moet uniek zijn binnen uw abonnement. 

Een galerie met installatiekopieën maken met [az sig create](/cli/azure/sig#az_sig_create). In het volgende voorbeeld wordt een resourcegroep gemaakt genaamd galerie met de naam *myGalleryRG* in *US -oost* en een galerie met de naam *myGallery*.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="share-the-gallery"></a>De galerie delen

U kunt installatiekopieën delen met meerdere abonnementen met behulp van toegangsbeheer op basis van rollen (RBAC). U kunt afbeeldingen delen op het niveau van de galerie, de definitie van de afbeelding of de versie van de afbeelding. Elke gebruiker die leesmachtigingen heeft voor een installatiekopieversie, ook in meerdere abonnementen, kan een virtuele machine implementeren op basis van de installatiekopieversie.

We raden aan om te delen met andere gebruikers op galerieniveau. Gebruik [az sig show](/cli/azure/sig#az_sig_show) om de object-id van uw galerie op te halen.

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Gebruik de object-id als een bereik, samen met een e-mailadres en [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) om een gebruiker toegang te geven tot de gedeelde galerie met installatiekopieën. Vervang `<email-address>` en `<gallery iD>` door uw eigen gegevens.

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

Zie [Toegang beheren met toegangsbeheer op basis van rollen en Azure CLI](../articles/role-based-access-control/role-assignments-cli.md) voor meer informatie over het delen van resources via RBAC.