---
title: Wat zijn machine learning pijplijnen?
titleSuffix: Azure Machine Learning
description: Meer informatie machine learning pijplijnen u helpen bij het bouwen, optimaliseren en beheren machine learning werkstromen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 02/26/2021
ms.custom: devx-track-python
ms.openlocfilehash: 57f5da06909436e0cbce92559c29c309ca9e20e3
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819228"
---
# <a name="what-are-azure-machine-learning-pipelines"></a>Wat zijn Azure Machine Learning pijplijnen?

In dit artikel leert u hoe u met machine learning pijplijn uw werkstroom kunt bouwen, optimaliseren machine learning beheren. 

<a name="compare"></a>
## <a name="which-azure-pipeline-technology-should-i-use"></a>Welke Azure-pijplijntechnologie moet ik gebruiken?

De Azure-cloud biedt verschillende typen pijplijnen, elk met een ander doel. De volgende tabel bevat de verschillende pijplijnen en waarvoor ze worden gebruikt:

| Scenario | Primaire persona | Azure-aanbieding | OSS-aanbieding | Canonieke pijp | Sterke punten | 
| -------- | --------------- | -------------- | ------------ | -------------- | --------- | 
| Model orchestration (Machine learning) | Data scientist | Azure Machine Learning pijplijnen | Kubeflow-pijplijnen | Data -> Model | Distributie, opslaan in deaching, code-first, hergebruik | 
| Gegevens orchestration (gegevensvoorbereiding) | Data engineer | [Azure Data Factory-pijplijnen](../data-factory/concepts-pipelines-activities.md) | Apache Airflow | Data -> Data | Sterk getypeerd verplaatsen, gegevensgerichte activiteiten |
| Code & app-orchestration (CI/CD) | App-ontwikkelaar/Ops | [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) | Jenkins | Code + Model - > App/Service | De meeste open en flexibele ondersteuning voor activiteiten, goedkeuringswachtrijen, fasen met gating | 

## <a name="what-can-machine-learning-pipelines-do"></a>Wat kunnen machine learning pijplijnen doen?

Een Azure Machine Learning pijplijn is een onafhankelijk uitvoerbare werkstroom van een volledige machine learning taak. Subtaken worden ingekapseld als een reeks stappen in de pijplijn. Een Azure Machine Learning-pijplijn kan net zo eenvoudig zijn als een pijplijn die een Python-script aanroept, _dus kan_ zo ongeveer alles doen. Pijplijnen _moeten_ zich richten op machine learning taken zoals:

+ Gegevensvoorbereiding, waaronder importeren, valideren en opschonen, munging en transformatie, normalisatie en fasering
+ Trainingsconfiguratie met inbegrip van parameteriseren van argumenten, bestandspaden en logboek-/rapportageconfiguratie
+ Efficiënt en herhaaldelijk trainen en valideren. Efficiëntie kan komen door het opgeven van specifieke gegevenssubsets, verschillende rekenresources voor hardware, gedistribueerde verwerking en voortgangsbewaking
+ Implementatie, inclusief versiebeheer, schalen, inrichten en toegangsbeheer

Met onafhankelijke stappen kunnen meerdere gegevenswetenschappers tegelijkertijd aan dezelfde pijplijn werken zonder rekenbronnen te veel te belasten. Afzonderlijke stappen maken het ook eenvoudig om verschillende rekentypen/grootten voor elke stap te gebruiken.

Nadat de pijplijn is ontworpen, is er vaak meer afstemming rond de trainingslus van de pijplijn. Wanneer u een pijplijn opnieuw uit te voeren, gaat de run naar de stappen die opnieuw moeten worden uitgevoerd, zoals een bijgewerkt trainingsscript. Stappen die niet opnieuw hoeven te worden doorlopen, worden overgeslagen. 

Met pijplijnen kunt u ervoor kiezen om verschillende hardware te gebruiken voor verschillende taken. Azure coördineert de verschillende [rekendoelen die](concept-azure-machine-learning-architecture.md) u gebruikt, zodat uw tussenliggende gegevens naadloos stromen naar downstream-rekendoelen.

U kunt [de metrische gegevens voor uw pijplijnexperimenten](./how-to-log-view-metrics.md) rechtstreeks in Azure Portal of op de landingspagina van uw [werkruimte (preview)](https://ml.azure.com). Nadat een pijplijn is gepubliceerd, kunt u een REST-eindpunt configureren, zodat u de pijplijn opnieuw kunt gebruiken vanaf elk platform of elke stack.

Kortom, alle complexe taken van de machine learning levenscyclus kunnen worden geholpen met pijplijnen. Andere Azure-pijplijntechnologieën hebben hun eigen sterke punten. [Azure Data Factory-pijplijnen](../data-factory/concepts-pipelines-activities.md) zijn handig bij het werken met gegevens en [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) is het juiste hulpprogramma voor continue integratie en implementatie. Maar als de focus ligt machine learning, Azure Machine Learning pijplijnen waarschijnlijk de beste keuze voor uw werkstroombehoeften. 

### <a name="analyzing-dependencies"></a>Afhankelijkheden analyseren

Veel programmeerecosystemen hebben hulpprogramma's die resource-, bibliotheek- of compilatieafhankelijkheden indeelt. Over het algemeen gebruiken deze hulpprogramma's bestandstijdstempels om afhankelijkheden te berekenen. Wanneer een bestand wordt gewijzigd, worden alleen het bestand en de afhankelijke bestanden bijgewerkt (gedownload, opnieuw gecompileerd of verpakt). Azure Machine Learning pijplijnen breiden dit concept uit. Net als bij traditionele build-hulpprogramma's berekenen pijplijnen afhankelijkheden tussen stappen en voeren ze alleen de benodigde herberekeningen uit. 

De afhankelijkheidsanalyse in Azure Machine Learning pijplijnen is echter geavanceerder dan eenvoudige tijdstempels. Elke stap kan worden uitgevoerd in een andere hardware- en softwareomgeving. Gegevensvoorbereiding kan een tijdrovend proces zijn, maar hoeft niet te worden uitgevoerd op hardware met krachtige GPU's. Voor bepaalde stappen is mogelijk besturingssysteemspecifieke software vereist, wilt u wellicht gedistribueerde training gebruiken, enzovoort. 

Azure Machine Learning alle afhankelijkheden tussen pijplijnstappen automatisch indeelt. Deze orchestration kan bestaan uit het omhoog en omlaag draaien van Docker-afbeeldingen, het koppelen en loskoppelen van rekenresources en het op een consistente en automatische manier verplaatsen van gegevens tussen de stappen.

### <a name="coordinating-the-steps-involved"></a>De betrokken stappen coördineren

Wanneer u een object maakt en uit te voeren, worden de volgende `Pipeline` stappen op hoog niveau uitgevoerd:

+ Voor elke stap berekent de service de vereisten voor:
    + Rekenbronnen voor hardware
    + Besturingssysteembronnen (Docker-image(s))
    + Softwarebronnen (Conda/virtualenv-afhankelijkheden)
    + Gegevensinvoer 
+ De service bepaalt de afhankelijkheden tussen de stappen, wat resulteert in een dynamische uitvoeringsgrafiek
+ Wanneer elk knooppunt in de uitvoeringsgrafiek wordt uitgevoerd:
    + De service configureert de benodigde hardware- en softwareomgeving (mogelijk door bestaande resources opnieuw te gebruiken)
    + De stap wordt uitgevoerd en geeft logboekregistratie- en bewakingsgegevens aan het object dat het `Experiment` bevat
    + Wanneer de stap is voltooid, worden de uitvoer als invoer voorbereid voor de volgende stap en/of naar de opslag geschreven
    + Resources die niet meer nodig zijn, worden afgerond en losgekoppeld

![Pijplijnstappen](./media/concept-ml-pipelines/run_an_experiment_as_a_pipeline.png)

## <a name="building-pipelines-with-the-python-sdk"></a>Pijplijnen bouwen met de Python-SDK

In de [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/install)is een pijplijn een Python-object dat in de module is `azureml.pipeline.core` gedefinieerd. Een [Pipeline-object](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29) bevat een geordende reeks van een of meer [PipelineStep-objecten.](/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep) De klasse is abstract en de werkelijke stappen zijn van subklassen zoals `PipelineStep` [EstimatorStep,](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep) [PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.pythonscriptstep)of [DataTransferStep.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep) De [modulestapklasse](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep) bevat een herbruikbare reeks stappen die kunnen worden gedeeld tussen pijplijnen. Een `Pipeline` wordt uitgevoerd als onderdeel van een `Experiment` .

Een Azure machine learning-pijplijn is gekoppeld aan Azure Machine Learning werkruimte en een pijplijnstap wordt gekoppeld aan een rekendoel dat beschikbaar is in die werkruimte. Zie Create [and manage Azure Machine Learning workspaces in the Azure Portal](./how-to-manage-workspace.md) or What are compute targets in Azure Machine Learning? (Wat zijn rekendoelen in [Azure Machine Learning? voor meer informatie.](./concept-compute-target.md)

### <a name="a-simple-python-pipeline"></a>Een eenvoudige Python-pijplijn

Dit fragment toont de objecten en aanroepen die nodig zijn om een te maken en uit te `Pipeline` voeren:

```python
ws = Workspace.from_config() 
blob_store = Datastore(ws, "workspaceblobstore")
compute_target = ws.compute_targets["STANDARD_NC6"]
experiment = Experiment(ws, 'MyExperiment') 

input_data = Dataset.File.from_files(
    DataPath(datastore, '20newsgroups/20news.pkl'))
prepped_data_path = OutputFileDatasetConfig(name="output_path")

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    source_directory="prep_src",
    compute_target=compute_target,
    arguments=["--prepped_data_path", prepped_data_path],
    inputs=[input_dataset.as_named_input('raw_data').as_mount() ]
    )

prepped_data = prepped_data_path.read_delimited_files()

train_step = PythonScriptStep(
    name="train",
    script_name="train.py",
    compute_target=compute_target,
    arguments=["--prepped_data", prepped_data],
    source_directory="train_src"
)
steps = [ dataprep_step, train_step ]

pipeline = Pipeline(workspace=ws, steps=steps)

pipeline_run = experiment.submit(pipeline)
pipeline_run.wait_for_completion()
```

Het codefragment begint met algemene Azure Machine Learning objecten, een `Workspace` , een , een `Datastore` [ComputeTarget](/python/api/azureml-core/azureml.core.computetarget)en een `Experiment` . Vervolgens worden met de code de objecten gemaakt die en `input_data` moeten `prepped_data_path` bevatten. De `input_data` is een exemplaar van [FileDataset](/python/api/azureml-core/azureml.data.filedataset) en de `prepped_data_path` is een exemplaar van [OutputFileDatasetConfig.](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig) Het standaardgedrag is om de uitvoer te kopiëren naar het gegevensexemplaar onder het pad , waarbij de id van de run is en een automatisch gegenereerde waarde is als deze niet is opgegeven door `OutputFileDatasetConfig` `workspaceblobstore` de `/dataset/{run-id}/{output-name}` `run-id` `output-name` ontwikkelaar.

De code voor gegevensvoorbereiding (niet weergegeven) schrijft bestanden met scheidingstekens naar `prepped_data_path` . Deze uitvoer van de gegevensvoorbereidingsstap wordt doorgegeven `prepped_data` aan de trainingsstap. 

De matrix `steps` bevat de twee s en `PythonScriptStep` `dataprep_step` `train_step` . Azure Machine Learning analyseert de gegevensafhankelijkheid van en `prepped_data` wordt uitgevoerd `dataprep_step` vóór `train_step` . 

Vervolgens wordt met de code het object zelf gemaakt en wordt de matrix met stappen `Pipeline` in de werkruimte en door geven. Met de aanroep `experiment.submit(pipeline)` van wordt de Azure ML-pijplijn uitgevoerd. De aanroep van `wait_for_completion()` blokkeert totdat de pijplijn is voltooid. 

Zie de artikelen Gegevenstoegang [in Azure Machine Learning](concept-data.md) en Gegevens verplaatsen naar en tussen ML-pijplijnstappen [(Python)](how-to-move-data-in-out-of-pipelines.md)voor meer informatie over het verbinden van uw pijplijn met uw gegevens. 

## <a name="building-pipelines-with-the-designer"></a>Pijplijnen bouwen met de ontwerpfunctie

Ontwikkelaars die de voorkeur geven aan een ontwerpoppervlak voor visuele elementen, kunnen de Azure Machine Learning gebruiken om pijplijnen te maken. U hebt toegang tot dit hulpprogramma via de **selectie Ontwerper** op de startpagina van uw werkruimte.  Met de ontwerpfunctie kunt u stappen naar het ontwerpoppervlak slepen en neerzetten. 

Wanneer u pijplijnen visueel ontwerpt, worden de invoer en uitvoer van een stap zichtbaar weergegeven. U kunt gegevensverbindingen slepen en neerzetten, zodat u de gegevensstroom van uw pijplijn snel kunt begrijpen en wijzigen.

![Voorbeeld van ontwerpfunctie van Azure Machine Learning](./media/concept-designer/designer-drag-and-drop.gif)

## <a name="key-advantages"></a>Belangrijkste voordelen

De belangrijkste voordelen van het gebruik van pijplijnen voor machine learning werkstromen zijn:

|Belangrijkste voordeel|Description|
|:-------:|-----------|
|**Onbeheerde &nbsp; runs**|Plan stappen die parallel of opeenvolgend op een betrouwbare en onbeheerde manier moeten worden uitgevoerd. Gegevensvoorbereiding en modellering kunnen dagen of weken duren en met pijplijnen kunt u zich richten op andere taken terwijl het proces wordt uitgevoerd. |
|**Heterogene rekenkracht**|Gebruik meerdere pijplijnen die betrouwbaar worden gecoördineerd in heterogene en schaalbare rekenresources en opslaglocaties. Maak efficiënt gebruik van beschikbare rekenbronnen door afzonderlijke pijplijnstappen uit te voeren op verschillende rekendoelen, zoals HDInsight, GPU Data Science VM's en Databricks.|
|**Herbruikbaarheid**|Pijplijnsjablonen maken voor specifieke scenario's, zoals opnieuw trainen en batch scoren. Gepubliceerde pijplijnen activeren vanuit externe systemen via eenvoudige REST-aanroepen.|
|**Tracering en versiering**|In plaats van gegevens en resultaatpaden handmatig bij te houden terwijl u itereert, gebruikt u de pijplijn-SDK om uw gegevensbronnen, invoer en uitvoer expliciet een naam en versie te geven. U kunt scripts en gegevens ook afzonderlijk beheren voor een hogere productiviteit.|
| **Modulariteit** | Door gebieden van problemen te scheiden en wijzigingen te isoleren, kan software zich sneller ontwikkelen met hogere kwaliteit. | 
|**Samenwerking**|Met pijplijnen kunnen gegevenswetenschappers samenwerken in alle gebieden van het machine learning en tegelijkertijd aan pijplijnstappen kunnen werken.|

## <a name="next-steps"></a>Volgende stappen

Azure Machine Learning pijplijnen zijn een krachtige faciliteit die waarde begint te leveren in de vroege ontwikkelingsfasen. De waarde neemt toe naarmate het team en het project groeit. In dit artikel is uitgelegd hoe pijplijnen worden opgegeven met de Azure Machine Learning Python SDK en worden georganiseerd in Azure. U hebt een aantal eenvoudige broncode gezien en u hebt kennis gemaakt met een aantal klassen `PipelineStep` die beschikbaar zijn. U moet een idee hebben van het gebruik van Azure Machine Learning pijplijnen en hoe deze in Azure worden uitgevoerd. 

+ Meer informatie over het [maken van uw eerste pijplijn.](./how-to-create-machine-learning-pipelines.md)

+ Meer informatie over het [uitvoeren van batchvoorspellingen op grote gegevens.](tutorial-pipeline-batch-scoring-classification.md )

+ Zie de SDK-referentie docs voor [pijplijnkern-](/python/api/azureml-pipeline-core/) en [pijplijnstappen.](/python/api/azureml-pipeline-steps/)

+ Probeer jupyter-voorbeeldnotelets met Azure Machine Learning [pijplijnen.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines) Meer informatie over het [uitvoeren van notebooks om deze service te verkennen.](samples-notebooks.md)