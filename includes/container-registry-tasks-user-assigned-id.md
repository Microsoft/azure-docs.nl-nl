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
ms.openlocfilehash: 70bd79ef944e5918d750a130bd2e2b2c2b656bf4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781115"
---
### <a name="create-a-user-assigned-identity"></a>Een door de gebruiker toegewezen identiteit maken

Maak een identiteit met de *naam myACRTasksId* in uw abonnement met behulp van [de opdracht az identity create.][az-identity-create] U kunt dezelfde resourcegroep gebruiken die u eerder hebt gebruikt om een containerregister of een ander containerregister te maken.

```azurecli
az identity create \
  --resource-group myResourceGroup \
  --name myACRTasksId
```

Als u de door de gebruiker toegewezen identiteit in de volgende stappen wilt configureren, gebruikt u de opdracht [az identity show][az-identity-show] om de resource-id, principal-id en client-id van de identiteit op te slaan in variabelen.

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
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-identity-show]: /cli/azure/identity#az_identity_show
