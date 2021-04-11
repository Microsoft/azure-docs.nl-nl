---
title: De container Insights-agent beheren | Microsoft Docs
description: In dit artikel wordt beschreven hoe u de meest voorkomende onderhouds taken beheert met de container Log Analytics agent die wordt gebruikt door container Insights.
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: 6b485f4d49f0dd80f712d96779098c26be3f6de1
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106442572"
---
# <a name="how-to-manage-the-container-insights-agent"></a>De container Insights-agent beheren

Container Insights maakt gebruik van een container versie van de Log Analytics-agent voor Linux. Na de eerste implementatie zijn er routine-of optionele taken die u tijdens de levens cyclus moet uitvoeren. In dit artikel wordt beschreven hoe u de agent hand matig bijwerkt en verzameling van omgevings variabelen uit een bepaalde container kunt uitschakelen. 

## <a name="how-to-upgrade-the-container-insights-agent"></a>De container Insights-agent bijwerken

Container Insights maakt gebruik van een container versie van de Log Analytics-agent voor Linux. Wanneer een nieuwe versie van de agent wordt uitgebracht, wordt de agent automatisch bijgewerkt op uw beheerde Kubernetes-clusters die worden gehost op Azure Kubernetes service (AKS) en Azure Red Hat open Shift versie 3. x. Voor een [hybride Kubernetes-cluster](container-insights-hybrid-setup.md) en Azure Red Hat open Shift versie 4. x wordt de agent niet beheerd en moet u de agent hand matig bijwerken.

Als de upgrade van de agent mislukt voor een cluster dat wordt gehost op AKS of Azure Red Hat open Shift versie 3. x, wordt in dit artikel ook het proces beschreven voor het hand matig bijwerken van de agent. Zie [Release aankondigingen](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)van de agent als u de gepubliceerde versies wilt volgen.

### <a name="upgrade-agent-on-aks-cluster"></a>Agent bijwerken op AKS-cluster

Het proces voor het upgraden van de agent op AKS-clusters bestaat uit twee rechte stappen voor door sturen. De eerste stap is het uitschakelen van de bewaking met container Insights met behulp van Azure CLI. Volg de stappen die worden beschreven in het artikel [controle uitschakelen](container-insights-optout.md?#azure-cli) . Met behulp van Azure CLI kunnen we de agent van de knoop punten in het cluster verwijderen zonder dat dit van invloed is op de oplossing en de bijbehorende gegevens die in de werk ruimte zijn opgeslagen. 

>[!NOTE]
>Terwijl u deze onderhouds activiteit uitvoert, worden de verzamelde gegevens niet door de knoop punten in het cluster doorgestuurd en worden er geen gegevens weer gegeven tussen de tijd die u de agent verwijdert en de nieuwe versie installeert. 
>

Als u de nieuwe versie van de agent wilt installeren, volgt u de stappen die worden beschreven in de [controle inschakelen met Azure cli](container-insights-enable-new-cluster.md#enable-using-azure-cli)om dit proces te volt ooien.  

Nadat u de bewaking opnieuw hebt ingeschakeld, kan het ongeveer 15 minuten duren voordat u bijgewerkte metrische gegevens voor de status voor het cluster kunt weer geven. Als u wilt controleren of de upgrade van de agent is uitgevoerd, kunt u het volgende doen:

* Voer de opdracht uit: `kubectl get pod <omsagent-pod-name> -n kube-system -o=jsonpath='{.spec.containers[0].image}'` . In de status die is geretourneerd, noteert u de waarde onder **afbeelding** voor omsagent in de sectie *containers* van de uitvoer.
* Op het tabblad **knoop punten** selecteert u het cluster knooppunt en in het deel venster **Eigenschappen** aan de rechter kant, noteer de waarde onder **Agent-installatie kopie code**.

De versie van de weer gegeven agent moet overeenkomen met de meest recente versie die wordt vermeld op de pagina [release geschiedenis](https://github.com/microsoft/docker-provider/tree/ci_feature_prod) .

### <a name="upgrade-agent-on-hybrid-kubernetes-cluster"></a>Upgrade agent op hybride Kubernetes-cluster

Voer de volgende stappen uit om de agent bij te werken op een Kubernetes-cluster dat wordt uitgevoerd op:

* Zelf beheerde Kubernetes-clusters die worden gehost op Azure met behulp van de AKS-engine.
* Zelf beheerde Kubernetes-clusters die worden gehost op Azure Stack of on-premises met behulp van AKS-engine.
* Red Hat open Shift versie 4. x.

Als de Log Analytics werk ruimte zich in commerciële Azure bevindt, voert u de volgende opdracht uit:

```console
$ helm upgrade --name myrelease-1 \
--set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
```

Als de Log Analytics-werk ruimte zich in azure China 21Vianet bevindt, voert u de volgende opdracht uit:

```console
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

Als de Log Analytics-werk ruimte zich in azure US Government bevindt, voert u de volgende opdracht uit:

```console
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

### <a name="upgrade-agent-on-azure-red-hat-openshift-v4"></a>Agent bijwerken op Azure Red Hat open Shift v4

Voer de volgende stappen uit om de agent bij te werken op een Kubernetes-cluster dat wordt uitgevoerd op Azure Red Hat open Shift versie 4. x. 

>[!NOTE]
>Azure Red Hat open Shift versie 4. x ondersteunt alleen uitvoering in de Azure commerciële Cloud.
>

```console
curl -o upgrade-monitoring.sh -L https://aka.ms/upgrade-monitoring-bash-script
export azureAroV4ClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>"
bash upgrade-monitoring.sh --resource-id $ azureAroV4ClusterResourceId
```

## <a name="how-to-disable-environment-variable-collection-on-a-container"></a>De verzameling van omgevings variabelen op een container uitschakelen

Container Insights verzamelt omgevings variabelen uit de containers die worden uitgevoerd in een pod en geeft deze weer in het eigenschappen venster van de geselecteerde container in de **containers** weergave. U kunt dit gedrag beheren door het verzamelen van een specifieke container tijdens de implementatie van het Kubernetes-cluster uit te scha kelen of door de omgevings variabele *AZMON_COLLECT_ENV* in te stellen. Deze functie is beschikbaar via de agent versie – ciprod11292018 en hoger.  

Als u het verzamelen van omgevings variabelen voor een nieuwe of bestaande container wilt uitschakelen, stelt u de variabele **AZMON_COLLECT_ENV** met de waarde **False** in het yaml-configuratie bestand van de Kubernetes-implementatie. 

```yaml
- name: AZMON_COLLECT_ENV  
  value: "False"  
```

Voer de volgende opdracht uit om de wijziging toe te passen op andere Kubernetes-clusters dan Azure Red Hat open Shift): `kubectl apply -f  <path to yaml file>` . Voer de volgende opdracht uit om ConfigMap te bewerken en deze wijziging toe te passen voor Azure Red Hat open Shift-clusters:

```bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

Hiermee opent u de standaard tekst editor. Sla het bestand op in de editor nadat u de variabele hebt ingesteld.

Als u wilt controleren of de configuratie wijziging is doorgevoerd, selecteert u een container in de weer gave **containers** in container Insights en vouwt u **omgevings variabelen** uit in het deel venster Eigenschappen.  In de sectie wordt alleen de variabele weer gegeven die eerder is gemaakt- **AZMON_COLLECT_ENV = False**. Voor alle andere containers moet u in de sectie omgevings variabelen alle gedetecteerde omgevings variabelen weer geven.

Als u de detectie van de omgevings variabelen opnieuw wilt inschakelen, past u hetzelfde proces eerder toe en wijzigt u de waarde van **False** in **True** en voert u de opdracht opnieuw uit `kubectl` om de container bij te werken.  

```yaml
- name: AZMON_COLLECT_ENV  
  value: "True"  
```  

## <a name="next-steps"></a>Volgende stappen

Als u problemen ondervindt tijdens het bijwerken van de agent, raadpleegt u de [hand leiding](container-insights-troubleshoot.md) voor het oplossen van problemen voor ondersteuning.
