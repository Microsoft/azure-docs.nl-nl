---
title: Bewaking met Azure Monitor-logboeken instellen
description: Meer informatie over het instellen van Azure Monitor-logboeken voor het visualiseren en analyseren van gebeurtenissen voor het bewaken van uw Azure Service Fabric-clusters.
author: srrengar
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: srrengar
ms.openlocfilehash: ba62ac80b2f8d318d0d13e81e88cc63a8d893a2b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100570342"
---
# <a name="set-up-azure-monitor-logs-for-a-cluster"></a>Azure Monitor-logboeken voor een cluster instellen

Azure Monitor-logboeken is onze aanbeveling voor het bewaken van gebeurtenissen op clusterniveau. U kunt Log Analytics werk ruimte instellen via Azure Resource Manager, Power shell of Azure Marketplace. Als u een bijgewerkte Resource Manager-sjabloon van uw implementatie wilt behouden voor toekomstig gebruik, gebruikt u dezelfde sjabloon om uw Azure Monitor-logboeken omgeving in te stellen. Implementatie via Marketplace is eenvoudiger als u al een cluster hebt dat is geïmplementeerd met diagnostische gegevens. Als u geen toegang op abonnements niveau hebt in het account waarmee u implementeert, implementeert u met Power shell of de Resource Manager-sjabloon.

> [!NOTE]
> Als u Azure Monitor logboeken wilt instellen om uw cluster te bewaken, moet u Diagnostische gegevens hebben ingeschakeld om gebeurtenissen op cluster niveau of platform niveau weer te geven. Zie [Diagnostische gegevens instellen in Windows-clusters](service-fabric-diagnostics-event-aggregation-wad.md) en [Diagnostische gegevens in Linux-clusters instellen](service-fabric-diagnostics-oms-syslog.md) voor meer informatie

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deploy-a-log-analytics-workspace-by-using-azure-marketplace"></a>Een Log Analytics-werk ruimte implementeren met behulp van Azure Marketplace

Als u een Log Analytics werkruimte wilt toevoegen nadat u een cluster hebt geïmplementeerd, gaat u naar Azure Marketplace in de portal en zoekt u naar **service Fabric-analyse**. Dit is een aangepaste oplossing voor Service Fabric implementaties met specifieke gegevens voor Service Fabric. In dit proces maakt u de oplossing (het dash board om inzichten weer te geven) en de werk ruimte (de aggregatie van de onderliggende cluster gegevens).

1. Selecteer **Nieuw** in het navigatie menu links. 

2. Zoeken naar **service Fabric-analyse**. Selecteer de resource die wordt weer gegeven.

3. Selecteer **Maken**.

    ![Service Fabric-analyse in Marketplace](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. Selecteer in het venster Service Fabric-analyse maken **een werk ruimte selecteren** voor het veld **OMS-werk** ruimte en **Maak vervolgens een nieuwe werk ruimte**. Vul de vereiste vermeldingen in. De enige vereiste hier is dat het abonnement voor het Service Fabric cluster en de werk ruimte hetzelfde is. Wanneer uw vermeldingen zijn gevalideerd, wordt de werk ruimte geïmplementeerd. De implementatie duurt slechts enkele minuten.

5. Wanneer u klaar bent, selecteert u opnieuw **maken** onder aan het venster Service Fabric-analyse maken. Zorg ervoor dat de nieuwe werk ruimte wordt weer gegeven onder de **OMS-werk ruimte**. Met deze actie wordt de oplossing toegevoegd aan de werk ruimte die u hebt gemaakt.

Als u Windows gebruikt, gaat u door met de volgende stappen om Azure Monitor logboeken te verbinden met het opslag account waarin uw cluster gebeurtenissen zijn opgeslagen. 

>[!NOTE]
>De Service Fabric-analyse oplossing wordt alleen ondersteund voor Windows-clusters. Voor Linux-clusters raadpleegt u ons artikel over [het instellen van Azure monitor logboeken voor Linux-clusters](service-fabric-diagnostics-oms-syslog.md).  

### <a name="connect-the-log-analytics-workspace-to-your-cluster"></a>De Log Analytics-werk ruimte koppelen aan uw cluster 

1. De werkruimte moet zijn verbonden met de diagnostische gegevens die afkomstig zijn van uw cluster. Ga naar de resource groep waarin u de Service Fabric-analyse oplossing hebt gemaakt. Selecteer **ServiceFabric \<nameOfWorkspace\>** en ga naar de pagina overzicht. Hier kunt u de instellingen voor de oplossing, de werk ruimte-instellingen wijzigen en toegang krijgen tot de Log Analytics-werk ruimte.

2. Selecteer in het navigatie menu aan de linkerkant onder **werkruimte gegevens bronnen** de optie **Logboeken voor opslag accounts**.

3. Op de pagina **Logboeken voor opslag accounts** selecteert u bovenaan **toevoegen** om de logboeken van uw cluster toe te voegen aan de werk ruimte.

4. Selecteer **opslag account** om het juiste account toe te voegen dat in het cluster is gemaakt. Als u de standaard naam hebt gebruikt, is het opslag **account \<resourceGroupName\> sfdg**. U kunt dit ook bevestigen met de Azure Resource Manager sjabloon die wordt gebruikt voor het implementeren van uw cluster door de waarde te controleren die wordt gebruikt voor **applicationDiagnosticsStorageAccountName**. Als de naam niet wordt weer gegeven, schuift u omlaag en selecteert u **laden meer**. Selecteer de naam van het opslagaccount.

5. Geef het gegevens type op. Stel deze in op **service Fabric-gebeurtenissen**.

6. Zorg ervoor dat de bron automatisch wordt ingesteld op **WADServiceFabric \* EventTable**.

7. Selecteer **OK** om uw werk ruimte te koppelen aan de logboeken van uw cluster.

    ![Logboeken voor opslag accounts toevoegen aan Azure Monitor logboeken](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

Het account wordt nu weer gegeven als onderdeel van de logboeken van uw opslag account in de gegevens bronnen van uw werk ruimte.

U hebt de oplossing Service Fabric-analyse toegevoegd aan een Log Analytics-werk ruimte die nu correct is verbonden met het platform en de toepassings logboek tabel van het cluster. U kunt op dezelfde manier extra bronnen toevoegen aan de werk ruimte.


## <a name="deploy-azure-monitor-logs-with-azure-resource-manager"></a>Azure Monitor-logboeken implementeren met Azure Resource Manager

Wanneer u een cluster implementeert met behulp van een resource manager-sjabloon, maakt de sjabloon een nieuwe Log Analytics-werk ruimte, voegt de Service Fabric oplossing toe aan de werk ruimte en configureert deze voor het lezen van gegevens uit de juiste opslag tabellen.

U kunt [deze voorbeeld sjabloon](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) gebruiken en wijzigen om te voldoen aan uw vereisten. Deze sjabloon doet het volgende:

* Hiermee maakt u een 5 knoop punt Service Fabric cluster
* Hiermee maakt u een Log Analytics werkruimte en Service Fabric oplossing
* Hiermee configureert u de Log Analytics-agent voor het verzamelen en verzenden van twee voorbeeld prestatie meter items naar de werk ruimte
* Hiermee configureert u WAD voor het verzamelen van Service Fabric en verzendt u deze naar Azure Storage-tabellen (WADServiceFabric * EventTable)
* Hiermee configureert u de Log Analytics-werk ruimte om de gebeurtenissen uit deze tabellen te lezen


U kunt de sjabloon als een resource manager-upgrade naar uw cluster implementeren met behulp `New-AzResourceGroupDeployment` van de API in de module Azure PowerShell. Een voor beeld van een opdracht is:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resourceGroupName>" -TemplateFile "<templatefile>.json" 
``` 

Azure Resource Manager detecteert dat deze opdracht een update is van een bestaande resource. De wijzigingen worden alleen verwerkt tussen de sjabloon die de bestaande implementatie bedient en de nieuwe sjabloon.

## <a name="deploy-azure-monitor-logs-with-azure-powershell"></a>Azure Monitor-logboeken implementeren met Azure PowerShell

U kunt uw log Analytics-resource ook via Power shell implementeren met behulp van de `New-AzOperationalInsightsWorkspace` opdracht. Als u deze methode wilt gebruiken, zorg er dan voor dat u [Azure PowerShell](/powershell/azure/install-az-ps)hebt geïnstalleerd. Gebruik dit script om een nieuwe Log Analytics-werk ruimte te maken en de Service Fabric oplossing hieraan toe te voegen: 

```powershell

$SubID = "<subscription ID>"
$ResourceGroup = "<Resource group name>"
$Location = "<Resource group location>"
$WorkspaceName = "<Log Analytics workspace name>"
$solution = "ServiceFabric"

# Sign in to Azure and access the correct subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionId $SubID 

# Create the resource group if needed
try {
    Get-AzResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzResourceGroup -Name $ResourceGroup -Location $Location
}

New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true

```

Wanneer u klaar bent, volgt u de stappen in de vorige sectie om Azure Monitor logboeken te verbinden met het juiste opslag account.

U kunt ook andere oplossingen toevoegen of andere wijzigingen aanbrengen in uw Log Analytics-werk ruimte met behulp van Power shell. Zie [Azure monitor-logboeken beheren met Power shell](../azure-monitor/logs/powershell-workspace-configuration.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
* [Implementeer de log Analytics-agent](service-fabric-diagnostics-oms-agent.md) op uw knoop punten om prestatie meter items te verzamelen en docker-statistieken en-logboeken te verzamelen voor uw containers
* Krijg vertrouwd met de functies voor [Zoeken in Logboeken en query's](../azure-monitor/logs/log-query-overview.md) die worden aangeboden als onderdeel van Azure monitor logboeken
* [De weer gave Designer gebruiken om aangepaste weer gaven te maken in Azure Monitor-logboeken](../azure-monitor/visualize/view-designer.md)
