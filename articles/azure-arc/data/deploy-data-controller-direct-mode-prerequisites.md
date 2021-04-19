---
title: Vereisten | Directe verbindingsmodus
description: Vereisten voor het implementeren van de gegevenscontroller in de modus voor directe verbinding.
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 03/31/2021
ms.topic: overview
ms.openlocfilehash: 940c08dc56e8b0f2217e556da1af31b44ceeda0a
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107716309"
---
# <a name="deploy-data-controller---direct-connect-mode-prerequisites"></a>Gegevenscontroller implementeren - modus voor directe verbinding (vereisten)

In dit artikel wordt beschreven hoe u de implementatie van een gegevenscontroller voor Azure Arc-gegevensservices in de modus voor directe verbinding voorbereidt.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

In een samenvatting op hoog niveau zijn de vereisten onder andere:

1. Hulpprogramma's installeren
1. Uitbreidingen toevoegen
1. De service-principal maken en rollen configureren voor metrische gegevens
1. Kubernetes-cluster verbinden met Azure met behulp Azure Arc Kubernetes

Nadat u aan deze vereisten hebt voltooid, kunt u [een Azure Arc controller implementeren | Directe verbindingsmodus](deploy-data-controller-direct-mode.md).

In de resterende secties van dit artikel worden de vereisten beschreven.

## <a name="install-tools"></a>Hulpprogramma's installeren

- Helm-versie 3.3+ ([installeren](https://helm.sh/docs/intro/install/))
- Azure CLI ([installeren](/sql/azdata/install/deploy-install-azdata))

## <a name="add-extensions-for-azure-cli"></a>Extensies toevoegen voor Azure CLI

Daarnaast zijn de volgende az-extensies ook vereist:
- Azure `k8s-extension` CLI-extensie (0.2.0)
- Azure CLI `customlocation` (0.1.0)

Voorbeeld `az` en de CLI-extensies zijn:

```console
$ az version
{
  "azure-cli": "2.19.1",
  "azure-cli-core": "2.19.1",
  "azure-cli-telemetry": "1.0.6",
  "extensions": {
    "connectedk8s": "1.1.0",
    "customlocation": "0.1.0",
    "k8s-configuration": "1.0.0",
    "k8s-extension": "0.2.0"
  }
}
```

## <a name="create-service-principal-and-configure-roles-for-metrics"></a>Service-principal maken en rollen configureren voor metrische gegevens

Volg de stappen die worden beschreven in het artikel [Metrische gegevens uploaden](upload-metrics-and-logs-to-azure-monitor.md) en maak een service-principal en verleen de rollen zoals beschreven in het artikel. 

De SPN ClientID-, TenantID- en Client Secret-gegevens zijn vereist wanneer u [de Azure Arc implementeert.](deploy-data-controller-direct-mode.md) 

## <a name="connect-kubernetes-cluster-to-azure-using-azure-arc-enabled-kubernetes"></a>Kubernetes-cluster verbinden met Azure met behulp Azure Arc Kubernetes

Volg de stappen in Een bestaand [Kubernetes-cluster verbinden](../kubernetes/quickstart-connect-cluster.md)met Azure Arc om deze taak te voltooien.

## <a name="next-steps"></a>Volgende stappen

Nadat u aan deze vereisten hebt voltooid, kunt u [een Azure Arc implementeren | Directe verbindingsmodus](deploy-data-controller-direct-mode.md).
