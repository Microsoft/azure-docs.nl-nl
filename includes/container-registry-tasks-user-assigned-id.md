---
title: bestand opnemen
description: bestand opnemen
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 07/12/2019
ms.author: danlep
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: d81b6f5367efa92c9249956faa058441edf98561
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92755604"
---
### <a name="create-a-user-assigned-identity"></a>Een door de gebruiker toegewezen identiteit maken

Maak een identiteit met de naam *myACRTasksId* in uw abonnement met behulp van de opdracht [AZ Identity Create][az-identity-create] . U kunt dezelfde resource groep gebruiken die u eerder hebt gebruikt voor het maken van een container register of een andere.

```azurecli
az identity create \
  --resource-group myResourceGroup \
  --name myACRTasksId
```

Als u de door de gebruiker toegewezen identiteit in de volgende stappen wilt configureren, gebruikt u de opdracht [AZ Identity show][az-identity-show] om de resource-id, de principal-id en de client-id van de identiteit op te slaan in variabelen.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACRTasksId \
  --query id --output tsv)

# Get principal ID of the task's user-assigned identity
principalID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACRTasksId \
  --query principalId --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACRTasksId \
  --query clientId --output tsv)
```

<!-- LINKS - Internal -->
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
