---
title: Bewakings Azure Machine Learning | Microsoft Docs
description: Meer informatie over het gebruik van Azure Monitor voor het weer geven, analyseren en maken van waarschuwingen over metrische gegevens van Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.custom: subject-monitoring
ms.date: 10/01/2020
ms.openlocfilehash: a18ee02b5e91b628a25655949a652270bd7436c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "100575143"
---
# <a name="monitor-azure-machine-learning"></a>Azure Machine Learning bewaken

Wanneer u belang rijke toepassingen en bedrijfs processen hebt die afhankelijk zijn van Azure-resources, wilt u deze resources controleren op hun Beschik baarheid, prestaties en werking. In dit artikel worden de bewakings gegevens beschreven die worden gegenereerd door Azure Machine Learning en hoe u deze gegevens kunt analyseren en er waarschuwingen op kunt volgen met behulp van Azure Monitor.

> [!TIP]
> De informatie in dit document is hoofd zakelijk bedoeld voor __beheerders__, omdat hierin de bewaking voor de Azure machine learning-service en de bijbehorende Azure-Services wordt beschreven. Als u een __Data-wetenschapper__ of- __ontwikkelaar__ bent en informatie wilt bewaken die specifiek is voor uw *model training-uitvoeringen*, raadpleegt u de volgende documenten:
>
> * [Trainings uitvoeringen starten, controleren en annuleren](how-to-manage-runs.md)
> * [Metrische gegevens registreren voor trainingsuitvoeringen](how-to-track-experiments.md)
> * [Experimenten bijhouden met MLflow](how-to-use-mlflow.md)
> * [Uitvoeringen visualiseren met TensorBoard](how-to-monitor-tensorboard.md)
>
> Als u gegevens wilt bewaken die zijn gegenereerd door modellen die zijn geïmplementeerd als webservices of IoT Edge modules, raadpleegt u [model gegevens verzamelen](how-to-enable-data-collection.md) en [controleren met Application Insights](how-to-enable-app-insights.md).

## <a name="what-is-azure-monitor"></a>Wat is Azure Monitor?

Azure Machine Learning maakt bewakings gegevens met behulp van [Azure monitor](../azure-monitor/overview.md). Dit is een volledige stack monitoring-service in Azure. Azure Monitor biedt een volledige set functies voor het bewaken van uw Azure-resources. Het kan ook resources in andere Clouds en on-premises bewaken.

Begin met het artikel [bewaking van Azure-resources met Azure monitor](../azure-monitor/essentials/monitor-azure-resource.md), waarin de volgende concepten worden beschreven:

- Wat is Azure Monitor?
- Kosten die zijn gekoppeld aan bewaking
- Bewaken van gegevens die zijn verzameld in azure
- Gegevens verzameling configureren
- Standaard Programma's in azure voor het analyseren en waarschuwen van bewakings gegevens

In de volgende secties vindt u een beschrijving van de specifieke gegevens die zijn verzameld voor Azure Machine Learning. In deze secties vindt u ook voor beelden voor het configureren van gegevens verzameling en het analyseren van deze gegevens met Azure-hulpprogram ma's.

> [!TIP]
> Zie [verbruik en geschatte kosten](../azure-monitor//usage-estimated-costs.md)voor meer informatie over de kosten die zijn gekoppeld aan Azure monitor. Zie [gegevens opname tijd vastleggen](../azure-monitor/logs/data-ingestion-time.md)voor meer informatie over de tijd die nodig is om uw gegevens weer te geven in azure monitor.

## <a name="monitoring-data-from-azure-machine-learning"></a>Gegevens van Azure Machine Learning bewaken

Azure Machine Learning worden dezelfde soorten bewakings gegevens verzameld als andere Azure-resources die worden beschreven in [gegevens van Azure-resources bewaken](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data). 

Zie [Azure machine learning monitoring data Naslag informatie](monitor-resource-reference.md) voor een gedetailleerde Naslag informatie over de logboeken en metrische gegevens die door Azure machine learning zijn gemaakt.

<a id="configuration"></a>

## <a name="collection-and-routing"></a>Verzameling en route ring

De metrische gegevens van het platform en het activiteiten logboek worden automatisch verzameld en opgeslagen, maar kunnen worden doorgestuurd naar andere locaties met behulp van een diagnostische instelling.  

Bron logboeken worden pas verzameld en opgeslagen als u een diagnostische instelling hebt gemaakt en deze naar een of meer locaties wilt door sturen.

Zie [Diagnostische instelling maken voor het verzamelen van platform logboeken en metrische gegevens in azure](../azure-monitor/essentials/diagnostic-settings.md) voor het gedetailleerde proces voor het maken van een diagnostische instelling met behulp van de Azure Portal, CLI of Power shell. Wanneer u een diagnostische instelling maakt, geeft u op welke categorieën logboeken u wilt verzamelen. De categorieën voor Azure Machine Learning worden vermeld in [Azure machine learning bewakings gegevens referentie](monitor-resource-reference.md#resource-logs).

> [!IMPORTANT]
> Het inschakelen van deze instellingen vereist extra Azure-Services (opslag account, Event Hub of Log Analytics), waardoor uw kosten kunnen worden verhoogd. Ga naar de [Azure-prijs calculator](https://azure.microsoft.com/pricing/calculator)om een geschatte kosten te berekenen.

U kunt de volgende logboeken configureren voor Azure Machine Learning:

| Categorie | Beschrijving |
|:---|:---|
| AmlComputeClusterEvent | Gebeurtenissen van Azure Machine Learning Reken clusters. |
| AmlComputeClusterNodeEvent | Gebeurtenissen van knoop punten binnen een Azure Machine Learning Compute-Cluster. |
| AmlComputeJobEvent | Gebeurtenissen van taken die worden uitgevoerd op Azure Machine Learning compute. |

> [!NOTE]
> Wanneer u metrische gegevens in een diagnostische instelling inschakelt, wordt er momenteel geen dimensie-informatie opgenomen als onderdeel van de gegevens die worden verzonden naar een opslag account, Event Hub of log Analytics.

De metrische gegevens en logboeken die u kunt verzamelen, worden besproken in de volgende secties.

## <a name="analyzing-metrics"></a>Metrische gegevens analyseren

U kunt metrische gegevens voor Azure Machine Learning analyseren, samen met metrische gegevens uit andere Azure-Services, door **metrische gegevens** te openen in het menu **Azure monitor** . Zie [aan de slag met Azure Metrics Explorer](../azure-monitor/essentials/metrics-getting-started.md) voor meer informatie over het gebruik van dit hulp programma.

Zie voor een lijst met de metrische gegevens van de platformen [bewaken Azure machine learning data referentie-metrieken](monitor-resource-reference.md#metrics).

Alle metrische gegevens voor Azure Machine Learning bevinden zich in de naam ruimte **machine learning service werkruimte**.

![Metrics Explorer met Machine Learning service werkruimte geselecteerd](./media/monitor-azure-machine-learning/metrics.png)

Ter referentie ziet u een lijst met [alle metrische resource gegevens die worden ondersteund in azure monitor](../azure-monitor/essentials/metrics-supported.md).

> [!TIP]
> Azure Monitor metrische gegevens zijn 90 dagen beschikbaar. Bij het maken van grafieken kan echter slechts 30 dagen worden gevisualiseerd. Als u bijvoorbeeld een periode van 90 dagen wilt visualiseren, moet u deze opsplitsen in drie grafieken van 30 dagen binnen de periode van 90 dagen.
### <a name="filtering-and-splitting"></a>Filteren en splitsen

Voor metrische gegevens die dimensies ondersteunen, kunt u filters toep assen met behulp van een dimensie waarde. U kunt bijvoorbeeld **actieve kernen** filteren op een **cluster naam** van `cpu-cluster` . 

U kunt ook een metriek op dimensie splitsen om te visualiseren hoe verschillende segmenten van de metrische gegevens met elkaar worden vergeleken. U kunt bijvoorbeeld het **stap type voor de pijp lijn** opsplitsen om een telling te zien van de typen stappen die worden gebruikt in de pijp lijn.

Zie [geavanceerde functies van Azure monitor](../azure-monitor/essentials/metrics-charts.md)voor meer informatie over filteren en splitsen.

<a id="analyzing-log-data"></a>
## <a name="analyzing-logs"></a>Logboeken analyseren

Als u Azure Monitor Log Analytics wilt gebruiken, moet u een diagnostische configuratie maken en __gegevens verzenden naar log Analytics__ inschakelen. Zie de sectie [verzameling en route ring](#collection-and-routing) voor meer informatie.

Gegevens in Azure Monitor logboeken worden opgeslagen in tabellen, waarbij elke tabel een eigen set unieke eigenschappen heeft. Azure Machine Learning slaat gegevens op in de volgende tabellen:

| Tabel | Beschrijving |
|:---|:---|
| AmlComputeClusterEvent | Gebeurtenissen van Azure Machine Learning Reken clusters. |
| AmlComputeClusterNodeEvent | Gebeurtenissen van knoop punten binnen een Azure Machine Learning Compute-Cluster. |
| AmlComputeJobEvent | Gebeurtenissen van taken die worden uitgevoerd op Azure Machine Learning compute. |

> [!IMPORTANT]
> Wanneer u **Logboeken** in het menu Azure machine learning selecteert, wordt log Analytics geopend met het query bereik dat is ingesteld op de huidige werk ruimte. Dit betekent dat logboek query's alleen gegevens van die bron bevatten. Als u een query wilt uitvoeren die gegevens uit andere data bases of gegevens uit andere Azure-Services bevat, selecteert u **Logboeken** in het **Azure monitor** menu. Zie de [logboek query bereik en het tijds bereik in Azure Monitor Log Analytics](../azure-monitor/logs/scope.md) voor meer informatie.

Zie [Azure machine learning monitoring data Reference](monitor-resource-reference.md)(Engelstalig) voor een gedetailleerde Naslag informatie over de logboeken en metrische gegevens.

### <a name="sample-kusto-queries"></a>Voor beeld van Kusto-query's

> [!IMPORTANT]
> Wanneer u **Logboeken** selecteert in het menu [service-name], wordt log Analytics geopend met het query bereik dat is ingesteld op de huidige Azure machine learning werk ruimte. Dit betekent dat logboek query's alleen gegevens van die bron bevatten. Als u een query wilt uitvoeren die gegevens uit andere werk ruimten of gegevens uit andere Azure-Services bevat, selecteert u **Logboeken** in het **Azure monitor** menu. Zie de [logboek query bereik en het tijds bereik in Azure Monitor Log Analytics](../azure-monitor/logs/scope.md) voor meer informatie.

Hieronder vindt u query's die u kunt gebruiken om uw Azure Machine Learning-resources te bewaken: 

+ Mislukte taken in de afgelopen vijf dagen ophalen:

    ```Kusto
    AmlComputeJobEvent
    | where TimeGenerated > ago(5d) and EventType == "JobFailed"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ Records ophalen voor een specifieke taak naam:

    ```Kusto
    AmlComputeJobEvent
    | where JobName == "automl_a9940991-dedb-4262-9763-2fd08b79d8fb_setup"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ In de afgelopen vijf dagen cluster gebeurtenissen ophalen voor clusters waarin de VM-grootte is Standard_D1_V2:

    ```Kusto
    AmlComputeClusterEvent
    | where TimeGenerated > ago(4d) and VmSize == "STANDARD_D1_V2"
    | project  ClusterName , InitialNodeCount , MaximumNodeCount , QuotaAllocated , QuotaUtilized
    ```

+ Knoop punten ophalen die zijn toegewezen in de afgelopen acht dagen:

    ```Kusto
    AmlComputeClusterNodeEvent
    | where TimeGenerated > ago(8d) and NodeAllocationTime  > ago(8d)
    | distinct NodeId
    ```

## <a name="alerts"></a>Waarschuwingen

U krijgt toegang tot waarschuwingen voor Azure Machine Learning door **waarschuwingen** te openen in het menu **Azure monitor** . Zie [metrische waarschuwingen maken, weer geven en beheren met behulp van Azure monitor](../azure-monitor/alerts/alerts-metric.md) voor meer informatie over het maken van waarschuwingen.

De volgende tabel bevat algemene en aanbevolen waarschuwings regels voor metrische gegevens voor Azure Machine Learning:

| Waarschuwingstype | Voorwaarde | Description |
|:---|:---|:---|
| Modelimplementatie is mislukt | Aggregatie type: Total, operator: groter dan, drempel waarde: 0 | Wanneer een of meer model implementaties zijn mislukt |
| Percentage quotum gebruik | Aggregatie type: Average, operator: groter dan, drempel waarde: 90| Wanneer het percentage van het quota gebruik groter is dan 90% |
| Niet-bruikbare knoop punten | Aggregatie type: Total, operator: groter dan, drempel waarde: 0 | Wanneer er een of meer niet-bruikbare knoop punten zijn |

## <a name="next-steps"></a>Volgende stappen

- Zie [Monitoring Azure machine learning data Reference](monitor-resource-reference.md)voor een verwijzing naar de logboeken en metrische gegevens.
- Zie voor meer informatie over het werken met quota's die betrekking hebben op Azure Machine Learning, [Quota's beheren en aanvragen voor Azure-resources](how-to-manage-quotas.md).
- Zie [Azure-resources bewaken met Azure monitor](../azure-monitor/essentials/monitor-azure-resource.md)voor meer informatie over het bewaken van Azure-resources.
