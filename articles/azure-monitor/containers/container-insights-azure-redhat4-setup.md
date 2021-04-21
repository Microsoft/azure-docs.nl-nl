---
title: Configureer Azure Red Hat OpenShift v4.x met Container Insights | Microsoft Docs
description: In dit artikel wordt beschreven hoe u bewaking configureert voor een Kubernetes-cluster met Azure Monitor die wordt gehost op Azure Red Hat OpenShift versie 4 of hoger.
ms.topic: conceptual
ms.date: 03/05/2021
ms.openlocfilehash: 11c702d1f46725a12e90a01dc1b38467344a1123
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784637"
---
# <a name="configure-azure-red-hat-openshift-v4x-with-container-insights"></a>Configureer Azure Red Hat OpenShift v4.x met Container Insights

Container Insights biedt een uitgebreide bewakingservaring voor AKS Azure Kubernetes Service engineclusters (AKS) en AKS. In dit artikel wordt beschreven hoe u een vergelijkbare bewakingservaring bereikt door bewaking in te schakelen voor Kubernetes-clusters die worden gehost [op Azure Red Hat OpenShift](../../openshift/intro-openshift.md) versie 4.x.

>[!NOTE]
>Ondersteuning voor Azure Red Hat OpenShift is op dit moment een functie in openbare preview.
>

U kunt Container Insights inschakelen voor een of meer bestaande implementaties van Azure Red Hat OpenShift v4.x met behulp van de ondersteunde methoden die in dit artikel worden beschreven.

Voer voor een bestaand cluster dit [Bash-script uit in de Azure CLI.](/cli/azure/openshift#az_openshift_create&preserve-view=true)

## <a name="supported-and-unsupported-features"></a>Ondersteunde en niet-ondersteunde functies

Container Insights biedt ondersteuning voor Azure Red Hat OpenShift v4.x, zoals beschreven in Overzicht van [Container Insights,](container-insights-overview.md)met uitzondering van de volgende functies:

- Live data (preview)
- [Metrische gegevens verzamelen](container-insights-update-metrics.md) van clusterknooppunten en -pods en deze opslaan in Azure Monitor database met metrische gegevens

## <a name="prerequisites"></a>Vereisten

- De Azure CLI versie 2.0.72 of hoger  

- Het [Helm 3](https://helm.sh/docs/intro/install/) CLI-hulpprogramma

- Nieuwste versie van [OpenShift CLI](https://docs.openshift.com/container-platform/4.7/cli_reference/openshift_cli/getting-started-cli.html)

- [Bash-versie 4](https://www.gnu.org/software/bash/)

- Het [Kubectl-opdrachtregelprogramma](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

- Een [Log Analytics-werkruimte](../logs/design-logs-deployment.md).

    Container Insights ondersteunt een Log Analytics-werkruimte in de regio's die worden vermeld in [Azure-producten per regio.](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor) Als u uw eigen werkruimte wilt maken, kunt u deze maken [via Azure Resource Manager](../logs/resource-manager-workspace.md), via [PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)of in [de Azure Portal](../logs/quick-create-workspace.md).

- Als u de functies in Container Insights wilt inschakelen en  openen, moet u minimaal de rol Azure-inzender hebben in het Azure-abonnement en de rol [*Log Analytics-inzender*](../logs/manage-access.md#manage-access-using-azure-permissions) in de Log Analytics-werkruimte, geconfigureerd met Container insights.

- Als u de bewakingsgegevens wilt weergeven, moet u de [*rol Van Log Analytics-lezer*](../logs/manage-access.md#manage-access-using-azure-permissions) hebben in de Log Analytics-werkruimte, geconfigureerd met Container Insights.

## <a name="enable-monitoring-for-an-existing-cluster"></a>Bewaking inschakelen voor een bestaand cluster

Als u bewaking wilt inschakelen voor Azure Red Hat OpenShift cluster van versie 4 of hoger dat in Azure is geïmplementeerd met behulp van het opgegeven Bash-script, doet u het volgende:

1. Meld u aan bij Azure door de volgende opdracht uit te voeren:

    ```azurecli
    az login
    ```

1. Download en sla in een lokale map het script op dat uw cluster configureert met de bewakings-invoegservice door de volgende opdracht uit te voeren:

    `curl -o enable-monitoring.sh -L https://aka.ms/enable-monitoring-bash-script`

1. Maak verbinding met het ARO v4-cluster met behulp van de instructies in [Zelfstudie: Verbinding maken met een Azure Red Hat OpenShift 4-cluster.](../../openshift/tutorial-connect-cluster.md)


### <a name="integrate-with-an-existing-workspace"></a>Integreren met een bestaande werkruimte

In deze sectie gaat u de bewaking van uw cluster inschakelen met behulp van het Bash-script dat u eerder hebt gedownload. Als u wilt integreren met een bestaande Log Analytics-werkruimte, begint u met het identificeren van de volledige resource-id van uw Log Analytics-werkruimte die vereist is voor de parameter. Voer vervolgens de opdracht uit om de bewakings-invoegruimte in te stellen voor de opgegeven `logAnalyticsWorkspaceResourceId` werkruimte.

Als u geen werkruimte hoeft op te geven, [](#integrate-with-the-default-workspace) kunt u naar de sectie Integreren met de standaardwerkruimte gaan en het script een nieuwe werkruimte voor u laten maken.

1. Voer de volgende opdracht uit om alle abonnementen weer te geven waar u toegang tot hebt:

    ```azurecli
    az account list --all -o table
    ```

    De uitvoer ziet er als volgt uit:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

1. Kopieer de waarde voor **SubscriptionId**.

1. Schakel over naar het abonnement dat als host voor de Log Analytics-werkruimte wordt gebruikt door de volgende opdracht uit te voeren:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

1. Geef de lijst met werkruimten in uw abonnementen weer in de standaard-JSON-indeling door de volgende opdracht uit te voeren:

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

1. Zoek in de uitvoer de naam van de werkruimte en kopieer vervolgens de volledige resource-id van die Log Analytics-werkruimte onder de **veld-id**.

1. Voer de volgende opdracht uit om bewaking in teschakelen. Vervang de waarden voor de `azureAroV4ClusterResourceId` `logAnalyticsWorkspaceResourceId` parameters en .

    ```bash
    export azureAroV4ClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>"
    export logAnalyticsWorkspaceResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>" 
    ```

    Dit is de opdracht die u moet uitvoeren nadat u de drie variabelen hebt gevuld met Export-opdrachten:

    `bash enable-monitoring.sh --resource-id $azureAroV4ClusterResourceId --workspace-id $logAnalyticsWorkspaceResourceId`

Nadat u bewaking hebt ingeschakeld, kan het ongeveer 15 minuten duren voordat u de metrische statusgegevens voor het cluster kunt bekijken.

### <a name="integrate-with-the-default-workspace"></a>Integreren met de standaardwerkruimte

In deze sectie gaat u bewaking inschakelen voor uw Azure Red Hat OpenShift v4.x-cluster met behulp van het Bash-script dat u hebt gedownload.

In dit voorbeeld bent u niet verplicht om vooraf een bestaande werkruimte te maken of op te geven. Met deze opdracht vereenvoudigt u het proces door een standaardwerkruimte te maken in de standaardresourcegroep van het clusterabonnement, als deze nog niet bestaat in de regio.

De standaardwerkruimte die wordt gemaakt, heeft de indeling *DefaultWorkspace- \<GUID> - \<Region>*.  

Vervang de waarde voor de `azureAroV4ClusterResourceId` parameter .

```bash
export azureAroV4ClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>"
```

Bijvoorbeeld:

'bash enable-monitoring.sh --resource-id $azureAroV 4ClusterResourceId 

Nadat u bewaking hebt ingeschakeld, kan het ongeveer 15 minuten duren voordat u de metrische statusgegevens voor het cluster kunt bekijken.

### <a name="enable-monitoring-from-the-azure-portal"></a>Bewaking inschakelen vanuit de Azure Portal

De weergave met meerdere clusters in Container Insights markeert uw Azure Red Hat OpenShift clusters die geen bewaking hebben ingeschakeld op het tabblad **Niet-gecontroleerd clusters.** Met **de** optie Inschakelen naast uw cluster wordt onboarding van bewaking niet gestart vanuit de portal. U wordt omgeleid naar dit artikel om bewaking handmatig in te stellen door de stappen te volgen die eerder in dit artikel zijn beschreven.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer in het linkerdeelvenster of op de startpagina **Azure Monitor.**

1. Selecteer containers **in de** sectie **Inzichten.**

1. Selecteer op **de pagina Monitor - containers** de optie **Niet-bewaakte clusters.**

1. Selecteer het cluster in de lijst met niet-bewaakte clusters en selecteer vervolgens **Inschakelen.**

    U kunt de resultaten in de lijst identificeren door te zoeken naar de **ARO-waarde** in de **kolom Clustertype.** Nadat u **Inschakelen hebt** geselecteerd, wordt u omgeleid naar dit artikel.

## <a name="next-steps"></a>Volgende stappen

- Nu u bewaking hebt ingeschakeld voor het verzamelen van status- en resourcegebruik van uw RedHat OpenShift versie 4.x-cluster en de workloads die erop worden uitgevoerd, leert u hoe [u](container-insights-analyze.md) Container Insights gebruikt.

- De containeragent verzamelt standaard de containerlogboeken *stdout* en *stderr* van alle containers die worden uitgevoerd in alle naamruimten, met uitzondering van kube-system. Als u een containerlogboekverzameling wilt configureren die specifiek is voor een bepaalde naamruimte of naamruimten, controleert u de configuratie van de [Container Insights-agent](container-insights-agent-config.md) om de instellingen voor gegevensverzameling te configureren die u wilt gebruiken voor uw *ConfigMap-configuratiebestand.*

- Als u metrische Prometheus-gegevens uit uw cluster wilt scrapen en analyseren, bekijkt u [Metrische scraping voor Prometheus configureren.](container-insights-prometheus-integration.md)

- Zie Het bewaken van uw cluster stoppen met het bewaken van uw cluster met behulp van Container Insights voor meer informatie over het stoppen van de bewaking [van Azure Red Hat OpenShift cluster.](./container-insights-optout-openshift-v3.md)
