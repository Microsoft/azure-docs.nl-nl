---
title: Bewakings- Azure Machine Learning | Microsoft Docs
description: Meer informatie over het gebruik Azure Monitor om waarschuwingen te bekijken, analyseren en maken voor metrische gegevens van Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.custom: subject-monitoring
ms.date: 10/01/2020
ms.openlocfilehash: e5fd0fdd5a6f9a4a7537a844b096efdfef253638
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816850"
---
# <a name="monitor-azure-machine-learning"></a>Azure Machine Learning bewaken

Wanneer u kritieke toepassingen en bedrijfsprocessen hebt die afhankelijk zijn van Azure-resources, wilt u deze resources controleren op beschikbaarheid, prestaties en werking. In dit artikel worden de bewakingsgegevens beschreven die worden gegenereerd door Azure Machine Learning en hoe u deze gegevens analyseert en waarschuwt met Azure Monitor.

> [!TIP]
> De informatie in dit document is voornamelijk bedoeld voor __beheerders,__ omdat deze bewaking beschrijft voor de Azure Machine Learning Service en bijbehorende Azure-services. Als u een data scientist of __ontwikkelaar__ __bent__ en informatie wilt bewaken die specifiek is voor de uitvoering van *uw modeltraining,* bekijkt u de volgende documenten:
>
> * [Trainings runs starten, bewaken en annuleren](how-to-track-monitor-analyze-runs.md)
> * [Metrische gegevens registreren voor trainingsuitvoeringen](how-to-log-view-metrics.md)
> * [Experimenten bijhouden met MLflow](how-to-use-mlflow.md)
> * [Uitvoeringen visualiseren met TensorBoard](how-to-monitor-tensorboard.md)
>
> Als u informatie wilt bewaken die wordt gegenereerd door modellen die zijn geïmplementeerd als webservices of IoT Edge-modules, zie [Modelgegevens](how-to-enable-data-collection.md) verzamelen en [Bewaken met Application Insights](how-to-enable-app-insights.md).

## <a name="what-is-azure-monitor"></a>Wat is Azure Monitor?

Azure Machine Learning maakt bewakingsgegevens met [behulp Azure Monitor](../azure-monitor/overview.md), een volledige stack monitoring-service in Azure. Azure Monitor biedt een volledige set functies voor het bewaken van uw Azure-resources. Het kan ook resources in andere clouds en on-premises bewaken.

Begin met het artikel [Azure-resources bewaken met Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md), waarin de volgende concepten worden beschreven:

- Wat is Azure Monitor?
- Kosten die zijn gekoppeld aan bewaking
- Bewakingsgegevens die zijn verzameld in Azure
- Gegevensverzameling configureren
- Standaardhulpprogramma's in Azure voor het analyseren en waarschuwen van bewakingsgegevens

De volgende secties zijn gebaseerd op dit artikel door de specifieke gegevens te beschrijven die zijn verzameld voor Azure Machine Learning. Deze secties bevatten ook voorbeelden voor het configureren van gegevensverzameling en het analyseren van deze gegevens met Azure-hulpprogramma's.

> [!TIP]
> Zie Gebruik en geschatte kosten Azure Monitor meer inzicht in de kosten die zijn gekoppeld aan [Azure Monitor.](../azure-monitor//usage-estimated-costs.md) Zie Logboekgegevens opnemen voor meer informatie over de tijd die het kost om uw gegevens weer te geven in Azure Monitor [logboekgegevens.](../azure-monitor/logs/data-ingestion-time.md)

## <a name="monitoring-data-from-azure-machine-learning"></a>Bewakingsgegevens van Azure Machine Learning

Azure Machine Learning verzamelt dezelfde soorten bewakingsgegevens als andere Azure-resources die worden beschreven in [Gegevens van Azure-resources bewaken.](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data) 

Zie [Azure Machine Learning controlegegevens voor](monitor-resource-reference.md) een gedetailleerde referentie van de logboeken en metrische gegevens die zijn gemaakt door Azure Machine Learning.

<a id="configuration"></a>

## <a name="collection-and-routing"></a>Verzameling en routering

Metrische gegevens van het platform en het activiteitenlogboek worden automatisch verzameld en opgeslagen, maar kunnen worden doorgeleid naar andere locaties met behulp van een diagnostische instelling.  

Resourcelogboeken worden niet verzameld en opgeslagen totdat u een diagnostische instelling maakt en ze doorrouteerd naar een of meer locaties.

Zie [Diagnostische instelling maken](../azure-monitor/essentials/diagnostic-settings.md) voor het verzamelen van platformlogboeken en metrische gegevens in Azure voor het gedetailleerde proces voor het maken van een diagnostische instelling met behulp van Azure Portal, CLI of PowerShell. Wanneer u een diagnostische instelling maakt, geeft u op welke categorieën logboeken moeten worden verzameld. De categorieën voor Azure Machine Learning worden vermeld in Azure Machine Learning [naslaginformatie over bewakingsgegevens.](monitor-resource-reference.md#resource-logs)

> [!IMPORTANT]
> Voor het inschakelen van deze instellingen zijn extra Azure-services vereist (opslagaccount, Event Hub of Log Analytics), waardoor uw kosten mogelijk toenemen. Ga naar de Azure-prijscalculator om de geschatte [kosten te berekenen.](https://azure.microsoft.com/pricing/calculator)

U kunt de volgende logboeken configureren voor Azure Machine Learning:

| Categorie | Beschrijving |
|:---|:---|
| AmlComputeClusterEvent | Gebeurtenissen uit Azure Machine Learning rekenclusters. |
| AmlComputeClusterNodeEvent | Gebeurtenissen van knooppunten in een Azure Machine Learning rekencluster. |
| AmlComputeJobEvent | Gebeurtenissen van taken die worden uitgevoerd op Azure Machine Learning compute. |

> [!NOTE]
> Wanneer u metrische gegevens in een diagnostische instelling inschakelen, wordt dimensie-informatie momenteel niet opgenomen als onderdeel van de informatie die wordt verzonden naar een opslagaccount, Event Hub of Log Analytics.

De metrische gegevens en logboeken die u kunt verzamelen, worden in de volgende secties besproken.

## <a name="analyzing-metrics"></a>Metrische gegevens analyseren

U kunt metrische gegevens voor Azure Machine Learning, samen met metrische gegevens van andere Azure-services, analyseren door Metrische gegevens te openen **Azure Monitor** **menu.** Zie [Aan de slag met Azure Metrics Explorer](../azure-monitor/essentials/metrics-getting-started.md) voor meer informatie over het gebruik van dit hulpprogramma.

Zie Monitoring Azure Machine Learning data reference metrics (Metrische gegevens voor gegevensverwijzing bewaken) Azure Machine Learning een lijst met verzamelde [platformgegevens.](monitor-resource-reference.md#metrics)

Alle metrische gegevens voor Azure Machine Learning staan in de naamruimte **Machine Learning Service-werkruimte**.

![Metrics Explorer met Machine Learning servicewerkruimte geselecteerd](./media/monitor-azure-machine-learning/metrics.png)

Ter referentie ziet u een lijst met alle metrische resourcegegevens die worden [ondersteund in Azure Monitor.](../azure-monitor/essentials/metrics-supported.md)

> [!TIP]
> Azure Monitor metrische gegevens zijn 90 dagen beschikbaar. Bij het maken van grafieken kan echter slechts 30 dagen worden gevisualiseerd. Als u bijvoorbeeld een periode van 90 dagen wilt visualiseren, moet u deze op in drie grafieken van 30 dagen binnen de periode van 90 dagen.
### <a name="filtering-and-splitting"></a>Filteren en splitsen

Voor metrische gegevens die dimensies ondersteunen, kunt u filters toepassen met behulp van een dimensiewaarde. U kunt bijvoorbeeld **Actieve kernen filteren op** de **clusternaam** `cpu-cluster` . 

U kunt ook een metrische gegevens opsplitsen per dimensie om te visualiseren hoe verschillende segmenten van de metrische gegevens met elkaar vergelijken. U kunt bijvoorbeeld het type **pijplijnstap uitsplitsen** om het aantal typen stappen te zien dat in de pijplijn wordt gebruikt.

Zie Geavanceerde functies van Azure Monitor voor meer informatie [over filteren en splitsen.](../azure-monitor/essentials/metrics-charts.md)

<a id="analyzing-log-data"></a>
## <a name="analyzing-logs"></a>Logboeken analyseren

Als u Azure Monitor Log Analytics gebruikt, moet u een diagnostische configuratie maken en __Gegevens verzenden naar Log Analytics inschakelen.__ Zie de sectie Verzameling en [routering voor meer](#collection-and-routing) informatie.

Gegevens in Azure Monitor logboeken worden opgeslagen in tabellen, met elke tabel een eigen set unieke eigenschappen. Azure Machine Learning slaat gegevens op in de volgende tabellen:

| Tabel | Beschrijving |
|:---|:---|
| AmlComputeClusterEvent | Gebeurtenissen uit Azure Machine Learning rekenclusters. |
| AmlComputeClusterNodeEvent | Gebeurtenissen van knooppunten in een Azure Machine Learning rekencluster. |
| AmlComputeJobEvent | Gebeurtenissen van taken die worden uitgevoerd op Azure Machine Learning compute. |

> [!IMPORTANT]
> Wanneer u **Logboeken selecteert** in Azure Machine Learning menu, wordt Log Analytics geopend met het querybereik ingesteld op de huidige werkruimte. Dit betekent dat logboekquery's alleen gegevens uit die resource bevatten. Als u een query wilt uitvoeren die gegevens uit andere databases of gegevens van andere Azure-services bevat, selecteert u **Logboeken** in **het Azure Monitor** menu. Zie [Logboekquerybereik en tijdsbereik in Azure Monitor Log Analytics voor](../azure-monitor/logs/scope.md) meer informatie.

Zie bewakingsgegevensreferenties voor Azure Machine Learning gedetailleerde naslaginformatie over de [logboeken en metrische gegevens.](monitor-resource-reference.md)

### <a name="sample-kusto-queries"></a>Kusto-voorbeeldquery's

> [!IMPORTANT]
> Wanneer u **Logboeken selecteert** in het menu [servicenaam], wordt Log Analytics geopend met het querybereik ingesteld op de Azure Machine Learning werkruimte. Dit betekent dat logboekquery's alleen gegevens uit die resource bevatten. Als u een query wilt uitvoeren die gegevens uit andere werkruimten of gegevens van andere Azure-services bevat, selecteert u **Logboeken** in **het Azure Monitor** menu. Zie [Logboekquerybereik en tijdsbereik in Azure Monitor Log Analytics voor](../azure-monitor/logs/scope.md) meer informatie.

Hieronder vindt u query's die u kunt gebruiken om uw resources Azure Machine Learning bewaken: 

+ Mislukte taken in de afgelopen vijf dagen:

    ```Kusto
    AmlComputeJobEvent
    | where TimeGenerated > ago(5d) and EventType == "JobFailed"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ Records voor een specifieke taaknaam op halen:

    ```Kusto
    AmlComputeJobEvent
    | where JobName == "automl_a9940991-dedb-4262-9763-2fd08b79d8fb_setup"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ Ontvang clustergebeurtenissen in de afgelopen vijf dagen voor clusters waarbij de VM-grootte Standard_D1_V2:

    ```Kusto
    AmlComputeClusterEvent
    | where TimeGenerated > ago(4d) and VmSize == "STANDARD_D1_V2"
    | project  ClusterName , InitialNodeCount , MaximumNodeCount , QuotaAllocated , QuotaUtilized
    ```

+ Knooppunten toewijzen in de afgelopen acht dagen:

    ```Kusto
    AmlComputeClusterNodeEvent
    | where TimeGenerated > ago(8d) and NodeAllocationTime  > ago(8d)
    | distinct NodeId
    ```

## <a name="alerts"></a>Waarschuwingen

U kunt waarschuwingen voor Azure Machine Learning openen door **Waarschuwingen te openen** in **Azure Monitor** menu. Zie [Metrische waarschuwingen maken, weergeven en beheren met behulp](../azure-monitor/alerts/alerts-metric.md) van Azure Monitor voor meer informatie over het maken van waarschuwingen.

De volgende tabel bevat algemene en aanbevolen regels voor metrische waarschuwingen voor Azure Machine Learning:

| Waarschuwingstype | Voorwaarde | Description |
|:---|:---|:---|
| Model implementeren is mislukt | Aggregatietype: Totaal, Operator: Groter dan, Drempelwaarde: 0 | Wanneer een of meer modelimplementaties zijn mislukt |
| Percentage quotumgebruik | Aggregatietype: Gemiddelde, Operator: Groter dan, Drempelwaarde: 90| Wanneer het quotumgebruikspercentage hoger is dan 90% |
| Onbruikbaar knooppunten | Aggregatietype: Totaal, Operator: Groter dan, Drempelwaarde: 0 | Wanneer er een of meer onbruikbaar knooppunten zijn |

## <a name="next-steps"></a>Volgende stappen

- Zie Monitoring Azure Machine Learning data reference (Controle van gegevens) [Azure Machine Learning naslaginformatie over de logboeken en metrische gegevens.](monitor-resource-reference.md)
- Zie Quota voor [Azure-resources](how-to-manage-quotas.md)beheren en aanvragen Azure Machine Learning informatie over het werken met quota met betrekking tot Azure Machine Learning.
- Zie Azure-resources bewaken met azure-resources met Azure Monitor voor [meer Azure Monitor.](../azure-monitor/essentials/monitor-azure-resource.md)
