---
title: Configureer Azure Red Hat OpenShift v3.x met Container Insights | Microsoft Docs
description: In dit artikel wordt beschreven hoe u de bewaking van een Kubernetes-cluster configureert met Azure Monitor gehost op Azure Red Hat OpenShift versie 3 en hoger.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: a2910655601548f39983547e12460d949901954d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784673"
---
# <a name="configure-azure-red-hat-openshift-v3-with-container-insights"></a>Een Azure Red Hat OpenShift v3 configureren met Container Insights

>[!IMPORTANT]
> Azure Red Hat OpenShift 3,11 wordt in juni 2022 ingetrokken.
>
> Vanaf oktober 2020 kunt u geen nieuwe 3.11-clusters meer maken.
> Bestaande 3.11-clusters blijven werken tot juni 2022, maar worden na die datum niet meer ondersteund.
>
> Volg deze handleiding om [een cluster Azure Red Hat OpenShift 4 te maken.](../../openshift/tutorial-create-cluster.md)
> Als u specifieke vragen hebt, kunt [u contact met ons opnemen.](mailto:aro-feedback@microsoft.com)

Container Insights biedt uitgebreide bewakingservaring voor de AKS Azure Kubernetes Service engineclusters (AKS) en AKS Engine. In [dit artikel](../../openshift/intro-openshift.md) wordt beschreven hoe u bewaking kunt inschakelen van Kubernetes-clusters die worden gehost op Azure Red Hat OpenShift versie 3 en de meest recente ondersteunde versie van versie 3, om een vergelijkbare bewakingservaring te bereiken.

>[!NOTE]
>Ondersteuning voor Azure Red Hat OpenShift is op dit moment een functie in openbare preview.
>

Containerinzichten kunnen worden ingeschakeld voor nieuwe of een of meer bestaande implementaties van Azure Red Hat OpenShift de volgende ondersteunde methoden:

- Voor een bestaand cluster van de Azure Portal of met Azure Resource Manager sjabloon.
- Voor een nieuw cluster met behulp Azure Resource Manager sjabloon of tijdens het maken van een nieuw cluster met behulp van [de Azure CLI.](/cli/azure/openshift#az_openshift_create)

## <a name="supported-and-unsupported-features"></a>Ondersteunde en niet-ondersteunde functies

Container Insights ondersteunt bewakingsfuncties Azure Red Hat OpenShift zoals beschreven in het artikel [Overzicht,](container-insights-overview.md) met uitzondering van de volgende functies:

- Live data (preview)
- [Metrische gegevens verzamelen](container-insights-update-metrics.md) van clusterknooppunten en pods en deze opslaan in Azure Monitor database met metrische gegevens

## <a name="prerequisites"></a>Vereisten

- Een [Log Analytics-werkruimte](../logs/design-logs-deployment.md).

    Container Insights ondersteunt een Log Analytics-werkruimte in de regio's die worden vermeld in [Azure-producten per regio.](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor) Als u uw eigen werkruimte wilt maken, kunt u deze maken [via Azure Resource Manager](../logs/resource-manager-workspace.md), via [PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)of in [de Azure Portal](../logs/quick-create-workspace.md).

- Als u de functies in Container Insights wilt inschakelen en openen, moet u minimaal lid zijn van de rol  Azure-inzender in het Azure-abonnement en lid zijn van de rol Log Analytics-inzender van de Log [*Analytics-werkruimte*](../logs/manage-access.md#manage-access-using-azure-permissions) die is geconfigureerd met Container Insights.

- Als u de bewakingsgegevens wilt weergeven, bent u lid van de rolmachtiging [*Log Analytics-lezer*](../logs/manage-access.md#manage-access-using-azure-permissions) met de Log Analytics-werkruimte die is geconfigureerd met Container Insights.

## <a name="identify-your-log-analytics-workspace-id"></a>De id van uw Log Analytics-werkruimte identificeren

 Als u wilt integreren met een bestaande Log Analytics-werkruimte, begint u met het identificeren van de volledige resource-id van uw Log Analytics-werkruimte. De resource-id van de werkruimte is vereist voor de parameter wanneer u bewaking inschakelen met behulp van Azure Resource Manager `workspaceResourceId` sjabloonmethode.

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

## <a name="enable-for-a-new-cluster-using-an-azure-resource-manager-template"></a>Een nieuw cluster inschakelen met behulp van een Azure Resource Manager sjabloon

Voer de volgende stappen uit om een cluster Azure Red Hat OpenShift te implementeren met bewaking ingeschakeld. Lees voordat u doorgaat de zelfstudie Een cluster [Azure Red Hat OpenShift](../../openshift/tutorial-create-cluster.md) maken om inzicht te krijgen in de afhankelijkheden die u moet configureren, zodat uw omgeving correct is ingesteld.

Deze methode bevat twee JSON-sjablonen. De ene sjabloon geeft de configuratie op voor het implementeren van het cluster met bewaking ingeschakeld en de andere sjabloon bevat parameterwaarden die u configureert om het volgende op te geven:

- De resource Azure Red Hat OpenShift-id van het cluster.

- De resourcegroep waarin het cluster is geïmplementeerd.

- [Azure Active Directory tenant-id die](../../openshift/howto-create-tenant.md#create-a-new-azure-ad-tenant) u hebt genoteerd na het uitvoeren van de stappen voor het maken van een of meer tenant-id's die al zijn gemaakt.

- [Azure Active Directory clienttoepassings-id die u](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-app-registration) hebt genoteerd na het uitvoeren van de stappen voor het maken van een of een al gemaakte clienttoepassings-id.

- [Azure Active Directory clientgeheim dat u](../../openshift/howto-aad-app-configuration.md#create-a-client-secret) hebt genoteerd nadat u de stappen hebt doorlopen om er al een of één te maken.

- [Azure AD-beveiligingsgroep die](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-security-group) is genoteerd na het uitvoeren van de stappen om er al een te maken.

- Resource-id van een bestaande Log Analytics-werkruimte. Zie [Uw Log Analytics-werkruimte-id identificeren](#identify-your-log-analytics-workspace-id) voor meer informatie over het verkrijgen van deze informatie.

- Het aantal hoofdknooppunten dat in het cluster moet worden maken.

- Het aantal rekenknooppunten in het agentpoolprofiel.

- Het aantal infrastructuurknooppunten in het agentpoolprofiel.

Als u niet bekend bent met het concept van het implementeren van resources met behulp van een sjabloon, gaat u naar:

- [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

- [Resources implementeren met Resource Manager-sjablonen en de Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Als u ervoor kiest om de Azure CLI te gebruiken, moet u eerst de CLI lokaal installeren en gebruiken. U moet Azure CLI versie 2.0.65 of hoger uitvoeren. Voer uit om uw versie te `az --version` identificeren. Zie Azure CLI installeren als u de Azure CLI wilt installeren of [upgraden.](/cli/azure/install-azure-cli)

1. Download en sla deze op in een lokale map, de Azure Resource Manager sjabloon en het parameterbestand, om een cluster te maken met de bewakings-invoegtoepassingen met behulp van de volgende opdrachten:

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoring.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoringParam.json`

2. Aanmelden bij Azure

    ```azurecli
    az login
    ```

    Als u toegang hebt tot meerdere abonnementen, voert u `az account set -s {subscription ID}` uit en vervangt u `{subscription ID}` door het abonnement dat u wilt gebruiken.

3. Maak een resourcegroep voor uw cluster als u er nog geen hebt. Zie Ondersteunde regio's voor een lijst met Azure-regio's die OpenShift in Azure [ondersteunen.](../../openshift/supported-resources.md#azure-regions)

    ```azurecli
    az group create -g <clusterResourceGroup> -l <location>
    ```

4. Bewerk het JSON-parameterbestand **newClusterWithMonitoringParam.jsen** werk de volgende waarden bij:

    - *location*
    - *clusterName*
    - *aadTenantId*
    - *aadClientId*
    - *aadClientSecret*
    - *aadCustomerAdminGroupId*
    - *workspaceResourceId*
    - *masterNodeCount*
    - *computeNodeCount*
    - *infraNodeCount*

5. Met de volgende stap wordt het cluster geïmplementeerd met bewaking ingeschakeld met behulp van de Azure CLI.

    ```azurecli
    az deployment group create --resource-group <ClusterResourceGroupName> --template-file ./newClusterWithMonitoring.json --parameters @./newClusterWithMonitoringParam.json
    ```

    De uitvoer lijkt op het volgende:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="enable-for-an-existing-cluster"></a>Inschakelen voor een bestaand cluster

Voer de volgende stappen uit om de bewaking in te Azure Red Hat OpenShift een cluster dat is geïmplementeerd in Azure. U kunt dit doen vanuit de Azure Portal of met behulp van de opgegeven sjablonen.

### <a name="from-the-azure-portal"></a>Vanuit Azure Portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Selecteer in Azure Portal menu of op de startpagina de **optie Azure Monitor.** Selecteer containers **in de** sectie **Inzichten.**

3. Selecteer op **de pagina Monitor - containers** de optie **Niet-bewaakte clusters.**

4. Zoek in de lijst met niet-bewaakte clusters het cluster in de lijst en klik op **Inschakelen.** U kunt de resultaten in de lijst identificeren door te zoeken naar de waarde **ARO** onder de kolom **CLUSTERTYPE**.

5. Als u op de pagina **Onboarding to Container Insights** een bestaande Log Analytics-werkruimte in hetzelfde abonnement als het cluster hebt, selecteert u deze in de vervolgkeuzelijst.  
    In de lijst worden vooraf de standaardwerkruimte en locatie geselecteerd waarin het cluster in het abonnement is geïmplementeerd.

    ![Bewaking inschakelen voor niet-bewaakte clusters](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Als u een nieuwe Log Analytics-werkruimte wilt maken voor het opslaan van de bewakingsgegevens uit het cluster, volgt u de instructies in [Een Log Analytics-werkruimte maken.](../logs/quick-create-workspace.md) Zorg ervoor dat u de werkruimte maakt in hetzelfde abonnement waarin het RedHat OpenShift-cluster is geïmplementeerd.

Nadat u bewaking hebt ingeschakeld, kan het ongeveer 15 minuten duren voordat u de metrische statusgegevens voor het cluster kunt bekijken.

### <a name="enable-using-an-azure-resource-manager-template"></a>Inschakelen met behulp van Azure Resource Manager sjabloon

Deze methode bevat twee JSON-sjablonen. De ene sjabloon geeft de configuratie op voor het inschakelen van bewaking en de andere sjabloon bevat parameterwaarden die u configureert om het volgende op te geven:

- De resource-id van het Azure RedHat OpenShift-cluster.

- De resourcegroep waarin het cluster is geïmplementeerd.

- Een Log Analytics-werkruimte. Zie [Uw Log Analytics-werkruimte-id identificeren](#identify-your-log-analytics-workspace-id) voor meer informatie over het verkrijgen van deze informatie.

Als u niet bekend bent met het concept van het implementeren van resources met behulp van een sjabloon, gaat u naar:

- [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

- [Resources implementeren met Resource Manager-sjablonen en de Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Als u ervoor kiest om de Azure CLI te gebruiken, moet u eerst de CLI lokaal installeren en gebruiken. U moet Azure CLI versie 2.0.65 of hoger uitvoeren. Voer uit om uw versie te `az --version` identificeren. Zie Azure CLI installeren als u de Azure CLI wilt installeren of [upgraden.](/cli/azure/install-azure-cli)

1. Download de sjabloon en het parameterbestand om uw cluster bij te werken met de bewakings-invoegtoepassingen met behulp van de volgende opdrachten:

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_existing_cluster/existingClusterOnboarding.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_existing_cluster/existingClusterParam.json`

2. Aanmelden bij Azure

    ```azurecli
    az login
    ```

    Als u toegang hebt tot meerdere abonnementen, voert u `az account set -s {subscription ID}` uit en vervangt u `{subscription ID}` door het abonnement dat u wilt gebruiken.

3. Geef het abonnement op van het Azure RedHat OpenShift-cluster.

    ```azurecli
    az account set --subscription "Subscription Name"  
    ```

4. Voer de volgende opdracht uit om de clusterlocatie en resource-id te identificeren:

    ```azurecli
    az openshift show -g <clusterResourceGroup> -n <clusterName>
    ```

5. Bewerk het JSON-parameterbestand **existingClusterParam.jsen** werk de waarden *aroResourceId* en *aroResourceLocation bij.* De waarde voor **workspaceResourceId** is de volledige resource-id van uw Log Analytics-werkruimte, die de naam van de werkruimte bevat.

6. Voer de volgende opdrachten uit om te implementeren met Azure CLI:

    ```azurecli
    az deployment group create --resource-group <ClusterResourceGroupName> --template-file ./ExistingClusterOnboarding.json --parameters @./existingClusterParam.json
    ```

    De uitvoer lijkt op het volgende:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="next-steps"></a>Volgende stappen

- Met bewaking ingeschakeld voor het verzamelen van status- en resourcegebruik van uw RedHat OpenShift-cluster en workloads die op deze clusters worden uitgevoerd, leert u [hoe u](container-insights-analyze.md) Container Insights gebruikt.

- De containeragent verzamelt standaard de containerlogboeken stdout/stderr van alle containers die worden uitgevoerd in alle naamruimten, met uitzondering van kube-system. Als u containerlogboekverzameling wilt configureren die specifiek is voor bepaalde naamruimtes of naamruimten, controleert u de configuratie van de [Container Insights-agent](container-insights-agent-config.md) om de gewenste instellingen voor gegevensverzameling te configureren voor het configuratiebestand ConfigMap.

- Als u metrische Prometheus-gegevens uit uw cluster wilt scrapen en analyseren, bekijkt u [Metrische scraping voor Prometheus configureren](container-insights-prometheus-integration.md)

- Zie How to Stop Monitoring Your Azure Red Hat OpenShift [cluster](./container-insights-optout-openshift-v3.md)(Bewaking van uw cluster stoppen) voor meer informatie over het stoppen van de bewaking van uw cluster met Container Insights.