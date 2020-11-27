---
title: Veelvoorkomende problemen met Azure Arc enabled Kubernetes oplossen (preview-versie)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Veelvoorkomende problemen met Kubernetes-clusters met Arc-functionaliteit oplossen.
keywords: Kubernetes, Arc, Azure, containers
ms.openlocfilehash: 2e05701b92d2d96074a07e0e61ce65145a1d9dfd
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/27/2020
ms.locfileid: "96302404"
---
# <a name="azure-arc-enabled-kubernetes-troubleshooting-preview"></a>Probleem oplossing voor Azure Arc ingeschakeld Kubernetes (preview)

Dit document bevat een aantal veelvoorkomende scenario's voor probleem oplossing met connectiviteit, machtigingen en agents.

## <a name="general-troubleshooting"></a>Algemene probleem oplossing

### <a name="azure-cli-set-up"></a>Azure CLI instellen
Voordat u AZ connectedk8s of AZ k8sconfiguration CLI-opdrachten gebruikt, moet u ervoor zorgen dat AZ is ingesteld om te werken aan het juiste Azure-abonnement.

```console
az account set --subscription 'subscriptionId'
az account show
```

### <a name="azure-arc-agents"></a>Azure-Arc-agents
Alle agents voor Azure Arc enabled Kubernetes worden in de naam ruimte als een Peul geïmplementeerd `azure-arc` . Onder normale bewerkingen moeten alle peulen worden uitgevoerd en moeten hun status controles worden door gegeven.

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

Als de helm-release niet wordt gevonden of ontbreekt, probeert u het cluster opnieuw voor te bereiden.

Als de helm-release aanwezig is en `STATUS: deployed` de status van de agents bepaalt met `kubectl` :

```console
$ kubectl -n azure-arc get deployments,pods
NAME                                        READY   UP-TO-DATE AVAILABLE AGE
deployment.apps/cluster-metadata-operator   1/1     1           1        16h
deployment.apps/clusteridentityoperator     1/1     1           1        16h
deployment.apps/config-agent                1/1     1           1        16h
deployment.apps/controller-manager          1/1     1           1        16h
deployment.apps/flux-logs-agent             1/1     1           1        16h
deployment.apps/metrics-agent               1/1     1           1        16h
deployment.apps/resource-sync-agent         1/1     1           1        16h

NAME                                            READY   STATUS   RESTART AGE
pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
```

Alle peulen moeten `STATUS` worden weer gegeven als `Running` en `READY` moeten ofwel `3/3` of zijn `2/2` . Logboeken ophalen en de peulen beschrijven die worden geretourneerd `Error` of `CrashLoopBackOff` . Als een van deze peulen in `Pending` staat is, kan dit worden veroorzaakt door onvoldoende bronnen op cluster knooppunten. Als u [het cluster omhoog schaalt](https://kubernetes.io/docs/tasks/administer-cluster/) , krijgen deze peulen de `Running` status.

## <a name="connecting-kubernetes-clusters-to-azure-arc"></a>Kubernetes-clusters verbinden met Azure Arc

Voor het verbinden van clusters met Azure is toegang vereist tot zowel een Azure-abonnement als `cluster-admin` toegang tot een doel cluster. Als het cluster niet kan worden bereikt of als de onboarding van onvoldoende machtigingen kan worden uitgevoerd.

### <a name="insufficient-cluster-permissions"></a>Onvoldoende cluster machtigingen

Als het meegeleverde kubeconfig-bestand niet voldoende machtigingen heeft om de Azure Arc-agents te installeren, retourneert de Azure CLI-opdracht een fout bij het aanroepen van de Kubernetes-API.

```console
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...

Error: list: failed to list: secrets is forbidden: User "myuser" cannot list resource "secrets" in API group "" at the cluster scope
```

De cluster eigenaar moet een Kubernetes-gebruiker met de machtigingen voor cluster beheer gebruiken.

### <a name="installation-timeouts"></a>Installatie-time-outs

Voor de installatie van de Azure Arc-agent moet een set containers op het doel cluster worden uitgevoerd. Als het cluster wordt uitgevoerd via een trage Internet verbinding, kan het verzamelen van de container installatie kopie langer duren dan de Azure CLI-time-outs.

```console
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...
```

### <a name="helm-issue"></a>Helm probleem

Helm- `v3.3.0-rc.1` versie heeft een [probleem](https://github.com/helm/helm/pull/8527) waarbij helm install/upgrade (die wordt gebruikt onder de schermen van de connectedk8s cli-extensie) resulteert in het uitvoeren van alle hooks die leiden tot de volgende fout:

```console
$ az connectedk8s connect -n shasbakstest -g shasbakstest
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Ensure that you have the latest helm version installed before proceeding.
This operation might take a while...

Please check if the azure-arc namespace was deployed and run 'kubectl get pods -n azure-arc' to check if all the pods are in running state. A possible cause for pods stuck in pending state could be insufficientresources on the kubernetes cluster to onboard to arc.
ValidationError: Unable to install helm release: Error: customresourcedefinitions.apiextensions.k8s.io "connectedclusters.arc.azure.com" not found
```

Voer de volgende stappen uit om dit probleem op te lossen:

1. Verwijder de Kubernetes-bron van Azure Arc enabled in het Azure Portal.
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
Voor hulp bij het oplossen van problemen met de configuratie van broncode beheer voert u AZ-opdrachten uit met de schakel optie--debug.

```console
az provider show -n Microsoft.KubernetesConfiguration --debug
az k8sconfiguration create <parameters> --debug
```

### <a name="create-source-control-configuration"></a>Broncode beheer configuratie maken
De rol Inzender voor de resource micro soft. Kubernetes/connectedCluster is nood zakelijk en voldoende voor het maken van een resource van micro soft. KubernetesConfiguration/sourceControlConfiguration.

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