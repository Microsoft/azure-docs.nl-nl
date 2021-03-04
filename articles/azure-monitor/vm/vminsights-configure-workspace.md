---
title: Log Analytics werkruimte configureren voor VM Insights
description: Hierin wordt beschreven hoe u de Log Analytics werkruimte maakt en configureert die wordt gebruikt door VM Insights.
ms.topic: conceptual
ms.custom: references_regions
author: bwren
ms.author: bwren
ms.date: 12/22/2020
ms.openlocfilehash: 5a0e04772cfc1c9be77c0ad8b32b0e93be8b9f54
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102046717"
---
# <a name="configure-log-analytics-workspace-for-vm-insights"></a>Log Analytics werkruimte configureren voor VM Insights
Met VM Insights worden gegevens verzameld uit een of meer Log Analytics-werk ruimten in Azure Monitor. Voordat u agents voorbereidt, moet u een werk ruimte maken en configureren. In dit artikel worden de vereisten van de werk ruimte beschreven en voor het configureren van deze voor VM Insights.

## <a name="overview"></a>Overzicht
Een abonnement kan elk wille keurig aantal werk ruimten gebruiken, afhankelijk van uw vereisten. De enige vereiste van de werk ruimte is dat deze zich op een ondersteunde locatie bevindt en geconfigureerd is met de *VMInsights* -oplossing.

Nadat de werk ruimte is geconfigureerd, kunt u een van de beschik bare opties gebruiken om de vereiste agents op de virtuele machine en de virtuele-machine schaalset te installeren en een werk ruimte op te geven waarmee ze hun gegevens kunnen verzenden. Met VM Insights worden gegevens verzameld van elke geconfigureerde werk ruimte in het abonnement.

> [!NOTE]
> Wanneer u VM Insights op één virtuele machine of virtuele-machine schaalset inschakelt met behulp van de Azure Portal, krijgt u de optie om een bestaande werk ruimte te selecteren of een nieuwe te maken. Als dit nog niet is gebeurd, wordt de *VMInsights* -oplossing in deze werk ruimte geïnstalleerd. U kunt deze werk ruimte vervolgens gebruiken voor andere agents.


## <a name="create-log-analytics-workspace"></a>Een Log Analytics-werkruimte maken

>[!NOTE]
>De gegevens die in deze sectie worden beschreven, zijn ook van toepassing op de [servicetoewijzing oplossing](service-map.md). 

Open Log Analytics-werk ruimten in de Azure Portal in het menu **log Analytics-werk ruimten** .

[![Anlytics-werk ruimten registreren](media/vminsights-configure-workspace/log-analytics-workspaces.png)](media/vminsights-configure-workspace/log-analytics-workspaces.png#lightbox)

U kunt een nieuwe Log Analytics-werk ruimte maken met behulp van een van de volgende methoden. Zie de [implementatie van uw Azure monitor-logboeken ontwerpen](../logs/design-logs-deployment.md) voor hulp bij het bepalen van het aantal werk ruimten dat u in uw omgeving moet gebruiken en hoe u de toegangs strategie kunt ontwerpen.


* [Azure-portal](../logs/quick-create-workspace.md)
* [Azure-CLI](../logs/quick-create-workspace-cli.md)
* [PowerShell](../logs/powershell-workspace-configuration.md)
* [Azure Resource Manager](../logs/resource-manager-workspace.md)

## <a name="supported-regions"></a>Ondersteunde regio’s
VM Insights ondersteunt een Log Analytics-werk ruimte in een van de [regio's die door log Analytics worden ondersteund](https://azure.microsoft.com/global-infrastructure/services/?products=monitor&regions=all) , met uitzonde ring van het volgende:

- Duitsland - west-centraal
- Korea - centraal

>[!NOTE]
>U kunt virtuele Azure-machines bewaken in elke regio. De Vm's zijn niet beperkt tot de regio's die worden ondersteund door de Log Analytics-werk ruimte.

## <a name="azure-role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer voor Azure
Als u de functies in VM Insights wilt inschakelen en openen, moet u de [rol log Analytics Inzender](../logs/manage-access.md#manage-access-using-azure-permissions) hebben in de werk ruimte. Als u de prestaties, de status en de kaart gegevens wilt bekijken, moet u de [rol bewakings lezer](../roles-permissions-security.md#built-in-monitoring-roles) hebben voor de Azure-VM. Zie [werk ruimten beheren](../logs/manage-access.md)voor meer informatie over het controleren van de toegang tot een log Analytics-werk ruimte.

## <a name="add-vminsights-solution-to-workspace"></a>VMInsights-oplossing toevoegen aan werk ruimte
Voordat een Log Analytics-werk ruimte kan worden gebruikt met VM Insights, moet de *VMInsights* -oplossing zijn geïnstalleerd. De methoden voor het configureren van de werk ruimte worden beschreven in de volgende secties.

> [!NOTE]
> Wanneer u de *VMInsights* -oplossing aan de werk ruimte toevoegt, worden alle bestaande virtuele machines die zijn verbonden met de werk ruimte, verzonden naar InsightsMetrics. Gegevens voor de andere gegevens typen worden pas verzameld wanneer u de Dependency Agent toevoegt aan de bestaande virtuele machines die zijn verbonden met de werk ruimte.

### <a name="azure-portal"></a>Azure Portal
Er zijn drie opties voor het configureren van een bestaande werk ruimte met behulp van de Azure Portal. Deze worden hieronder beschreven.

Als u één werk ruimte wilt configureren, gaat u naar de optie **virtual machines** in het menu **Azure monitor** , selecteert u de **andere opties voor onboarding** en **configureert u vervolgens een werk ruimte**. Selecteer een abonnement en een werk ruimte en klik vervolgens op **configureren**.

[![Werkruimte configureren](../vm/media/vminsights-enable-policy/configure-workspace.png)](../vm/media/vminsights-enable-policy/configure-workspace.png#lightbox)

Als u meerdere werk ruimten wilt configureren, selecteert u het tabblad **werkruimte configuratie** in het menu **virtual machines** van het menu **monitor** in de Azure Portal. De filter waarden instellen om een lijst met bestaande werk ruimten weer te geven. Schakel het selectie vakje in naast elke werk ruimte die u wilt inschakelen en klik vervolgens op **geselecteerde items configureren**.

[![Werkruimteconfiguratie](../vm/media/vminsights-enable-policy/workspace-configuration.png)](../vm/media/vminsights-enable-policy/workspace-configuration.png#lightbox)


Wanneer u VM Insights op één virtuele machine of virtuele-machine schaalset inschakelt met behulp van de Azure Portal, krijgt u de optie om een bestaande werk ruimte te selecteren of een nieuwe te maken. Als dit nog niet is gebeurd, wordt de *VMInsights* -oplossing in deze werk ruimte geïnstalleerd. U kunt deze werk ruimte vervolgens gebruiken voor andere agents.

[![Eén virtuele machine in de portal inschakelen](../vm/media/vminsights-enable-portal/enable-vminsights-vm-portal.png)](../vm/media/vminsights-enable-portal/enable-vminsights-vm-portal.png#lightbox)


### <a name="resource-manager-template"></a>Resource Manager-sjabloon
De Azure Resource Manager sjablonen voor VM Insights zijn opgenomen in een archief bestand (. zip) dat u kunt [downloaden via onze github-opslag plaats](https://aka.ms/VmInsightsARMTemplates). Dit omvat een sjabloon met de naam **ConfigureWorkspace** die een log Analytics werk ruimte CONFIGUREERT voor VM Insights. U implementeert deze sjabloon met een van de standaard methoden, met inbegrip van de volgende Power shell-en CLI-opdrachten: 

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az deployment group create --name ConfigureWorkspace --resource-group my-resource-group --template-file CreateWorkspace.json  --parameters workspaceResourceId='/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace' workspaceLocation='eastus'

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
New-AzResourceGroupDeployment -Name ConfigureWorkspace -ResourceGroupName my-resource-group -TemplateFile ConfigureWorkspace.json -workspaceResourceId /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace -location eastus
```

---



## <a name="next-steps"></a>Volgende stappen
- Bekijk agents [voor VM Insights](vminsights-enable-overview.md) om agents te verbinden met VM Insights.
- Zie [doel bewakings oplossingen in azure monitor (preview)](../insights/solution-targeting.md) om de hoeveelheid gegevens te beperken die vanuit een oplossing naar de werk ruimte wordt verzonden.
