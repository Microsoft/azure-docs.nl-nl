---
title: Een Kubernetes-cluster maken met Azure dev Spaces ingeschakeld-Azure Cloud Shell
services: azure-dev-spaces
ms.date: 10/04/2018
ms.topic: conceptual
description: Meer informatie over hoe u snel een Kubernetes-cluster kunt maken dat rechtstreeks vanuit uw browser kan worden geactiveerd zonder dat u iets hoeft te installeren.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, servicemesh, servicemeshroutering, kubectl, k8s
ms.openlocfilehash: ce73f46a2451dfa87751d90f9cd6b31d5c80683f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91963547"
---
# <a name="create-a-kubernetes-cluster-with-azure-dev-spaces-enabled-with-azure-cloud-shell"></a>Een Kubernetes-cluster maken met Azure dev Spaces ingeschakeld met Azure Cloud Shell

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

U kunt [Azure Cloud shell](/azure/cloud-shell) gebruiken om een Azure Kubernetes-service cluster te maken met behulp van de knop **try it** van deze pagina. Als u niet bent aangemeld, volgt u de aanwijzingen om u aan te melden met een Azure-account. vervolgens typt u de opdrachten op de Azure Cloud Shell prompt wanneer deze wordt weer gegeven.

## <a name="create-the-cluster"></a>Het cluster maken

Maak eerst de resource groep in een [regio die Azure-ontwikkel ruimten ondersteunt][supported-regions].

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

Maak een Kubernetes-cluster met de volgende opdracht:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --generate-ssh-keys
```

Het duurt een paar minuten om het cluster te maken.  Wanneer dit is voltooid, wordt de uitvoer weer gegeven in de JSON-indeling. Zoek `provisioningState` en controleer het `Succeeded` .

## <a name="next-steps"></a>Volgende stappen

Zie [Azure dev Spaces](../index.yml) voor koppelingen naar volledige zelf studies.

> [!IMPORTANT]
> Veel van de Azure dev Spaces Quick starts en zelf studies gebruiken de Azure dev Spaces CLI om bewerkingen uit te voeren. U kunt de CLI voor Azure Dev Spaces niet installeren in Azure Cloud Shell.


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
