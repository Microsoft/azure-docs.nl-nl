---
title: Azure IoT Edge workloads implementeren (preview-versie)
services: azure-arc
ms.service: azure-arc
ms.date: 02/10/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Azure IoT Edge workloads implementeren
keywords: Kubernetes, Arc, azure, K8s, containers
ms.openlocfilehash: f228b79f14ab24281415cd4bd5964fc86a095d3c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100390433"
---
# <a name="deploy-azure-iot-edge-workloads-preview"></a>Azure IoT Edge workloads implementeren (preview-versie)

## <a name="overview"></a>Overzicht

Azure-Arc en Azure IoT Edge bieden eenvoudig een aanvulling op de mogelijkheden van elkaar. 

Azure Arc biedt mechanismen voor cluster operators voor het configureren van de basis onderdelen van een cluster en het Toep assen en afdwingen van cluster beleid. 

Met Azure IoT Edge kunnen Opera tors op afstand de workloads op schaal implementeren en beheren met handige Cloud opname en bidirectionele communicatie primitieven. 

In het onderstaande diagram ziet u de relatie van Azure Arc en Azure IoT Edge:

![IoT-Arc-configuratie](./media/edge-arc.png)

## <a name="pre-requisites"></a>Vereisten

* [Registreer een IOT edge apparaat](../../iot-edge/quickstart-linux.md#register-an-iot-edge-device) en [Implementeer de module gesimuleerde temperatuur sensor](../../iot-edge/quickstart-linux.md#deploy-a-module). Noteer de connection string van het apparaat voor de *waarden. yaml* die hieronder worden beschreven.

* Gebruik [de ondersteuning van IOT Edge voor Kubernetes](https://aka.ms/edgek8sdoc) om deze te implementeren via de stroom operator van Azure Arc.

* Down load het bestand [*Values. yaml*](https://github.com/Azure/iotedge/blob/preview/iiot/kubernetes/charts/edge-kubernetes/values.yaml) voor IOT Edge helm-diagram en vervang de `deviceConnectionString` tijdelijke aanduiding aan het einde van het bestand door de Connection String die u eerder hebt genoteerd. Stel zo nodig andere installatie opties voor de grafiek in. Maak een naam ruimte voor de IoT Edge workload en Genereer er een geheim in:

  ```
  $ kubectl create ns iotedge

  $ kubectl create secret generic dcs --from-file=fully-qualified-path-to-values.yaml --namespace iotedge
  ```

  U kunt ook op afstand instellen met behulp van het [cluster configuratie voorbeeld](./use-gitops-connected-cluster.md).

## <a name="connect-a-cluster"></a>Verbinding maken met een cluster

Gebruik de `az` Azure cli- `connectedk8s` extensie om een Kubernetes-cluster te verbinden met Azure Arc:

  ```
  az connectedk8s connect --name AzureArcIotEdge --resource-group AzureArcTest
  ```

## <a name="create-a-configuration-for-iot-edge"></a>Een configuratie maken voor IoT Edge

Het [voor beeld](https://github.com/veyalla/edgearc) van een Git-opslag plaats wijst naar het IOT Edge helm-diagram en verwijst naar het geheim dat is gemaakt in de sectie vereisten.

Gebruik de `az` Azure cli- `k8sconfiguration` extensie om een configuratie te maken die het verbonden cluster koppelt aan de Git-opslag plaats:

  ```
  az k8sconfiguration create --name iotedge --cluster-name AzureArcIotEdge --resource-group AzureArcTest --operator-instance-name iotedge --operator-namespace azure-arc-iot-edge --enable-helm-operator --helm-operator-chart-version 0.6.0 --helm-operator-chart-values "--set helm.versions=v3" --repository-url "git://github.com/veyalla/edgearc.git" --cluster-scoped
  ```

In een paar minuten ziet u de IoT Edge werkbelasting modules die in de naam ruimte van uw cluster zijn geïmplementeerd `iotedge` . 

Bekijk de `SimulatedTemperatureSensor` pod-Logboeken in die naam ruimte om de voorbeeld waarden te zien die worden gegenereerd. U kunt ook de berichten bekijken die binnenkomen op uw IoT-hub met behulp van de [Azure IOT hub Toolkit-extensie voor Visual Studio code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

## <a name="cleanup"></a>Opschonen

De configuratie verwijderen met:

```
az k8sconfiguration delete -g AzureArcTest --cluster-name AzureArcIotEdge --name iotedge
```

## <a name="next-steps"></a>Volgende stappen

[Azure Policy gebruiken om de cluster configuratie te bepalen](./use-azure-policy.md)
