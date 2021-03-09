---
title: Azure Machine Learning gegevens referentie bewaken | Microsoft Docs
description: Referentie documentatie voor het bewaken van Azure Machine Learning. Meer informatie over de gegevens & verzamelde en beschik bare resources in Azure Monitor.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.custom: subject-monitoring
ms.date: 10/02/2020
ms.openlocfilehash: f130fc0c65c49c33c838812fc2758619e0d1bca0
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102521336"
---
# <a name="monitoring-azure-machine-learning-data-reference"></a>Naslag informatie over Azure machine learning-gegevens bewaken

Meer informatie over de gegevens en resources die worden verzameld door Azure Monitor van uw Azure Machine Learning-werk ruimte. Zie [bewaking Azure machine learning](monitor-azure-machine-learning.md) voor meer informatie over het verzamelen en analyseren van bewakings gegevens.

## <a name="metrics"></a>Metrische gegevens

In deze sectie vindt u alle automatisch verzamelde platform gegevens die zijn verzameld voor Azure Machine Learning. De resource provider voor deze metrische gegevens is [micro soft. MachineLearningServices/Workspaces](../azure-monitor/essentials/metrics-supported.md#microsoftmachinelearningservicesworkspaces).

**Model**

| Metrisch | Eenheid | Beschrijving |
| ----- | ----- | ----- |
| Model implementeren is mislukt | Count | Het aantal model implementaties dat is mislukt. |
| Model implementeren gestart | Count | Het aantal model implementaties is gestart. |
| Model implementeren is voltooid | Count | Het aantal model implementaties dat is geslaagd. |
| Model register mislukt | Count | Het aantal model registraties dat is mislukt. |
| Model registratie is voltooid | Count | Het aantal model registraties dat is geslaagd. |

**Quota**

Quota gegevens zijn alleen voor het berekenen van Azure Machine Learning.

| Metrisch | Eenheid | Beschrijving |
| ----- | ----- | ----- |
| Actieve kernen | Count | Het aantal actieve reken kernen. |
| Actieve knoop punten | Count | Het aantal actieve knoop punten. |
| Niet-actieve kernen | Count | Het aantal niet-actieve Compute-kernen. |
| Niet-actieve knoop punten | Count | Het aantal niet-actieve reken knooppunten. |
| Kernen verlaten | Count | Het aantal Leave-kernen. |
| Knoop punten verlaten | Count | Het aantal knoop punten dat de poort verlaat. |
| Afgebroken kernen | Count | Het aantal afgebroken kernen. |
| Knoop punten die zijn afgebroken | Count | Het aantal knoop punten dat is afgebroken. |
| Percentage quotum gebruik | Percentage | Het percentage van de gebruikte quota. |
| Totaal aantal cores | Count | Het totale aantal kernen. |
| Totaal aantal knoop punten | Count | Het totale aantal knoop punten. |
| Onbruikbaar aantal kern geheugens | Count | Het aantal onbruikbaar kern geheugens. |
| Niet-bruikbare knoop punten | Count | Het aantal niet-bruikbare knoop punten. |

**Resource**

| Metrisch | Eenheid | Beschrijving |
| ----- | ----- | ----- |
| CpuUtilization | Percentage | Hoeveel procent van de CPU voor een bepaald knoop punt is gebruikt tijdens een uitvoering/taak. Deze metrische gegevens worden alleen gepubliceerd wanneer een taak wordt uitgevoerd op een knoop punt. Eén taak kan gebruikmaken van een of meer knoop punten. Deze metriek wordt per knoop punt gepubliceerd. |
| GpuUtilization | Percentage | Hoeveel percentage van de GPU voor een bepaald knoop punt is gebruikt tijdens een uitvoering/taak. Eén knoop punt kan een of meer Gpu's hebben. Deze metrische gegevens worden per GPU per knoop punt gepubliceerd. |

**Uitvoeren**

Informatie over trainings uitvoeringen.

| Metrisch | Eenheid | Beschrijving |
| ----- | ----- | ----- |
| Voltooide uitvoeringen | Count | Het aantal voltooide uitvoeringen. |
| Mislukte uitvoeringen | Count | Het aantal mislukte uitvoeringen. |
| Gestart uitvoeringen | Count | Het aantal gestarte uitvoeringen. |

## <a name="metric-dimensions"></a>Metrische dimensies

Zie [multidimensionale metrische](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics)gegevens voor meer informatie over de metrieke dimensies.

Azure Machine Learning heeft de volgende dimensies die zijn gekoppeld aan de metrische gegevens.

| Dimensie | Beschrijving |
| ---- | ---- |
| Clusternaam | De naam van de bron van het berekenings cluster. Beschikbaar voor alle quota-metrische gegevens. |
| Naam van VM-familie | De naam van de VM-familie die door het cluster wordt gebruikt. Beschikbaar voor percentage quotum gebruik. |
| VM-prioriteit | De prioriteit van de virtuele machine. Beschikbaar voor percentage quotum gebruik.
| CreatedTime | Alleen beschikbaar voor CpuUtilization en GpuUtilization. |
| DeviceId | ID van het apparaat (GPU). Alleen beschikbaar voor GpuUtilization. |
| NodeId | De ID van het knoop punt dat is gemaakt waarbij de taak wordt uitgevoerd. Alleen beschikbaar voor CpuUtilization en GpuUtilization. |
| RunId | ID van de uitvoeringsrun/taak. Alleen beschikbaar voor CpuUtilization en GpuUtilization. |
| ComputeType | Het reken type dat wordt gebruikt voor de uitvoering. Alleen beschikbaar voor voltooide uitvoeringen, mislukte uitvoeringen en gestarte uitvoeringen. |
| PipelineStepType | Het type [PipelineStep](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep) dat wordt gebruikt in de uitvoering. Alleen beschikbaar voor voltooide uitvoeringen, mislukte uitvoeringen en gestarte uitvoeringen. |
| PublishedPipelineId | De ID van de gepubliceerde pijp lijn die wordt gebruikt in de uitvoering. Alleen beschikbaar voor voltooide uitvoeringen, mislukte uitvoeringen en gestarte uitvoeringen. |
| RunType | Het type uitvoering. Alleen beschikbaar voor voltooide uitvoeringen, mislukte uitvoeringen en gestarte uitvoeringen. |

De geldige waarden voor de dimensie RunType zijn:

| Waarde | Beschrijving |
| ----- | ----- |
| Experiment | Niet-pijplijn uitvoeringen. |
| PipelineRun | Een pijplijn uitvoering, het bovenliggende element van een StepRun. |
| StepRun | Een uitvoering voor een pijplijn stap. |
| ReusedStepRun | Een uitvoering van een pijplijn stap die een eerdere uitvoering opnieuw gebruikt. |

## <a name="activity-log"></a>Activiteitenlogboek

De volgende tabel geeft een lijst van de bewerkingen met betrekking tot Azure Machine Learning die in het activiteiten logboek kunnen worden gemaakt.

| Bewerking | Beschrijving |
|:---|:---|
| Hiermee wordt een Machine Learning werkruimte gemaakt of bijgewerkt | Er is een werk ruimte gemaakt of bijgewerkt |
| CheckComputeNameAvailability | Controleren of een berekenings naam al wordt gebruikt |
| Hiermee worden de reken resources gemaakt of bijgewerkt | Er is een compute-resource gemaakt of bijgewerkt |
| Hiermee worden de reken resources verwijderd | Er is een compute-resource verwijderd |
| Geheimen vermelden | Voor een in de bewerking vermelde geheimen voor een Machine Learning-werk ruimte |

## <a name="resource-logs"></a>Resourcelogboeken

In deze sectie vindt u de typen bron logboeken die u kunt verzamelen voor Azure Machine Learning-werk ruimte.

Resource provider en type: [micro soft. MachineLearningServices/Workspace](../azure-monitor/essentials/resource-logs-categories.md#microsoftmachinelearningservicesworkspaces).

| Categorie | Weergavenaam |
| ----- | ----- |
| AmlComputeClusterEvent | AmlComputeClusterEvent |
| AmlComputeClusterNodeEvent | AmlComputeClusterNodeEvent |
| AmlComputeCpuGpuUtilization | AmlComputeCpuGpuUtilization |
| AmlComputeJobEvent | AmlComputeJobEvent |
| AmlRunStatusChangedEvent | AmlRunStatusChangedEvent |

## <a name="schemas"></a>Schema 's

De volgende schema's zijn in gebruik door Azure Machine Learning

### <a name="amlcomputejobevents-table"></a>AmlComputeJobEvents-tabel

| Eigenschap | Beschrijving |
|:--- |:---|
| TimeGenerated | Tijdstip waarop de logboek vermelding is gegenereerd |
| OperationName | De naam van de bewerking die is gekoppeld aan de logboek gebeurtenis |
| Categorie | De naam van de logboek gebeurtenis, AmlComputeClusterNodeEvent |
| JobId | ID van de verzonden taak |
| ExperimentId | ID van het experiment |
| Experimentnaam | De naam van het experiment |
| CustomerSubscriptionId | SubscriptionId waar experiment en job worden verzonden |
| WorkspaceName | De naam van de machine learning-werk ruimte |
| Clusternaam | De naam van het cluster |
| ProvisioningState | Status van het verzenden van taken |
| ResourceGroupName | Naam van de resource groep |
| JobName | De naam van de taak |
| ClusterId | ID van het cluster |
| EventType | Het type taak gebeurtenis. Bijvoorbeeld, JobSubmitted, JobRunning, JobFailed, JobSucceeded. |
| ExecutionState | De status van de taak (de uitvoering). Bijvoorbeeld in de wachtrij, actief, geslaagd, mislukt |
| ErrorDetails | Details van taak fout |
| CreationApiVersion | API-versie die wordt gebruikt om de taak te maken |
| ClusterResourceGroupName | De naam van de resource groep van het cluster |
| TFWorkerCount | Aantal TF-werk rollen |
| TFParameterServerCount | Aantal TF para meter server |
| ToolType | Type programma dat wordt gebruikt |
| RunInContainer | Vlag waarmee wordt beschreven of de taak moet worden uitgevoerd in een container |
| JobErrorMessage | gedetailleerd bericht van taak fout |
| NodeId | De ID van het knoop punt dat is gemaakt waarbij de taak wordt uitgevoerd |

### <a name="amlcomputeclusterevents-table"></a>AmlComputeClusterEvents-tabel

| Eigenschap | Beschrijving |
|:--- |:--- |
| TimeGenerated | Tijdstip waarop de logboek vermelding is gegenereerd |
| OperationName | De naam van de bewerking die is gekoppeld aan de logboek gebeurtenis |
| Categorie | De naam van de logboek gebeurtenis, AmlComputeClusterNodeEvent |
| ProvisioningState | De inrichtings status van het cluster |
| Clusternaam | De naam van het cluster |
| ClusterType | Type van het cluster |
| Type CreatedBy | Gebruiker die het cluster heeft gemaakt |
| CoreCount | Aantal kernen in het cluster |
| VmSize | VM-grootte van het cluster |
| VmPriority | Prioriteit van de knoop punten die zijn gemaakt in een cluster toegewezen-LowPriority |
| ScalingType | Type hand matig/automatisch schalen cluster |
| InitialNodeCount | Oorspronkelijk aantal knoop punten van het cluster |
| MinimumNodeCount | Minimum aantal knoop punten van het cluster |
| MaximumNodeCount | Maximum aantal knoop punten van het cluster |
| NodeDeallocationOption | Hoe de toewijzing van het knoop punt moet worden opgeheven |
| Publisher | Uitgever van het cluster type |
| Aanbieding | Aanbod waarmee het cluster is gemaakt |
| Sku | SKU van het knoop punt/de virtuele machine die in het cluster is gemaakt |
| Versie | Versie van de installatie kopie die wordt gebruikt tijdens het maken van het knoop punt/VM |
| SubnetId | SubnetId van het cluster |
| AllocationState | Cluster toewijzings status |
| CurrentNodeCount | Huidig aantal knoop punten van het cluster |
| TargetNodeCount | Aantal doel knooppunten van het cluster tijdens het omhoog/omlaag schalen |
| EventType | Type gebeurtenis tijdens het maken van het cluster. |
| NodeIdleTimeSecondsBeforeScaleDown | Niet-actieve tijd in seconden voordat het cluster omlaag wordt geschaald |
| PreemptedNodeCount | Telling van het aantal knoop punten van het cluster |
| IsResizeGrow | Vlag waarmee wordt aangegeven dat het cluster omhoog wordt geschaald |
| VmFamilyName | De naam van de VM-serie van de knoop punten die in het cluster kunnen worden gemaakt |
| LeavingNodeCount | Het aantal knoop punten van het cluster verlaten |
| UnusableNodeCount | Onbruikbaar aantal knoop punten van het cluster |
| IdleNodeCount | Aantal niet-actieve knoop punten van het cluster |
| RunningNodeCount | Aantal actieve knoop punten van het cluster |
| PreparingNodeCount | Het aantal knoop punten van het cluster voorbereiden |
| QuotaAllocated | Toegewezen quotum aan het cluster |
| QuotaUtilized | Gebruikt quotum van het cluster |
| AllocationStateTransitionTime | Overgangs tijd van de ene status naar de andere |
| ClusterErrorCodes | Fout code die wordt ontvangen tijdens het maken of schalen van het cluster |
| CreationApiVersion | API-versie die wordt gebruikt tijdens het maken van het cluster |

### <a name="amlcomputeclusternodeevents-table"></a>AmlComputeClusterNodeEvents-tabel

| Eigenschap | Beschrijving |
|:--- |:--- |
| TimeGenerated | Tijdstip waarop de logboek vermelding is gegenereerd |
| OperationName | De naam van de bewerking die is gekoppeld aan de logboek gebeurtenis |
| Categorie | De naam van de logboek gebeurtenis, AmlComputeClusterNodeEvent |
| Clusternaam | De naam van het cluster |
| NodeId | ID van het cluster knooppunt dat is gemaakt |
| VmSize | VM-grootte van het knoop punt |
| VmFamilyName | De VM-familie waartoe het knoop punt behoort |
| VmPriority | Prioriteit van het knoop punt dat speciaal is gemaakt/LowPriority |
| Publisher | Uitgever van de VM-installatie kopie. Bijvoorbeeld micro soft-dsvm |
| Aanbieding | Aanbieding gekoppeld aan het maken van een virtuele machine |
| Sku | SKU van het knoop punt/VM gemaakt |
| Versie | Versie van de installatie kopie die wordt gebruikt tijdens het maken van het knoop punt/VM |
| ClusterCreationTime | Tijdstip waarop het cluster is gemaakt |
| ResizeStartTime | Tijdstip waarop het omhoog/omlaag schalen van het cluster is gestart |
| ResizeEndTime | Tijd waarop het omhoog/omlaag schalen van het cluster is beëindigd |
| NodeAllocationTime | Tijdstip waarop het knoop punt is toegewezen |
| NodeBootTime | Tijdstip waarop het knoop punt is opgestart |
| StartTaskStartTime | Tijd waarop de taak is toegewezen aan een knoop punt en is gestart |
| StartTaskEndTime | Tijd waarop de taak die aan een knoop punt is toegewezen, is beëindigd |
| TotalE2ETimeInSeconds | Het totale tijds knooppunt is actief |


## <a name="see-also"></a>Zie ook

- Zie [bewaking Azure machine learning](monitor-azure-machine-learning.md) voor een beschrijving van de bewakings Azure machine learning.
- Zie [Azure-resources bewaken met Azure monitor](../azure-monitor/essentials/monitor-azure-resource.md) voor meer informatie over het bewaken van Azure-resources.
