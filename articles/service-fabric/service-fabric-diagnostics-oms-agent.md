---
title: Prestatie bewaking met Azure Monitor-logboeken
description: Meer informatie over het instellen van de Log Analytics-agent voor het bewaken van containers en prestatie meter items voor uw Azure Service Fabric-clusters.
ms.topic: conceptual
ms.date: 04/16/2018
ms.openlocfilehash: 9bb89dc2eebe584a0a9f81a6707c0a2e4fa2fc30
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105626670"
---
# <a name="performance-monitoring-with-azure-monitor-logs"></a>Prestatie bewaking met Azure Monitor-logboeken

In dit artikel worden de stappen beschreven voor het toevoegen van de Log Analytics agent als een extensie voor virtuele-machine schaal sets aan uw cluster en om deze te verbinden met uw bestaande Azure Log Analytics-werk ruimte. Hierdoor kunt u diagnostische gegevens over containers, toepassingen en prestatiebewaking verzamelen. Door de uitbreiding toe te voegen aan de virtuele-machineschaalset, zorgt Azure Resource Manager ervoor dat deze op elk knooppunt wordt geïnstalleerd, zelfs wanneer het cluster wordt geschaald.

> [!NOTE]
> In dit artikel wordt ervan uitgegaan dat u al een Azure Log Analytics-werk ruimte hebt ingesteld. Als u dit niet doet, moet u [Azure monitor-logboeken instellen](service-fabric-diagnostics-oms-setup.md)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="add-the-agent-extension-via-azure-cli"></a>De agent extensie toevoegen via Azure CLI

De beste manier om de Log Analytics agent toe te voegen aan uw cluster is via de virtuele-machine Scale set Api's die beschikbaar zijn in de Azure CLI. Als u nog geen Azure CLI hebt ingesteld, gaat u naar Azure Portal en opent u een [Cloud shell](../cloud-shell/overview.md) instantie of [installeert u de Azure cli](/cli/azure/install-azure-cli).

1. Als uw Cloud Shell is aangevraagd, moet u ervoor zorgen dat u werkt in hetzelfde abonnement als uw resource. Controleer dit met `az account show` en controleer of de naam waarde overeenkomt met die van het abonnement van uw cluster.

2. Navigeer in de portal naar de resource groep waar uw Log Analytics werk ruimte zich bevindt. Klik in de log Analytics-resource (het type van de resource wordt Log Analytics werk ruimte). Klik op de pagina overzicht van resources op **Geavanceerde instellingen** onder de sectie instellingen in het menu links.

    ![Eigenschappen pagina voor logboek analyse](media/service-fabric-diagnostics-oms-agent/oms-advanced-settings.png)

3. Klik op **Windows-servers** als u een Windows-cluster wilt maken en **Linux-servers** als u een Linux-cluster maakt. Op deze pagina ziet u uw `workspace ID` en `workspace key` (vermeld als primaire sleutel in de portal). U hebt beide nodig voor de volgende stap.

4. Voer de opdracht uit om de Log Analytics agent te installeren op uw cluster met behulp van de `vmss extension set` API:

    Voor een Windows-cluster:

    ```azurecli
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Voor een Linux-cluster:

    ```azurecli
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Hier volgt een voor beeld van de Log Analytics agent wordt toegevoegd aan een Windows-cluster.

    ![Opdracht Agent CLI Log Analytics](media/service-fabric-diagnostics-oms-agent/cli-command.png)

5. Dit duurt minder dan 15 minuten om de agent aan uw knoop punten toe te voegen. U kunt controleren of de agents zijn toegevoegd met behulp van de `az vmss extension list` API:

    ```azurecli
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>De agent toevoegen via de Resource Manager-sjabloon

Voor beelden van Resource Manager-sjablonen voor het implementeren van een Azure Log Analytics-werk ruimte en het toevoegen van een agent aan elk van uw knoop punten is beschikbaar voor [Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) of [Linux](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeType-Secure-OMS).

U kunt deze sjabloon downloaden en aanpassen om een cluster te implementeren dat het beste bij uw behoeften past.

## <a name="view-performance-counters"></a>Prestatie meter items weer geven

Nu u de Log Analytics-agent hebt toegevoegd, gaat u naar de Log Analytics Portal om te kiezen welke prestatie meter items u wilt verzamelen.

1. Ga in het Azure Portal naar de resource groep waarin u de Service Fabric-analyse oplossing hebt gemaakt. Selecteer **ServiceFabric \<nameOfLog AnalyticsWorkspace\>**.

2. Klik op **Log Analytics**.

3. Klik op **Geavanceerde instellingen**.

4. Klik op **gegevens** en vervolgens op **Windows-of Linux-prestatie meter items**. Er is een lijst met standaard meteritems die u kunt inschakelen en u kunt ook het interval voor verzameling instellen. U kunt ook [aanvullende prestatiemeteritems toevoegen](service-fabric-diagnostics-event-generation-perf.md) om te verzamelen. In dit [artikel](/windows/win32/perfctrs/specifying-a-counter-path) wordt verwezen naar de juiste indeling.

5. Klik op **Opslaan** en klik vervolgens op **OK**.

6. Sluit de Blade geavanceerde instellingen.

7. Klik onder de kop algemeen op **werkruimte samenvatting**.

8. U ziet tegels in de vorm van een grafiek voor elk van de ingeschakelde oplossingen, met inbegrip van een voor Service Fabric. Klik op de **Service Fabric**-grafiek om door te gaan naar de oplossing Service Fabric-analyse.

9. Er worden enkele tegels weer geven met grafieken op operationele kanaal en betrouw bare Services-gebeurtenissen. De grafische weergave van de inkomende gegevens voor de meteritems die u hebt geselecteerd, wordt weergegeven onder Metrische knooppuntgegevens.

10. Klik op de metrische grafiek van een container om meer details weer te geven. U kunt ook query's uitvoeren op gegevens van prestatiemeteritems, net als bij clustergebeurtenissen, en filteren op de knooppunten, de naam van het prestatiemeteritem en waarden met behulp van de Kusto-querytaal.

![Log Analytics prestatie meter query](media/service-fabric-diagnostics-event-analysis-oms/oms_node_metrics_table.PNG)

## <a name="next-steps"></a>Volgende stappen

* Relevante [prestatie meter items](service-fabric-diagnostics-event-generation-perf.md)verzamelen. Als u de Log Analytics-agent wilt configureren voor het verzamelen van specifieke prestatie meter items, raadpleegt u [gegevens bronnen configureren](../azure-monitor/agents/agent-data-sources.md#configuring-data-sources).
* Azure Monitor logboeken configureren om [automatische waarschuwingen](../azure-monitor/alerts/alerts-overview.md) in te stellen voor detectie en diagnostische gegevens
* Als alternatief kunt u prestatie meter items verzamelen via [Azure Diagnostics extensie en deze naar Application Insights verzenden](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template)
