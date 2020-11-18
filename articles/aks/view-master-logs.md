---
title: AKS-controller Logboeken (Azure Kubernetes service) weer geven
description: Meer informatie over het inschakelen en weer geven van de logboeken voor het hoofd knooppunt Kubernetes in azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 10/14/2020
ms.openlocfilehash: a0e58174c38ec19d42f524b9bc94247e05296467
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94682227"
---
# <a name="enable-and-review-kubernetes-master-node-logs-in-azure-kubernetes-service-aks"></a>Logboeken van Kubernetes-hoofdknooppunten inschakelen en controleren in AKS (Azure Kubernetes Service)

Met Azure Kubernetes service (AKS) worden de hoofd onderdelen, zoals de *uitvoeren-apiserver* en *uitvoeren-Controller-Manager* , als beheerde service verschaft. U maakt en beheert de knoop punten waarop de *kubelet* en de container runtime worden uitgevoerd en implementeert uw toepassingen via de beheerde Kubernetes API-server. Voor het oplossen van problemen met uw toepassing en services, moet u mogelijk de logboeken weer geven die door deze hoofd onderdelen zijn gegenereerd. In dit artikel wordt beschreven hoe u Azure Monitor-Logboeken kunt gebruiken om de logboeken van de Kubernetes-hoofd onderdelen in te scha kelen en te doorzoeken.

## <a name="before-you-begin"></a>Voordat u begint

Voor dit artikel is een bestaand AKS-cluster vereist dat wordt uitgevoerd in uw Azure-account. Als u nog geen AKS-cluster hebt, kunt u er een maken met behulp van [Azure cli][cli-quickstart] of [Azure Portal][portal-quickstart]. Azure Monitor logboeken werken met AKS-clusters met zowel Kubernetes RBAC, Azure RBAC en niet-RBAC-ondersteuning.

## <a name="enable-resource-logs"></a>Resourcelogboeken inschakelen

Azure Monitor-logboeken bieden een query taal en analyse-engine die inzicht biedt in uw omgeving om gegevens uit meerdere bronnen te verzamelen en te controleren. Een werk ruimte wordt gebruikt om de gegevens te sorteren en te analyseren en kan worden geïntegreerd met andere Azure-Services, zoals Application Insights en Security Center. Als u een ander platform wilt gebruiken om de logboeken te analyseren, kunt u in plaats daarvan een resource logboek verzenden naar een Azure-opslag account of Event Hub. Zie [Wat is Azure monitor-logboeken?][log-analytics-overview]voor meer informatie.

Azure Monitor-logboeken worden in de Azure Portal ingeschakeld en beheerd. Als u logboek verzameling voor de Kubernetes-hoofd onderdelen in uw AKS-cluster wilt inschakelen, opent u de Azure Portal in een webbrowser en voert u de volgende stappen uit:

1. Selecteer de resource groep voor uw AKS-cluster, zoals *myResourceGroup*. Selecteer niet de resource groep die uw afzonderlijke AKS-cluster resources bevat, zoals *MC_myResourceGroup_myAKSCluster_eastus*.
1. Klik aan de linkerkant op **Diagnostische instellingen**.
1. Selecteer uw AKS-cluster, zoals *myAKSCluster*, en kies vervolgens **Diagnostische instelling toevoegen**.
1. Voer een naam in, zoals *myAKSClusterLogs*, en selecteer vervolgens de optie om naar **log Analytics te verzenden**.
1. Selecteer een bestaande werk ruimte of maak een nieuwe. Als u een werk ruimte maakt, moet u een werkruimte naam, een resource groep en een locatie opgeven.
1. Selecteer in de lijst met beschik bare Logboeken de logboeken die u wilt inschakelen. Voor dit voor beeld schakelt u de logboeken *uitvoeren-audit* en *uitvoeren-audit-admin* in. Veelvoorkomende logboeken zijn de *uitvoeren-apiserver*, *uitvoeren-Controller-Manager* en *uitvoeren scheduler*. U kunt de verzamelde logboeken retour neren en wijzigen als Log Analytics werk ruimten zijn ingeschakeld.
1. Wanneer u klaar bent, selecteert u **Opslaan** om het verzamelen van de geselecteerde Logboeken in te scha kelen.

## <a name="log-categories"></a>Logboek Categorieën

Naast de vermeldingen die door Kubernetes zijn geschreven, bevatten de audit logboeken van uw project ook vermeldingen van AKS.

Audit logboeken worden vastgelegd in drie categorieën: *uitvoeren-audit*, *uitvoeren-audit-admin* en *Guard*.

- De categorie *uitvoeren* bevat alle controle logboek gegevens voor elke controle gebeurtenis, zoals *Get*, *List*, *Create*, *Update*, *Delete*, *patch* en *post*.
- De categorie *uitvoeren-audit-admin* is een subset van de *uitvoeren-audit* logboek categorie. *uitvoeren-audit-admin* vermindert het aantal logboeken aanzienlijk door het uitsluiten van de controle gebeurtenissen *ophalen* en *weer geven* uit het logboek.
- De categorie *Guard* beheert Azure AD en Azure RBAC-controles. Voor beheerde Azure AD: token in, gegevens van de gebruiker. Voor Azure RBAC: toegangs beoordelingen in en uit.

## <a name="schedule-a-test-pod-on-the-aks-cluster"></a>Een test pod plannen op het AKS-cluster

Als u een aantal logboeken wilt genereren, maakt u een nieuwe pod in uw AKS-cluster. Het volgende voor beeld van een YAML-manifest kan worden gebruikt voor het maken van een Basic NGINX-exemplaar. Maak een bestand met `nginx.yaml` de naam in een editor van uw keuze en plak de volgende inhoud:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: mypod
    image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    ports:
    - containerPort: 80
```

Maak de Pod met de opdracht [kubectl Create][kubectl-create] en geef uw yaml-bestand op, zoals wordt weer gegeven in het volgende voor beeld:

```
$ kubectl create -f nginx.yaml

pod/nginx created
```

## <a name="view-collected-logs"></a>Verzamelde logboeken weer geven

Het kan tot tien minuten duren voordat de diagnostische logboeken zijn ingeschakeld en worden weer gegeven.

> [!NOTE]
> Als u alle controle logboek gegevens voor naleving of andere doel einden nodig hebt, verzamelt en slaat u deze op in goedkope opslag, zoals Blob Storage. Gebruik de *uitvoeren-audit-admin-* logboek categorie voor het verzamelen en opslaan van een zinvolle set audit logboek gegevens voor controle-en waarschuwings doeleinden.

Ga in het Azure Portal naar uw AKS-cluster en selecteer **Logboeken** aan de linkerkant. Sluit het venster *voorbeeld query's* als dit wordt weer gegeven.

Kies **Logboeken** aan de linkerkant. Als u de *uitvoeren-audit* logboeken wilt weer geven, voert u de volgende query in het tekstvak in:

```
AzureDiagnostics
| where Category == "kube-audit"
| project log_s
```

Veel logboeken worden waarschijnlijk geretourneerd. Als u de query wilt verkleinen om de logboeken te bekijken over de NGINX pod die u in de vorige stap hebt gemaakt, voegt u een extra *where* -instructie toe om naar *NGINX* te zoeken, zoals wordt weer gegeven in de volgende voorbeeld query:

```
AzureDiagnostics
| where Category == "kube-audit"
| where log_s contains "nginx"
| project log_s
```

Voer de volgende query in het tekstvak in om de *uitvoeren-audit-admin-* logboeken weer te geven:

```
AzureDiagnostics
| where Category == "kube-audit-admin"
| project log_s
```

In dit voor beeld toont de query alle Create-taken in *uitvoeren-audit-admin*. Er zijn waarschijnlijk veel resultaten geretourneerd. Als u de query wilt afsluiten om de logboeken te bekijken over de NGINX pod die in de vorige stap zijn gemaakt, voegt u een extra *where* -instructie toe om naar *NGINX* te zoeken, zoals wordt weer gegeven in de volgende voorbeeld query.

```
AzureDiagnostics
| where Category == "kube-audit-admin"
| where log_s contains "nginx"
| project log_s
```


Zie [verzamelde gegevens weer geven of analyseren met][analyze-log-analytics]logboek registratie van log Analytics voor meer informatie over het opvragen en filteren van uw logboek gegevens.

## <a name="log-event-schema"></a>Logboek gebeurtenis schema

AKS registreert de volgende gebeurtenissen:

* [AzureActivity][log-schema-azureactivity]
* [AzureDiagnostics][log-schema-azurediagnostics]
* [AzureMetrics][log-schema-azuremetrics]
* [ContainerImageInventory][log-schema-containerimageinventory]
* [ContainerInventory][log-schema-containerinventory]
* [ContainerLog][log-schema-containerlog]
* [ContainerNodeInventory][log-schema-containernodeinventory]
* [ContainerServiceLog][log-schema-containerservicelog]
* [Hartslag][log-schema-heartbeat]
* [InsightsMetrics][log-schema-insightsmetrics]
* [KubeEvents][log-schema-kubeevents]
* [KubeHealth][log-schema-kubehealth]
* [KubeMonAgentEvents][log-schema-kubemonagentevents]
* [KubeNodeInventory][log-schema-kubenodeinventory]
* [KubePodInventory][log-schema-kubepodinventory]
* [KubeServices][log-schema-kubeservices]
* [Prestatie][log-schema-perf]

## <a name="log-roles"></a>Logboek rollen

| Rol                     | Beschrijving |
|--------------------------|-------------|
| *aksService*             | De weergave naam in het controle logboek voor de bewerking van het besturings vlak (van de hcpService) |
| *masterclient*           | De weergave naam in het controle logboek voor MasterClientCertificate, het certificaat dat u krijgt van AZ AKS Get-credentials |
| *nodeclient*             | De weergave naam voor ClientCertificate, die wordt gebruikt door agent knooppunten |

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u de logboeken voor de Kubernetes-hoofd onderdelen in uw AKS-cluster kunt inschakelen en bekijken. Als u verder wilt controleren en problemen wilt oplossen, kunt u ook [de Kubelet-logboeken weer geven][kubelet-logs] en [toegang tot SSH-knoop punt inschakelen][aks-ssh].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create

<!-- LINKS - internal -->
[cli-quickstart]: kubernetes-walkthrough.md
[portal-quickstart]: kubernetes-walkthrough-portal.md
[log-analytics-overview]: ../azure-monitor/log-query/log-query-overview.md
[analyze-log-analytics]: ../azure-monitor/log-query/get-started-portal.md
[kubelet-logs]: kubelet-logs.md
[aks-ssh]: ssh.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[log-schema-azureactivity]: /azure/azure-monitor/reference/tables/azureactivity
[log-schema-azurediagnostics]: /azure/azure-monitor/reference/tables/azurediagnostics
[log-schema-azuremetrics]: /azure/azure-monitor/reference/tables/azuremetrics
[log-schema-containerimageinventory]: /azure/azure-monitor/reference/tables/containerimageinventory
[log-schema-containerinventory]: /azure/azure-monitor/reference/tables/containerinventory
[log-schema-containerlog]: /azure/azure-monitor/reference/tables/containerlog
[log-schema-containernodeinventory]: /azure/azure-monitor/reference/tables/containernodeinventory
[log-schema-containerservicelog]: /azure/azure-monitor/reference/tables/containerservicelog
[log-schema-heartbeat]: /azure/azure-monitor/reference/tables/heartbeat
[log-schema-insightsmetrics]: /azure/azure-monitor/reference/tables/insightsmetrics
[log-schema-kubeevents]: /azure/azure-monitor/reference/tables/kubeevents
[log-schema-kubehealth]: /azure/azure-monitor/reference/tables/kubehealth
[log-schema-kubemonagentevents]: /azure/azure-monitor/reference/tables/kubemonagentevents
[log-schema-kubenodeinventory]: /azure/azure-monitor/reference/tables/kubenodeinventory
[log-schema-kubepodinventory]: /azure/azure-monitor/reference/tables/kubepodinventory
[log-schema-kubeservices]: /azure/azure-monitor/reference/tables/kubeservices
[log-schema-perf]: /azure/azure-monitor/reference/tables/perf
