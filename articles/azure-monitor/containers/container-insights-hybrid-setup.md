---
title: Hybride Kubernetes-clusters configureren met Container Insights | Microsoft Docs
description: In dit artikel wordt beschreven hoe u Container Insights kunt configureren voor het bewaken van Kubernetes-clusters die worden gehost op Azure Stack of een andere omgeving.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 90a4c14397df8e70fc8f3d88bc339f826bb1ccc9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767019"
---
# <a name="configure-hybrid-kubernetes-clusters-with-container-insights"></a>Hybride Kubernetes-clusters configureren met Container Insights

Container Insights biedt uitgebreide bewakingservaring voor de Azure Kubernetes Service (AKS) en [AKS-engine in Azure.](https://github.com/Azure/aks-engine)Dit is een zelf-beheerd Kubernetes-cluster dat wordt gehost in Azure. In dit artikel wordt beschreven hoe u bewaking van Kubernetes-clusters die buiten Azure worden gehost, kunt inschakelen en een vergelijkbare bewakingservaring kunt bereiken.

## <a name="supported-configurations"></a>Ondersteunde configuraties

De volgende configuraties worden officieel ondersteund met Container Insights. Als u een andere versie van Kubernetes en versies van het besturingssysteem hebt, stuurt u een e-mail naar askcoin@microsoft.com .

- Omgevingen:

    - Kubernetes on-premises
    - AKS-engine in Azure en Azure Stack. Zie [AKS Engine on Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview)
    - [OpenShift](https://docs.openshift.com/container-platform/4.3/welcome/index.html) versie 4 en hoger, on-premises of andere cloudomgevingen.

- Versies van Kubernetes en ondersteuningsbeleid zijn hetzelfde als versies van [AKS die worden ondersteund.](../../aks/supported-kubernetes-versions.md)

- De volgende containerruntimes worden ondersteund: Met Docker, Moby en CRI compatibele runtimes zoals CRI-O en ContainerD.

- Ondersteunde linux-besturingssysteemre release voor hoofd- en werkknooppunten zijn: Ubuntu (18.04 LTS en 16.04 LTS) en Red Hat Enterprise Linux CoreOS 43.81.

- Toegangsbeheer ondersteund: Kubernetes RBAC en niet-RBAC

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u ervoor zorgen dat u het volgende hebt:

- Een [Log Analytics-werkruimte](../logs/design-logs-deployment.md).

    Container Insights ondersteunt een Log Analytics-werkruimte in de regio's die worden vermeld in [Azure-producten per regio.](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor) Als u uw eigen werkruimte wilt maken, kunt u deze maken [via Azure Resource Manager](../logs/resource-manager-workspace.md), via [PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)of in [de Azure Portal](../logs/quick-create-workspace.md).

    >[!NOTE]
    >Bewaking van meerdere clusters met dezelfde clusternaam inschakelen voor dezelfde Log Analytics-werkruimte wordt niet ondersteund. Clusternamen moeten uniek zijn.
    >

- U bent lid van de rol **Log Analytics-inzender om** containerbewaking in te stellen. Zie Toegang tot werkruimte en logboekgegevens beheren voor meer informatie over het beheren van toegang tot een Log [Analytics-werkruimte.](../logs/manage-access.md)

- Als u de bewakingsgegevens wilt weergeven, moet u de [*rol Van Log Analytics-lezer*](../logs/manage-access.md#manage-access-using-azure-permissions) hebben in de Log Analytics-werkruimte, geconfigureerd met Container Insights.

- [HELM-client](https://helm.sh/docs/using_helm/) voor onboarding van de Container Insights-grafiek voor het opgegeven Kubernetes-cluster.

- De volgende proxy- en firewallconfiguratie-informatie is vereist om de in een container geplaatste versie van de Log Analytics-agent voor Linux te laten communiceren met Azure Monitor:

    |Agentresource|Poorten |
    |------|---------|
    |*.ods.opinsights.azure.com |Poort 443 |
    |*.oms.opinsights.azure.com |Poort 443 |
    |*.dc.services.visualstudio.com |Poort 443 |

- Voor de in een container geplaatste agent moeten kubelet's of worden geopend op alle knooppunten `cAdvisor secure port: 10250` in het cluster om `unsecure port :10255` metrische prestatiegegevens te verzamelen. We raden u aan om op de c Advisor van de Kubelet te configureren `secure port: 10250` als deze nog niet is geconfigureerd.

- Voor de containeragent moeten de volgende omgevingsvariabelen worden opgegeven in de container om te kunnen communiceren met de Kubernetes API-service binnen het cluster om inventarisgegevens te verzamelen - `KUBERNETES_SERVICE_HOST` en `KUBERNETES_PORT_443_TCP_PORT` .

>[!IMPORTANT]
>De minimale agentversie die wordt ondersteund voor het bewaken van hybride Kubernetes-clusters is ciprod10182019 of hoger.

## <a name="enable-monitoring"></a>Bewaking inschakelen

Het inschakelen van Container Insights voor het hybride Kubernetes-cluster bestaat uit het in de volgorde uitvoeren van de volgende stappen.

1. Configureer uw Log Analytics-werkruimte met de Container Insights-oplossing.   

2. Schakel de HELM-grafiek containerinzichten in met de Log Analytics-werkruimte.

Voor meer informatie over bewakingsoplossingen in Azure Monitor u [hier](../../azure-monitor/insights/solutions.md).

### <a name="how-to-add-the-azure-monitor-containers-solution"></a>De oplossing Azure Monitor Containers toevoegen

U kunt de oplossing implementeren met de opgegeven Azure Resource Manager met behulp van de Azure PowerShell-cmdlet `New-AzResourceGroupDeployment` of met Azure CLI.

Als u niet bekend bent met het concept van het implementeren van resources met behulp van een sjabloon, gaat u naar:

- [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

- [Resources implementeren met Resource Manager-sjablonen en de Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Als u ervoor kiest om de Azure CLI te gebruiken, moet u eerst de CLI lokaal installeren en gebruiken. U moet azure CLI versie 2.0.59 of hoger uitvoeren. Voer uit om uw versie te `az --version` identificeren. Zie Azure CLI installeren als u de Azure CLI wilt installeren of [upgraden.](/cli/azure/install-azure-cli)

Deze methode bevat twee JSON-sjablonen. De ene sjabloon geeft de configuratie op voor het inschakelen van bewaking en de andere sjabloon bevat parameterwaarden die u configureert om het volgende op te geven:

- **workspaceResourceId:** de volledige resource-id van uw Log Analytics-werkruimte.
- **workspaceRegion:** de regio waarin de werkruimte wordt gemaakt. Dit wordt ook wel Locatie **genoemd** in de eigenschappen van de werkruimte bij weergave vanuit de Azure Portal.

Voer de volgende stappen uit en voer vervolgens de PowerShell-cmdlet of Azure CLI-opdracht uit om eerst de volledige resource-id van uw Log Analytics-werkruimte te identificeren die vereist is voor de parameterwaarde in de `workspaceResourceId` **containerSolutionParams.json-file.**

1. Gebruik de volgende opdracht om alle abonnementen weer te geven waar u toegang toe hebt:

    ```azurecli
    az account list --all -o table
    ```

    De uitvoer lijkt op het volgende:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

    Kopieer de waarde voor **SubscriptionId.**

2. Schakel over naar het abonnement dat als host voor de Log Analytics-werkruimte wordt gebruikt met de volgende opdracht:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. In het volgende voorbeeld wordt de lijst met werkruimten in uw abonnementen weergegeven in de standaard-JSON-indeling.

    ```azurecli
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Zoek in de uitvoer de naam van de werkruimte en kopieer vervolgens de volledige resource-id van die Log Analytics-werkruimte onder de **veld-id**.

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

5. Sla dit bestand op containerSolution.jsin een lokale map.

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

7. Bewerk de waarden voor **workspaceResourceId** met behulp van de waarde die  u in stap 3 hebt gekopieerd en kopieer voor **workspaceRegion** de regiowaarde na het uitvoeren van de Azure [CLI-opdracht az monitor log-analytics workspace show.](/cli/azure/monitor/log-analytics/workspace#az_monitor-log-analytics-workspace-list&preserve-view=true)

8. Sla dit bestand op containerSolutionParams.jsin een lokale map.

9. U kunt deze sjabloon nu implementeren.

   - Als u wilt implementeren Azure PowerShell, gebruikt u de volgende opdrachten in de map die de sjabloon bevat:

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

       Het kan enkele minuten duren voordat de configuratie is gewijzigd. Wanneer dit is voltooid, wordt een bericht weergegeven dat lijkt op het volgende en het resultaat bevat:

       ```powershell
       provisioningState       : Succeeded
       ```

   - Voer de volgende opdrachten uit om te implementeren met Azure CLI:

       ```azurecli
       az login
       az account set --name <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       az login
       az account set --subscription "Subscription Name"
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       az deployment group create --resource-group <resource group of log analytics workspace> --name <deployment name> --template-file  ./containerSolution.json --parameters @./containerSolutionParams.json
       ```

       Het kan enkele minuten duren voordat de configuratie is gewijzigd. Wanneer dit is voltooid, wordt een bericht weergegeven dat lijkt op het volgende en het resultaat bevat:

       ```azurecli
       provisioningState       : Succeeded
       ```

       Nadat u bewaking hebt ingeschakeld, kan het ongeveer 15 minuten duren voordat u de metrische statusgegevens voor het cluster kunt bekijken.

## <a name="install-the-helm-chart"></a>De HELM-grafiek installeren

In deze sectie installeert u de containeragent voor Container Insights. Voordat u doorgaat, moet u de werkruimte-id identificeren die vereist is voor de parameter en de primaire `omsagent.secret.wsid` sleutel die vereist is voor de `omsagent.secret.key` parameter. U kunt deze informatie identificeren door de volgende stappen uit te voeren en vervolgens de opdrachten uit te voeren om de agent te installeren met behulp van de HELM-grafiek.

1. Voer de volgende opdracht uit om de werkruimte-id te identificeren:

    `az monitor log-analytics workspace list --resource-group <resourceGroupName>`

    Zoek in de uitvoer de naam van de werkruimte onder de veldnaam **en** kopieer vervolgens de werkruimte-id van die Log Analytics-werkruimte onder het veld **customerID**.

2. Voer de volgende opdracht uit om de primaire sleutel voor de werkruimte te identificeren:

    `az monitor log-analytics workspace get-shared-keys --resource-group <resourceGroupName> --workspace-name <logAnalyticsWorkspaceName>`

    Zoek in de uitvoer de primaire sleutel onder het veld **primarySharedKey** en kopieer de waarde.

>[!NOTE]
>De volgende opdrachten zijn alleen van toepassing op Helm-versie 2. Het gebruik van `--name` de parameter is niet van toepassing met Helm-versie 3. 

>[!NOTE]
>Als uw Kubernetes-cluster communiceert via een proxyserver, configureert u de parameter `omsagent.proxy` met de URL van de proxyserver. Als het cluster niet communiceert via een proxyserver, hoeft u deze parameter niet op te geven. Zie Proxy-eindpunt configureren verder [in](#configure-proxy-endpoint) dit artikel voor meer informatie.

3. Voeg de Opslagplaats voor Azure-grafieken toe aan uw lokale lijst door de volgende opdracht uit te voeren:

    ```
    helm repo add microsoft https://microsoft.github.io/charts/repo
    ````

4. Installeer de grafiek door de volgende opdracht uit te voeren:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<my_prod_cluster> microsoft/azuremonitor-containers
    ```

    Als de Log Analytics-werkruimte zich in Azure China 21Vianet, voer dan de volgende opdracht uit:

    ```
    $ helm install --name myrelease-1 \
     --set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

    Voer de volgende opdracht uit als de Log Analytics-werkruimte zich in Azure VOOR de Amerikaanse overheid voordeed:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

### <a name="enable-the-helm-chart-using-the-api-model"></a>De Helm-grafiek inschakelen met behulp van het API-model

U kunt een invoeg toevoegen in het JSON-bestand met de AKS Engine-clusterspecificatie, ook wel het API-model genoemd. Geef in deze invoegcode de base64-gecodeerde versie van en op van de Log Analytics-werkruimte waarin `WorkspaceGUID` `WorkspaceKey` de verzamelde bewakingsgegevens worden opgeslagen. U vindt de `WorkspaceGUID` en met behulp van stap `WorkspaceKey` 1 en 2 in de vorige sectie.

Ondersteunde API-definities voor Azure Stack Hub cluster vindt u in dit voorbeeld: [kubernetes-container-monitoring_existing_workspace_id_and_key.jsop](https://github.com/Azure/aks-engine/blob/master/examples/addons/container-monitoring/kubernetes-container-monitoring_existing_workspace_id_and_key.json). Zoek met name de **eigenschap addons** in **kubernetesConfig**:

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

## <a name="configure-agent-data-collection"></a>Verzamelen van agentgegevens configureren

In de grafiekversie 1.0.0 worden de instellingen voor het verzamelen van agentgegevens beheerd vanuit de ConfigMap. Raadpleeg hier de documentatie over instellingen voor het verzamelen van [agentgegevens.](container-insights-agent-config.md)

Nadat u de grafiek hebt geïmplementeerd, kunt u de gegevens voor uw hybride Kubernetes-cluster controleren in Container Insights van de Azure Portal.  

>[!NOTE]
>De opnamelatentie is ongeveer vijf tot tien minuten vanaf agent om door te geven in de Azure Log Analytics-werkruimte. Status van het cluster geeft de waarde **Geen gegevens** of Onbekend **weer** totdat alle vereiste bewakingsgegevens beschikbaar zijn in Azure Monitor.

## <a name="configure-proxy-endpoint"></a>Proxy-eindpunt configureren

Vanaf grafiekversie 2.7.1 ondersteunt grafiek het opgeven van het proxy-eindpunt met de `omsagent.proxy` grafiekparameter. Hierdoor kan deze communiceren via uw proxyserver. Communicatie tussen de Container Insights-agent en Azure Monitor kan een HTTP- of HTTPS-proxyserver zijn en zowel anonieme als basisverificatie (gebruikersnaam/wachtwoord) worden ondersteund.

De waarde van de proxyconfiguratie heeft de volgende syntaxis: `[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
>Als uw proxyserver geen verificatie vereist, moet u nog steeds een psuedo gebruikersnaam/wachtwoord opgeven. Dit kan elke gebruikersnaam of elk wachtwoord zijn.

|Eigenschap| Beschrijving |
|--------|-------------|
|Protocol | http of https |
|gebruiker | Optionele gebruikersnaam voor proxyverificatie |
|wachtwoord | Optioneel wachtwoord voor proxyverificatie |
|proxyhost | Adres of FQDN van de proxyserver |
|poort | Optioneel poortnummer voor de proxyserver |

Bijvoorbeeld: `omsagent.proxy=http://user01:password@proxy01.contoso.com:8080`

Als u het protocol opgeeft als **http,** worden de HTTP-aanvragen gemaakt met behulp van een beveiligde SSL/TLS-verbinding. Uw proxyserver moet SSL/TLS-protocollen ondersteunen.

## <a name="troubleshooting"></a>Problemen oplossen

Als er een fout is opgetreden bij het inschakelen van bewaking voor uw hybride Kubernetes-cluster, kopieert u het PowerShell-script [TroubleshootError_nonAzureK8s.ps1](https://aka.ms/troubleshoot-non-azure-k8s) en sla het op in een map op uw computer. Dit script wordt aangeboden om te helpen bij het detecteren en oplossen van de problemen die zijn aangetroffen. De problemen die worden gedetecteerd en gecorrigeerd, zijn als volgt:

- De opgegeven Log Analytics-werkruimte is geldig
- De Log Analytics-werkruimte is geconfigureerd met de Container Insights-oplossing. Als dat niet het beste is, configureert u de werkruimte.
- OmsAgent-replicasetpods worden uitgevoerd
- OmsAgent-daemonset-pods worden uitgevoerd
- OmsAgent Health-service wordt uitgevoerd
- De Log Analytics-werkruimte-id en -sleutel die zijn geconfigureerd op de in een container geplaatste agent komen overeen met de werkruimte waar de Inzichten mee is geconfigureerd.
- Controleer of alle Linux-werkknooppunten `kubernetes.io/role=agent` een label hebben om de RS-pod te plannen. Als deze niet bestaat, voegt u deze toe.
- Valideer `cAdvisor secure port:10250` of wordt geopend op alle `unsecure port: 10255` knooppunten in het cluster.

Als u wilt uitvoeren Azure PowerShell, gebruikt u de volgende opdrachten in de map die het script bevat:

```powershell
.\TroubleshootError_nonAzureK8s.ps1 - azureLogAnalyticsWorkspaceResourceId </subscriptions/<subscriptionId>/resourceGroups/<resourcegroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName> -kubeConfig <kubeConfigFile> -clusterContextInKubeconfig <clusterContext>
```

## <a name="next-steps"></a>Volgende stappen

Met bewaking ingeschakeld voor het verzamelen van status- en resourcegebruik van uw hybride Kubernetes-cluster en workloads die op deze clusters worden uitgevoerd, leert u [hoe u](container-insights-analyze.md) Container Insights gebruikt.