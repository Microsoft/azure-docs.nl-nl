---
title: Azure Monitor voor containers bijwerken voor metrische gegevens | Microsoft Docs
description: In dit artikel wordt beschreven hoe u Azure Monitor voor containers bijwerkt om de functie voor aangepaste metrische gegevens in te scha kelen die ondersteuning biedt voor het verkennen en waarschuwen van geaggregeerde metrische gegevens.
ms.topic: conceptual
ms.date: 10/09/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 37c19cd074e9ce1985d5d0e82137d8603913d4bd
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100609892"
---
# <a name="how-to-update-azure-monitor-for-containers-to-enable-metrics"></a>Azure Monitor voor containers bijwerken om metrische gegevens in te schakelen

Azure Monitor voor containers wordt ondersteuning geïntroduceerd voor het verzamelen van metrische gegevens van Azure Kubernetes Services (AKS) en Azure Arc enabled Kubernetes-clusters knoop punten en een Peul en schrijf ze naar de opslag voor de Azure Monitor metrische gegevens. Deze wijziging is bedoeld om een verbeterde tijd lijn te bieden bij het presen teren van geaggregeerde berekeningen (Gem, aantal, Max, min, Sum) in prestatie grafieken, ondersteuning voor het vastmaken van prestatie grafieken in Azure Portal Dash boards en het ondersteunen van metrische waarschuwingen.

>[!NOTE]
>Deze functie biedt momenteel geen ondersteuning voor Azure Red Hat open Shift-clusters.
>

De volgende metrische gegevens zijn ingeschakeld als onderdeel van deze functie:

| Metrische naam ruimte | Metrisch | Beschrijving |
|------------------|--------|-------------|
| Inzichten. container/knoop punten | cpuUsageMillicores, cpuUsagePercentage, memoryRssBytes, memoryRssPercentage, memoryWorkingSetBytes, memoryWorkingSetPercentage, nodesCount, diskUsedPercentage, | De metrische gegevens van *knoop punten* zijn *host* als een dimensie. Ze omvatten ook de<br> de naam van het knoop punt als waarde voor de *host* -dimensie. |
| Inzichten. container/peul | podCount, completedJobsCount, restartingContainerCount, oomKilledContainerCount, podReadyPercentage | Als *pod* -metrische gegevens bevatten ze de volgende dimensies: afmetingen-controller, Kubernetes naam ruimte, naam, fase. |
| Inzichten. container/containers | cpuExceededPercentage, memoryRssExceededPercentage, memoryWorkingSetExceededPercentage | |
| Inzichten. container/persistentvolumes | pvUsageExceededPercentage | |

Ter ondersteuning van deze nieuwe mogelijkheden is een nieuwe container met agents opgenomen in de release, versie **micro soft/OMS: ciprod05262020** for AKS en Version **micro soft/OMS: Ciprod09252020** for Azure Arc enabled Kubernetes clusters. Nieuwe implementaties van AKS bevatten automatisch deze configuratie wijziging en mogelijkheden. Het bijwerken van uw cluster ter ondersteuning van deze functie kan worden uitgevoerd vanuit de Azure Portal, Azure PowerShell of met Azure CLI. Met Azure PowerShell en CLI. U kunt dit per cluster of voor alle clusters in uw abonnement inschakelen.

In beide gevallen wordt de rol **bewakings metrieken** voor de uitgever toegewezen aan de service-principal van het cluster of de door de gebruiker toegewezen MSI voor de invoeg toepassing voor bewaking, zodat de gegevens die door de agent worden verzameld, kunnen worden gepubliceerd naar de cluster bron. Bewaking van metrische gegevens van de uitgever heeft alleen toestemming voor het pushen van metrische gegevens naar de resource, het kan geen status wijzigen, de resource bijwerken of gegevens lezen. Zie voor meer informatie over de rol [bewaking metrische gegevens van uitgever](../../role-based-access-control/built-in-roles.md#monitoring-metrics-publisher). De rol vereiste voor de uitgever van de bewakings gegevens is niet van toepassing op Kubernetes-clusters met Azure Arc-functionaliteit.

> [!IMPORTANT]
> De upgrade is niet vereist voor Azure Arc enabled Kubernetes-clusters, omdat deze al beschikken over de mini maal vereiste agent versie.

## <a name="prerequisites"></a>Vereisten

Controleer het volgende voordat u het cluster bijwerkt:

* Aangepaste metrische gegevens zijn alleen beschikbaar in een subset van Azure-regio's. [Hier](../essentials/metrics-custom-overview.md#supported-regions)wordt een lijst met ondersteunde regio's beschreven.

* U bent lid van de rol **[eigenaar](../../role-based-access-control/built-in-roles.md#owner)** op de AKS-cluster bron om het verzamelen van aangepaste prestatie gegevens voor knoop punten en pod in te scha kelen. Deze vereiste is niet van toepassing op Azure Arc enabled Kubernetes-clusters.

Als u ervoor kiest om de Azure CLI te gebruiken, moet u de CLI eerst lokaal installeren en gebruiken. U moet de Azure CLI-versie 2.0.59 of hoger uitvoeren. Voer uit om uw versie te identificeren `az --version` . Als u de Azure CLI wilt installeren of upgraden, raadpleegt u [de Azure cli installeren](/cli/azure/install-azure-cli).

## <a name="upgrade-a-cluster-from-the-azure-portal"></a>Een cluster upgraden van de Azure Portal

Voor bestaande AKS-clusters die worden bewaakt door Azure Monitor voor containers, na het selecteren van het cluster om de status weer te geven van de weer gave met meerdere clusters in Azure Monitor of rechtstreeks vanuit het cluster door **inzichten** te selecteren in het linkerdeel venster, ziet u boven aan de portal een banner.

![Span doek voor AKS-cluster bijwerken in Azure Portal](./media/container-insights-update-metrics/portal-banner-enable-01.png)

Als u op **inschakelen** klikt, wordt het proces voor het upgraden van het cluster gestart. Het kan enkele seconden duren voordat dit proces is voltooid en u kunt de voortgang bijhouden onder meldingen in het menu.

## <a name="upgrade-all-clusters-using-bash-in-azure-command-shell"></a>Alle clusters bijwerken met behulp van bash in azure-opdracht shell

Voer de volgende stappen uit om alle clusters in uw abonnement bij te werken met behulp van bash in azure-opdracht shell.

1. Voer de volgende opdracht uit met behulp van de Azure CLI.  Bewerk de waarde voor **subscriptionId** met behulp van de waarde op de pagina **overzicht van AKS** voor het AKS-cluster.

    ```azurecli
    az login
    az account set --subscription "Subscription Name"
    curl -sL https://aka.ms/ci-md-onboard-atscale | bash -s subscriptionId   
    ```

    Het kan een paar seconden duren voordat de configuratie wijziging is voltooid. Wanneer de service is voltooid, wordt een bericht weer gegeven dat er ongeveer als volgt uitziet en het resultaat bevat:

    ```azurecli
    completed role assignments for all AKS clusters in subscription: <subscriptionId>
    ```

## <a name="upgrade-per-cluster-using-azure-cli"></a>Upgrade per cluster met behulp van Azure CLI

Voer de volgende stappen uit om een specifiek cluster in uw abonnement bij te werken met Azure CLI.

1. Voer de volgende opdracht uit met behulp van de Azure CLI. Bewerk de waarden voor **subscriptionId**, **resourceGroupName** en **clustername** met de waarden op de **OVERZICHTs** pagina van AKS voor het AKS-cluster.  Om de waarde van **clientIdOfSPN** op te halen, wordt deze geretourneerd wanneer u de opdracht uitvoert, `az aks show` zoals wordt weer gegeven in het onderstaande voor beeld.

    ```azurecli
    az login
    az account set --subscription "<subscriptionName>"
    az aks show -g <resourceGroupName> -n <clusterName> 
    az role assignment create --assignee <clientIdOfSPN> --scope <clusterResourceId> --role "Monitoring Metrics Publisher" 
    ```

    Als u de waarde voor **clientIdOfSPNOrMsi** wilt ophalen, kunt u de opdracht uitvoeren `az aks show` zoals wordt weer gegeven in het onderstaande voor beeld. Als het **servicePrincipalProfile** -object een geldige *ClientID* -waarde heeft, kunt u die gebruiken. Anders, als deze is ingesteld op *MSI*, moet u de ClientID door geven van `addonProfiles.omsagent.identity.clientId` .

    ```azurecli
    az login
    az account set --subscription "<subscriptionName>"
    az aks show -g <resourceGroupName> -n <clusterName> 
    az role assignment create --assignee <clientIdOfSPNOrMsi> --scope <clusterResourceId> --role "Monitoring Metrics Publisher"
    ```

## <a name="upgrade-all-clusters-using-azure-powershell"></a>Alle clusters bijwerken met behulp van Azure PowerShell

Voer de volgende stappen uit om alle clusters in uw abonnement bij te werken met Azure PowerShell.

1. [Down load](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/docs/aks/mdmonboarding/mdm_onboarding_atscale.ps1) het **mdm_onboarding_atscale.ps1** script en sla het op in een lokale map vanuit onze github opslag plaats.
2. Voer de volgende opdracht uit met behulp van de Azure PowerShell.  Bewerk de waarde voor **subscriptionId** met behulp van de waarde op de pagina **overzicht van AKS** voor het AKS-cluster.

    ```powershell
    .\mdm_onboarding_atscale.ps1 subscriptionId
    ```
    Het kan een paar seconden duren voordat de configuratie wijziging is voltooid. Wanneer de service is voltooid, wordt een bericht weer gegeven dat er ongeveer als volgt uitziet en het resultaat bevat:

    ```powershell
    Completed adding role assignment for the aks clusters in subscriptionId :<subscriptionId>
    ```

## <a name="upgrade-per-cluster-using-azure-powershell"></a>Upgrade per cluster met behulp van Azure PowerShell

Voer de volgende stappen uit om een specifiek cluster bij te werken met Azure PowerShell.

1. [Down load](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/docs/aks/mdmonboarding/mdm_onboarding.ps1) het **mdm_onboarding.ps1** script en sla het op in een lokale map vanuit onze github opslag plaats.

2. Voer de volgende opdracht uit met behulp van de Azure PowerShell. Bewerk de waarden voor **subscriptionId**, **resourceGroupName** en **clustername** met de waarden op de **OVERZICHTs** pagina van AKS voor het AKS-cluster.

    ```powershell
    .\mdm_onboarding.ps1 subscriptionId <subscriptionId> resourceGroupName <resourceGroupName> clusterName <clusterName>
    ```

    Het kan een paar seconden duren voordat de configuratie wijziging is voltooid. Wanneer de service is voltooid, wordt een bericht weer gegeven dat er ongeveer als volgt uitziet en het resultaat bevat:

    ```powershell
    Successfully added Monitoring Metrics Publisher role assignment to cluster : <clusterName>
    ```

## <a name="verify-update"></a>Update controleren

Nadat u de update hebt gestart met een van de methoden die eerder zijn beschreven, kunt u Azure Monitor Metrics Explorer gebruiken en controleren of de **metrische naam ruimte** die **inzichten** bevat wordt weer gegeven. Als dat het geval is, kunt u door gaan met het instellen van [metrische waarschuwingen](../alerts/alerts-metric.md) of het vastmaken van uw grafieken aan [Dash boards](../../azure-portal/azure-portal-dashboards.md).  
