---
title: Azure IoT Edge workloads implementeren (preview-versie)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Azure IoT Edge workloads implementeren
keywords: Kubernetes, Arc, azure, K8s, containers
ms.openlocfilehash: 88c480f93bfe28a424441a1c5857c623efb4e1d3
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99091644"
---
# <a name="deploy-azure-iot-edge-workloads-preview"></a>Azure IoT Edge workloads implementeren (preview-versie)

## <a name="overview"></a>Overzicht

Azure-Arc en Azure IoT Edge zijn de mogelijkheden van elkaar heel goed. Azure Arc biedt mechanismen voor cluster operators voor het configureren van de basis onderdelen van een cluster en het Toep assen en afdwingen van cluster beleidsregels. En IoT Edge kunnen toepassings exploitanten de werk belastingen op schaal op afstand implementeren en beheren met handige Cloud opname en bidirectionele communicatie primitieven. In het onderstaande diagram ziet u dit:

![IoT-Arc-configuratie](./media/edge-arc.png)

## <a name="pre-requisites"></a>Vereisten

* [Registreer een IOT edge apparaat](../../iot-edge/quickstart-linux.md#register-an-iot-edge-device) en [Implementeer de module gesimuleerde temperatuur sensor](../../iot-edge/quickstart-linux.md#deploy-a-module). Noteer de connection string van het apparaat.

* Gebruik [de ondersteuning van IOT Edge voor Kubernetes](https://aka.ms/edgek8sdoc) om deze te implementeren via de stroom operator van Azure Arc.

* Down load het bestand [**Values. yaml**](https://github.com/Azure/iotedge/blob/preview/iiot/kubernetes/charts/edge-kubernetes/values.yaml) voor IOT Edge helm-diagram en vervang de tijdelijke aanduiding **deviceConnectionString** aan het einde van het bestand door de naam die u in stap 1 hebt genoteerd. U kunt andere ondersteunde installatie opties voor grafieken instellen zoals vereist. Maak een naam ruimte voor de IoT Edge workload en maak er een geheim in:

    ```
    $ kubectl create ns iotedge

    $ kubectl create secret generic dcs --from-file=fully-qualified-path-to-values.yaml --namespace iotedge
    ```

    U kunt dit ook op afstand instellen met behulp van het [cluster configuratie voorbeeld](./use-gitops-connected-cluster.md).

## <a name="connect-a-cluster"></a>Verbinding maken met een cluster

Gebruik de `az` cli- `connectedk8s` extensie om een Kubernetes-cluster te verbinden met Azure Arc:

  ```
  az connectedk8s connect --name AzureArcIotEdge --resource-group AzureArcTest
  ```

## <a name="create-a-configuration-for-iot-edge"></a>Een configuratie maken voor IoT Edge

Voor beeld van opslag plaats: https://github.com/veyalla/edgearc

Deze opslag plaats verwijst naar het IoT Edge helm-diagram en verwijst naar het geheim dat is gemaakt in de sectie vereisten.

1. Gebruik de `az` cli- `k8sconfiguration` extensie om een configuratie te maken om het verbonden cluster te koppelen aan het git-opslag plaats:

    ```
    az k8sconfiguration create --name iotedge --cluster-name AzureArcIotEdge --resource-group AzureArcTest --operator-instance-name iotedge --operator-namespace azure-arc-iot-edge --enable-helm-operator --helm-operator-chart-version 0.6.0 --helm-operator-chart-values "--set helm.versions=v3" --repository-url "git://github.com/veyalla/edgearc.git" --cluster-scoped
    ```

    In een paar minuten moet u de IoT Edge werkbelasting modules weer geven die zijn geïmplementeerd in de `iotedge` naam ruimte in uw cluster. U kunt de logboeken van de `SimulatedTemperatureSensor` pod in die naam ruimte weer geven om te zien welke voorbeeld waarden worden gegenereerd. U kunt ook de berichten bekijken die binnenkomen op uw IoT-hub met behulp van de [Azure IOT hub Toolkit-extensie voor Visual Studio code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

## <a name="cleanup"></a>Opschonen

U kunt de configuratie verwijderen met:

```
az k8sconfiguration delete -g AzureArcTest --cluster-name AzureArcIotEdge --name iotedge
```

## <a name="next-steps"></a>Volgende stappen

[Azure Policy gebruiken om de cluster configuratie te bepalen](./use-azure-policy.md)
