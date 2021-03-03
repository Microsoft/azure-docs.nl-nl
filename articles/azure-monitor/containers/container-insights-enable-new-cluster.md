---
title: Een nieuw Azure Kubernetes service-cluster (AKS) bewaken | Microsoft Docs
description: Meer informatie over het inschakelen van bewaking voor een nieuw Azure Kubernetes service (AKS)-cluster met een container Insights-abonnement.
ms.topic: conceptual
ms.date: 04/25/2019
ms.custom: devx-track-terraform, devx-track-azurecli
ms.openlocfilehash: 9b6c4f8a05b8e7a350ebd5afd677e8bb2ee6e9b4
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101717567"
---
# <a name="enable-monitoring-of-a-new-azure-kubernetes-service-aks-cluster"></a>Bewaking van een nieuw Azure Kubernetes service (AKS)-cluster inschakelen

In dit artikel wordt beschreven hoe u container Insights instelt voor het bewaken van beheerde Kubernetes-clusters die worden gehost op de [Azure Kubernetes-service](../../aks/index.yml) die u voorbereidt om te worden geïmplementeerd in uw abonnement.

U kunt de bewaking van een AKS-cluster inschakelen met een van de ondersteunde methoden:

* Azure CLI
* Terraform

## <a name="enable-using-azure-cli"></a>Inschakelen met behulp van Azure CLI

Als u de bewaking van een nieuw AKS-cluster dat is gemaakt met Azure CLI wilt inschakelen, volgt u de stap in het artikel Quick Start in het gedeelte [AKS-cluster maken](../../aks/kubernetes-walkthrough.md#create-aks-cluster).  

>[!NOTE]
>Als u ervoor kiest om de Azure CLI te gebruiken, moet u de CLI eerst lokaal installeren en gebruiken. U moet de Azure CLI-versie 2.0.74 of hoger uitvoeren. Voer uit om uw versie te identificeren `az --version` . Als u de Azure CLI wilt installeren of upgraden, raadpleegt u [de Azure cli installeren](/cli/azure/install-azure-cli). Als u de AKS-preview CLI-extensie versie 0.4.12 of hoger hebt geïnstalleerd, verwijdert u alle wijzigingen die u hebt aangebracht om een preview-uitbrei ding in te scha kelen, omdat AKS preview-functies niet beschikbaar zijn in de Cloud voor Amerikaanse Governmnet van Azure.

## <a name="enable-using-terraform"></a>Inschakelen met behulp van terraform

Als u [een nieuw AKS-cluster implementeert met behulp van terraform](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks), geeft u de vereiste argumenten in het profiel [op om een log Analytics-werk ruimte te maken](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_workspace.html) als u geen bestaande hebt opgegeven. 

>[!NOTE]
>Als u ervoor kiest om terraform te gebruiken, moet u de terraform Azure RM-provider versie 1.17.0 of hoger uitvoeren.

Als u container inzichten wilt toevoegen aan de werk ruimte, raadpleegt u [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) en vult u het profiel in door de [**addon_profile**](https://www.terraform.io/docs/providers/azurerm/r/kubernetes_cluster.html#addon_profile) op te nemen en **oms_agent** op te geven. 

Nadat u bewaking hebt ingeschakeld en alle configuratie taken zijn voltooid, kunt u de prestaties van uw cluster op twee manieren controleren:

* Rechtstreeks in het AKS-cluster door de **status** te selecteren in het linkerdeel venster.
* Door de tegel **container Insights bewaken** in de AKS-cluster pagina voor het geselecteerde cluster te selecteren. Selecteer in Azure Monitor in het linkerdeel venster **status**. 

  ![Opties voor het selecteren van container Insights in AKS](./media/container-insights-onboard/kubernetes-select-monitoring-01.png)

Nadat u bewaking hebt ingeschakeld, kan het ongeveer 15 minuten duren voordat u de metrische gegevens van de status voor het cluster kunt weer geven. 

## <a name="verify-agent-and-solution-deployment"></a>Implementatie van agent en oplossing controleren
Met Agent versie *06072018* of hoger kunt u controleren of zowel de agent als de oplossing is geïmplementeerd. Met eerdere versies van de agent kunt u alleen de implementatie van de agent verifiëren.

### <a name="agent-version-06072018-or-later"></a>Agent versie 06072018 of hoger
Voer de volgende opdracht uit om te controleren of de agent is geïmplementeerd. 

```
kubectl get ds omsagent --namespace=kube-system
```

De uitvoer moet er als volgt uitzien, wat aangeeft dat het goed is geïmplementeerd:

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

Voer de volgende opdracht uit om de implementatie van de oplossing te controleren:

```
kubectl get deployment omsagent-rs -n=kube-system
```

De uitvoer moet er als volgt uitzien, wat aangeeft dat het goed is geïmplementeerd:

```
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

```
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

```
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

* Als controle is ingeschakeld voor het verzamelen van het status-en resource gebruik van uw AKS-cluster en werk belastingen die erop worden uitgevoerd, leert [u hoe u container Insights kunt gebruiken](container-insights-analyze.md) .

