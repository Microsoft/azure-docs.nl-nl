---
title: Helm-grafieken implementeren met behulp van GitOps op Kubernetes-cluster waarvoor Arc is ingeschakeld (preview)
services: azure-arc
ms.service: azure-arc
ms.date: 02/09/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: GitOps gebruiken met helm voor een Azure Arc-cluster configuratie (preview-versie)
keywords: GitOps, Kubernetes, K8s, azure, helm, Arc, AKS, Azure Kubernetes service, containers
ms.openlocfilehash: 883eb9c152bdc8a7c0e60e999cf9decf47fb80ec
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377921"
---
# <a name="deploy-helm-charts-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>Helm-grafieken implementeren met behulp van GitOps op Kubernetes-cluster waarvoor Arc is ingeschakeld (preview)

Helm is een opensource-inpakprogramma waarmee u Kubernetes-toepassingen kunt installeren en de levenscyclus hiervan kunt beheren. Net als Linux-pakket beheer, zoals APT en yum, wordt helm gebruikt voor het beheren van Kubernetes-grafieken, die pakketten van vooraf geconfigureerde Kubernetes-resources zijn.

In dit artikel wordt beschreven hoe u helm configureert en gebruikt met Azure Arc enabled Kubernetes.

## <a name="before-you-begin"></a>Voordat u begint

Controleer of er een bestaand Kubernetes-verbonden Azure-Arc-cluster is ingeschakeld. Als u een verbonden cluster nodig hebt, gaat u naar de [Snelstartgids een Azure-Kubernetes-cluster verbinden](./connect-cluster.md).

## <a name="overview-of-using-gitops-and-helm-with-azure-arc-enabled-kubernetes"></a>Overzicht van het gebruik van GitOps en helm met Azure Arc enabled Kubernetes

 De helm-operator biedt een uitbrei ding van stroom waarmee helm-grafiek releases worden geautomatiseerd. Een helm-grafiek release wordt beschreven via een aangepaste Kubernetes-resource met de naam HelmRelease. Stroom synchroniseert deze resources van Git naar het cluster, terwijl de helm-operator ervoor zorgt dat helm-grafieken worden vrijgegeven zoals opgegeven in de resources.

 De [voor beeld-opslag plaats](https://github.com/Azure/arc-helm-demo) die in dit artikel wordt gebruikt, is op de volgende manier gestructureerd:

```console
├── charts
│   └── azure-arc-sample
│       ├── Chart.yaml
│       ├── templates
│       │   ├── NOTES.txt
│       │   ├── deployment.yaml
│       │   └── service.yaml
│       └── values.yaml
└── releases
    └── app.yaml
```

In de Git-opslag plaats hebben we twee directory's: één die een helm-grafiek bevat en één die de configuratie van de releases bevat. In de `releases` map bevat de de `app.yaml` HelmRelease-configuratie, zoals hieronder weer gegeven:

```yaml
apiVersion: helm.fluxcd.io/v1
kind: HelmRelease
metadata:
  name: azure-arc-sample
  namespace: arc-k8s-demo
spec:
  releaseName: arc-k8s-demo
  chart:
    git: https://github.com/Azure/arc-helm-demo
    ref: master
    path: charts/azure-arc-sample
  values:
    serviceName: arc-k8s-demo
```

De helm-release configuratie bevat de volgende velden:

| Veld | Description |
| ------------- | ------------- | 
| `metadata.name` | Verplicht veld. De naam conventies van Kubernetes moeten volgen. |
| `metadata.namespace` | Optioneel veld. Bepaalt waar de release wordt gemaakt. |
| `spec.releaseName` | Optioneel veld. Als u niets opgeeft, wordt de naam van de release `$namespace-$name` . |
| `spec.chart.path` | De map met de grafiek, opgegeven ten opzichte van de hoofdmap van de opslag plaats. |
| `spec.values` | Gebruikers aanpassingen van standaard parameter waarden in de grafiek zelf. |

De opties die zijn opgegeven in de HelmRelease `spec.values` , overschrijven de opties die zijn opgegeven in `values.yaml` uit de grafiek bron.

Meer informatie over de HelmRelease vindt u in de officiële [helm-operator documentatie](https://docs.fluxcd.io/projects/helm-operator/en/stable/).

## <a name="create-a-configuration"></a>Een configuratie maken

Koppel uw verbonden cluster met de Azure CLI-extensie voor `k8sconfiguration` aan de voor beeld van de Git-opslag plaats. Geef deze configuratie de naam `azure-arc-sample` en implementeer de stroom operator in de `arc-k8s-demo` naam ruimte.

```console
az k8sconfiguration create --name azure-arc-sample --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name flux --operator-namespace arc-k8s-demo --operator-params='--git-readonly --git-path=releases' --enable-helm-operator --helm-operator-version='0.6.0' --helm-operator-params='--set helm.versions=v3' --repository-url https://github.com/Azure/arc-helm-demo.git --scope namespace --cluster-type connectedClusters
```

### <a name="configuration-parameters"></a>Configuratie parameters

[Meer informatie over aanvullende para meters die u kunt gebruiken](./use-gitops-connected-cluster.md#additional-parameters)om het maken van de configuratie aan te passen.

## <a name="validate-the-configuration"></a>De configuratie valideren

Controleer of de is gemaakt met behulp van de Azure CLI `sourceControlConfiguration` .

```console
az k8sconfiguration show --name azure-arc-sample --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

De `sourceControlConfiguration` resource wordt bijgewerkt met de nalevings status, berichten en informatie over fout opsporing.

**Uitvoer**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2019-12-05T05:34:41.481000",
    "message": "{\"OperatorMessage\":null,\"ClusterState\":null}",
    "messageLevel": "3"
  },
  "enableHelmOperator": "True",
  "helmOperatorProperties": {
    "chartValues": "--set helm.versions=v3",
    "chartVersion": "0.6.0"
  },
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/azure-arc-sample",
  "name": "azure-arc-sample",
  "operatorInstanceName": "flux",
  "operatorNamespace": "arc-k8s-demo",
  "operatorParams": "--git-readonly --git-path=releases",
  "operatorScope": "namespace",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "",
  "repositoryUrl": "https://github.com/Azure/arc-helm-demo.git",
  "resourceGroup": "AzureArcTest",
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

## <a name="validate-application"></a>Toepassing valideren

Voer de volgende opdracht uit en navigeer naar `localhost:8080` in uw browser om te controleren of de toepassing wordt uitgevoerd.

```console
kubectl port-forward -n arc-k8s-demo svc/arc-k8s-demo 8080:8080
```

## <a name="next-steps"></a>Volgende stappen

- [Azure Policy gebruiken om de cluster configuratie te bepalen](./use-azure-policy.md)
