---
title: Azure Arc enabled Kubernetes-cluster configureren met Azure Monitor voor containers | Microsoft Docs
description: In dit artikel wordt beschreven hoe u bewaking configureert met Azure Monitor voor containers op Azure Arc ingeschakelde Kubernetes-clusters.
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 77b536141f0e7c6094964011719a0e536e8d33f1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100612357"
---
# <a name="enable-monitoring-of-azure-arc-enabled-kubernetes-cluster"></a>De bewaking van een Kubernetes-cluster met Azure Arc inschakelen

Azure Monitor voor containers biedt uitgebreide bewakings ervaring voor de Azure Kubernetes-service (AKS) en AKS-engine clusters. In dit artikel wordt beschreven hoe u de bewaking van uw Kubernetes-clusters die buiten Azure worden gehost, inschakelt voor een vergelijk bare bewakings ervaring.

Azure Monitor voor containers kunnen worden ingeschakeld voor een of meer bestaande implementaties van Kubernetes met behulp van een Power shell-of bash-script.

## <a name="supported-configurations"></a>Ondersteunde configuraties

Azure Monitor voor containers ondersteunt bewaking van Azure Arc enabled Kubernetes (preview), zoals beschreven in het [overzichts](container-insights-overview.md) artikel, met uitzonde ring van de volgende functies:

- Live-gegevens (preview-versie)

Het volgende wordt officieel ondersteund met Azure Monitor voor containers:

- De versies van Kubernetes en het ondersteunings beleid zijn hetzelfde als de versies van AKS die worden [ondersteund](../../aks/supported-kubernetes-versions.md).

- De volgende container-Runtimes worden ondersteund: docker-, Moby-en CRI-compatibele runtime, zoals CRI-O en Containered.

- De versie van het Linux-besturings systeem voor de hoofd-en worker-knoop punten wordt ondersteund: Ubuntu (18,04 LTS en 16,04 LTS).

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u ervoor zorgen dat u over het volgende beschikt:

- Een Log Analytics-werkruimte.

    Azure Monitor voor containers ondersteunt een Log Analytics-werk ruimte in de regio's die worden vermeld in azure- [producten per regio](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor). Om uw eigen werk ruimte te maken, kan deze worden gemaakt via [Azure Resource Manager](../samples/resource-manager-workspace.md), via [Power shell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)of in de [Azure Portal](../learn/quick-create-workspace.md).

- Als u de functies in Azure Monitor voor containers wilt inschakelen en openen, moet u Mini maal lid zijn van de rol Azure *contributor* in het Azure-abonnement en een lid van de rol [*log Analytics Inzender*](../platform/manage-access.md#manage-access-using-azure-permissions) van de werk ruimte log Analytics die is geconfigureerd met Azure monitor voor containers.

- U bent lid van de rol [Inzender](../../role-based-access-control/built-in-roles.md#contributor) op de Azure Arc-cluster resource.

- Als u de bewakings gegevens wilt bekijken, bent u lid van de machtiging [*log Analytics lezer*](../platform/manage-access.md#manage-access-using-azure-permissions) -rol met de log Analytics werk ruimte die is geconfigureerd met Azure monitor voor containers.

- [Helm-client](https://helm.sh/docs/using_helm/) voor het voorbereiden van de grafiek Azure monitor voor containers voor het opgegeven Kubernetes-cluster.

- De volgende proxy-en firewall configuratie gegevens zijn vereist voor de container versie van de Log Analytics-agent voor Linux om te communiceren met Azure Monitor:

    |Agentresource|Poorten |
    |------|---------|
    |`*.ods.opinsights.azure.com` |Poort 443 |
    |`*.oms.opinsights.azure.com` |Poort 443 |
    |`*.dc.services.visualstudio.com` |Poort 443 |

- De container agent vereist `cAdvisor secure port: 10250` dat Kubelet of moet `unsecure port :10255` worden geopend op alle knoop punten in het cluster om metrische gegevens over prestaties te verzamelen. We raden u `secure port: 10250` aan om te configureren in de cAdvisor van Kubelet als deze nog niet is geconfigureerd.

- Voor de container agent moeten de volgende omgevings variabelen worden opgegeven in de container om te communiceren met de Kubernetes API-service binnen het cluster om inventaris gegevens te verzamelen `KUBERNETES_SERVICE_HOST` `KUBERNETES_PORT_443_TCP_PORT` .

    >[!IMPORTANT]
    >De minimale agent versie die wordt ondersteund voor het bewaken van Kubernetes-clusters met Arc-functionaliteit is ciprod04162020 of hoger.

- [Power shell core](/powershell/scripting/install/installing-powershell?view=powershell-6&preserve-view=true) is vereist als u controle inschakelt met behulp van de Power shell-methode script.

- [Bash versie 4](https://www.gnu.org/software/bash/) is vereist als u controle inschakelt met behulp van de script methode bash.

## <a name="identify-workspace-resource-id"></a>Resource-ID van werk ruimte identificeren

Als u de bewaking van uw cluster wilt inschakelen met het Power shell-of bash-script dat u eerder hebt gedownload en met een bestaande Log Analytics-werk ruimte hebt geïntegreerd, voert u de volgende stappen uit om eerst de volledige Resource-ID van uw Log Analytics-werk ruimte te identificeren. Dit is vereist voor de `workspaceResourceId` para meter bij het uitvoeren van de opdracht om de invoeg toepassing bewaking in te scha kelen met de opgegeven werk ruimte. Als u geen werk ruimte hebt om op te geven, kunt u overs laan met de `workspaceResourceId` para meter, en kunt u het script een nieuwe werk ruimte laten maken.

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

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Zoek in de uitvoer de naam van de werk ruimte en kopieer de volledige Resource-ID van die Log Analytics werk ruimte onder de veld **-id**.

## <a name="enable-monitoring-using-powershell"></a>Bewaking inschakelen met Power shell

1. Down load het script en sla het op in een lokale map die uw cluster configureert met de invoeg toepassing voor bewaking met behulp van de volgende opdrachten:

    ```powershell
    Invoke-WebRequest https://aka.ms/enable-monitoring-powershell-script -OutFile enable-monitoring.ps1
    ```

2. Configureer de `$azureArcClusterResourceId` variabele door de bijbehorende waarden in te stellen voor `subscriptionId` `resourceGroupName` en `clusterName` de resource-id van uw Azure Arc-Kubernetes-cluster resource weer te geven.

    ```powershell
    $azureArcClusterResourceId = "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. Configureer de `$kubeContext` variabele met de **uitvoeren-context** van uw cluster door de opdracht uit te voeren `kubectl config get-contexts` . Als u de huidige context wilt gebruiken, stelt u de waarde in op `""` .

    ```powershell
    $kubeContext = "<kubeContext name of your k8s cluster>"
    ```

4. Als u bestaande Azure Monitor Log Analytics werk ruimte wilt gebruiken, configureert u de variabele `$logAnalyticsWorkspaceResourceId` met de bijbehorende waarde voor de resource-id van de werk ruimte. Als dat niet het geval is, stelt u de variabele in op `""` en het script maakt een standaardwerk ruimte in de standaard resource groep van het cluster abonnement als deze nog niet in de regio bestaat. De standaardwerk ruimte die wordt gemaakt, lijkt op de indeling van *DefaultWorkspace- \<SubscriptionID> - \<Region>*.

    ```powershell
    $logAnalyticsWorkspaceResourceId = "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.operationalinsights/workspaces/<workspaceName>"
    ```

5. Als uw met Arc ingeschakeld Kubernetes-cluster communiceert via een proxy server, configureert u de variabele `$proxyEndpoint` met de URL van de proxy server. Als het cluster niet via een proxy server communiceert, kunt u de waarde instellen op `""` .  Zie [Configure proxy endpoint](#configure-proxy-endpoint) verderop in dit artikel voor meer informatie.

6. Voer de volgende opdracht uit om controle in te scha kelen.

    ```powershell
    .\enable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -kubeContext $kubeContext -workspaceResourceId $logAnalyticsWorkspaceResourceId -proxyEndpoint $proxyEndpoint
    ```

Nadat u bewaking hebt ingeschakeld, kan het ongeveer 15 minuten duren voordat u de metrische gegevens van de status voor het cluster kunt weer geven.

### <a name="using-service-principal"></a>Service-Principal gebruiken
Het script *enable-monitoring.ps1* maakt gebruik van de aanmelding voor interactieve apparaten. Als u de voor keur geeft aan niet-interactieve aanmelding, kunt u een bestaande Service-Principal gebruiken of een nieuwe maken die de vereiste machtigingen heeft, zoals wordt beschreven in [vereisten](#prerequisites). Als u de Service-Principal wilt gebruiken, moet u $servicePrincipalClientId, $servicePrincipalClientSecret en $tenantId para meters door geven aan de waarden van de service-principal die u wilt gebruiken voor *enable-monitoring.ps1* script.

```powershell
$subscriptionId = "<subscription Id of the Azure Arc connected cluster resource>"
$servicePrincipal = New-AzADServicePrincipal -Role Contributor -Scope "/subscriptions/$subscriptionId"
```

De roltoewijzing hieronder is alleen van toepassing als u een bestaande Log Analytics-werk ruimte gebruikt in een ander Azure-abonnement dan de met Arc K8s verbonden cluster bron.

```powershell
$logAnalyticsWorkspaceResourceId = "<Azure Resource Id of the Log Analytics Workspace>" # format of the Azure Log Analytics workspace should be /subscriptions/<subId>/resourcegroups/<rgName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>
New-AzRoleAssignment -RoleDefinitionName 'Log Analytics Contributor'  -ObjectId $servicePrincipal.Id -Scope  $logAnalyticsWorkspaceResourceId

$servicePrincipalClientId =  $servicePrincipal.ApplicationId.ToString()
$servicePrincipalClientSecret = [System.Net.NetworkCredential]::new("", $servicePrincipal.Secret).Password
$tenantId = (Get-AzSubscription -SubscriptionId $subscriptionId).TenantId
```

Bijvoorbeeld:

```powershell
.\enable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -servicePrincipalClientId $servicePrincipalClientId -servicePrincipalClientSecret $servicePrincipalClientSecret -tenantId $tenantId -kubeContext $kubeContext -workspaceResourceId $logAnalyticsWorkspaceResourceId -proxyEndpoint $proxyEndpoint
```



## <a name="enable-using-bash-script"></a>Inschakelen met behulp van bash-script

Voer de volgende stappen uit om de bewaking in te scha kelen met behulp van het meegeleverde bash-script.

1. Down load het script en sla het op in een lokale map die uw cluster configureert met de invoeg toepassing voor bewaking met behulp van de volgende opdrachten:

    ```bash
    curl -o enable-monitoring.sh -L https://aka.ms/enable-monitoring-bash-script
    ```

2. Configureer de `azureArcClusterResourceId` variabele door de bijbehorende waarden in te stellen voor `subscriptionId` `resourceGroupName` en `clusterName` de resource-id van uw Azure Arc-Kubernetes-cluster resource weer te geven.

    ```bash
    export azureArcClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. Configureer de `kubeContext` variabele met de **uitvoeren-context** van uw cluster door de opdracht uit te voeren `kubectl config get-contexts` . Als u de huidige context wilt gebruiken, stelt u de waarde in op `""` .

    ```bash
    export kubeContext="<kubeContext name of your k8s cluster>"
    ```

4. Als u bestaande Azure Monitor Log Analytics werk ruimte wilt gebruiken, configureert u de variabele `logAnalyticsWorkspaceResourceId` met de bijbehorende waarde voor de resource-id van de werk ruimte. Als dat niet het geval is, stelt u de variabele in op `""` en het script maakt een standaardwerk ruimte in de standaard resource groep van het cluster abonnement als deze nog niet in de regio bestaat. De standaardwerk ruimte die wordt gemaakt, lijkt op de indeling van *DefaultWorkspace- \<SubscriptionID> - \<Region>*.

    ```bash
    export logAnalyticsWorkspaceResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.operationalinsights/workspaces/<workspaceName>"
    ```

5. Als uw met Arc ingeschakeld Kubernetes-cluster communiceert via een proxy server, configureert u de variabele `proxyEndpoint` met de URL van de proxy server. Als het cluster niet via een proxy server communiceert, kunt u de waarde instellen op `""` . Zie [Configure proxy endpoint](#configure-proxy-endpoint) verderop in dit artikel voor meer informatie.

6. Als u bewaking op uw cluster wilt inschakelen, zijn er verschillende opdrachten beschikbaar op basis van uw implementatie scenario.

    Voer de volgende opdracht uit om bewaking met standaard opties in te scha kelen, zoals het gebruik van de huidige uitvoeren-context, het maken van een standaard Log Analytics werkruimte en zonder een proxy server op te geven:

    ```bash
    bash enable-monitoring.sh --resource-id $azureArcClusterResourceId
    ```

    Voer de volgende opdracht uit om een standaard Log Analytics-werk ruimte te maken en zonder een proxy server op te geven:

    ```bash
   bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext
    ```

    Voer de volgende opdracht uit om een bestaande Log Analytics-werk ruimte te gebruiken en zonder een proxy server op te geven:

    ```bash
    bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext  --workspace-id $logAnalyticsWorkspaceResourceId
    ```

    Voer de volgende opdracht uit om een bestaande Log Analytics-werk ruimte te gebruiken en een proxy server op te geven:

    ```bash
    bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext  --workspace-id $logAnalyticsWorkspaceResourceId --proxy $proxyEndpoint
    ```

Nadat u bewaking hebt ingeschakeld, kan het ongeveer 15 minuten duren voordat u de metrische gegevens van de status voor het cluster kunt weer geven.

### <a name="using-service-principal"></a>Service-Principal gebruiken
De bash script *Enable-monitoring.sh* maakt gebruik van de aanmelding van het interactieve apparaat. Als u de voor keur geeft aan niet-interactieve aanmelding, kunt u een bestaande Service-Principal gebruiken of een nieuwe maken die de vereiste machtigingen heeft, zoals wordt beschreven in [vereisten](#prerequisites). Als u de Service-Principal wilt gebruiken, moet u de waarden voor client-id,--client-Secret en--Tenant-id van de service-principal die u wilt gebruiken voor het *Enable-monitoring.sh* bash-script door geven.

```bash
subscriptionId="<subscription Id of the Azure Arc connected cluster resource>"
servicePrincipal=$(az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${subscriptionId}")
servicePrincipalClientId=$(echo $servicePrincipal | jq -r '.appId')
```

De roltoewijzing hieronder is alleen van toepassing als u een bestaande Log Analytics-werk ruimte gebruikt in een ander Azure-abonnement dan de met Arc K8s verbonden cluster bron.

```bash
logAnalyticsWorkspaceResourceId="<Azure Resource Id of the Log Analytics Workspace>" # format of the Azure Log Analytics workspace should be /subscriptions/<subId>/resourcegroups/<rgName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>
az role assignment create --role 'Log Analytics Contributor' --assignee $servicePrincipalClientId --scope $logAnalyticsWorkspaceResourceId

servicePrincipalClientSecret=$(echo $servicePrincipal | jq -r '.password')
tenantId=$(echo $servicePrincipal | jq -r '.tenant')
```

Bijvoorbeeld:

```bash
bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --client-id $servicePrincipalClientId --client-secret $servicePrincipalClientSecret  --tenant-id $tenantId --kube-context $kubeContext  --workspace-id $logAnalyticsWorkspaceResourceId --proxy $proxyEndpoint
```

## <a name="configure-proxy-endpoint"></a>Proxy-eind punt configureren

Met de container agent voor Azure Monitor voor containers kunt u een proxy-eind punt configureren zodat het kan communiceren via uw proxy server. De communicatie tussen de container agent en Azure Monitor kan een HTTP-of HTTPS-proxy server zijn, en zowel anonieme als basis verificatie (gebruikers naam en wacht woord) worden ondersteund.

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

Bijvoorbeeld: `http://user01:password@proxy01.contoso.com:3128`

Als u het protocol als **http** opgeeft, worden de HTTP-aanvragen gemaakt met behulp van een beveiligde SSL/TLS-verbinding. Uw proxy server moet SSL/TLS-protocollen ondersteunen.

### <a name="configure-using-powershell"></a>Configureren met behulp van PowerShell

Geef de gebruikers naam en het wacht woord, het IP-adres of de FQDN en het poort nummer van de proxy server op. Bijvoorbeeld:

```powershell
$proxyEndpoint = https://<user>:<password>@<proxyhost>:<port>
```

### <a name="configure-using-bash"></a>Configureren met behulp van bash

Geef de gebruikers naam en het wacht woord, het IP-adres of de FQDN en het poort nummer van de proxy server op. Bijvoorbeeld:

```bash
export proxyEndpoint=https://<user>:<password>@<proxyhost>:<port>
```

## <a name="next-steps"></a>Volgende stappen

- Als bewaking is ingeschakeld voor het verzamelen van het status-en resource gebruik van uw Kubernetes-cluster met Arc-functionaliteit en workloads die hierop worden uitgevoerd, leert [u hoe u Azure monitor gebruikt](container-insights-analyze.md) voor containers.

- De container agent verzamelt standaard de container/stderr-logboeken van alle containers die worden uitgevoerd in alle naam ruimten, met uitzonde ring van uitvoeren-System. Als u de container logboek verzameling specifiek wilt configureren voor bepaalde naam ruimten of naam ruimten, bekijkt u de configuratie van de [container Insights-agent](container-insights-agent-config.md) om de gewenste instellingen voor het verzamelen van gegevens te configureren voor uw ConfigMap-configuratie bestand.

- Als u Prometheus-metrische gegevens uit uw cluster wilt opwaarderen en analyseren, raadpleegt u [Prometheus metrische gegevens](container-insights-prometheus-integration.md) weer geven

- Zie [How to Stop Monitoring your Hybrid cluster](container-insights-optout-hybrid.md#how-to-stop-monitoring-on-arc-enabled-kubernetes)(Engelstalig) voor meer informatie over het stoppen van het bewaken van uw Kubernetes-cluster met Azure monitor voor containers.