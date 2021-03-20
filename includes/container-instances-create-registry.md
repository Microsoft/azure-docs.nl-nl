---
title: bestand opnemen
description: bestand opnemen
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 08/13/2020
ms.author: danlep
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 173c9156f253e43111299b53287e97ab7b2c0aa5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92746933"
---
## <a name="create-azure-container-registry"></a>Azure Container Registry maken

Voordat u het containerregister maakt, hebt u een *resourcegroep* nodig om dit in te implementeren. Een resourcegroep is een logische verzameling waarin alle Azure-resources worden geïmplementeerd en beheerd.

Een resourcegroep maken met de opdracht [az group create][az-group-create]. In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt in de regio *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Zodra u de resourcegroep hebt gemaakt, maakt u een Azure Container Registry met de opdracht [az acr create][az-acr-create]. De containerregisternaam moet uniek zijn binnen Azure en mag 5 tot 50 alfanumerieke tekens bevatten. Vervang `<acrName>` door een unieke naam voor het register:

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

Hier volgt een gedeeltelijke uitvoer voor een nieuw Azure-containerregister met de naam *mycontainerregistry082*:

```output
{
  "creationDate": "2020-07-16T21:54:47.297875+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/mycontainerregistry082",
  "location": "eastus",
  "loginServer": "mycontainerregistry082.azurecr.io",
  "name": "mycontainerregistry082",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

In de rest van de zelfstudie wordt naar `<acrName>` verwezen als tijdelijke aanduiding voor de containerregisternaam die u in deze stap hebt gekozen.

## <a name="log-in-to-container-registry"></a>Aanmelden bij het containerregister

U moet u aanmelden bij het Azure Container Registry-exemplaar voordat u installatiekopieën kunt pushen. Gebruik de opdracht [az acr login][az-acr-login] om de bewerking te voltooien. U moet de unieke naam van het containerregister opgeven die u hebt gekozen toen u het maakte.

```azurecli
az acr login --name <acrName>
```

Bijvoorbeeld:

```azurecli
az acr login --name mycontainerregistry082
```

De opdracht retourneert `Login Succeeded` nadat deze is voltooid:

```output
Login Succeeded
```

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-group-create]: /cli/azure/group#az-group-create
