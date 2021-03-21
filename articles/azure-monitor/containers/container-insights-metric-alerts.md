---
title: Metrische waarschuwingen van container Insights
description: In dit artikel worden de aanbevolen metrische waarschuwingen weer gegeven die beschikbaar zijn via container Insights in open bare preview.
ms.topic: conceptual
ms.date: 10/28/2020
ms.openlocfilehash: f19959c76d31422a0bdf898a6fa41e6b168e2e61
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101728889"
---
# <a name="recommended-metric-alerts-preview-from-container-insights"></a>Aanbevolen metrische waarschuwingen (preview) van container Insights

Als u een waarschuwing wilt ontvangen over problemen met de systeem bronnen wanneer er sprake is van piek vraag en de nabije capaciteit wordt uitgevoerd, kunt u met container Insights een logboek waarschuwing maken op basis van de prestatie gegevens die zijn opgeslagen in Azure Monitor Logboeken. Container Insights bevat nu vooraf geconfigureerde metrische waarschuwings regels voor uw AKS en Azure Arc Kubernetes-cluster, dat zich in de open bare preview bevindt.

In dit artikel wordt de ervaring beoordeeld en vindt u richt lijnen voor het configureren en beheren van deze waarschuwings regels.

Als u niet bekend bent met Azure Monitor waarschuwingen, raadpleegt u [overzicht van waarschuwingen in Microsoft Azure](../alerts/alerts-overview.md) voordat u begint. Zie [metrische waarschuwingen in azure monitor](../alerts/alerts-metric-overview.md)voor meer informatie over metrische waarschuwingen.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, controleert u het volgende:

* Aangepaste metrische gegevens zijn alleen beschikbaar in een subset van Azure-regio's. Een lijst met ondersteunde regio's wordt beschreven in [ondersteunde regio's](../essentials/metrics-custom-overview.md#supported-regions).

* Voor het ondersteunen van metrische waarschuwingen en de introductie van extra metrische gegevens, is de mini maal vereiste agent versie **MCR.Microsoft.com/azuremonitor/containerinsights/ciprod:ciprod05262020** voor AKS en **MCR.Microsoft.com/azuremonitor/containerinsights/ciprod:ciprod09252020** voor Azure Arc enabled Kubernetes cluster.

    Als u wilt controleren of op uw cluster de nieuwere versie van de agent wordt uitgevoerd, kunt u het volgende doen:

    * Voer de opdracht uit: `kubectl describe <omsagent-pod-name> --namespace=kube-system` . In de status die is geretourneerd, noteert u de waarde onder **afbeelding** voor omsagent in de sectie *containers* van de uitvoer. 
    * Op het tabblad **knoop punten** selecteert u het cluster knooppunt en in het deel venster **Eigenschappen** aan de rechter kant, noteer de waarde onder **Agent-installatie kopie code**.

    De waarde die wordt weer gegeven voor AKS moet versie **ciprod05262020** of hoger zijn. De waarde die wordt weer gegeven voor Azure Arc enabled Kubernetes cluster moet versie **ciprod09252020** of hoger zijn. Als uw cluster een oudere versie heeft, raadpleegt [u de container Insights-agent bijwerken](container-insights-manage-agent.md#upgrade-agent-on-aks-cluster) voor stappen om de nieuwste versie te verkrijgen.

    Zie [release geschiedenis van agent](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)voor meer informatie over de agent release. U kunt controleren of de metrische gegevens worden verzameld door Azure Monitor Metrics Explorer te gebruiken **en te controleren** in de **metrische naam ruimte** die wordt weer gegeven. Als dat het geval is, kunt u door gaan met het instellen van de waarschuwingen. Als er geen metrische gegevens worden verzameld, ontbreken de benodigde machtigingen voor de Cluster-service-principal of MSI. Als u wilt controleren of de SPN of MSI lid is van de Publisher-rol **bewakings metrieken** , volgt u de stappen die worden beschreven in de sectie [upgrade per cluster met behulp van Azure cli](container-insights-update-metrics.md#upgrade-per-cluster-using-azure-cli) om de roltoewijzing te bevestigen en in te stellen.

## <a name="alert-rules-overview"></a>Overzicht van waarschuwings regels

Container Insights bevat de volgende metrische waarschuwingen voor uw AKS-en Azure Arc ingeschakelde Kubernetes-clusters om te waarschuwen over welke zaken u moet doen:

|Naam| Beschrijving |Standaard drempelwaarde |
|----|-------------|------------------|
|Gemiddeld CPU-percentage van container |Berekent gemiddeld CPU-gebruik per container.|Wanneer het gemiddelde CPU-gebruik per container groter is dan 95%.| 
|Gemiddelde geheugen% van de container werkset |Hiermee berekent u het gemiddelde aantal werkset geheugen dat per container wordt gebruikt.|Wanneer het geheugen gebruik van de gemiddelde werkset per container groter is dan 95%. |
|Gemiddeld CPU-gebruik (percentage) |Berekent het gemiddelde CPU-verbruik per knoop punt. |Als gemiddeld CPU-gebruik van het knoop punt groter is dan 80% |
|Gemiddeld schijf gebruik% |Berekent het gemiddelde schijf gebruik voor een knoop punt.|Wanneer het schijf gebruik voor een knoop punt groter is dan 80%. |
|Gemiddeld gebruik van het permanente volume% |Berekent het gemiddelde HW-gebruik per pod. |Wanneer het gemiddelde HW-gebruik per pod groter is dan 80%.|
|Gemiddelde werkset geheugen% |Berekent het gemiddelde werkset geheugen voor een knoop punt. |Wanneer de gemiddelde werkset geheugen voor een knoop punt groter is dan 80%. |
|Aantal containers opnieuw starten |Hiermee wordt het aantal opnieuw te starten containers berekend. | Wanneer het opnieuw opstarten van de container groter is dan 0. |
|Aantal mislukte pod |Hiermee wordt berekend of een pod de status Mislukt heeft.|Wanneer een aantal peulen de status mislukt groter dan 0 is. |
|Status van knoop punt |Hiermee wordt berekend of een knoop punt zich in een niet-loopvlak status bevindt.|Wanneer een aantal knoop punten in de status van het loopvlak punt groter is dan 0. |
|OOM beëindigde containers |Hiermee wordt het aantal beëindigde OOM-containers berekend. |Wanneer een aantal gedode OOM-containers groter is dan 0. |
|Klaar voor het Peul% |Berekent de gemiddelde status gereed. |Als de status gereed is voor een Peul, is dit minder dan 80%.|
|Aantal voltooide taken |Hiermee wordt het aantal taken berekend dat meer dan zes uur geleden is voltooid. |Wanneer het aantal verouderde taken ouder dan zes uur groter is dan 0.|

Er zijn algemene eigenschappen voor al deze waarschuwings regels:

* Alle waarschuwings regels zijn gebaseerd op metrische gegevens.

* Alle waarschuwings regels zijn standaard uitgeschakeld.

* Alle waarschuwings regels worden eenmaal per minuut geëvalueerd en ze worden op de laatste vijf minuten van de gegevens weer gegeven.

* Aan waarschuwings regels is standaard geen actie groep toegewezen. U kunt een [actie groep](../alerts/action-groups.md) toevoegen aan de waarschuwing door een bestaande actie groep te selecteren of een nieuwe actie groep te maken tijdens het bewerken van de waarschuwings regel.

* U kunt de drempel waarde voor waarschuwings regels wijzigen door deze rechtstreeks te bewerken. Raadpleeg echter de richt lijnen van elke waarschuwings regel voordat u de drempel waarde wijzigt.

De volgende metrische gegevens op basis van een waarschuwing hebben unieke gedrags kenmerken ten opzichte van de andere metrische gegevens:

* *completedJobsCount* metric wordt alleen verzonden als er taken zijn die meer dan zes uur geleden zijn voltooid.

* *containerRestartCount* metric wordt alleen verzonden wanneer containers opnieuw worden opgestart.

* *oomKilledContainerCount* metric wordt alleen verzonden wanneer er oom zijn gedood.

* *cpuExceededPercentage*-, *MemoryRssExceededPercentage*-en *memoryWorkingSetExceededPercentage* -metrische gegevens worden verzonden wanneer de waarden van de CPU, het geheugen en de geheugen werkset de geconfigureerde drempel waarde overschrijden (de standaard drempel waarde is 95%). Deze drempel waarden zijn exclusief de drempel waarde voor waarschuwings voorwaarden die is opgegeven voor de bijbehorende waarschuwings regel. Als u deze metrische gegevens wilt verzamelen en wilt analyseren vanuit [Metrics Explorer](../essentials/metrics-getting-started.md), raden we u aan om de drempel waarde te configureren op een lager niveau dan uw waarschuwings drempel. De configuratie voor de verzamelings instellingen voor de drempel waarden voor het resource gebruik van een container kan worden overschreven in het ConfigMaps-bestand onder de sectie `[alertable_metrics_configuration_settings.container_resource_utilization_thresholds]` . Zie de sectie [waarschuwingen over metrische gegevens ConfigMaps configureren](#configure-alertable-metrics-in-configmaps) voor meer informatie over het configureren van uw ConfigMap-configuratie bestand.

* *pvUsageExceededPercentage* metric wordt verzonden wanneer het gebruiks percentage van het permanente volume de geconfigureerde drempel waarde overschrijdt (de standaard drempel waarde is 60%). Deze drempel waarde is exclusief de drempel waarde voor waarschuwings voorwaarden die is opgegeven voor de bijbehorende waarschuwings regel. Als u deze metrische gegevens wilt verzamelen en wilt analyseren vanuit [Metrics Explorer](../essentials/metrics-getting-started.md), raden we u aan om de drempel waarde te configureren op een lager niveau dan uw waarschuwings drempel. De configuratie met betrekking tot de verzamelings instellingen voor drempel waarden voor permanente volume gebruik kan worden overschreven in het ConfigMaps-bestand onder de sectie `[alertable_metrics_configuration_settings.pv_utilization_thresholds]` . Zie de sectie [waarschuwingen over metrische gegevens ConfigMaps configureren](#configure-alertable-metrics-in-configmaps) voor meer informatie over het configureren van uw ConfigMap-configuratie bestand. Verzameling van gegevens over het permanente volume met claims in de *uitvoeren-* naam ruimte worden standaard uitgesloten. Gebruik de sectie in het ConfigMap-bestand om de verzameling in deze naam ruimte in te scha kelen `[metric_collection_settings.collect_kube_system_pv_metrics]` . Zie [instellingen voor metrische verzameling](./container-insights-agent-config.md#metric-collection-settings) voor meer informatie.

## <a name="metrics-collected"></a>Verzamelde metrische gegevens

De volgende metrische gegevens worden ingeschakeld en verzameld, tenzij anders aangegeven, als onderdeel van deze functie:

|Metrische naam ruimte |Metrisch |Beschrijving |
|---------|----|------------|
|Inzichten. container/knoop punten |cpuUsageMillicores |CPU-gebruik in millicores per host.|
|Inzichten. container/knoop punten |cpuUsagePercentage |Percentage CPU-gebruik per knoop punt.|
|Inzichten. container/knoop punten |memoryRssBytes |RSS-gebruik van geheugen in bytes per host.|
|Inzichten. container/knoop punten |memoryRssPercentage |Percentage van het RSS-gebruik van het geheugen per host.|
|Inzichten. container/knoop punten |memoryWorkingSetBytes |Het gebruik van geheugen sets in bytes door de host.|
|Inzichten. container/knoop punten |memoryWorkingSetPercentage |Gebruiks percentage van geheugen werkset per host.|
|Inzichten. container/knoop punten |nodesCount |Aantal knoop punten op status.|
|Inzichten. container/knoop punten |diskUsedPercentage |Percentage van de schijf die op het knoop punt wordt gebruikt door het apparaat.|
|Inzichten. container/peul |podCount |Aantal per controller, naam ruimte, knoop punt en fase.|
|Inzichten. container/peul |completedJobsCount |Voltooide taken tellen de drempel waarde voor oudere gebruikers die kan worden geconfigureerd (standaard is zes uur) per controller, Kubernetes-naam ruimte. |
|Inzichten. container/peul |restartingContainerCount |Aantal herstarts van container per controller, Kubernetes naam ruimte.|
|Inzichten. container/peul |oomKilledContainerCount |Aantal OOMkilled-containers per controller, Kubernetes naam ruimte.|
|Inzichten. container/peul |podReadyPercentage |Percentage van de in de status gereed per controller, Kubernetes naam ruimte.|
|Inzichten. container/containers |cpuExceededPercentage |Percentage CPU-gebruik voor containers die de door de gebruiker geconfigureerde drempel waarde overschrijden (standaard is 95,0) op container naam, controller naam, Kubernetes naam ruimte, Pod naam.<br> Monsters  |
|Inzichten. container/containers |memoryRssExceededPercentage |Percentage geheugen-RSS voor containers die Configureer bare drempel waarde voor gebruikers overschrijden (standaard is 95,0) op container naam, controller naam, Kubernetes naam ruimte, Pod naam.|
|Inzichten. container/containers |memoryWorkingSetExceededPercentage |Percentage geheugen werkset voor containers die Configureer bare drempel waarde voor gebruikers overschrijden (standaard is 95,0) op container naam, naam van de controller, Kubernetes naam ruimte, Pod naam.|
|Inzichten. container/persistentvolumes |pvUsageExceededPercentage |Percentage van het HW-gebruik voor permanente volumes die de door de gebruiker geconfigureerde drempel waarde overschrijden (standaard is 60,0) door de claim naam, de naam ruimte, de volume naam, de pod-naam en de naam van het knoop punt.

## <a name="enable-alert-rules"></a>Waarschuwings regels inschakelen

Volg deze stappen om de metrische waarschuwingen in Azure Monitor van de Azure Portal in te scha kelen. Zie [inschakelen met een resource manager-sjabloon](#enable-with-a-resource-manager-template)om het gebruik van een resource manager-sjabloon in te scha kelen.

### <a name="from-the-azure-portal"></a>Vanuit Azure Portal

In deze sectie wordt uitgelegd hoe u container Insights-metrische waarschuwing (preview) inschakelt vanuit de Azure Portal.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

2. Toegang tot de functie waarschuwing over metrische gegevens van de container Insights (preview) is rechtstreeks beschikbaar vanuit een AKS-cluster door **inzichten** te selecteren in het linkerdeel venster van de Azure Portal.

3. Selecteer de **Aanbevolen waarschuwingen** op de opdracht balk.

    ![Aanbevolen waarschuwings opties in container Insights](./media/container-insights-metric-alerts/command-bar-recommended-alerts.png)

4. Het eigenschappen venster **Aanbevolen waarschuwingen** wordt automatisch weer gegeven aan de rechter kant van de pagina. Standaard zijn alle waarschuwings regels in de lijst uitgeschakeld. Nadat u **inschakelen** hebt geselecteerd, wordt de waarschuwings regel gemaakt en wordt de regel naam bijgewerkt met een koppeling naar de resource van de waarschuwing.

    ![Eigenschappen venster aanbevolen waarschuwingen](./media/container-insights-metric-alerts/recommended-alerts-pane.png)

    Nadat u de wissel knop **inschakelen/uitschakelen** voor het inschakelen van de waarschuwing hebt geselecteerd, wordt er een waarschuwings regel gemaakt en wordt de regel naam bijgewerkt met een koppeling naar de werkelijke waarschuwings resource.

    ![Waarschuwingsregel inschakelen](./media/container-insights-metric-alerts/recommended-alerts-pane-enable.png)

5. Waarschuwings regels zijn niet gekoppeld aan een [actie groep](../alerts/action-groups.md) om gebruikers ervan op de hoogte te stellen dat er een waarschuwing is geactiveerd. Selecteer **geen actie groep toegewezen** en geef op de pagina **actie groepen** een bestaande of een actie groep maken door **toevoegen** of **maken** te selecteren.

    ![Een actie groep selecteren](./media/container-insights-metric-alerts/select-action-group.png)

### <a name="enable-with-a-resource-manager-template"></a>Inschakelen met een resource manager-sjabloon

U kunt een Azure Resource Manager sjabloon en parameter bestand gebruiken om de meegeleverde metrische waarschuwingen in Azure Monitor te maken.

De basis stappen zijn als volgt:

1. Down load een of meer van de beschik bare sjablonen die beschrijven hoe u de waarschuwing maakt vanuit [github](https://github.com/microsoft/Docker-Provider/tree/ci_dev/alerts/recommended_alerts_ARM).

2. Maak en gebruik een [parameter bestand](../../azure-resource-manager/templates/parameter-files.md) als JSON om de vereiste waarden in te stellen voor het maken van de waarschuwings regel.

3. Implementeer de sjabloon vanuit de Azure Portal, Power shell of Azure CLI.

#### <a name="deploy-through-azure-portal"></a>Implementeren via Azure Portal

1. Down load en sla het bestand op met behulp van de volgende opdrachten om de waarschuwings regel te maken en op te slaan in een lokale map, het Azure Resource Manager sjabloon en de para meter.

2. Als u een aangepaste sjabloon via de portal wilt implementeren, selecteert u **een resource maken** op basis van de [Azure Portal](https://portal.azure.com).

3. Zoek naar een **sjabloon** en selecteer vervolgens **Sjabloonimlementatie**.

4. Selecteer **Maken**.

5. U ziet verschillende opties voor het maken van een sjabloon. Selecteer **uw eigen sjabloon bouwen in de editor**.

6. Selecteer op de **pagina sjabloon bewerken** de optie **bestand laden** en selecteer vervolgens het sjabloon bestand.

7. Selecteer op de pagina **sjabloon bewerken** de optie **Opslaan**.

8. Geef op de pagina **aangepaste implementatie** het volgende op en klik vervolgens op **kopen** volt ooien om de sjabloon te implementeren en de waarschuwings regel te maken.

    * Resourcegroep
    * Locatie
    * Naam van waarschuwing
    * Cluster bron-ID

#### <a name="deploy-with-azure-powershell-or-cli"></a>Implementeren met Azure PowerShell of CLI

1. Down load en sla het bestand op met behulp van de volgende opdrachten om de waarschuwings regel te maken en op te slaan in een lokale map, het Azure Resource Manager sjabloon en de para meter.

2. U kunt de metrische waarschuwing maken met behulp van de sjabloon en het parameter bestand met behulp van Power shell of Azure CLI.

    Azure PowerShell gebruiken

    ```powershell
    Connect-AzAccount

    Select-AzSubscription -SubscriptionName <yourSubscriptionName>
    New-AzResourceGroupDeployment -Name CIMetricAlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
    -TemplateFile templateFilename.json -TemplateParameterFile templateParameterFilename.parameters.json
    ```

    Azure CLI gebruiken

    ```azurecli
    az login

    az deployment group create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file templateFileName.json \
    --parameters @templateParameterFilename.parameters.json
    ```

    >[!NOTE]
    >Terwijl de metrische waarschuwing in een andere resource groep kan worden gemaakt naar de doel resource, raden we u aan dezelfde resource groep te gebruiken als de doel resource.

## <a name="edit-alert-rules"></a>Waarschuwings regels bewerken

U kunt de container Insights-waarschuwings regels weer geven en beheren, de drempel waarde bewerken of een [actie groep](../alerts/action-groups.md) voor uw AKS-cluster configureren. Hoewel u deze acties kunt uitvoeren vanuit het Azure Portal en Azure CLI, kan het ook rechtstreeks vanuit uw AKS-cluster in container Insights worden uitgevoerd.

1. Selecteer de **Aanbevolen waarschuwingen** op de opdracht balk.

2. Als u de drempel waarde wilt wijzigen, selecteert u de ingeschakelde waarschuwing in het deel venster **Aanbevolen waarschuwingen** . Selecteer in de **regel bewerken** de **waarschuwings criteria** die u wilt bewerken.

    * Selecteer de **voor waarde** om de drempel waarde voor de waarschuwings regel te wijzigen.
    * Als u een bestaande wilt opgeven of een actie groep wilt maken, selecteert u **toevoegen** of **maken** onder **actie groep**

Als u waarschuwingen wilt weer geven die zijn gemaakt voor de ingeschakelde regels, selecteert u in het deel venster **Aanbevolen waarschuwingen** **weer geven in waarschuwingen**. U wordt omgeleid naar het menu waarschuwing voor het AKS-cluster, waarin u alle waarschuwingen kunt zien die momenteel zijn gemaakt voor uw cluster.

## <a name="configure-alertable-metrics-in-configmaps"></a>Waarschuwings gegevens configureren in ConfigMaps

Voer de volgende stappen uit om het configuratie bestand van uw ConfigMap te configureren om de standaard drempel waarden voor gebruik te overschrijven. Deze stappen zijn alleen van toepassing op de volgende beschik bare metrische gegevens:

* *cpuExceededPercentage*
* *memoryRssExceededPercentage*
* *memoryWorkingSetExceededPercentage*
* *pvUsageExceededPercentage*

1. Bewerk het ConfigMap YAML-bestand onder de sectie `[alertable_metrics_configuration_settings.container_resource_utilization_thresholds]` of `[alertable_metrics_configuration_settings.pv_utilization_thresholds]` .

   - Configureer het ConfigMap-bestand met behulp van het volgende voor beeld om de drempel waarde voor *cpuExceededPercentage* te wijzigen in 90% en te beginnen met het verzamelen van deze metrische waarde wanneer die drempel is bereikt en overschreden:

     ```
     [alertable_metrics_configuration_settings.container_resource_utilization_thresholds]
         # Threshold for container cpu, metric will be sent only when cpu utilization exceeds or becomes equal to the following percentage
         container_cpu_threshold_percentage = 90.0
         # Threshold for container memoryRss, metric will be sent only when memory rss exceeds or becomes equal to the following percentage
         container_memory_rss_threshold_percentage = 95.0
         # Threshold for container memoryWorkingSet, metric will be sent only when memory working set exceeds or becomes equal to the following percentage
         container_memory_working_set_threshold_percentage = 95.0
     ```

   - Configureer het ConfigMap-bestand met behulp van het volgende voor beeld om de drempel waarde voor *pvUsageExceededPercentage* te wijzigen in 80% en te beginnen met het verzamelen van deze metrische waarde wanneer die drempel is bereikt en overschreden:

     ```
     [alertable_metrics_configuration_settings.pv_utilization_thresholds]
         # Threshold for persistent volume usage bytes, metric will be sent only when persistent volume utilization exceeds or becomes equal to the following percentage
         pv_usage_threshold_percentage = 80.0
     ```

2. Voer de volgende kubectl-opdracht uit: `kubectl apply -f <configmap_yaml_file.yaml>` .

    Bijvoorbeeld: `kubectl apply -f container-azm-ms-agentconfig.yaml`.

Het kan een paar minuten duren voordat de configuratie wijziging is doorgevoerd en alle omsagent in het cluster opnieuw worden opgestart. Het opnieuw opstarten is een rolling start voor alle omsagent-peulen; ze worden niet op hetzelfde moment opnieuw opgestart. Wanneer het opnieuw opstarten is voltooid, wordt een bericht weer gegeven dat lijkt op het volgende voor beeld en het resultaat bevat: `configmap "container-azm-ms-agentconfig" created` .

## <a name="next-steps"></a>Volgende stappen

- Bekijk de [voor beelden van logboek query's](container-insights-log-search.md#search-logs-to-analyze-data) om vooraf gedefinieerde query's en voor beelden te bekijken voor het evalueren of aanpassen van waarschuwingen, het visualiseren of analyseren van uw clusters.

- Zie [Kubernetes cluster prestaties weer geven](container-insights-analyze.md)voor meer informatie over Azure monitor en het bewaken van andere aspecten van uw Kubernetes-cluster.