---
title: Aangepaste locaties op Azure Arc enabled Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
ms.custom: references_regions
description: Aangepaste locaties gebruiken voor het implementeren van Azure PaaS Services op Azure-Kubernetes-clusters die zijn ingeschakeld
ms.openlocfilehash: ddda6420acd7126cb46b043f5c1bce67758342bc
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451040"
---
# <a name="custom-locations-on-azure-arc-enabled-kubernetes"></a>Aangepaste locaties op Azure Arc enabled Kubernetes

Als *Azure-locatie* -extensie kunnen Tenant beheerders hun Azure Arc enabled Kubernetes-clusters gebruiken als doel locaties voor het implementeren van Azure Services-exemplaren. Azure-resources-voor beelden zijn onder meer Azure Arc enabled SQL Managed instance en Azure Arc enabled PostgreSQL grootschalige.

Net als bij Azure-locaties kunnen eind gebruikers in de Tenant met toegang tot aangepaste locaties resources implementeren met behulp van de persoonlijke Compute van hun bedrijf.

Een conceptueel overzicht van deze functie is beschikbaar op [aangepaste locaties: Azure Arc enabled Kubernetes-](conceptual-custom-locations.md) artikel.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Vereisten

- [Azure cli installeren of upgraden](https://docs.microsoft.com/cli/azure/install-azure-cli) naar versie >= 2.16.0.

- `connectedk8s` (versie >= 1.1.0), `k8s-extension` (versie >= 0.2.0) en `customlocation` (versie >= 0.1.0) Azure cli-extensies. Installeer deze Azure CLI-extensies door de volgende opdrachten uit te voeren:
  
    ```azurecli
    az extension add --name connectedk8s
    az extension add --name k8s-extension
    az extension add --name customlocation
    ```
    
    Als de `connectedk8s` `k8s-extension` extensies en `customlocation` al zijn geïnstalleerd, kunt u deze bijwerken naar de nieuwste versie met behulp van de volgende opdracht:

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8s-extension
    az extension update --name customlocation
    ```

- Registratie van provider is voltooid voor `Microsoft.ExtendedLocation` .
    1. Voer de volgende opdrachten in:
    
    ```azurecli
    az provider register --namespace Microsoft.ExtendedLocation
    ```

    2. Het registratie proces bewaken. Het kan tot tien minuten duren voordat de registratie is uitgevoerd.
    
    ```azurecli
    az provider show -n Microsoft.ExtendedLocation -o table
    ```

>[!NOTE]
>**Ondersteunde regio's voor aangepaste locaties:**
>* VS - oost
>* Europa -west

## <a name="enable-custom-locations-on-cluster"></a>Aangepaste locaties op het cluster inschakelen

Als u deze functie op uw cluster wilt inschakelen, voert u de volgende opdracht uit:

```console
az connectedk8s enable-features -n <clusterName> -g <resourceGroupName> --features cluster-connect custom-locations
```

> [!NOTE]
> 1. De functie voor aangepaste locaties is afhankelijk van de functie cluster Connect. Daarom moeten beide functies zijn ingeschakeld voor het werken met aangepaste locaties.
> 2. `az connectedk8s enable-features` moet worden uitgevoerd op een computer waarop het `kubeconfig` bestand verwijst naar het cluster waarop de functies moeten worden ingeschakeld.

## <a name="create-custom-location"></a>Aangepaste locatie maken

1. Maak een Azure Arc enabled Kubernetes-cluster.
    - Als u nog geen cluster hebt verbonden, gebruikt u onze [Quick](quickstart-connect-cluster.md)start.
    - [Werk uw agents](agent-upgrade.md#manually-upgrade-agents) bij naar versie >= 1.1.0.

1. Implementeer de cluster uitbreiding van de Azure-service waarvan u wilt dat deze uiteindelijk boven op de aangepaste locatie valt:

    ```azurecli
    az k8s-extension create --name <extensionInstanceName> --extension-type microsoft.arcdataservices --cluster-type connectedClusters -c <clusterName> -g <resourceGroupName> --scope cluster --release-namespace arc --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper
    ```

    > [!NOTE]
    > Uitgaande proxy zonder verificatie en uitgaande proxy met basis verificatie worden ondersteund door de Arc enabled Data Services-cluster extensie. De uitgaande proxy waarmee vertrouwde certificaten worden verwacht, wordt momenteel niet ondersteund.

1. Haal de Azure Resource Manager-id op van het Azure Arc enabled Kubernetes-cluster, dat in latere stappen wordt genoemd als `connectedClusterId` :

    ```azurecli
    az connectedk8s show -n <clusterName> -g <resourceGroupName>  --query id -o tsv
    ```

1. Haal de Azure Resource Manager-id van de cluster uitbreiding die is geïmplementeerd boven op Azure Arc enabled Kubernetes-cluster, in latere stappen als `extensionId` volgt:

    ```azurecli
    az k8s-extension show --name <extensionInstanceName> --cluster-type connectedClusters -c <clusterName> -g <resourceGroupName>  --query id -o tsv
    ```

1. Maak een aangepaste locatie door te verwijzen naar het Azure Arc enabled Kubernetes-cluster en de uitbrei ding:

    ```azurecli
    az customlocation create -n <customLocationName> -g <resourceGroupName> --namespace arc --host-resource-id <connectedClusterId> --cluster-extension-ids <extensionId>
    ```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> Veilig verbinding maken met het cluster met [cluster Connect](cluster-connect.md)