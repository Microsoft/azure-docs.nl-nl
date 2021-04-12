---
title: Kubernetes-clusters met Azure-Arc-functionaliteit bewaken
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Metrische gegevens en logboeken van Azure Arc enabled Kubernetes-clusters verzamelen met behulp van Azure Monitor
ms.openlocfilehash: 0a983f6d7032310d02d35e713482de942bfbfd70
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443847"
---
# <a name="azure-monitor-container-insights-for-azure-arc-enabled-kubernetes-clusters"></a>Azure Monitor container Insights voor Azure Arc enabled Kubernetes-clusters

[Azure monitor container Insights](container-insights-overview.md) biedt uitgebreide bewakings ervaring voor Azure Arc enabled Kubernetes-clusters.

[!INCLUDE [preview features note](../../azure-arc/kubernetes/includes/preview/preview-callout.md)]

## <a name="supported-configurations"></a>Ondersteunde configuraties

- Azure Monitor container Insights ondersteunt het controleren van de Kubernetes (preview) van Azure Arc enabled, zoals beschreven in het [overzichts](container-insights-overview.md) artikel, met uitzonde ring van de functie Live data (preview). Gebruikers hoeven ook geen [eigenaars](../../role-based-access-control/built-in-roles.md#owner) machtigingen te hebben om [metrische gegevens in te scha kelen](container-insights-update-metrics.md)
- `Docker`, `Moby` en cri compatibele container runtimes, zoals `CRI-O` en `containerd` .
- Uitgaande proxy zonder verificatie en uitgaande proxy met basis verificatie worden ondersteund. De uitgaande proxy waarmee vertrouwde certificaten worden verwacht, wordt momenteel niet ondersteund.

## <a name="prerequisites"></a>Vereisten

- U hebt voldaan aan de vereisten die worden vermeld in de [documentatie over algemene cluster uitbreidingen](../../azure-arc/kubernetes/extensions.md#prerequisites).
- Een Log Analytics-werk ruimte: Azure Monitor container Insights ondersteunt een Log Analytics-werk ruimte in de regio's die worden vermeld op de pagina Azure- [producten per regio](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor). U kunt uw eigen werk ruimte maken via [Azure Resource Manager](../logs/resource-manager-workspace.md), [power shell](../logs/powershell-sample-create-workspace.md)of [Azure Portal](../logs/quick-create-workspace.md).
- U moet de roltoewijzing voor de rol van [Inzender](../../role-based-access-control/built-in-roles.md#contributor) hebben voor het Azure-abonnement met de Azure Arc enabled Kubernetes-resource. Als de Log Analytics-werk ruimte zich in een ander abonnement bevindt, moet u Log Analytics rol voor [Inzender](../logs/manage-access.md#manage-access-using-azure-permissions) toewijzing is vereist in de log Analytics-werk ruimte.
- Als u de bewakings gegevens wilt bekijken, moet u de toewijzing van [log Analytics-lezer](../logs/manage-access.md#manage-access-using-azure-permissions) hebben op de log Analytics-werk ruimte.
- De volgende eind punten moeten worden ingeschakeld voor uitgaande toegang, naast de waarden die worden vermeld onder [een Kubernetes-cluster verbinden met Azure Arc](../../azure-arc/kubernetes/quickstart-connect-cluster.md#meet-network-requirements).

    | Eindpunt | Poort |
    |----------|------|
    | `*.ods.opinsights.azure.com` | 443 |
    | `*.oms.opinsights.azure.com` | 443 |
    | `dc.services.visualstudio.com` | 443 |
    | `*.monitoring.azure.com` | 443 |
    | `login.microsoftonline.com` | 443 |

    Als uw Arc-Kubernetes-bron zich in de Azure-Amerikaanse overheids omgeving bevindt, moeten de volgende eind punten worden ingeschakeld voor uitgaande toegang:

    | Eindpunt | Poort |
    |----------|------|
    | `*.ods.opinsights.azure.us` | 443 |
    | `*.oms.opinsights.azure.us` | 443 |
    | `dc.services.visualstudio.com` | 443 |
    

- Als u eerder Azure Monitor container Insights op dit cluster hebt geïmplementeerd met behulp van script zonder cluster uitbreidingen, volgt u de instructies die [hier](container-insights-optout-hybrid.md) worden weer gegeven om dit helm-diagram te verwijderen. U kunt vervolgens door gaan met het maken van een cluster extensie-exemplaar voor Azure Monitor container Insights.

    >[!NOTE]
    > De op scripts gebaseerde versie van Deploy Azure Monitor container Insights (preview) wordt vervangen door de [cluster extensie](../../azure-arc/kubernetes/extensions.md) vorm van implementatie. Azure Monitor die eerder via script zijn geïmplementeerd, wordt alleen ondersteund tot en met 2021. het wordt daarom geadviseerd om op het eerste gebruik te migreren naar de cluster extensie vorm van implementatie.

### <a name="identify-workspace-resource-id"></a>Resource-ID van werk ruimte identificeren

Voer de volgende opdrachten uit om de volledige Azure Resource Manager-id van de Log Analytics-werk ruimte te vinden. 

1. Een lijst met alle abonnementen waartoe u toegang hebt met behulp van de volgende opdracht:

    ```azurecli
    az account list --all -o table
    ```

2. Schakel over naar het abonnement dat als host fungeert voor de Log Analytics-werk ruimte met behulp van de volgende opdracht:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. In het volgende voor beeld wordt de lijst met werk ruimten in uw abonnementen weer gegeven in de standaard JSON-indeling.

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Zoek in de uitvoer de naam van de werk ruimte die van belang is. Het `id` veld van dat staat voor de Azure Resource Manager id van die log Analytics werk ruimte.

    >[!TIP]
    > Dit `id` kan ook worden gevonden op de Blade *overzicht* van de log Analytics-werk ruimte via de Azure Portal.

## <a name="create-extension-instance-using-azure-cli"></a>Een extensie-exemplaar maken met behulp van Azure CLI

### <a name="option-1---with-default-values"></a>Optie 1: met standaard waarden

Deze optie maakt gebruik van de volgende standaard waarden:

- Maakt of gebruikt bestaande standaard logboek analyse-werk ruimte die overeenkomt met de regio van het cluster
- Automatische upgrade is ingeschakeld voor de Azure Monitor cluster uitbreiding

```console
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers
```

### <a name="option-2---with-existing-azure-log-analytics-workspace"></a>Optie 2: met een bestaande Azure Log Analytics-werk ruimte

U kunt een bestaande Azure Log Analytics-werk ruimte gebruiken in elk abonnement waarvoor u *Inzender* of een meer strikte roltoewijzing hebt.

```console
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers --configuration-settings logAnalyticsWorkspaceResourceID=<armResourceIdOfExistingWorkspace>
```

### <a name="option-3---with-advanced-configuration"></a>Optie 3: met geavanceerde configuratie

Als u de standaard resource aanvragen en limieten wilt aanpassen, kunt u de geavanceerde configuratie-instellingen gebruiken:

```console
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers --configuration-settings  omsagent.resources.daemonset.limits.cpu=150m omsagent.resources.daemonset.limits.memory=600Mi omsagent.resources.deployment.limits.cpu=1 omsagent.resources.deployment.limits.memory=750Mi
```

De [sectie resource aanvragen en limieten van het helm diagram](https://github.com/helm/charts/blob/master/incubator/azuremonitor-containers/values.yaml) voor de beschik bare configuratie-instellingen uitchecken.

### <a name="option-4---on-azure-stack-edge"></a>Optie 4-op Azure Stack rand

Als het Azure Arc enabled Kubernetes-cluster zich op Azure Stack Edge bevindt, moet u een aangepast koppelingspad `/home/data/docker` gebruiken.

```console
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers --configuration-settings omsagent.logsettings.custommountpath=/home/data/docker
```

>[!NOTE]
> Als u expliciet de versie opgeeft van de uitbrei ding die moet worden geïnstalleerd in de opdracht maken, moet u ervoor zorgen dat de opgegeven versie >= 2.8.2.

## <a name="create-extension-instance-using-azure-portal"></a>Een extensie-exemplaar maken met behulp van Azure Portal

>[!IMPORTANT]
>  Als u Azure Monitor implementeert op een Kubernetes-cluster dat wordt uitgevoerd boven op Azure Stack Edge, moet u de Azure CLI-optie volgen in plaats van de optie Azure Portal als een aangepast koppelingspad moet worden ingesteld voor deze clusters.    

### <a name="onboarding-from-the-azure-arc-enabled-kubernetes-resource-blade"></a>Onboarding van de Blade Kubernetes-resource van Azure-boog ingeschakeld

1. Selecteer in de Azure Portal het Kubernetes-cluster dat u wilt bewaken.

2. Selecteer het item Insights (preview) onder de sectie bewaking van de Blade resource.

3. Selecteer op de pagina voor onboarding de knop Azure Monitor configureren

4. U kunt nu de [log Analytics-werk ruimte](../logs/quick-create-workspace.md) kiezen voor het verzenden van uw metrische gegevens en het vastleggen van informatie in.

5. Selecteer de knop configureren om de Azure Monitor container Insights-cluster uitbreiding te implementeren.

### <a name="onboarding-from-azure-monitor-blade"></a>Onboarding van Azure Monitor Blade

1. In de Azure Portal gaat u naar de Blade monitor en selecteert u de optie ' containers ' in het menu ' Insights '.

2. Selecteer het tabblad niet-bewaakte clusters om de Azure Arc enabled Kubernetes-clusters weer te geven waarvoor u bewaking kunt inschakelen.

3. Klik op de koppeling ' inschakelen ' naast het cluster waarvoor u bewaking wilt inschakelen.

4. Kies de werk ruimte Log Analytics en selecteer de knop configureren om door te gaan.

## <a name="create-extension-instance-using-azure-resource-manager"></a>Een extensie-exemplaar maken met behulp van Azure Resource Manager

1. Azure Resource Manager sjabloon en-para meter downloaden:

    ```console
    curl -L https://aka.ms/arc-k8s-azmon-extension-arm-template -o arc-k8s-azmon-extension-arm-template.json
    curl -L https://aka.ms/arc-k8s-azmon-extension-arm-template-params -o  arc-k8s-azmon-extension-arm-template-params.json
    ```

2. Parameter waarden bijwerken in arc-k8s-azmon-extension-arm-template-params.jsvoor het bestand. Voor open bare Azure-Cloud `opinsights.azure.com` moet worden gebruikt als de waarde van workspaceDomain.

3. De sjabloon implementeren om Azure Monitor container Insights-extensie te maken 

    ```console
    az login
    az account set --subscription "Subscription Name"
    az deployment group create --resource-group <resource-group> --template-file ./arc-k8s-azmon-extension-arm-template.json --parameters @./arc-k8s-azmon-extension-arm-template-params.json
    ```

## <a name="delete-extension-instance"></a>Extensie-exemplaar verwijderen

Met de volgende opdracht wordt alleen het extensie-exemplaar verwijderd, maar wordt de Log Analytics-werk ruimte niet verwijderd. De gegevens in de Log Analytics resource blijven intact.

```bash
az k8s-extension delete --name azuremonitor-containers --cluster-type connectedClusters --cluster-name <cluster-name> --resource-group <resource-group>
```

## <a name="disconnected-cluster"></a>Niet-verbonden cluster
Als uw cluster gedurende > 48 uur wordt losgekoppeld van Azure, heeft Azure resource Graph geen informatie over uw cluster. Als gevolg hiervan kan de Blade Insights onjuiste informatie over uw cluster status weer geven.

## <a name="next-steps"></a>Volgende stappen

- Als bewaking is ingeschakeld voor het verzamelen van het status-en resource gebruik van uw Kubernetes-cluster met Arc-functionaliteit en workloads die hierop worden uitgevoerd, leert [u hoe u container Insights kunt gebruiken](container-insights-analyze.md) .

- De container agent verzamelt standaard de container/stderr-logboeken van alle containers die worden uitgevoerd in alle naam ruimten, met uitzonde ring van uitvoeren-System. Als u de container logboek verzameling specifiek wilt configureren voor bepaalde naam ruimten of naam ruimten, bekijkt u de configuratie van de [container Insights-agent](container-insights-agent-config.md) om de gewenste instellingen voor het verzamelen van gegevens te configureren voor uw ConfigMap-configuratie bestand.

- Als u Prometheus-metrische gegevens uit uw cluster wilt opwaarderen en analyseren, raadpleegt u [Prometheus metrische gegevens](container-insights-prometheus-integration.md) weer geven
