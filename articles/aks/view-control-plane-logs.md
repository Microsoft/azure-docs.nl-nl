---
title: AKS Azure Kubernetes Service controllerlogboeken (AKS) weergeven
description: Meer informatie over het inschakelen en weergeven van de logboeken voor het Kubernetes-besturingsvlak in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 01/27/2020
ms.openlocfilehash: 395422ca97b43488a7d7ad1c7434b20ccc59f2b0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767287"
---
# <a name="enable-and-review-kubernetes-control-plane-logs-in-azure-kubernetes-service-aks"></a>Logboeken van het Kubernetes-besturingsvlak inschakelen en controleren in Azure Kubernetes Service (AKS)

Met Azure Kubernetes Service (AKS) worden de onderdelen van het besturingsvlak, zoals de *kube-apiserver* en *kube-controller-manager,* geleverd als een beheerde service. U maakt en beheert de knooppunten die de *kubelet-* en containerruntime uitvoeren en implementeert uw toepassingen via de beheerde Kubernetes API-server. Als u problemen met uw toepassing en services wilt oplossen, moet u mogelijk de logboeken weergeven die zijn gegenereerd door deze onderdelen van het besturingsvlak. In dit artikel wordt beschreven hoe u Azure Monitor logboeken kunt gebruiken om de logboeken van de onderdelen van het Kubernetes-besturingsvlak in teschakelen en er query's op uit te voeren.

## <a name="before-you-begin"></a>Voordat u begint

Voor dit artikel is een bestaand AKS-cluster vereist dat wordt uitgevoerd in uw Azure-account. Als u nog geen AKS-cluster hebt, maakt u er een met behulp van [de Azure CLI][cli-quickstart] of [Azure Portal.][portal-quickstart] Azure Monitor-logboeken werken met zowel Kubernetes RBAC, Azure RBAC als AKS-clusters zonder RBAC-ondersteuning.

## <a name="enable-resource-logs"></a>Resourcelogboeken inschakelen

Om gegevens uit meerdere bronnen te verzamelen en te controleren, Azure Monitor logboeken een querytaal en analyse-engine die inzicht bieden in uw omgeving. Een werkruimte wordt gebruikt voor het sorteren en analyseren van de gegevens en kan worden geïntegreerd met andere Azure-services, zoals Application Insights en Security Center. Als u een ander platform wilt gebruiken om de logboeken te analyseren, kunt u in plaats daarvan resourcelogboeken verzenden naar een Azure-opslagaccount of Event Hub. Zie Wat zijn Azure Monitor [logboeken? voor meer informatie.][log-analytics-overview]

Azure Monitor logboeken worden ingeschakeld en beheerd in de Azure Portal. Als u logboekverzameling wilt inschakelen voor de onderdelen van het Kubernetes-besturingsvlak in uw AKS-cluster, opent u de Azure Portal in een webbrowser en voltooit u de volgende stappen:

1. Selecteer de resourcegroep voor uw AKS-cluster, zoals *myResourceGroup.* Selecteer niet de resourcegroep die uw afzonderlijke AKS-clusterresources bevat, *zoals MC_myResourceGroup_myAKSCluster_eastus*.

2. Kies aan de linkerkant **Diagnostische instellingen.**

3. Selecteer uw AKS-cluster, zoals *myAKSCluster,* en kies vervolgens **Diagnostische instelling toevoegen.**
  :::image type="content" source="media\view-control-plane-logs\select-add-diagnostic-setting.PNG" alt-text="Schermopname van Azure Portal in een browservenster met diagnostische instellingen, waarmee wordt aangegeven dat Diagnostische instelling toevoegen moet zijn geselecteerd":::

4. Voer een naam in, zoals *myAKSClusterLogs,* en selecteer vervolgens de optie **Verzenden naar Log Analytics-werkruimte.**

5. Selecteer een bestaande werkruimte of maak een nieuwe. Als u een werkruimte maakt, geeft u een werkruimtenaam, een resourcegroep en een locatie op.

6. Selecteer in de lijst met beschikbare logboeken de logboeken die u wilt inschakelen. Schakel voor dit voorbeeld de *logboeken kube-audit en* *kube-audit-admin in.* Algemene logboeken zijn onder andere *kube-apiserver,* *kube-controller-manager* en *kube-scheduler*. U kunt de verzamelde logboeken retourneren en wijzigen zodra Log Analytics-werkruimten zijn ingeschakeld.

7. Wanneer u klaar bent, **selecteert u Opslaan** om het verzamelen van de geselecteerde logboeken in te stellen.
  :::image type="content" source="media\view-control-plane-logs\settings-selected.PNG" alt-text="Schermopname van Azure Portal het scherm Diagnostische instelling toevoegen van het scherm. De bestemming 'Verzenden naar Log Analytics-werkruimte' en logboeken 'kube-audit' en 'kube-audit-admin' zijn geselecteerd":::

## <a name="log-categories"></a>Logboekcategorieën

Naast vermeldingen die zijn geschreven door Kubernetes, bevatten de auditlogboeken van uw project ook vermeldingen uit AKS.

Auditlogboeken worden vastgelegd in drie categorieën: *kube-audit,* *kube-audit-admin* en *guard.*

- De *categorie kube-audit* bevat alle auditlogboekgegevens voor elke controlegebeurtenis, waaronder *get*, *list*, *create*, *update,* *delete,* *patch* en *post.*
- De *categorie kube-audit-admin* is een subset van de categorie *kube-audit-logboek.* *Kube-audit-admin vermindert* het aantal logboeken aanzienlijk door de *get-* en list-controlegebeurtenissen uit het logboek uit te sluiten. 
- De *categorie Guard* wordt beheerd door Azure AD en Azure RBAC-controles. Voor beheerde Azure AD: token in, gebruikersgegevens uit. Voor Azure RBAC: toegangsbeoordelingen in en uit.

## <a name="schedule-a-test-pod-on-the-aks-cluster"></a>Een testpod plannen in het AKS-cluster

Als u logboeken wilt genereren, maakt u een nieuwe pod in uw AKS-cluster. Het volgende voorbeeld van een YAML-manifest kan worden gebruikt om een eenvoudige NGINX-instantie te maken. Maak een bestand met de `nginx.yaml` naam in een editor naar keuze en plak de volgende inhoud:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  nodeSelector:
    "beta.kubernetes.io/os": linux
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

Maak de pod met de [opdracht kubectl create][kubectl-create] en geef uw YAML-bestand op, zoals wordt weergegeven in het volgende voorbeeld:

```
$ kubectl create -f nginx.yaml

pod/nginx created
```

## <a name="view-collected-logs"></a>Verzamelde logboeken weergeven

Het kan tot 10 minuten duren voordat de diagnostische logboeken zijn ingeschakeld en worden weergegeven.

> [!NOTE]
> Als u alle auditlogboekgegevens voor naleving of andere doeleinden nodig hebt, verzamelt en opgeslagen in goedkope opslag, zoals blobopslag. Gebruik de *logboekcategorie kube-audit-admin* om een zinvolle set auditlogboekgegevens te verzamelen en op te slaan voor bewakings- en waarschuwingsdoeleinden.

Navigeer Azure Portal het AKS-cluster en selecteer **Logboeken** aan de linkerkant. Sluit het *venster Voorbeeldquery's* als dit wordt weergegeven.

Kies aan de linkerkant **Logboeken.** Als u de *logboeken van kube-audit wilt* weergeven, voert u de volgende query in het tekstvak in:

```
AzureDiagnostics
| where Category == "kube-audit"
| project log_s
```

Veel logboeken worden waarschijnlijk geretourneerd. Als u het bereik van de query wilt wijzigen om de logboeken weer te geven over de NGINX-pod die u in de vorige stap hebt gemaakt, voegt u een extra *where-instructie* toe om te zoeken naar *nginx,* zoals wordt weergegeven in de volgende voorbeeldquery:

```
AzureDiagnostics
| where Category == "kube-audit"
| where log_s contains "nginx"
| project log_s
```

Als u de *logboeken kube-audit-admin wilt* weergeven, voert u de volgende query in het tekstvak in:

```
AzureDiagnostics
| where Category == "kube-audit-admin"
| project log_s
```

In dit voorbeeld toont de query alle maaktaken in *kube-audit-admin.* Er worden waarschijnlijk veel resultaten geretourneerd om het bereik van de query te wijzigen om de logboeken weer te geven over de NGINX-pod die in de vorige stap is gemaakt. Voeg een extra *where-instructie* toe om te zoeken naar *nginx,* zoals wordt weergegeven in de volgende voorbeeldquery.

```
AzureDiagnostics
| where Category == "kube-audit-admin"
| where log_s contains "nginx"
| project log_s
```


Zie View or analyze data collected with log analytics log search (Gegevens weergeven of analyseren die zijn verzameld met zoeken in logboeken in Log Analytics) voor meer informatie over het doorzoeken en filteren van [uw logboekgegevens.][analyze-log-analytics]

## <a name="log-event-schema"></a>Logboekgebeurtenisschema

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
* [Perf][log-schema-perf]

## <a name="log-roles"></a>Logboekrollen

| Rol                     | Beschrijving |
|--------------------------|-------------|
| *aksService*             | De weergavenaam in het auditlogboek voor de besturingsvlakbewerking (van de hcpService) |
| *masterclient*           | De weergavenaam in het auditlogboek voor MasterClientCertificate, het certificaat dat u krijgt van az aks get-credentials |
| *nodeclient*             | De weergavenaam voor ClientCertificate, die wordt gebruikt door agentknooppunten |

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u de logboeken voor de onderdelen van het Kubernetes-besturingsvlak in uw AKS-cluster kunt inschakelen en controleren. Als u verder wilt controleren en problemen wilt oplossen, kunt u ook [de Kubelet-logboeken bekijken][kubelet-logs] en [SSH-knooppunttoegang inschakelen.][aks-ssh]

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create

<!-- LINKS - internal -->
[cli-quickstart]: kubernetes-walkthrough.md
[portal-quickstart]: kubernetes-walkthrough-portal.md
[log-analytics-overview]: ../azure-monitor/logs/log-query-overview.md
[analyze-log-analytics]: ../azure-monitor/logs/log-analytics-tutorial.md
[kubelet-logs]: kubelet-logs.md
[aks-ssh]: ssh.md
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
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