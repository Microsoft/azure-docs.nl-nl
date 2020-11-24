---
title: Log Analytics-werkruimte voor Azure Monitor voor VM's configureren
description: Hierin wordt beschreven hoe u de Log Analytics werkruimte maakt en configureert die wordt gebruikt door Azure Monitor voor VM's.
ms.subservice: ''
ms.topic: conceptual
ms.custom: references_regions
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: a4380ae7fdb523f8b2f5bc14fefa094b6d96e547
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95750523"
---
# <a name="configure-log-analytics-workspace-for-azure-monitor-for-vms"></a>Log Analytics-werkruimte voor Azure Monitor voor VM's configureren
Azure Monitor voor VM's verzamelt gegevens uit een of meer Log Analytics-werk ruimten in Azure Monitor. Voordat u agents voorbereidt, moet u een werk ruimte maken en configureren. In dit artikel worden de vereisten van de werk ruimte beschreven en om deze voor Azure Monitor voor VM's te configureren.

## <a name="overview"></a>Overzicht
Een abonnement kan gebruikmaken van een wille keurig aantal werk ruimten, afhankelijk van uw vereisten, is de enige vereiste van de werk ruimte dat deze zich op een ondersteunde locatie bevindt en wordt geconfigureerd met de *VMInsights* -oplossing.

Nadat de werk ruimte is geconfigureerd, kunt u een van de beschik bare opties gebruiken om de vereiste agents te installeren op de VM en VMSS en een werk ruimte op te geven waarmee ze hun gegevens kunnen verzenden. Azure Monitor voor VM's verzamelt gegevens van elke geconfigureerde werk ruimte in het abonnement.

> [!NOTE]
> Wanneer u Azure Monitor voor VM's op één virtuele machine of VMSS inschakelt met behulp van de Azure Portal, krijgt u de mogelijkheid om een bestaande werk ruimte te selecteren of een nieuwe te maken. Als dit nog niet is gebeurd, wordt de *VMInsights* -oplossing in deze werk ruimte geïnstalleerd. U kunt deze werk ruimte vervolgens gebruiken voor andere agents.


## <a name="create-log-analytics-workspace"></a>Een Log Analytics-werkruimte maken

>[!NOTE]
>De gegevens die in deze sectie worden beschreven, zijn ook van toepassing op de [servicetoewijzing oplossing](service-map.md). 

Open Log Analytics-werk ruimten in de Azure Portal in het menu **log Analytics-werk ruimten** .

[![Anlytics-werk ruimten registreren](media/vminsights-configure-workspace/log-analytics-workspaces.png)](media/vminsights-configure-workspace/log-analytics-workspaces.png#lightbox)

U kunt een nieuwe Log Analytics-werk ruimte maken met behulp van een van de volgende methoden. Zie de [implementatie van uw Azure monitor-logboeken ontwerpen](../platform/design-logs-deployment.md) voor hulp bij het bepalen van het aantal werk ruimten dat u in uw omgeving moet gebruiken en hoe u de toegangs strategie kunt ontwerpen.


* [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../platform/powershell-workspace-configuration.md)
* [Azure Resource Manager](../samples/resource-manager-workspace.md)

## <a name="supported-regions"></a>Ondersteunde regio’s

Azure Monitor voor VM's ondersteunt Log Analytics-werk ruimten in de volgende regio's, hoewel u virtuele machines in elke regio kunt bewaken. De virtuele machines zelf zijn niet beperkt tot de regio's die worden ondersteund door de Log Analytics-werk ruimte.

- VS - west-centraal
- VS - west
- VS - west 2
- South Central US
- VS - oost
- VS - oost 2
- Central US
- VS - noord-centraal
- US Gov AZ
- US Gov VA
- Canada - midden
- Verenigd Koninkrijk Zuid
- Europa - noord
- Europa -west
- Azië - oost
- Azië - zuidoost
- India - centraal
- Japan - oost
- Australië - oost
- Australië - zuidoost

## <a name="azure-role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer voor Azure
Als u de functies in Azure Monitor voor VM's wilt inschakelen en gebruiken, moet u de [rol log Analytics Inzender](../platform/manage-access.md#manage-access-using-azure-permissions) hebben in de werk ruimte. Als u de prestaties, de status en de kaart gegevens wilt bekijken, moet u de [rol bewakings lezer](../platform/roles-permissions-security.md#built-in-monitoring-roles) hebben voor de Azure-VM. Zie [werk ruimten beheren](../platform/manage-access.md)voor meer informatie over het controleren van de toegang tot een log Analytics-werk ruimte.

## <a name="add-vminsights-solution-to-workspace"></a>VMInsights-oplossing toevoegen aan werk ruimte
Voordat een Log Analytics-werk ruimte kan worden gebruikt met Azure Monitor voor VM's, moet de *VMInsights* -oplossing zijn geïnstalleerd. De methoden voor het configureren van de werk ruimte worden beschreven in de volgende secties.

> [!NOTE]
> Wanneer u de *VMInsights* -oplossing aan de werk ruimte toevoegt, worden alle bestaande virtuele machines die zijn verbonden met de werk ruimte, verzonden naar InsightsMetrics. Gegevens voor de andere gegevens typen worden pas verzameld wanneer u de Dependency Agent toevoegt aan de bestaande virtuele machines die zijn verbonden met de werk ruimte.

### <a name="azure-portal"></a>Azure Portal
Er zijn drie opties voor het configureren van een bestaande werk ruimte met behulp van de Azure Portal. Deze worden hieronder beschreven.

Als u één werk ruimte wilt configureren, gaat u naar de optie **virtual machines** in het menu **Azure monitor** , selecteert u de **andere opties voor onboarding** en **configureert u vervolgens een werk ruimte**. Selecteer een abonnement en een werk ruimte en klik vervolgens op **configureren**.

[![Werkruimte configureren](media/vminsights-enable-at-scale-policy/configure-workspace.png)](media/vminsights-enable-at-scale-policy/configure-workspace.png#lightbox)

Als u meerdere werk ruimten wilt configureren, selecteert u het tabblad **werkruimte configuratie** in het menu **virtual machines** van het menu **monitor** in de Azure Portal. De filter waarden instellen om een lijst met bestaande werk ruimten weer te geven. Schakel het selectie vakje in naast elke werk ruimte die u wilt inschakelen en klik vervolgens op **geselecteerde items configureren** .

[![Werkruimteconfiguratie](media/vminsights-enable-at-scale-policy/workspace-configuration.png)](media/vminsights-enable-at-scale-policy/workspace-configuration.png#lightbox)


Wanneer u Azure Monitor voor VM's op één virtuele machine of VMSS inschakelt met behulp van de Azure Portal, krijgt u de mogelijkheid om een bestaande werk ruimte te selecteren of een nieuwe te maken. Als dit nog niet is gebeurd, wordt de *VMInsights* -oplossing in deze werk ruimte geïnstalleerd. U kunt deze werk ruimte vervolgens gebruiken voor andere agents.

[![Eén virtuele machine in de portal inschakelen](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png#lightbox)


### <a name="resource-manager-template"></a>Resource Manager-sjabloon
De Azure Resource Manager sjablonen voor Azure Monitor voor VM's zijn opgenomen in een archief bestand (. zip) dat u kunt [downloaden van onze github opslag plaats](https://aka.ms/VmInsightsARMTemplates). Dit omvat een sjabloon met de naam **ConfigureWorkspace** die een log Analytics werk ruimte configureert voor Azure monitor voor VM's. U implementeert deze sjabloon met een van de standaard methoden, met inbegrip van de volgende Power shell-en CLI-opdrachten: 

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
- Zie [agents voor Onboarding Azure monitor voor VM's](vminsights-enable-overview.md) om agents te verbinden met Azure monitor voor VM's.
- Zie [doel bewakings oplossingen in azure monitor (preview)](solution-targeting.md) om de hoeveelheid gegevens te beperken die vanuit een oplossing naar de werk ruimte wordt verzonden.
