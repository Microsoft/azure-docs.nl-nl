---
title: Veelvoorkomende problemen met Azure Arc enabled Kubernetes oplossen
services: azure-arc
ms.service: azure-arc
ms.date: 03/02/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Veelvoorkomende problemen met Kubernetes-clusters met Arc-functionaliteit oplossen.
keywords: Kubernetes, Arc, Azure, containers
ms.openlocfilehash: e1f4e84f16c6b584f1ffbd918a86c251f47efcca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101653997"
---
# <a name="azure-arc-enabled-kubernetes-troubleshooting"></a>Problemen met Azure Arc enabled Kubernetes oplossen

Dit document bevat richt lijnen voor probleem oplossing voor problemen met connectiviteit, machtigingen en agents.

## <a name="general-troubleshooting"></a>Algemene probleem oplossing

### <a name="azure-cli"></a>Azure CLI

Controleer voordat `az connectedk8s` u `az k8s-configuration` de-of cli-opdrachten gebruikt of Azure cli is ingesteld om te werken aan het juiste Azure-abonnement.

```azurecli
az account set --subscription 'subscriptionId'
az account show
```

### <a name="azure-arc-agents"></a>Azure Arc-agents

Alle agents voor Azure Arc enabled Kubernetes worden in de naam ruimte als een Peul geïmplementeerd `azure-arc` . Alle peulen moeten worden uitgevoerd en hun status controles worden door gegeven.

Controleer eerst de Azure Arc helm-release:

```console
$ helm --namespace default status azure-arc
NAME: azure-arc
LAST DEPLOYED: Fri Apr  3 11:13:10 2020
NAMESPACE: default
STATUS: deployed
REVISION: 5
TEST SUITE: None
```

Als de helm-release niet wordt gevonden of ontbreekt, probeert [u het cluster opnieuw te verbinden met Azure Arc](./connect-cluster.md) .

Als de helm-release aanwezig is bij `STATUS: deployed` , controleert u de status van de agents met behulp van `kubectl` :

```console
$ kubectl -n azure-arc get deployments,pods
NAME                                       READY  UP-TO-DATE  AVAILABLE  AGE
deployment.apps/clusteridentityoperator     1/1       1          1       16h
deployment.apps/config-agent                1/1       1          1       16h
deployment.apps/cluster-metadata-operator   1/1       1          1       16h
deployment.apps/controller-manager          1/1       1          1       16h
deployment.apps/flux-logs-agent             1/1       1          1       16h
deployment.apps/metrics-agent               1/1       1          1       16h
deployment.apps/resource-sync-agent         1/1       1          1       16h

NAME                                            READY   STATUS  RESTART  AGE
pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
```

Alle peulen moeten worden weer gegeven `STATUS` als `Running` met `3/3` of `2/2` onder de `READY` kolom. Logboeken ophalen en het gehele resultaat van een or-bewerking beschrijven `Error` `CrashLoopBackOff` . Als er een wille keurig peul in `Pending` staat is, zijn er mogelijk onvoldoende resources op cluster knooppunten. Als u [de schaal van uw cluster omhoog](https://kubernetes.io/docs/tasks/administer-cluster/) laat, kan dit een overgang zijn naar de `Running` status.

## <a name="connecting-kubernetes-clusters-to-azure-arc"></a>Kubernetes-clusters verbinden met Azure Arc

Voor het verbinden van clusters met Azure is toegang tot een Azure-abonnement en `cluster-admin` toegang tot een doel cluster vereist. Als u het cluster niet kunt bereiken of als u onvoldoende machtigingen hebt, mislukt het verbinden van het cluster met Azure Arc.

### <a name="insufficient-cluster-permissions"></a>Onvoldoende cluster machtigingen

Als het meegeleverde kubeconfig-bestand niet voldoende machtigingen heeft om de Azure Arc-agents te installeren, wordt er een fout geretourneerd door de Azure CLI-opdracht.

```azurecli
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...

Error: list: failed to list: secrets is forbidden: User "myuser" cannot list resource "secrets" in API group "" at the cluster scope
```

Aan de gebruiker die verbinding maakt met het cluster met Azure Arc moet de `cluster-admin` rol aan hen zijn toegewezen in het cluster.

### <a name="installation-timeouts"></a>Installatie-time-outs

Voor het verbinden van een Kubernetes-cluster met Azure Arc enabled Kubernetes is de installatie van Azure Arc-agents op het cluster vereist. Als het cluster wordt uitgevoerd via een trage Internet verbinding, kan de container installatie kopie die voor agents wordt opgehaald langer duren dan de Azure CLI-time-outs.

```azurecli
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...
```

### <a name="helm-issue"></a>Helm probleem

Helm- `v3.3.0-rc.1` versie heeft een [probleem](https://github.com/helm/helm/pull/8527) waarbij de installatie/upgrade van helm (gebruikt door `connectedk8s` de CLI-extensie) resulteert in het uitvoeren van alle hooks waardoor de volgende fout wordt veroorzaakt:

```console
$ az connectedk8s connect -n shasbakstest -g shasbakstest
Ensure that you have the latest helm version installed before proceeding.
This operation might take a while...

Please check if the azure-arc namespace was deployed and run 'kubectl get pods -n azure-arc' to check if all the pods are in running state. A possible cause for pods stuck in pending state could be insufficientresources on the kubernetes cluster to onboard to arc.
ValidationError: Unable to install helm release: Error: customresourcedefinitions.apiextensions.k8s.io "connectedclusters.arc.azure.com" not found
```

Voer de volgende stappen uit om dit probleem op te lossen:

1. Verwijder de Azure Arc enabled Kubernetes-resource in de Azure Portal.
2. Voer de volgende opdrachten uit op de computer:
    
    ```console
    kubectl delete ns azure-arc
    kubectl delete clusterrolebinding azure-arc-operator
    kubectl delete secret sh.helm.release.v1.azure-arc.v1
    ```

3. [Installeer een stabiele versie](https://helm.sh/docs/intro/install/) van helm 3 op uw computer in plaats van de release Candi date-versie.
4. Voer de `az connectedk8s connect` opdracht uit met de juiste waarden om het cluster te verbinden met Azure Arc.

## <a name="configuration-management"></a>Configuratiebeheer

### <a name="general"></a>Algemeen
Voor hulp bij het oplossen van problemen met de configuratie bron voert u AZ-opdrachten uit met de `--debug` opgegeven para meter.

```console
az provider show -n Microsoft.KubernetesConfiguration --debug
az k8s-configuration create <parameters> --debug
```

### <a name="create-configurations"></a>Configuraties maken

Schrijf machtigingen voor de Azure Arc enabled Kubernetes-resource ( `Microsoft.Kubernetes/connectedClusters/Write` ) zijn nood zakelijk en voldoende voor het maken van configuraties op dat cluster.

### <a name="configuration-remains-pending"></a>Configuratie blijft `Pending`

```console
kubectl -n azure-arc logs -l app.kubernetes.io/component=config-agent -c config-agent
$ k -n pending get gitconfigs.clusterconfig.azure.com  -o yaml
apiVersion: v1
items:
- apiVersion: clusterconfig.azure.com/v1beta1
  kind: GitConfig
  metadata:
    creationTimestamp: "2020-04-13T20:37:25Z"
    generation: 1
    name: pending
    namespace: pending
    resourceVersion: "10088301"
    selfLink: /apis/clusterconfig.azure.com/v1beta1/namespaces/pending/gitconfigs/pending
    uid: d9452407-ff53-4c02-9b5a-51d55e62f704
  spec:
    correlationId: ""
    deleteOperator: false
    enableHelmOperator: false
    giturl: git@github.com:slack/cluster-config.git
    helmOperatorProperties: null
    operatorClientLocation: azurearcfork8s.azurecr.io/arc-preview/fluxctl:0.1.3
    operatorInstanceName: pending
    operatorParams: '"--disable-registry-scanning"'
    operatorScope: cluster
    operatorType: flux
  status:
    configAppliedTime: "2020-04-13T20:38:43.081Z"
    isSyncedWithAzure: true
    lastPolledStatusTime: ""
    message: 'Error: {exit status 1} occurred while doing the operation : {Installing
      the operator} on the config'
    operatorPropertiesHashed: ""
    publicKey: ""
    retryCountPublicKey: 0
    status: Installing the operator
kind: List
metadata:
  resourceVersion: ""
  selfLink: ""
```
## <a name="monitoring"></a>Bewaking

Voor het Azure Monitor van containers moet de Daemonset worden uitgevoerd in de geprivilegieerde modus. Voer de volgende opdracht uit om een canonieke geruimen Kubernetes-cluster voor bewaking in te stellen:

```console
juju config kubernetes-worker allow-privileged=true
```