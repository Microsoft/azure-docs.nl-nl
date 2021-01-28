---
title: Hybride Kubernetes-clusters met Azure Monitor voor containers configureren | Microsoft Docs
description: In dit artikel wordt beschreven hoe u Azure Monitor voor containers kunt configureren voor het bewaken van Kubernetes-clusters die worden gehost op Azure Stack of een andere omgeving.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 12901b1d2d7edd85fbe1650600856d09105c15b2
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98936414"
---
# <a name="configure-hybrid-kubernetes-clusters-with-azure-monitor-for-containers"></a>Hybride Kubernetes-clusters met Azure Monitor voor containers configureren

Azure Monitor voor containers biedt uitgebreide bewakings ervaring voor de Azure Kubernetes-service (AKS) en [AKS-engine op Azure](https://github.com/Azure/aks-engine), een zelf beheerd Kubernetes-cluster dat wordt gehost op Azure. In dit artikel wordt beschreven hoe u de bewaking van Kubernetes-clusters die buiten Azure worden gehost, inschakelt en een vergelijk bare bewakings ervaring krijgt.

## <a name="supported-configurations"></a>Ondersteunde configuraties

De volgende configuraties worden officieel ondersteund met Azure Monitor voor containers. Als u een andere versie van Kubernetes-en besturingssysteem versies hebt, kunt u een e-mail sturen naar askcoin@microsoft.com .

- Verschillend

    - On-premises Kubernetes
    - AKS-engine op Azure en Azure Stack. Zie de [AKS-engine op Azure stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview) voor meer informatie
    - Open [SHIFT](https://docs.openshift.com/container-platform/4.3/welcome/index.html) versie 4 en hoger, on-premises of andere Cloud omgevingen.

- De versies van Kubernetes en het ondersteunings beleid zijn hetzelfde als de versies van AKS die worden [ondersteund](../../aks/supported-kubernetes-versions.md).

- De volgende container-Runtimes worden ondersteund: docker-, Moby-en CRI-compatibele runtime, zoals CRI-O en Containered.

- De versie van het Linux-besturings systeem voor hoofd-en worker-knoop punten wordt ondersteund: Ubuntu (18,04 LTS en 16,04 LTS) en Red Hat Enterprise Linux CoreOS 43,81.

- Ondersteund toegangs beheer: Kubernetes RBAC en non-RBAC

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u ervoor zorgen dat u over het volgende beschikt:

- Een [Log Analytics-werkruimte](../platform/design-logs-deployment.md).

    Azure Monitor voor containers ondersteunt een Log Analytics-werk ruimte in de regio's die worden vermeld in azure- [producten per regio](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor). Om uw eigen werk ruimte te maken, kan deze worden gemaakt via [Azure Resource Manager](../samples/resource-manager-workspace.md), via [Power shell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)of in de [Azure Portal](../learn/quick-create-workspace.md).

    >[!NOTE]
    >Het inschakelen van de bewaking van meerdere clusters met dezelfde cluster naam op dezelfde Log Analytics werk ruimte wordt niet ondersteund. Cluster namen moeten uniek zijn.
    >

- U bent lid van de **rol log Analytics Inzender** om container bewaking in te scha kelen. Voor meer informatie over het beheren van de toegang tot een Log Analytics-werk ruimte raadpleegt [u toegang tot de werk ruimte en de gegevens](../platform/manage-access.md)van een logboek.

- Als u de bewakings gegevens wilt bekijken, moet u de rol van [*log Analytics lezer*](../platform/manage-access.md#manage-access-using-azure-permissions) hebben in de log Analytics-werk ruimte, geconfigureerd met Azure monitor voor containers.

- [Helm-client](https://helm.sh/docs/using_helm/) voor het voorbereiden van de grafiek Azure monitor voor containers voor het opgegeven Kubernetes-cluster.

- De volgende proxy-en firewall configuratie gegevens zijn vereist voor de container versie van de Log Analytics-agent voor Linux om te communiceren met Azure Monitor:

    |Agentresource|Poorten |
    |------|---------|
    |*.ods.opinsights.azure.com |Poort 443 |
    |*.oms.opinsights.azure.com |Poort 443 |
    |*. dc.services.visualstudio.com |Poort 443 |

- De container agent vereist `cAdvisor secure port: 10250` dat Kubelet of moet `unsecure port :10255` worden geopend op alle knoop punten in het cluster om metrische gegevens over prestaties te verzamelen. We raden u `secure port: 10250` aan om te configureren in de cAdvisor van Kubelet als deze nog niet is geconfigureerd.

- Voor de container agent moeten de volgende omgevings variabelen worden opgegeven in de container om te communiceren met de Kubernetes API-service binnen het cluster om inventaris gegevens te verzamelen `KUBERNETES_SERVICE_HOST` `KUBERNETES_PORT_443_TCP_PORT` .

>[!IMPORTANT]
>De minimale agent versie die wordt ondersteund voor het bewaken van hybride Kubernetes-clusters is ciprod10182019 of hoger.

## <a name="enable-monitoring"></a>Bewaking inschakelen

Het inschakelen van Azure Monitor voor containers voor het hybride Kubernetes-cluster bestaat uit de volgende stappen in de aangegeven volg orde uit te voeren.

1. Configureer uw Log Analytics-werk ruimte met de container Insights-oplossing.   

2. Schakel de Azure Monitor-grafiek voor containers HELM in met Log Analytics werk ruimte.

Zie [hier](../../azure-monitor/insights/solutions.md)voor meer informatie over het controleren van oplossingen in azure monitor.

### <a name="how-to-add-the-azure-monitor-containers-solution"></a>De Azure Monitor containers-oplossing toevoegen

U kunt de oplossing met de meegeleverde Azure Resource Manager sjabloon implementeren met behulp van de Azure PowerShell-cmdlet `New-AzResourceGroupDeployment` of met Azure cli.

Als u niet bekend bent met het concept van het implementeren van resources met behulp van een sjabloon, raadpleegt u:

- [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

- [Resources implementeren met Resource Manager-sjablonen en Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Als u ervoor kiest om de Azure CLI te gebruiken, moet u de CLI eerst lokaal installeren en gebruiken. U moet de Azure CLI-versie 2.0.59 of hoger uitvoeren. Voer uit om uw versie te identificeren `az --version` . Als u de Azure CLI wilt installeren of upgraden, raadpleegt u [de Azure cli installeren](/cli/azure/install-azure-cli).

Deze methode bevat twee JSON-sjablonen. Met één sjabloon geeft u de configuratie op om bewaking in te scha kelen. de andere bevat parameter waarden die u configureert om het volgende op te geven:

- **workspaceResourceId** : de volledige resource-id van uw log Analytics-werk ruimte.
- **workspaceRegion** : de regio waarin de werk ruimte is gemaakt, die ook wel **locatie** in de werkruimte eigenschappen wordt genoemd bij het weer geven van de Azure Portal.

`workspaceResourceId`Voer de volgende stappen uit en voer vervolgens de Power shell-cmdlet of de Azure cli-opdracht uit om de oplossing toe te voegen, om eerst de volledige resource-id te identificeren van uw log Analytics werk ruimte die vereist is voor de parameter waarde in het **containerSolutionParams.jsin** het bestand.

1. Een lijst met alle abonnementen waartoe u toegang hebt met behulp van de volgende opdracht:

    ```azurecli
    az account list --all -o table
    ```

    De uitvoer ziet er ongeveer als volgt uit:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

    Kopieer de waarde voor **SubscriptionId**.

2. Schakel over naar het abonnement dat als host fungeert voor de Log Analytics-werk ruimte met behulp van de volgende opdracht:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. In het volgende voor beeld wordt de lijst met werk ruimten in uw abonnementen weer gegeven in de standaard JSON-indeling.

    ```azurecli
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Zoek in de uitvoer de naam van de werk ruimte en kopieer de volledige Resource-ID van die Log Analytics werk ruimte onder de veld **-id**.

4. Kopieer en plak de volgende JSON-syntaxis in het bestand:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceResourceId": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace Resource ID"
            }
        },
        "workspaceRegion": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace region"
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "name": "[Concat('ContainerInsights', '-',  uniqueString(parameters('workspaceResourceId')))]",
            "apiVersion": "2017-05-10",
            "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
            "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "apiVersion": "2015-11-01-preview",
                            "type": "Microsoft.OperationsManagement/solutions",
                            "location": "[parameters('workspaceRegion')]",
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "properties": {
                                "workspaceResourceId": "[parameters('workspaceResourceId')]"
                            },
                            "plan": {
                                "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                                "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                                "promotionCode": "",
                                "publisher": "Microsoft"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
         }
      ]
   }
    ```

5. Sla dit bestand als containerSolution.jsop in een lokale map.

6. Plak de volgende JSON-syntaxis in het bestand:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaceResourceId": {
          "value": "<workspaceResourceId>"
      },
      "workspaceRegion": {
        "value": "<workspaceRegion>"
      }
     }
    }
    ```

7. Bewerk de waarden voor **workspaceResourceId** met de waarde die u in stap 3 hebt gekopieerd en voor **WorkspaceRegion** Kopieer de **regio** waarde na het uitvoeren van de Azure cli-opdracht [AZ monitor log-Analytics werk ruimte show](/cli/azure/monitor/log-analytics/workspace#az-monitor-log-analytics-workspace-list&preserve-view=true).

8. Sla dit bestand als containerSolutionParams.jsop in een lokale map.

9. U kunt deze sjabloon nu implementeren.

   - Als u wilt implementeren met Azure PowerShell, gebruikt u de volgende opdrachten in de map met de sjabloon:

       ```powershell
       # configure and login to the cloud of Log Analytics workspace.Specify the corresponding cloud environment of your workspace to below command.
       Connect-AzureRmAccount -Environment <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       ```

       ```powershell
       # set the context of the subscription of Log Analytics workspace
       Set-AzureRmContext -SubscriptionId <subscription Id of Log Analytics workspace>
       ```

       ```powershell
       # execute deployment command to add Container Insights solution to the specified Log Analytics workspace
       New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <resource group of Log Analytics workspace> -TemplateFile .\containerSolution.json -TemplateParameterFile .\containerSolutionParams.json
       ```

       Het kan enkele minuten duren voordat de configuratie is gewijzigd. Wanneer de service is voltooid, wordt een bericht weer gegeven dat er ongeveer als volgt uitziet en het resultaat bevat:

       ```powershell
       provisioningState       : Succeeded
       ```

   - Als u wilt implementeren met Azure CLI, voert u de volgende opdrachten uit:

       ```azurecli
       az login
       az account set --name <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       az login
       az account set --subscription "Subscription Name"
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       az deployment group create --resource-group <resource group of log analytics workspace> --name <deployment name> --template-file  ./containerSolution.json --parameters @./containerSolutionParams.json
       ```

       Het kan enkele minuten duren voordat de configuratie is gewijzigd. Wanneer de service is voltooid, wordt een bericht weer gegeven dat er ongeveer als volgt uitziet en het resultaat bevat:

       ```azurecli
       provisioningState       : Succeeded
       ```

       Nadat u bewaking hebt ingeschakeld, kan het ongeveer 15 minuten duren voordat u de metrische gegevens van de status voor het cluster kunt weer geven.

## <a name="install-the-helm-chart"></a>Het HELM-diagram installeren

In deze sectie installeert u de container agent voor Azure Monitor voor containers. Voordat u doorgaat, moet u de werk ruimte-ID identificeren die is vereist voor de `omsagent.secret.wsid` para meter en de primaire sleutel die vereist is voor de `omsagent.secret.key` para meter. U kunt deze informatie vinden door de volgende stappen uit te voeren en vervolgens de opdrachten uit te voeren om de agent te installeren met behulp van de HELM-grafiek.

1. Voer de volgende opdracht uit om de werk ruimte-ID te identificeren:

    `az monitor log-analytics workspace list --resource-group <resourceGroupName>`

    In de uitvoer vindt u de naam van de werk ruimte onder de veld **naam** en kopieert u de werk ruimte-id van die log Analytics werk ruimte onder het veld **customerID**.

2. Voer de volgende opdracht uit om de primaire sleutel voor de werk ruimte te identificeren:

    `az monitor log-analytics workspace get-shared-keys --resource-group <resourceGroupName> --workspace-name <logAnalyticsWorkspaceName>`

    In de uitvoer vindt u de primaire sleutel onder het veld **primarySharedKey** en kopieert u de waarde.

>[!NOTE]
>De volgende opdrachten zijn alleen van toepassing op helm versie 2. Het gebruik van de `--name` para meter is niet van toepassing op helm versie 3. 

>[!NOTE]
>Als uw Kubernetes-cluster communiceert via een proxy server, configureert u de para meter `omsagent.proxy` met de URL van de proxy server. Als het cluster niet via een proxy server communiceert, hoeft u deze para meter niet op te geven. Zie [Configure proxy endpoint](#configure-proxy-endpoint) verderop in dit artikel voor meer informatie.

3. Voeg de opslag plaats van Azure Charts toe aan uw lokale lijst door de volgende opdracht uit te voeren:

    ```
    helm repo add microsoft https://microsoft.github.io/charts/repo
    ````

4. Installeer de grafiek door de volgende opdracht uit te voeren:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<my_prod_cluster> microsoft/azuremonitor-containers
    ```

    Als de Log Analytics-werk ruimte zich in azure China 21Vianet bevindt, voert u de volgende opdracht uit:

    ```
    $ helm install --name myrelease-1 \
     --set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

    Als de Log Analytics-werk ruimte zich in azure US Government bevindt, voert u de volgende opdracht uit:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

### <a name="enable-the-helm-chart-using-the-api-model"></a>De helm-grafiek inschakelen met het API-model

U kunt een invoeg toepassing opgeven in het JSON-bestand AKS engine cluster Specification, ook wel het API-model genoemd. Geef in deze invoeg toepassing de base64-gecodeerde versie van `WorkspaceGUID` en `WorkspaceKey` de log Analytics-werk ruimte op waar de verzamelde bewakings gegevens worden opgeslagen. U kunt de `WorkspaceGUID` en de `WorkspaceKey` stappen 1 en 2 in de vorige sectie vinden.

Ondersteunde API-definities voor het Azure Stack hub-cluster vindt u in dit voor beeld- [kubernetes-container-monitoring_existing_workspace_id_and_key.jsop](https://github.com/Azure/aks-engine/blob/master/examples/addons/container-monitoring/kubernetes-container-monitoring_existing_workspace_id_and_key.json). Zoek in het bijzonder de eigenschap **Addons** in **kubernetesConfig**:

```json
"orchestratorType": "Kubernetes",
       "kubernetesConfig": {
         "addons": [
           {
             "name": "container-monitoring",
             "enabled": true,
             "config": {
               "workspaceGuid": "<Azure Log Analytics Workspace Id in Base-64 encoded>",
               "workspaceKey": "<Azure Log Analytics Workspace Key in Base-64 encoded>"
             }
           }
         ]
       }
```

## <a name="configure-agent-data-collection"></a>Gegevens verzameling van agent configureren

Als u een stert met grafiek versie 1.0.0, worden de instellingen voor het verzamelen van agents bepaald vanuit de ConfigMap. Raadpleeg [hier](container-insights-agent-config.md)de documentatie over het verzamelen van instellingen voor agent gegevens.

Nadat u de grafiek hebt geïmplementeerd, kunt u de gegevens voor uw hybride Kubernetes-cluster in Azure Monitor voor containers in de Azure Portal controleren.  

>[!NOTE]
>De latentie van de opname is ongeveer vijf tot tien minuten van de agent die moet worden doorgevoerd in de Azure Log Analytics-werk ruimte. De status van het cluster toont de waarde **geen gegevens** of is **onbekend** totdat alle vereiste bewakings gegevens beschikbaar zijn in azure monitor.

## <a name="configure-proxy-endpoint"></a>Proxy-eind punt configureren

Vanaf grafiek versie 2.7.1 wordt de grafiek ondersteund om het proxy-eind punt op te geven met de `omsagent.proxy` grafiek parameter. Hierdoor kan het communiceren via uw proxy server. De communicatie tussen de Azure Monitor voor containers agent en Azure Monitor kan een HTTP-of HTTPS-proxy server zijn en zowel anonieme als basis verificatie (gebruikers naam/wacht woord) worden ondersteund.

De waarde van de proxy configuratie heeft de volgende syntaxis: `[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
>Als uw proxy server geen verificatie vereist, moet u nog steeds een psuedo-gebruikers naam en-wacht woord opgeven. Dit kan een gebruikers naam of wacht woord zijn.

|Eigenschap| Beschrijving |
|--------|-------------|
|Protocol | http of https |
|gebruiker | Optionele gebruikers naam voor proxy verificatie |
|wachtwoord | Optioneel wacht woord voor proxy verificatie |
|proxyhost | Adres of FQDN van de proxy server |
|poort | Optioneel poort nummer voor de proxy server |

Bijvoorbeeld: `omsagent.proxy=http://user01:password@proxy01.contoso.com:8080`

Als u het protocol als **http** opgeeft, worden de HTTP-aanvragen gemaakt met behulp van een beveiligde SSL/TLS-verbinding. Uw proxy server moet SSL/TLS-protocollen ondersteunen.

## <a name="troubleshooting"></a>Problemen oplossen

Als er een fout optreedt bij het inschakelen van bewaking voor uw hybride Kubernetes-cluster, kopieert u het Power shell-script [TroubleshootError_nonAzureK8s.ps1](https://aka.ms/troubleshoot-non-azure-k8s) en slaat u het op in een map op uw computer. Dit script wordt verstrekt om te helpen bij het detecteren en oplossen van de gevonden problemen. De problemen waarmee het volgende kan worden opgelost:

- De opgegeven Log Analytics werk ruimte is geldig
- De Log Analytics-werk ruimte is geconfigureerd met de Azure Monitor voor containers. Als dat niet het geval is, configureert u de werk ruimte.
- De OmsAgent-replicaset wordt uitgevoerd
- OmsAgent-daemonset-peulen worden uitgevoerd
- De OmsAgent Health-Service wordt uitgevoerd
- De Log Analytics werk ruimte-ID en-sleutel die zijn geconfigureerd op de container agent komen overeen met de werk ruimte waarin het inzicht is geconfigureerd.
- Controleer of alle Linux worker-knoop punten een `kubernetes.io/role=agent` label hebben om RS pod te plannen. Als deze niet bestaat, voegt u deze toe.
- Valideer `cAdvisor secure port:10250` of `unsecure port: 10255` is geopend op alle knoop punten in het cluster.

Als u wilt uitvoeren met Azure PowerShell, gebruikt u de volgende opdrachten in de map die het script bevat:

```powershell
.\TroubleshootError_nonAzureK8s.ps1 - azureLogAnalyticsWorkspaceResourceId </subscriptions/<subscriptionId>/resourceGroups/<resourcegroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName> -kubeConfig <kubeConfigFile> -clusterContextInKubeconfig <clusterContext>
```

## <a name="next-steps"></a>Volgende stappen

Als bewaking is ingeschakeld voor het verzamelen van het status-en resource gebruik van uw hybride Kubernetes-cluster en werk belastingen die hierop worden uitgevoerd, leert [u hoe u Azure monitor gebruikt](container-insights-analyze.md) voor containers.
