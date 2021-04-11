---
title: Kubernetes-cluster uitbreidingen van Azure Arc enabled
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: De levens cyclus van uitbrei dingen op Azure Arc enabled Kubernetes implementeren en beheren
ms.openlocfilehash: 63fb14818d148dcc579300fdb4c89d636b47fd05
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451039"
---
# <a name="kubernetes-cluster-extensions"></a>Kubernetes-cluster uitbreidingen

De functie Kubernetes Extensions maakt het volgende mogelijk op Azure Arc ingeschakelde Kubernetes-clusters:

* Implementatie van cluster uitbreiding op basis van Azure Resource Manager.
* Levenscyclus beheer van uitbrei dingen helm-grafieken.

Een conceptueel overzicht van deze functie is beschikbaar in [cluster uitbreidingen: Azure Arc enabled Kubernetes-](conceptual-extensions.md) artikel.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Vereisten

- [Azure cli installeren of upgraden](https://docs.microsoft.com/cli/azure/install-azure-cli) naar versie >= 2.16.0.
- `connectedk8s` (versie >= 1.1.0) en `k8s-extension` (versie >= 0.2.0) Azure cli-extensies. Installeer deze Azure CLI-extensies door de volgende opdrachten uit te voeren:
  
    ```azurecli
    az extension add --name connectedk8s
    az extension add --name k8s-extension
    ```
    
    Als de `connectedk8s` `k8s-extension` extensies en al zijn geïnstalleerd, kunt u deze bijwerken naar de nieuwste versie met behulp van de volgende opdracht:

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8s-extension
    ```

- Er is een bestaand Kubernetes-verbonden Azure-Arc-cluster ingeschakeld.
    - Als u nog geen cluster hebt verbonden, gebruikt u onze [Quick](quickstart-connect-cluster.md)start.
    - [Werk uw agents](agent-upgrade.md#manually-upgrade-agents) bij naar versie >= 1.1.0.

## <a name="currently-available-extensions"></a>Momenteel beschik bare extensies

| Toestelnummer | Beschrijving |
| --------- | ----------- |
| [Azure Monitor](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md?toc=/azure/azure-arc/kubernetes/toc.json) | Geeft inzicht in de prestaties van werk belastingen die zijn geïmplementeerd op het Kubernetes-cluster. Verzamelt metrische gegevens over het geheugen en CPU-gebruik van controllers, knoop punten en containers. |
| [Azure Defender](../../security-center/defender-for-kubernetes-azure-arc.md?toc=/azure/azure-arc/kubernetes/toc.json) | Verzamelt gegevens van het controle logboek van knoop punten van het Kubernetes-cluster. Voorziet in aanbevelingen en bedreigingen op basis van verzamelde gegevens. |

## <a name="usage-of-cluster-extensions"></a>Gebruik van cluster uitbreidingen

### <a name="create-extensions-instance"></a>Extensies-exemplaar maken

Maak een nieuw extensie-exemplaar met `k8s-extension create` , waarbij u waarden opgeeft voor de verplichte para meters. Met de onderstaande opdracht maakt u een Azure Monitor voor containers-extensie-exemplaar in uw Azure Arc enabled Kubernetes-cluster:

```azurecli
az k8s-extension create --name azuremonitor-containers  --extension-type Microsoft.AzureMonitor.Containers --scope cluster --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

**Uitvoer**

```json
{
  "autoUpgradeMinorVersion": true,
  "configurationProtectedSettings": null,
  "configurationSettings": {
    "logAnalyticsWorkspaceResourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/defaultresourcegroup-eus/providers/microsoft.operationalinsights/workspaces/defaultworkspace-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-eus"
  },
  "creationTime": "2021-04-02T12:13:06.7534628+00:00",
  "errorInfo": {
    "code": null,
    "message": null
  },
  "extensionType": "microsoft.azuremonitor.containers",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/demo/providers/Microsoft.Kubernetes/connectedClusters/demo/providers/Microsoft.KubernetesConfiguration/extensions/azuremonitor-containers",
  "identity": null,
  "installState": "Pending",
  "lastModifiedTime": "2021-04-02T12:13:06.753463+00:00",
  "lastStatusTime": null,
  "name": "azuremonitor-containers",
  "releaseTrain": "Stable",
  "resourceGroup": "demo",
  "scope": {
    "cluster": {
      "releaseNamespace": "azuremonitor-containers"
    },
    "namespace": null
  },
  "statuses": [],
  "systemData": null,
  "type": "Microsoft.KubernetesConfiguration/extensions",
  "version": "2.8.2"
}
```

> [!NOTE]
> * De service kan geen gevoelige informatie meer dan 48 uur bewaren. Als Kubernetes-agents die zijn ingeschakeld voor Azure Arc, meer dan 48 uur geen netwerk verbinding hebben en niet kunnen bepalen of er een uitbrei ding op het cluster moet worden gemaakt, wordt de uitbrei ding overgezet naar `Failed` status. Eenmaal in `Failed` de staat moet u `k8s-extension create` opnieuw uitvoeren om een nieuwe extensie Azure-resource te maken.
> * * Azure Monitor voor containers is een singleton-extensie (maar één vereist per cluster). U moet eerdere helm-grafiek installaties van Azure Monitor voor containers (zonder uitbrei dingen) opschonen voordat u dezelfde via uitbrei dingen installeert. Volg de instructies voor [het verwijderen van het helm- `az k8s-extension create` diagram voordat u uitvoert ](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-optout-hybrid).

**Vereiste para meters**

| Parameternaam | Beschrijving |
|----------------|------------|
| `--name` | De naam van het extensie-exemplaar |
| `--extension-type` | Het type extensie dat u wilt installeren op het cluster. Bijvoorbeeld: micro soft. AzureMonitor. containers, micro soft. azuredefender. kubernetes | 
| `--scope` | Bereik van de installatie voor de extensie- `cluster` of `namespace` |
| `--cluster-name` | Naam van de Kubernetes-resource van Azure Arc waarvoor het extensie-exemplaar moet worden gemaakt |
| `--resource-group` | De resource groep met de Azure Arc enabled Kubernetes-resource |
| `--cluster-type` | Het cluster type waarop het extensie-exemplaar moet worden gemaakt. Alleen huidige `connectedClusters` , die overeenkomt met Azure Arc enabled Kubernetes, is een geaccepteerde waarde |

**Optionele para meters**

| Parameternaam | Beschrijving |
|--------------|------------|
| `--auto-upgrade-minor-version` | Booleaanse eigenschap waarmee wordt opgegeven of de secundaire versie van de extensie automatisch wordt bijgewerkt. Standaard: `true`.  Als deze para meter is ingesteld op waar, kunt u geen `version` para meter instellen, omdat de versie dynamisch wordt bijgewerkt. Als deze optie is ingesteld op `false` , wordt de extensie niet automatisch bijgewerkt, zelfs voor patch versies. |
| `--version` | Versie van de uitbrei ding die moet worden geïnstalleerd (specifieke versie om het extensie-exemplaar aan te koppelen). Mag niet worden opgegeven als de functie voor automatisch bijwerken-secundair-version is ingesteld op `true` . |
| `--configuration-settings` | Instellingen die kunnen worden door gegeven aan de extensie om de functionaliteit ervan te beheren. Ze moeten worden door gegeven als spaties gescheiden `key=value` paren na de parameter naam. Als deze para meter in de opdracht wordt gebruikt, `--configuration-settings-file` kan deze niet worden gebruikt in dezelfde opdracht. |
| `--configuration-settings-file` | Het pad naar het JSON-bestand met sleutel waardeparen die moeten worden gebruikt voor het door geven van de configuratie-instellingen aan de extensie. Als deze para meter in de opdracht wordt gebruikt, `--configuration-settings` kan deze niet worden gebruikt in dezelfde opdracht. |
| `--configuration-protected-settings` | Deze instellingen kunnen niet worden opgehaald met `GET` API-aanroepen of `az k8s-extension show` -opdrachten en worden daarom gebruikt voor het door geven van gevoelige instellingen. Ze moeten worden door gegeven als spaties gescheiden `key=value` paren na de parameter naam. Als deze para meter in de opdracht wordt gebruikt, `--configuration-protected-settings-file` kan deze niet worden gebruikt in dezelfde opdracht. |
| `--configuration-protected-settings-file` | Het pad naar het JSON-bestand met sleutel waardeparen die moeten worden gebruikt voor het door geven van gevoelige instellingen aan de uitbrei ding. Als deze para meter in de opdracht wordt gebruikt, `--configuration-protected-settings` kan deze niet worden gebruikt in dezelfde opdracht. |
| `--release-namespace` | Deze para meter geeft de naam ruimte aan waarbinnen de release moet worden gemaakt. Deze para meter is alleen relevant als de `scope` para meter is ingesteld op `cluster` . |
| `--release-train` |  Auteurs van extensies kunnen versies publiceren in verschillende release treinen zoals `Stable` , `Preview` enzovoort. Als deze para meter niet expliciet is ingesteld, `Stable` wordt de standaard waarde gebruikt. Deze para meter kan niet worden gebruikt als de `autoUpgradeMinorVersion` para meter is ingesteld op `false` . |
| `--target-namespace` | Deze para meter geeft de naam ruimte aan waarbinnen de release wordt gemaakt. De machtiging van het systeem account dat is gemaakt voor dit extensie-exemplaar, wordt beperkt tot deze naam ruimte. Deze para meter is alleen relevant als de `scope` para meter is ingesteld op `namespace` . |

### <a name="show-details-of-an-extension-instance"></a>Details van een extensie-exemplaar weer geven

Details van een geïnstalleerde extensie-exemplaar weer geven met `k8s-extension show` , in waarden door geven voor de verplichte para meters:

```azurecli
az k8s-extension show --name azuremonitor-containers --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

**Uitvoer**

```json
{
  "autoUpgradeMinorVersion": true,
  "configurationProtectedSettings": null,
  "configurationSettings": {
    "logAnalyticsWorkspaceResourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/defaultresourcegroup-eus/providers/microsoft.operationalinsights/workspaces/defaultworkspace-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-eus"
  },
  "creationTime": "2021-04-02T12:13:06.7534628+00:00",
  "errorInfo": {
    "code": null,
    "message": null
  },
  "extensionType": "microsoft.azuremonitor.containers",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/demo/providers/Microsoft.Kubernetes/connectedClusters/demo/providers/Microsoft.KubernetesConfiguration/extensions/azuremonitor-containers",
  "identity": null,
  "installState": "Installed",
  "lastModifiedTime": "2021-04-02T12:13:06.753463+00:00",
  "lastStatusTime": "2021-04-02T12:13:49.636+00:00",
  "name": "azuremonitor-containers",
  "releaseTrain": "Stable",
  "resourceGroup": "demo",
  "scope": {
    "cluster": {
      "releaseNamespace": "azuremonitor-containers"
    },
    "namespace": null
  },
  "statuses": [],
  "systemData": null,
  "type": "Microsoft.KubernetesConfiguration/extensions",
  "version": "2.8.2"
}
```

### <a name="list-all-extensions-installed-on-the-cluster"></a>Alle uitbrei dingen weer geven die op het cluster zijn geïnstalleerd

Alle uitbrei dingen weer geven die zijn geïnstalleerd op een cluster met `k8s-extension list` , waarbij waarden worden door gegeven voor de verplichte para meters.

```azurecli
az k8s-extension list --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

**Uitvoer**

```json
[
  {
    "autoUpgradeMinorVersion": true,
    "creationTime": "2020-09-15T02:26:03.5519523+00:00",
    "errorInfo": {
      "code": null,
      "message": null
    },
    "extensionType": "Microsoft.AzureMonitor.Containers",
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myRg/providers/Microsoft.Kubernetes/connectedClusters/myCluster/providers/Microsoft.KubernetesConfiguration/extensions/myExtInstanceName",
    "identity": null,
    "installState": "Pending",
    "lastModifiedTime": "2020-09-15T02:48:45.6469664+00:00",
    "lastStatusTime": null,
    "name": "myExtInstanceName",
    "releaseTrain": "Stable",
    "resourceGroup": "myRG",
    "scope": {
      "cluster": {
        "releaseNamespace": "myExtInstanceName1"
      }
    },
    "statuses": [],
    "type": "Microsoft.KubernetesConfiguration/extensions",
    "version": "0.1.0"
  },
  {
    "autoUpgradeMinorVersion": true,
    "creationTime": "2020-09-02T00:41:16.8005159+00:00",
    "errorInfo": {
      "code": null,
      "message": null
    },
    "extensionType": "microsoft.azuredefender.kubernetes",
    "id": "/subscriptions/0e849346-4343-582b-95a3-e40e6a648ae1/resourceGroups/myRg/providers/Microsoft.Kubernetes/connectedClusters/myCluster/providers/Microsoft.KubernetesConfiguration/extensions/defender",
    "identity": null,
    "installState": "Pending",
    "lastModifiedTime": "2020-09-02T00:41:16.8005162+00:00",
    "lastStatusTime": null,
    "name": "microsoft.azuredefender.kubernetes",
    "releaseTrain": "Stable",
    "resourceGroup": "myRg",
    "scope": {
      "cluster": {
        "releaseNamespace": "myExtInstanceName2"
      }
    },
    "type": "Microsoft.KubernetesConfiguration/extensions",
    "version": "0.1.0"
  }
]
```

### <a name="update-an-existing-extension-instance"></a>Een bestaand extensie-exemplaar bijwerken

Een extensie-exemplaar in een cluster bijwerken met `k8s-extension update` , waarbij de bij te werken waarden worden door gegeven.  Met deze opdracht worden alleen `auto-upgrade-minor-version` de `release-train` Eigenschappen, en, bijgewerkt `version` . Bijvoorbeeld:

- **Update van release-training:**

    ```azurecli
    az k8s-extension update --name azuremonitor-containers --cluster-type connectedClusters --cluster-name <clusterName> --resource-group <resourceGroupName> --release-train Preview
    ```

- **Automatisch bijwerken en uitbrei ding van extensie-exemplaar naar een specifieke versie uitschakelen:**

    ```azurecli
    az k8s-extension update --name azuremonitor-containers --cluster-type connectedClusters --cluster-name <clusterName> --resource-group <resourceGroupName> --auto-upgrade-minor-version false --version 2.2.2
    ```

- **Schakel de automatische upgrade voor het uitbreidings exemplaar in:**

    ```azurecli
    az k8s-extension update --name azuremonitor-containers --cluster-type connectedClusters --cluster-name <clusterName> --resource-group <resourceGroupName> --auto-upgrade-minor-version true
    ```

> [!NOTE]
> De `version` para meter kan alleen worden ingesteld als deze `--auto-upgrade-minor-version` is ingesteld op `false` .

### <a name="delete-extension-instance"></a>Extensie-exemplaar verwijderen

Verwijder een extensie-exemplaar in een cluster met `k8s-extension delete` , waarbij waarden worden door gegeven voor de verplichte para meters.

```azurecli
az k8s-extension delete --name azuremonitor-containers --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

>[!NOTE]
> De Azure-resource die deze uitbrei ding vertegenwoordigt, wordt onmiddellijk verwijderd. De helm-release in het cluster dat is gekoppeld aan deze extensie wordt alleen verwijderd wanneer de agents die op het Kubernetes-cluster worden uitgevoerd, verbinding hebben met het netwerk en de gewenste status weer kunnen bereiken.


## <a name="next-steps"></a>Volgende stappen

Meer informatie over de cluster uitbreidingen die momenteel beschikbaar zijn voor Azure Arc enabled Kubernetes:
> [!div class="nextstepaction"]
> [Azure monitor](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md?toc=/azure/azure-arc/kubernetes/toc.json) 
>  [Azure Defender](../../security-center/defender-for-kubernetes-azure-arc.md?toc=/azure/azure-arc/kubernetes/toc.json)