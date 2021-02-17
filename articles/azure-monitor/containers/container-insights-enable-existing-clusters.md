---
title: Een AKS-cluster (Azure Kubernetes service) bewaken dat is geïmplementeerd | Microsoft Docs
description: Meer informatie over het inschakelen van bewaking van een Azure Kubernetes service-cluster (AKS) met Azure Monitor voor containers die al zijn geïmplementeerd in uw abonnement.
ms.topic: conceptual
ms.date: 09/12/2019
ms.custom: devx-track-terraform, devx-track-azurecli
ms.openlocfilehash: 547c22e4d82aa728009a2fdb42f2c3b481b7a625
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100612348"
---
# <a name="enable-monitoring-of-azure-kubernetes-service-aks-cluster-already-deployed"></a>Bewaking van het cluster van Azure Kubernetes service (AKS) inschakelen dat al is geïmplementeerd

In dit artikel wordt beschreven hoe u Azure Monitor instelt voor containers voor het bewaken van beheerde Kubernetes-clusters die worden gehost op de [Azure Kubernetes-service](../../aks/index.yml) die al is geïmplementeerd in uw abonnement.

U kunt de bewaking van een AKS-cluster inschakelen dat al is geïmplementeerd met een van de ondersteunde methoden:

* Azure CLI
* Terraform
* [Vanuit Azure monitor](#enable-from-azure-monitor-in-the-portal) of [rechtstreeks vanuit het AKS-cluster](#enable-directly-from-aks-cluster-in-the-portal) in de Azure Portal
* Met de [meegeleverde Azure Resource Manager sjabloon](#enable-using-an-azure-resource-manager-template) met behulp van de Azure PowerShell cmdlet `New-AzResourceGroupDeployment` of met Azure cli.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="enable-using-azure-cli"></a>Inschakelen met behulp van Azure CLI

Met de volgende stap wordt het controleren van uw AKS-cluster ingeschakeld met behulp van Azure CLI. In dit voor beeld bent u niet verplicht een bestaande werk ruimte vooraf te maken of op te geven. Met deze opdracht wordt het proces voor u vereenvoudigd door een standaard werk ruimte te maken in de standaard resource groep van het AKS-cluster abonnement als er nog geen bestaat in de regio.  De standaardwerk ruimte die wordt gemaakt, lijkt op de indeling van *DefaultWorkspace- \<GUID> - \<Region>*.

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG
```

De uitvoer ziet er ongeveer als volgt uit:

```output
provisioningState       : Succeeded
```

### <a name="integrate-with-an-existing-workspace"></a>Integreren met een bestaande werk ruimte

Als u liever met een bestaande werk ruimte integreert, voert u de volgende stappen uit om eerst de volledige Resource-ID te identificeren van uw Log Analytics werk ruimte die voor de `--workspace-resource-id` para meter is vereist en voert u vervolgens de opdracht uit om de invoeg toepassing bewaking voor de opgegeven werk ruimte in te scha kelen.

1. Een lijst met alle abonnementen waartoe u toegang hebt met behulp van de volgende opdracht:

    ```azurecli
    az account list --all -o table
    ```

    De uitvoer ziet er ongeveer als volgt uit:

    ```output
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   68627f8c-91fO-4905-z48q-b032a81f8vy0  Enabled  True
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

4. Voer de volgende opdracht uit om de invoeg toepassing bewaking in te scha kelen, waarbij u de waarde voor de `--workspace-resource-id` para meter vervangt. De teken reeks waarde moet binnen de dubbele aanhalings tekens staan:

    ```azurecli
    az aks enable-addons -a monitoring -n ExistingManagedCluster -g ExistingManagedClusterRG --workspace-resource-id "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>"
    ```

    De uitvoer ziet er ongeveer als volgt uit:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="enable-using-terraform"></a>Inschakelen met behulp van terraform

1. Voeg het profiel voor de **oms_agent** -invoeg toepassing toe aan de bestaande [azurerm_kubernetes_cluster resource](https://www.terraform.io/docs/providers/azurerm/d/kubernetes_cluster.html#addon_profile)

   ```
   addon_profile {
    oms_agent {
      enabled                    = true
      log_analytics_workspace_id = "${azurerm_log_analytics_workspace.test.id}"
     }
   }
   ```

2. Voeg de [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) toe volgens de stappen in de terraform-documentatie.

## <a name="enable-from-azure-monitor-in-the-portal"></a>Inschakelen van Azure Monitor in de portal

Ga als volgt te werk om de bewaking van uw AKS-cluster in de Azure Portal van Azure Monitor in te scha kelen:

1. Selecteer in de Azure Portal **monitor**.

2. Selecteer **containers** in de lijst.

3. Selecteer op de pagina **monitor-containers** de optie niet- **bewaakte clusters**.

4. Zoek in de lijst met niet-bewaakte clusters de container in de lijst en klik op **inschakelen**.

5. Als u een bestaande Log Analytics-werk ruimte hebt in hetzelfde abonnement als het cluster, selecteert u in de vervolg keuzelijst de pagina **onboarding to Azure monitor voor containers** .
    De lijst preselecteert de standaard werkruimte en de locatie waar de AKS-container in het abonnement is geïmplementeerd.

    ![AKS container Insights-bewaking inschakelen](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Als u een nieuwe Log Analytics-werk ruimte wilt maken voor het opslaan van de bewakings gegevens uit het cluster, volgt u de instructies in [een log Analytics-werk ruimte maken](../learn/quick-create-workspace.md). Zorg ervoor dat u de werk ruimte maakt in hetzelfde abonnement waarin de AKS-container is geïmplementeerd.

Nadat u bewaking hebt ingeschakeld, kan het ongeveer 15 minuten duren voordat u de metrische gegevens van de status voor het cluster kunt weer geven.

## <a name="enable-directly-from-aks-cluster-in-the-portal"></a>Rechtstreeks inschakelen vanuit het AKS-cluster in de portal

Ga als volgt te werk om bewaking rechtstreeks vanuit een van uw AKS-clusters in de Azure Portal in te scha kelen:

1. Selecteer in de Azure-portal de optie **Alle services**.

2. Begin met het typen van **containers** in de lijst met resources.  De lijst filters op basis van uw invoer.

3. Selecteer **Kubernetes Services**.
    
4. Selecteer in de lijst met Kubernetes Services een service.

5. Selecteer op de pagina overzicht van Kubernetes-service de optie **bewaking-Insights**.

6. Als u een bestaande Log Analytics-werk ruimte hebt in hetzelfde abonnement als het cluster, selecteert u in de vervolg keuzelijst de pagina **onboarding to Azure monitor voor containers** .
    De lijst preselecteert de standaard werkruimte en de locatie waar de AKS-container in het abonnement is geïmplementeerd.

    ![Status controle van AKS-container inschakelen](./media/container-insights-onboard/kubernetes-onboard-brownfield-02.png)

    >[!NOTE]
    >Als u een nieuwe Log Analytics-werk ruimte wilt maken voor het opslaan van de bewakings gegevens uit het cluster, volgt u de instructies in [een log Analytics-werk ruimte maken](../learn/quick-create-workspace.md). Zorg ervoor dat u de werk ruimte maakt in hetzelfde abonnement waarin de AKS-container is geïmplementeerd.

Nadat u bewaking hebt ingeschakeld, kan het ongeveer 15 minuten duren voordat u de operationele gegevens voor het cluster kunt weer geven.

## <a name="enable-using-an-azure-resource-manager-template"></a>Inschakelen met behulp van een Azure Resource Manager sjabloon

Deze methode bevat twee JSON-sjablonen. Met één sjabloon geeft u de configuratie op om bewaking in te scha kelen. de andere bevat parameter waarden die u configureert om het volgende op te geven:

* De resource-ID van de AKS-container.
* De resource groep waarin het cluster is geïmplementeerd.

>[!NOTE]
>De sjabloon moet worden geïmplementeerd in dezelfde resource groep als het cluster.
>

De Log Analytics-werk ruimte moet worden gemaakt voordat u bewaking met behulp van Azure PowerShell of CLI inschakelt. Als u de werk ruimte wilt maken, kunt u deze instellen via [Azure Resource Manager](../samples/resource-manager-workspace.md), via [Power shell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)of in de [Azure Portal](../learn/quick-create-workspace.md).

Als u niet bekend bent met het concept van het implementeren van resources met behulp van een sjabloon, raadpleegt u:

* [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

* [Resources implementeren met Resource Manager-sjablonen en Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Als u ervoor kiest om de Azure CLI te gebruiken, moet u de CLI eerst lokaal installeren en gebruiken. U moet de Azure CLI-versie 2.0.59 of hoger uitvoeren. Voer uit om uw versie te identificeren `az --version` . Als u de Azure CLI wilt installeren of upgraden, raadpleegt u [de Azure cli installeren](/cli/azure/install-azure-cli).

### <a name="create-and-execute-a-template"></a>Een sjabloon maken en uitvoeren

1. Kopieer en plak de volgende JSON-syntaxis in het bestand:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
          "type": "string",
          "metadata": {
            "description": "AKS Cluster Resource ID"
          }
        },
        "aksResourceLocation": {
          "type": "string",
          "metadata": {
            "description": "Location of the AKS resource e.g. \"East US\""
          }
        },
        "aksResourceTagValues": {
          "type": "object",
          "metadata": {
            "description": "Existing all tags on AKS Cluster Resource"
          }
        },
        "workspaceResourceId": {
          "type": "string",
          "metadata": {
            "description": "Azure Monitor Log Analytics Resource ID"
          }
        }
      },
      "resources": [
        {
          "name": "[split(parameters('aksResourceId'),'/')[8]]",
          "type": "Microsoft.ContainerService/managedClusters",
          "location": "[parameters('aksResourceLocation')]",
          "tags": "[parameters('aksResourceTagValues')]",
          "apiVersion": "2018-03-31",
          "properties": {
            "mode": "Incremental",
            "id": "[parameters('aksResourceId')]",
            "addonProfiles": {
              "omsagent": {
                "enabled": true,
                "config": {
                  "logAnalyticsWorkspaceResourceID": "[parameters('workspaceResourceId')]"
                }
              }
            }
          }
        }
      ]
    }
    ```

2. Sla dit bestand als **existingClusterOnboarding.jsop in** een lokale map.

3. Plak de volgende JSON-syntaxis in het bestand:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
          "value": "/subscriptions/<SubscriptionId>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
        },
        "aksResourceLocation": {
          "value": "<aksClusterLocation>"
        },
        "workspaceResourceId": {
          "value": "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>"
        },
        "aksResourceTagValues": {
          "value": {
            "<existing-tag-name1>": "<existing-tag-value1>",
            "<existing-tag-name2>": "<existing-tag-value2>",
            "<existing-tag-nameN>": "<existing-tag-valueN>"
          }
        }
      }
    }
    ```

4. Bewerk de waarden voor **aksResourceId** en **aksResourceLocation** met behulp van de waarden op de **overzichts** pagina van AKS voor het AKS-cluster. De waarde voor **workspaceResourceId** is de volledige resource-id van uw log Analytics-werk ruimte, met inbegrip van de naam van de werk ruimte.

    Bewerk de waarden voor **aksResourceTagValues** zodat deze overeenkomen met de bestaande label waarden die zijn opgegeven voor het AKS-cluster.

5. Sla dit bestand als **existingClusterParam.jsop in** een lokale map.

6. U kunt deze sjabloon nu implementeren.

   * Als u wilt implementeren met Azure PowerShell, gebruikt u de volgende opdrachten in de map met de sjabloon:

       ```powershell
       New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
       ```

       Het kan enkele minuten duren voordat de configuratie is gewijzigd. Wanneer de service is voltooid, wordt een bericht weer gegeven dat er ongeveer als volgt uitziet en het resultaat bevat:

       ```output
       provisioningState       : Succeeded
       ```

   * Als u wilt implementeren met Azure CLI, voert u de volgende opdrachten uit:

       ```azurecli
       az login
       az account set --subscription "Subscription Name"
       az deployment group create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
       ```

       Het kan enkele minuten duren voordat de configuratie is gewijzigd. Wanneer de service is voltooid, wordt een bericht weer gegeven dat er ongeveer als volgt uitziet en het resultaat bevat:

       ```output
       provisioningState       : Succeeded
       ```

       Nadat u bewaking hebt ingeschakeld, kan het ongeveer 15 minuten duren voordat u de metrische gegevens van de status voor het cluster kunt weer geven.

## <a name="verify-agent-and-solution-deployment"></a>Implementatie van agent en oplossing controleren

Met Agent versie *06072018* of hoger kunt u controleren of zowel de agent als de oplossing is geïmplementeerd. Met eerdere versies van de agent kunt u alleen de implementatie van de agent verifiëren.

### <a name="agent-version-06072018-or-later"></a>Agent versie 06072018 of hoger

Voer de volgende opdracht uit om te controleren of de agent is geïmplementeerd.

```
kubectl get ds omsagent --namespace=kube-system
```

De uitvoer moet er als volgt uitzien, wat aangeeft dat het goed is geïmplementeerd:

```output
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```

Als er Windows Server-knoop punten in het cluster zijn, kunt u de volgende opdracht uitvoeren om te controleren of de agent met succes is geïmplementeerd.

```
kubectl get ds omsagent-win --namespace=kube-system
```

De uitvoer moet er als volgt uitzien, wat aangeeft dat het goed is geïmplementeerd:

```output
User@aksuser:~$ kubectl get ds omsagent-win --namespace=kube-system
NAME                   DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                   AGE
omsagent-win           2         2         2         2            2           beta.kubernetes.io/os=windows   1d
```

Voer de volgende opdracht uit om de implementatie van de oplossing te controleren:

```
kubectl get deployment omsagent-rs -n=kube-system
```

De uitvoer moet er als volgt uitzien, wat aangeeft dat het goed is geïmplementeerd:

```output
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>Agent versie ouder dan 06072018

Voer de volgende opdracht uit om te controleren of de versie van de Log Analytics agent die is uitgebracht vóór *06072018* correct is geïmplementeerd:

```
kubectl get ds omsagent --namespace=kube-system
```

De uitvoer moet er als volgt uitzien, wat aangeeft dat het goed is geïmplementeerd:

```output
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```

## <a name="view-configuration-with-cli"></a>Configuratie weer geven met CLI

Gebruik de `aks show` opdracht om details weer te geven, zoals de oplossing die is ingeschakeld of niet, wat is de log Analytics werk ruimte resourceID en samenvattings gegevens over het cluster.

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

Na enkele minuten is de opdracht voltooid en retourneert deze informatie over de JSON-indeling over de oplossing.  De resultaten van de opdracht moeten het profiel voor het toevoegen van bewaking weer geven en lijken op de volgende voorbeeld uitvoer:

```output
"addonProfiles": {
    "omsagent": {
      "config": {
        "logAnalyticsWorkspaceResourceID": "/subscriptions/<WorkspaceSubscription>/resourceGroups/<DefaultWorkspaceRG>/providers/Microsoft.OperationalInsights/workspaces/<defaultWorkspaceName>"
      },
      "enabled": true
    }
  }
```

## <a name="next-steps"></a>Volgende stappen

* Als u problemen ondervindt bij het voorbereiden van de oplossing, raadpleegt u de [hand leiding](container-insights-troubleshoot.md) voor het oplossen van problemen

* Als controle is ingeschakeld voor het verzamelen van het status-en resource gebruik van uw AKS-cluster en werk belastingen die erop worden uitgevoerd, leert [u hoe u Azure monitor gebruikt](container-insights-analyze.md) voor containers.
