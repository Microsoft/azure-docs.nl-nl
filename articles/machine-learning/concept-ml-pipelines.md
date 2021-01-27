---
title: Wat zijn machine learning pijp lijnen?
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe u met machine learning pijp lijnen machine learning-werk stromen kunt bouwen, optimaliseren en beheren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 01/12/2021
ms.custom: devx-track-python
ms.openlocfilehash: e3f92f445068b98c12069577ddf61a71568e403b
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98871550"
---
# <a name="what-are-azure-machine-learning-pipelines"></a>Wat zijn Azure Machine Learning pijp lijnen?

In dit artikel leert u hoe u met een machine learning pijp lijn uw machine learning werk stroom kunt bouwen, optimaliseren en beheren. 

<a name="compare"></a>
## <a name="which-azure-pipeline-technology-should-i-use"></a>Welke Azure pipeline-technologie moet ik gebruiken?

De Azure-Cloud biedt verschillende soorten pijp lijnen, elk met een ander doel. De volgende tabel geeft een lijst van de verschillende pijp lijnen en waarvoor ze worden gebruikt:

| Scenario | Primaire persoon | Azure-aanbieding | OSS-aanbieding | Canonieke pijp | Sterke punten | 
| -------- | --------------- | -------------- | ------------ | -------------- | --------- | 
| Model indeling (machine learning) | Data scientist | Azure Machine Learning pijp lijnen | Kubeflow-pijp lijnen | Model voor gegevens > | Distributie, caching, code, opnieuw gebruiken | 
| Gegevens indeling (gegevens voorbereiding) | Data engineer | [Azure Data Factory-pijplijnen](../data-factory/concepts-pipelines-activities.md) | Apache-lucht flow | Gegevens > gegevens | Sterk getypeerde bewegingen, gegevens gerichte activiteiten |
| App-indeling voor code & (CI/CD) | App-ontwikkelaar/OPS | [Azure-pijplijnen](https://azure.microsoft.com/services/devops/pipelines/) | Jenkins | Code + model-> app/service | De meeste open en flexibele activiteiten ondersteuning, goedkeurings wachtrijen, fasen met beperking | 

## <a name="what-can-machine-learning-pipelines-do"></a>Wat kan machine learning pijp lijnen doen?

Een Azure Machine Learning pijp lijn is een onafhankelijk uitvoer bare werk stroom van een volledige machine learning taak. Subtaken worden ingekapseld als een reeks stappen in de pijplijn. Een Azure Machine Learning-pijp lijn kan net zo eenvoudig zijn als één om een python-script aan te roepen. dit _kan_ dus iets anders doen. Pijp lijnen _moeten_ zich richten op machine learning taken, zoals:

+ Gegevensvoorbereiding, waaronder importeren, valideren en opschonen, munging en transformatie, normalisatie en fasering
+ Trainingsconfiguratie met inbegrip van parameteriseren van argumenten, bestandspaden en logboek-/rapportageconfiguratie
+ Training en validatie efficiënt en herhaaldelijk. Efficiëntie kan afkomstig zijn van het opgeven van specifieke gegevens subsets, verschillende hardwarebronnen, gedistribueerde verwerking en voortgangs bewaking
+ Implementatie, inclusief versiebeheer, schalen, inrichten en toegangsbeheer

Met onafhankelijke stappen kunnen er meerdere gegevens wetenschappers tegelijk op dezelfde pijp lijn werken zonder gegevens over reken bronnen te hoeven te gebruiken. Door afzonderlijke stappen te maken, kunt u voor elke stap ook eenvoudig verschillende reken typen/-groottes gebruiken.

Nadat de pijp lijn is ontworpen, is er vaak meer nauw keuriger om de training van de pijp lijn te verfijnen. Wanneer u een pijp lijn opnieuw uitvoert, springt de uitvoering naar de stappen die moeten worden uitgevoerd, zoals een bijgewerkt trainings script. De stappen die niet opnieuw hoeven te worden uitgevoerd, worden overgeslagen. 

Met pijp lijnen kunt u ervoor kiezen om andere hardware te gebruiken voor verschillende taken. Azure coördineert de verschillende [reken doelen](concept-azure-machine-learning-architecture.md) die u gebruikt, zodat uw tussenliggende gegevens naadloos stromen naar downstream Compute-doelen.

U kunt [de metrische gegevens voor uw pijplijn experimenten rechtstreeks bijhouden](./how-to-track-experiments.md) in azure portal of de [landings pagina van uw werk ruimte (preview)](https://ml.azure.com). Nadat een pijp lijn is gepubliceerd, kunt u een REST-eind punt configureren. Hiermee kunt u de pijp lijn opnieuw uitvoeren vanaf elk platform of elke stack.

Kortom, alle complexe taken van de machine learning levenscyclus kunnen worden geholpen met pijp lijnen. Andere Azure-pijplijn technologieën hebben hun eigen sterke punten. [Azure Data Factory pijp lijnen](../data-factory/concepts-pipelines-activities.md) die Excel gebruiken bij het werken met gegevens en [Azure-pijp lijnen](https://azure.microsoft.com/services/devops/pipelines/) , is het juiste hulp middel voor continue integratie en implementatie. Maar als uw focus machine learning is, zijn Azure Machine Learning pijp lijnen waarschijnlijk de beste keuze voor uw werk stroom behoeften. 

### <a name="analyzing-dependencies"></a>Afhankelijkheden analyseren

Veel programmeer ecosystemen beschikken over hulpprogram ma's voor het organiseren van afhankelijkheden van resources, bibliotheken of compilatie. Over het algemeen gebruiken deze hulpprogram ma's bestands tempels voor het berekenen van afhankelijkheden. Wanneer een bestand wordt gewijzigd, worden alleen de bijbehorende afhankelijkheden bijgewerkt (gedownload, opnieuw gecompileerd of verpakt). Met Azure Machine Learning pijp lijnen wordt dit concept uitgebreid. Net als bij traditionele hulpprogram ma's voor builds, worden door pijp lijnen afhankelijkheden tussen de stappen berekend en worden alleen de benodigde herberekeningen uitgevoerd. 

De afhankelijkheids analyse in Azure Machine Learning pijp lijnen is geavanceerder dan eenvoudige tijds tempels. Elke stap kan worden uitgevoerd in een andere hardware-en software omgeving. Het voorbereiden van gegevens kan een tijdrovend proces zijn, maar het is niet nodig om te worden uitgevoerd op hardware met krachtige Gpu's. voor bepaalde stappen kunnen specifieke software zijn vereist, kunt u gebruikmaken van gedistribueerde training, enzovoort. 

Azure Machine Learning zijn automatisch alle afhankelijkheden tussen pijplijn stappen. Deze indeling kan het volgende omvatten: omhoog en omlaag docker-installatie kopieën, het koppelen en loskoppelen van reken resources en het verplaatsen van gegevens tussen de stappen op consistente en automatische wijze.

### <a name="coordinating-the-steps-involved"></a>De betrokken stappen coördineren

Wanneer u een-object maakt en uitvoert `Pipeline` , worden de volgende stappen op hoog niveau uitgevoerd:

+ Voor elke stap berekent de service vereisten voor:
    + Hardware Compute-resources
    + OS-resources (docker-installatie kopie (en))
    + Software bronnen (Conda/virtualenv-afhankelijkheden)
    + Gegevens invoer 
+ De service bepaalt de afhankelijkheden tussen de stappen, wat resulteert in een dynamische uitvoerings grafiek
+ Wanneer elk knoop punt in de uitvoerings grafiek wordt uitgevoerd:
    + Met de service worden de benodigde hardware-en software omgeving geconfigureerd (mogelijk worden bestaande resources opnieuw gebruikt)
    + De stap wordt uitgevoerd, zodat informatie over logboek registratie en controle kan worden verstrekt aan het betreffende `Experiment` object
    + Wanneer de stap is voltooid, worden de uitvoer voor bereid als invoer voor de volgende stap en/of naar opslag geschreven
    + Resources die niet meer nodig zijn, worden voltooid en ontkoppeld

![Pijplijn stappen](./media/concept-ml-pipelines/run_an_experiment_as_a_pipeline.png)

## <a name="building-pipelines-with-the-python-sdk"></a>Pijp lijnen bouwen met de python-SDK

In de [Azure machine learning PYTHON SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)is een pijp lijn een python-object dat is gedefinieerd in de `azureml.pipeline.core` module. Een [pijplijn](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?preserve-view=true&view=azure-ml-py) object bevat een geordende sequentie van een of meer [PipelineStep](/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?preserve-view=true&view=azure-ml-py) -objecten. De `PipelineStep` klasse is abstract en de daad werkelijke stappen zijn subklassen, zoals [EstimatorStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep?preserve-view=true&view=azure-ml-py), [PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.pythonscriptstep?preserve-view=true&view=azure-ml-py)of [DataTransferStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?preserve-view=true&view=azure-ml-py). De [ModuleStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep?preserve-view=true&view=azure-ml-py) -klasse bevat een herbruikbare reeks stappen die kunnen worden gedeeld tussen pijp lijnen. Een `Pipeline` uitvoering als onderdeel van een `Experiment` .

Een Azure machine learning-pijp lijn is gekoppeld aan een Azure Machine Learning-werk ruimte en een pijplijn stap is gekoppeld aan een berekenings doel dat in die werk ruimte beschikbaar is. Zie [Azure machine learning-werk ruimten maken en beheren in de Azure Portal](./how-to-manage-workspace.md) of [Wat zijn reken doelen in azure machine learning?](./concept-compute-target.md)voor meer informatie.

### <a name="a-simple-python-pipeline"></a>Een eenvoudige python-pijp lijn

Dit fragment toont de objecten en aanroepen die nodig zijn om een te maken en uit te voeren `Pipeline` :

```python
ws = Workspace.from_config() 
blob_store = Datastore(ws, "workspaceblobstore")
compute_target = ws.compute_targets["STANDARD_NC6"]
experiment = Experiment(ws, 'MyExperiment') 

input_data = Dataset.File.from_files(
    DataPath(datastore, '20newsgroups/20news.pkl'))

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    compute_target=cluster,
    arguments=[input_dataset.as_named_input('raw_data').as_mount(), dataprep_output]
    )
output_data = OutputFileDatasetConfig()
input_named = input_data.as_named_input('input')

steps = [ PythonScriptStep(
    script_name="train.py",
    arguments=["--input", input_named.as_download(), "--output", output_data],
    compute_target=compute_target,
    source_directory="myfolder"
) ]

pipeline = Pipeline(workspace=ws, steps=steps)

pipeline_run = experiment.submit(pipeline)
pipeline_run.wait_for_completion()
```

Het fragment begint met algemene Azure Machine Learning objecten, a `Workspace` , a `Datastore` , een [ComputeTarget](/python/api/azureml-core/azureml.core.computetarget?preserve-view=true&view=azure-ml-py)en een `Experiment` . Vervolgens maakt de code de objecten die moeten worden bewaard `input_data` en `output_data` . Het `input_data` is een exemplaar van [FileDataset](/python/api/azureml-core/azureml.data.filedataset?preserve-view=true&view=azure-ml-py) en de `output_data` is een exemplaar van  [OutputFileDatasetConfig](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py). Voor `OutputFileDatasetConfig` het standaard gedrag is het kopiëren van de uitvoer naar de `workspaceblobstore` gegevens opslag onder het pad `/dataset/{run-id}/{output-name}` , waarbij `run-id` de id van de uitvoeringsrun is en `output-name` een automatisch gegenereerde waarde als niet is opgegeven door de ontwikkelaar.

De matrix `steps` bevat één element, een `PythonScriptStep` dat de gegevens objecten gaat gebruiken en wordt uitgevoerd op de `compute_target` . Vervolgens maakt de code een instantie `Pipeline` van het object zelf, waarbij de werk ruimte en de stappen matrix worden door gegeven. De aanroep om `experiment.submit(pipeline)` de Azure ml-pijplijn uitvoering te starten. De aanroep naar `wait_for_completion()` blokken tot de pijp lijn is voltooid. 

Zie voor meer informatie over het verbinden van uw pijp lijn met uw gegevens de artikelen [gegevens toegang in azure machine learning](concept-data.md) en het [verplaatsen van gegevens naar en tussen ml-pijplijn stappen (python)](how-to-move-data-in-out-of-pipelines.md). 

## <a name="building-pipelines-with-the-designer"></a>Pijp lijnen bouwen met de ontwerper

Ontwikkel aars die de voor keur geven aan een visueel ontwerp vlak kunnen de Azure Machine Learning Designer gebruiken om pijp lijnen te maken. U kunt dit hulp programma openen via de selectie van de **ontwerp functie** op de start pagina van uw werk ruimte.  Met de ontwerper kunt u stappen naar het ontwerp oppervlak slepen en neerzetten. 

Wanneer u pijp lijnen visueel ontwerpt, worden de invoer en uitvoer van een stap zichtbaar weer gegeven. U kunt gegevens verbindingen slepen en neerzetten om de gegevensstroom van uw pijp lijn snel te begrijpen en te wijzigen.

![Voorbeeld van ontwerpfunctie van Azure Machine Learning](./media/concept-designer/designer-drag-and-drop.gif)

## <a name="key-advantages"></a>Belangrijkste voor delen

De belangrijkste voor delen van het gebruik van pijp lijnen voor uw machine learning-werk stromen zijn:

|Belangrijkste voor deel|Beschrijving|
|:-------:|-----------|
|**&nbsp;Uitvoeringen zonder toezicht**|Stappen plannen om parallel of op een betrouw bare en zonder toezicht te worden uitgevoerd. De voor bereiding en het model leren van gegevens kunnen de afgelopen dagen of weken zijn en met pijp lijnen kunt u zich richten op andere taken terwijl het proces wordt uitgevoerd. |
|**Heterogene compute**|Gebruik meerdere pijp lijnen die betrouwbaar zijn afgestemd op heterogene en schaal bare reken resources en opslag locaties. Maak efficiënt gebruik van beschik bare reken resources door afzonderlijke pijplijn stappen uit te voeren op verschillende reken doelen, zoals HDInsight, GPU data Science Vm's en Databricks.|
|**Herbruikbaarheid**|Maak pijplijn sjablonen voor specifieke scenario's, zoals retraining en batch-scores. Activeer gepubliceerde pijp lijnen van externe systemen via eenvoudige REST-aanroepen.|
|**Bijhouden en versie beheer**|In plaats van gegevens en resultaat paden hand matig te traceren tijdens het herhalen, gebruikt u de SDK van de pijp lijnen om uw gegevens bronnen, invoer en uitvoer expliciet een naam en versie te gegeven. U kunt ook scripts en gegevens afzonderlijk beheren voor een verhoogde productiviteit.|
| **Modulariteit** | Het scheiden van aandachtspunten en het isoleren van wijzigingen stelt software in staat om sneller te werken met een hogere kwaliteit. | 
|**Samenwerking**|Met pijp lijnen kunnen gegevens wetenschappers samen werken aan alle gebieden van het machine learning-ontwerp proces, terwijl ze gelijktijdig kunnen werken aan pijplijn stappen.|

## <a name="next-steps"></a>Volgende stappen

Azure Machine Learning-pijp lijnen zijn een krachtige faciliteit die in de vroege ontwikkel stadia waarde begint te leveren. De waarde neemt toe naarmate het team en project groeien. In dit artikel wordt uitgelegd hoe u pijp lijnen kunt opgeven met de Azure Machine Learning python SDK en die zijn georganiseerd op Azure. U hebt een eenvoudige bron code gezien en deze is geïntroduceerd in een aantal van de `PipelineStep` beschik bare klassen. U moet een idee hebben van het gebruik van Azure Machine Learning pijp lijnen en hoe Azure deze uitvoert. 

+ Meer informatie over het [maken van uw eerste pijp lijn](./how-to-create-machine-learning-pipelines.md).

+ Meer informatie over het [uitvoeren van batch voorspellingen voor grote gegevens](tutorial-pipeline-batch-scoring-classification.md ).

+ Zie de SDK-documentatie voor [pijplijn kernen](/python/api/azureml-pipeline-core/?preserve-view=true&view=azure-ml-py) en [pijplijn stappen](/python/api/azureml-pipeline-steps/?preserve-view=true&view=azure-ml-py).

+ Probeer een voor beeld van Jupyter-notitie blokken [Azure machine learning-pijp lijnen](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines). Meer informatie over het [uitvoeren van notitie blokken om deze service te verkennen](samples-notebooks.md).