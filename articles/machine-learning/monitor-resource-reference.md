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
ms.date: 04/07/2021
ms.openlocfilehash: de4d934144d6721db8c00d7199061842e518e44f
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107031066"
---
# <a name="monitoring-azure-machine-learning-data-reference"></a>Naslag informatie over Azure machine learning-gegevens bewaken

Meer informatie over de gegevens en resources die worden verzameld door Azure Monitor van uw Azure Machine Learning-werk ruimte. Zie [bewaking Azure machine learning](monitor-azure-machine-learning.md) voor meer informatie over het verzamelen en analyseren van bewakings gegevens.

## <a name="metrics"></a>Metrische gegevens

In deze sectie vindt u alle automatisch verzamelde platform gegevens die zijn verzameld voor Azure Machine Learning. De resource provider voor deze metrische gegevens is [micro soft. MachineLearningServices/Workspaces](../azure-monitor/essentials/metrics-supported.md#microsoftmachinelearningservicesworkspaces).

**Model**

| Metrisch | Eenheid | Description |
|--|--|--|
| Model registratie is voltooid | Count | Aantal model registraties dat is geslaagd in deze werk ruimte |
| Model register mislukt | Count | Aantal model registraties dat is mislukt in deze werk ruimte |
| Modelimplementatie gestart | Count | Aantal model implementaties gestart in deze werk ruimte |
| Modelimplementatie geslaagd | Count | Aantal model implementaties dat is geslaagd in deze werk ruimte |
| Modelimplementatie is mislukt | Count | Aantal model implementaties die zijn mislukt in deze werk ruimte |

**Quota**

Quota gegevens zijn alleen voor het berekenen van Azure Machine Learning.

| Metrisch | Eenheid | Description |
|--|--|--|
| Totaal aantal knoop punten | Count | Totaal aantal knoop punten. Dit totaal omvat enkele actieve knoop punten, niet-actieve knoop punten, niet-bruikbare knoop punten, afgebroken knoop punten, waardoor knoop punten |
| Actieve knoop punten | Count | Aantal actieve knoop punten. De knoop punten waarop een taak actief wordt uitgevoerd. |
| Niet-actieve knoop punten | Count | Aantal niet-actieve knoop punten. Niet-actieve knoop punten zijn de knoop punten waarop geen taken worden uitgevoerd, maar u kunt wel een nieuwe taak accepteren, indien beschikbaar. |
| Niet-bruikbare knoop punten | Count | Aantal niet-bruikbare knoop punten. Niet-bruikbare knoop punten zijn niet functioneel vanwege een probleem met onherleidbare. Deze knoop punten worden door Azure gerecycled. |
| Knoop punten die zijn afgebroken | Count | Aantal knoop punten dat is afgebroken. Deze knoop punten zijn de knoop punten met lage prioriteit die worden verwijderd uit de beschik bare knooppunt groep. |
| Knoop punten verlaten | Count | Aantal knoop punten dat de poort verlaat. Als u knoop punten verlaat, worden de knoop punten die zojuist de verwerking van een taak hebben voltooid, naar de niet-actieve status. |
| Totaal aantal kernen | Count | Aantal totale kernen |
| Actieve kernen | Count | Aantal actieve kernen |
| Niet-actieve kernen | Count | Aantal niet-actieve kern geheugens |
| Onbruikbaar aantal kern geheugens | Count | Aantal niet-bruikbare kernen |
| Afgebroken kernen | Count | Aantal afgebroken kernen |
| Kernen verlaten | Count | Aantal te verlaten kernen |
| Percentage quotum gebruik | Count | Percentage van gebruikte quota |

**Resource**

| Metrisch| Eenheid | Description |
|--|--|--|
| CpuUtilization | Count | Het percentage van het gebruik op een CPU-knoop punt. Het gebruik wordt met een interval van één minuut gerapporteerd. |
| GpuUtilization | Count | Het percentage van het gebruik op een GPU-knoop punt. Het gebruik wordt met een interval van één minuut gerapporteerd. |
| GpuMemoryUtilization | Count | Percentage geheugen gebruik op een GPU-knoop punt. Het gebruik wordt met een interval van één minuut gerapporteerd. |
| GpuEnergyJoules | Count | Interval energie in joules op een GPU-knoop punt. Energie wordt met een interval van één minuut gerapporteerd. |

**Uitvoeren**

Informatie over de trainings uitvoeringen voor de werk ruimte.

| Metrisch | Eenheid | Description |
|--|--|--|
| Geannuleerde uitvoeringen | Count | Aantal uitvoeringen geannuleerd voor deze werk ruimte. Het aantal wordt bijgewerkt wanneer een uitvoering is geannuleerd. |
| Geannuleerde uitvoeringen annuleren | Count | Aantal uitvoeringen waarvoor annuleren is aangevraagd voor deze werk ruimte. Het aantal wordt bijgewerkt wanneer de annulerings aanvraag is ontvangen voor een run. |
| Voltooide uitvoeringen | Count | Het aantal uitvoeringen dat is voltooid voor deze werk ruimte. Het aantal wordt bijgewerkt wanneer een uitvoering is voltooid en de uitvoer is verzameld. |
| Mislukte uitvoeringen | Count | Aantal uitvoeringen is mislukt voor deze werk ruimte. Het aantal wordt bijgewerkt wanneer een uitvoering mislukt. |
| Uitvoeringen volt ooien | Count | Aantal uitvoeringen dat de voltooiings status voor deze werk ruimte heeft opgegeven. Het aantal wordt bijgewerkt wanneer een uitvoering is voltooid, maar de uitvoer verzameling nog steeds wordt uitgevoerd. | 
| Reageert niet | Count | Het aantal uitvoeringen dat niet reageert voor deze werk ruimte. Het aantal wordt bijgewerkt wanneer de status van een uitvoering niet reageert. |
| Niet gestart uitvoeringen | Count | Het aantal uitvoeringen in de status niet gestart voor deze werk ruimte. Het aantal wordt bijgewerkt wanneer een aanvraag wordt ontvangen om een uitvoering te maken, maar er is nog geen uitvoerings informatie ingevuld. |
| Uitvoering voorbereiden | Count | Aantal uitvoeringen dat wordt voor bereid voor deze werk ruimte. Het aantal wordt bijgewerkt wanneer de status van een uitvoering wordt voor bereid terwijl de uitvoerings omgeving wordt voor bereid. |
| Uitvoeringen van inrichting | Count | Aantal uitvoeringen dat wordt ingericht voor deze werk ruimte. Het aantal wordt bijgewerkt wanneer een uitvoering wordt gewacht op het maken of inrichten van het reken doel. |
| Uitvoeringen in wachtrij | Count | Aantal uitvoeringen dat in de wachtrij staat voor deze werk ruimte. Het aantal wordt bijgewerkt wanneer een uitvoering in de wachtrij wordt geplaatst in een compute-doel. Kan optreden wanneer wordt gewacht tot de vereiste reken knooppunten gereed zijn. |
| Gestart uitvoeringen | Count | Aantal uitvoeringen dat wordt uitgevoerd voor deze werk ruimte. Het aantal wordt bijgewerkt wanneer de uitvoering wordt uitgevoerd op de vereiste resources. |
| Uitvoeringen starten | Count | Aantal uitvoeringen gestart voor deze werk ruimte. Het aantal wordt bijgewerkt nadat de aanvraag voor het maken van uitvoerings-en uitvoerings gegevens, zoals de uitvoerings-ID, is ingevuld |
| Fouten | Count | Aantal uitvoerings fouten in deze werk ruimte. Het aantal wordt bijgewerkt wanneer er een fout optreedt. |
| Waarschuwingen | Count | Aantal uitvoerings waarschuwingen in deze werk ruimte. Het aantal wordt bijgewerkt wanneer er een waarschuwing wordt gevonden. |

## <a name="metric-dimensions"></a>Metrische dimensies

Zie [multidimensionale metrische](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics)gegevens voor meer informatie over de metrieke dimensies.

Azure Machine Learning heeft de volgende dimensies die zijn gekoppeld aan de metrische gegevens.

| Dimensie | Description |
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
