---
title: Azure Dev Spaces inschakelen op AKS & de hulpprogramma's aan de clientzijde te installeren
services: azure-dev-spaces
ms.date: 07/24/2019
ms.topic: conceptual
description: Meer informatie over het inschakelen van Azure Dev Spaces op een AKS-cluster en het installeren van de hulpprogramma's aan de clientzijde.
ms.custom: devx-track-azurecli
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, servicemesh, servicemeshroutering, kubectl, k8s
ms.openlocfilehash: 079a9e1b28b315457ac20d3aa9e7d29ce28fa077
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505357"
---
# <a name="enable-azure-dev-spaces-on-an-aks-cluster-and-install-the-client-side-tools"></a>Azure Dev Spaces inschakelen op een AKS-cluster en de hulpprogramma's aan de clientzijde installeren

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

In dit artikel worden verschillende manieren beschreven om Azure Dev Spaces in teschakelen op een AKS-cluster en de hulpprogramma's aan de clientzijde te installeren.

## <a name="enable-azure-dev-spaces-using-the-azure-cli"></a>Azure Dev Spaces inschakelen met de Azure CLI

Voordat u Dev Spaces kunt inschakelen met behulp van de CLI, hebt u het volgende nodig:
* Een Azure-abonnement. Als u geen Azure-abonnement hebt, kunt u een [gratis account][az-portal-create-account] maken.
* [De Azure CLI is geïnstalleerd.][install-cli]
* [Een AKS-cluster][create-aks-cli] in een [ondersteunde regio][supported-regions].

Gebruik de opdracht `use-dev-spaces` om Dev Spaces in te schakelen voor uw AKS-cluster en volg de prompts.

```azurecli
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster
```

Met de bovenstaande opdracht schakelt u Dev Spaces in op het cluster *myAKSCluster* in *de* *groep myResourceGroup* en maakt u een standaard dev-ruimte.

```console
'An Azure Dev Spaces Controller' will be created that targets resource 'myAKSCluster' in resource group 'myResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'myAKSCluster' in resource group 'myResourceGroup' that targets resource 'myAKSCluster' in resource group 'myResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'myAKSCluster' in resource group 'myResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

Met `use-dev-spaces` de opdracht wordt ook de AZURE Dev Spaces CLI geïnstalleerd.

## <a name="install-the-client-side-tools"></a>De hulpprogramma's aan de clientzijde installeren

U kunt de hulpprogramma's aan de clientzijde van Azure Dev Spaces gebruiken om te communiceren met dev-ruimten op een AKS-cluster vanaf uw lokale computer. Er zijn verschillende manieren om de hulpprogramma's aan de clientzijde te installeren:

* Installeer [Visual Studio Azure][vscode] [Dev Spaces-extensie][vscode-extension]in Visual Studio Code.
* Installeer [Visual Studio 2019][visual-studio]de workload Azure Development.
* Download en installeer [de Windows-,][cli-win] [Mac-][cli-mac]of [Linux][cli-linux] CLI.

## <a name="remove-azure-dev-spaces-using-the-azure-cli"></a>Azure Dev Spaces verwijderen met behulp van de Azure CLI

Als u Azure Dev Spaces uit uw AKS-cluster wilt verwijderen, gebruikt u de `azds remove` opdracht .

```azurecli
azds remove -g MyResourceGroup -n MyAKS
```

In de onderstaande voorbeelduitvoer ziet u hoe Azure Dev Spaces uit het *MyAKS-cluster wordt* verwijderd.

```azurecli
$ azds remove -g MyResourceGroup -n MyAKS
Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup' will be deleted. This will remove Azure Dev Spaces instrumentation from the target resource for new workloads. Continue? (y/N): y

Deleting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAks' in resource group 'MyResourceGroup' (takes a few minutes)...
```

Alle naamruimten die u hebt gemaakt met Azure Dev Spaces blijven samen met hun workloads, maar nieuwe workloads in deze naamruimten worden niet instrumenteerd met Azure Dev Spaces. Als u bestaande pods die zijn instrumenteerd met Azure Dev Spaces opnieuw opstart, kunnen er bovendien fouten optreden. Deze pods moeten opnieuw worden geïmplementeerd zonder hulpprogramma's van Azure Dev Spaces. Als u Azure Dev Spaces volledig wilt verwijderen uit uw cluster, verwijdert u alle pods in alle naamruimten waarin Azure Dev Spaces is ingeschakeld.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe Azure Dev Spaces werkt.

> [!div class="nextstepaction"]
> [Hoe Azure Dev Spaces werkt](../how-dev-spaces-works.md)

[create-aks-cli]: ../../aks/kubernetes-walkthrough.md#create-a-resource-group
[install-cli]: /cli/azure/install-azure-cli
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[az-portal]: https://portal.azure.com
[az-portal-create-account]: https://azure.microsoft.com/free
[cli-linux]: https://aka.ms/get-azds-linux
[cli-mac]: https://aka.ms/get-azds-mac
[cli-win]: https://aka.ms/get-azds-windows
[visual-studio]: https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs
[visual-studio-k8s-tools]: https://aka.ms/get-vsk8stools
[vscode]: https://code.visualstudio.com/download
[vscode-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
